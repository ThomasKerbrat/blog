# Argument Labels with ES6 Destructuring


## What is an argument label and why to use them?

An argument label is an identifier, that you define in a function signature, to name that function's arguments.
But how is it different from the arguments names themsleves?

Let's take this C# example:

``` c#
public class MyClass {
  public static string SayHello(string person1, string person2) {
    return $"Hello {person1} and {person2}!";
  }
}
Console.WriteLine( MyClass.SayHello(person2: "Ted", person1: "Bill") );
// Outputs: "Hello Bill and Ted".
```

We declare a static method ```SayHello``` in ```MyClass```.
This method must be called with two strings.
Here we call the method with the parameters names repeated inside the call.
Thus, we can order them as we want.
Of course we can omit to name the arguments, but in this case it must follow the declaration order.

Naming arguments while calling a function is useful to identify the parameter/value association.
It helps to read the code and to understand it quicker.
That's what is called _parameter names_.

Swift allows to add an additional _external parameter name_.
If present in the declaration, you must explicitly use it when you call the function.
In this case, you can't use the parameter name, you have to use the external one.

Here is a Swift example:

``` swift
func sayHello(to person1: String, and person2: String) -> String {
    return "Hello \(person1) and \(person2)!"
}
print(sayHello(to: "Bill", and: "Ted"))
// Prints "Hello Bill and Ted!"
```

Having this feature allows us to have beautiful APIs that reads nicely.


## What about JavaScript?

JavaScript does not support this feature, nor TypeScript
(and it will not, see this [thread](https://github.com/Microsoft/TypeScript/issues/467)).

With ES5, you can only see one parameter in the function declaration, the ```options``` object.
When we call the function, the object literal must contain the exact property names used in the function.
We could say that the implementation leaked outside because of that.

``` javascript
function sayHello(options) {
  return `Hello ${options.person1} and ${options.person2}!`;
}
console.log(sayHello({ person1: "Bill", person2: "Ted" }));
```

However, thanks to ES6 destructuring, we can achieve a similar behavior.
In a lot of frameworks, we often find that functions take a configuration object.
Usually, we create an object inside the function call, thanks to the object literal syntax.

With ES6 destructuring, it has become easy to read function declaration.
It also bring the ability to create _external parameter names_.
Plus, we declare variables instead of accessing ```options``` properties.

``` javascript
function sayHello({ to: person1, and: person2 }) {
  return `Hello ${person1} and ${person2 }!`
}
console.log(sayHello({ to: "Bill", and: "Ted" }))
```

Here we are declaring local variables ```person1``` and ```person2``` in the function signature.
The destruction of the passed object will initialize our variables to the corresponding values.


# Conclusion

Destructuring is a good way to write self describing code, that read nicely at point of use.

However, it can become a mess.
A configuration object can have a lot of properties, possibly nested.
So I would not recommend having one big function who destructures a big configuration object,
but rather (if possible) multiple functions with clear and simple names and signatures.

Although it is possible to write the function call on multiple line,
please note that my example where simple enough to have them in one line.
