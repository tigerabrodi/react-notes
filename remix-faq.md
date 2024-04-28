# Introduction

This post is for my younger self.

A post that would've helped me get up to speed with Remix faster.

# What is Remix?

Remix is a web framework built on top of the Web Fetch API, allowing for deployment on multiple platforms. It acts as a centralized bridge between the server and client, simplifying data fetching and UI rendering.

If Remix was e.g. built on top of node, it'd rely on node's specifics rather than being agnostic.

Unlike traditional web frameworks, Remix is not a server itself but a handler. This means it processes requests and generates responses without deciding how your application is hosted or deployed.

You own where to deploy your app, and Remix will work with it.

You create a request handler based on the adapter you're using, e.g. if deploying to Vercel, you'd use the Vercel adapter.

# What is nested routing and why should I care?

Nested routing is a powerful feature in Remix that allows you to break down your page into components that are tied to specific routes.

First, you need to understand the drawbacks of traditional routing to see why nested routing is beneficial.

## Traditional routing

In traditional routing, every route is a full page. The URL corresponds to a whole page. So, when you land on a page, you may have multiple components on the page. Inside each component, you will typically make a request to the server to fetch data.

Or you might make the requests in a parent component and pass the data down to the child components.

Regardless, you're gonna have to make several sequential requests to the server to render a page.

This leads to "waterfall" requests, where the server waits for the first request to finish before starting the second request.

An example would be a dashboard page with a list of items. You can click on an item to view its details. And inside the detailed view itself, you would make another request to fetch the item's details.

This would be 3-4 requests done sequentially.

## Nested routing

Remix's [website](https://remix.run/) explains nested routing perfectly.

But to summarize:

Components of the page are tied to specific URL segments (e.g. `/dashboard/items`). `items` is a route segment that's nested inside `/dashboard`.

So `/dashboard/items` is a full URL containing both the `/dashboard` and `items` segments. Each segment is a component that's rendered on the page. `items` is nested inside `/dashboard`. `/dashboard` is like a wrapper component that contains `items`.

With a special touch: **Because Remix loaders run in parallel, you can fetch data from `items` and `/dashboard` in parallel.** This is better than waiting for `/dashboard` to finish before fetching `items`.

As a result, no waterfall requests!

# What's the difference between useSubmit and useFetcher?

"I wanna make a request programmatically, should I use useSubmit or useFetcher?"

Let's dive into it!

## The criteria

To decide whether to go with `useSubmit` or `useFetcher`, you need to ask yourself: Will the action cause a navigation?

By a navigation, I mean the URL changes after the action is performed.

For example, this is typically the case when you fill out a form to create a new item.

If you want the URL to change after the action is performed, you want a submission. Otherwise, you want to make a request from the client to the server without causing a navigation.

## useSubmit

`useSubmit` is used for submitting forms that cause a navigation, i.e. the URL changes after the form is submitted. It is usually used with the `<Form>` component.

If you're okay with the URL changing after the action is performed, use `useSubmit`. Even if you're coming back to the same page, it's still considered a navigation when doing a submission.

When you submit a form with `useSubmit`, Remix will call the action handler defined for that route. After the action completes, Remix will navigate to the URL specified by the redirect in the action response, or if no redirect is specified, it will navigate back to the same URL.

The navigation will trigger the loader function of the route to run again. So, even if you're staying on the same page, under the hood, a full navigation cycle is happening.

```js
const submit = useSubmit();

submit(targetOrData, options);
```

## useFetcher

If you wanna make a request to the server without causing a navigation, use `useFetcher`.

With a fetcher, we stay on the client and simply make a request to the server. The URL doesn't change.

From my experience, it's quite useful when building something highly dynamic.

- Deleting an item from a list without navigating away
- Adding a new item to a list and seeing it appear without a full page refresh
- Loading data for a popover or combobox without changing the user's context
- Performing background actions

`useFetcher` doesn't return a function like `useSubmit`. It returns an object with submit, load, Form(a component), data, state and other properties.

If you need to look up e.g. the state of a fetcher that's in a different route, you can use the `key` option to identify the fetcher.

```js
const fetcher = useFetcher({ key: "add-to-bag" });
```

- `Form` is like the `<Form>` component but doesn't cause a navigation.
- `load` used to make a GET request to one of the routes.
- `submir` used to make a POST request to one of the routes. A submission without a navigation.

# Wtf file uploads lol

File uploads can be hard to understand in Remix. Their [documentation](https://remix.run/docs/en/main/utils/unstable-create-file-upload-handler) on it is quite dry.

Before we dive into some code, let's understand the difference between disk and memory on your computer.

## Disk vs Memory

Memory (RAM) is used for short-term data storage and quick access by the CPU while a program is running. Disk (HDD or SSD) is used for long-term, permanent storage of data, files, and programs, even when the computer is turned off.

RAM is much faster than disk storage. Accessing data from RAM takes nanoseconds, while accessing data from disk takes milliseconds. RAM can be 100,000 times faster than disk for random access operations.

RAM is volatile, meaning its contents are lost when the computer is powered off. Disk storage is non-volatile and retains data even without power.

RAM capacity is typically smaller, ranging from a few gigabytes to tens of gigabytes in modern computers. **Disk storage capacity is much larger, ranging from hundreds of gigabytes to several terabytes.**

## FormData needs to be stored

During a submission, we need to store the form data somewhere temporarily. Otherwise, we'd lose it when the server processes the request.

We typically wanna parse, validate and potentially transform data from the form before storing it.

That's where the disk and memory come in.

Usually, we store the form data in memory. But if the form data could be big such as a file, we store it on disk.

## Multipart vs Regular form

When you submit a regular form without file uploads, the form data is sent as a URL-encoded string (`application/x-www-form-urlencoded`) in the request body. It may look something like `name=John+Doe&email=john%40example.com&message=Hello%2C+World%21`.

As you can see, this format is not suitable for file uploads. We will run into issues:

1. Files can contain binary data, which may include characters that have special meaning in the URL-encoded format, leading to corruption or misinterpretation of the data.
2. Files can be large in size, and URL-encoding them would significantly increase the size of the request body, making it inefficient to transmit over the network.

To overcome these limitations, the `multipart/form-data` encoding was introduced. In this encoding:

1. The form data is divided into multiple parts, each representing a form field or a file.
2. Each part is separated by a unique boundary delimiter, which is a string that acts as a marker to identify the beginning and end of each part. The boundary is randomly generated and specified in the `Content-Type` header of the request.
3. Each part includes its own headers, such as `Content-Disposition`, which specifies the name of the form field or the filename of the uploaded file, and `Content-Type`, which indicates the type of data in that part.
4. The actual data of each part, whether it's a text field value or the binary content of a file, is placed after the headers, separated by a blank line.

An example of how a multipart form data request might look like:

```
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=---------------------------123456789012345

---------------------------123456789012345
Content-Disposition: form-data; name="name"

John Doe
---------------------------123456789012345
Content-Disposition: form-data; name="email"

john@example.com
---------------------------123456789012345
Content-Disposition: form-data; name="file"; filename="example.txt"
Content-Type: text/plain

{This is where the content of the file would go.}
---------------------------123456789012345--
```

- The boundary delimiter is ---------------------------123456789012345.
- There are three parts: two text fields (name and email) and one file (file).
- Each part has its own headers (Content-Disposition and Content-Type) and data.
- The file part includes the filename and the content of the uploaded file.

### Wait, are multiple requests sent?

...No.

When the form is submitted, the browser creates a special HTTP request called a multipart request. This request contains the various form fields and files to be sent to the server.

So the entire multipart request is sent as a single HTTP request, but with the body divided into multiple sections by the boundary delimiters. The server then needs to parse this multipart data, using the boundary to split it into the original form fields and files.

Using multipart encoding allows the form data and files to be transmitted efficiently.

Another alternative would be to use Base64 encoding. A way to encode binary data (like files) into a text format that can be safely transmitted over the network. But that would increase the size of the data by about 33%.

## Code

Let's dive into some code to understand how it all works.

```js
import {
  unstable_composeUploadHandlers,
  unstable_createFileUploadHandler,
  unstable_createMemoryUploadHandler,
} from "@remix-run/node";

export async function action({ request }: ActionArgs) {
  const uploadHandler = unstable_composeUploadHandlers(
    unstable_createFileUploadHandler({
      directory: "public/uploads",
      file: ({ filename }) => filename,
    }),
    unstable_createMemoryUploadHandler()
  );

  const formData = await unstable_parseMultipartFormData(request, uploadHandler);
  const file = formData.get("file") as File;
  const title = formData.get("title") as string;

  // Process the uploaded file and form data
  // ...

  return json({ message: "File uploaded successfully!" });
}
```

An upload handler is a function that processes the individual parts of a multipart form submission, such as form fields and files.

Remix provides two built-in upload handlers: `unstable_createFileUploadHandler` and `unstable_createMemoryUploadHandler`. The first will store the file on disk, while the second will store form data in memory.

`directory` specifies the directory where the uploaded files will be stored. `file` is a function that returns the filename of the uploaded file. If we wanted to, we could modify the file name before storing it. Note: **This is the filename on the disk, and not related to the form field name.**

`unstable_composeUploadHandlers` is a utility function provided by Remix that allows you to combine multiple upload handlers into a single handler.

`unstable_parseMultipartFormData` is a function provided by Remix that parses the multipart form data from the incoming request.

As mentioned above, the form data is a multipart request, and the server needs to parse it to extract the form fields and files.

After that, you can simply access the form fields and files from the parsed form data using the `get` method.

# Remix SPA vs SSR: What's the difference? Which one should I use?

Remix offers two modes: SPA and SSR.

You need to pick one and go all in. You can't have both in the same app.

SPA is like React Router and Vite with Remix goodies.

SSR is the traditional Remix. Starting out on the server, then hydrating on the client.

You can dive deeper into this if you wish, but I'll get straight to the point: Always choose SSR mode unless you're migrating from React Router and keeping it all in SPA mode.

## Why SSR mode?

- **SEO improved:** Search engines can crawl your site more effectively because the content is rendered on the server.
- **Performance is better:** You can fetch data on the server before rendering the page. This reduces the time to first byte (TTFB) and improves the perceived performance of your app. Eliminates the need for additional requests on the client.
- **Accessibility:** SSR allows users with slower devices or limited internet connectivity to access content more quickly.

# The power of useFetchers

You remember how we spoke about the `useFetcher` hook earlier?

`useFetchers` is a hook that let's you manage and interact with multiple concurrent data fetching operations.

With `useFetchers`, you can create multiple data fetching operations simultaneously.

Each data fetch is associated with a unique key, allowing you to differentiate between different fetchers. The fetchers run in parallel, meaning they don't block each other or the main UI thread.

Each fetcher has its own state, allowing you to update the UI accordingly based on the status of each fetcher (e.g., loading, success, error). This level of control allows you to create more complex and dynamic user interfaces that respond to multiple data sources.

Let's look at some code:

```jsx
import { useFetchers } from "@remix-run/react";

export default function MyComponent() {
  const fetchers = useFetchers();

  const handleFetchUser = () => {
    fetchers.submit({ action: "/api/user" }, { key: "user", method: "post" });
  };

  const handleFetchPosts = () => {
    fetchers.submit({ action: "/api/posts" }, { key: "posts", method: "post" });
  };

  const userFetcher = fetchers.find((fetcher) => fetcher.key === "user");
  const postsFetcher = fetchers.find((fetcher) => fetcher.key === "posts");

  return (
    <div>
      <button onClick={handleFetchUser}>Fetch User</button>
      <button onClick={handleFetchPosts}>Fetch Posts</button>

      {userFetcher?.state === "loading" ? (
        <p>Loading user...</p>
      ) : userFetcher?.data ? (
        <p>User: {userFetcher.data.name}</p>
      ) : null}

      {postsFetcher?.state === "loading" ? (
        <p>Loading posts...</p>
      ) : postsFetcher?.data ? (
        <ul>
          {postsFetcher.data.map((post) => (
            <li key={post.id}>{post.title}</li>
          ))}
        </ul>
      ) : null}
    </div>
  );
}
```

In this example:

- The `useFetchers` hook is used to access the fetchers object.
- Two buttons are rendered: "Fetch User" and "Fetch Posts".
- When the "Fetch User" button is clicked, the `handleFetchUser` function is called, which submits a fetcher with the key "user" and the action "/api/user".
- Similarly, when the "Fetch Posts" button is clicked, the `handleFetchPosts` function is called, which submits a fetcher with the key "posts" and the action "/api/posts".
- The `userFetcher` and `postsFetcher` variables are obtained by finding the fetchers with the corresponding keys using `fetchers.find()`.

`useFetchers` is so powerful.

I love it a ton personally haha.

## Comparing to Next.js Server Actions

Even with the new App Router, RSCs and Next.js 14, Server Actions run sequentially and not in parallel.

This means that if you have multiple Server Actions on a page, they will run one after the other, leading to a bad user experience. Especially on highly dynamic pages where you want the user to be able to click around and see data load in real-time.
