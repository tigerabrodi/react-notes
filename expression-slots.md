In JSX, when you do curly brackets, you can put any JavaScript expression inside of them. This includes calling functions, using operators, and more.

```jsx
function App() {
  const name = "World";
  return <h1>Hello, {name}!</h1>;
}
```

In this example, we're using the `+` operator to concatenate two strings together. We're also calling a function `getName()` to get the name of the user.

```jsx
function App() {
  const firstName = "John";
  const lastName = "Doe";
  return <h1>Hello, {firstName + " " + lastName}!</h1>;
}
```

What actually happens under the hood is that React simply forwards what's inside the curly brackets to `React.createElement()`. This means that you can put any valid JavaScript expression inside of them.

For example, above may be compiled to:

```jsx
const compiledElement = React.createElement(
  "h1",
  null,
  "Hello, ",
  firstName + " " + lastName,
  "!"
);
```
