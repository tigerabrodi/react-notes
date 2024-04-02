# TypeScript is a superset of JavaScript

What does it mean that TypeScript is a superset of JavaScript?

That's what we'll dive into today and uncover exactly what that means.

# TypeScript isn't a real language

TypeScript is a superset of JavaScript. It contains all the features of JavaScript and extends them with additional functionality. Essentially, any valid JavaScript code is also valid TypeScript code. TypeScript adds static typing to JavaScript, allowing developers to specify types.

However, TypeScript doesn't run in production. TypeScript is transpiled to JavaScript before execution, meaning that TypeScript code is converted into plain JavaScript code which can then be run in any JavaScript environment. This transpilation process is necessary because browsers and most environments do not natively understand TypeScript

# Duck Typing

In TypeScript, types aren't unique. They are just a way to describe the shape. In many other languages, types are unique.

Duck typing simply means that the type of an object is determined by its shape. The term comes from the phrase "If it looks like a duck, swims like a duck, and quacks like a duck, then it probably is a duck."

This means that TypeScript's type system is structural rather than nominal. In a nominal type system, two types are considered equivalent or compatible if and only if their declarations name the same type.

A structural type system can lead to weird behaviors. For example, if two different types have the same structure, TypeScript will consider them to be compatible, even if they were intended to be distinct.

Another example is that if you pass an object to a function that expects a specific type, as long as the value you pass satisfies the type's structure, TypeScript will not complain.

# Nominal Typing in TypeScript

Surprise, surprise!

You can achieve nominal typing in TypeScript by using symbols.

Symbols are unique identifiers that can be used to represent a type. They're guaranteed to be unique, so you can use them to distinguish between different types.

We will "tag" our types with symbols to make them nominal.

Let's say we have two types, CustomerProfile and AdminProfile. They have the same structure, but they are different types. And we decide for some reason they should be distinct.

Looking at the code, we can see that if we try to assign a CustomerProfile to an AdminProfile, TypeScript will complain.
