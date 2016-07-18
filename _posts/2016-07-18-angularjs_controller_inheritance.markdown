---
layout: post
title: "AngularJS Controller Inheritance using $scope"
excerpt: ""
date: 2016-07-18 21:30:45 +0200
---

## Context

In a recent project, I had to solve an issue where we had one code base for three variations of the same product.
Those three variations were targeted for mobiles, tablets and desktops.

The team developed the tablet version of the product and we were facing to include the desktop version.
The requirement was to keep as much common code as possible across the different variations.

The idea we choose was to make a common controller that will be specialized by some product controllers.
As we develop with ES5 JavaScript, we had no class inheritance.
So I figured out I can mimic the inheritance mechanism thanks to AngularJS $scope.


## An Inheritor Service

As my requirement was limited, I decided to factor the inheritance code as much as I could.
I also wanted, for my fellow coworkers, a simple API **at point of use**.

Here is a simplified version of the service we use to inherit our controllers:

``` js
angular.module('app')
  .service('ControllerHeritorService', ['$injector', function ($injector) {
    return {
      inherit: function inherit(baseFunction, productFunction, scope) {
        $injector.instantiate(baseFunction, { '$scope': scope });
        $injector.instantiate(productFunction, { '$scope': scope });
      }
    };
  }]);
```

This service exposes one method called ```inherit``` taking three parameters in:

- The first is the constructor function for the common controller of every products.
- The second is the constructor function that specialize the base controller for a particular product variation.
- The last is the scope given by AngularJS for the controller we want to declare.

The code above resumes the essence of the solution.
The two instructions are simply constructors for the $scope object.
In those functions, we declare properties on $scope for values and functions we want to attach to the scope.

The $injector service allows us to call the two provided functions, but most importantly, to provide the ```'$scope'``` dependency.
The second argument of [$injector.instantiate](https://docs.angularjs.org/api/auto/service/$injector#instantiate) is an optional object.
It is used by the dependency injection container to resolve dependencies.
When providing the ```{ '$scope' : scope }``` object, we tell the injector to use the ```scope``` object for the ```'$scope'``` dependency.

This is the intimate reason why this solution is working.
By passing the same object for ```'$scope'``` dependency, both functions are attaching and using properties **on the same scope**.
Calling the base function first allow the product function to have access to its "parent" controller.


## Usage

Now that we have created our service to inherit our controllers, we can use it to declare a controller.
I wanted the less intrusion for the developer.
In the same fashion AngularJS let us declare controllers and services,
I wanted the developer to use my API to declare the inheritance.
I didn't want the developer to implement something inside its controller (like a virtual method),
but to be directly at home like it was itself an AngularJS controller.

Here is an exemple of usage:

``` js
angular.module('app')
  .controller('MainController', ['$scope', 'ControllerHeritorService', function ($scope, heritor) {
    heritor.inherit(MainControllerBase, MainControllerTablet, $scope);
  }]);

MainControllerBase.$inject = ['$scope', 'SomeOtherServices'];
function MainControllerBase($scope, SomeOtherServices) {
  $scope.options = {
    'option1': true,
    'option2': false
  };
}

MainControllerTablet.$inject = ['$scope', 'SomeOtherServices'];
function MainControllerTablet($scope, SomeOtherServices) {
  $scope.options.option2 = true;
}
```

Let's dissect this code.

The first block of code is the declaration of the ```MainController```.
We declare the controller as a normal controller using the AngularJS ```module(...).controller(...);``` notation.
The only instruction in the function is a call to the ```ControllerHeritorService```.
Here we pass the two controllers involved in the inheritance operation and the scope given by the dependency injection container of the controller.
This is the only place we can have a new controller scope (except by creating a new one via the $rootScope).

The next two block of code are the actual implementation of the base and product controllers.
Here, we can see that the base controller is declaring an options property on the $scope object.
Because, we call the MainControllerBase first in the ControllerHeritorService, the MainControllerTablet has access to that property when it is executed.
It can freely change the scope properties for that particular variation of the product.

This solution has the advantage of not requiring the developer to manually instantiate the base controller in its code.
Also, as you can see in both base and tablet controllers, the dependencies array is free of ```$injector``` or ```ControllerHeritorService```.
Only the intermediate function in the ```MainController``` depends on ```ControllerHeritorService```.


# Conclusion

This solution is easily portable from projects to projects.
But it should be noted that this is not a solution for every needs.
We could have decided to embrace the AngularJS 1.5 components and the scopes inheritance would have been done by AngularJS itself.

A drawback for this solution is that it is not compatible with the _controller as_ notation and the use of the ```this``` keyword inside the controllers.
But this solution only require a slightly modification to accommodate this need.
You can try for yourself and notify me, I'd be more than happy to update this post.
