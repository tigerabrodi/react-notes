# Introduction

I enjoy reading Open Source code. Especially ones around [performance optimization](https://github.com/wooorm/stringify-entities/pull/13).

I have always had a tough time understanding `WeakMap` in JavaScript. And it's not because it's a complex concept, but rather because I had a wrong mental model of it.

# Map and its problems

Let's say we want to cache the result of an expensive computation based on some object. We can use a Map to store the object as the key and the computed result as the value:

```js
const cache = new Map();

function expensiveComputation(obj) {
  if (cache.has(obj)) {
    return cache.get(obj);
  }

  const result = /* expensive computation based on obj */ ;
  cache.set(obj, result);
  return result;
}
```

We'd perhaps use it like this:

```js
const cache = new Map();

function useObj() {
  const obj = { large: "object" };
  const result = expensiveComputation(obj);

  // obj is no longer needed
  // however, it's still in memory
}
```

## Key thing to understand

The key thing to understand here is when creating objects, they're stored in memory. When we use them as keys in a Map, the keys are strongly held. This means that as long as the Map exists, the keys will not be garbage collected.

The objects will remain in memory, even if they're no longer needed.

## Garbage collection

Garbage collection is a process where the JavaScript engine frees up memory by removing objects that are no longer in use. This is important because if we have a lot of objects in memory that are no longer in use, it can lead to memory leaks.

## Memory leaks

The definition of memory leak: When a program doesn't release memory it no longer needs, it's called a memory leak. It's inefficient because we're using memory that we don't need.

## My misunderstanding

I initially thought that the "garbage collection" process is removing the key from the Map. But that confused the shit out of me. I didn't understand how it's more efficient.

Instead, it's about removing the object from the actual memory. Which makes more sense. If we no longer need an object, we should remove it from the memory.

# Enter WeakMap

WeakMap is different. Keys are weakly held. In translated words: If an object is used as key in a WeakMap, JavaScript knows to remove the object from the memory if it's no longer in use.

"No longer in use" means that the object is no longer needed in the program.

Let's look at the same example:

```js
const cache = new WeakMap();

function expensiveComputation(obj) {
  if (cache.has(obj)) {
    return cache.get(obj);
  }

  const result = /* expensive computation based on obj */ ;
  cache.set(obj, result);
  return result;
}
```

We'd perhaps use it like this:

```js
const cache = new WeakMap();

function useObj() {
  const obj = { large: "object" };
  const result = expensiveComputation(obj);

  // obj is no longer needed
  // removed from memory
}
```

In this case, when `obj` is no longer needed, it's removed from the memory. This is because the key is weakly held. It's like magic where JavaScript knows when to remove the object from the memory.

# Caveats

- WeakMap can only have objects as keys. This is because it's about removing objects from memory.
- WeakMap is not iterable. This means we can't loop over it like we can with a Map. Map has `entries` method for example.
- WeakMap doesn't have a `size` property, while Map does.

# Caution

Don't use WeakMap everywhere. It's a specialization tool. Most of the time, you'd want to use a Map. WeakMap can in fact introduce problems themselves if overused.

Many times, you'd want to keep the object in memory to use it later.
