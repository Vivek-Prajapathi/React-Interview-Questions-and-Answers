# Advanced State Management and Context Patterns

## 1. Classify state before choosing a tool

A useful first step is to classify state:
- **Local UI state:** open/closed, selected tab, input text.
- **Shared client state:** authenticated user, theme, cart.
- **Server state:** data owned by an API, with loading, caching, freshness, and mutation concerns.
- **URL state:** search terms, filters, sorting, and page number that should be shareable or navigable.

Different categories have different needs. A server-state library is not simply a replacement for `useState`, and Context is not automatically a full data cache.

## 2. Lift state up

When sibling components need the same value, move ownership to their closest common parent and pass data and callbacks through props. This keeps one source of truth.

## 3. Context API

Context passes values through a component tree without manually threading props at every level. It is useful for values consumed by many descendants, such as authentication or theme.

```jsx
const ThemeContext = createContext(null);

function App() {
  const [theme, setTheme] = useState("light");
  const value = useMemo(() => ({ theme, setTheme }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      <Page />
    </ThemeContext.Provider>
  );
}
```

Context does not eliminate state management decisions. A provider value change notifies consumers, so split contexts by concern when useful.

## 4. useReducer with Context

For complex shared transitions, combine a reducer with Context. The reducer defines legal transitions; Context exposes state and dispatch to descendants. Keep reducer logic pure and avoid putting unrelated high-frequency state into one broad provider.

## 5. External stores

An external store can manage shared state outside React. React's `useSyncExternalStore` is the supported Hook for subscribing to external stores in a way that works with concurrent rendering.

A store should provide a stable snapshot until the underlying data changes and a subscription function that notifies React.

## 6. Server state vs client state

Server state is remote, asynchronous, can become stale, and may be shared across users or devices. It often benefits from caching, deduplication, retries, invalidation, and background refetching. Libraries such as TanStack Query or framework data APIs can provide these capabilities.

Client state is owned by the current UI session and is often managed with React state, reducers, Context, or a state library.

## 7. Choosing a state solution

- Use `useState` for simple local state.
- Use `useReducer` for complex local transitions.
- Lift state for a small group of related components.
- Use Context for values needed across a subtree.
- Use an external store when shared state requirements justify it.
- Use a server-state solution for remote data caching and synchronization.
- Use URL parameters for navigable/shareable state.

## 8. Common mistakes

- Putting every state value in one global store.
- Using Context for rapidly changing data consumed by a huge tree without measuring.
- Duplicating derived state.
- Treating API cache data as ordinary local UI state.
- Creating multiple sources of truth for the same value.
