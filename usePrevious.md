# Introduction

You've probably seen the pattern where `useRef` is used to keep track of the previous value of a state. But how does it always stay one time behind the `useState`?

That's what we'll dive into today!

# usePrevious hook

```jsx
import { useRef, useEffect } from "react";

function usePrevious(value) {
  const ref = useRef();

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref.current;
}
```

We have a custom hook called `usePrevious` that takes a value and returns the previous value. Let's break it down:

1. We import `useRef` and `useEffect` from React.
2. We define a function called `usePrevious` that takes a `value` as an argument.
3. We create a `ref` using `useRef()`.
4. We set the `ref.current` to the `value` inside the `useEffect` hook.
5. We return `ref.current`.

So, how come `ref.current` always stays one time behind the `useState`?

To understand this, we have to understand how `useRef` and `useState` work.

# useRef

`useRef` returns a mutable object whose `.current` property is initialized to the passed argument (initial value). Mutating the `.current` property doesn't trigger a re-render. This is important to understand because it allows us to store mutable values that persist across renders.

# useState

`useState` returns a tuple with two values: the current state and a function to update the state. When you call the update function, React schedules a re-render with the new state.

# Why useRef stays one time behind useState

During the very first render of the component, `useEffect` hasn't had a chance to run yet. React's effect hooks run after the render phase. This is intentional so that they don't block the rendering process. This also applies to every re-render.

Now that we understand the difference pieces, let's go over the flow:

1. During the initial render, the `useRef` hook is initialized with the provided `initialValue`, setting `ref.current` to that value.
2. After the component renders and commits to the DOM, the `useEffect` runs and updates `ref.current` with the `newValue`. Remember, this happens after the render phase. "Commit" means that React has applied the changes to the DOM.
3. However, this update to `ref.current` does not trigger a re-render of the component.
4. On the next render cycle (e.g. when state/props change), the `useRef` hook returns the same ref object it created during the initial render.
5. This means that `ref.current` still holds the `newValue` from the previous render, not the current value.
6. During that next render, `ref.current` will hold the `newValue` that was assigned in the previous render's `useEffect`.
   1. This is the key point to understand. The `useEffect` hook updates the `ref.current` value, but that update doesn't trigger a re-render.

# Conclusion

Understanding how the `usePrevious` hook works requires understanding `useRef`, `useState`, `useEffect` and the order in which they run. By understanding these concepts, you can build more complex hooks and components that leverage the power of React's hooks system.

When working with XState, it's generally recommended to define the state machine first, before introducing it to your components or application logic. This allows you to focus on modeling the state transitions and logic separately from the UI or other concerns.
