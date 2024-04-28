# Introduction

In this post, we'll take a look at the evolution of how we render in React and got to the point of React Server Components.

# Client-side rendering

When React was first introduced, it was primarily used for client-side rendering. This means that the entire application was rendered on the client side, in the browser.

The initial HTML document is minimal. The client then:

- Downloads the JavaScript bundle.
- Parses and executes the JavaScript.
- Renders the application in the browser.
- Fetches data from the server and hydrates the application.
- Updates the UI as needed.

This approach is nice because once loaded, allows for an interactive experience. However, it has some downsides:

- Slow initial load times due to the need to download and parse large JavaScript bundles.
- Poor SEO as search engines struggled to index content rendered on the client-side.
- Not the best user experience, especially on slower networks or devices.

# Waterfall

Waterfall is the the chart that shows the time it takes to load a page. It's a good way to visualize the performance of a website.

It shows the sequence and timing of each resource (HTML, CSS, JavaScript, images, etc.) as it's requested and loaded by the browser.

## Horizontal bars

Each resource is represented as a horizontal bar, with the length of the bar indicating the time it takes to load that resource.

Longer bars suggest slower loading times for that particular resource, which could be a performance bottleneck.

Ideally, you want the bars to be as short as possible to ensure fast page load times.

## Number of rows

Each row represents a resource. Having too many rows can indicate that the page is loading a large number of resources, which can slow down the page load time.

## colors

The colors of the bars in the waterfall provide additional information about each resource.

In Chrome DevTools, the colors typically represent the type of resource:

- Blue: HTML.
- Green: CSS.
- Purple: Images.
- Orange: JavaScript.
- Gray: Other resources.

## Good waterfall

Good waterfall charts have:

- Short bars: Indicating fast load times for each resource.
- Few rows: Indicating that the page is loading a small number of resources.

## Bad waterfall

Bad waterfall charts have:

- Long bars: Indicating slow load times for resources.
- Many rows: Indicating that the page is loading a large number of resources.

## How to improve the waterfall

### Minimize the number of requests

- Review each resource on your pages and determine if it's truly necessary. Remove any unnecessary content, scripts, or styles.
- Combine multiple CSS or JavaScript files into single files where possible. This reduces the number of requests the browser needs to make. You can use tools like Webpack or Gulp to bundle and minify your assets.
- Inline small CSS or JavaScript files (less than 2KB) directly into the HTML to eliminate additional requests.

### Optimize and compress assets

- Minify CSS, JavaScript, and HTML files by removing unnecessary characters like whitespace, comments, and formatting. This reduces file sizes.
- Compress images using tools like ImageOptim, TinyPNG, or Squoosh. This reduces image file sizes without sacrificing quality.
- Use modern image formats like WebP, which offer better compression and quality compared to older formats like JPEG or PNG.
- Leverage browser caching by setting appropriate cache headers for your assets. This allows the browser to store assets locally and avoid re-downloading them on subsequent visits.

### Prioritize critical resources

- Preload key resources like fonts, critical CSS, and hero images using `<link rel="preload">`. This prompts the browser to download them sooner.

### Optimize the critical rendering path

- Structure your HTML to load critical CSS and content first. Move scripts to the bottom of the page where possible.
- Minimize render-blocking JavaScript by using async or defer attributes on script tags. This allows the browser to continue parsing and rendering the page while the script is being downloaded. Both `async` and `defer` attributes will download the script asynchronously, but `defer` will execute the script after the HTML has been parsed, while `async` will execute the script as soon as it's downloaded. So `async` can block rendering, while `defer` will not. `async` is good for scripts that don't depend on the DOM and are independent of other scripts, while `defer` is good for scripts that need to be executed in order or depend on the DOM.
- Preconnect to external domains that host critical resources like fonts, APIs, or CDNs. This establishes early connections to these domains, reducing the latency when fetching resources.

### Monitor and analyze performance

- Use tools like Lighthouse, PageSpeed Insights, or WebPageTest to analyze your site's performance and identify areas for improvement.

# The "Waterfall" Model in CSR

In a traditional CSR application, the rendering process follows a "waterfall" model:

1. The browser requests the initial HTML document from the server.
2. The server sends a minimal HTML document with placeholders for dynamic content.
3. The browser starts parsing the HTML and discovers the JavaScript bundle.
4. The browser requests and downloads the JavaScript bundle.
5. The JavaScript bundle is parsed and executed, and the React application is initialized.
6. The application makes API requests to fetch necessary data.
7. Once the data is received, the application renders the dynamic content on the client-side.

This waterfall model can lead to slower perceived performance, as the user has to wait for the JavaScript bundle to be downloaded, parsed, and executed before seeing any meaningful content.

# Server-side rendering (SSR): Addressing CSR challenges

To address the limitations of CSR, Server-Side Rendering (SSR) was introduced. With SSR, the server renders the complete HTML content and sends it to the browser, providing several benefits:

- Faster initial page load times, as the browser receives a fully rendered HTML document.
- Improved SEO, as search engines can easily index the server-rendered content.
- Better user experience, especially for users with slower networks or devices.

# Challenges with SSR

One issue with SSR, there is no way to pause the rendering process while waiting for data. Fetching data must be completed before the server can send the fully rendered HTML to the client.

This leads to slower initial load times, as the server has to wait for all data to be fetched before sending the response.

The other issue is that React must hydrate the entire page before you can interact with any of the interactive components. It's not like you can hydrate the page in chunks and have it be interactive as it loads.

# Suspense for SSR

React 18 introduced `Suspense`, a powerful feature that enhances SSR and addresses some of its challenges. With `Suspense`, you can:

- Incrementally render and stream HTML from the server to the client.
- Start hydrating your app as early as possible, even before the rest of the HTML and JavaScript code are fully downloaded. This creates an illusion of instant hydration aka improved perceived performance.
- Code split and lazy load components. Suspense works well with `React.lazy`, allowing you to code split your application and lazy load components. "Code split" means that you can split your code into smaller bundles that are loaded on demand, reducing the initial load time. "Lazy load" means that you can load components only when they are needed. For example, you can load a component only when a user navigates to a specific route.

This addresses some of the challenges of SSR:

- You no longer have to wait for all the data to load on the server before sending HTML.
- You can start hydrating the app before all the JavaScript has loaded.
- Users can interact with parts of the page without waiting for the entire app to hydrate.

# Challenges with Suspense for SSR

1. **Large JavaScript bundle sizes:** Even though Suspense allows streaming JavaScript code to the browser asynchronously, the entire JavaScript bundle for the application must still eventually be downloaded by the user. This is because Suspense only affects the timing and order of when parts of the application are rendered and made interactive, but it doesn't fundamentally change the fact that the complete application code needs to be available on the client for the application to function properly. As applications grow in features and complexity, the size of this JavaScript bundle increases, which can impact performance, especially on slower networks.
2. **Default hydration of all components:** While Suspense enables selective hydration, the default behavior is still that all components will be hydrated on the client-side, even those that don't require interactivity. This is because React's default approach is to ensure that the client-side application matches the server-rendered HTML for consistency and to avoid potential UI mismatches. Developers need to explicitly opt-in to selective hydration by wrapping components in `<Suspense>` boundaries. So while Suspense provides a solution, it doesn't change the default hydration behavior, which can still lead to unnecessary work for the client.
3. **Heavy client-side processing:** Even with Suspense SSR, after the initial HTML is delivered, the client-side device still needs to execute all the JavaScript code to make the page interactive. This is because the server-rendered HTML is static and doesn't include the event handlers, state management, and other interactive logic that React provides. This includes the React library itself, the application code, and any third-party libraries that the application depends on. For complex applications, this can be a great amount of JavaScript for the user's device to process, which can be slow on low-powered devices, impacting the time to interactivity.

# Introducing React Server Components

React Server Components (RSCs) were introduced to address the limitations of React 18 and Suspense. RSCs provide a more comprehensive solution for server-side rendering and data fetching in React applications.

I've covered the ins and outs of React Server Components in a separate post.

I wanna focus on the benefits of RSCs in this post:

- **Zero bundle size:** RSCs allow you to fetch and render components on the server without including them in the client-side bundle. This means the JavaScript sent to the client is minimal, improving performance. The only JavaScript you would need to send is for interactive components that need to be hydrated.
- **Enhanced security:** By keeping sensitive logic and data on the server, RSCs can improve the security of your application.
- **Direct server access:** RSCs have direct access to server-side resources, such as database and file systems. This eliminates the need to make additional API requests from the client, reducing latency and improving performance.
- **Caching:** RSCs can be cached on the server, reducing the need for repeated data fetching and rendering.
- **Simplified data fetching:** With RSCs, data fetching can be co-located with the components that need the data, making the code easier to understand and maintain.

# Future of React Server Components

Currently, Client components are like `client:load` directive in Astro. They are hydrated right away on the client. You can't configure a client component to hydrate when the browser is idle or when it's in view.

I could see a future where that's possible with React Server Components. You could configure a client component to hydrate only when it's in view or when the browser is idle. This would further improve performance by deferring the hydration of non-critical components until they are needed.

Personally, I love React Server Components a ton!
