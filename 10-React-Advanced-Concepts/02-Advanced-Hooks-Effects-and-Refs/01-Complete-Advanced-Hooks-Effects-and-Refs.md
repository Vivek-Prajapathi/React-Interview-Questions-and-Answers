# Advanced Hooks, Effects, and Refs

A practical, in-depth guide to advanced React Hooks, effect timing, cleanup, refs, custom Hooks, and reliable synchronization with external systems.

## Learning roadmap

Study this chapter in this order:

1. Understand the Rules of Hooks and why call order matters.
2. Understand render, commit, and effect execution.
3. Master `useEffect`, dependency arrays, and cleanup.
4. Learn how to remove unnecessary Effects.
5. Compare `useEffect` and `useLayoutEffect`.
6. Use `useRef` for DOM access and mutable values.
7. Understand stale closures and safe effect patterns.
8. Learn custom Hooks and how to design reusable logic.
9. Understand `useImperativeHandle` and imperative APIs.
10. Apply the concepts to timers, subscriptions, event listeners, fetching, and DOM measurement.
11. Debug common mistakes and apply best practices.

---

## 1. What are React Hooks?

Hooks are functions that let function components use React features such as state, context, refs, and lifecycle-related synchronization.

Before Hooks, stateful logic and lifecycle methods were commonly implemented in class components. Hooks allow function components to use these capabilities and allow stateful logic to be extracted into reusable custom Hooks.

Examples:

```jsx
import { useEffect, useRef, useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);
  const buttonRef = useRef(null);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return (
    <button ref={buttonRef} onClick={() => setCount(c => c + 1)}>
      Count: {count}
    </button>
  );
}
```

In this example:
- `useState` stores data that affects rendering.
- `useRef` stores a reference that persists between renders.
- `useEffect` synchronizes the document title with the current count.

Hooks must be called according to the Rules of Hooks.

## 2. Rules of Hooks

### Rule 1: Call Hooks only at the top level

Call Hooks at the top level of a function component or custom Hook. Do not call them inside conditions, loops, nested functions, or callbacks.

Incorrect:

```jsx
function Profile({ isLoggedIn }) {
  if (isLoggedIn) {
    const [name, setName] = useState("Alex"); // Incorrect
  }

  return <p>Profile</p>;
}
```

Correct:

```jsx
function Profile({ isLoggedIn }) {
  const [name, setName] = useState("Alex");

  if (!isLoggedIn) {
    return <p>Please log in.</p>;
  }

  return <p>Welcome, {name}</p>;
}
```

### Rule 2: Call Hooks only from React function components or custom Hooks

Do not call Hooks from ordinary JavaScript functions, class components, event handlers, or arbitrary utility functions.

```jsx
// Custom Hook: valid
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);
  return width;
}

// Ordinary utility function: do not call Hooks here
function calculateSomething() {
  // useState() here would violate the Rules of Hooks
}
```

### Why does call order matter?

React relies on the order of Hook calls to associate Hook state with the correct component. If a Hook is conditionally skipped, the order can shift between renders and React can associate state with the wrong Hook.

Think of Hook calls as a stable ordered sequence. Every render of a component must execute that sequence consistently.

## 3. Component rendering, commit, and Effects

To understand Effects, separate React's work into two broad phases:

### Render phase

React calls the component function to calculate what the UI should look like. The component should remain pure: it should calculate JSX from props, state, and context without causing external side effects.

A render does not necessarily mean that the result will be committed to the DOM. In concurrent rendering, React may start, pause, restart, or abandon render work.

### Commit phase

React applies the required changes to the host environment, such as the browser DOM. Once the relevant DOM updates are committed, Effects can run according to their scheduling rules.

### Passive Effects

`useEffect` is for synchronization with external systems. React generally allows the browser to paint before running a passive Effect, although interaction-related scheduling can vary. Do not rely on a fixed timing assumption for every interaction.

### Layout Effects

`useLayoutEffect` runs after DOM mutations have been committed but before the browser repaints. It is useful when code must measure or synchronously adjust layout before the user sees the frame.

The key distinction is purpose:
- Render: calculate UI.
- Commit: apply UI changes.
- Effect: synchronize with something outside React.

## 4. `useEffect`: synchronization with external systems

`useEffect` lets a component synchronize with an external system. Examples include:
- Browser event listeners.
- Timers.
- WebSocket connections.
- Third-party widgets.
- Subscriptions.
- Browser APIs.
- Network synchronization when an Effect is genuinely the appropriate mechanism.

Syntax:

```jsx
useEffect(() => {
  // Setup or synchronization

  return () => {
    // Cleanup, if required
  };
}, [dependencies]);
```

The Effect callback runs after a commit. It may return a cleanup function. The cleanup runs before the Effect is set up again with changed dependencies and when the component is removed.

### Example: synchronize the document title

```jsx
import { useEffect, useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return (
    <button onClick={() => setCount(current => current + 1)}>
      Count: {count}
    </button>
  );
}
```

Flow:
1. The component renders using the current `count`.
2. React commits the UI.
3. The Effect updates the document title.
4. When `count` changes, React renders and commits again.
5. The Effect runs again with the new value.

## 5. The three common dependency-array patterns

### Pattern 1: No dependency array

```jsx
useEffect(() => {
  console.log("Effect after every commit");
});
```

The Effect runs after every committed render of the component. Use this only when synchronization truly needs to happen after every commit.

### Pattern 2: Empty dependency array

```jsx
useEffect(() => {
  console.log("Effect for this mount lifecycle");
}, []);
```

The Effect has no reactive dependencies. It runs after the component mounts and its cleanup runs when the component unmounts. In development Strict Mode, React may perform an additional setup-and-cleanup cycle to help reveal unsafe Effects. Therefore, do not interpret an empty array as a guarantee that setup executes exactly once in every environment.

### Pattern 3: Dependency array with values

```jsx
useEffect(() => {
  console.log("Effect when userId changes");
}, [userId]);
```

The Effect runs after the initial commit and again when a dependency changes according to React's dependency comparison.

React compares dependencies using `Object.is`. Include every reactive value from the component scope that the Effect reads, unless the code is structured so that the value is no longer reactive or no longer needs to be read.

## 6. Effect dependencies and reactive values

A reactive value is a value that can change between renders, such as:
- Props.
- State.
- Variables and functions declared inside the component.

If an Effect reads a reactive value, that value normally belongs in the dependency array.

```jsx
function ChatRoom({ roomId }) {
  useEffect(() => {
    const connection = createConnection(roomId);
    connection.connect();

    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Room: {roomId}</h1>;
}
```

When `roomId` changes:
1. React commits the render with the new room ID.
2. React runs cleanup for the previous Effect setup.
3. React runs the new setup using the new room ID.

### Why not use an empty array?

This is incorrect if the Effect reads a changing prop:

```jsx
useEffect(() => {
  const connection = createConnection(roomId);
  connection.connect();

  return () => connection.disconnect();
}, []); // Stale roomId risk
```

The Effect would not resynchronize when `roomId` changes.

### Avoid suppressing dependency warnings

The `exhaustive-deps` lint rule helps identify values used by an Effect but missing from its dependencies. Do not silence the warning simply to force an Effect to run less often. First examine whether the Effect is doing too much or whether the code can be restructured.

## 7. Cleanup functions

Cleanup reverses or stops the external work started by an Effect. Common examples:
- Disconnect a connection.
- Unsubscribe from an event source.
- Clear a timer.
- Remove an event listener.
- Cancel or ignore an outdated asynchronous result.

Example: event listener

```jsx
function OnlineStatus() {
  const [online, setOnline] = useState(navigator.onLine);

  useEffect(() => {
    function updateStatus() {
      setOnline(navigator.onLine);
    }

    window.addEventListener("online", updateStatus);
    window.addEventListener("offline", updateStatus);

    return () => {
      window.removeEventListener("online", updateStatus);
      window.removeEventListener("offline", updateStatus);
    };
  }, []);

  return <p>{online ? "Online" : "Offline"}</p>;
}
```

The same function references are passed to `addEventListener` and `removeEventListener`. This is important because listener removal requires the matching event type, function, and relevant options.

### Cleanup before re-synchronization

```jsx
useEffect(() => {
  const subscription = subscribeToRoom(roomId);

  return () => {
    subscription.unsubscribe();
  };
}, [roomId]);
```

When `roomId` changes, React cleans up the old subscription before setting up the new one.

### Cleanup is not only for unmounting

A common misunderstanding is that cleanup runs only when a component unmounts. It also runs before an Effect is re-run because its dependencies changed.

## 8. Effects and asynchronous requests

Effects can initiate asynchronous work, but an Effect callback itself should not be declared `async`, because an Effect may return only a cleanup function or nothing—not a Promise.

A simple stale-result protection pattern:

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [error, setError] = useState(null);

  useEffect(() => {
    let ignore = false;

    async function loadUser() {
      setError(null);

      try {
        const response = await fetch(`/api/users/${userId}`);
        if (!response.ok) {
          throw new Error("Unable to load user");
        }

        const data = await response.json();

        if (!ignore) {
          setUser(data);
        }
      } catch (err) {
        if (!ignore) {
          setError(err);
        }
      }
    }

    loadUser();

    return () => {
      ignore = true;
    };
  }, [userId]);

  if (error) return <p>{error.message}</p>;
  if (!user) return <p>Loading...</p>;

  return <h1>{user.name}</h1>;
}
```

The cleanup marks the previous request as outdated so its result cannot update this component's state. For APIs that support cancellation, an `AbortController` can cancel the request:

```jsx
useEffect(() => {
  const controller = new AbortController();

  async function loadUser() {
    try {
      const response = await fetch(`/api/users/${userId}`, {
        signal: controller.signal
      });

      if (!response.ok) throw new Error("Request failed");

      const data = await response.json();
      setUser(data);
    } catch (error) {
      if (error.name !== "AbortError") {
        setError(error);
      }
    }
  }

  loadUser();

  return () => controller.abort();
}, [userId]);
```

For application data fetching, a framework or dedicated data-fetching/cache library is often preferable because it can handle caching, deduplication, race conditions, retries, and server rendering more comprehensively.

## 9. Avoid unnecessary Effects

Effects are not a general-purpose place to put every piece of logic. Use them when synchronizing with an external system.

### Derived values should usually be calculated during render

Avoid:

```jsx
const [fullName, setFullName] = useState("");

useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);
```

Prefer:

```jsx
const fullName = `${firstName} ${lastName}`;
```

The preferred version avoids an unnecessary state variable, extra render, and synchronization problem.

### User-event logic belongs in the event handler

Avoid using an Effect to detect that a user clicked a button indirectly through state when the action can happen directly in the handler.

Prefer:

```jsx
function handlePurchase() {
  sendPurchaseRequest(productId);
}
```

Use an Effect when the component must keep an external system synchronized with current rendered state—not merely to respond to a particular user event.

## 10. `useLayoutEffect`

`useLayoutEffect` has a similar API to `useEffect`, but its timing is different.

```jsx
useLayoutEffect(() => {
  // Read layout and, if necessary, synchronously update layout
}, [dependencies]);
```

It runs after React commits DOM changes and before the browser repaints. This makes it useful for layout measurement and corrections that must happen before the user sees the frame.

### Example: measure an element

```jsx
function Tooltip() {
  const tooltipRef = useRef(null);
  const [height, setHeight] = useState(0);

  useLayoutEffect(() => {
    const element = tooltipRef.current;

    if (element) {
      setHeight(element.getBoundingClientRect().height);
    }
  }, []);

  return (
    <div ref={tooltipRef}>
      Tooltip height: {height}px
    </div>
  );
}
```

### `useEffect` vs `useLayoutEffect`

| Feature | `useEffect` | `useLayoutEffect` |
|---|---|---|
| Main purpose | External synchronization | Pre-paint layout work |
| Timing | Passive Effect after commit; often after paint | After DOM mutations, before repaint |
| Can block paint | Generally does not block paint in the same way | Yes, synchronous work can block repaint |
| Typical use | Subscriptions, timers, connections | Measure layout, prevent visual flicker |
| Default choice | Prefer this | Use only when necessary |

Do not choose `useLayoutEffect` simply because it feels more immediate. Its synchronous work can hurt responsiveness. In server-rendered applications, layout Effects cannot measure browser layout on the server; use an appropriate client-only strategy where needed.

## 11. `useRef`: persistent mutable storage

`useRef` returns an object with a `current` property. The same ref object is retained across renders of the component.

```jsx
const ref = useRef(initialValue);
```

Changing `ref.current` does not trigger a re-render.

Use refs for:
- DOM nodes.
- Timer IDs.
- Mutable values that do not directly determine rendered output.
- Imperative handles.
- Values that need to persist between renders without scheduling a render.

### Ref vs state

| Feature | `useState` | `useRef` |
|---|---|---|
| Persists across renders | Yes | Yes |
| Update triggers render | Yes, when state changes | No |
| Intended for rendered UI | Yes | Usually not |
| Access | State value | `ref.current` |
| Typical use | Count, form value, visible status | DOM node, timer ID, mutable instance |

Do not use a ref as a replacement for state when the UI must update after the value changes.

## 12. Accessing DOM elements with `useRef`

```jsx
import { useRef } from "react";

function SearchBox() {
  const inputRef = useRef(null);

  function focusInput() {
    inputRef.current?.focus();
  }

  return (
    <>
      <input ref={inputRef} placeholder="Search..." />
      <button onClick={focusInput}>Focus input</button>
    </>
  );
}
```

How it works:
1. `useRef(null)` creates a persistent ref object.
2. React assigns the DOM input element to `inputRef.current` when the element is mounted.
3. The event handler reads the DOM node and calls its `focus()` method.
4. The ref object remains stable across renders.

A DOM ref is generally `null` before the element is mounted and after it is removed.

## 13. Storing mutable values with refs

### Example: timer ID

```jsx
function Stopwatch() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef(null);

  function start() {
    if (intervalRef.current !== null) return;

    intervalRef.current = setInterval(() => {
      setSeconds(value => value + 1);
    }, 1000);
  }

  function stop() {
    clearInterval(intervalRef.current);
    intervalRef.current = null;
  }

  useEffect(() => {
    return () => clearInterval(intervalRef.current);
  }, []);

  return (
    <>
      <p>{seconds}</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </>
  );
}
```

The seconds value belongs in state because it is displayed. The interval ID belongs in a ref because it is an imperative handle and changing it does not need to render the UI.

## 14. Stale closures

A closure captures variables from the render in which it was created. An asynchronous callback or long-lived subscription may continue to use an older value if it is not updated or re-subscribed appropriately.

Example of a stale closure:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      console.log(count);
    }, 1000);

    return () => clearInterval(id);
  }, []); // Captures the initial count

  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

The interval callback captures the initial `count`, because the Effect is not re-created when `count` changes.

Possible solutions depend on the goal:

**Solution A: Include the dependency**

```jsx
useEffect(() => {
  const id = setInterval(() => console.log(count), 1000);
  return () => clearInterval(id);
}, [count]);
```

This re-creates the interval when `count` changes.

**Solution B: Use a functional state update**

When the callback only needs to update state based on its previous value:

```jsx
useEffect(() => {
  const id = setInterval(() => {
    setCount(current => current + 1);
  }, 1000);

  return () => clearInterval(id);
}, []);
```

**Solution C: Use a ref for a latest mutable value when appropriate**

A ref can hold a value that callbacks need to read without re-creating the external subscription. Keep the ref synchronized carefully, and prefer simpler dependency-based solutions when they meet the requirement.

## 15. Custom Hooks

A custom Hook is a JavaScript function whose name begins with `use` and that can call other Hooks. It lets you reuse stateful logic across components.

Custom Hooks share logic, not a single shared state instance. Each component calling a custom Hook gets its own Hook state unless the logic deliberately connects to a shared external store or context.

### Example: `useWindowWidth`

```jsx
import { useEffect, useState } from "react";

function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    function updateWidth() {
      setWidth(window.innerWidth);
    }

    window.addEventListener("resize", updateWidth);
    return () => window.removeEventListener("resize", updateWidth);
  }, []);

  return width;
}

function WindowSize() {
  const width = useWindowWidth();
  return <p>Window width: {width}px</p>;
}
```

The custom Hook owns the reusable behavior. The component decides how to display the returned value.

### Custom Hook design principles

- Name it with the `use` prefix.
- Call Hooks only at its top level.
- Keep its responsibility focused.
- Return the smallest useful API.
- Clean up subscriptions and timers.
- Avoid hiding surprising side effects behind unclear names.
- Document important parameters and return values.

## 16. `useImperativeHandle`

`useImperativeHandle` lets a component customize the value exposed through a ref. It is useful when a parent needs a small imperative API rather than direct access to a child's entire DOM node.

```jsx
import { forwardRef, useImperativeHandle, useRef } from "react";

const CustomInput = forwardRef(function CustomInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focus() {
      inputRef.current?.focus();
    },
    clear() {
      if (inputRef.current) {
        inputRef.current.value = "";
      }
    }
  }), []);

  return <input ref={inputRef} {...props} />;
});

function Parent() {
  const customInputRef = useRef(null);

  return (
    <>
      <CustomInput ref={customInputRef} />
      <button onClick={() => customInputRef.current?.focus()}>
        Focus
      </button>
      <button onClick={() => customInputRef.current?.clear()}>
        Clear
      </button>
    </>
  );
}
```

The parent receives the custom `{ focus, clear }` handle rather than the underlying input node.

React 19 allows `ref` to be passed as a prop to function components, so `forwardRef` is no longer necessary for new React 19 component patterns. `forwardRef` remains common in existing code and libraries. The exact implementation should match the React version used by the project.

## 17. Effects and Strict Mode

In development, when Strict Mode is enabled, React performs an additional setup-and-cleanup cycle for Effects to help reveal missing cleanup and non-reversible setup logic.

For example, a subscription should be safe to set up, clean up, and set up again.

Do not fix a Strict Mode duplicate by adding a ref flag that prevents the second setup. Instead, make setup and cleanup correct and ensure that external work is properly reversed.

Strict Mode's development checks are not the same as production Effect scheduling.

## 18. Common practical patterns

### Debounced work

A timeout can be created and cleaned up when the value changes:

```jsx
useEffect(() => {
  const timeoutId = setTimeout(() => {
    setDebouncedValue(value);
  }, 500);

  return () => clearTimeout(timeoutId);
}, [value]);
```

Every new value clears the previous timeout and starts a new one.

### Subscribe and unsubscribe

```jsx
useEffect(() => {
  const unsubscribe = store.subscribe(handleChange);
  return unsubscribe;
}, [store]);
```

The cleanup is the unsubscribe function returned by the subscription API.

### Third-party widget synchronization

```jsx
useEffect(() => {
  const widget = new MapWidget(containerRef.current);
  widget.setZoom(zoomLevel);

  return () => widget.destroy();
}, []);
```

If the widget needs to respond to changing props, synchronize those values in a separate Effect or include them in the relevant dependencies, according to the widget API.

### Document event listener

```jsx
useEffect(() => {
  function handleKeyDown(event) {
    if (event.key === "Escape") {
      closeDialog();
    }
  }

  window.addEventListener("keydown", handleKeyDown);
  return () => window.removeEventListener("keydown", handleKeyDown);
}, [closeDialog]);
```

Ensure `closeDialog` has a stable identity if appropriate, or structure the component so the dependency is correctly managed.

## 19. Common mistakes and how to avoid them

1. **Calling Hooks conditionally:** move Hook calls to the top level.
2. **Missing dependencies:** include reactive values or restructure the Effect.
3. **Using Effects for derived state:** calculate derived values during render.
4. **Forgetting cleanup:** unsubscribe, clear timers, remove listeners, or cancel work.
5. **Using `useLayoutEffect` everywhere:** prefer `useEffect` unless pre-paint work is necessary.
6. **Storing visible UI state in refs:** use state when a change must render.
7. **Mutating refs during render without a specific supported pattern:** perform imperative mutations in event handlers or Effects.
8. **Making the Effect callback `async`:** define and call an inner async function.
9. **Ignoring stale closures:** understand which render's values a callback captures.
10. **Using a ref flag to defeat Strict Mode checks:** fix setup and cleanup instead.
11. **Treating an empty dependency array as a universal run-once guarantee:** understand development Strict Mode and remounts.
12. **Using Effects for click-specific actions:** put the action in the event handler.

## 20. Debugging checklist

When an Effect runs too often:
- Is a dependency changing on every render?
- Are you creating a new object or function during render and using it as a dependency?
- Can the object or function be moved inside the Effect?
- Is the Effect doing work that can be calculated during render?
- Is Strict Mode's development setup-and-cleanup cycle being mistaken for a production issue?

When an Effect does not update:
- Is a reactive value missing from the dependency array?
- Is a stale closure being used?
- Is the external subscription still connected to old data?
- Is the component actually mounted?

When an Effect leaks resources:
- Does setup have a matching cleanup?
- Are the same listener function and options used for removal?
- Are timers cleared?
- Are subscriptions unsubscribed?
- Are outdated async results ignored or aborted?

## 21. Best practices

- Treat components as pure calculations of UI.
- Use Effects only to synchronize with external systems.
- Include all reactive dependencies.
- Write cleanup that mirrors setup.
- Prefer derived values over duplicated state.
- Prefer `useEffect` unless layout must be measured or corrected before paint.
- Use state for values that affect rendering.
- Use refs for mutable values that do not need to trigger rendering.
- Keep custom Hooks focused and predictable.
- Test setup and cleanup, including remount and dependency changes.
- Follow the Hooks lint rules rather than suppressing warnings casually.

## 22. Quick revision

| Hook | Main purpose |
|---|---|
| `useEffect` | Synchronize with external systems after commit |
| `useLayoutEffect` | Read or adjust layout before browser repaint |
| `useRef` | Persist a mutable value without causing a render |
| `useImperativeHandle` | Expose a controlled imperative API through a ref |
| Custom Hook | Reuse stateful logic across components |

**Remember:** Effects are for synchronization, state is for rendered data, and refs are for persistent mutable values that do not themselves need to trigger rendering.
