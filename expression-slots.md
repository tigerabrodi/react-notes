# Expression Slots in React

Have you ever wondered how React evaluates what's inside curly braces `{}`?

That's what we'll dive into today!

# JSX transpilation

When you write JSX, it gets transpiled into JavaScript. The transpilation process is what allows you to write HTML-like syntax in JavaScript.

Let's take a look at an example:

```jsx
const element = <h1>Hello, world!</h1>;
```

This JSX code gets transpiled into the following JavaScript code:

```jsx
const element = React.createElement("h1", null, "Hello, world!");
```

`React.createElement()` is a function that creates a React element. It takes three arguments:

1. The type of element (in this case, `"h1"`).
2. The element's properties (in this case, `null`).
3. The element's children (in this case, `"Hello, world!"`).

So, how does React evaluate what's inside curly braces `{}`?

# What's inside the curly braces?

When you write JSX, you can use curly braces `{}` to embed JavaScript expressions. These expressions can almost be anything. It can't be for example a `for` loop or an `if` statement. But it can be a variable, a function call, or a ternary operator.

Why is that?

That's because when React sees the curly braces, it simply extracts the expression inside and puts it in the `children` of the element.

```jsx
const name = "John Doe";
const element = <h1>Hello, world! {name}</h1>;
```

This will transpile into:

```jsx
const name = "John Doe";
const element = React.createElement("h1", null, "Hello, world! ", name);
```

It's important to note that anything after the first two arguments of `React.createElement()` is considered the children of the element. It can be annotated like `React.createElement(type, props, ...children)`.

Going back to the example, as you can see, the `name` variable simply gets forwarded as a child of the `h1` element.

When you see this, it's starting to make sense why you can't use a `for` loop or an `if` statement inside the curly braces. React expects a single expression, not a block of code.

Let's take a look at a last example with an if statement:

```jsx
const isLoggedIn = true;
const element = <h1>Hello, world! {if (isLoggedIn) {
  return "John Doe";
} else {
  return "Guest";
}}</h1>;
```

If we were to forward this to `React.createElement()`, it would look like this:

```jsx
const isLoggedIn = true;
const element = React.createElement("h1", null, "Hello, world! ", if (isLoggedIn) {
  return "John Doe";
} else {
  return "Guest";
});
```

This doesn't work because `if` is a statement, not an expression. React expects an expression inside the curly braces, not a statement. An expression can be evaluated to produce a value on its own. If statements are used for control flow and executing different blocks of code based on a condition.

# Conclusion

Understanding how React evaluates expressions inside curly braces is crucial when working with JSX. Remember that React expects an expression, not a statement, inside the curly braces. This expression gets evaluated and passed as a child to the element.
