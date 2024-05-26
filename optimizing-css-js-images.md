# CSS before JS

CSS links should be placed in the `<head>` of the document, while JavaScript links should be placed at the end of the document, just before the closing `</body>` tag. This is because CSS is render blocking, while JavaScript is not.

Meaning, the browser will not render until it has loaded the CSS. This is why it's important to load CSS first, so the browser can start rendering the page as soon as possible.

Edit: You don't need to place the JS script tags at the end of the document. With the new attributes like `defer` and `async`, you can place the JS script tags in the `<head>` of the document.

# Do not @import in CSS

When using the `@import` rule in CSS, we get additional waterfall requests:

1. Browser requests the CSS file.
2. Browser sees the `@import` rule and requests the imported file.

As mentioned, the browser won't render until it has loaded all the CSS. So, if we have multiple `@import` rules, the browser will have to wait for each file to load before rendering the page.

This is really expensive!

Instead, use multiple `<link>` tags to load CSS files in parallel.

# Defer JavaScript

Defer JavaScript as much as possible. It blocks the rendering of the page and uses the main thread, which can slow down the page.

How HTML parsing works: When it encounters a `<script>` tag, it stops parsing the HTML and executes the JavaScript. After that, it continues parsing the HTML.

## Defer and async

Defer: Downloads the script in parallel to parsing the HTML. It executes the script after the HTML is parsed. This is your default.

Async: Downloads the script in parallel to parsing the HTML. It executes the script as soon as it's downloaded. This one is more dangerous because even tho it's done in parallel, it can still harm the parsing if expensive. Use this one for scripts that don't depend on the DOM and aren't expensive.

# Preload images

You don't want the browser to wait for all CSS before it starts downloading the images. This can happen in parallel. This doesn't mean rendering the images, just downloading them. When it comes to render, the browser will have the images ready in it's cache.

```html
<link rel="preload" href="image.png" as="image" />
```

# `fetchpriority` attribute

By default, browser gives each resource to load a priority. As the authors, we know e.g. the most important things to load for a good LCP. We can use the `fetchpriority` attribute to tell the browser to load this resource with a higher priority.

- `high`: The resource is critical for the page load.
- `low`: The resource is not critical for the page load.
- `auto`: The browser decides the priority.

Supported elements are: `<img>`, `<script>`, `<link>` and `<iframe>`.

```html
<img src="image.png" fetchpriority="high" />
<script src="script.js" fetchpriority="low"></script>
<link href="style.css" fetchpriority="high" />
<iframe src="iframe.html" fetchpriority="low"></iframe>
```

# `preconnect`

The `preconnect` link relation type is used to indicate an origin that will be used to fetch required resources. Initiating an early connection, which includes the DNS lookup, TCP handshake, and optional TLS negotiation, allows the browser to mask the high latency costs of establishing a connection.

# Lazy load images

You can use the `loading` attribute to lazy load images. This tells the browser to only load the image when it's in the viewport.

```html
<img src="image.png" loading="lazy" />
```

Other values for the `loading` attribute are `eager` and `auto`.

`eager` is the default value. It tells the browser to load the image as soon as possible.
