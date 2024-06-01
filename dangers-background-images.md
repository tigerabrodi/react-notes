# Dangerous code

Assume, you've code where you get images from a CMS.

These images should be background images that keep changing.

You approach it in a way where you map out the images and set them as background images.

```tsx
import React from "react";

const BackgroundImages = ({ images }: { images: string[] }) => {
  return (
    <div className="flex">
      {images.map((image, index) => (
        <div
          key={index}
          className="w-32 h-32 bg-cover bg-center"
          style={{ backgroundImage: `url(${image})` }}
        />
      ))}
    </div>
  );
};

export default BackgroundImages;
```

Now, your code may look different, but this code is just to demonstrate the dangerous code.

Can you already see the problem?

If not, don't worry. We'll dive into how the browser parses HTML and CSS for us to understand this problem.

# Browser receives an HTML document

When the browser receives an HTML document, it parses it from top to bottom, building the DOM tree.

If the parser encounters a `<script>` tag referencing an external script, it stops parsing the HTML document and fetches the script. You don't want this to happen. Solutions like `defer` and `async` are [there to help](https://tigerabrodi.blog/more-of-my-notes-on-web-performance#heading-defer-and-async). Your default go-to should be using `defer` for scripts. I'm not here to explain why, that's covered in my previous blog posts. Several of them, actually.

# What about `<link>` tag referencing stylesheets?

When the parser encounters a `<link>` tag referencing a stylesheet, it requests that file. Howeverm the parsing of the HTML document can continue. A new parser is started. This is the CSS parser. It builds the CSSOM (CSS Object Model) tree.

Why the browser needs the CSSOM tree:

- Allows browser to efficiently apply styles.
- JavaScript can access and modify styles via the CSSOM APIs.
- CSSOM is used in the critical rendering path. Critical rendering path is the sequence of steps the browser goes through to convert HTML, CSS, and JavaScript into pixels on the screen.

# When CSS parser encounters external links

What happens when the CSS parser encounters an external link?

It triggers a request and downloads the file. This blocks the CSS parser from continuing.

If you think through this, this "blocking" is a serious issue. It blocks the critical rendering path from completing. Because we need the CSSOM tree to complete the critical rendering path.

# The problem with the code

Going back to our code, you should see the problem now.

The fact that we have to request and download the images sequentially is a problem. This blocks the CSS parser from continuing big time.
