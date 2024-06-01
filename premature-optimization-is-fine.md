# Premature optimization is the root of all evil

You have probably heard this quote before. It's from Donald Knuth. He's a computer scientist. He's also the author of the book "The Art of Computer Programming".

It's quite a famous quote, but it's often taken in the wrong way. People read this and become afraid of optimizing their code. They become afraid and shy away from learning about performance and writing performant code.

# Measure first

Yes, you should always measure before optimizing. Or, should you?

If you know clearly how something works under the hood and how to make it more performant, you don't need to measure first. Go ahead and make the code more performant. It's fine.

# The problem isn't measuring first

The problem is peoples' lack of knowledge. They don't understand how things work under the hood. I remember at my first job, a Senior engineer kept using `useMemo` all over the place. FYI `useMemo` is a performance API in React. It's used to memoize values. It always comes with a cost, not always a performance gain.

When I asked him about it, he said it's to stabilize functions. That makes zero sense. I clearly knew he was just writing code without understanding what he was doing. Maybe he wanted to look smart. Who knows?

The point here: When you don't understand how things work under the hood, you'll write code that's not performant. Another example is understanding how the browser works. What's the [critical rendering path](https://developer.mozilla.org/en-US/docs/Web/Performance/Critical_rendering_path)?

# What am I trying to say?

I'm not disagreeing with the quote. Yes, if you think something can be optimized but you don't fully understand it, don't optimize it. Measure first. Figure out where the bottleneck is. Then, make the code more performant.

# Learn your tools

Learn how your tools work. Understand their inner workings. This will help you write better code.

# The idea behind performance

A lot of the times, when we think about making our code more performant, we think about putting in more effort. Writing more code to make it faster.

If you want to make your code more performant, a simple question you can ask: "Can I do less?"

The goal is to look at what you're trying to achieve and see what's the shortest path to get there.

This is what performance is about. It's not always the same in every case.

# Don't neglect performance

Keep performance in mind. Don't neglect it. Small things compound over time. It's not something to dismiss.
