# Introduction

When browsing [Open Source code](https://github.com/TanStack/query/pull/1578/files), I came across `Object.defineProperty`.

It reminded me of Proxies. However, it provides more fine-grained control over the properties of an object.

# Example from MDN

```js
const object1 = {};

Object.defineProperty(object1, "property1", {
  value: 42,
  writable: false,
});

object1.property1 = 77;
// Throws an error in strict mode

console.log(object1.property1);
// Expected output: 42
```

In this example, we define property `property1` on `object1` with a value of `42`. We also set `writable` to `false`. This means that the property cannot be changed.

When we try to change the property, it throws an error in strict mode.

The object in the third argument of `Object.defineProperty` is called a property descriptor. It defines the behavior of the property. It can have the following keys:

- `value`: The value associated with the property.
- `writable`: If true, the property's value can be changed. Defaults to false.
- `enumerable`: If true, the property will be enumerated in for...in loops. Defaults to false.
- `configurable`: If true, the property's attributes can be changed and it can be deleted. Defaults to false.
- `get`: A function that serves as a getter for the property. Can not be used with `value` at the same time. Will also run when the property is destructured from the object.
- `set`: A function that serves as a setter for the property.

# Code in the PR

When reading the code in the PR, it seems like whenever we destructure properties from `useQuery` or other hooks from React Query, they automatically get tracked. So, the component re-renders only if the properties we destructure change.

```ts
if (!shallowEqualObjects(result, this.currentResult)) {
  this.currentResult = result;

  if (this.options.notifyOnChangeProps === "tracked") {
    const addTrackedProps = (prop: keyof QueryObserverResult) => {
      if (!this.trackedProps.includes(prop)) {
        this.trackedProps.push(prop);
      }
    };
    this.trackedCurrentResult = {} as QueryObserverResult<TData, TError>;

    Object.keys(result).forEach((key) => {
      Object.defineProperty(this.trackedCurrentResult, key, {
        configurable: false,
        enumerable: true,
        get() {
          // Add the property to the tracked props
          addTrackedProps(key as keyof QueryObserverResult);
          return result[key as keyof QueryObserverResult];
        },
      });
    });
  }
}
```

It's a part of the QueryObserver class, which I assume is a class that React Query uses internally to observe usage and do internal logic when that happens.

Looking at the code today, it seems like it changed a bit.

```ts
  trackResult(
    result: QueryObserverResult<TData, TError>,
    onPropTracked?: (key: keyof QueryObserverResult) => void,
  ): QueryObserverResult<TData, TError> {
    const trackedResult = {} as QueryObserverResult<TData, TError>

    Object.keys(result).forEach((key) => {
      Object.defineProperty(trackedResult, key, {
        configurable: false,
        enumerable: true,
        get: () => {
          this.trackProp(key as keyof QueryObserverResult)
          onPropTracked?.(key as keyof QueryObserverResult)
          return result[key as keyof QueryObserverResult]
        },
      })
    })

    return trackedResult
  }

  trackProp(key: keyof QueryObserverResult) {
    this.#trackedProps.add(key)
  }
```

`#trackedProps` is a private field in the class. It's a `Set` that keeps track of the properties that have been tracked.
