# Keys in React

When mapping over an array of elements in React, you need to provide a key prop to each element. If you don't, React will throw a warning in the console.

- What should we use as a key?
- What happens if we don't provide a key?
- So why do we need to provide a key prop?

# Problematic code

Let's look at some code where React throws a warning because we didn't provide a key prop:

```jsx
function App() {
  const items = ["apple", "banana", "cherry"];

  return (
    <ul>
      {items.map((item) => (
        <li>{item}</li>
      ))}
    </ul>
  );
}
```

This code will throw a warning in the console:

```
Warning: Each child in a list should have a unique "key" prop.
```

# What's the problem?

When you map over an array of elements, React needs a way to identify each element uniquely.

Imagine the files on your computer didn't have a name. They were identified only by their order. If you moved a file to a different position or deleted a file, you wouldn't know which file you were referring to. This is an analogy coming from React's own documentation.

React needs a way to always know which element is which. That's why you need to provide a key prop.

# Rules

Two rules to follow when choosing a key:

1. **Stable**: The key should be stable. It shouldn't change between renders.
2. **Unique**: The key should be unique among siblings.

It's also why you shouldn't use the index as a key. If the order of the elements changes, React won't be able to identify which element is which.

# Solution

Let's fix the warning by providing a key prop:

```jsx
function App() {
  const items = ["apple", "banana", "cherry"];

  return (
    <ul>
      {items.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
}
```

In this case, it's ok to use the item itself as a key because the items are unique.

In other cases where you get data from backend, you'll likely want to use `id` or some other unique identifier instead of the item itself.

# Surprise, it's not a prop

The key prop is not an actual prop that gets passed to the component. It's a special attribute that React uses internally to keep track of elements. That's why you can't access the `key` prop inside the component.

If we look at the JSX from the previous example again:

```jsx
function App() {
  const items = ["apple", "banana", "cherry"];

  return (
    <ul>
      {items.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
}
```

When this JSX is transpiled, it will look something like this:

```jsx
const element = {
  type: "ul",
  props: {
    children: [
      {
        type: "li",
        key: "apple",
        props: {
          children: "apple",
        },
      },
      {
        type: "li",
        key: "banana",
        props: {
          children: "banana",
        },
      },
      {
        type: "li",
        key: "cherry",
        props: {
          children: "cherry",
        },
      },
    ],
  },
};
```

As you can see, the `key` is a top-level property of the element, not a prop that gets passed to the component. React uses this `key` to identify the element internally and keep track of it across re-renders.

So remember, when mapping over an array of elements in React, always provide a unique and stable `key` to each element. It's not a prop, but a special attribute that React uses under the hood to efficiently update the DOM.
