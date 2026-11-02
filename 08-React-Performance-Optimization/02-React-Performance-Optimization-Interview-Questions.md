# React Performance Optimization – Interview Questions and Answers

> **Chapter 8 – React Interview Questions and Answers Repository**  
> Interview-ready answers from fundamentals to advanced, tricky, and scenario-based questions. Answers are written in a way a candidate can explain them directly to an interviewer.

## Table of Contents

### Fundamentals
1. [What is performance optimization in React?](#1-what-is-performance-optimization-in-react)
2. [Why is performance optimization important?](#2-why-is-performance-optimization-important)
3. [What is rendering in React?](#3-what-is-rendering-in-react)
4. [What is a re-render?](#4-what-is-a-re-render)
5. [Does every re-render update the DOM?](#5-does-every-re-render-update-the-dom)
6. [What are the render phase and commit phase?](#6-what-are-the-render-phase-and-commit-phase)
7. [What is reconciliation?](#7-what-is-reconciliation)
8. [What is the Virtual DOM?](#8-what-is-the-virtual-dom)
9. [What causes a React component to re-render?](#9-what-causes-a-react-component-to-re-render)
10. [What is an unnecessary re-render?](#10-what-is-an-unnecessary-re-render)

### Diagnosing Performance
11. [How do you identify a performance problem in React?](#11-how-do-you-identify-a-performance-problem-in-react)
12. [What is React Developer Tools Profiler?](#12-what-is-react-developer-tools-profiler)
13. [What is a flamegraph?](#13-what-is-a-flamegraph)
14. [Should we optimize every component?](#14-should-we-optimize-every-component)
15. [What is the difference between render time and page load time?](#15-what-is-the-difference-between-render-time-and-page-load-time)

### Memoization
16. [What is memoization?](#16-what-is-memoization)
17. [What is React.memo?](#17-what-is-reactmemo)
18. [How does React.memo work?](#18-how-does-reactmemo-work)
19. [When should you use React.memo?](#19-when-should-you-use-reactmemo)
20. [When should you avoid React.memo?](#20-when-should-you-avoid-reactmemo)
21. [What is the difference between React.memo and useMemo?](#21-what-is-the-difference-between-reactmemo-and-usememo)
22. [What is useMemo?](#22-what-is-usememo)
23. [What are the useMemo dependencies?](#23-what-are-the-usememo-dependencies)
24. [What happens if the dependency array is empty?](#24-what-happens-if-the-dependency-array-is-empty)
25. [What happens if we omit the dependency array?](#25-what-happens-if-we-omit-the-dependency-array)
26. [What is useCallback?](#26-what-is-usecallback)
27. [Does useCallback prevent a function from executing?](#27-does-usecallback-prevent-a-function-from-executing)
28. [What is the difference between useMemo and useCallback?](#28-what-is-the-difference-between-usememo-and-usecallback)
29. [What is referential equality?](#29-what-is-referential-equality)
30. [What is shallow comparison?](#30-what-is-shallow-comparison)
31. [Why can inline objects break memoization?](#31-why-can-inline-objects-break-memoization)
32. [Why can inline functions cause child re-renders?](#32-why-can-inline-functions-cause-child-re-renders)
33. [Can React.memo prevent a component from rendering when its state changes?](#33-can-reactmemo-prevent-a-component-from-rendering-when-its-state-changes)
34. [Can React.memo prevent context-driven updates?](#34-can-reactmemo-prevent-context-driven-updates)
35. [What are stale closures in memoized callbacks?](#35-what-are-stale-closures-in-memoized-callbacks)
36. [What are the disadvantages of memoization?](#36-what-are-the-disadvantages-of-memoization)

### Component and State Architecture
37. [How does state placement affect performance?](#37-how-does-state-placement-affect-performance)
38. [What is state colocation?](#38-what-is-state-colocation)
39. [How can you reduce unnecessary Context API re-renders?](#39-how-can-you-reduce-unnecessary-context-api-re-renders)
40. [Why should large contexts sometimes be split?](#40-why-should-large-contexts-sometimes-be-split)
41. [Should derived data be stored in state?](#41-should-derived-data-be-stored-in-state)

### Lists and Loading
42. [How do keys affect list performance and correctness?](#42-how-do-keys-affect-list-performance-and-correctness)
43. [Why should array indexes not be used as keys in dynamic lists?](#43-why-should-array-indexes-not-be-used-as-keys-in-dynamic-lists)
44. [What is list virtualization?](#44-what-is-list-virtualization)
45. [What is the difference between pagination and virtualization?](#45-what-is-the-difference-between-pagination-and-virtualization)
46. [What is code splitting?](#46-what-is-code-splitting)
47. [What is lazy loading in React?](#47-what-is-lazy-loading-in-react)
48. [What are React.lazy and Suspense?](#48-what-are-reactlazy-and-suspense)
49. [What is route-based code splitting?](#49-what-is-route-based-code-splitting)
50. [What happens if a lazy-loaded component fails to load?](#50-what-happens-if-a-lazy-loaded-component-fails-to-load)

### Concurrent UI and Event Optimization
51. [What is useTransition?](#51-what-is-usetransition)
52. [What does isPending represent?](#52-what-does-ispending-represent)
53. [Can a controlled text input be updated inside startTransition?](#53-can-a-controlled-text-input-be-updated-inside-starttransition)
54. [What is useDeferredValue?](#54-what-is-usedeferredvalue)
55. [What is the difference between useTransition and useDeferredValue?](#55-what-is-the-difference-between-usetransition-and-usedeferredvalue)
56. [What is debouncing?](#56-what-is-debouncing)
57. [What is throttling?](#57-what-is-throttling)
58. [What is the difference between debouncing and throttling?](#58-what-is-the-difference-between-debouncing-and-throttling)

### Network, Bundles, and Metrics
59. [How do you optimize API calls in React?](#59-how-do-you-optimize-api-calls-in-react)
60. [What is a request waterfall?](#60-what-is-a-request-waterfall)
61. [How can independent API requests be executed in parallel?](#61-how-can-independent-api-requests-be-executed-in-parallel)
62. [How do you prevent duplicate API requests?](#62-how-do-you-prevent-duplicate-api-requests)
63. [How do you optimize images in a React application?](#63-how-do-you-optimize-images-in-a-react-application)
64. [What is bundle size and why does it matter?](#64-what-is-bundle-size-and-why-does-it-matter)
65. [How do you analyze a production bundle?](#65-how-do-you-analyze-a-production-bundle)
66. [What are Core Web Vitals?](#66-what-are-core-web-vitals)
67. [What are LCP, INP, and CLS?](#67-what-are-lcp-inp-and-cls)
68. [Is React Profiler enough to measure application performance?](#68-is-react-profiler-enough-to-measure-application-performance)

### Advanced and Tricky Questions
69. [Does useMemo guarantee that a value will never be recalculated?](#69-does-usememo-guarantee-that-a-value-will-never-be-recalculated)
70. [Does useCallback make an application faster automatically?](#70-does-usecallback-make-an-application-faster-automatically)
71. [Can memoization make performance worse?](#71-can-memoization-make-performance-worse)
72. [Why is a component rendering even though its props look identical?](#72-why-is-a-component-rendering-even-though-its-props-look-identical)
73. [Why does React render a component twice in development?](#73-why-does-react-render-a-component-twice-in-development)
74. [Does React.StrictMode cause double rendering in production?](#74-does-reactstrictmode-cause-double-rendering-in-production)
75. [What is React Compiler?](#75-what-is-react-compiler)
76. [Does React Compiler make useMemo and useCallback obsolete?](#76-does-react-compiler-make-usememo-and-usecallback-obsolete)
77. [What is the difference between concurrent rendering and parallel rendering?](#77-what-is-the-difference-between-concurrent-rendering-and-parallel-rendering)
78. [Does useTransition run work on another thread?](#78-does-usetransition-run-work-on-another-thread)
79. [Can virtualization improve API response time?](#79-can-virtualization-improve-api-response-time)
80. [Can React.memo stop a parent from rendering?](#80-can-reactmemo-stop-a-parent-from-rendering)

### Scenario-based Questions
81. [A search input becomes slow with thousands of records. How would you optimize it?](#81-a-search-input-becomes-slow-with-thousands-of-records-how-would-you-optimize-it)
82. [A child component re-renders whenever its parent updates. How would you investigate it?](#82-a-child-component-re-renders-whenever-its-parent-updates-how-would-you-investigate-it)
83. [A dashboard has many Context consumers. What would you do?](#83-a-dashboard-has-many-context-consumers-what-would-you-do)
84. [A page renders 20,000 rows. What would you recommend?](#84-a-page-renders-20000-rows-what-would-you-recommend)
85. [The initial page load is slow, but interactions are fast. What would you investigate?](#85-the-initial-page-load-is-slow-but-interactions-are-fast-what-would-you-investigate)
86. [Navigation to an admin page is slow because it contains a charting library. What would you do?](#86-navigation-to-an-admin-page-is-slow-because-it-contains-a-charting-library-what-would-you-do)
87. [A page feels slow even though React Profiler shows short render times. Why?](#87-a-page-feels-slow-even-though-react-profiler-shows-short-render-times-why)
88. [An API search displays results from an older query after a newer query. How would you fix it?](#88-an-api-search-displays-results-from-an-older-query-after-a-newer-query-how-would-you-fix-it)
89. [How would you optimize a large e-commerce product listing?](#89-how-would-you-optimize-a-large-e-commerce-product-listing)
90. [How do you prove that an optimization actually worked?](#90-how-do-you-prove-that-an-optimization-actually-worked)

---

## Fundamentals

### 1. What is performance optimization in React?

**Answer:**

React performance optimization is the process of making an application render efficiently, respond quickly to user interactions, and load its content without unnecessary delays. It includes reducing avoidable rendering work, improving expensive calculations, optimizing large lists, splitting JavaScript bundles, and improving network and asset loading.

I do not optimize every component blindly. I first measure the application, identify the actual bottleneck, apply the appropriate technique, and then measure again to confirm the improvement.

### 2. Why is performance optimization important?

**Answer:**

Performance is important because it directly affects user experience. A slow application makes users wait, interrupts their workflow, and can cause them to leave the page. It is especially important on mobile devices and slower networks.

Good performance also helps applications scale as data and features increase. I consider rendering, JavaScript execution, network requests, images, and user-perceived loading as parts of overall performance.

### 3. What is rendering in React?

**Answer:**

Rendering is the process in which React calls a component and calculates what the UI should look like based on its current props, state, and context. React then compares the new result with the previous one and determines what changes need to be committed to the DOM.

Rendering does not mean that the browser DOM is completely recreated every time. React can reuse existing elements and update only what is necessary.

### 4. What is a re-render?

**Answer:**

A re-render means React calls a component again to calculate its next UI. It commonly happens when the component's state changes, its parent renders, or a context value it consumes changes.

A re-render is not automatically bad. It becomes a performance concern when it causes expensive work that could have been avoided.

### 5. Does every re-render update the DOM?

**Answer:**

No. A re-render means React recalculates the component's output. React then reconciles the new result with the previous one. If the output is effectively unchanged, React may not need to make DOM mutations.

This is why component rendering and actual DOM updates are two different concepts.

### 6. What are the render phase and commit phase?

**Answer:**

The render phase is when React calls components and calculates the next UI. It should remain pure because React may repeat, pause, or abandon rendering work.

The commit phase is when React applies the necessary changes to the DOM and performs commit-related operations. I keep side effects out of the component body and use the appropriate effect or external-system integration.

### 7. What is reconciliation?

**Answer:**

Reconciliation is React's process of comparing the previous element tree with the next element tree to determine what needs to change. React uses element types, positions, and keys to understand whether elements can be reused or need to be replaced.

Stable keys are particularly important for lists because they help React preserve the identity of items when the list changes.

### 8. What is the Virtual DOM?

**Answer:**

The Virtual DOM is a common term for React's in-memory representation of the UI. Components produce React elements, and React uses its internal tree and reconciliation process to determine the required updates.

It is not accurate to say that React recreates the entire real DOM on every render. React calculates changes and commits only the necessary host updates.

### 9. What causes a React component to re-render?

**Answer:**

The common causes are a change in the component's own state, a parent component rendering, or a change in a context value consumed by the component. External store subscriptions and framework updates can also schedule renders.

When investigating a re-render, I check which input changed instead of assuming that the component itself has a problem.

### 10. What is an unnecessary re-render?

**Answer:**

An unnecessary re-render is a render that repeats work even though the component's relevant data and output do not need to change. For example, a costly child may render again because its parent creates a new callback or object on every render.

I use profiling to confirm whether that work is actually expensive before applying memoization or changing the component structure.

## Diagnosing Performance

### 11. How do you identify a performance problem in React?

**Answer:**

I first reproduce the slow interaction consistently. Then I use React Developer Tools Profiler to inspect component rendering and the browser Performance and Network panels to inspect JavaScript execution, layout, painting, and requests.

After identifying the bottleneck, I apply one targeted change and repeat the same test. This helps me verify that the optimization improved the actual problem.

### 12. What is React Developer Tools Profiler?

**Answer:**

The React Developer Tools Profiler is a debugging tool that records React rendering activity. It helps identify which components rendered, how much time they took, and which commits contained expensive work.

I use it to investigate unnecessary renders and expensive component trees. I combine it with browser performance tools because React Profiler does not measure every aspect of page loading or network performance.

### 13. What is a flamegraph?

**Answer:**

A flamegraph is a visual representation of component rendering work in the Profiler. It shows components involved in a commit and helps identify where rendering time is concentrated.

I use it to locate expensive components or large subtrees. I do not assume that every component shown is a problem; I focus on components with meaningful cost.

### 14. Should we optimize every component?

**Answer:**

No. Optimizing every component can add unnecessary complexity. Small components that render quickly usually do not need memoization.

I prefer to measure first and optimize components that are expensive or render frequently with unchanged inputs. Maintainability is also important, so I avoid adding optimization code without a clear benefit.

### 15. What is the difference between render time and page load time?

**Answer:**

Render time refers to the time spent calculating or committing UI updates. Page load time is broader and includes network requests, JavaScript download and parsing, asset loading, rendering, and browser work.

A React component can render quickly while the page still loads slowly because the JavaScript bundle or images are large. That is why I use both React-specific and browser-level performance tools.

## Memoization

### 16. What is memoization?

**Answer:**

Memoization is a technique where a previous result is stored and reused when the relevant inputs have not changed. In React, memoization can reduce repeated calculations, preserve function or object references, and skip some component renders.

React provides APIs such as `memo`, `useMemo`, and `useCallback`. I use them selectively because caching and comparison also have costs.

### 17. What is React.memo?

**Answer:**

`React.memo` is a higher-order component API that creates a memoized version of a component. By default, React compares its props using `Object.is` for each prop and can skip rendering the component when the props are unchanged.

It is useful for expensive child components that frequently receive the same props. It does not prevent updates caused by the component's own state or consumed context.

### 18. How does React.memo work?

**Answer:**

When a parent renders, React normally considers rendering its children. If a child is wrapped in `memo`, React compares the previous and next props. If the props are equal according to the comparison, React can reuse the previous rendered result.

The default comparison is shallow and identity-based for each prop. New object and function references can therefore cause the memoized component to render again.

### 19. When should you use React.memo?

**Answer:**

I use `React.memo` when a component is expensive to render, receives the same props across many parent renders, and profiling shows that skipping those renders would help.

It is especially useful for complex cards, rows, charts, or repeated UI components. I also make sure the props are stable; otherwise memoization may not provide the expected benefit.

### 20. When should you avoid React.memo?

**Answer:**

I avoid it when a component is cheap to render, its props change frequently, or profiling shows no meaningful performance issue. The comparison itself has a cost, and memoization adds complexity.

I also avoid using it as a substitute for good state placement or efficient component design.

### 21. What is the difference between React.memo and useMemo?

**Answer:**

`React.memo` memoizes a component based on its props and can skip rendering that component when the props are unchanged.

`useMemo` memoizes the result of a calculation inside a component and reuses that value while its dependencies remain unchanged.

So, `memo` is primarily for component rendering, while `useMemo` is for calculated values.

### 22. What is useMemo?

**Answer:**

`useMemo` is a React Hook that caches the result of a calculation between renders. It accepts a calculation function and a dependency array.

```jsx
const filteredItems = useMemo(() => {
  return items.filter((item) => item.active);
}, [items]);
```

React recalculates the value when a dependency changes. I use it for expensive calculations or when a stable object reference is useful, not for every variable.

### 23. What are the useMemo dependencies?

**Answer:**

The dependency array contains the reactive values used by the calculation. React compares each dependency with its previous value using `Object.is`.

If any dependency changes, React recalculates the value. Every reactive value used by the calculation should be included so that the result does not become stale.

### 24. What happens if the dependency array is empty?

**Answer:**

An empty dependency array means the calculation has no reactive dependencies. React can reuse the cached result across normal re-renders of that component instance.

However, I do not treat this as a permanent storage guarantee. React may discard a memoized value in certain circumstances, so application correctness must not depend on the cache.

### 25. What happens if we omit the dependency array?

**Answer:**

If I omit the dependency array, React runs the calculation on every render. In that case, `useMemo` does not provide the usual dependency-based caching benefit.

I include the dependency array that represents the values used by the calculation.

### 26. What is useCallback?

**Answer:**

`useCallback` is a React Hook that caches a function definition between renders while its dependencies remain unchanged.

```jsx
const handleSave = useCallback(() => {
  saveRecord(recordId);
}, [recordId]);
```

It is useful when passing callbacks to memoized child components or when a stable function reference is needed by another Hook.

### 27. Does useCallback prevent a function from executing?

**Answer:**

No. `useCallback` does not prevent a function from executing. It preserves the function reference between renders when dependencies remain unchanged.

The function still runs whenever it is called. It is different from `useMemo`, which caches the result of a calculation.

### 28. What is the difference between useMemo and useCallback?

**Answer:**

`useMemo` caches a calculated value, while `useCallback` caches a function reference.

For example, I use `useMemo` to cache a filtered list and `useCallback` to preserve an event handler passed to a memoized child.

Conceptually, `useCallback(fn, deps)` is similar to `useMemo(() => fn, deps)`.

### 29. What is referential equality?

**Answer:**

Referential equality checks whether two reference values point to the same object or function. In JavaScript, two objects with identical properties are still different references if they were created separately.

React uses identity comparisons in memoization and Hook dependency checks. This is why creating a new object or callback on every render can affect memoization.

### 30. What is shallow comparison?

**Answer:**

Shallow comparison checks the top-level values or references rather than recursively comparing every nested property.

For example, two objects may contain the same nested data but still fail a shallow comparison because the nested object references are different. React's default `memo` comparison compares each prop using `Object.is`.

### 31. Why can inline objects break memoization?

**Answer:**

An inline object creates a new reference every time the parent renders.

```jsx
<Child options={{ compact: true }} />
```

Even though the object has the same content, its reference changes. A memoized child sees the prop as changed. If stable identity is genuinely needed, I can use `useMemo` or move a constant object outside the component.

### 32. Why can inline functions cause child re-renders?

**Answer:**

An inline function creates a new function reference on every render.

```jsx
<Child onSave={() => save(id)} />
```

If the child is wrapped in `React.memo`, the changed callback reference can cause it to render again. I can use `useCallback` when preserving the function identity is useful.

### 33. Can React.memo prevent a component from rendering when its state changes?

**Answer:**

No. `React.memo` only helps skip rendering when the component's props are unchanged. If the component's own state changes, it still needs to render to reflect that update.

Memoization does not block updates that the component itself depends on.

### 34. Can React.memo prevent context-driven updates?

**Answer:**

No. If a component consumes a context and the context value it reads changes, the component can re-render even when it is wrapped in `React.memo`.

To reduce unnecessary context updates, I can split contexts, stabilize provider values, or redesign how frequently changing data is distributed.

### 35. What are stale closures in memoized callbacks?

**Answer:**

A stale closure occurs when a callback continues to use values captured from an older render because its dependencies were not updated correctly.

For example, if a callback uses `count` but has an empty dependency array, it may keep referring to an old value. I include the required dependencies or use a functional state update when appropriate.

### 36. What are the disadvantages of memoization?

**Answer:**

Memoization adds comparison and cache-management work, consumes memory, and increases code complexity. Incorrect dependency arrays can also create stale values or stale closures.

It can even make performance worse when the calculation is cheap or the props change on every render. I use memoization only when it solves a measured problem.

## Component and State Architecture

### 37. How does state placement affect performance?

**Answer:**

State placement determines which part of the component tree is updated when the state changes. If I keep frequently changing state in a high-level parent, a large subtree may render whenever that state updates.

I place state as close as possible to the components that need it. This reduces the number of unrelated components affected by an update.

### 38. What is state colocation?

**Answer:**

State colocation means keeping state near the components that use it instead of placing all state in a common root component.

For example, if only a search box needs its input value, I can keep that state inside the search component. This makes the component tree easier to maintain and can reduce unrelated re-renders.

### 39. How can you reduce unnecessary Context API re-renders?

**Answer:**

I can stabilize the provider value with `useMemo`, stabilize functions included in that value with `useCallback` when necessary, and split unrelated values into separate contexts.

I also avoid placing rapidly changing data into a context consumed by the entire application unless that sharing is required. These changes reduce unrelated updates but do not stop consumers from updating when their context value genuinely changes.

### 40. Why should large contexts sometimes be split?

**Answer:**

A context consumer updates when the context value it reads changes. If authentication, theme, and frequently changing dashboard data are combined into one provider value, an update to one part can affect consumers that only need another part.

Splitting contexts by responsibility and update frequency can reduce unnecessary updates and make the application easier to understand.

### 41. Should derived data be stored in state?

**Answer:**

Usually, no. If a value can be calculated from existing props or state, I prefer deriving it during rendering rather than storing a duplicate copy in state.

This avoids synchronization problems. If the calculation is expensive, I can consider `useMemo` after measuring the need.

## Lists and Loading

### 42. How do keys affect list performance and correctness?

**Answer:**

Keys help React identify which list item corresponds to which element between renders. Stable keys allow React to preserve the correct component identity and update, insert, or remove items accurately.

I use a stable unique identifier from the data, such as `product.id`, rather than generating a new key during rendering.

### 43. Why should array indexes not be used as keys in dynamic lists?

**Answer:**

An array index represents an item's current position, not its identity. If an item is inserted, deleted, or reordered, the same index may refer to a different item.

This can cause React to preserve the wrong component state or update the wrong DOM element. For dynamic lists, I use stable IDs.

### 44. What is list virtualization?

**Answer:**

List virtualization is a technique where only the visible items in a large list, plus a small buffer, are rendered. The remaining items are not mounted until the user scrolls to them.

It reduces DOM nodes, memory use, and layout work. It is useful for large tables, feeds, logs, and product catalogs.

### 45. What is the difference between pagination and virtualization?

**Answer:**

Pagination divides data into separate pages and usually loads or displays one page at a time. Virtualization keeps a larger logical list but renders only the items currently visible in the viewport.

Pagination reduces the amount of data shown or fetched at once. Virtualization reduces the number of mounted DOM elements. They can also be used together.

### 46. What is code splitting?

**Answer:**

Code splitting divides an application's JavaScript into smaller bundles or chunks. Instead of loading all feature code initially, the application can load some code only when a user visits a route or opens a feature.

This can reduce the initial JavaScript payload and improve loading experience. I usually consider route-based splitting for large applications.

### 47. What is lazy loading in React?

**Answer:**

Lazy loading means loading a component or resource only when it is needed rather than loading everything at the beginning.

React provides `lazy` for loading component code dynamically, and browser features such as image `loading="lazy"` can defer off-screen images. Lazy loading can reduce initial work, but it needs suitable loading and error states.

### 48. What are React.lazy and Suspense?

**Answer:**

`React.lazy` lets me dynamically import a component. `Suspense` provides a fallback UI while that component's code is loading.

```jsx
const Dashboard = lazy(() => import("./Dashboard"));

<Suspense fallback={<p>Loading...</p>}>
  <Dashboard />
</Suspense>
```

This is commonly used for route-level code splitting. For data fetching, Suspense behavior depends on the framework or library.

### 49. What is route-based code splitting?

**Answer:**

Route-based code splitting means loading the JavaScript for a page only when the user navigates to that route.

For example, the public home page does not need to download the entire admin dashboard and charting code. I can use dynamic imports with `React.lazy` and `Suspense` around route content.

### 50. What happens if a lazy-loaded component fails to load?

**Answer:**

If the dynamic import rejects, the error is not handled by the Suspense fallback alone. Suspense handles the pending loading state, while an Error Boundary or route-level error handling should handle the failure.

I provide a useful error message and a retry or navigation option where appropriate.

## Concurrent UI and Event Optimization

### 51. What is useTransition?

**Answer:**

`useTransition` is a React Hook that lets me mark certain state updates as non-urgent transitions. React can prioritize urgent updates, such as typing into an input, over expensive UI updates.

It returns `isPending` and `startTransition`. I use it when a user interaction triggers a large UI update that should not block more urgent interactions.

### 52. What does isPending represent?

**Answer:**

`isPending` indicates whether a transition started by that Hook is pending. I can use it to display a subtle loading indicator or communicate that results are being updated.

It does not mean that an API request is automatically running. A transition is about the priority of React state updates.

### 53. Can a controlled text input be updated inside startTransition?

**Answer:**

No, I should not use a transition to control the immediate value of a text input. The input's value is urgent because the user expects each keystroke to appear immediately.

I update the input state normally and mark the expensive result-view update as a transition.

### 54. What is useDeferredValue?

**Answer:**

`useDeferredValue` lets a component use a deferred version of a value. React can keep showing UI based on the previous value while preparing an update with the latest value.

It is useful when an input should update immediately but a large result view can update with lower priority.

### 55. What is the difference between useTransition and useDeferredValue?

**Answer:**

`useTransition` lets me mark a state update as non-urgent using `startTransition`. `useDeferredValue` lets me defer the value used by part of the UI without directly controlling the state update that produced it.

I use `useTransition` when I control the state update. I use `useDeferredValue` when I want a consumer of a value to lag behind temporarily.

### 56. What is debouncing?

**Answer:**

Debouncing delays a function until a specified period has passed without another event. For example, in a search box, I can wait until the user stops typing for 300 or 400 milliseconds before sending an API request.

This reduces unnecessary requests. It is a JavaScript timing technique, not a React Hook.

### 57. What is throttling?

**Answer:**

Throttling limits how often a function can run during a period of repeated events. For example, I can throttle a scroll handler so it runs at most once every 100 milliseconds.

It is useful when I need regular updates during continuous events but do not need to process every event.

### 58. What is the difference between debouncing and throttling?

**Answer:**

Debouncing waits until events stop for a configured delay and then runs the function. Throttling limits the maximum frequency at which the function runs while events continue.

I commonly use debouncing for search requests and throttling for scroll or resize event handling.

## Network, Bundles, and Metrics

### 59. How do you optimize API calls in React?

**Answer:**

I avoid unnecessary and duplicate requests, request only the data the page needs, and use pagination for large datasets. I also consider caching, request cancellation, retries, and parallel requests where appropriate.

For complex server-state management, a library such as TanStack Query can help with caching and request deduplication. I also provide loading, error, and empty states.

### 60. What is a request waterfall?

**Answer:**

A request waterfall happens when requests are made sequentially even though some of them could run independently. Each request waits for the previous request to finish, increasing the total waiting time.

I inspect the dependency between requests and run independent requests in parallel.

### 61. How can independent API requests be executed in parallel?

**Answer:**

I use `Promise.all` when all results are required before continuing.

```js
const [user, courses] = await Promise.all([
  getUser(),
  getCourses(),
]);
```

This starts the independent operations together rather than waiting for each one sequentially. If partial success is useful, I consider `Promise.allSettled`.

### 62. How do you prevent duplicate API requests?

**Answer:**

I first identify why the same request is triggered more than once. I keep data ownership clear, avoid unnecessary effects, and use caching or request deduplication where appropriate.

I also handle effect cleanup and understand the extra development checks performed by Strict Mode. I do not simply disable Strict Mode to hide duplicate-request issues.

### 63. How do you optimize images in a React application?

**Answer:**

I use appropriately sized and compressed images, modern formats where supported, and responsive image techniques. I specify image dimensions to reduce layout shifts and lazy-load images that are below the fold.

I avoid lazy-loading the main visible hero image without a reason because it may be the Largest Contentful Paint element.

### 64. What is bundle size and why does it matter?

**Answer:**

Bundle size is the amount of JavaScript and other assets the browser needs to download for the application or a particular chunk. Large bundles can increase download, parsing, compilation, and execution time.

I reduce unnecessary dependencies, split large routes and features, and inspect production output to understand what is included in the initial bundle.

### 65. How do you analyze a production bundle?

**Answer:**

I create a production build and inspect the generated chunks using the bundler's output and an appropriate bundle analyzer. I look for large dependencies, duplicated packages, unexpectedly large entry chunks, and features that should be loaded separately.

I verify that code splitting works in the actual production build rather than assuming that dynamic imports automatically produced the desired result.

### 66. What are Core Web Vitals?

**Answer:**

Core Web Vitals are Google's user-experience metrics that focus on loading, interactivity, and visual stability. The current Core Web Vitals are LCP, INP, and CLS.

They help measure real user experience beyond React component rendering time.

### 67. What are LCP, INP, and CLS?

**Answer:**

LCP stands for Largest Contentful Paint and measures loading performance. INP stands for Interaction to Next Paint and measures interaction responsiveness. CLS stands for Cumulative Layout Shift and measures unexpected visual movement.

I use these metrics to understand the experience users have on the whole page, not just inside React components.

### 68. Is React Profiler enough to measure application performance?

**Answer:**

No. React Profiler is useful for measuring React component rendering work, but it does not cover every performance factor.

I also use the browser Performance panel, Network panel, asset inspection, and Web Vitals. This helps me distinguish React rendering problems from network, JavaScript, layout, or image-loading problems.

## Advanced and Tricky Questions

### 69. Does useMemo guarantee that a value will never be recalculated?

**Answer:**

No. `useMemo` is a performance optimization, not a semantic guarantee or permanent storage mechanism. React normally reuses the cached value while dependencies remain unchanged, but it may discard the cache in certain circumstances.

Therefore, my code must remain correct even if React recalculates the value.

### 70. Does useCallback make an application faster automatically?

**Answer:**

No. `useCallback` preserves a function reference, but it does not make the function body execute faster. It is useful when stable identity prevents unnecessary child renders or helps with Hook dependencies.

If the function is passed to a component that is not memoized and there is no other identity requirement, `useCallback` may not provide a benefit.

### 71. Can memoization make performance worse?

**Answer:**

Yes. Memoization adds dependency comparisons, cache management, and sometimes custom comparison work. If the calculation is cheap or the props change frequently, the optimization can cost more than simply rendering again.

That is why I measure first and use memoization only when it provides a meaningful benefit.

### 72. Why is a component rendering even though its props look identical?

**Answer:**

The props may look identical in the console but have different object or function references. React's default memo comparison is based on `Object.is` for each prop, not a deep comparison of object contents.

The component may also be rendering because its own state or consumed context changed, or because it is not memoized.

### 73. Why does React render a component twice in development?

**Answer:**

When Strict Mode is enabled, React performs additional development-only checks to help reveal impure rendering and unsafe side effects. This can make some render logic appear to run more than once.

It is a development diagnostic behavior and should not be treated as a production rendering guarantee. I make rendering pure and effects correctly cleaned up rather than relying on a specific render count.

### 74. Does React.StrictMode cause double rendering in production?

**Answer:**

No. The extra Strict Mode checks that cause repeated rendering behavior are development-only. They are intended to expose bugs early.

I keep Strict Mode enabled during development because it helps identify code that is not safe under React's rendering model.

### 75. What is React Compiler?

**Answer:**

React Compiler is a build-time compiler that analyzes React component and Hook code and can automatically apply memoization optimizations when it can determine that doing so is safe.

Its goal is to reduce the need for developers to manually add memoization everywhere. It does not replace performance measurement or fix unrelated issues such as slow APIs, large images, and inefficient algorithms.

### 76. Does React Compiler make useMemo and useCallback obsolete?

**Answer:**

Not as a blanket statement. React Compiler can automatically handle many memoization cases in supported and correctly configured projects, reducing the need for manual memoization.

However, the exact behavior depends on compiler support and configuration. Developers still need to understand the Hooks, write correct code, and follow the project's React Compiler guidance.

### 77. What is the difference between concurrent rendering and parallel rendering?

**Answer:**

Concurrent rendering means React can work on rendering updates in an interruptible and prioritized way. It can pause or abandon lower-priority rendering work to respond to more urgent updates.

It does not mean React automatically runs component rendering on multiple CPU threads. Concurrent scheduling and parallel execution are different concepts.

### 78. Does useTransition run work on another thread?

**Answer:**

No. `useTransition` does not move JavaScript work to a Web Worker or another CPU thread. It marks React state updates as non-urgent so React can schedule and interrupt rendering work appropriately.

If I have CPU-intensive computation that blocks the main thread, I may need a Web Worker or a more efficient algorithm.

### 79. Can virtualization improve API response time?

**Answer:**

No. Virtualization reduces the number of rendered DOM elements; it does not make the server respond faster.

It can make the interface smoother after data is available. To improve API response time, I would investigate the backend, query, payload size, network, caching, or pagination.

### 80. Can React.memo stop a parent from rendering?

**Answer:**

No. `React.memo` applies to the component that is wrapped with it. It can allow that child to skip rendering when its props are unchanged, but it does not prevent the parent from rendering.

It is a way to isolate rendering work at component boundaries.

## Scenario-based Questions

### 81. A search input becomes slow with thousands of records. How would you optimize it?

**Answer:**

First, I would profile the interaction to determine whether filtering, rendering the result list, or API calls are causing the delay.

If filtering is expensive, I would improve the algorithm and consider `useMemo` when the same inputs are reused. I could use `useDeferredValue` or `useTransition` to keep the input responsive while the result view updates. For API-based search, I would debounce requests and cancel or ignore stale responses. If there are thousands of visible results, I would use pagination or virtualization.

### 82. A child component re-renders whenever its parent updates. How would you investigate it?

**Answer:**

I would first use React Profiler to confirm that the child render is actually expensive. Then I would inspect its props and check whether the parent creates new objects, arrays, or functions on every render.

If the child is expensive and its props can remain stable, I would consider `React.memo`, `useMemo`, or `useCallback`. I would also check whether the child has its own state or consumes a changing context, because memoization does not block those updates.

### 83. A dashboard has many Context consumers. What would you do?

**Answer:**

I would inspect which context values change and how many consumers are affected. If unrelated values are combined into one provider, I would split the context by responsibility or update frequency.

I would also stabilize provider values where useful and keep frequently changing local state close to the components that use it. I would verify the improvement with profiling.

### 84. A page renders 20,000 rows. What would you recommend?

**Answer:**

I would not render all 20,000 rows into the DOM at once. I would consider list virtualization so only visible rows and a small buffer are mounted.

I would also use stable keys, keep row components focused, and check whether server-side pagination or filtering is more appropriate. Virtualization reduces DOM work, while pagination can reduce the amount of data fetched and processed.

### 85. The initial page load is slow, but interactions are fast. What would you investigate?

**Answer:**

I would inspect the Network panel and production bundle. I would check the size of the initial JavaScript, images, fonts, API requests, and the largest visible content element.

Potential improvements include route-based code splitting, reducing unused dependencies, optimizing the hero image, caching static assets, and prioritizing critical resources. I would also measure LCP and other Web Vitals.

### 86. Navigation to an admin page is slow because it contains a charting library. What would you do?

**Answer:**

I would check whether the charting library is included in the initial application bundle even when the admin page is not open. If so, I would use route-based code splitting and dynamically import the admin page or chart feature.

I would show a suitable Suspense fallback and verify that the charting code is loaded only when required. I would also check whether the chart itself processes too much data.

### 87. A page feels slow even though React Profiler shows short render times. Why?

**Answer:**

The bottleneck may be outside React rendering. It could be a slow API, large JavaScript parsing cost, image loading, browser layout and painting, long main-thread tasks, or a layout shift.

I would inspect the browser Performance and Network panels and measure Web Vitals. React Profiler is only one part of performance diagnosis.

### 88. An API search displays results from an older query after a newer query. How would you fix it?

**Answer:**

This is a race condition: an earlier request may finish after a later request and overwrite the newer results. I would cancel obsolete requests using `AbortController` where supported, or track the latest request and ignore stale responses.

I would also debounce search requests if requests are being sent for every keystroke. Debouncing reduces request frequency, while cancellation or stale-response checks ensure that old results do not replace newer ones.

### 89. How would you optimize a large e-commerce product listing?

**Answer:**

I would begin by measuring initial load, search, filtering, sorting, and scrolling. I would use pagination or server-side filtering for large datasets, optimize product images, and virtualize long lists when appropriate.

For React rendering, I would keep state local, use stable keys, and selectively memoize expensive product cards. I would also split routes, avoid duplicate API requests, and measure the result in a production build.

### 90. How do you prove that an optimization actually worked?

**Answer:**

I compare the same interaction before and after the change using the same data and environment. I use React Profiler for component rendering, browser Performance tools for main-thread and layout work, Network tools for requests, and Web Vitals for user experience.

I keep an optimization only when the measurements show a meaningful improvement and the application still behaves correctly.

---

## Final Interview Revision: Which Technique Should You Choose?

| Situation | Technique to consider |
|---|---|
| Expensive child with unchanged props | `React.memo` |
| Expensive calculated value | `useMemo` |
| Stable callback passed to memoized child | `useCallback` |
| State update affects unrelated components | State colocation |
| Too many context consumers update | Split contexts / stabilize provider value |
| Very large list | Virtualization |
| Large initial JavaScript bundle | Code splitting |
| Component code needed only on navigation | `lazy` + `Suspense` |
| Expensive UI update makes typing feel slow | `useTransition` |
| A displayed value can lag behind input | `useDeferredValue` |
| Search API called on every keystroke | Debouncing |
| Scroll/resize handler runs too often | Throttling |
| Independent API calls run sequentially | `Promise.all` |
| Older request overwrites newer results | Abort or ignore stale requests |
| Slow page despite fast React renders | Browser Performance / Network / Web Vitals |

## Important Interview Statements

- A re-render does not necessarily mean a DOM update.
- `React.memo` compares props; it does not block a component's own state or context updates.
- `useMemo` caches a value; `useCallback` caches a function reference.
- Memoization is a performance optimization, not a correctness mechanism.
- Stable keys preserve list-item identity; indexes are risky for dynamic lists.
- Virtualization reduces mounted DOM nodes; it does not improve API response time.
- `useTransition` changes update priority; it does not run work on another thread.
- `useDeferredValue` defers a value for rendering; it does not debounce network requests.
- Suspense handles pending UI for supported suspending work; an Error Boundary handles rendering errors such as a failed lazy import.
- React Profiler measures React rendering work, not the entire browser experience.
- Measure first, optimize the bottleneck, and measure again.

### Official documentation

- `memo`: https://react.dev/reference/react/memo
- `useMemo`: https://react.dev/reference/react/useMemo
- `useCallback`: https://react.dev/reference/react/useCallback
- `useTransition`: https://react.dev/reference/react/useTransition
- `useDeferredValue`: https://react.dev/reference/react/useDeferredValue
- `lazy`: https://react.dev/reference/react/lazy
- `Suspense`: https://react.dev/reference/react/Suspense
- React Developer Tools: https://react.dev/learn/react-developer-tools
- React Compiler: https://react.dev/learn/react-compiler
- Web Vitals: https://web.dev/articles/vitals

**Chapter 8 complete.** Before moving to Chapter 9, revise the concepts file and practise explaining the scenario-based questions aloud.
