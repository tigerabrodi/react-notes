# Regexes in JavaScript

Regular expressions in JavaScript are objects that represent patterns used for matching and manipulating strings.

There are two ways to create a regular expression in JavaScript:

- Using the regular expression literal syntax: `/pattern/flags`.
- Using the `RegExp` constructor function: `new RegExp('pattern', 'flags')`.

I prefer the literal syntax because it's more concise and easier to read.

But in cases where you need e.g. template literal strings in your regex, you'll have to use the `RegExp` constructor function.

# Regex Compilation

When a regex is created, it gets compiled. The JavaScript engine analyzes the pattern and converts it into an internal representation that can be efficiently used for matching.

This compilation step involves parsing the pattern, validating its syntax, and creating an optimized data structure for fast matching.

To be clear, Regexes are JavaScript objects under the hood. But the JavaScript engine may further optimize them for performance.

# Regex state

Regexes in JavaScript have internal state that can be modified during matching operations.

One worth of mentioning is the `lastIndex` property, which is used with the g (global) flag. It keeps track of the index at which the next match should start.

When a match is found, `lastIndex` is updated to the index following the matched substring. This allows following matches to start from the correct position.

```js
const regex = /pattern/g;
const str = "text pattern text";
console.log(regex.exec(str)); // ["pattern"]
console.log(regex.lastIndex); // 11
console.log(regex.exec(str)); // null
```

Here we have a regex with the global flag `/pattern/g` and a string `'text pattern text'`. The first call to `regex.exec(str)` finds the pattern at index 5 and updates `lastIndex` to 11. The second call returns `null` because there are no more matches following index 11.
