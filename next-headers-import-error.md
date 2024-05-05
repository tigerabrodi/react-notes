# Introduction

I had this error at work when working with Next.js `You're importing a component that needs next/headers. That only works in a Server Component which is not supported in the pages/ directory.`.

We're using Next.js 14.1 with App router. I was trying to import a Zod schema from one file to another.

Let's dive into the error and why it happens.

# Why it happens?

The error message says that you're importing a component that needs `next/headers`. It's not really clear what it means. Because I was importing a Zod schema, not a component.

However, a better wording would be `You're importing from a file that needs next/headers`.

The file uses a function called `privateFetch` which is our abstraction around fetch for making authenticated API requests. This function is defined in a file that uses `next/headers`.

# But, we're just importing a Zod schema?

Yes, that's true. We're just importing a Zod schema. But this is related to how Next.js works.

Or more specifically, how React Server Components work.

# Different bundles

As you know, if you don't add "use client" directive at the top of your file, it's a server component.

When Next.js bundles your application, it separates the server components from the client components. It creates two different bundles. One for the server and one for the client.

The client bundle contains the client-side code, including Client Components and the necessary JavaScript to make the UI interactive. The client bundle is optimized and sent to the browser. The server one is not sent to the browser.

## Bundling process

During the build process (next build), Next.js analyzes the application code and separates Server Components from Client Components.

Server Components are compiled and bundled to run on the server. They are not included in the client bundle, reducing its size.

Client Components, marked with the "use client" directive, are bundled along with their dependencies into one or more client bundles that will be sent to the browser. This means anything imported into a client component will be included in the client bundle.

# Solution to our problem

The solution to our problem is to extract the Zod schema into a separate file. We can then import it into both the server and client components.

The problem we had was that the Zod schema was defined in a file that used `next/headers`. This caused the error because the file it's located in is bundled as a server component.

# What if something is imported into both server and client components?

Now, the Zod schema is imported into both server and client components. How does this affect the bundling?

It's quite simple: The Zod schema will be included in both the server and client bundles. This is because it's imported into both types of components.

There is no "mixing" of server and client code. The server bundle is separate from the client bundle. The Zod schema will be included in both bundles.
