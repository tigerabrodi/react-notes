# Introduction

We have had the viewport height `vh` in CSS for a while now. For example, `100vh` represents 100% of the viewport height.

There has however been limitations with `vh`. In particular on mobile screens, the browser UI can take up a portion of the viewport height.

The browser UI contains e.g. the address bar where you can type in a URL.

# Diving into the problem

Let's say we have a `div` that we want to take up the full height of the viewport. We can set the height to `100vh`. This works well on desktop screens, but on mobile screens, the browser UI can take up a portion of the viewport height. This means that the `div` will not take up the full height of the viewport, but rather beyond that because of the browser UI.

This will cause the `div` to be scrollable, which is not what we want because that wouldn't be the "full height" of the viewport from the user's perspective.

A common solution to this problem is to use JavaScript to calculate the height of the `div` based on the viewport height minus the height of the browser UI.

# `svh` and `dvh` to the rescue

We now have more modern viewport height units that take into account the browser UI. These are `svh` and `dvh`.

SVH stands for small viewport height and DVH stands for dynamic viewport height.

# `svh`

`svh` is based on the smallest possible viewport height. It assumes the "worst case" where the browser UI is always visible and takes up space.

`100svh` will take up the full height of the viewport, but won't change if e.g. the address bar is goes hidden.

**Example:** If you set min-height: 100svh on a hero section, it will always be at least the height of the viewport with the browser UI visible. The hero won't get taller if the UI hides.

# `dvh`

`dvh` is based on the dynamic viewport height. It adapts to the viewport height. It doesn't matter if the browser UI is visible or not. So `100dvh` will always take up the full height of the viewport.

**Example:** If you set height: 100dvh on a full-screen overlay, it will always perfectly cover the visible viewport. It will adjust if the browser UI hides.

# Which to use?

My personal take at first was `dvh` is the way to go. But after doing more research, you should be careful with overusing `dvh`. Because it's dynamic, the browser would be forced to recalculate the height of the element every time the viewport changes. This can lead to performance issues. I would only use `dvh` on the first element of the page that needs to be full height to provide a good first user experience.

If you still need `vh` but want to take into account the browser UI, `svh` is the way to go. This can be better for elements other than the first one on the page.

That's my personal opinion. Everything is case by case. So the real answer as usual: It depends.

# Summary

- `vh` is based on the viewport height, including the browser UI.
- `svh` is based on the smallest possible viewport height.
- `dvh` is based on the dynamic viewport height.
