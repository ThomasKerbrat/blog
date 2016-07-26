# Argument Labels with ES6 Destructuring

## What is an argument label and why to use them?

An argument label is an identifier, that you define in a function signature, to name that function's arguments.
But how is it different from the arguments names themsleves?

Let's take this example in C#:

``` c#
public class MyClass {
  public static void sayHello(string name, int age) {
    Console.WriteLine("Hello " . name . ". You are " . age . " years old.");
  }
}
```

You would then use this code as follows:

``` c#
public class Program {
  public static void Main(string[] args) {
    MyClass.sayHello("Thomas", 21); // Outputs: "Hello Thomas. You are 21 years old.".
    MyClass.sayHello(age: 21, name: "Thomas"); // Equivalent to the previous call.
  }
}
```

An immediate benefice of naming arguments while calling a function is to pass the arguments in any order we want.
But that's not the most useful benefice.

However, being able to know what value will be resulting in which argument is more useful.
It helps to read the code and to understand it quicker.
That's what is called _parameter names_.

Often, you will not want to name your arguments the same way whether you're using it internally or externally.
In Swift, this is present everywhere.
This is why Swift integrates a feature called _external parameters name_.

Here is an example from the Swift documentation:

``` swift
func someFunction(externalParameterName localParameterName: Int) {
  // function body goes here, and can use localParameterName
  // to refer to the argument value for that parameter
}
```

This allows Swift APIs to be extreamly readable.
Recently, Apple introduced The Grand Renaming of their Cocoa and Cocoa Touch APIs.
This leads to more readable code, as demonstrates the following example.
The goal is to make APIs to read grammatically.

``` swift
friends.remove(at: positionOfFormerFriend)
```

## What about JavaScript?

## Examples

# Conclusion
