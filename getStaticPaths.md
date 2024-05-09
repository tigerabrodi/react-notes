# Introduction

I never understood getStaticPaths in Next.js, so I decided to write this blog post as a way to understand it better.

# getStaticProps

I assume you already know about getStaticProps. Let's recap it quickly:

- getStaticProps is a function that runs at build time in Next.js.
- It's used to statically generate pages.
- This mean when we run `next build`, Next.js will call getStaticProps for each page and generate the HTML files.
- This way, when a user visits the page, the content is already there, and the page loads faster.
- This is great for SEO because search engines can crawl the page and index the content.

Do you already spot the problem?

# The problem for dynamic pages

Let's say we have a page like `/posts/:id`. This page is dynamic because it can have multiple IDs. For example, `/posts/1`, `/posts/2`, `/posts/3`, etc.

If we were to server-side render this page, we could look at the ID during the time of the request and fetch the data for that ID.

However, with static generation, we can't do that because we don't know all the possible IDs at build time.

The solution?

We pre-generate all the possible pages at build time.

This is where getStaticPaths comes in.

It's a way for us to tell Next.js all the possible paths that we want to pre-generate.

# getStaticPaths

When you export `getStaticPaths` from a page, Next.js will pre-render all the `paths` that you return.

Let's look at an example:

```ts
export async function getStaticPaths() {
  return {
    paths: [{ params: { id: "1" } }, { params: { id: "2" } }],
    fallback: false,
  };
}
```

Here, we tell Next.js to pre-render `/posts/1` and `/posts/2`.

If a user requests `/posts/3`, Next.js will return a 404 because we didn't pre-render that page.

However, if a user requests `/posts/1`, Next.js will return the pre-rendered HTML file.

# Wait, what if the user requests `/posts/3`?

This is where the `fallback` option comes in.

We have three options for `fallback`:

- `false`
- `true`
- `blocking`

If the user requests `/posts/3` and we didn't pre-render it, Next.js will do different things based on the `fallback` option.

## Fallback set to `false`

Return a 404 page. Only the paths that we pre-rendered will work. Use when to ensure all paths are pre-rendered.

## Fallback set to `true`

Render the page on the server (on demand). This only happens the first time the page is requested. On following requests to the same page, Next.js will return the pre-rendered HTML file. Use when have many static pages and only want to pre-render some of them. This is useful for blogs, e-commerce sites, etc. that have a lot of data. Handle the loading state by checking `router.isFallback`.

## Fallback set to `blocking`

If you don't want to show a loading state like in the option `true`, you can use `blocking`. This is the same, but the user won't see a loading state. Instead, they will have to wait a bit longer for the page to load. Use when you don't want to show a loading state e.g. if you know the page will load super quickly.

# Recap

- `getStaticPaths` is used to pre-render dynamic pages.
- It tells Next.js all the possible paths that we want to pre-generate.
- We can use `fallback` to handle cases where the page wasn't pre-rendered.
