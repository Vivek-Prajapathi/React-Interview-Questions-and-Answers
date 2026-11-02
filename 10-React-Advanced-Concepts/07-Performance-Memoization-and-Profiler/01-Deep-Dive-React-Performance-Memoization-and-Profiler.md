# React Performance: Memoization and Profiling

## 1. Measure before optimizing

Performance work begins by identifying the actual bottleneck. React DevTools Profiler helps inspect commits, render duration, and which components rendered. Browser performance tools help identify scripting, layout, paint, network, and long-task costs.

A component rendering is not automatically a performance problem. Optimize user-visible latency and expensive work, not render counts as a vanity metric.

## 2. React.memo

`memo` can skip rendering a component when its props are unchanged according to shallow comparison by default.

```jsx
const UserRow = memo(function UserRow({ user, onSelect }) {
  return <button onClick={() => onSelect(user.id)}>{user.name}</button>;
});
```

A new object or function prop has a new reference and can defeat memoization. `memo` is an optimization, not a guarantee that React will never render the component.

## 3. useMemo and useCallback

- `useMemo` caches a value calculation.
- `useCallback` caches a function reference.
- Both compare dependencies with `Object.is`.
- They are useful when they avoid expensive work or stabilize props/dependencies.
- They add complexity and should not be added mechanically.

## 4. State placement and component boundaries

Keep state as close as practical to the components that need it. If a high-level component owns rapidly changing input state, many unrelated descendants may render when that state changes. Splitting components or moving state down can reduce the affected subtree.

## 5. Context performance

When a Context provider receives a new value identity, consumers of that Context may render. Avoid creating a new provider object unnecessarily when it causes measurable work:

```jsx
const value = useMemo(() => ({ user, logout }), [user, logout]);
return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
```

This is not a universal fix. Splitting unrelated Context values can reduce how many consumers subscribe to changes.

## 6. Expensive calculations

Use `useMemo` for genuinely expensive calculations whose inputs change less frequently than the component renders. For large collections, also consider better algorithms, server filtering, or list virtualization.

## 7. useTransition and useDeferredValue

`useTransition` marks state updates as non-urgent so urgent interactions can remain responsive. `useDeferredValue` lets a part of the UI lag behind a rapidly changing value.

They do not make the underlying calculation faster. They let React schedule rendering work more flexibly.

## 8. List virtualization

Virtualization renders only the visible portion of a long list plus a small buffer. It reduces DOM node count and rendering/layout work. Libraries can manage item measurements, scrolling, and windowing.

Virtualization is especially useful for thousands of rows, but adds complexity around variable row heights, accessibility, and scroll behavior.

## 9. Code splitting

`React.lazy` and `Suspense` can defer loading a component until it is needed:

```jsx
const SettingsPage = React.lazy(() => import("./SettingsPage"));

<Suspense fallback={<p>Loading settings...</p>}>
  <SettingsPage />
</Suspense>
```

Code splitting reduces initial JavaScript when chunks are arranged around routes or features. Suspense fallback design and error handling for failed chunk loads should be considered.

## 10. Common optimization mistakes

- Adding memoization everywhere.
- Optimizing a component without profiling.
- Passing new object/function props to memoized children unnecessarily.
- Keeping rapidly changing state too high in the tree.
- Using virtualization for short lists.
- Assuming transitions make CPU work faster.
- Ignoring network, image, and bundle-size bottlenecks.
