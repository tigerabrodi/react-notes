# Introduction

I'm really excited about this post. I've used `Suspense` in React but never really understood how the whole thing works.

Another thing that's interesting is the concept of a "suspended component".

What does it mean when a component "suspends"?

We'll dive into all of that in this post.

# Pre-requisites

This is quite an advanced post.

I assume you have a good understanding of React and React Server Components.

# What does "suspend" mean?

In React, a component is said to "suspend" when it's in the middle of rendering and needs to wait for some asynchronous operation to complete. This could be anything from fetching data from an API to dynamically importing a module.

React can handle suspended components in two different ways - either by showing a fallback UI or by "pausing" the rendering of the component until the asynchronous operation completes.

# Suspending witout Suspense

A component can suspend even if it's not wrapped in a Suspense boundary. Here's a simple example:

```jsx
function DataComponent() {
  const data = await fetchData(); // fetchData is an asynchronous operation
  return <div>{data}</div>;
}

function App() {
  return (
    <div>
      <h1>My App</h1>
      <DataComponent />
    </div>
  );
}
```

In this example, when `<DataComponent />` is rendered, it will call `fetchData()`. Because `fetchData()` is an asynchronous operation, the component will suspend until the operation completes. Meaning, it will not render anything until the data is fetched.

Since we're not using a Suspense boundary, React will not show a fallback UI. Instead, it will just pause the rendering of the component until the data is fetched. Once fetched, React will discard the entire `<App />` component and re-render it with the fetched data.

This is not ideal. With Suspense, we wouldn't need to discard the entire `<App />` component.

# Clarification

Render here refers to rendering on the server.

On the server, React will render to achieve the final HTML that will be sent to the client.

We can't send a response while a component is suspended. That would be sending an incomplete HTML response.

# Suspending with Suspense

Let's add a Suspense boundary to the previous example:

```jsx
function DataComponent() {
  const data = await fetchData(); // fetchData is an asynchronous operation
  return <div>{data}</div>;
}

function App() {
  return (
    <div>
      <h1>My App</h1>
      <Suspense fallback={<div>Loading...</div>}>
	<DataComponent />
      </Suspense>
    </div>
  );
}
```

In this case, when `<DataComponent />` suspends, React will look for the nearest Suspense boundary above it in the component tree. It finds `<Suspense>`, and instead of discarding the entire `<App />`, it will render the fallback UI (`<div>Loading...</div>`) until `fetchData()` resolves. Once the data is fetched, React will discard the entire `<App />` component and re-render it with the fetched data.

Once `fetchData()` resolves, React will replace the fallback UI with the actual rendered content of `<DataComponent />`.

## Better experience

This is where streaming comes in. Streaming is the process of sending the HTML to the client as soon as it's rendered on the server. The server can split the HTML into chunks and send them to the client as they're rendered. So, if a component takes longer to fetch data, the client can start rendering the HTML that's already rendered on the server. That's why again, Suspense is so important. Otherwise, the client would have to wait for the entire component to be rendered before it can start rendering the HTML.

It provides a much better experience. The HTML will be sent to the client as soon as it's rendered. The client can start rendering the HTML while the component is suspended.

So while the component is fetching data, the client can render the fallback UI. Once the data is fetched, the client can replace the fallback UI with the actual content of the component.

It's done without discarding the entire component and re-rendering it.

This improves perceived performance and time to first byte, which means the time it takes for the client to receive the first byte of the response.

# Recap

As mentioned, there are two scenarios when it comes to suspending components:

1. **Without Suspense**: React will pause the rendering of the component until the asynchronous operation completes. It will not show a fallback UI. Once done, React will discard the entire component and re-render it with the fetched data.
2. **With Suspense**: React will show a fallback UI when a component suspends. Once the asynchronous operation completes, React will replace the fallback UI with the actual content of the component.
