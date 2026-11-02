# Chapter 9 – React Scenarios: Practical Scenarios, Debugging, and Output-Based Learning

> A concept-first guide to reasoning about real React applications. This chapter is deliberately written as a learning resource—not as interview questions and answers.

## Learning objectives

By the end of this chapter, you should be able to:
- Trace how React state, props, effects, rendering, and reconciliation interact.
- Diagnose common UI bugs using evidence instead of random code changes.
- Predict the behavior of JavaScript and React code snippets.
- Design reliable forms, lists, API flows, modals, authentication flows, and reusable components.
- Recognize stale closures, race conditions, unnecessary renders, and state synchronization problems.
- Explain the trade-offs behind a solution and test that it works.

---

## 1. A repeatable method for solving React scenarios

A scenario is a description of observable behavior: a button does nothing, a list shows old data, a component renders too often, or a page briefly displays protected content. The first task is not to edit code. It is to convert the symptom into a testable hypothesis.

### 1.1 Reproduce the symptom

Record:
1. The exact user action.
2. The expected result.
3. The actual result.
4. Whether the issue happens always or intermittently.
5. The relevant route, user role, data, and browser state.

A useful bug report is: “On `/profile`, changing the name and clicking Save shows a success toast, but after refresh the old name returns.” This is more useful than “profile is broken,” because it points toward persistence or request handling.

### 1.2 Follow the data path

Trace data from its source to its display:

`User action → event handler → state update / API request → state or cache → props → render`

For an input, inspect the `value`, `onChange`, state variable, and handler. For a fetched list, inspect the request, response, state update, and list rendering. A bug usually appears at one of these boundaries.

### 1.3 Separate facts from assumptions

Use logs, React DevTools, Network panel, and small isolated tests. Check the actual request payload and response rather than assuming the API received the expected values. Avoid changing several unrelated things at once; otherwise, you will not know which change fixed the issue.

### 1.4 Verify the fix

Test the original reproduction steps, nearby edge cases, refresh behavior, loading and error states, and the relevant mobile layout. A fix is complete only when it prevents the bug without breaking adjacent behavior.

---

## 2. React rendering and state-update scenarios

### 2.1 What causes a component to render?

A function component is called again when React needs to calculate its next UI. Common causes include:
- Its state changes.
- Its parent renders and React renders the child as part of the update.
- A context value it consumes changes.
- An external store subscription reports a change.

A render is not the same as a DOM update. React can call a component, compare the resulting element description with the previous one, and determine that no DOM mutation is needed. Therefore, seeing a component function run does not automatically mean the browser rebuilt its DOM.

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  console.log("Counter rendered");

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

Clicking the button changes state, so React schedules another render. React then commits the necessary DOM change.

### 2.2 State is a render snapshot

State values are snapshots associated with a particular render. Calling a setter requests a future render; it does not mutate the variable inside the currently running function.

```jsx
function Example() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
    console.log(count); // value from this render
  }

  return <button onClick={handleClick}>{count}</button>;
}
```

The log uses the current render's `count`. The next render receives the updated value.

### 2.3 Functional state updates

When the next state depends on the previous state, use the updater form. This is especially important when multiple updates are queued.

```jsx
setCount(previousCount => previousCount + 1);
```

Compare:

```jsx
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
```

All three expressions read the same render snapshot. They may request the same next value. By contrast:

```jsx
setCount(c => c + 1);
setCount(c => c + 1);
setCount(c => c + 1);
```

Each updater receives the result of the preceding queued updater, so the value increases by three.

### 2.4 Batching

React can group multiple state updates into one render to avoid unnecessary work. Do not rely on seeing an intermediate UI after every setter call. Batching behavior depends on the React version and execution environment; modern React supports automatic batching in more situations than older versions.

### 2.5 Updating objects and arrays immutably

React state should be treated as read-only. Create a new object or array rather than mutating the existing value.

```jsx
setUser(previous => ({
  ...previous,
  city: "Bengaluru"
}));

setTasks(previous =>
  previous.map(task =>
    task.id === targetId ? { ...task, completed: true } : task
  )
);
```

Mutation can make change detection unreliable and can accidentally alter values used by earlier renders. The spread operator is shallow: nested objects still need to be copied at the levels being changed.

### 2.6 State belongs to a component position

React preserves state while the same component remains at the same position in the UI tree. Changing a component's key or replacing its component type can reset its state. This is useful for intentionally resetting a form when switching between records, but harmful when unstable keys cause accidental resets.

---

## 3. Props, state, and derived data

### 3.1 Props are inputs; state is local memory

Props are passed from a parent. A child should normally treat them as read-only. State represents information that changes over time and is owned by the component or a shared state layer.

Avoid copying a prop into state without a clear reason:

```jsx
function Profile({ name }) {
  const [localName, setLocalName] = useState(name);
  // localName will not automatically follow later name changes
}
```

This creates two sources of truth. If the value should always reflect the prop, render the prop directly. If the user is editing a draft, model that draft intentionally and define when it resets or saves.

### 3.2 Derived state

Do not store values that can be calculated from existing state unless there is a strong reason.

```jsx
const [products, setProducts] = useState([]);
const [search, setSearch] = useState("");

const filteredProducts = products.filter(product =>
  product.title.toLowerCase().includes(search.toLowerCase())
);
```

Storing `filteredProducts` separately can create synchronization bugs. Derive it during rendering; use memoization only when measurement shows the calculation is expensive.

### 3.3 Lifting state up

When sibling components need the same changing value, move ownership to their closest common parent and pass the value and event handlers down. This creates one source of truth.

### 3.4 Controlled and uncontrolled inputs

A controlled input receives its current value from React state and reports changes through `onChange`.

```jsx
const [email, setEmail] = useState("");

<input
  name="email"
  value={email}
  onChange={event => setEmail(event.target.value)}
/>
```

The `name` attribute matters when a generic handler reads `event.target.name`. If it is missing, the handler may not know which field to update. An uncontrolled input stores its current value in the DOM and is commonly accessed with a ref or `FormData`.

Do not switch an input between controlled and uncontrolled modes during its lifetime. Initialize text values to an empty string rather than `undefined` when using a controlled input.

---

## 4. Event-handler and closure scenarios

### 4.1 Passing a function versus calling it

Correct:

```jsx
<button onClick={handleSave}>Save</button>
```

Incorrect when the intention is to wait for a click:

```jsx
<button onClick={handleSave()}>Save</button>
```

The second form invokes the function while rendering. Use an arrow function when arguments are needed:

```jsx
<button onClick={() => handleDelete(product.id)}>Delete</button>
```

### 4.2 Closures and stale values

A closure retains access to variables from the render in which it was created. An asynchronous callback can therefore read an older state snapshot.

```jsx
useEffect(() => {
  const id = setInterval(() => {
    setCount(count + 1);
  }, 1000);

  return () => clearInterval(id);
}, []);
```

This interval captures the initial `count`. Use a functional update:

```jsx
useEffect(() => {
  const id = setInterval(() => {
    setCount(current => current + 1);
  }, 1000);

  return () => clearInterval(id);
}, []);
```

A dependency array is not a tool for silencing the linter. It describes the reactive values an effect uses. If dependencies cause an unwanted loop, reconsider the effect's design rather than omitting dependencies blindly.

### 4.3 `this` is usually not the issue in function components

Function components use hooks and closures rather than class component instance methods. When debugging a handler, inspect whether it is being called, what arguments it receives, and which render snapshot it reads.

---

## 5. `useEffect` scenarios and lifecycle reasoning

### 5.1 Effects synchronize with external systems

An effect is for synchronizing a component with something outside React: a network connection, browser API, event listener, timer, or imperative third-party widget. It is not required for every calculation or for responding to a button click.

```jsx
useEffect(() => {
  document.title = `Cart (${cartCount})`;
}, [cartCount]);
```

### 5.2 Dependency array patterns

```jsx
useEffect(() => {
  // after every committed render
});

useEffect(() => {
  // after initial mount; cleanup on unmount
}, []);

useEffect(() => {
  // when userId changes
}, [userId]);
```

The dependency list must include reactive values read by the effect. React compares dependencies using `Object.is`.

### 5.3 Cleanup

Cleanup runs before an effect is set up again with changed dependencies and when the component is removed. It is used to unsubscribe, clear timers, abort requests, and remove listeners.

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(`/api/users/${userId}`, { signal: controller.signal })
    .then(response => {
      if (!response.ok) throw new Error("Request failed");
      return response.json();
    })
    .then(setUser)
    .catch(error => {
      if (error.name !== "AbortError") setError(error.message);
    });

  return () => controller.abort();
}, [userId]);
```

### 5.4 Infinite effect loops

An effect can loop when it updates state that changes one of its dependencies on every run. Another common cause is creating a new object or function on every render and using it as a dependency. Prefer primitive dependencies, move non-reactive calculations outside the effect, or stabilize values only when that is genuinely necessary.

### 5.5 Strict Mode and development behavior

Strict Mode performs additional development-only checks, including an extra setup/cleanup cycle for effects in supported React versions. This exposes effects that are not correctly cleaned up. Do not “fix” it by disabling Strict Mode or adding a ref guard without understanding the underlying synchronization problem.

---

## 6. API fetching, loading, errors, and race conditions

### 6.1 Model the request as states

A robust data view accounts for:
- Initial state.
- Loading state.
- Success with data, including an empty result.
- Failure with a useful message.
- Retry or refresh behavior when appropriate.

```jsx
const [status, setStatus] = useState("idle");
const [users, setUsers] = useState([]);
const [error, setError] = useState("");

async function loadUsers() {
  setStatus("loading");
  setError("");

  try {
    const response = await axiosInstance.get("/users");
    setUsers(response.data);
    setStatus("success");
  } catch (error) {
    setError(error.response?.data?.message ?? "Unable to load users.");
    setStatus("error");
  }
}
```

Keep API logic in a service module when it is shared or substantial. Components should coordinate UI state and call the service rather than duplicating endpoint details.

### 6.2 Check HTTP status and response shape

With `fetch`, a 404 or 500 does not automatically reject the promise; check `response.ok`. Axios rejects non-2xx responses by default. In either case, inspect the actual response structure before reading fields such as `data.users`.

### 6.3 Race conditions

Suppose a search request for “re” starts first, then a request for “react” starts second. The first request may finish last and overwrite the newer results. Cancel obsolete requests with `AbortController`, or use a request identity so only the latest response updates the UI.

### 6.4 Avoid setting state after obsolete work

A request may finish after a component unmounts or after its inputs change. Abort it during cleanup where supported. Cancellation is not the same as displaying an error; expected aborts should generally not show a failure message.

### 6.5 Avoid duplicate requests caused by effect design

Check dependency arrays, Strict Mode development checks, parent remounts, and whether fetching is triggered both in an effect and an event handler. Do not assume every repeated request is a React bug.

---

## 7. Forms and validation scenarios

### 7.1 Form data flow

A typical form flow is:

`Input → state/FormData → client validation → submit handler → API → success/error UI`

Use a single source of truth for each field. For a generic change handler, ensure each input has a unique `name` attribute.

```jsx
const [form, setForm] = useState({
  name: "",
  email: "",
  password: ""
});

function handleChange(event) {
  const { name, value } = event.target;
  setForm(previous => ({ ...previous, [name]: value }));
}
```

### 7.2 Validation layers

Client-side validation improves usability but is not a security boundary. The server must validate all untrusted input. Validate required fields, format, length, confirmation fields, and domain-specific constraints. Show field-level errors close to the relevant input and preserve entered values when safe.

### 7.3 Prevent accidental page reload

A submit handler should normally call `event.preventDefault()` when handling submission in React. Use the form's `onSubmit`, not only a button's click handler, so Enter-key submission works.

### 7.4 Prevent duplicate submissions

Disable the submit action while a request is pending, but also make the server operation safe against duplicates where needed. UI disabling alone cannot guarantee exactly-once processing.

---

## 8. Lists, keys, filtering, sorting, and pagination

### 8.1 Why keys matter

Keys help React match list items between renders. Use stable identifiers from the data.

```jsx
{users.map(user => (
  <UserRow key={user.id} user={user} />
))}
```

An array index is unsafe when items can be inserted, removed, reordered, or filtered, because component state may become associated with the wrong record. Do not generate random keys during rendering.

### 8.2 Filtering and sorting

Treat the original data as immutable. Use `filter` to select records and copy before sorting because `sort()` mutates the array.

```jsx
const visible = users
  .filter(user => user.name.toLowerCase().includes(query.toLowerCase()))
  .slice()
  .sort((a, b) => a.name.localeCompare(b.name));
```

### 8.3 Pagination

For zero-based page index `page` and page size `pageSize`:

`start = page * pageSize`

`pageItems = items.slice(start, start + pageSize)`

Reset or clamp the page when filters change; otherwise, a user may remain on a page number that no longer exists.

### 8.4 Empty and loading states

An empty array after a successful request is different from a request that has not completed. Render separate loading, error, empty, and populated states. This prevents confusing blank screens.

---

## 9. Conditional rendering and component composition

### 9.1 Conditional UI

Use conditions that reflect the UI state clearly:

```jsx
if (status === "loading") return <Spinner />;
if (status === "error") return <ErrorMessage message={error} />;
if (items.length === 0) return <EmptyState />;
return <ItemList items={items} />;
```

Be careful with `condition && element` when the condition may be `0`, because React can render the number zero. Use a boolean comparison such as `items.length > 0 && ...`.

### 9.2 Component boundaries

Extract a component when it has a clear responsibility, meaningful reuse, or enough complexity to make the parent difficult to understand. Do not split every few lines into a component. Keep state close to where it is used, and lift it only when multiple parts need coordinated access.

### 9.3 Composition over excessive configuration

Prefer component composition when a component has many boolean props that create complex combinations. `children` and named slots can make relationships clearer.

---

## 10. Routing and protected-page scenarios

### 10.1 Route matching and navigation

A route maps a URL to a UI. Use declarative route configuration and navigation APIs instead of manually changing `window.location` for ordinary client-side navigation.

### 10.2 Protected routes

A protected route checks authentication state before rendering its child route. It should distinguish:
- Still checking the session.
- Authenticated.
- Unauthenticated.
- Authenticated but not authorized for the requested role.

Do not briefly render protected content while the authentication state is still being restored. Show a loading state until the check is complete.

### 10.3 Authentication is not authorization

Authentication establishes who the user is. Authorization determines what that user may do. Hiding an admin button in the frontend is not sufficient security; the backend must enforce permissions on every protected operation.

### 10.4 Preserve intended destination

When redirecting an unauthenticated user to login, retain the intended destination in route state or a safe return URL. After successful login, validate that the destination is an allowed internal route before navigating.

---

## 11. Context and shared-state scenarios

### 11.1 Context propagation

A context consumer re-renders when the provider supplies a different value according to `Object.is`. Creating a new object literal on every provider render can cause consumers to update even if the meaningful data did not change.

```jsx
const value = useMemo(
  () => ({ user, login, logout }),
  [user, login, logout]
);

return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
```

Memoization can reduce unnecessary updates, but it does not replace good state placement or profiling.

### 11.2 Split contexts by responsibility

A frequently changing value can cause many consumers to update. Separate unrelated contexts, such as authentication identity and theme, when they have different update patterns. Avoid turning Context into a global dumping ground.

### 11.3 Choose the right state location

- Local component state: UI details used in one component or small subtree.
- Lifted state: coordinated sibling state.
- Context: values needed by many descendants.
- External state library: complex shared state, subscriptions, or workflows where its capabilities justify the dependency.
- Server-state/cache library: remote data lifecycle, caching, invalidation, retries, and synchronization.

---

## 12. Modal, focus, keyboard, and accessibility scenarios

A modal is not only a box positioned above the page. A usable modal needs:
- An accessible name, usually through `aria-labelledby`.
- Dialog semantics such as `role="dialog"` and `aria-modal="true"`.
- Focus moved into the dialog when it opens.
- Keyboard focus kept inside while modal.
- Escape handling when dismissal is allowed.
- Focus restored to the element that opened it.
- Background interaction prevented while open.
- Correct cleanup of event listeners and scroll locking.

Use a portal when the dialog should render outside an ancestor's stacking or overflow context. A portal changes DOM placement, not React ownership or context. Ensure event handling and focus behavior remain correct.

---

## 13. Performance and rendering diagnosis

### 13.1 Measure before optimizing

Use React DevTools Profiler and browser performance tools to identify slow commits, expensive calculations, and unnecessary work. A component rendering often is not necessarily a performance problem.

### 13.2 Memoization tools

- `React.memo` can skip rendering a component when its props compare equal.
- `useMemo` caches a calculated value between renders.
- `useCallback` caches a function identity between renders.

These tools are performance optimizations, not correctness mechanisms. They are most useful when a measured cost exists or stable identity enables another optimization.

### 13.3 Large lists

Rendering thousands of rows can create expensive DOM work. Virtualization renders only the visible region plus a buffer. Also consider server pagination, filtering on the server, and avoiding unnecessary columns or nested components.

### 13.4 Code splitting

`React.lazy` and `Suspense` can defer loading a component until it is needed. Route-level splitting is a common strategy. A fallback should communicate loading and avoid large layout shifts.

---

## 14. Error handling and resilient UI

### 14.1 Error boundaries

Error boundaries catch errors thrown while rendering descendants and in certain lifecycle paths. They do not automatically catch errors in event handlers, ordinary asynchronous callbacks, or the boundary itself. Handle those with `try/catch`, promise rejection handling, or explicit error state.

### 14.2 Error states should be actionable

A useful error UI says what failed and what the user can do next. Avoid exposing stack traces, tokens, internal URLs, or sensitive server details to end users. Log diagnostic details through an approved monitoring path.

### 14.3 Retry safely

Retry transient reads when appropriate, but do not blindly retry non-idempotent operations such as payments or order creation. Use server-supported idempotency mechanisms for operations that must not be duplicated.

---

## 15. JavaScript output-tracing foundations used in React

React code is JavaScript. Output-based reasoning requires understanding:
- `let`, `const`, and block scope.
- `var` hoisting and function scope.
- Temporal Dead Zone for `let` and `const`.
- Primitive values versus object references.
- `===` versus `Object.is`.
- Truthy and falsy values.
- Short-circuit evaluation.
- Array methods such as `map`, `filter`, `reduce`, `find`, and `some`.
- Destructuring, spread, and rest.
- Closures and lexical scope.
- Synchronous execution, microtasks, and tasks.
- Promise chaining and `async`/`await`.

### 15.1 Reference identity

Two separately created objects are not equal by reference:

```js
({ id: 1 }) === ({ id: 1 }) // false
```

This matters for React dependencies and memoized props. A new object with the same fields is still a new reference.

### 15.2 Truthiness in JSX

```jsx
{count && <p>There are items</p>}
```

If `count` is `0`, the expression evaluates to `0`, which React can render. Prefer:

```jsx
{count > 0 && <p>There are items</p>}
```

### 15.3 Event loop and promises

Synchronous code runs first. Promise callbacks are queued as microtasks; timer callbacks are tasks. React scheduling and rendering add another layer, so do not infer exact DOM timing from JavaScript logs alone.

---

## 16. Testing scenario-driven behavior

### 16.1 Test behavior, not implementation details

A useful test describes what a user can observe:
- The user types a valid email.
- The form submits.
- A loading indicator appears.
- A success message appears after a successful response.
- An error message appears after a failed response.

### 16.2 Unit, integration, and end-to-end tests

- Unit tests focus on small functions or isolated component behavior.
- Integration tests verify that components, context, routing, and API boundaries work together.
- End-to-end tests exercise realistic user journeys in a browser.

### 16.3 Test edge cases

For every scenario, consider empty data, slow network, rejected request, malformed response, repeated click, unmount during request, invalid input, keyboard navigation, and permission denial.

---

## 17. Security and data-handling scenarios

- Never trust client-side role checks as the only authorization layer.
- Do not place secrets in frontend environment variables; bundled client variables are visible to users.
- Avoid logging passwords, access tokens, or personal health/financial data.
- Validate and encode untrusted content appropriately.
- Treat API responses as untrusted data.
- Use secure session/token handling according to the backend's threat model.
- Handle token expiry and unauthorized responses consistently.
- Do not assume that hiding a route or button protects an API endpoint.

---

## 18. A scenario-solving checklist

Before changing code:
1. Reproduce the exact behavior.
2. Identify the component and route.
3. Trace props, state, context, and API data.
4. Inspect the console and Network panel.
5. Check event handler invocation and payload.
6. Check effect dependencies and cleanup.
7. Check keys and state identity.
8. Check loading, error, and empty states.
9. Make the smallest correct change.
10. Retest the original case and nearby edge cases.

## 19. Suggested hands-on exercises

1. Fix a controlled form whose fields do not update because `name` attributes are missing.
2. Fix a counter interval that becomes stuck at one.
3. Prevent an older search response from replacing newer results.
4. Correct a list that uses array indexes as keys and shows the wrong row state after deletion.
5. Add loading, empty, error, and retry states to a course list.
6. Protect an admin route while authentication is being restored.
7. Build a modal with focus management and Escape support.
8. Diagnose a Context provider that causes unrelated pages to re-render.
9. Fix a pagination view that remains on an invalid page after filtering.
10. Add tests for successful and failed form submission.

---

## 20. Final principles

- Understand the data flow before editing code.
- Treat state as immutable snapshots.
- Use functional updates when the next value depends on the previous value.
- Keep one source of truth for each piece of data.
- Use effects for external synchronization, not for ordinary derived calculations.
- Include correct dependencies and clean up external work.
- Model loading, success, empty, and error states explicitly.
- Use stable keys for dynamic lists.
- Separate authentication from authorization.
- Measure performance before optimizing.
- Make accessibility and security part of the solution, not afterthoughts.
- Verify fixes with realistic tests.
