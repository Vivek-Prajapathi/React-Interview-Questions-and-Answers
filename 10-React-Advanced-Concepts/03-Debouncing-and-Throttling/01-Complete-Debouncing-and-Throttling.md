# Debouncing and Throttling in React

## Learning Roadmap

1. Understand why high-frequency events create performance problems.
2. Learn the difference between debouncing and throttling.
3. Understand timers, closures, and cleanup.
4. Implement debounce and throttle utilities in plain JavaScript.
5. Use them safely in React components.
6. Apply them to search, resize, scroll, and button interactions.
7. Handle stale values, unmounting, and changing dependencies.
8. Test, debug, and choose the correct technique.

## 1. Why Debouncing and Throttling Matter

Some browser events can fire many times in a short period:

- `input` and `change` while a user types
- `scroll` while a page moves
- `resize` while a window is resized
- `mousemove` while a pointer moves
- repeated button clicks

Running expensive work for every event can cause unnecessary API requests, calculations, renders, or other work.

**Debouncing** and **throttling** control how often a function is allowed to execute. They do not make the underlying operation faster; they reduce how frequently it is started.

## 2. Debouncing

Debouncing delays execution until a specified period has passed without another call.

If calls keep arriving before the delay expires, the timer is restarted. The function runs after the calls stop for the full delay period.

### Timeline

Suppose the delay is 300 ms and calls arrive at 0 ms, 100 ms, and 220 ms.

- At 0 ms, schedule execution for 300 ms.
- At 100 ms, cancel the previous timer and schedule for 400 ms.
- At 220 ms, cancel the previous timer and schedule for 520 ms.
- If no more calls arrive, execute at approximately 520 ms.

### Common use cases

- Search suggestions while typing
- Validating a field after the user pauses
- Saving a draft after typing stops
- Processing a filter after the user finishes changing it

### Basic JavaScript implementation

```js
function debounce(callback, delay) {
  let timerId;

  return function (...args) {
    clearTimeout(timerId);

    timerId = setTimeout(() => {
      callback.apply(this, args);
    }, delay);
  };
}
```

Example:

```js
const searchLater = debounce((value) => {
  console.log("Search for:", value);
}, 300);

searchLater("r");
searchLater("re");
searchLater("rea");
searchLater("react");
```

Only the final call is executed if all calls occur within the debounce window.

### Important details

- `timerId` is retained by the returned function's closure.
- `clearTimeout` cancels the previous pending callback.
- `...args` forwards every argument from the latest call.
- `apply(this, args)` preserves the calling context and arguments.
- A trailing debounce runs after the last call.
- A leading debounce can run immediately on the first call, depending on the implementation.

## 3. Throttling

Throttling limits execution to at most once during a specified time interval.

If calls continue to arrive, the function executes at a controlled rate instead of running for every event.

### Timeline

Suppose the throttle interval is 300 ms.

- A call at 0 ms can execute immediately.
- Calls at 50 ms, 100 ms, and 200 ms are suppressed or queued, depending on the implementation.
- A later call can execute after the interval has elapsed.

### Common use cases

- Tracking scroll position
- Handling window resize work at a controlled rate
- Pointer movement tracking
- Limiting repeated actions

### Basic leading-edge implementation

```js
function throttle(callback, delay) {
  let lastExecutionTime = 0;

  return function (...args) {
    const now = Date.now();

    if (now - lastExecutionTime >= delay) {
      lastExecutionTime = now;
      callback.apply(this, args);
    }
  };
}
```

This implementation executes immediately when the interval permits. Calls inside the interval are ignored. It is a leading-edge throttle, not a trailing throttle.

### Timer-based throttle

A timer-based implementation can schedule a trailing execution:

```js
function throttleWithTrailing(callback, delay) {
  let timerId = null;

  return function (...args) {
    if (timerId !== null) return;

    timerId = setTimeout(() => {
      timerId = null;
      callback.apply(this, args);
    }, delay);
  };
}
```

This version waits before executing the first call and then permits one execution per interval. It is not identical to the immediate leading-edge version.

## 4. Debouncing vs. Throttling

| Aspect | Debouncing | Throttling |
|---|---|---|
| Main rule | Run after calls stop for a delay | Limit execution frequency |
| Repeated calls | Reset the timer | Enforce an interval |
| Typical behavior | One execution after a burst | Periodic executions during a burst |
| Search suggestions | Usually a strong fit | Usually not ideal |
| Scroll tracking | Useful when only the final position matters | Useful for periodic updates |
| Resize handling | Useful after resizing stops | Useful during resizing |

**Decision rule:** Use debounce when the final action matters after the user pauses. Use throttle when updates should continue, but only at a controlled frequency.

## 5. React-Specific Considerations

A utility created inside a component body is recreated on every render. If it owns a timer in a local variable, that timer state may be lost when the component renders again.

For a stable debounced or throttled function, preserve the utility instance across renders with `useMemo` or `useRef`. Also consider which callback and values it should use.

### Debounced search with `useMemo`

```jsx
import { useEffect, useMemo, useState } from "react";

function debounce(callback, delay) {
  let timerId;

  const debounced = (...args) => {
    clearTimeout(timerId);
    timerId = setTimeout(() => callback(...args), delay);
  };

  debounced.cancel = () => clearTimeout(timerId);

  return debounced;
}

function SearchBox() {
  const [query, setQuery] = useState("");

  const search = useMemo(
    () =>
      debounce((value) => {
        console.log("Fetch search results for:", value);
      }, 400),
    []
  );

  useEffect(() => {
    return () => search.cancel();
  }, [search]);

  function handleChange(event) {
    const value = event.target.value;
    setQuery(value);
    search(value);
  }

  return (
    <input
      value={query}
      onChange={handleChange}
      placeholder="Search products"
    />
  );
}
```

The debounced function is created once for this component instance. The cleanup cancels a pending timer when the component unmounts.

For a real API request, also handle loading, errors, response ordering, and cancellation of in-flight requests. Debouncing only controls when requests start; it does not automatically cancel a request that has already started.

## 6. A Reusable Debounce Utility with Cancellation

A reusable utility should expose cancellation so a component can clear pending work.

```js
export function debounce(callback, delay) {
  let timerId;

  function debounced(...args) {
    clearTimeout(timerId);

    timerId = setTimeout(() => {
      callback.apply(this, args);
    }, delay);
  }

  debounced.cancel = () => {
    clearTimeout(timerId);
    timerId = undefined;
  };

  return debounced;
}
```

Usage:

```js
const debouncedSave = debounce(saveDraft, 500);

// Schedule or reset the pending save.
debouncedSave(formData);

// Cancel a pending save when necessary.
debouncedSave.cancel();
```

## 7. A React Hook for Debouncing a Value

Sometimes the component needs a debounced value rather than a debounced callback.

```jsx
import { useEffect, useState } from "react";

function useDebouncedValue(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timerId = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timerId);
  }, [value, delay]);

  return debouncedValue;
}
```

Example:

```jsx
function SearchBox() {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebouncedValue(query, 400);

  useEffect(() => {
    if (!debouncedQuery.trim()) return;

    console.log("Search API for:", debouncedQuery);
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={(event) => setQuery(event.target.value)}
      placeholder="Search"
    />
  );
}
```

Each change schedules a new timer. Cleanup clears the previous timer. The debounced value updates only after the user has stopped changing the value for the delay.

## 8. Avoiding Stale Closures

A closure captures values from the render in which it was created. A debounced callback created once may continue to refer to an old state value or prop if it reads those values directly.

A simple strategy is to pass the latest value as an argument:

```js
const debouncedSearch = useMemo(
  () => debounce((latestQuery) => {
    searchApi(latestQuery);
  }, 400),
  []
);

// Pass the current value explicitly.
debouncedSearch(query);
```

For callbacks that must read the latest props or state without recreating the debounced function, a ref can hold the latest callback:

```jsx
import { useEffect, useMemo, useRef } from "react";

function useDebouncedCallback(callback, delay) {
  const callbackRef = useRef(callback);

  useEffect(() => {
    callbackRef.current = callback;
  }, [callback]);

  const debounced = useMemo(() => {
    let timerId;

    const fn = (...args) => {
      clearTimeout(timerId);
      timerId = setTimeout(() => {
        callbackRef.current(...args);
      }, delay);
    };

    fn.cancel = () => {
      clearTimeout(timerId);
      timerId = undefined;
    };

    return fn;
  }, [delay]);

  useEffect(() => () => debounced.cancel(), [debounced]);

  return debounced;
}
```

This pattern keeps the timer-owning function stable while updating the callback reference. When `delay` changes, a new debounced function is created and the old one is cleaned up.

## 9. Throttling a Scroll Handler in React

```jsx
import { useEffect } from "react";

function throttle(callback, delay) {
  let lastExecutionTime = 0;

  return (...args) => {
    const now = Date.now();

    if (now - lastExecutionTime >= delay) {
      lastExecutionTime = now;
      callback(...args);
    }
  };
}

function ScrollTracker() {
  useEffect(() => {
    const handleScroll = throttle(() => {
      console.log("Scroll position:", window.scrollY);
    }, 200);

    window.addEventListener("scroll", handleScroll);

    return () => {
      window.removeEventListener("scroll", handleScroll);
    };
  }, []);

  return <p>Scroll the page and check the console.</p>;
}
```

The same function reference must be passed to `removeEventListener` that was passed to `addEventListener`. In this example, the handler is created inside the Effect and removed by the cleanup.

For production code, consider whether the throttle needs trailing execution, whether it should be cancelled, and whether `requestAnimationFrame` is a better fit for visual updates.

## 10. Cleanup and Component Lifecycle

Pending timers should generally be cancelled when they are no longer needed.

Why cleanup matters:

- Prevents delayed work after a component has unmounted.
- Avoids updating state from an obsolete scheduled callback.
- Prevents duplicate timers when dependencies change.
- Makes behavior easier to reason about in React Strict Mode.

Common cleanup pattern:

```jsx
useEffect(() => {
  const handler = debounce(() => {
    // Work
  }, 300);

  window.addEventListener("resize", handler);

  return () => {
    window.removeEventListener("resize", handler);
    handler.cancel?.();
  };
}, []);
```

Removing an event listener does not automatically cancel a timer that the listener already scheduled. Both may need cleanup.

## 11. Debounce, Throttle, and `requestAnimationFrame`

These techniques solve related but different problems.

- **Debounce:** wait until activity pauses.
- **Throttle:** cap how frequently work runs.
- **`requestAnimationFrame`:** schedule visual work for the browser's next repaint opportunity.

For scroll-driven visual effects, `requestAnimationFrame` can be appropriate because it coordinates updates with painting. It is not a general replacement for debouncing or API request rate limiting.

## 12. Common Mistakes

1. **Creating the debounced function on every render.** Its timer state is repeatedly reset or discarded.
2. **Forgetting cleanup.** A pending timer can run after the component no longer needs it.
3. **Using debounce for continuous feedback.** The callback may not run until activity stops.
4. **Using throttle for a search box without considering UX.** Intermediate queries may be sent while the user is still typing.
5. **Assuming debounce cancels an in-flight fetch.** It only delays starting work; use `AbortController` or another request-management strategy for in-flight requests.
6. **Ignoring stale closures.** A callback may read values from an older render.
7. **Removing the wrong event listener.** `removeEventListener` needs the same handler reference and matching options.
8. **Not defining leading/trailing behavior.** Different implementations have different timing semantics.
9. **Assuming throttling queues every event.** Many simple throttles discard calls inside the interval.
10. **Testing only with one event.** The key behavior appears when many calls happen close together.

## 13. Testing and Debugging

Test with fake timers when using a test framework that supports them.

A debounce test should verify:
- The callback does not run before the delay.
- Repeated calls reset the delay.
- Only the latest arguments are used.
- Cancellation prevents a pending callback.

A throttle test should verify:
- The callback runs at the expected permitted times.
- Calls inside the interval follow the intended leading/trailing policy.
- Timer cleanup works if the implementation uses timers.

Always test the exact implementation's timing contract. A leading-only throttle and a leading-plus-trailing throttle are intentionally different.

## 14. Best Practices

- Choose debounce or throttle based on the interaction's desired behavior.
- Keep timer-owning functions stable across renders.
- Pass current values as arguments when possible.
- Use refs when a stable scheduled callback must read the latest callback.
- Cancel pending timers during cleanup.
- Keep API request cancellation and response-race handling separate from debounce.
- Document whether the utility is leading, trailing, or both.
- Prefer a tested utility library when a project needs advanced options such as `leading`, `trailing`, `maxWait`, or cancellation and flushing.

## 15. Quick Revision

- Debounce: execute after a quiet period.
- Throttle: limit execution frequency.
- Search input: commonly debounce.
- Scroll tracking: commonly throttle.
- `clearTimeout`: cancels a pending timer.
- Closure: retains timer state between calls.
- `useMemo`/`useRef`: can preserve a utility instance across renders.
- `useEffect` cleanup: remove listeners and cancel pending timers.
- Debounce does not cancel requests already in flight.
- Stale closures can be avoided by passing current values or reading a latest callback from a ref.

## Final Interview Summary

Debouncing and throttling are techniques for controlling high-frequency function calls. Debouncing waits until calls stop for a configured delay, making it useful for search inputs and autosave. Throttling allows a function to execute at most once per configured interval, making it useful for scroll and resize processing. In React, I preserve the timer-owning function across renders, clean up pending timers and event listeners, and handle stale closures and in-flight API requests separately.
