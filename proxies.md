# Proxies in JavaScript

Proxies are a way for you to customize the behavior of objects in JavaScript.

For example, let's say you always wanna log when a property is accessed on an object. You can use a proxy for that.

It's a way to create a more "powerful" version of an object. Or to be fair, an accurate description would be that it's a way to create a "wrapper" around an object.

# Basic example

You create a proxy by calling `new Proxy(target, handler)`.

`target` is the object you want to wrap. `handler` is an object that let's you "trap" operations on the object. In simpler words: Let's you intercept operations on the object and decide what to do.

A silly example: You may not allow users to delete properties on an object. You can use a proxy to intercept the `deleteProperty` operation and throw an error if the user tries to delete a property.

Such a code would look like this:

```javascript
const target = {
  name: "John Doe",
};

const handler = {
  deleteProperty(target, prop) {
    throw new Error(`You can't delete the property ${prop}`);
  },
};

const proxy = new Proxy(target, handler);

delete proxy.name; // Error: You can't delete the property name
```

# The parameters of the handler

Each handler method receives different parameters.

For example, `deleteProperty` receives the `target` object and the `prop` property that the user is trying to delete. `target` is the object you're wrapping, and `prop` is the property the user is trying to delete.

`get` accepts `target`, `prop`, and `receiver`. `target` is the object you're wrapping, `prop` is the property the user is trying to access, and `receiver` is the object that the property is accessed on.

receiver can be confusing to understand, let me show you some code:

```javascript
const target = {
  name: "John Doe",
};

const handler = {
  get(target, prop, receiver) {
    console.log(receiver === proxy); // true
    return target[prop];
  },
};

const proxy = new Proxy(target, handler);
```

`proxy` is the object that the property is accessed on. So in this case, `receiver` is the `proxy` object.

# Handlers I find interesting

- `apply`: Traps function calls. For example, you can log when a function is called. And yes, because functions are objects, you can use them as targets for proxies.
- `get`: Traps property access.
- `set`: Traps property assignment.
- `deleteProperty`: Traps property deletion.
- `has`: Traps the `in` operator.

# `get` and destructuring

`get` trap of a proxy also works when you destructure an object.

```javascript
const target = {
  name: "John",
  age: 30,
  email: "john@example.com",
};

const handler = {
  get(target, property) {
    console.log(`Accessing property: ${property}`);
    return target[property];
  },
};

const proxy = new Proxy(target, handler);

const { name, age } = proxy;
// Output:
// Accessing property: name
// Accessing property: age

console.log(name); // Output: John
console.log(age); // Output: 30
```

# Examples

## Validation

```js
const handler = {
  set(target, property, value) {
    const isName = property === "name";
    const isAge = property === "age";
    if (isName && typeof value !== "string") {
      throw new TypeError("Name must be a string.");
    }

    if (isAge && typeof value !== "number") {
      throw new TypeError("Age must be a number.");
    }

    target[property] = value;
  },
};

const proxy = new Proxy({}, handler);
proxy.name = "  John Doe  "; // Works
proxy.age = "30"; // Throws an error
proxy.age = 30; // Works
proxy.name = 30; // Throws an error
```

## Logging

```js
const handler = {
  get(target, property) {
    console.log(`Accessing property: ${property}`);
    return target[property];
  },
  set(target, property, value) {
    console.log(`Setting property: ${property} = ${value}`);
    target[property] = value;
    return true;
  },
};

const proxy = new Proxy(target, handler);
proxy.name; // Logs: Accessing property: name
proxy.age = 30; // Logs: Setting property: age = 30
```
