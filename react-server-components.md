# Introduction

In this post, you will learn about React Server Components.

I did a lot of research and this will be the write-up I wish I had.

My aim is that you only need to read this post to understand and start using React Server Components.

I was thinking a lot about how to write this. I think it would be good to start from scratch at a high level and focus on shifting our mental model.

After that, we can dive into the details and misconceptions.

# New Mental Model

Throw away everything you learned about React except for JSX. Think of React as a templating language. You have JSX as the syntax and by default, everything is server-first.

# Code on the server

Since it's all on the server, you can make server requests, access the file system, and do anything you can do on the server.

```jsx
function HomePage() {
  const images = await getImagesFromDatabase();

  return (
    <div>
      <h1>Welcome to My App</h1>
      <p>This is all rendered on the server!</p>
      {images.map((image) => (
        <Image key={image.id} src={image.url} alt={image.alt} />
      ))}
    </div>
  );
}
```

Here, `getImagesFromDatabase` is a function that fetches images from a database. You can do this because the code is running on the server.

As I've said, forget about React. Focus on this being a "new" server-side framework where we do SSR and use JSX.

# No Client-Side React

It's important to empashize that there is no client-side React. Remove it from your mind.

```jsx
function HomePage() {
  const images = await getImagesFromDatabase();

  // This is not possible.
  // We're on the server.
  const [state, setState] = useState(0);

  return (
    <div>
      <h1>Welcome to My App</h1>
      <p>This is all rendered on the server!</p>
      {images.map((image) => (
        <Image key={image.id} src={image.url} alt={image.alt} />
      ))}
    </div>
  );
}
```

We can't use hooks like `useState` because we're on the server. Interactive components are not possible. Because the server sends HTML to the client. On the client is where the interactivity happens.

# But, I want interactivity!

As powerful as server-side rendering is, we still need interactivity on the client side. After all, what good is a web application if users can't interact with it? This is where the "use client" directive comes into play.

By adding "use client" at the top of a component file, we tell React that this component needs to be hydrated on the client. It's like giving the component superpowers to handle user events and manage state.

```jsx
// HomePage.js
import { getImagesFromDatabase } from './database';
import InteractiveButton from './InteractiveButton';

function HomePage() {
  const images = await getImagesFromDatabase();

  return (
    <div>
      <h1>Welcome to My App</h1>
      <p>This is all rendered on the server!</p>
      {images.map((image) => (
        <Image key={image.id} src={image.url} alt={image.alt} />
      ))}
      <InteractiveButton />
    </div>
  );
}

// InteractiveButton.js
'use client';

function InteractiveButton() {
  const handleClick = () => {
    console.log('Button clicked!');
  };

  return <button onClick={handleClick}>Click Me</button>;
}
```

There is much we can unpack here already. But to not confuse you, simply think of "use client" as a way to add interactivity to your components.

So in `HomePage.js`, we do everything on the server (server-first, default is on the server). But we are allowed to include client-side components in our JSX.

You can think of it like this:

1. Everything is server-first.
2. If React sees a component with "use client", it marks it as a client-side component.
3. Send the HTML to the client.
4. On the client, React sees the "marked" components and fuels them with interactivity (hydration).

# That works! Are we done?

Alright, we got it all working, or?

What if we need to make a request on the client to the server? Should we use fetch?

It would feel weird to use fetch on the client because where should we make the request to? We're kind of mixing server and client code.

Well, that's where Server Actions come into the picture.

They bridge the gap beautifully.

# Bridging the gap with Server Actions

By marking a function with "use server", we expose it as a server action. These functions run on the server but can be seamlessly called from client components. It's like having a secret tunnel between the client and the server.

Let's say on the client, we have some form data. We want to store a new user in the database.

Note: Forget about the `InteractiveButton` component, that was just to show you how to use "use client".

```jsx
// HomePage.js
function HomePage() {
  const images = await getImagesFromDatabase();

  return (
    <div>
      <h1>Welcome to My App</h1>
      <p>This is all rendered on the server!</p>
      {images.map((image) => (
	<Image key={image.id} src={image.url} alt={image.alt} />
      ))}
      <FormComponent />
    </div>
  );
}

// FormComponent.js
"use client"

function FormComponent() {
  const handleSubmit = async (event) => {
    event.preventDefault();
    const formData = new FormData(event.target);
    await createUser(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" name="name" />
      <input type="email" name="email" />
      <button type="submit">Create user</button>
    </form>
  );
}
```

Do you see the missing piece? We need to define `createUser` somewhere.

```jsx
// serverActions.js
"use server";

import { createUserInDatabase } from "./database";

export async function createUser(formData) {
  const user = await createUserInDatabase(formData);
  return user;
}

// FormComponent.js
("use client");

import { createUser } from "./serverActions";

// ...
```

`FormComponent` remains the same. I've just added the import of `createUser` from `serverActions`.

`createUser` is a server action. It's a function that runs on the server. It's exposed to the client. Meaning, the client can call this function and run server code. This is possible thanks to concurrent React (introduced in React 18).

Another good practice to follow is to keep server actions separate from the other server code.

As you can see:

1. We have a new file `serverActions.js` where we define server actions.
2. We don't expose `createUserInDatabase` but rather try to keep a good separation of concerns.

This is also for security reasons explained in the next sections.

## You don't always need "use server"

"use server" is used to define server actions. As mentioned in the beginning, by default, everything is server-first.

If you need to "expose" server functions to the client, you can use "use server".

## Why not expose all server code?

It's a good question. Wouldn't it be more convenient to expose all server code to the client?

Well, this would be a security risk. Because now the client can call any server function. We want to be explicit about what functions can be called from the client.

Remember, the secret tunnel from client to the server gives the client access to the server code. For example, modifying the database.

# I wanna show a loading state

It's possible to keep track of the loading state of a server action with the `useTransition` hook. This is done in the client component.

Reminder: Thanks to concurrent React, this is all possible.

```jsx
"use client";

import { createUser } from "./serverActions";
import { useTransition } from "react";

function FormComponent() {
  const [isPending, startTransition] = useTransition();

  const handleSubmit = (event) => {
    event.preventDefault();
    const formData = new FormData(event.target);
    startTransition(async () => {
      await createUser(formData);
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" name="name" />
      <input type="email" name="email" />
      <button type="submit">
        {isPending ? "Creating user..." : "Create user"}
      </button>
    </form>
  );
}
```

Here, we use `useTransition` to keep track of the loading state of the server action. When the form is submitted, we call `startTransition` and inside it, we call `createUser`.

## useTransition explained

The `useTransition` hook is a part of React's concurrent rendering feature. It allows you to mark certain state updates as "transitions" and gives you control over their priority.

A "transition" is a non-urgent state update. It's like saying, "Hey React, this state update is not urgent. If anything else is happening, you can finish that first."

`useTransition` as you saw, returns an array with two values: `isPending` and `startTransition`.

### isPending

The `isPending` flag is a boolean value that indicates whether there is a pending transition or not. When you call `startTransition` and pass a callback function that updates the state, React marks that state update as a transition.

During the execution of the `startTransition` callback, React sets the `isPending` flag to true. This flag remains true until the transition is completed.

When `isPending` is true, it means that React is currently processing a transition state update in the background, and the UI may not yet reflect the updated state. You can use this flag to show loading indicators or to disable certain UI interactions while the transition is pending.

### startTransition

The `startTransition` function is used to mark a state update as a transition. When you call `startTransition`, you pass a callback function as an argument. Inside this callback, you can perform state updates that you want to be treated as transitions.

Under the hood, when you call `startTransition`, React does the following:

1. React immediately executes the callback function you provided.
2. Any state updates that happen within the callback function are marked as transitions.
3. React assigns a lower priority to these transition state updates compared to other urgent updates like user input or animations (default state updates are urgent).
4. If there are any urgent updates pending, React will interrupt the transition and prioritize the urgent update first.
5. Once the urgent updates are processed, React resumes the transition and applies the state updates marked as transitions.

By marking state updates as transitions, you tell React that they are not critical for immediate user interaction and can be processed in the background without blocking the UI.

### Transitions and Server Actions

Transitions are particularly useful when dealing with server actions or asynchronous operations that may take some time to complete. Because you don't want to block the UI while waiting for the server response.

By wrapping server actions with `startTransition`, you can ensure that the UI remains responsive and doesn't get blocked while waiting for the server response.

### Transitions and Concurrent Rendering

Transitions are a key concept in React's concurrent rendering model. Concurrent rendering allows React to work on multiple state updates concurrently, rather than processing them sequentially (synchronously).

With concurrent rendering, React can start rendering a component tree, pause the rendering if a higher-priority update comes in (like user input), and then resume the rendering later.

Transitions have a lower priority compared to urgent updates, so React can pause the rendering of transitions if more critical updates need to be processed first

Urgent updates are the ones not wrapped in `startTransition`.

# All the code

Let's look at all the code and recap the journey we went on:

```jsx
// HomePage.js

import { getImagesFromDatabase } from "./database";
import FormComponent from "./FormComponent";

function HomePage() {
  const images = await getImagesFromDatabase();

  return (
    <div>
      <h1>Welcome to My App</h1>
      <p>This is all rendered on the server!</p>
      {images.map((image) => (
	<Image key={image.id} src={image.url} alt={image.alt} />
      ))}
      <FormComponent />
    </div>
  );
}

// FormComponent.js
"use client";

import { createUser } from "./serverActions";
import { useTransition } from "react";

function FormComponent() {
  const [isPending, startTransition] = useTransition();

  const handleSubmit = (event) => {
    event.preventDefault();
    const formData = new FormData(event.target);
    startTransition(async () => {
      await createUser(formData);
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" name="name" />
      <input type="email" name="email" />
      <button type="submit">
        {isPending ? "Creating user..." : "Create user"}
      </button>
    </form>
  );
}

// serverActions.js
"use server";

import { createUserInDatabase } from "./database";

export async function createUser(formData) {
  const user = await createUserInDatabase(formData);
  return user;
}
```

To summarize:

1. Everything is server-first.
2. Server code is not exposed to the client.
3. Client components that need interactivity and DOM manipulation are marked with "use client".
4. Client components need a way to run server code.
5. That's where server actions come in. They are server functions that are exposed to the client.
6. You mark server actions with "use server".
7. Server actions can be called from client components.
8. To keep track of the "work" of a server action, you can use `useTransition`.
9. Thanks to concurrent React, all of this is possible.

# Details of React Server Components

## RSC Payload

At the heart of RSCs is the concept of the RSC payload. This allows React to mix Server and Client components together.

When a Server Component is rendered on the server, React generates a special JSON-like data structure called the RSC payload for the client components inside the Server Component.

This payload contains:

1. The rendered output of the Server Component as HTML.
2. Placeholders indicating where Client Components should be rendered.
3. References to the JavaScript files needed for the Client Components.
4. Serialized props to be passed from Server Components to Client Components.

Here's an example of a Server Component and its related RSC payload:

```jsx
// ServerComponent.js
export default function ServerComponent() {
  return (
    <div>
      <h1>Welcome to my app!</h1>
      <ClientComponent />
    </div>
  );
}

// ClientComponent.js
'use client';
export default function ClientComponent() {
  return <button>Click me!</button>;
}
```

The payload may look something like this:

```json
{
  "html": "<div><h1>Welcome to my app!</h1><!--$-->",
  "clientComponents": [
    {
      "id": "ClientComponent",
      "chunk": "client-component.js",
      "props": {}
    }
  ]
}
```

The `$` in the HTML string is a placeholder where the Client Component will be rendered.

On the client side, React uses this RSC payload to build the component tree and hydrate the Client Components.

**Note:** The actual RSC payload format is sent as a binary format to reduce the payload size. On the client, React parses this binary format to reconstruct the component tree.

## Bundling

With RSCs, React introduces a new way of bundling components. Instead of bundling all components into a single JavaScript file, RSCs split components into separate server and client bundles.

Now, whatever you import into a "use client" component will be a part of the client bundle. So, if you include server components that aren't doing server-only stuff like writing to the database, they'll be bundled with the client code:

```jsx
// ClientComponent.js
"use client";
import ServerComponent from "./ServerComponent";

export default function ClientComponent() {
  return (
    <div>
      <ServerComponent />
      <button>This is a Client Component</button>
    </div>
  );
}
```

This is a client component that imports a server component. The server component will be bundled with the client code.

If you use the same component (the server one) in another server component, it will be bundled as a server component in that context.

This type of component would be a "shared" component. It's used in both server and client components.

## Streaming and Suspense

Streaming is a powerful feature of RSCs. We don't have to wait for the entire component tree to be rendered on the server before sending it to the client. Instead, we can stream the HTML output of the Server Components as they are rendered. "Stream" means sending the data in chunks as it becomes available.

Let's say of 20% of the component tree is rendered on the server, we can begin sending that 20% to the client while the remaining 80% is still being rendered (processed).

This enables faster initial loading and interactivity.

To leverage streaming effectively, you can use the `Suspense` component from React. `Suspense` allows you to define loading states for parts of your component tree that are not yet ready.

A component that's waiting for some asynchronous operation to complete before it can render is called "suspended".

### Example

```jsx
// app/layout.js
import { Suspense } from 'react';
import { Header } from './Header';
import { Footer } from './Footer';

export default function Layout({ children }) {
  return (
    <html>
      <body>
        <Header />
        <Suspense fallback={<div>Loading...</div>}>
          {children}
        </Suspense>
        <Footer />
      </body>
    </html>
  );
}

// app/page.js
import { Suspense } from 'react';
import { ProductList } from './ProductList';
import { RecommendedProducts } from './RecommendedProducts';

export default function Home() {
  return (
    <div>
      <h1>Welcome to our store!</h1>
      <Suspense fallback={<div>Loading products...</div>}>
        <ProductList />
      </Suspense>
      <Suspense fallback={<div>Loading recommendations...</div>}>
        <RecommendedProducts />
      </Suspense>
    </div>
  );
}

// app/ProductList.js
async function getProducts() {
  const res = await fetch('https://api.example.com/products');
  return res.json();
}

export async function ProductList() {
  const products = await getProducts();

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}

// app/RecommendedProducts.js
async function getRecommendedProducts() {
  const res = await fetch('https://api.example.com/recommended');
  return res.json();
}

export async function RecommendedProducts() {
  const products = await getRecommendedProducts();

  return (
    <ul>
      {products.map((product) => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

Here, we have a `Layout` component that wraps the entire app. It includes `Suspense` because by having `Suspense` at the top level, we define a global loading state for all the routes in our app. It provides a consistent loading experience and improves the perceived performance of the app.

On the `Home` page, we have two main components: `ProductList` and `RecommendedProducts`. Both components fetch data from an API and render a list of products. We wrap each component in a `Suspense` component with a loading fallback. This way, we can show loading indicators while the data is being fetched.

The nice part is that because of Concurrent React, we can stream both components independently. This provides a nice experience when having multiple parts of the page loading at different times.

### Under the hood

It's important to note that the fallback will be shown until the first component is ready. Once the first component is ready, the fallback will be replaced with the content of the first component. Once subsequent components are ready, they will be shown.

By a "component" being ready, I mean the element in the map function e.g. `<li key={product.id}>{product.name}</li>`.

This is possible because React maintains a virtual representation of the component tree during the rendering process. It knows when a component has finished rendering and can make the decision to send that chunk of HTML independently.

This way React doesn't send a "half" of a component. It waits until the component is fully rendered and then sends it.

This smart chunking mechanism allows React to progressively stream meaningful parts of the UI to the client.

### How streaming improves the UX

Streaming enhances the user experience in scenarios where the content is not immediately available and takes some time to load.

Instead of waiting for the entire page to load before displaying anything, streaming allows the user to see parts of the page as soon as they are ready. This is particularly beneficial for:

- **Pages with slow data fetches:** If a page relies on data from a slow API or database query, streaming can show the static parts of the page first while the dynamic content is loading.
- **Complex pages with multiple sections:** Streaming allows different sections of the page to load independently. Users can start interacting with the already loaded sections while the remaining sections are still loading
- **Slow network connections:** Streaming provides a better experience for users with slow network connections. They can see the content gradually appearing instead of staring at a blank screen for a long time

# Misconceptions

## All server code need "use server"

This is false. "use server" is only needed for server actions. It's only needed for server code you want to expose to the client.

As mentioned before, if we exposed all server code to the client, it would be a security risk.

## RSCs are a replacement for CSRs

RSCs are not a replacement for CSR (Client-Side Rendering). They are a complement to CSR. RSCs are designed to work alongside CSR to provide a better user experience.

That's why we have both server and client components.

## Client components are never server-rendered

This is false. Client components start on the server and are hydrated on the client. That's why we have the RSC payload, to hydrate the client components.

## Client components can't import any server components

This is false. Client components can import server components that don't do server-only stuff like writing to the database. They will be bundled with the client code.

So you can have shared components that are used in both server and client components.

They will end up in different bundles depending on the context.

That's why it's important to be mindful of the components you import into client components because they will increase the client bundle size.

## Require a specific directory structure

This is false. Even though it's a convention to use `.server.js` and `.client.js` for server and client components, it's not a requirement.

This is just something subjective.

## Everything imported into client components need "use client"

You only need to mark the client component with "use client". It's like the "entry point" into the client world. Everything imported into the client component will a part of this world.

In technical terms: Everything imported into a "use client" component will be bundled with the client code.
