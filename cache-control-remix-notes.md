# Introduction

I had some questions around Cache Control and using them in Remix.

This is kind of just me answering my own questions lol.

Before going over my questions, let's look at two examples and some of the values.

Cache-Control without stale-while-revalidate:

```jsx
export let loader: LoaderFunction = async () => {
  const posts = await db.post.findMany();

  return json(posts, {
    headers: {
      "Cache-Control": "public, max-age=300, s-maxage=3600",
    },
  });
};
```

Cache-Control with stale-while-revalidate:

```jsx
export let loader: LoaderFunction = async () => {
  const products = await db.product.findMany();

  return json(products, {
    headers: {
      "Cache-Control": "public, s-maxage=60, stale-while-revalidate=3600",
    },
  });
};
```

- `public`: The response can be cached by the browser or any CDNs. It's made public.
- `max-age`: The amount of seconds the response is considered fresh by the browser.
- `s-maxage`: The amount of seconds the response is considered fresh by the CDN. This overrides `max-age`.
- `stale-while-revalidate`: The amount of seconds the response is considered OK to serve stale (cached) data while the CDN revalidates the data.

# Is public required?

No, it's not required. If you don't set it, the response will be private by default. This means the response can only be cached by the browser and no shared caches such as CDNs.

# Do we need both max-age and s-maxage?

No, you don't need both.

If you only set `max-age`, it will be the same time for both browser and CDN caches.

If you only set `s-maxage`, this will only apply for shared caches like CDNs. The browser will use the default `max-age` value.

If you don't need different cache times for both browser and CDN, `max-age` is enough.

# When do I want max-age and s-maxage to be different though?

Let's go over two examples to understand when you may want `max-age` and `s-maxage` to be different.

## Frequently updated content with high traffic

Let's say you have a blog post that is frequently updated. By that, we can assume multiple times a day.

We can set `max-age` to 5 minutes and `s-maxage` to 1 hour. The browser will be ok with serving cached data for 5 minutes. After that, it will go to the CDN to check if the data is still fresh. We do this to avoid overloading the origin server.

The CDN is responsible for giving the browser the content. And every hour, it will check the origin server to get the latest data. This means the browser will have the latest data within 5 minutes after the CDN has checked the origin.

## Personalized content

If you're serving personalized content to the user e.g. a personalized news feed page, you don't want this to be cached by the CDN since it shouldn't be shared.

So you can set `max-age` to 1 hour and `s-maxage` to 0. This means the browser will check the origin server every hour. And the CDN will not cache the response.

# Why when giving examples do people choose max-age as smaller value than s-maxage, is that intentional?

Yes, the purpose here is to make sure the browser has the latest data. We'll do some more aggressive caching for the CDN. CDNs can cache for longer and serve more traffic. Compared to browser cache, CDNs do not clear after session closes.

# Stale while revalidate

Quite a straightforward one: `"Cache-Control": "public, s-maxage=60, stale-while-revalidate=3600"`.

We of course need to make the response public because we want the CDN to cache the response.

The CDN will cache it for 60 seconds. For 60 seconds, serving the stale (cached) data is fine. Then in the background, the CDN will get the latest data from the origin server. However, during that time, it is still allowed to serve stale data up to 3600 seconds which is 1 hour.

Honestly, in most cases, the background revalidation will happen faster than an hour. But it's good to have a buffer.

This is one of my favorite caching strategies. It's the default strategy for React Query, which I'm a big fan of because it provides a good experience. However, in the case of React Query, `s-maxage` is 0. Meaning, in most cases it will serve a very fresh response, but in some cases it will be stale due to background revalidation still happening.
