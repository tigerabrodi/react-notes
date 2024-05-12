# Introduction

I had a bit of a tough time understanding `gcTime` in TanStack Query. So I decided to write this post for myself.

# Every query is cached

When you fetch data using Tanstack Query, the data is cached so that following requests can be served from the cache. This is done to avoid unnecessary network requests.

As long as a query is being actively used, the cached data will be kept in memory.

What about inactive queries? If we kept all the data in memory, it would be a waste of resources. Why should we cache data that is not being used?

# Garbage collection

Garbage collection is the process of cleaning up unused data. In Tanstack Query, this is done by the `gcTime` option.

The "thing" to be cleaned up is the cache for a query that's inactive. The `gcTime` option tells Tanstack Query how long to keep the data in the cache before it's cleaned up.

By default, inactive queries are garbage collected after 5 minutes. This means that if a query is not being used for 5 minutes, the cache for that query will be cleaned up.

# Configuring `gcTime`

You can configure the `gcTime` option by passing it to the `QueryClient` constructor.

```js
const ONE_SECOND = 1000;
const ONE_MINUTE = ONE_SECOND * 60;
const TEN_MINUTES = ONE_MINUTE * 10;

// Set gcTime to 10 minutes globally
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      gcTime: TEN_MINUTES,
    },
  },
});
```

# Conclusion

The purpose of `gcTime` is to strike a balance between caching data for future use and not consumingtoo much memory.
