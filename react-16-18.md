# Introduction

In this post, we'll look at the history of React and how it has evolved over the years since the introduction of hooks.

The reason I'm writing this is because I felt like React 17 didn't really introduce anything new, but React 18 felt like such a big leap forward.

I'm writing this post for myself. To really understand what has changed and why it has changed.

The main focus will be React 18.

# Hooks

Hooks were introduced in React 16.8. This was released in February 2019. It was a massive change in how we write React components.

## The challenges hooks solved

Hooks weren't of course mindlessly introduced. They solved a lot of problems that we had with class components.

1. **Reusability**: With class components, we had to use higher-order components or render props to share logic between components. Hooks made it easier to share logic between components by extracting it into a custom hook.
2. **Complexity**: Class components had a lot of boilerplate code. The more complex the component, the harder it was to read and maintain. Hooks made it easier to read and write complex logic.
3. **this**: The `this` keyword in class components was a source of confusion for many developers. Hooks removed the need for `this` and made it easier to reason about the code.
4. **Simplicity**: Hooks made it easier to write simpler components. You didn't have to worry about lifecycle methods or class properties. You could just write functions.
5. **Performance**: Hooks made it easier to optimize performance. You could use the `useMemo` and `useCallback` hooks to memoize values and functions.

As you can see, hooks solved a lot of problems that we had with class components. They made it easier to write and maintain React components.

# React 17

React 17 was released in October 2020. Even though it didn't introduce any new features, it was an important release because it was a stepping stone to React 18.

Let's take a look at the problems React 17 solved.

## Event Pooling

In React versions prior to 17, React used a technique called "event pooling" to improve performance. When an event is triggered (like a click), React creates a synthetic event object that wraps the native browser event.

Creating new event objects for every event can be expensive in terms of memory and performance, especially if there are many events being triggered. So React used a technique called "event pooling" to reuse event objects. When an event is triggered, React would reuse the same event object for all event handlers.

Essentially, if you had a new event, React would use the same event object and update its properties.

While this improved performance, it had some downsides:

- It made it difficult to use asynchronous event handlers because the event object could be reused by the time the async operation completed. This could lead to inconsistent behavior.
- It could lead to subtle bugs if the code relied on the event object being immutable. Which you may want when working with Redux or other state management libraries because you don't want the event object to change.

In React 17, event pooling has been removed. Now, each event is a unique instance, which makes the behavior more predictable and allows for async event handlers.

It's also worth mentioning that the performance gains from event pooling were minimal in modern browsers.

## Tree Reconciliation

React's core strength is its ability to efficiently update the UI in response to changes in data. It does this through a process called "reconciliation".

When a component's state or props change, React creates a new virtual DOM tree. It then compares this new tree with the previous one to determine what has changed. Finally, it updates the real DOM to reflect these changes. This process is called "diffing".

In React 16 and earlier, the reconciliation algorithm would traverse and compare the entire virtual DOM tree, even if only a small part of it had changed. This could be inefficient, especially for large applications.

React 17 introduces changes to the reconciliation algorithm. Now, React will only traverse the parts of the tree that have actually changed. This improves performance, especially for large applications.

## Portals

Portals provide a way to render children into a DOM node that is outside the parent component's DOM hierarchy. This can be useful for scenarios like modals, tooltips, and popovers.

While portals have existed since React 16, they had some limitations. For example, events triggered from within a portal would not bubble up to the parent component.

React 17 improves the support for portals. It ensures that events triggered within a portal will properly propagate to the parent component, regardless of whether the parent is a React component or a regular DOM node.

## Gradual Upgrades

One of the biggest challenges with any library or framework is upgrading to a new major version. It's often a pain and requires a lot of work to ensure that everything still works as expected.

React 17 aims to make this process easier by enabling gradual upgrades. This means you can upgrade your application piece by piece, rather than all at once.

For example, you could have the majority of your application running on React 17, but keep a few complex components on React 16. This makes the upgrade process more manageable and less risky.

## New JSX Transform

JSX is a syntax extension for JavaScript that allows you to write HTML-like code in your JavaScript files. Before the browser can understand JSX, it needs to be transformed into regular JavaScript. This was typically done by Babel, a popular JavaScript compiler.

In React 17, a new JSX transform is introduced. This transform is a pure compiler transform, which means it doesn't require React to be in scope. This can lead to smaller bundle sizes and slightly improved performance.

# React 18

React 18 was released in March 2022. It was a big release with a lot of new features and improvements.

## Concurrent React

Concurrent React is a new feature introduced in React 18 that fundamentally changes how React handles rendering and updates. It allows React to work on multiple tasks concurrently, enabling it to prioritize and respond to high-priority updates more efficiently.

## Interruptible Rendering

One of the key aspects of Concurrent React is interruptible rendering. Prior to React 18, rendering was a synchronous and uninterruptible process. Once React started rendering an update, it couldn't be paused or interrupted until the entire component tree was rendered.

With Concurrent React, rendering can be paused and resumed at any time. This allows React to prioritize high-priority updates and respond to user interactions more quickly.

## Prioritizating Updates

Concurrent React introduces the concept of prioritizing updates. It allows React to categorize updates into two types: urgent and transition.

**Urgent updates:** These are high-priority updates that require immediate attention, such as user interactions like clicking a button or typing in an input field. React ensures that urgent updates are processed quickly to maintain a responsive user interface. By default, all updates are considered urgent unless specified otherwise.

**Transition updates:** These are lower-priority updates that can be interrupted or deferred if more urgent updates come in. Transition updates are typically used for non-critical UI changes, such as rendering a large list of items or updating a complex component tree. This is achieved by using the `startTransition` API to mark a batch of updates as transition updates.

By prioritizing updates, React can ensure that the user interface remains responsive and interactive.

## React Fiber

The old reconciler in React used a Stack:

- It was blocking.
- It was synchronous.
- Had to work until it was done.

Once the reconciler started working, it had to finish before anything else could happen. This could lead to janky user interfaces, especially for complex applications. Because there was no way to prioritize updates.

With that in mind, we needed a new way to handle updates:

- Ability to prioritize updates.
- Ability to pause and resume rendering.
- Ability to work on multiple tasks concurrently.

That's why React Fiber was introduced. It's important to note that React Fiber was introduced in React 16, but it's vital to understand it to understand Concurrent React.

React Fiber is a reimplementation of React's core algorithm. It changes the fundamental way React works. It's not just about performance, but also about enabling new features like Concurrent React.

### What is Fiber

At it's core, Fiber is simply a JavaScript object that represents a unit of work. It's a lightweight representation of a component tree that React can work on.

React processes Fibers and when done, it commits the changes to the DOM.

This all happens in two phases:

1. **Render phase:** During this phase, React does asynchronous work behind the scenes. It processes all Fibers. Because it happens asynchronously, work can be prioritized, paused, resumed, and aborted. Internal functions like `beginWork()` and `completeWork()` are called during this process.
2. **Commit phase:** Once the render phase is complete, React commits the changes to the DOM by calling `commitWork()`. This is synchronous and can't be interrupted.

### Fiber `tag` property

A Fiber always has a 1-1 relationship with a component instance, DOM node, etc. The type of the thing is stored inside the `tag` property. It can be a number between 0 and 24. In the React codebase, these numbers are constants:

```js
export const FunctionComponent = 0;
export const ClassComponent = 1;
export const IndeterminateComponent = 2;
export const HostRoot = 3;

// ...
```

`stateNode` property holds a reference to the thing that the Fiber represents. For example, if the Fiber represents a class component, `stateNode` will hold a reference to the instance of that class component.

### Fiber vs React Elements

Fibers are similar to React elements. In fact, they're often created from React elements and share the `type` and `key` properties. While React elements are re-created on every render, Fibers are reused.

Fibers only need to be created once and can be updated and reused multiple times. This is what allows React to pause and resume rendering.

### Fiber Relationships

Fiber nodes are organized in a tree structure that mirrors the component tree. Each Fiber has a reference to its parent, child, and sibling Fibers. This allows React to traverse the tree efficiently and perform updates.

There are three properties that define the relationships between Fibers: Child, Sibling, and Return.

- **Child:** Points to the first child Fiber of the current Fiber.
- **Sibling:** Points to the next sibling Fiber of the current Fiber.
- **Return:** Points to the parent Fiber of the current Fiber.

Let's look at some code:

```jsx
<div>
  <p>Hello</p>
  <p>World</p>
</div>
```

Here we have three elements: a `div` with two `p` elements inside it. React will create a Fiber for each of these elements and organize them in a tree structure. `div` will be the parent of the two `p` elements, and the two `p` elements will be siblings of each other. The child of `div` will be the first `p` element.

### What is work?

In the Fiber architecture, "work" refers to the units of work that React needs to perform to update the UI. Each unit of work is represented by a Fiber node. These units can be prioritized, paused, resumed, and aborted as needed.

### Time Slicing

Time slicing is a technique used by React Fiber to break down the rendering work into smaller chunks.

Instead of performing all the work in a single, synchronous go, React Fiber can pause and resume the work as needed.

This allows React to split the rendering process into multiple frames, preventing the main thread from being blocked for too long.

### Scheduler

React Fiber uses a scheduler to prioritize and manage the work.

The scheduler assigns different priorities to different types of work, such as user interactions, updates, and background tasks.

Higher-priority work, like user input, is given preference and is executed earlier than lower-priority work.

### requestAnimationFrame

React Fiber uses `requestAnimationFrame` to schedule the rendering work.

`requestAnimationFrame` is a browser API that allows scheduling a callback to be executed before the next repaint. A "repaint" is when the browser updates the screen with the latest changes.

By using `requestAnimationFrame`, React Fiber can coordinate the rendering work with the browser's painting process, ensuring smooth and efficient updates.

### requestIdleCallback

React Fiber also uses `requestIdleCallback` to schedule low-priority work.

`requestIdleCallback` is a browser API that allows scheduling a callback to be executed when the browser is idle and not busy with other tasks. So, if the browser is busy with high-priority work, the low-priority work will be deferred until the browser is idle.

### `beginWork` and `completeWork`

`beginWork` and `completeWork` are two internal functions in React Fiber that are responsible for processing the work. Together, they perform the rendering and reconciliation of the Fiber tree.

`beginWork` going down the tree, and `completeWork` going back up the tree.

### `beginWork`

`beginWork` is called during the render phase, which is the first phase of the fiber reconciliation process. Inside the `beginWork` function, React compares the current props with the new props or checks for context changes, and sets flags to indicate if updates are needed.

`beginWork` recursively performs work on the fiber tree, traversing down the tree until there are no more child nodes.

### `completeWork`

`completeWork` is called during the commit phase, which is the second phase of the fiber reconciliation process. `completeWork` is called when `beginWork` returns null, indicating that there is no more work to be done on the current branch.

It traverses back up the fiber tree, completing the work for each node. During completeWork, React can create, update, or delete DOM nodes based on the reconciliation results.

### Fiber trees

We have the current tree and the work-in-progress tree. The current tree is the one that is currently rendered on the screen. The work-in-progress tree is the one that React is working on. It's a copy of the current tree with the updates applied. The reason we don't update the current tree directly is that it could lead to inconsistencies if e.g. an error occurs during the update.

When React starts rendering, it creates a work-in-progress tree that mirrors the current tree. As React processes the work, it updates the work-in-progress tree. Once the work is complete, React swaps the current tree with the work-in-progress tree.

## Automatic Batching

Prior to React 18, only updates triggered by React events (like `onClick` or `onChange`) were batched. Updates triggered by other events (like `setTimeout` or `fetch`) were not batched, which could lead to performance issues.

The issues would occur because React would have to perform a separate render for each update, even if they were triggered in quick succession. This could lead to janky user interfaces and unnecessary re-renders.

Let's look at an example:

```jsx
// React 17
const handleClick = () => {
  setCount(count + 1); // Triggers a re-render
  setFlag(!flag); // Triggers another re-render
};

// React 18
const handleClick = () => {
  setCount(count + 1); // Queued state update
  setFlag(!flag); // Queued state update
  // React batches the updates and re-renders once
};
```

With automatic batching, state updates are queued and batched together, regardless of how they are triggered. This leads to fewer re-renders and a smoother user experience.

React 18 also provides an escape hatch called `flushSync` that allows you to opt-out of automatic batching and force synchronous state updates when needed:

```jsx
import { flushSync } from "react-dom";

const handleClick = () => {
  flushSync(() => {
    setCount(count + 1); // Triggers an immediate re-render
  });
  setFlag(!flag); // Queued state update
};
```

In this example, the `setCount` update will be processed synchronously, while the `setFlag` update will be queued and batched with other state updates.

## Suspense improvements

Suspense was introduced in React 16.6 as a way to specify the loading state for a part of the component tree. It allowed components to suspend rendering while waiting for data to load. "Suspend" means that the component will not render until the data is available.

A basic example of Suspense:

```jsx
<Suspense fallback={<Spinner />}>
  <Comments />
</Suspense>
```

Here, the `Suspense` component wraps the `Comments` component. If the `Comments` component needs to fetch data before rendering, it can suspend rendering and show the `Spinner` component as a fallback.

Suspense was also used for code-splitting, where components could be loaded lazily when needed. Lazily loading components means that they are only fetched when they are needed, reducing the initial bundle size and improving performance.

### Suspense on the server

With React 18, Suspense can be used on the server. With server-side Suspense, you can use Suspense to handle asynchronous data fetching on the server and progressively render the HTML as the data becomes available.

Progressively rendering the HTML means that the server can send parts of the page to the client as they become available, rather than waiting for the entire page to be rendered before sending anything. This can lead to a faster perceived loading time for users. This is also known as "streaming".

Here's an example of using Suspense on the server:

```jsx
function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <Comments />
    </Suspense>
  );
}

async function renderToHTML() {
  const html = await renderToString(<App />);
  return html;
}
```

In this example, the server will render the App component and suspend rendering until the Comments component is ready. The server will send the fallback HTML (`<Spinner />`) to the client first, and then stream the rendered Comments component as soon as it's available.

### Suspense boundaries

React 18 introduces the concept of "Suspense boundaries." A Suspense boundary is a component that wraps one or more components that may suspend. It allows you to specify the loading state for a specific part of the component tree.

Here's an example of using Suspense boundaries:

```jsx
function App() {
  return (
    <>
      <Suspense fallback={<HeaderSpinner />}>
        <Header />
      </Suspense>
      <Suspense fallback={<MainContentSpinner />}>
        <MainContent />
      </Suspense>
      <Suspense fallback={<FooterSpinner />}>
        <Footer />
      </Suspense>
    </>
  );
}
```

In this example, we have three Suspense boundaries: one for the `Header`, one for the `MainContent`, and one for the `Footer`. Each boundary has its own fallback component. This allows for more granular control over the loading experience, as each part of the UI can display its own loading state independently.

### SuspenseList

React 18 introduces a new component called SuspenseList that allows you to coordinate the loading sequence of multiple Suspense boundaries.

With SuspenseList, you can specify the order in which the Suspense boundaries should be revealed. It provides options like showing the Suspense boundaries in a defined order (`forwards`), in the reverse order (`backwards`), or revealing them together (`together`).

Here's an example of using SuspenseList:

```jsx
<SuspenseList revealOrder="forwards">
  <Suspense fallback={<ProfilePictureSpinner />}>
    <ProfilePicture />
  </Suspense>
  <Suspense fallback={<ProfileDetailsSpinner />}>
    <ProfileDetails />
  </Suspense>
  <Suspense fallback={<FriendListSpinner />}>
    <FriendList />
  </Suspense>
</SuspenseList>
```

In this example, the SuspenseList component coordinates the loading sequence of the `ProfilePicture`, `ProfileDetails`, and `FriendList` components. With `revealOrder="forwards"`, the Suspense boundaries will be revealed in the order they are defined, ensuring a top-to-bottom loading experience.

## New Root API

React 18 comes with a new Root API.

The new Root API is a set of methods provided by the `react-dom/client` package in React 18. It is designed to replace the legacy `ReactDOM.render` method and offers a more powerful way to manage the rendering of React components.

The main method in the new Root API is `createRoot`, which creates a root for rendering React components. Here's how you can use it:

```jsx
import { createRoot } from "react-dom/client";

const container = document.getElementById("root");
const root = createRoot(container);

root.render(<App />);
```

`createRoot` creates an object that represents a root for rendering React components. You can then call the `render` method on the root object to render a component into the specified container.

The new Root API has advantages over the legacy `ReactDOM.render` method:

- It supports concurrent rendering, allowing React to work on multiple tasks concurrently.
- You can create multiple roots for rendering different parts of the application independently.
- Error handling is improved, with better support for error boundaries and error recovery.

## Strict Mode

React 18 introduces a new feature called "Strict Mode." Strict Mode is a tool that helps you write better React code by highlighting potential problems and deprecated features.

This only runs in development mode.

One example is the detection of unexpected side effects. Strict Mode runs the render function twice to detect side effects that may cause issues in your application.

## Hooks

### useTransition

`useTransition` gives you two things: a way to start a transition and a way to check if a transition is pending.

`startTransition` is a method provided by the useTransition hook in React 18. It allows you to mark certain state updates as "transitions" aka non-urgent updates.

Here's an example:

```jsx
import React, { useState, useTransition } from "react";

function App() {
  const [isPending, startTransition] = useTransition();
  const [count, setCount] = useState(0);
  const [items, setItems] = useState([]);

  const handleClick = () => {
    // Urgent update
    setCount(count + 1);

    // Transition update
    startTransition(() => {
      const newItems = Array(20000)
        .fill()
        .map((_, index) => ({
          id: index,
          text: `Item ${index}`,
        }));
      setItems(newItems);
    });
  };

  return (
    <div>
      <button onClick={handleClick}>Click me</button>
      <p>Count: {count}</p>
      {isPending && <p>Loading...</p>}
      <ul>
        {items.map((item) => (
          <li key={item.id}>{item.text}</li>
        ))}
      </ul>
    </div>
  );
}
```

In this example:

- We use the useTransition hook to get the isPending boolean and the startTransition function.
- When the button is clicked, we perform an urgent update by incrementing the count state immediately.
- We wrap the expensive state update (setting a large array of items) inside the startTransition callback. This marks it as a non-urgent update.
- If there are any pending transitions (isPending is true), we display a loading message.

### useDeferredValue

`useDeferredValue` lets you defer the update of a value until more urgent updates have been completed.

Here's an example:

```jsx
import React, { useState, useDeferredValue } from "react";

function App() {
  const [text, setText] = useState("");
  const deferredText = useDeferredValue(text);

  const handleChange = (e) => {
    setText(e.target.value);
  };

  return (
    <div>
      <input type="text" value={text} onChange={handleChange} />
      <ExpensiveComponent text={deferredText} />
    </div>
  );
}

function ExpensiveComponent({ text }) {
  // Simulating an expensive computation
  const expensiveResult = expensiveComputation(text);

  return <p>Expensive Result: {expensiveResult}</p>;
}

function expensiveComputation(text) {
  // Simulating an expensive computation
  let result = "";
  for (let i = 0; i < 1000; i++) {
    result += text;
  }
  return result;
}
```

In this example:

- We have an input field where the user can enter text.
- The text state is updated immediately as the user types.
- We use the useDeferredValue hook to create a deferred version of the text value.
- The deferred text value is passed to an ExpensiveComponent that performs an expensive computation.

If we didn't use useDeferredValue, the expensive computation would run on every keystroke, which could lead to performance issues. By deferring the update of the text value, we ensure that the expensive computation only runs when the text value has stabilized.

"Stabilized" means that the deferred value has not changed for a certain period of time. This period is determined by the React scheduler. It uses "time slicing" to break down the rendering work into smaller chunks and prioritize high-priority updates.

### useId

The `useId` hook is used to generate unique IDs that are stable across server and client rendering. It helps in generating unique IDs for accessibility purposes or for linking related elements.

Here's an example:

```jsx
import { useId } from "react";

function FormInput({ label }) {
  const id = useId();

  return (
    <>
      <label htmlFor={id}>{label}</label>
      <input type="text" id={id} />
    </>
  );
}
```

### useSyncExternalStore

`useSyncExternalStore` is a powerful hook introduced in React 18 that allows you to subscribe to an external store and keep your components in sync with its data.

In the context of useSyncExternalStore, a store refers to any external data source that holds state outside of React. This could be:

- Third-party state management libraries like Redux, MobX, or Zustand.
- Browser APIs that expose mutable values and events to subscribe to, such as browser history, localStorage, or online/offline status.
- Custom stores or event emitters that you create yourself.

The key characteristic of a store is that it manages its own state independently of React, and provides a way to subscribe to changes in that state.

#### How it works

useSyncExternalStore takes three arguments:

```js
const state = useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

- `subscribe`: A function that subscribes to the store and returns an unsubscribe function. It is called whenever the component mounts or the store changes.
- `getSnapshot`: A function that returns the current snapshot of the store's data.
- `getServerSnapshot` (optional): A function that returns the initial snapshot of the store's data during server-side rendering.

#### Quick example

Here's a simple example of using useSyncExternalStore to subscribe to the online/offline status of the browser:

```jsx
import { useSyncExternalStore } from "react";

function useOnlineStatus() {
  const isOnline = useSyncExternalStore(
    () => {
      window.addEventListener("online", () => {});
      window.addEventListener("offline", () => {});
      return () => {
        window.removeEventListener("online", () => {});
        window.removeEventListener("offline", () => {});
      };
    },
    () => navigator.onLine
  );
  return isOnline;
}

function MyComponent() {
  const isOnline = useOnlineStatus();
  return <div>Online: {isOnline ? "Yes" : "No"}</div>;
}
```

In this example, the subscribe function adds event listeners for the 'online' and 'offline' events, and returns a cleanup function that removes those listeners. The `getSnapshot` function simply returns the current value of `navigator.onLine`.

#### Why not useEffect?

With React 18, as you know, there are new features like Concurrent Mode and Suspense that allow React to work on multiple tasks concurrently. This means that components can be rendered in a non-blocking way, and updates can be paused and resumed as needed.

These are all "edge cases" that you would have to handle manually with `useEffect`. `useSyncExternalStore` abstracts away the complexity of managing subscriptions and state updates, and provides a more declarative and efficient way to keep your components in sync with external stores.

Additionally, `useSyncExternalStore` supports server-side rendering with `getServerSnapshot`, which is not possible with `useEffect`.

# I'm gonna faint

This was such a long post. I literally just kept researching and writing it for myself to gain clarity on what has changed in React over the years.
