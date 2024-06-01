# Response body can only be read once

I had a silly situation at work where I was trying to see what's in the response of a request.

I placed `console.log(await response.json())` before calling `response.json()` where I'm returning it.

This gave me an error in lines of: `TypeError: Body is unusable.`.

I was confused. I thought it was a backend issue. I was so confused I forgot I had placed a `console.log` in my code. Now, I didn't think it would cause an issue. So, it took me a while to figure out what was going on.

# Removing the console log

When removing the console log, the error went away. This surprised me. Why would a console log cause an issue?

After some research, I found out that the response body can only be read once. This is why I was getting the error.

# What is Response

In JavaScript, a `Response` object is the response to a request. It's returned by `fetch()`. It's a readable stream, which means the response body data can be read asynchronously and processed in chunks.

This stream can only be read once. If you try to read it again, you'll get an error.

# Why can a response body only be consumed once?

It's implemented as a readable stream under the hood. It's like the whole response body data is divided into chunks. And these chunks are in a queue. When you read a chunk, it's removed from the queue.

When you do `response.json()`, it reads the whole response body data and parses it as JSON. This means the whole response body data is consumed. So the queue is empty.

This is a part of the [Streams API](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API) and is implemented consistently across browsers.

# What if I want to read the response multiple times?

If you need to read the response multiple times, you can clone the response. This creates a new response object with the same data. You use `response.clone()` to achieve this:

```js
fetch("https://example.com").then((response) => {
  const clone = response.clone();

  // Consume the original response
  response.json().then((data) => {
    console.log(data);
  });

  // Consume the cloned response
  clone.text().then((text) => {
    console.log(text);
  });
});
```
