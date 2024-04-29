# Seen this as a tailwind coder?

```jsx
<button
  className={cn("px-4 py-2 rounded", {
    "bg-blue-500 text-white": isPrimary,
    "bg-gray-200 text-gray-800": !isPrimary,
  })}
>
  Click me
</button>
```

Have you ever wondered why everyone is using the `cn` function?

That's what we'll dive into today.

There are two problems `cn` solves:

1. Class conflicts
2. Conditional classes

# Class conflicts

Let's say we have a button component with `bg-blue-500` and `text-white` classes.

```jsx
export function Button({ className }) {
  return (
    <button className={`px-4 py-2 rounded bg-blue-500 text-white ${className}`}>
      Click me
    </button>
  );
}
```

Now, let's use this button in a component. However, we wanna override the background color to `bg-red-500`.

```jsx
<Button className="bg-red-500">Click me</Button>
```

The problem with tailwind is that overriding classes is not as simple as adding a new class. The outcome is unpredictable. Whether we put the `className` prop of the button at the beginning or the end, we don't know what Tailwind will prioritize.

Hence we use `twMerge` from `tailwind-merge` to deal with this.

```jsx
import { twMerge } from "tailwind-merge";

export function Button({ className }) {
  return (
    <button
      className={twMerge(`px-4 py-2 rounded bg-blue-500 text-white`, className)}
    >
      Click me
    </button>
  );
}
```

Huh! That works.

`className` will be passed and work as expected.

# Conditional classes

We have another problem. What if we want to conditionally apply classes?

We could still use `twMerge` for this. But it would become a bit messy.

```jsx
export function Button({ isPrimary }) {
  return (
    <button
      className={twMerge(
        `px-4 py-2 rounded`,
        isPrimary ? `bg-blue-500 text-white` : `bg-gray-200 text-gray-800`
      )}
    >
      Click me
    </button>
  );
}
```

You can imagine if we had multiple conditional classes, it would become a mess.

That's where `clsx` comes in.

It let's us specify the classes as key and the values as the condition.

It looks clean and is really convenient.

```jsx
import clsx from "clsx";

export function Button({ isPrimary }) {
  return (
    <button
      className={clsx(`px-4 py-2 rounded`, {
        "bg-blue-500 text-white": isPrimary,
        "bg-gray-200 text-gray-800": !isPrimary,
      })}
    >
      Click me
    </button>
  );
}
```

# `cn` into the picture

`cn` is a combines both `twMerge` and `clsx`.

```jsx
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

And yeah, that's the story of `cn`.
