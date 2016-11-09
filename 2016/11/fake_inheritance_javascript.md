
# Fake inheritance in JavaScript

JavaScript does not have classes or any sort of mold to cast object from.
As a result, JavaScript does not support inheritance as we understand it in classical Object Oriented Programming.



## The Constructor Function

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

What happens if we try to access the `myProperty` property on `error`?

``` javascript
error.myProperty; // undefined
```

The expression is evaluated to `undefined`.
Indeed, the `error` object does not have a `myProperty` property on it.

Now let's defined the `myProperty` property on the `MyError` prototype:

``` javascript
MyError.prototype.myProperty = 3712;
error.myProperty; // 3712
```

Given any instance of `MyError`, we can now access `myProperty` and will return `3712`.
The `myProperty` property does not exists on the `error` instance.
It is defined on its prototype.

If the property does not exists on an object, it will check on its prototype.
The prototype being an object itself, the prototype has a prototype.
Hence, the process repeats until the final prototype of all objects has been reached.

This is referred as the **prototype chain**.
It must not be confused with inheritance.
This mechanism is actually **delegation**.



## Simulating inheritance

Now that we know about the prototype chain, we have a better idea on where inheritance behavior comes from.

We want `MyError` to prototypically inherits from `Error`.
As we saw, a prototype is an object.
So our prototype should be an instance of `Error`.

``` javascript
function MyError(message) {
  this.name = 'MyError';
  this.message = message || 'Default message';
}

MyError.prototype = new Error();
MyError.prototype.constructor = MyError;

let error = new MyError();
```

By default, each prototype comes with an implicit `constructor` property who's value is the constructor function.
Unfortunately, by assigning the prototype to a new object, we break this behavior.
We must set back the `constructor` property to our `MyError` function.
Otherwise, the `constructor` property lookup will find a match in the prototype's prototype and return the `Error` function.

Notice how we did not defined a `stack` property on `MyError`?
However, if we access it with `error.stack` we get a value back.
This is because the lookup went through the prototype chain until it encountered a property matching `stack`.

To have an idea of what it looks like, here is the `error` with its prototype chain represented under the `__proto__` property:

``` javascript
{
  message: "Default message",
  name: "MyError",
  // No property 'stack' here
  __proto__: {
    constructor: function MyError(message),
    // Here is a property named 'stack'. No need to go further.
    stack: "Error\n    at <anonymous>:6:21",
    __proto__: {
      constructor: function Error(),
      message: "",
      name: "Error",
      toString: function toString()
      __proto__: {
        ...
        constructor: function Object(),
        hasOwnProperty: function hasOwnProperty(),
        __proto__: null
      }
    }
  }
}
```

The lookup went only to the `MyError` prototype to find a `stack` property because it is an instance of `Error`.
Instances of `Error` have a `stack` property.

If we want to know if `error` has a property, we use `error.hasOwnProperty('name')`.
Notice that `hasOwnProperty` is defined on the Object's prototype.
To access `error.hasOwnProperty`, the lookup went all the way to the Object's prototype.

That's how we have an inheritance behavior with delegation.



# Conclusion

Thanks to the prototype chain and delegation, we can reproduce an inheritance _behavior_.

Here is a code snippet to get the prototype chain from any value in JavaScript:

``` javascript
Object.getPrototypeChain = function getPrototypeChain(obj) {
  if (typeof obj == 'undefined' || typeof obj === 'null') return [];
  let next = obj, chain = [];
  while (next !== null) {
    chain.push(next = Object.getPrototypeOf(next));
  }
  return chain;
}
```

Use it like this: `Object.getPrototypeChain(123)`.
It should return an array like this: `[Number, Object, Null]`.
