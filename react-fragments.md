# Fragments

Have you ever wondered why you can't have sibling elements in React?

To understand why, let's look at some React code and see what it transpiles to:

```jsx
function App() {
  return (
      <h1>Hello</h1>
      <h2>World</h2>
  );
}
```

This code will throw an error because React expects a single element to be returned from a component. If we were to transpile this code, it would look like this:

```jsx
return React.createElement("h1", null, "Hello");
return React.createElement("h2", null, "World");
```

The problem here is that we're returning two separate elements. This will break because when you return at the first line, the function will exit and the second line will never be reached.

How do we fix this?

```jsx
function App() {
  return (
    <React.Fragment>
      <h1>Hello</h1>
      <h2>World</h2>
    </React.Fragment>
  );
}
```

We can use `React.Fragment` to wrap multiple elements and return them as a single element. When transpiled, it will look like this:

```jsx
return React.createElement(
  React.Fragment,
  null,
  React.createElement("h1", null, "Hello"),
  React.createElement("h2", null, "World")
);
```

We can also use the shorthand syntax `<>` and `</>` to achieve the same result:

```jsx
function App() {
  return (
    <>
      <h1>Hello</h1>
      <h2>World</h2>
    </>
  );
}
```
