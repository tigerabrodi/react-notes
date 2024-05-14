# Hoisted Regex Performance Optimization

In JavaScript, when you define a regular expression using the literal syntax (e.g., `/pattern/flags`), the regex is compiled at the point where it is defined in the code.

If the regex is defined inside a function that is called multiple times, the regex compilation will occur each time the function is invoked. This repeated compilation can have a performance impact, especially for complex regexes or frequently called functions.

To optimize performance, a common technique is to hoist the regex definition outside the function. By doing this, the regex is compiled only once when the script is loaded, and following function calls can reuse the same compiled regex instance.

```js
// Unoptimized
function processString(str) {
  const regex = /pattern/flags;
  // ...
}

// Optimized
const regex = /pattern/flags;
function processString(str) {
  // ...
}
```

In the optimized version, the regex is defined at the top level, outside the function scope. This ensures that the regex is compiled only once, improving performance for repeated function calls.

The trade off here is that the regex is now a global variable. It remains in memory for the lifetime of the script, which may not be desirable in some cases. However, the performance benefits of hoisting the regex can outweigh the memory considerations, especially for performance-critical code paths (also known as hot code).
