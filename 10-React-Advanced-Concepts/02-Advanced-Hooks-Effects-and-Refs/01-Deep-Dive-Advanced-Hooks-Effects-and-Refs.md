# Advanced Hooks, Effects, and Refs

## 1. Rules of Hooks

Call Hooks at the top level of React function components or custom Hooks. Do not call them inside loops, conditions, nested functions, or ordinary JavaScript functions. This stable call order lets React associate hook state with the correct call on every render.

## 2. useEffect: synchronization with external systems

`useEffect` synchronizes a component with something outside React: subscriptions, timers, browser APIs, or network connections.

```jsx
useEffect(() => {
  const connection = createConnection(roomId);
  connection.connect();

  return () => connection.disconnect();
}, [roomId]);
```

The cleanup runs before the effect is set up again with changed dependencies and when the component unmounts. The dependency list must include reactive values used by the effect. Do not suppress dependency warnings without understanding the data flow.

Avoid effects for derived values:

```jsx
// Prefer this
const fullName = `${firstName} ${lastName}`;

// Usually unnecessary
useEffect(() => setFullName(`${firstName} ${lastName}`), [firstName, lastName]);
```

## 3. useLayoutEffect

`useLayoutEffect` runs after React has changed the DOM but before the browser paints. Use it when you must measure layout and synchronously apply a correction to avoid visible flicker. It can block painting, so prefer `useEffect` when pre-paint timing is not required.

## 4. useRef

A ref stores a mutable value that persists across renders without causing a render when changed. Common uses:
- Accessing a DOM node.
- Storing a timer ID.
- Keeping a previous value.
- Holding an imperative object.

```jsx
const inputRef = useRef(null);

function focusInput() {
  inputRef.current?.focus();
}

return <input ref={inputRef} />;
```

Do not use refs as a substitute for state when the value must appear in the UI.

## 5. useImperativeHandle

`useImperativeHandle` customizes the value exposed through a ref. It is useful when a parent needs a small imperative API, such as `focus()` or `scrollToTop()`. Prefer declarative props for normal UI behavior. In React 19, `ref` can be passed as a prop to function components; older patterns commonly used `forwardRef`.

## 6. useMemo and useCallback

`useMemo` caches a calculated value between renders when dependencies are unchanged. `useCallback` caches a function identity. They are performance tools, not correctness tools.

```jsx
const filtered = useMemo(
  () => items.filter(item => item.active),
  [items]
);

const handleSelect = useCallback(id => {
  setSelectedId(id);
}, []);
```

Memoization has overhead and should be used when it avoids meaningful work or stabilizes props for a memoized child.

## 7. useReducer

`useReducer` centralizes state transitions in a reducer function. It is useful when state has multiple related fields or transitions.

```jsx
function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "reset":
      return { count: 0 };
    default:
      return state;
  }
}
```

Reducers should be pure: return a new state and do not perform network requests or mutate the existing state.

## 8. Custom Hooks

A custom Hook extracts reusable stateful logic. Its name must begin with `use`, and it can call other Hooks.

```jsx
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    function update() {
      setWidth(window.innerWidth);
    }
    window.addEventListener("resize", update);
    return () => window.removeEventListener("resize", update);
  }, []);

  return width;
}
```

A custom Hook shares logic, not one shared state instance. Each component calling it gets its own Hook state unless the logic connects to a shared external store or Context.

## 9. Common mistakes

- Missing effect dependencies.
- Using effects to derive values.
- Forgetting cleanup for subscriptions and timers.
- Mutating ref values during render in ways that affect output.
- Memoizing every function without profiling.
- Expecting a custom Hook to share state automatically.
