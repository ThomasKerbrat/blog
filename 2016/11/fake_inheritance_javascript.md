
# Fake inheritance in JavaScript

## The Constructor Function

JavaScript does not have classes or any sort of mold to cast object from.
As a result, JavaScript does not support inheritance as we understand it in classical Object Oriented.

However, any function can act as a constructor.
It works by calling the function preceded by the `new` keyword.
The naming convention for constructor functions are to PascalCase their names.

``` javascript
function MyError(message) {
  this.name = 'MyError';
  this.message = message || 'Default message';
}
let error = new MyError();
```

Here, the **constructor** of the `error` variable is the `MyError` function.
We **instantiated** an object from the `MyError` constructor.

We can challenge the object to know if it has been instantiated by a particular function.
It can be achieve with the `instanceof` keyword, or by getting the `error.constructor` value.

``` javascript
error instanceof MyError; // true
error.constructor == MyError; // true
```

The `error.constructor` returns the function that was used to instantiate the object.
As ugly as it can be in real code, we can instantiate a new object without directly referring to its constructor function:

``` javascript
let error2 = new error.constructor();
```

The `error2` variable will be an instance of `MyError`.

## The Prototype

What happens if I try to access the `myProperty` property on `error`?

``` javascript
error.myProperty; // undefined
```

The expression is evaluated to `undefined`, which is its own type: `typeof undefined === 'undefined'`.
To understand the inner working of that lookup, we must learn about the prototype.

A prototype is just an object.
It is used as a fallback in case a property does not exist on an object, in our case `error`.
The prototype is associated to a constructor function.
For the `MyError` constructor, we can refer to it as `MyError.prototype`.

By default, our functions' prototypes are an empty object:

``` javascript
MyError.prototype; // {}
```

The nice thing about prototype is that they are used as a fallback.
Now let's defined the `myProperty` property on the `MyError` prototype:

``` javascript
MyError.prototype.myProperty = 3712;
error.myProperty; // 3712
let error2 = new MyError();
error2.myProperty; // 3712
```

Given any instance of `MyError`, we can now access `myProperty` and will return `3712`.
The `myProperty` property does not exists on the `error` instance.
It is defined on its prototype.

If the property does not exists on the instance, it will check on its prototype.
The prototype being an instance itself, the process repeats until the final prototype of all objects has been reached.

This is referred as the **prototype chain**.
It must not be confused with inheritance.
Looking through the prototype chain to perform a property lookup is actually **delegation**.
