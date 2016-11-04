
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
if (error instanceof MyError) { } // true
if (error.constructor == MyError) { } // true
```

The `error.constructor` returns the function that was used to instantiate the object.
As ugly as it can be in real code, we can instantiate a new object without directly referring to its constructor function:

``` javascript
let error2 = new error.constructor();
```

The `error2` variable will be an instance of `MyError`.

## The Prototype

An object is a map - also called a dictionary - between a string and any value in JavaScript.
Thus, any object has its own space where it can store any value.
We access those values by accessing the value under a certain key - also called a property.
The usual notation is to refer to the object by its name, followed by a dot and then the name of the key.
But ultimately, the key is just a string.

``` javascript
let o = {
  x: 42,
  y: 21,
};

o.x; // 42
o.y; // 21

o["x"]; // 42
o["y"]; // 21
```

What happens if I try to access the `z` property on `o`?

``` javascript
o.z; // undefined
```

The expression is evaluated to `undefined`, which is its own type: `typeof undefined === 'undefined'`.
To understand the inner working of that lookup, we must learn about the prototype.

A prototype is just an object.
It is used as a fallback in case a property does not exist on an object, in our case `o`.
The prototype is associated to a constructor function.
For the `Object` constructor, we can refer to it as `Object.prototype`.

By default, our functions' prototypes are an empty object:

``` javascript
MyError.prototype; // {}
```

The nice thing about prototype is that they are used as a fallback.
Let's say I want to access an other property from my `error` object.
If I write `error.myProperty`, it is evaluated to `undefined`.

Now let's defined the `myProperty` property on the `MyError` prototype:

``` javascript
MyError.prototype.myProperty = 3712;
error.myProperty; // 3712
let error2 = new MyErro();
error2.myProperty; // 3712
```

Given any instance of `MyError`, we can now access `myProperty` and will return `3712`.
The `myProperty` property does not exists on the `error` instance.
It is defined on its prototype.

If the property does not exists on the instance, it will check on its prototype.
The prototype being an instance itself, the process repeats until the final prototype of all objects has been reached.

This is referred as the **prototype chain**.
