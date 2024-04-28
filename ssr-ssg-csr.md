# Client Side vs Server Side Rendering vs Static Site Generation

In this post, we will dive into Client Side, Server Side, and Static Site Generation rendering methods. We will discuss the differences between them and when to use each one to achieve the best user experience.

# What is Client Side Rendering?

With Client Side Rendering (CSR), the server sends a minimal HTML document to the client. This document doesn't contain any content, but it includes links to JavaScript files. The browser then downloads these files to properly display the content.

Let's look at an example of how Client Side Rendering works. We have some minimal HTML code that includes a link to a JavaScript file:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Client Side Rendering</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="app.js"></script>
  </body>
</html>
```

When the browser loads this HTML document, it will make a separate request to the server to fetch the `app.js` file. This process is referred to as "downloading" the JavaScript file.

Once the JavaScript file is downloaded, it executes in the browser. This JavaScript code is responsible for fetching the necessary data from the server (usually via API calls) and then rendering the content on the page. This rendering process involves manipulating the DOM (Document Object Model) to display the fetched data.

As you can see, the server doesn't render the content. Instead, the client's browser does all the work.

## Pros and Cons

The main advantage of Client Side Rendering is that it allows for dynamic content updates without reloading the page. This makes the user experience more interactive and engaging.

The several downsides however:

- The initial load time can be slow because the browser needs to both download and execute the JavaScript files.
- Search engines have a hard time indexing the content because they don't execute JavaScript.
- The content is not visible until the JavaScript files are downloaded and executed.
- The page might not be accessible to users with JavaScript disabled.

## When to Use Client Side Rendering

If you do use Client Side Rendering, make sure it's for pages that are:

- Highly interactive and require frequent updates.
- Behind a login wall. Don't need to be indexed by search engines.

It's important to note that both of above requirements should be met to justify the use of Client Side Rendering.

And even then, you should really think through whether the benefits of Client Side Rendering outweigh the downsides.

# What is Server Side Rendering?

Server Side Rendering (SSR) is the process of rendering the content on the server. This means on the server we do all the work of fetching the data, rendering the HTML, and sending the fully rendered HTML to the client that includes the content.

When the browser receives the HTML document, it doesn't need to download any JavaScript files to display the content. The content is already there, ready to be shown to the user.

If further interactivity is required, the server may send additional JavaScript files to the client. Just like we spoke about in CSR, these files will need to be downloaded and executed by the browser when needed.

This is also where the term "hydration" comes into play. I'll cover that in a future blog post.

## Pros and Cons

SSR has many upsides:

- The initial load time is faster because the browser doesn't need to download and execute JavaScript files.
- Fetching data on the server from another server or database is faster than doing it on the client.
- Search engines can easily index the content because it's already in the HTML document.
- The content is visible to the user immediately.
- The page is accessible to users with JavaScript disabled.
- It's easier to implement server-side caching for performance improvements. For example, using a CDN (Content Delivery Network) to cache the HTML content.
- It's more secure because sensitive data can be kept on the server.

Downsides to SSR:

- The server needs to do more work to render the content.
- The user experience might not be as interactive as with Client Side Rendering. However, with modern fullstack frameworks, this won't be a problem.

# What is Static Site Generation?

Static Site Generation (SSG) is the process of generating static HTML files at build time. "Build time" is before the user even requests the page. This happens before deployment when the developer builds the application.

When the user requests a page, the server simply serves the pre-generated HTML file. There's no need to render the content on the server. However, modern frameworks still include JavaScript for interactivity and additional functionality if needed.

This method is great for websites that don't need to be updated frequently. For example, a blog or a portfolio website.

It's easy to cache the HTML files for even faster load times.

This is also good for SEO because search engines can easily index the content since it's already in the HTML files.

However, if you need the content to be dynamic, this isn't the right approach. It won't work for pages that require frequent updates or user interactions.

The main problem here is that the content is completely static. The HTML files are generated before deployment, and they don't change until the next build.

That's why SSG is good for public pages that don't change often.

# Incremental Static Site Generation

Incremental Static Regeneration (ISR) is a powerful feature in Next.js that allows you to update static pages without rebuilding the entire site. It combines the benefits of Static Site Generation (SSG) and Server-Side Rendering (SSR) to provide fast performance and fresh content.

## How ISR works in Next.js

### 1. Initial Build

When a request is made to a page that was pre-rendered at build time, it will initially show the cached page. Any requests to the page within the revalidation time window (e.g. 10 seconds) are also cached and served instantly.

### 2. Background Regeneration

After the revalidation time window, the next request will still show the cached (stale) page, but Next.js triggers a regeneration of the page in the background. Once the page generates successfully, Next.js invalidates the cache and shows the updated page.

It's important to note that the regeneration process only happens if a request is made to the page after the revalidation time window. If no requests are made, the page won't be regenerated.

This is not like a "scheduled job" that regenerates the page every X minutes. Instead, it's triggered by user requests.

### 3. Path not generated yet

If a request is made to a path that hasn't been generated yet, Next.js will server-render the page on the first request. Future requests will then serve the static file from the cache.

### 4. Manual Revalidation

Next.js also supports On-Demand Revalidation, which allows manually purging the cache for a specific page using the `revalidate()` function, triggering an immediate regeneration.

## When to use ISR

Incremental Static Regeneration is a great choice for pages that need to be updated frequently but don't require real-time data. For example, a blog post that gets updated every few minutes or an e-commerce product page that changes every hour.
