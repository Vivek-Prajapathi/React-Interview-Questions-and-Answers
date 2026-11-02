# Debouncing and Throttling in React

## 1. Why rate-limit event work?

Some browser events fire rapidly. A search input can change on every keystroke; scroll and resize events can fire many times per second. Running expensive work for every event can cause unnecessary requests, calculations, or rendering.

Debouncing and throttling are rate-limiting techniques. They are JavaScript timing patterns, not React-specific features, but React components must handle closures, cleanup, and changing callbacks correctly.

## 2. Debouncing

Debouncing delays execution until a specified period has passed without another event. Each new event resets the timer.

Use cases:
- Search suggestions after the user pauses typing.
- Validating a field after typing stops.
- Saving a draft after a pause.
- Processing resize after resizing settles.

Timeline example with a 300 ms delay: input events at 0 ms, 100 ms, and 180 ms keep resetting the timer. The callback runs around 480 ms if no new event occurs.

### A reusable debounce Hook

```jsx
import { useEffect, useState } from "react";

function useDebouncedValue(value, delay = 300) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}
```

Usage:

```jsx
function SearchBox() {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebouncedValue(query, 400);

  useEffect(() => {
    if (!debouncedQuery.trim()) return;
    // Trigger search using debouncedQuery.
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={event => setQuery(event.target.value)}
      placeholder="Search..."
    />
  );
}
```

The input remains responsive because its value updates immediately; only the downstream work is delayed.

## 3. Throttling

Throttling limits execution to at most once per time interval while events continue. For example, a scroll handler might run at most once every 100 ms.

Use cases:
- Scroll position tracking.
- Pointer movement processing.
- Window resize work while resizing continues.
- Repeated actions where periodic updates are useful.

A simple throttle utility:

```js
function throttle(callback, delay) {
  let lastRun = 0;
  return (...args) => {
    const now = Date.now();
    if (now - lastRun >= delay) {
      lastRun = now;
      callback(...args);
    }
  };
}
```

This leading-edge example runs immediately when the interval permits. Production utilities may support trailing execution, cancellation, and flushing.

## 4. Debounce vs throttle

| Debounce | Throttle |
|---|---|
| Runs after events stop for the delay | Runs at a controlled maximum frequency |
| Repeated events reset the timer | Events during the interval are skipped or coalesced |
| Good for search-as-you-type | Good for continuous scroll tracking |
| May not run while events keep arriving | Continues periodic updates during activity |

## 5. React-specific concerns

- Clear pending timers during cleanup.
- Avoid stale closures: a delayed callback can capture old props or state.
- Do not recreate timers unpredictably on every render.
- Consider request cancellation or ignoring stale responses for search requests.
- Debouncing does not guarantee responses arrive in order.
- A debounced input should usually update its displayed value immediately.

## 6. Debounce a function safely

A function-based debounce can be created with `useMemo` or a ref, but it must have a clear lifecycle and latest-callback strategy. For many UI cases, debouncing a value with an effect is simpler and easier to reason about.

## 7. Common mistakes

1. Debouncing the input's own state update, making typing feel delayed.
2. Forgetting to clear the timer on unmount.
3. Assuming debounce prevents stale network responses.
4. Using debounce for scroll tracking when periodic updates are required.
5. Recreating a throttled function every render without considering its timer state.
6. Treating debounce and throttle as React Hooks rather than general JavaScript patterns.
