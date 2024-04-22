# Introduction

Have you ever wondered how to achieve a circular-like functionality with an array?

For example, let's say you have a component that displays a list of items. You can navigate the list by clicking "Next" and "Previous" buttons. When you reach the end of the list, you want to loop back to the beginning.

This can be achieved by using the modulo operator `%`.

Let's dive in!

# Modulo Operator

The modulo operator `%` returns the remainder of a division operation.

What's the remainder?

`4 % 2` returns `0` because `4` divided by `2` is `2` with no remainder.

`5 % 2` returns `1` because `5` divided by `2` is `2` with a remainder of `1`.

You can think of it as how many times does the right-hand number fit into the left-hand number, and what's left over.

`5 % 2` -> 2 fits into 5 two times with 1 left over.

# What about the other way around?

What if you have `2 % 5`?

This is where it gets interesting.

`2 % 5` returns `2` because `2` divided by `5` is `0` with a remainder of `2`. To be clear, `5` fits into `2` zero times with `2` left over.

An easy way to think about this: Every time the left-hand number is less than the right-hand number, the result is the left-hand number.

# How to use the modulo operator for circular functionality

Referring back to the example in the introduction, you can use the modulo operator to loop back to the beginning of the list when you reach the end.

Let's look at an example:

```jsx
const items = ["A", "B", "C", "D", "E"];
let currentIndex = 0;

function nextItem() {
  currentIndex = (currentIndex + 1) % items.length;
  return items[currentIndex];
}

function previousItem() {
  currentIndex = (currentIndex - 1 + items.length) % items.length;
  return items[currentIndex];
}

console.log(nextItem()); // Output: "B"
console.log(nextItem()); // Output: "C"
console.log(nextItem()); // Output: "D"
console.log(nextItem()); // Output: "E"
console.log(nextItem()); // Output: "A"
console.log(previousItem()); // Output: "E"
console.log(previousItem()); // Output: "D"
```

## Next Item

Let's break it down, starting with `nextItem`. `(currentIndex + 1)` this increments the `currentIndex` by `1`. We do this because we want to move to the next item in the list.

The first time this is called `currentIndex = (currentIndex + 1) % items.length` evaluates to `1 % 5` which is `1`. This means we're now at index `1` in the `items` array.

What happens if we're at the last item in the list? The last item would have the index `4`. `4 % 5` is `4`, which is fine. But if we move to the next item after that, we would be at index `5`. This is out of bounds and where we want to loop back to the beginning. `5 % 5` is `0`, which is the first item in the list.

And as you see we're always reassigning the `currentIndex`. So after the last item, `currentIndex` is back to `0`.

## Previous Item

What if we want to go back to the previous item? We use the same logic but in reverse.

`(currentIndex - 1 + items.length) % items.length` this decrements the `currentIndex` by `1`. We add `items.length` to ensure we don't get a negative number. Then we use the modulo operator to loop back to the end of the list if we're at the beginning.

Let's say we're at the first item and want to go back. This would mean moving to the last item in the list. `0 - 1 + 5 % 5` is `4 % 5` which is `4`. This means we're now at index `4` in the `items` array.

What if we're at the last item and want to go back? This would mean moving to the second to last item in the list. `4 - 1 + 5 % 5` is `8 % 5` which is `3`. It's because 5 fits into 8 once with 3 left over.

# Conclusion

The modulo operator `%` is a powerful tool for creating circular-like functionality with arrays. It allows you to loop back to the beginning of the list when you reach the end. This can be useful for creating navigational components or any situation where you need to cycle through a list of items.
