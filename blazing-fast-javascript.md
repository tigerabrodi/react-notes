# Performance Tab Chrome

When running `node --inspect`, it starts the Node.js process with the V8 inspector enabled. This allows you to connect to the Node.js process and debug it in Chrome DevTools.

When you navigate to `chrome://inspect` in Chrome, you can see a list of all the Node.js processes that are running with the V8 inspector enabled. You can click on the "inspect" link to open the Chrome DevTools and start debugging the process.

Open performance tab in chrome. Hit Record, do the actions you want to do and hten click stop. This will show you a timeline of what's happening.

- Overview
- Flame chart
- Bottom up
- Call tree
- Event Log

## Overview

A timeline at the top showing CPU utilization over time. Taller bars indicate higher CPU usage. Different colors represent different browser activities like scripting (orange), rendering (purple), painting (green) etc.

A frames per second (FPS) chart showing the frame rate over time. Higher green bars are better, indicating the page was responsive and smooth.

## Flame chart

Each bar represents a function call. Wider bars mean the function took longer to execute.

Functions are stacked on top of each other to show the call hierarchy. The function on top called the ones below it.

## Bottom up

The "Self Time" column shows how long a function took to execute its own code, excluding time spent in functions it called.

The "Total Time" column shows the total time spent in a function including functions it called.

You can expand each function to see its parent functions (who called it) and child functions (who it called).

If a function has a high "Self Time" but low "Total Time", it's likely that the function itself is inefficient. If a function has a high "Total Time", it's likely that the functions it calls are inefficient.

### General approach

1. Look for functions with a high "Total Time" in the Bottom-Up view. This points to branches of the call tree that consume a lot of time.

2. Expand those branches and traverse down to find the child functions with high "Self Time". These are the inefficient functions themselves.

## Call tree

The Call Tree is similar to the Bottom-Up view but organizes the data as a tree structure based on the call hierarchy.

# Sets and Maps aren't always the right answer

Sets and Maps are done differently under the hood. They are implemented as hash tables, which means they have O(1) lookup time. However, don't let that fool you.

Under the hood, a hash table is an array of "buckets". When you insert a key-value pair, the hash function determines which bucket the key should go into. If two keys hash to the same bucket, they are stored in a linked list.

Arrays are contiguous blocks of memory, which means they have better cache locality. When you iterate over an array, you can process elements one after the other without jumping around in memory.

So, not always are Sets/Maps the right answer. In fact, for a small number of elements, an array might be faster. Because even when you run `includes` on an array, it's O(n) but the bytes are contiguous in memory. Meaning next to each other. This is what makes it faster.

Use an array up to a certain size and then switch to a Set/Map if needed when the size grows.

# Memory profiling

The memory panel in chrome devtools provides a way to take heap snapshots, record allocation timelines, and analyze memory usage.

- Heap Snapshot: Shows a summary of memory usage and allows you to compare snapshots to find memory leaks.
- Allocation instrumentation on timeline: Shows when memory is allocated and garbage collected.
- Allocation sampling: Similar to the timeline but it uses a sampling approach instead of recording every single allocation. It periodically samples memory allocations to reduce overhead.

# General approach to using these tools

1. Use "Allocation Instrumentation on Timeline" or "Allocation Sampling" to record memory allocations over a period of time where you suspect there might be a memory issue.

2. Look for unexpected growth in memory usage over time, or large allocations that seem unnecessary.

3. Use the recorded stack traces to identify the parts of your code responsible for those allocations.

4. Take "Heap Snapshots" at different points (e.g., before and after performing a suspected leaky operation) and compare them to see if certain objects are accumulating in memory and not being cleaned up.

5. Optimize your code to reduce unnecessary allocations and properly clean up objects when they're no longer needed.

# Promises

Having a large number of promises can negatively impact performance. Each promise has memory overhead for storing state and requires additional work for the javascript engine to manage.

## Minimize the number of promises

- Avoid unnecesarily marking functions as `async`. This causes them to return a promise even if they don't need to.
- Avoid creating promises in a loop. Instead, use `Promise.all` to create a single promise that resolves when all the promises in the array have resolved.

## Promise.all for concurrency

When you've multiple promises that can be executed concurrently, use `Promise.all` to run them in parallel.

This allows them to resolve in parallel, rather than sequentially.

```js
// Sequential (slower)
const user = await fetchUser();
const product = await fetchProduct();

// Concurrent (faster)
const [user, product] = await Promise.all([fetchUser(), fetchProduct()]);
```

## Promise chain

Avoid deeply nested promise chains. Deeply nested .then() chains can become hard to read and lead to "promise hell", similar to callback hell.

## How promises work

When a new promise is created using `new Promise()`, an instance of the Promise object is allocated in memory. This object stores the state of the promise (pending, fulfilled, or rejected) and the result or error value once it settles

When the executor function is called, it runs synchronously. Inside the executor, asynchronous operations can be initiated. The executor should call `resolve(value)` when the async operation completes successfully, or `reject(error)` if an error occurs.

The Promise object returned by the constructor has an internal `[[PromiseState]]` property that is initially set to "pending". It also has an internal `[[PromiseResult]]` property that is initially set to undefined.

### Memory overhead and performance implications

- Each promise instance requires memory allocation to store its state, result and associated callbacks. This overhead can add up if you have a large number of promises.
- Promises use more memory than callbacks because they store additional state information
- JS engine need to manage lifecycle of promises
-
