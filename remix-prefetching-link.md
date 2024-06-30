# Options

In Remix you can use the `<Link>` component to prefetch resources.

It would prefetch both the loader data from the route and the JavaScript the route needs.

You have 4 different options:

- `none`: No prefetching
- `intent`: Prefetch on hover or focus.
- `render`: Prefetch when the link is in DOM.
- `viewport`: Prefetch when the link becomes visible in viewport.

`render` and `viewport` aren't the same things. A link may be in the DOM but not visible yet.

Example:

```jsx
<Link to={`/movies/${movie.id}`} prefetch="render">
  {movie.title}
</Link>
```

# Prefetching images

Prefetching links doesn't prefetch images. That's a part of the browser's job when encountering an image.

However, we can still prefetch images. When prefetched, the browser won't need to load the image again after navigation. Instead, it will use the cached version.

If you've ever worked with images, you probably know that there is `onLoad` on the image tag. That's a way in React to keep track of when an image is loaded.

So, to clarify, downloading an image is different from prefetching a link.

How to prefetch images:

```js
function prefetchImage(url) {
  let img = new Image();
  img.src = url;
}
```

Here, we create a new `Image` object. When setting `src`, the browser will start downloading the image asynchronously and then cache it.

When the user navigates to the page, the browser uses the cached image.

You can decide when to prefetch the image.

You can either do it right away or when the user hovers (`onMouseEnter`) or focuses (`onFocus`) on the link.

# What I prefer

It depends.

If most of our users are on mobile, I prefer `render`. That's because there is no way to hover on mobile.

If most of our users are on desktop, then it depends. I do prefer `render` still. But sometimes it's more inefficient than using `intent`.

It depends how many links and how heavy it is to prefetch the data of that route.

---

Edit: In hindsight, the balance of best worlds I think is to use `viewport` for prefetching.
