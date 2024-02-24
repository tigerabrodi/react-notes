# React Notes

My notes on various topics related to React.

# Virtual DOM

React optimizes webpage rendering through a concept called the Virtual DOM, which is a simplified, in-memory representation of the real Document Object Model (DOM).

Instead of directly modifying the real DOM every time there's a change in the UI, React first updates the Virtual DOM. It then performs a process called "diffing," where it compares the updated Virtual DOM with a snapshot of the Virtual DOM before the update.

This comparison identifies the exact changes made. Finally, React applies these specific changes to the real DOM, updating only what's necessary. This approach enhances performance by minimizing direct interactions with the real DOM, which are costly in terms of processing time.

## Why Directly Updating the Real DOM is Not Ideal

Updating the real DOM directly for every minor change is inefficient for several reasons.

### Performance Costs

The DOM is a complex, tree-structured representation of a webpage. Direct manipulations (like adding, deleting, or modifying elements) trigger a process called "re-rendering," where the browser recalculates layouts, repaints the UI, and applies any changes to the visible content. These operations are computationally expensive and can lead to noticeable performance issues, especially in complex applications.

### Reflows and Repaints

Direct updates can cause multiple "reflows" and "repaints." A reflow happens when the layout of a part of the webpage is recalculated (for example, changing the size of an element). A repaint occurs when changes are made that affect the visual appearance of an element (like color changes) but not its layout. **Frequent reflows and repaints are resource-intensive and can degrade the user experience, leading to sluggish interactions and animations.**

### Batching and Efficiency

The Virtual DOM allows React to batch multiple updates together. Instead of applying each change as it comes, React can accumulate changes to the Virtual DOM and then make all the updates at once. This batching minimizes the number of reflows and repaints, thereby enhancing efficiency and performance.

### Diffing Algorithm

React employs a diffing algorithm when comparing the Virtual DOM with the real DOM. This algorithm identifies the minimum number of changes needed to update the real DOM. By focusing on only the necessary updates, React minimizes direct manipulation of the DOM, reducing the performance impact of updates.

### Predictable State Management

By abstracting away the direct manipulation of the DOM, React provides a more predictable way of managing the state of the UI. Developers work with the state of components, and React takes care of translating those state changes into DOM updates. This separation of concerns simplifies the development process and makes it easier to reason about the behavior of the application.

# Render and Commit
