# Introduction

In this post, we'll demistify the term "hydration" on the web.

# What is Hydration?

Hydration refers to the process of attaching JavaScript behavior to HTML elements that have been generated on the server. When a web page is rendered on the server using techniques like Server-Side Rendering (SSR), the initial HTML is sent to the client as a static representation of the page.

However, to make the page interactive and dynamic, the JavaScript code needs to be executed on the client side.

Hydration is the process of "watering" the static HTML with interactivity and event handlers. It involves the following steps:

1. The server generates the initial HTML markup and sends it to the client.
2. The client receives the HTML and renders it in the browser.
3. The JavaScript code is downloaded and executed on the client side.
4. The JavaScript code attaches event listeners and adds interactivity to the HTML elements.

By hydrating the server-rendered HTML, the web application becomes fully interactive and responsive to user actions.

A common confusion is that it's the browser performing the hydration process. However, it's the JavaScript itself that performs the hydration process by attaching event listeners and updating the DOM.

# Different Approaches to Hydration

There are different approaches to hydration.

I want to touch on two of them: full hydration and partial hydration.

# Full Hydration

The approach we just described is known as full hydration. In full hydration, the entire page is rendered on the server and sent to the client as static HTML. The client-side JavaScript then hydrates the entire page, making it interactive.

# Partial Hydration

Partial hydration is an approach where only specific parts of a web page, known as "islands" or "components," are hydrated with JavaScript functionality on the client-side. This is in contrast to full hydration, where the entire page is hydrated at once.

In frameworks like Marko and Astro, the initial HTML is rendered on the server and sent to the client. The HTML includes placeholders or markers for interactive components. When the page loads in the browser, only the necessary components are hydrated with JavaScript, while the rest of the page remains static.

The exciting part here is that the time to interactive is much faster because only the necessary parts are hydrated. This can lead to a better user experience and performance.

So, like SSR, we send the initial HTML with content to the client. However, instead of needing to wait for the entire page to be hydrated, we only need to wait for the necessary parts to be hydrated so the user can start interacting with the page sooner.

## How Partial Hydration Works

Here's how partial hydration generally works in these frameworks:

1. **Server-side rendering:** The framework renders the initial HTML on the server, including the static content and placeholders for interactive components.
2. **Selective hydration:** The framework identifies which components need to be interactive and includes the necessary JavaScript for those components.
3. **Client-side rendering:** When the page loads in the browser, the framework selectively hydrates only the marked components with JavaScript, making them interactive.
4. **Static content remains static:** The parts of the page that don't require interactivity remain as static HTML, without any JavaScript overhead.

For example, in Astro, you have the concept [islands](https://docs.astro.build/en/concepts/islands/). You can mark an island as interactive **yourself** by using the `client:*` directive. There are multiple different ways to tell Astro which parts of the page should be hydrated.

One example is `client:visible`: This will only hydrate the component when it becomes visible in the viewport.
