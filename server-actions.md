# Introduction

Server Actions in Next.js can be confusing to understand.

I already dove into Server Actions in my previous post on RSCs.

My aim here is to dive into the nuances of Server Actions.

# When to use `bind` and why?

The common pattern of using `bind` with server actions in Next.js is to pass additional arguments to the action beyond just the submitted form data.

If you look at this code example:

```jsx
// actions.ts
"use server";

export async function deleteAction(id, formData) {
  // Delete the post with the given id
  await deletePost(id);

  // Perform any other necessary operations using formData
  // ...

  // Revalidate or redirect as needed
  revalidatePath("/blog");
}

// BlogPost.tsx
import { deleteAction } from "./actions";

export default function BlogPost({ post }) {
  const deletePost = deleteAction.bind(null, post.id);

  return (
    <form action={deletePost}>
      <button type="submit">Delete</button>
    </form>
  );
}
```

Here, `bind` is used to create a new function `deletePost` that has the `id` argument pre-bound to it. When the form is submitted, `deletePost` will be invoked as the server action, and Next.js will send the `id` along with the form data to the server.

# Works like a Queue

Let's say you've multiple Server Actions on a page. You're implementing something highly dynamic where users can click around to trigger different actions and you expect them to run concurrently.

```jsx
// FileUpload.jsx
export async function deleteAction(id) {
  "use server";
  // Delete the file with the given id
  // ...
}

export default function FileUpload({ file }) {
  const deleteFile = deleteAction.bind(null, file.id);

  return (
    <div>
      <p>{file.name}</p>
      <Form action={deleteFile}>
        <button type="submit">Delete</button>
      </Form>
    </div>
  );
}

// FileList.jsx
import FileUpload from "./FileUpload";

export default function FileList({ files }) {
  return (
    <div>
      {files.map((file) => (
        <FileUpload key={file.id} file={file} />
      ))}
    </div>
  );
}
```

Here, we've a list of files and each file has a delete button. When the user clicks the delete button, the file should be deleted.

Now, let's say the user tries to delete 3 files at once. What happens?

You'd expect the files to be deleted concurrently. But that's not what happens. Server Actions run sequentially. They work like a queue.

This is one of the current limitations of Server Actions.

# Inline Server Actions

```jsx
export default function Page() {
  async function action(formData) {
    "use server";
    // Server action logic
  }

  return <form action={action}>...</form>;
}
```

I had two confusions with this piece of code:

1. Why would you want to use them?
2. Wait, why do we need "use server" when we're in a server component?

## Inline Actions

You don't have to define inline actions. They can be useful when the action is specific to a single component and not reused elsewhere in the application.

Personally, I don't think I would ever define an inline action. It just looks weird to me.

## "use server" in a server component

"use server" is still necessary in a server component when defining an inline action.

Server actions are different from regular server-side code. They are specifically designed to be invoked from the client-side, usually through form submissions or other user interactions.

How you can see it: "use server" exposes server code to the client.

## "use server" under the hood

### Unique Identifier

Next.js generates a unique identifier for each server action. This identifier is used to associate the client-side invocation with the corresponding server-side function. This way, the server knows which function to run when the client invokes the action.

### API Endpoint

Next.js automatically generates an API endpoint for each server action. These endpoints are created during the compilation process and are not visible in your codebase. The generated endpoints handle the incoming requests from the client and route them to the appropriate server action.

### Client-Side Invocation

When you invoke a server action from the client-side, such as through a form submission or a button click, Next.js sends a POST request to the generated API endpoint.

The request includes a special header called "Next-Action" which contains the unique identifier of the server action.

This header is automatically added by Next.js and is used to map the request to the corresponding server action.

# Always gotta be async

Under the hood, server actions are by nature asynchronous. This is because they are designed to perform server-side operations that may take some time to complete.

Hence you should always use the `async` keyword when defining a server action.

# Error Handling

Error handling is done by returning an error in the `catch` block of a `try-catch` statement.

That's then sent up to your nearest error UI boundary which you define like `error.tsx`.

```jsx
export async function deleteAction(id, formData) {
  try {
    // Delete the post with the given id
    await deletePost(id);

    // Perform any other necessary operations using formData
    // ...

    // Revalidate or redirect as needed
    revalidatePath("/blog");
  } catch (error) {
    return { message: "An error occurred while deleting the post." };
  }
}
```

You can still throw an error. Do it if it's an unexpected error.

Return: Handle expected errors.

Throw: Handle unexpected errors.

# Revalidation

Server Actions integrate with Next.js' caching and revalidation architecture. When an action is invoked, Next.js can return both the updated UI and new data in a single server roundtrip.

There are two main ways to revalidate data after a Server Action:

1. `revalidatePath`: Revalidates data for a specific path. It accepts a relative URL string where it will clear the cache and revalidate the data for that path e.g. `/blog`.
2. `revalidateTag`: Revalidates data associated with a specific cache tag. Next.js has a cache tagging system for invalidating fetch requests across routes.

## revalidatePath

Example: After creating a new todo item via a Server Action, you can revalidate the `/` path to ensure the list of todos is updated.

```jsx
// app/actions.ts
"use server";
import { revalidatePath } from "next/cache";

export async function addTodo(data: FormData) {
  // Save new todo to database
  await createTodo(data);

  // Revalidate the "/" path
  revalidatePath("/");
}
```

If you don't do this, the user will have to refresh the page to see the new todo item.

## revalidateTag

The way this works: You can tag fetch requests with one or more tags

```jsx
const res = await fetch("https://...", {
  next: { tags: ["todos"] },
});
```

Then call `revalidateTag` to revalidate all entries with that tag. This works across routes.

```jsx
// app/actions.ts
"use server";
import { revalidateTag } from "next/cache";

export async function addTodo(data: FormData) {
  // Save new todo to database
  await createTodo(data);

  // Revalidate data tagged with 'todos'
  revalidateTag("todos");
}
```

## fetch and tags

### On the server

The `fetch` call with cache tags is used on the server-side in Next.js, not on the client. It's typically used inside Server Components, Route Handlers, or Server Actions.

In contrast, libraries like React Query are primarily used for client-side data fetching and caching

### Works flawlessly

Next.js' built-in data fetching and caching capabilities, including cache tags, are designed to work seamlessly with the framework's server-rendering architecture.

They provide a way to fine-tune caching and revalidation behavior on the server.

### Practical scenario

Using multiple tags for a single `fetch` call can be useful in scenarios where the fetched data is associated with multiple entities or categories.

It allows for more granular control over cache invalidation.

Let's look at an example where a post may be associated with multiple categories:

```js
// Fetching a blog post associated with multiple categories
const res = await fetch(`https://api.example.com/posts/${postId}`, {
  next: { tags: ["posts", `category:${category1}`, `category:${category2}`] },
});
```

In this case, the blog post is tagged with a general `'posts'` tag and specific category tags like `'category:tech'` and `'category:javascript'`.

This allows for targeted cache invalidation:

- Invalidating the `'posts'` tag will revalidate all blog posts.
- Invalidating a specific category tag like `'category:tech'` will revalidate only posts in that category.
