# Complete React Performance Optimization

> **Chapter 8 – React Interview Questions and Answers Repository**  
> A detailed, practical guide to understanding, measuring, diagnosing, and improving React application performance.

## Table of Contents

1. [What Is React Performance Optimization?](#1-what-is-react-performance-optimization)
2. [Why Performance Matters](#2-why-performance-matters)
3. [How React Rendering Works](#3-how-react-rendering-works)
4. [Render Phase and Commit Phase](#4-render-phase-and-commit-phase)
5. [Reconciliation and the Virtual DOM](#5-reconciliation-and-the-virtual-dom)
6. [What Causes Unnecessary Re-renders?](#6-what-causes-unnecessary-re-renders)
7. [How to Identify Performance Problems](#7-how-to-identify-performance-problems)
8. [React Developer Tools Profiler](#8-react-developer-tools-profiler)
9. [React.memo](#9-reactmemo)
10. [useMemo](#10-usememo)
11. [useCallback](#11-usecallback)
12. [React.memo vs useMemo vs useCallback](#12-reactmemo-vs-usememo-vs-usecallback)
13. [Referential Equality and Shallow Comparison](#13-referential-equality-and-shallow-comparison)
14. [Memoization: Benefits and Costs](#14-memoization-benefits-and-costs)
15. [State Placement and Component Design](#15-state-placement-and-component-design)
16. [Preventing Context-driven Re-renders](#16-preventing-context-driven-re-renders)
17. [Lists, Keys, and List Performance](#17-lists-keys-and-list-performance)
18. [Virtualization and Windowing](#18-virtualization-and-windowing)
19. [Code Splitting](#19-code-splitting)
20. [React.lazy and Suspense](#20-reactlazy-and-suspense)
21. [Route-based Lazy Loading](#21-route-based-lazy-loading)
22. [useTransition](#22-usetransition)
23. [useDeferredValue](#23-usedeferredvalue)
24. [Debouncing and Throttling](#24-debouncing-and-throttling)
25. [Optimizing Expensive Calculations](#25-optimizing-expensive-calculations)
26. [Images, Fonts, and Static Assets](#26-images-fonts-and-static-assets)
27. [Network and Data-fetching Performance](#27-network-and-data-fetching-performance)
28. [Avoiding Waterfalls and Duplicate Requests](#28-avoiding-waterfalls-and-duplicate-requests)
29. [Production Builds and Bundle Analysis](#29-production-builds-and-bundle-analysis)
30. [Web Vitals and User-perceived Performance](#30-web-vitals-and-user-perceived-performance)
31. [React Compiler](#31-react-compiler)
32. [Common Performance Anti-patterns](#32-common-performance-anti-patterns)
33. [A Practical Optimization Workflow](#33-a-practical-optimization-workflow)
34. [End-to-end Example: Optimizing a Searchable Product List](#34-end-to-end-example-optimizing-a-searchable-product-list)
35. [Performance Checklist](#35-performance-checklist)
36. [Best Practices](#36-best-practices)
37. [Important Version and API Notes](#37-important-version-and-api-notes)

---

## 1. What Is React Performance Optimization?

React performance optimization is the process of making a React application respond quickly, render efficiently, use resources responsibly, and remain smooth as the application and its data grow.

Optimization is not simply about reducing the number of component renders. A render is not automatically a performance problem. React is designed to render components frequently when their inputs change. The goal is to avoid **unnecessary expensive work** and reduce the time users wait for meaningful results.

Performance work can target several layers:

- **Rendering:** avoid repeating expensive component work when its inputs have not meaningfully changed.
- **Computation:** avoid recalculating expensive derived values unnecessarily.
- **DOM updates:** minimize unnecessary changes to the browser DOM.
- **Loading:** send less JavaScript initially and load features when needed.
- **Lists:** avoid mounting thousands of off-screen rows at once.
- **Network:** avoid duplicate requests, request waterfalls, and oversized responses.
- **Assets:** deliver appropriately sized images, fonts, and other resources.
- **Perceived responsiveness:** keep interactions responsive while expensive work is happening.

### A simple example

Imagine a page with a search input and a list of 10,000 products. When a user types one character, the page updates. If every keystroke also triggers expensive calculations, renders unrelated components, and processes all 10,000 rows, typing may feel slow.

Optimization means identifying the expensive part and applying the right solution. It may be better state placement, a more efficient filtering algorithm, memoization, deferred rendering, or list virtualization—not automatically adding `useMemo` everywhere.

## 2. Why Performance Matters

Performance directly affects user experience.

1. **Responsiveness:** users expect buttons, inputs, navigation, and menus to react quickly.
2. **Perceived quality:** a slow interface feels unreliable even if the API and business logic are correct.
3. **Mobile experience:** lower-powered devices and constrained networks make unnecessary work more noticeable.
4. **Scalability:** a design that works with 20 records may become slow with 20,000.
5. **Accessibility:** users who rely on assistive technologies also benefit from predictable, responsive interfaces.
6. **Business outcomes:** slow pages can increase abandonment and reduce task completion.

### Performance is not just rendering speed

A page can render quickly but still feel slow because its images load late, its API request takes too long, or its JavaScript bundle delays interaction. Diagnose the actual bottleneck before choosing a technique.

## 3. How React Rendering Works

A React component is a JavaScript function that describes what the UI should look like for the current props, state, and context.

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

When `count` changes, React schedules an update. React calls the component again to calculate its next UI description. React then determines what needs to change in the host environment, such as the browser DOM.

### Important terms

- **Render:** React calls component functions and calculates the next UI description.
- **Re-render:** a component renders again after an update.
- **Commit:** React applies the required changes to the DOM and runs relevant commit-related work.
- **Mount:** a component is added to the rendered tree for the first time.
- **Unmount:** a component is removed from the rendered tree.
- **Update:** an already mounted component receives a new render.

A component rendering again does not mean every DOM element is recreated. React compares the new result with the previous result and commits only the necessary host updates.

### What can cause a component to render?

Common causes include:

- Its own state changes.
- Its parent renders and React renders the child as part of the update.
- A context value it reads changes.
- An external store subscription reports a change.
- A framework or library schedules an update.

A parent rendering does not necessarily mean every descendant must perform expensive work. Component boundaries, memoization, and state placement can reduce the work.

## 4. Render Phase and Commit Phase

React updates are commonly explained using two broad phases.

### 4.1 Render phase

During the render phase, React calls components and calculates what the next UI should be. This phase should be pure: rendering should not directly mutate the DOM, start subscriptions, or perform other side effects.

In concurrent-capable React, rendering work may be paused, restarted, or abandoned before it commits. Therefore, render logic must be safe to run more than once.

### 4.2 Commit phase

During the commit phase, React applies the necessary changes to the host environment. In a browser this includes DOM mutations. React also handles relevant ref updates and effect scheduling according to the effect type.

### Why this distinction matters

Do not put side effects in the component body:

```jsx
function Profile() {
  // Incorrect: this runs during rendering and may run more than once.
  // fetch("/api/profile");

  return <h1>Profile</h1>;
}
```

Use an effect for synchronization with an external system, or use a framework/data-fetching solution appropriate to the application:

```jsx
function Profile() {
  const [profile, setProfile] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    async function loadProfile() {
      try {
        const response = await fetch("/api/profile", {
          signal: controller.signal,
        });

        if (!response.ok) {
          throw new Error("Could not load profile");
        }

        setProfile(await response.json());
      } catch (error) {
        if (error.name !== "AbortError") {
          console.error(error);
        }
      }
    }

    loadProfile();
    return () => controller.abort();
  }, []);

  return <pre>{JSON.stringify(profile, null, 2)}</pre>;
}
```

## 5. Reconciliation and the Virtual DOM

**Reconciliation** is the process React uses to compare the previous rendered element tree with the next one and determine the updates required.

The term **Virtual DOM** is often used to describe React's in-memory representation of UI elements. It is useful as a teaching model, but it should not be understood as React blindly rebuilding the entire real DOM on every render.

### Simplified example

Previous UI:

```jsx
<div>
  <h1>Welcome</h1>
  <p>Count: 1</p>
</div>
```

Next UI:

```jsx
<div>
  <h1>Welcome</h1>
  <p>Count: 2</p>
</div>
```

The element type and structure are compatible. React can update the text in the paragraph rather than replacing the whole page.

### Element type and position

React uses element types, positions, and keys to understand identity. If a component at a position changes to a different type, React may discard the old subtree and mount a new one. This can reset state and cause extra work.

```jsx
function Page({ isAdmin }) {
  return isAdmin ? <AdminPanel /> : <StudentPanel />;
}
```

Switching between these component types replaces one subtree with the other. That may be correct behavior, but it is important to understand the remount.

### Keys

Keys help React match list items across updates. They should be stable and unique among siblings.

```jsx
{users.map((user) => (
  <UserCard key={user.id} user={user} />
))}
```

Avoid array indexes as keys when items can be inserted, removed, sorted, or reordered. An index identifies a position, not necessarily the same logical item.

## 6. What Causes Unnecessary Re-renders?

A render is not inherently unnecessary. It becomes a concern when it repeats work that does not need to happen or causes expensive descendants to do avoidable work.

### 6.1 State is placed too high

If a parent stores state used by only one small child, updating that state may cause a large part of the tree to render.

```jsx
function Page() {
  const [search, setSearch] = useState("");

  return (
    <>
      <Header />
      <SearchBox value={search} onChange={setSearch} />
      <LargeDashboard />
    </>
  );
}
```

If `LargeDashboard` does not depend on `search`, consider isolating the search state in a smaller component boundary.

### 6.2 Parent re-renders

By default, when a parent renders, React generally renders its child components too. `React.memo` can sometimes skip a child render when its props are unchanged.

### 6.3 New object and array references

```jsx
<Chart options={{ showLegend: true }} />
```

The object literal creates a new object on each parent render. A memoized child sees a different reference.

### 6.4 New function references

```jsx
<Child onSave={() => saveRecord(id)} />
```

The inline function is a new function object each time the parent renders. This matters when the child uses shallow prop comparison or the function is a dependency of another hook.

### 6.5 Context value changes

All consumers of a context may update when the provider's value changes according to React's context comparison. A newly created provider object can change on every provider render.

### 6.6 Expensive calculations in render

Sorting, filtering, parsing, or transforming a large dataset on every render can make updates slow.

### 6.7 Large lists

Rendering thousands of DOM nodes can affect layout, painting, memory, and interaction responsiveness even if each row is simple.

### 6.8 Effects that update state repeatedly

An effect that updates state and has incorrect dependencies can cause repeated render cycles or loops.

```jsx
// Incorrect: a new object is created every render, and the effect
// updates state on every run.
useEffect(() => {
  setOptions({ enabled: true });
}, [{ enabled: true }]);
```

Dependencies should represent the actual reactive values used by the effect. Do not suppress dependency warnings as a substitute for understanding the data flow.

## 7. How to Identify Performance Problems

The most important optimization rule is:

> Measure first. Find the bottleneck. Optimize the bottleneck. Measure again.

### A practical diagnosis sequence

1. Reproduce the slow interaction.
2. Identify whether the delay occurs during initial loading, typing, scrolling, navigation, or a network operation.
3. Use browser performance tools to inspect scripting, layout, painting, and network activity.
4. Use React Developer Tools Profiler to identify components that render and how long they take.
5. Check the component's props, state, and context changes.
6. Apply one targeted change.
7. Repeat the same interaction and compare results.

### Do not optimize based only on render counts

A component can render many times without creating a noticeable problem. A component can also render once and perform a very expensive calculation. Render count is only one clue.

## 8. React Developer Tools Profiler

React Developer Tools includes a **Profiler** for recording component rendering work.

### How to use it

1. Install or open React Developer Tools in the browser.
2. Open the Profiler tab.
3. Start recording.
4. Perform the interaction that feels slow.
5. Stop recording.
6. Inspect the commits and component flamegraph or ranked view.
7. Select a component to inspect why it rendered, where that information is available.

### What to look for

- Components with high render duration.
- Components repeatedly rendering after unrelated state changes.
- Large subtrees rendering for a small interaction.
- Expensive list items.
- Context updates affecting many consumers.

Profiler timings are diagnostic information, not a guarantee of the exact production experience. Development mode, Strict Mode, device performance, and profiling overhead can affect measurements. Use production-like testing when evaluating real user performance.

## 9. React.memo

`memo` is a React API that lets React skip rendering a component when its props have not changed according to the comparison function.

```jsx
import { memo } from "react";

const UserCard = memo(function UserCard({ user }) {
  console.log("UserCard rendered");

  return <h3>{user.name}</h3>;
});

export default UserCard;
```

If the parent renders again and the `user` prop is still the same reference, React can reuse the previous rendered result for this component.

### Default comparison

By default, `memo` compares each prop using `Object.is`. This is a shallow, per-prop comparison; it does not deeply compare nested objects.

### When React.memo is useful

- A component renders frequently because its parent renders.
- Its props are usually unchanged.
- Rendering it is meaningfully expensive.
- It receives stable primitive or memoized props.

### When it may not help

- Props change on nearly every parent render.
- The component is very cheap to render.
- The component updates because its own state changes.
- The component consumes a context whose value changes.
- New object/function props are passed every time.

### Custom comparison

```jsx
const UserCard = memo(
  function UserCard({ user }) {
    return <h3>{user.name}</h3>;
  },
  (previousProps, nextProps) => {
    return previousProps.user.id === nextProps.user.id &&
      previousProps.user.name === nextProps.user.name;
  }
);
```

A custom comparison must return `true` only when the new props would produce the same output and behavior as the previous props. Ignoring a changed callback can preserve a stale closure and create bugs. Custom deep comparisons may cost more than rendering.

## 10. useMemo

`useMemo` is a React Hook that caches the result of a calculation between renders while its dependencies remain unchanged.

### Syntax

```jsx
const cachedValue = useMemo(() => calculateValue(a, b), [a, b]);
```

- First argument: a calculation function.
- Second argument: dependency array.
- Return value: the calculated or cached value.

### Example: expensive filtering

```jsx
import { useMemo, useState } from "react";

function ProductList({ products }) {
  const [search, setSearch] = useState("");

  const filteredProducts = useMemo(() => {
    const normalizedSearch = search.toLowerCase();

    return products.filter((product) =>
      product.name.toLowerCase().includes(normalizedSearch)
    );
  }, [products, search]);

  return (
    <>
      <input
        value={search}
        onChange={(event) => setSearch(event.target.value)}
        placeholder="Search products"
      />

      {filteredProducts.map((product) => (
        <p key={product.id}>{product.name}</p>
      ))}
    </>
  );
}
```

The filter recalculates when `products` or `search` changes. If the component re-renders for another reason while both dependencies remain the same, React can reuse the cached result.

### Example: stable object prop

```jsx
const chartOptions = useMemo(
  () => ({ showLegend: true, theme: "light" }),
  []
);

return <Chart options={chartOptions} />;
```

This can help when a memoized child relies on a stable object reference.

### Important rules

- Call `useMemo` at the top level of a component or custom Hook.
- Do not call it inside conditions, loops, or nested functions.
- Include every reactive value used by the calculation in the dependency array.
- The calculation must be pure.
- Do not use it to make code correct; it is a performance optimization.
- React may discard memoized values in some circumstances. Your code must remain correct without relying on the cache.

### When not to use useMemo

Do not memoize every variable. For a cheap calculation, the dependency tracking and cache management may add complexity without meaningful benefit.

## 11. useCallback

`useCallback` is a React Hook that caches a function definition between renders while its dependencies remain unchanged.

### Syntax

```jsx
const cachedFunction = useCallback(() => {
  performAction(value);
}, [value]);
```

### Example

```jsx
import { memo, useCallback, useState } from "react";

const SaveButton = memo(function SaveButton({ onSave }) {
  console.log("SaveButton rendered");
  return <button onClick={onSave}>Save</button>;
});

function Editor() {
  const [title, setTitle] = useState("");

  const handleSave = useCallback(() => {
    console.log("Saving:", title);
  }, [title]);

  return (
    <>
      <input value={title} onChange={(event) => setTitle(event.target.value)} />
      <SaveButton onSave={handleSave} />
    </>
  );
}
```

Without `useCallback`, a new `handleSave` function would be created on each render. Since `SaveButton` is memoized, a changed function prop would cause it to render again. `useCallback` can preserve the function reference while `title` is unchanged.

### Functional state updates can reduce dependencies

```jsx
const increment = useCallback(() => {
  setCount((currentCount) => currentCount + 1);
}, []);
```

The updater receives the latest state, so the callback does not need to capture `count`.

### useCallback does not stop a function from executing

It preserves a function reference; it does not cache the result of calling the function. Use `useMemo` for a calculated value.

## 12. React.memo vs useMemo vs useCallback

| API | What it memoizes | Typical use |
|---|---|---|
| `memo(Component)` | A component's rendered result based on props | Skip child rendering when props are unchanged |
| `useMemo(fn, deps)` | A calculated value | Avoid repeating expensive calculations or preserve object identity |
| `useCallback(fn, deps)` | A function reference | Preserve callback identity for memoized children or hook dependencies |

### Combined example

```jsx
import { memo, useCallback, useMemo, useState } from "react";

const Results = memo(function Results({ items, onSelect }) {
  return items.map((item) => (
    <button key={item.id} onClick={() => onSelect(item.id)}>
      {item.name}
    </button>
  ));
});

function SearchPage({ products }) {
  const [query, setQuery] = useState("");

  const results = useMemo(() => {
    return products.filter((product) =>
      product.name.toLowerCase().includes(query.toLowerCase())
    );
  }, [products, query]);

  const handleSelect = useCallback((id) => {
    console.log("Selected:", id);
  }, []);

  return (
    <>
      <input value={query} onChange={(event) => setQuery(event.target.value)} />
      <Results items={results} onSelect={handleSelect} />
    </>
  );
}
```

Each API solves a different identity or rendering problem. They are not interchangeable.

## 13. Referential Equality and Shallow Comparison

JavaScript objects, arrays, and functions are reference values. Two separately created objects are not equal under `Object.is`, even if their contents look identical.

```js
const first = { name: "Asha" };
const second = { name: "Asha" };

console.log(Object.is(first, second)); // false
console.log(Object.is(first.name, second.name)); // true
```

This matters because `memo`, Hook dependency comparisons, and context value comparisons use identity-oriented checks.

### Primitive vs reference values

```js
Object.is(10, 10); // true
Object.is("React", "React"); // true

Object.is([], []); // false
Object.is(() => {}, () => {}); // false
```

### Practical consequence

A parent can create a new object on every render:

```jsx
<Profile settings={{ compact: true }} />
```

Even if `compact` is always true, the reference changes. If stable identity matters, create the object with `useMemo` or move it outside the component when it is constant.

## 14. Memoization: Benefits and Costs

Memoization means retaining a previous result so it can be reused when the relevant inputs have not changed.

### Benefits

- Avoids repeating expensive calculations.
- Can prevent unnecessary child renders.
- Can preserve object and function identity.
- Can improve responsiveness in measured bottlenecks.

### Costs and risks

- More code and cognitive complexity.
- Dependency arrays can be incorrect.
- Cached values consume memory.
- Comparing props also costs time.
- Custom comparisons can be expensive.
- Stale closures can introduce subtle bugs.
- Memoization can hide poor component boundaries or data flow.

### Rule

Use memoization when there is a measured or clearly understood performance reason. Do not treat it as a default requirement for every component, function, or variable.

## 15. State Placement and Component Design

One of the simplest performance improvements is putting state as close as possible to the components that need it.

### Less effective design

```jsx
function Dashboard() {
  const [search, setSearch] = useState("");

  return (
    <div>
      <Header />
      <input value={search} onChange={(e) => setSearch(e.target.value)} />
      <AnalyticsPanel />
      <ActivityFeed />
      <LargeTable />
    </div>
  );
}
```

Every keystroke updates `Dashboard`, potentially causing all children to render.

### Better state locality

```jsx
function Dashboard() {
  return (
    <div>
      <Header />
      <DashboardSearch />
      <AnalyticsPanel />
      <ActivityFeed />
      <LargeTable />
    </div>
  );
}

function DashboardSearch() {
  const [search, setSearch] = useState("");

  return (
    <input
      value={search}
      onChange={(event) => setSearch(event.target.value)}
      placeholder="Search"
    />
  );
}
```

Now search updates are contained within `DashboardSearch`, unless the state needs to be shared elsewhere.

### Other design techniques

- Split large components by responsibility.
- Avoid passing state through many layers when a suitable context or state-management solution is more appropriate.
- Avoid putting unrelated values into one frequently changing context.
- Keep derived data derived; do not duplicate it in state unless necessary.
- Use stable IDs and normalized data for large collections.

## 16. Preventing Context-driven Re-renders

Context is useful for sharing values such as the current user, theme, or locale. A context consumer updates when the context value it reads changes.

### Problem: new provider object every render

```jsx
function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const value = { user, setUser };

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}
```

The `value` object is newly created whenever `AuthProvider` renders. Consumers can update even if `user` itself has not changed.

### Stabilize the provider value

```jsx
function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const value = useMemo(() => ({ user, setUser }), [user]);

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}
```

This avoids changing the provider value reference when `user` has not changed.

### Split contexts by update frequency

If theme, authentication, and frequently changing dashboard data are placed in one context, all consumers of that context may be affected by updates to the shared value. Separate contexts can reduce unrelated updates.

### Important limitation

Memoizing the provider object does not prevent consumers from updating when a value they use actually changes. Context is a data-sharing mechanism, not a general-purpose selector system.

## 17. Lists, Keys, and List Performance

Lists can become expensive because each item may create components and DOM nodes.

### Use stable keys

```jsx
{products.map((product) => (
  <ProductCard key={product.id} product={product} />
))}
```

A stable key helps React preserve item identity when the list changes.

### Avoid unstable keys

```jsx
// Avoid: a new key on each render forces remounting.
{products.map((product) => (
  <ProductCard key={Math.random()} product={product} />
))}
```

Do not use random values or generate a new key during rendering. Avoid array indexes for reorderable or editable lists.

### Keep list item components focused

```jsx
const ProductRow = memo(function ProductRow({ product, onChoose }) {
  return (
    <li>
      <span>{product.name}</span>
      <button onClick={() => onChoose(product.id)}>Choose</button>
    </li>
  );
});
```

Memoization can help if the row is expensive and receives stable props. It does not solve the cost of mounting thousands of DOM nodes.

## 18. Virtualization and Windowing

**Virtualization** (also called windowing) renders only the visible portion of a large list, plus a small buffer, instead of rendering every item at once.

For example, a dataset may contain 50,000 rows, but the viewport may show only 20. A virtualized list renders the visible rows and reuses or updates them as the user scrolls.

### Benefits

- Fewer mounted DOM nodes.
- Lower memory usage for large lists.
- Less layout and paint work.
- Often smoother scrolling.

### When to use it

- Large tables.
- Long chat histories.
- Product catalogs with many entries.
- Logs, timelines, and dashboards.

### What it does not solve

Virtualization does not automatically make filtering, sorting, or fetching faster. Those operations may need separate optimization.

### Library note

Libraries such as `react-window` and TanStack Virtual are commonly used for virtualization. Check the library's current React compatibility and API documentation before choosing a version.

## 19. Code Splitting

Code splitting divides an application's JavaScript into smaller chunks that can be loaded when needed.

Without effective splitting, a user may need to download code for every feature before using the first page.

### Benefits

- Smaller initial JavaScript payload.
- Faster initial loading on many devices and networks.
- Features can be loaded on demand.
- Less code needs to be parsed and executed initially.

### Common splitting strategies

- Route-based splitting.
- Feature-based splitting.
- Splitting large third-party libraries.
- Loading rarely used editors, charts, or admin tools only when needed.

Code splitting is not free: it can introduce additional network requests and loading states. Split at meaningful boundaries.

## 20. React.lazy and Suspense

`lazy` lets a component's code be loaded dynamically when React first attempts to render it. `Suspense` displays a fallback while the lazy component's code is loading.

### Syntax

```jsx
import { lazy, Suspense } from "react";

const ReportsPage = lazy(() => import("./pages/ReportsPage"));

function App() {
  return (
    <Suspense fallback={<p>Loading reports...</p>}>
      <ReportsPage />
    </Suspense>
  );
}
```

### How it works

1. `lazy` receives a function that returns a Promise, usually from `import()`.
2. React attempts to render the lazy component.
3. If its module is not loaded, React suspends that part of the tree.
4. The nearest suitable Suspense boundary displays its fallback.
5. When the module is ready, React can render the component.

### Important points

- The dynamically imported module must resolve to a module with a default component export for the standard `lazy` pattern.
- Place Suspense boundaries around meaningful loading regions.
- A fallback should communicate progress and avoid a jarring blank page.
- Suspense for code loading is supported by `lazy`; data-fetching Suspense depends on the data framework or library being used.
- Handle loading failures with an appropriate error boundary or route-level error handling.

### Named exports

```jsx
const ReportsPage = lazy(() =>
  import("./pages/ReportsPage").then((module) => ({
    default: module.ReportsPage,
  }))
);
```

## 21. Route-based Lazy Loading

Route-based code splitting loads a page's JavaScript only when the user navigates to that route.

### React Router example

```jsx
import { lazy, Suspense } from "react";
import { BrowserRouter, Routes, Route } from "react-router-dom";

const Home = lazy(() => import("./pages/Home"));
const Courses = lazy(() => import("./pages/Courses"));
const AdminDashboard = lazy(() => import("./pages/AdminDashboard"));

function AppRoutes() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading page...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/courses" element={<Courses />} />
          <Route path="/admin" element={<AdminDashboard />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

The route components are split into separate chunks by the bundler when supported. Verify the production build output to confirm the splitting.

### Where to place Suspense

A single boundary around all routes is simple. More granular boundaries can keep shared layout visible while only the page content shows a loading state.

## 22. useTransition

`useTransition` is a React Hook that lets you mark some state updates as **non-urgent transitions**. React can prioritize urgent updates, such as typing, over transition work.

### Syntax

```jsx
const [isPending, startTransition] = useTransition();
```

- `isPending`: indicates whether the transition is pending.
- `startTransition`: marks state updates inside its callback as transitions.

### Example: responsive search

```jsx
import { useState, useTransition } from "react";

function SearchPage({ items }) {
  const [input, setInput] = useState("");
  const [query, setQuery] = useState("");
  const [isPending, startTransition] = useTransition();

  function handleChange(event) {
    const value = event.target.value;

    // Urgent: keep the controlled input responsive.
    setInput(value);

    // Non-urgent: update the expensive result view.
    startTransition(() => {
      setQuery(value);
    });
  }

  const results = items.filter((item) =>
    item.name.toLowerCase().includes(query.toLowerCase())
  );

  return (
    <>
      <input value={input} onChange={handleChange} />
      {isPending && <span>Updating results...</span>}
      <ul>
        {results.map((item) => <li key={item.id}>{item.name}</li>)}
      </ul>
    </>
  );
}
```

For a very large dataset, filtering itself may still be expensive. Combine appropriate algorithms, memoization, deferred rendering, or virtualization as needed.

### Important rules

- A transition update is non-urgent; it is not a network request.
- Do not use a transition to control the text input's immediate value.
- The callback passed to `startTransition` runs immediately; the state updates scheduled inside it are marked as transitions.
- `isPending` is useful for showing a subtle pending indicator.
- Transitions do not make expensive work disappear. They allow React to prioritize other updates.

## 23. useDeferredValue

`useDeferredValue` lets a part of the UI use a deferred version of a value. React can keep rendering the previous value while preparing an update using the new value.

### Syntax

```jsx
const deferredValue = useDeferredValue(value);
```

### Example

```jsx
import { memo, useDeferredValue, useState } from "react";

const Results = memo(function Results({ query, items }) {
  const results = items.filter((item) =>
    item.name.toLowerCase().includes(query.toLowerCase())
  );

  return results.map((item) => <p key={item.id}>{item.name}</p>);
});

function Search({ items }) {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query);

  const isStale = query !== deferredQuery;

  return (
    <>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <div aria-busy={isStale}>
        <Results query={deferredQuery} items={items} />
      </div>
    </>
  );
}
```

The input updates immediately, while the result view can lag behind during expensive rendering.

### useTransition vs useDeferredValue

| `useTransition` | `useDeferredValue` |
|---|---|
| Marks state updates as non-urgent | Defers the value used by part of the UI |
| You control the update with `startTransition` | React manages a deferred version of an existing value |
| Returns `isPending` and `startTransition` | Returns a deferred value |
| Useful when you own the state update | Useful when you receive or use a value and want to defer its rendering |

Neither Hook debounces network requests. Neither automatically optimizes a slow algorithm.

## 24. Debouncing and Throttling

Debouncing and throttling are JavaScript techniques often used to reduce how frequently expensive event-driven work runs.

### Debouncing

Debouncing waits until a specified period has passed without another event before running a function.

Useful for:

- Search API requests.
- Autosave after typing stops.
- Validation after a user finishes entering text.

```jsx
import { useEffect, useState } from "react";

function SearchBox() {
  const [query, setQuery] = useState("");
  const [debouncedQuery, setDebouncedQuery] = useState("");

  useEffect(() => {
    const timeoutId = setTimeout(() => {
      setDebouncedQuery(query);
    }, 400);

    return () => clearTimeout(timeoutId);
  }, [query]);

  useEffect(() => {
    if (!debouncedQuery.trim()) return;

    // Trigger the search request here.
    console.log("Search API for:", debouncedQuery);
  }, [debouncedQuery]);

  return (
    <input value={query} onChange={(e) => setQuery(e.target.value)} />
  );
}
```

### Throttling

Throttling limits a function to running at most once during a specified time interval.

Useful for:

- Scroll handlers.
- Resize handlers.
- Pointer movement tracking.
- Repeated events where regular updates are needed.

### Difference

- Debounce: run after events stop for the configured delay.
- Throttle: run at a controlled maximum frequency while events continue.

Neither is a React rendering Hook. They are general JavaScript techniques that can be used in React with correct cleanup and lifecycle handling.

## 25. Optimizing Expensive Calculations

First check whether the algorithm can be improved. Memoization does not compensate for an unnecessarily inefficient algorithm.

### Example: avoid repeated work

```jsx
const total = cart.reduce((sum, item) => {
  return sum + item.price * item.quantity;
}, 0);
```

For a small cart, this is already inexpensive. For a large dataset or complex transformation, consider:

- Reducing algorithmic complexity.
- Avoiding repeated nested loops.
- Precomputing indexes or lookup maps.
- Performing work only when inputs change.
- Moving CPU-intensive work to a Web Worker when appropriate.
- Paginating or virtualizing large results.

### Lookup with a Map

Repeatedly calling `array.find()` inside another loop can lead to repeated linear scans.

```js
const userById = new Map(users.map((user) => [user.id, user]));

const assignedUsers = tasks.map((task) => userById.get(task.userId));
```

Building the map takes time and memory, but can make repeated lookups more efficient for sufficiently large collections.

## 26. Images, Fonts, and Static Assets

Large assets can slow loading even when React rendering is efficient.

### Images

- Use appropriately sized images rather than serving huge originals.
- Prefer modern formats such as WebP or AVIF when supported by your delivery pipeline.
- Use responsive image techniques such as `srcSet` and `sizes`.
- Set width and height or an aspect ratio to reduce layout shifts.
- Lazy-load below-the-fold images where appropriate.
- Do not lazy-load the main above-the-fold or likely Largest Contentful Paint image without a reason.
- Compress images while maintaining acceptable quality.

```jsx
<img
  src="/images/course-card.webp"
  alt="Course preview"
  width="640"
  height="360"
  loading="lazy"
/>
```

### Fonts

- Load only the font weights and character subsets needed.
- Prefer efficient font formats such as WOFF2.
- Use `font-display` thoughtfully.
- Preload only critical fonts; unnecessary preloads compete with other important resources.

### Static assets

Use caching and content-hashed filenames through the build system or CDN. Avoid shipping unused assets.

## 27. Network and Data-fetching Performance

A React page can feel slow because of network behavior rather than rendering.

### Techniques

- Request only the data the page needs.
- Use pagination or cursor-based loading for large datasets.
- Avoid fetching the same data repeatedly.
- Cache reusable server data with an appropriate data-fetching library or framework.
- Cancel or ignore obsolete requests when the user changes screens or search terms.
- Provide loading, empty, error, and retry states.
- Avoid serial requests when independent requests can run in parallel.

### Parallel requests

```jsx
const [profile, courses] = await Promise.all([
  fetch("/api/profile").then((response) => response.json()),
  fetch("/api/courses").then((response) => response.json()),
]);
```

Check each response's `ok` status in production code and handle errors deliberately. `Promise.all` rejects if one input rejects; use `Promise.allSettled` when partial results are useful.

## 28. Avoiding Waterfalls and Duplicate Requests

A request waterfall occurs when one request must finish before another independent request begins.

Less efficient:

```js
const user = await getUser();
const courses = await getCourses();
const notifications = await getNotifications();
```

If all three requests are independent, this makes the total wait approximately the sum of their durations.

Better:

```js
const [user, courses, notifications] = await Promise.all([
  getUser(),
  getCourses(),
  getNotifications(),
]);
```

### Avoid duplicate requests

- Keep data ownership clear.
- Avoid triggering the same request from multiple components without a shared cache or deduplication strategy.
- Be aware that development Strict Mode can expose effects that are not safely written; do not disable Strict Mode merely to hide duplicate-effect problems.
- Use cancellation or ignore stale responses when appropriate.
- Consider a query library such as TanStack Query or a framework's data APIs when caching, deduplication, retries, and synchronization become complex.

## 29. Production Builds and Bundle Analysis

Development mode is designed for developer feedback and is not representative of production performance.

### Build and preview with Vite

```bash
npm run build
npm run preview
```

Use the scripts defined by the project. Vite's production build uses its configured bundler to generate deployable assets.

### Bundle analysis

Inspect:

- Total JavaScript transferred.
- Largest chunks.
- Duplicate dependencies.
- Large libraries included in the initial bundle.
- Whether route-based splitting worked.
- Unused code and tree-shaking opportunities.

Do not remove a dependency solely because it is large; verify that the replacement is correct and maintainable.

## 30. Web Vitals and User-perceived Performance

Web Vitals are metrics used to understand important aspects of real-world user experience.

### Core Web Vitals

1. **LCP – Largest Contentful Paint:** measures when the largest visible content element is rendered. It reflects loading experience.
2. **INP – Interaction to Next Paint:** measures interaction responsiveness across a page's lifetime. It replaced First Input Delay as a Core Web Vital.
3. **CLS – Cumulative Layout Shift:** measures unexpected visual movement of page content.

### Other useful metrics

- **FCP – First Contentful Paint:** when the first content is rendered.
- **TTFB – Time to First Byte:** how long the browser waits for the first byte of a response.
- **TBT – Total Blocking Time:** a lab metric that helps identify long main-thread blocking tasks.

### Important distinction

React component render duration is not the same as LCP, INP, or CLS. A fast React render can coexist with poor network performance or layout shifts. Measure the whole page experience.

## 31. React Compiler

React Compiler is a build-time compiler that can automatically optimize component rendering by applying memoization where it can prove it is safe.

### Why it matters

Historically, developers often used `memo`, `useMemo`, and `useCallback` manually to preserve identities and skip repeated work. React Compiler aims to reduce the need for manual memoization in supported code.

### Important points

- It is a compiler toolchain feature, not a runtime Hook.
- It analyzes component and Hook code and can generate memoization.
- It does not eliminate the need for good component architecture or performance measurement.
- It cannot make network requests faster, fix an inefficient algorithm, or eliminate the cost of thousands of DOM nodes.
- Compatibility, setup, and configuration depend on the React version and build tooling.
- Follow the current official React Compiler installation and configuration guidance for the version being used.

Do not assume a project has React Compiler enabled simply because it uses a recent React version.

## 32. Common Performance Anti-patterns

### 32.1 Using memoization everywhere

Memoization has costs. Use it when it solves a real problem.

### 32.2 Using random keys

Random keys cause list items to be treated as new elements and can force remounts.

### 32.3 Using array indexes for dynamic lists

Indexes can associate state with the wrong item after insertions, removals, or reordering.

### 32.4 Storing derived state unnecessarily

If a value can be calculated from props or state, duplicating it in state can create synchronization bugs.

```jsx
// Usually unnecessary:
const [fullName, setFullName] = useState("");

useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// Prefer deriving it:
const fullName = `${firstName} ${lastName}`;
```

### 32.5 Putting all state in one root component

This can cause broad updates. Keep state local unless it genuinely needs to be shared.

### 32.6 Using useEffect for every calculation

Effects are for synchronizing with external systems. Calculations based on current props/state generally belong in render, optionally memoized when expensive.

### 32.7 Ignoring image and bundle size

A well-memoized React tree can still load slowly because of oversized assets and JavaScript.

### 32.8 Optimizing without measuring

A change can make code more complex and even slower. Verify the result.

## 33. A Practical Optimization Workflow

Use this workflow in real projects:

### Step 1: Define the problem

Be specific: “Typing in the course search input becomes delayed with 5,000 courses” is better than “the page is slow.”

### Step 2: Reproduce consistently

Use the same data, device profile, browser, and interaction when comparing before and after.

### Step 3: Measure

Use React Profiler, browser Performance panel, Network panel, and Web Vitals where appropriate.

### Step 4: Find the root cause

Ask:

- Is JavaScript computation expensive?
- Is a large subtree rendering?
- Are there too many DOM nodes?
- Is the main thread blocked?
- Is the network slow?
- Is the initial bundle too large?
- Are images causing loading delays or layout shifts?

### Step 5: Choose the matching technique

| Problem | Potential solution |
|---|---|
| Expensive derived calculation | Better algorithm, `useMemo` if beneficial |
| Memoized child receives new callback | `useCallback` where useful |
| Child re-renders with unchanged props | `memo` where useful |
| State update re-renders unrelated page areas | Move state closer to where it is used |
| Too many context consumers update | Split contexts or redesign subscriptions |
| Huge list creates too many DOM nodes | Virtualization |
| Initial bundle is too large | Code splitting and lazy loading |
| Typing is blocked by expensive result rendering | `useTransition` or `useDeferredValue` |
| Search fires on every keystroke | Debouncing, request cancellation |
| Independent requests run one after another | Parallelize with `Promise.all` |
| Large images delay loading | Responsive, compressed images |
| Layout shifts | Reserve image/media dimensions |

### Step 6: Change one thing at a time

This helps establish which change improved or worsened performance.

### Step 7: Measure again

Compare the same scenario. Keep the change only if it provides a real benefit without breaking behavior.

## 34. End-to-end Example: Optimizing a Searchable Product List

This example combines several techniques. It is intentionally illustrative; for a very large production dataset, server-side search or virtualization may be more suitable.

### Initial implementation

```jsx
function ProductPage({ products }) {
  const [search, setSearch] = useState("");

  const results = products.filter((product) =>
    product.name.toLowerCase().includes(search.toLowerCase())
  );

  return (
    <>
      <input
        value={search}
        onChange={(event) => setSearch(event.target.value)}
      />

      {results.map((product) => (
        <ProductCard
          key={product.id}
          product={product}
          onSelect={() => console.log(product.id)}
        />
      ))}
    </>
  );
}
```

Potential issues:

- Filtering happens on every render.
- Each inline callback is a new function.
- Every visible product card may render when the parent renders.
- A very large list mounts every item.

### Improved implementation

```jsx
import { memo, useCallback, useMemo, useState } from "react";

const ProductCard = memo(function ProductCard({ product, onSelect }) {
  return (
    <article>
      <h3>{product.name}</h3>
      <p>{product.price}</p>
      <button onClick={() => onSelect(product.id)}>View</button>
    </article>
  );
});

function ProductPage({ products }) {
  const [search, setSearch] = useState("");

  const filteredProducts = useMemo(() => {
    const normalizedSearch = search.trim().toLowerCase();

    return products.filter((product) =>
      product.name.toLowerCase().includes(normalizedSearch)
    );
  }, [products, search]);

  const handleSelect = useCallback((productId) => {
    console.log("Selected product:", productId);
  }, []);

  return (
    <section>
      <input
        value={search}
        onChange={(event) => setSearch(event.target.value)}
        placeholder="Search products"
      />

      <div>
        {filteredProducts.map((product) => (
          <ProductCard
            key={product.id}
            product={product}
            onSelect={handleSelect}
          />
        ))}
      </div>
    </section>
  );
}
```

### What improved?

- `useMemo` can reuse the filtered array when `products` and `search` have not changed.
- `useCallback` preserves the `handleSelect` function reference.
- `memo` can skip rendering a card when its props remain unchanged.
- Stable product IDs preserve list identity.

### What is still not solved?

When the search text changes, the filtered array changes and the list's membership may change. The visible cards whose props change still need to render. All matching items are still mounted. For a very large dataset, consider server-side filtering, pagination, and virtualization.

Do not add every technique automatically. Profile the actual application and select only what helps.

## 35. Performance Checklist

### Rendering

- [ ] Have I measured which components are expensive?
- [ ] Is state located close to the components that use it?
- [ ] Are expensive child components receiving stable props where appropriate?
- [ ] Are context values changing unnecessarily?
- [ ] Are render functions pure?

### Lists

- [ ] Does every item have a stable unique key?
- [ ] Is the list too large to mount all at once?
- [ ] Would pagination or virtualization help?

### Loading

- [ ] Is the initial JavaScript bundle reasonable?
- [ ] Are routes or large features split?
- [ ] Are loading fallbacks useful?
- [ ] Are images sized and compressed appropriately?

### Data

- [ ] Are duplicate requests avoided?
- [ ] Can independent requests run in parallel?
- [ ] Are large responses paginated?
- [ ] Are stale requests handled?

### Verification

- [ ] Did I measure before and after?
- [ ] Did I test a production build?
- [ ] Did I check real user experience metrics where appropriate?

## 36. Best Practices

1. Measure before optimizing.
2. Optimize the cause, not the symptom.
3. Keep rendering pure.
4. Place state near the components that need it.
5. Use stable keys for lists.
6. Use `memo`, `useMemo`, and `useCallback` selectively.
7. Prefer better algorithms before adding memoization.
8. Split large routes and features when it reduces initial loading cost.
9. Virtualize genuinely large lists.
10. Keep images and bundles small.
11. Avoid duplicate network requests and unnecessary waterfalls.
12. Use transitions to preserve responsiveness during non-urgent rendering.
13. Test production builds and representative devices.
14. Re-measure after every meaningful optimization.
15. Prefer simple, maintainable code when performance is already sufficient.

## 37. Important Version and API Notes

This chapter focuses on modern function components and Hooks.

- `React.memo`, `useMemo`, and `useCallback` are established React APIs.
- `React.lazy` and `Suspense` support code-loading boundaries. Data Suspense behavior depends on the framework or library.
- `useTransition` and `useDeferredValue` are concurrent UI APIs introduced in React 18.
- React 19 continues to support these APIs and includes additional improvements to the React platform.
- React Compiler is a separate compiler capability; its availability and setup depend on project configuration.
- React Router APIs and bundler behavior vary by their own versions. Route lazy-loading syntax should match the installed React Router version.
- Always check the official documentation for the exact React, React Router, bundler, and virtualization-library versions used in a project.

### Official documentation

- React `memo`: https://react.dev/reference/react/memo
- React `useMemo`: https://react.dev/reference/react/useMemo
- React `useCallback`: https://react.dev/reference/react/useCallback
- React `useTransition`: https://react.dev/reference/react/useTransition
- React `useDeferredValue`: https://react.dev/reference/react/useDeferredValue
- React `lazy`: https://react.dev/reference/react/lazy
- React `Suspense`: https://react.dev/reference/react/Suspense
- React Developer Tools: https://react.dev/learn/react-developer-tools
- React Compiler: https://react.dev/learn/react-compiler
- Web Vitals: https://web.dev/articles/vitals

---

**Chapter 8 learning outcome:** You should be able to explain how React rendering works, diagnose unnecessary work, choose the correct optimization technique, understand the trade-offs of memoization, improve loading and list performance, and justify an optimization using measurements rather than guesswork.
