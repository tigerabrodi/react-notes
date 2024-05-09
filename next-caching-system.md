# Introduction

Let's start by understanding the caching system in Next.js and then dive into "no-store" and "force-dynamic".

# Next.js Caching System and RSC

By default, Next.js uses a caching mechanism to improve performance. When a page is requested, Next.js checks if it has a cached version. If found, it serves the cached page, otherwise it generates the page on the server and caches it for future requests.

With the introduction of RSC, components can be rendered on the server. RSC allows you to fetch data on the server and pass it as props to your components. However, this can lead to caching issues if not handled properly

# 'force-dynamic' Option

The `force-dynamic` option is used to force a page to be rendered dynamically on each request, similar to `getServerSideProps` in the `pages` directory.

To use `force-dynamic`, you can export it from your page:

```js
export const dynamic = "force-dynamic";
```

When 'force-dynamic' is set, Next.js will skip caching and always render the page on the server for each request.

However, it's not the preferred option anymore. The main issue with 'force-dynamic' is that it disables caching for the entire page, and not specific parts of the page.

# No-Store cache option

The `no-store` cache option is used to prevent caching of data fetched by RSC. When you make a fetch request with the `no-store` option, Next.js will not cache the response.

Here's an example of using `no-store`:

```js
const res = await fetch("https://api.example.com/data", { cache: "no-store" });
```

By setting `cache: 'no-store'`, you ensure that the data is always fetched fresh from the server and not served from cache.

This is useful when you know never caching the data is the right choice, such as when the data is very dynamic.

# What if I need to cache a component of the page?

This is where `unstable_noStore` comes into play. You can use `unstable_noStore` to prevent caching of specific parts of the page.

You can use it like this:

```js
import { unstable_noStore as noStore } from 'next/cache';

export default async function Component() {
  noStore();
  const result = await db.query(...);
  // ...
}
```

Any data fetched after `noStore()` will not be cached by Next.js.

This is more granular than `force-dynamic` and recommended to use when you need to prevent caching of specific parts of the page.

If you need the caching to be even more granular, then use the `cache: "no-store"` option in the fetch request.
