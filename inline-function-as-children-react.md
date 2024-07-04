# Ever see this?

Have you ever needed to pass a function as a child to a component that returns JSX?

By that I mean:

```jsx
<Wrapper>{() => <div>Hello</div>}</Wrapper>
```

It looks weird. When I first saw this, I thought: "Why not just pass the component itself as child?"

After digging into it, it actually has a purpose.

# The wrapper

When you receive children as a function, you can decide when to render the children.

```jsx
function Wrapper({ children }) {
  return <div>{shouldShow ? children() : null}</div>;
}
```

This gives you more fine-grained control over when to render the children.

Now, to be honest, you can still conditionally render the children if they weren't passed as a function.

# Pass additional props

As for the children, they can receive additional props.

```jsx
function Wrapper({ children }) {
  return <div>{children({ name: "John" })}</div>;
}
```

This would be used as:

```jsx
<Wrapper>{({ name }) => <div>Hello, {name}</div>}</Wrapper>
```

This may not be something you always wanna do, but it's useful when you have a more complex wrapper component.
