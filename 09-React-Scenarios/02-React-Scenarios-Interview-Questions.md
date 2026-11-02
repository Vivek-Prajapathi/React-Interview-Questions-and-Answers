# Chapter 9 – React Scenarios: Interview Questions and Answers

> Answers are written in a natural, confident interview-speaking style. Each answer includes reasoning and examples where useful.

## Table of Contents

1. [How do you approach debugging a React application?](#q1-how-do-you-approach-debugging-a-react-application)
2. [Why does a component re-render?](#q2-why-does-a-component-re-render)
3. [Does a re-render always update the DOM?](#q3-does-a-re-render-always-update-the-dom)
4. [Why does setState not update the variable immediately?](#q4-why-does-setstate-not-update-the-variable-immediately)
5. [Why can three setCount(count + 1) calls increment only once?](#q5-why-can-three-setcountcount--1-calls-increment-only-once)
6. [When should we use a functional state update?](#q6-when-should-we-use-a-functional-state-update)
7. [Why should React state not be mutated directly?](#q7-why-should-react-state-not-be-mutated-directly)
8. [What is the difference between props and state?](#q8-what-is-the-difference-between-props-and-state)
9. [Why is copying props into state often a mistake?](#q9-why-is-copying-props-into-state-often-a-mistake)
10. [What is derived state?](#q10-what-is-derived-state)
11. [What is lifting state up?](#q11-what-is-lifting-state-up)
12. [What is the difference between controlled and uncontrolled inputs?](#q12-what-is-the-difference-between-controlled-and-uncontrolled-inputs)
13. [Why might a controlled input not accept typing?](#q13-why-might-a-controlled-input-not-accept-typing)
14. [What is the difference between onClick={handleClick} and onClick={handleClick()}?](#q14-what-is-the-difference-between-onclickhandleclick-and-onclickhandleclick)
15. [What is a stale closure in React?](#q15-what-is-a-stale-closure-in-react)
16. [How do you fix a counter interval that always uses the initial state?](#q16-how-do-you-fix-a-counter-interval-that-always-uses-the-initial-state)
17. [When should useEffect be used?](#q17-when-should-useeffect-be-used)
18. [What does the useEffect dependency array mean?](#q18-what-does-the-useeffect-dependency-array-mean)
19. [Why is cleanup important in useEffect?](#q19-why-is-cleanup-important-in-useeffect)
20. [What causes an infinite useEffect loop?](#q20-what-causes-an-infinite-useeffect-loop)
21. [Why can an effect run twice in development?](#q21-why-can-an-effect-run-twice-in-development)
22. [How do you cancel an API request in useEffect?](#q22-how-do-you-cancel-an-api-request-in-useeffect)
23. [What is an API race condition?](#q23-what-is-an-api-race-condition)
24. [How do you prevent an older search result from replacing a newer one?](#q24-how-do-you-prevent-an-older-search-result-from-replacing-a-newer-one)
25. [How should an API-driven component represent its states?](#q25-how-should-an-api-driven-component-represent-its-states)
26. [What is the difference between fetch and Axios error handling?](#q26-what-is-the-difference-between-fetch-and-axios-error-handling)
27. [Why should form submission use onSubmit?](#q27-why-should-form-submission-use-onsubmit)
28. [Why are name attributes important in forms?](#q28-why-are-name-attributes-important-in-forms)
29. [How do you prevent duplicate form submissions?](#q29-how-do-you-prevent-duplicate-form-submissions)
30. [Why are stable keys important in lists?](#q30-why-are-stable-keys-important-in-lists)
31. [Why can array index keys cause bugs?](#q31-why-can-array-index-keys-cause-bugs)
32. [How do you sort an array without mutating state?](#q32-how-do-you-sort-an-array-without-mutating-state)
33. [How do you implement pagination?](#q33-how-do-you-implement-pagination)
34. [How do you distinguish empty, loading, and error states?](#q34-how-do-you-distinguish-empty-loading-and-error-states)
35. [What is a common conditional-rendering mistake with zero?](#q35-what-is-a-common-conditional-rendering-mistake-with-zero)
36. [How do you decide when to create a separate component?](#q36-how-do-you-decide-when-to-create-a-separate-component)
37. [How do protected routes work?](#q37-how-do-protected-routes-work)
38. [What is the difference between authentication and authorization?](#q38-what-is-the-difference-between-authentication-and-authorization)
39. [How do you avoid flashing protected content during session restoration?](#q39-how-do-you-avoid-flashing-protected-content-during-session-restoration)
40. [How can Context cause unnecessary re-renders?](#q40-how-can-context-cause-unnecessary-re-renders)
41. [How can you optimize a Context provider?](#q41-how-can-you-optimize-a-context-provider)
42. [When would you choose Context versus a state-management library?](#q42-when-would-you-choose-context-versus-a-state-management-library)
43. [What makes a modal accessible?](#q43-what-makes-a-modal-accessible)
44. [Why might a portal be useful for a modal?](#q44-why-might-a-portal-be-useful-for-a-modal)
45. [What does an error boundary catch?](#q45-what-does-an-error-boundary-catch)
46. [Can an error boundary catch errors in event handlers?](#q46-can-an-error-boundary-catch-errors-in-event-handlers)
47. [How do you investigate unnecessary renders?](#q47-how-do-you-investigate-unnecessary-renders)
48. [When should React.memo, useMemo, and useCallback be used?](#q48-when-should-reactmemo-usememo-and-usecallback-be-used)
49. [How do you handle a very large list?](#q49-how-do-you-handle-a-very-large-list)
50. [What is code splitting and how does React.lazy help?](#q50-what-is-code-splitting-and-how-does-reactlazy-help)
51. [Why can a component display zero instead of nothing?](#q51-why-can-a-component-display-zero-instead-of-nothing)
52. [What is the difference between == and ===?](#q52-what-is-the-difference-between--and-)
53. [What is the difference between Object.is and ===?](#q53-what-is-the-difference-between-objectis-and-)
54. [Why are two objects with the same properties not equal?](#q54-why-are-two-objects-with-the-same-properties-not-equal)
55. [What is the difference between map and forEach?](#q55-what-is-the-difference-between-map-and-foreach)
56. [What is the difference between filter and find?](#q56-what-is-the-difference-between-filter-and-find)
57. [What is the difference between slice and splice?](#q57-what-is-the-difference-between-slice-and-splice)
58. [What is the event loop?](#q58-what-is-the-event-loop)
59. [What is the difference between a microtask and a task?](#q59-what-is-the-difference-between-a-microtask-and-a-task)
60. [How do you test a React scenario?](#q60-how-do-you-test-a-react-scenario)
61. [What is the difference between unit, integration, and end-to-end tests?](#q61-what-is-the-difference-between-unit-integration-and-end-to-end-tests)
62. [Why is client-side validation not enough for security?](#q62-why-is-client-side-validation-not-enough-for-security)
63. [Why is hiding an admin button not authorization?](#q63-why-is-hiding-an-admin-button-not-authorization)
64. [How do you handle token expiry?](#q64-how-do-you-handle-token-expiry)
65. [How would you debug a page that works after navigation but fails on refresh?](#q65-how-would-you-debug-a-page-that-works-after-navigation-but-fails-on-refresh)
66. [How would you debug a delete action that removes the wrong row?](#q66-how-would-you-debug-a-delete-action-that-removes-the-wrong-row)
67. [How would you debug a form that shows success but saves nothing?](#q67-how-would-you-debug-a-form-that-shows-success-but-saves-nothing)
68. [How would you debug a component that keeps fetching repeatedly?](#q68-how-would-you-debug-a-component-that-keeps-fetching-repeatedly)
69. [How would you debug a blank screen after an API call?](#q69-how-would-you-debug-a-blank-screen-after-an-api-call)
70. [How would you debug a modal that closes when clicking inside it?](#q70-how-would-you-debug-a-modal-that-closes-when-clicking-inside-it)
71. [How would you debug a route that is accessible to the wrong role?](#q71-how-would-you-debug-a-route-that-is-accessible-to-the-wrong-role)
72. [How would you debug a child component that does not update?](#q72-how-would-you-debug-a-child-component-that-does-not-update)
73. [How would you debug a list that displays stale data after deletion?](#q73-how-would-you-debug-a-list-that-displays-stale-data-after-deletion)
74. [How would you debug a search box that shows results for an old query?](#q74-how-would-you-debug-a-search-box-that-shows-results-for-an-old-query)
75. [How would you debug a page that becomes slow after adding many records?](#q75-how-would-you-debug-a-page-that-becomes-slow-after-adding-many-records)
76. [What is the output of multiple state updates using the same snapshot?](#q76-what-is-the-output-of-multiple-state-updates-using-the-same-snapshot)
77. [What is the output of functional state updates?](#q77-what-is-the-output-of-functional-state-updates)
78. [What is the output of a closure capturing a variable?](#q78-what-is-the-output-of-a-closure-capturing-a-variable)
79. [What is the output order of synchronous code and a Promise?](#q79-what-is-the-output-order-of-synchronous-code-and-a-promise)
80. [What is the output of a map callback without a return?](#q80-what-is-the-output-of-a-map-callback-without-a-return)
81. [What is the output of filter with Boolean?](#q81-what-is-the-output-of-filter-with-boolean)
82. [What is the output of short-circuit rendering when a value is zero?](#q82-what-is-the-output-of-short-circuit-rendering-when-a-value-is-zero)
83. [What happens when a list uses index keys and its first item is removed?](#q83-what-happens-when-a-list-uses-index-keys-and-its-first-item-is-removed)
84. [What happens when an effect depends on a new object each render?](#q84-what-happens-when-an-effect-depends-on-a-new-object-each-render)
85. [What happens when a controlled input has value but no onChange?](#q85-what-happens-when-a-controlled-input-has-value-but-no-onchange)
86. [What is the output of comparing two object literals?](#q86-what-is-the-output-of-comparing-two-object-literals)
87. [What is the output of Promise.resolve followed by setTimeout?](#q87-what-is-the-output-of-promiseresolve-followed-by-settimeout)
88. [How do you explain a complete scenario solution in an interview?](#q88-how-do-you-explain-a-complete-scenario-solution-in-an-interview)

---

## Q1. How do you approach debugging a React application?

**Interview answer:**

“I start by reproducing the issue and identifying the expected and actual behavior. Then I trace the data flow from the user action through the event handler, state update or API request, and finally to the component that renders the result. I inspect the browser console, Network tab, and React DevTools instead of immediately changing code. Once I identify the root cause, I make the smallest fix and test the original case plus related edge cases.”

**Example:** If a profile form shows a success message but the old value returns after refresh, I inspect the request payload, response, and whether the backend actually persisted the update.

## Q2. Why does a component re-render?

**Interview answer:**

“A component commonly re-renders when its own state changes, its parent re-renders, a context value it consumes changes, or an external store subscription notifies it. A re-render means React calls the component again to calculate the next UI. It does not necessarily mean the browser DOM is fully recreated.”

**Example:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

Updating `count` schedules a new render.

## Q3. Does a re-render always update the DOM?

**Interview answer:**

“No. Rendering calculates the next React element tree. React compares it with the previous result and commits only the necessary DOM changes. A component can render again even when its resulting DOM is unchanged.”

**Example:** A parent can re-render while a child receives the same primitive text value. React may determine that no DOM mutation is required.

## Q4. Why does setState not update the variable immediately?

**Interview answer:**

“React state is a snapshot for a particular render. Calling a setter schedules an update; it does not change the variable inside the currently executing handler. The next render receives the new state.”

```jsx
function handleClick() {
  setCount(count + 1);
  console.log(count); // current render's value
}
```

The log prints the old snapshot.

## Q5. Why can three setCount(count + 1) calls increment only once?

**Interview answer:**

“All three calls read the same `count` value from the current render. They therefore request the same next value. If I want to apply multiple updates based on the previous result, I use the functional updater form.”

```jsx
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
```

If `count` is zero, these calls request one. They do not mean ‘increment the latest value three times’.

## Q6. When should we use a functional state update?

**Interview answer:**

“I use a functional update whenever the next state depends on the previous state, especially when updates can be queued or when a callback may hold an older render snapshot.”

```jsx
setCount(previous => previous + 1);
```

This lets React calculate the next value from the latest queued state.

## Q7. Why should React state not be mutated directly?

**Interview answer:**

“React state should be treated as immutable. Direct mutation changes an existing object or array instead of creating a new state value. That can make updates difficult to detect, cause previous snapshots to be modified, and introduce unpredictable bugs. I create a new object or array when updating state.”

```jsx
setUser(previous => ({ ...previous, city: "Bengaluru" }));
```

## Q8. What is the difference between props and state?

**Interview answer:**

“Props are inputs passed from a parent component, while state is data managed by a component that can change over time. A child should treat props as read-only. State is updated through its setter or the relevant state-management mechanism.”

## Q9. Why is copying props into state often a mistake?

**Interview answer:**

“Copying props into state creates two sources of truth. If the parent later changes the prop, the copied state does not automatically update. I use the prop directly when it should always reflect the parent. I copy it into local state only when I intentionally need an independent draft, such as an editable form, and I define when that draft resets.”

## Q10. What is derived state?

**Interview answer:**

“Derived state is a value that can be calculated from existing props or state. I generally calculate it during rendering instead of storing a duplicate value, because duplicated state can become inconsistent.”

```jsx
const filtered = products.filter(product =>
  product.title.toLowerCase().includes(search.toLowerCase())
);
```

## Q11. What is lifting state up?

**Interview answer:**

“Lifting state up means moving shared state to the closest common parent of the components that need it. The parent owns the value and passes it down through props along with callbacks. This keeps one source of truth for related components.”

## Q12. What is the difference between controlled and uncontrolled inputs?

**Interview answer:**

“A controlled input gets its value from React state and updates through an `onChange` handler. An uncontrolled input keeps its current value in the DOM and is usually accessed through a ref or `FormData`. Controlled inputs are useful when the UI needs to respond to every change, such as validation or conditional fields.”

## Q13. Why might a controlled input not accept typing?

**Interview answer:**

“I first check whether the input has a valid `onChange` handler that updates the state used by its `value` prop. I also check whether the handler reads the correct field name and whether the state update uses the right key. If a generic handler uses `event.target.name`, every input must have a matching `name` attribute.”

```jsx
<input
  name="email"
  value={form.email}
  onChange={handleChange}
/>
```

Without `name="email"`, a handler using `name` may update an `undefined` property instead of `email`.

## Q14. What is the difference between onClick={handleClick} and onClick={handleClick()}?

**Interview answer:**

“`onClick={handleClick}` passes the function so React can call it when the user clicks. `onClick={handleClick()}` calls the function immediately while rendering and passes its return value as the handler. If I need to pass an argument, I use an arrow function.”

```jsx
<button onClick={() => handleDelete(id)}>Delete</button>
```

## Q15. What is a stale closure in React?

**Interview answer:**

“A stale closure occurs when a callback uses values captured from an older render. This commonly happens with timers, subscriptions, or asynchronous callbacks. I fix it by using a functional state update, correctly managing effect dependencies, or restructuring the callback so it reads the current value.”

## Q16. How do you fix a counter interval that always uses the initial state?

**Interview answer:**

“The interval callback may capture the initial state because the effect runs only once. I use a functional updater so the callback receives the latest state.”

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    setCount(current => current + 1);
  }, 1000);

  return () => clearInterval(timer);
}, []);
```

## Q17. When should useEffect be used?

**Interview answer:**

“I use `useEffect` to synchronize a React component with an external system, such as a network connection, timer, browser event, or third-party widget. I do not use it for values that can be calculated during rendering or for logic that belongs directly in an event handler.”

## Q18. What does the useEffect dependency array mean?

**Interview answer:**

“The dependency array tells React which reactive values used by the effect should cause it to run again. With no dependency array, the effect runs after every committed render. With an empty array, it runs after the initial mount, with cleanup on unmount. With dependencies, it runs after the initial mount and again when one of those dependencies changes.”

```jsx
useEffect(() => {
  fetchUser(userId);
}, [userId]);
```

## Q19. Why is cleanup important in useEffect?

**Interview answer:**

“Cleanup releases resources created by an effect. It can clear timers, remove event listeners, disconnect subscriptions, or abort requests. React runs cleanup before re-running an effect with changed dependencies and when the component unmounts.”

## Q20. What causes an infinite useEffect loop?

**Interview answer:**

“An infinite effect loop can happen when an effect updates state and that update changes one of the effect's dependencies, causing the effect to run again. It can also happen when a new object or function is created on every render and used as a dependency. I inspect the dependency list and reconsider whether the effect is necessary.”

## Q21. Why can an effect run twice in development?

**Interview answer:**

“In development, React Strict Mode performs an extra setup and cleanup cycle for effects to help identify missing cleanup and unsafe side effects. I verify that the effect is correctly reversible instead of trying to hide the behavior with a ref flag. This extra check is not the same as saying production will always run the effect twice.”

## Q22. How do you cancel an API request in useEffect?

**Interview answer:**

“I create an `AbortController`, pass its signal to the request, and call `abort()` in the effect cleanup. I also avoid showing an error for an expected abort.”

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

## Q23. What is an API race condition?

**Interview answer:**

“A race condition occurs when multiple asynchronous requests are in progress and their completion order differs from their start order. An older response can arrive later and overwrite the result of a newer request. Search interfaces are a common example.”

## Q24. How do you prevent an older search result from replacing a newer one?

**Interview answer:**

“I can cancel the previous request with an `AbortController`, or assign each request an identifier and update the UI only if the response belongs to the latest request. I also debounce input when appropriate to reduce unnecessary requests.”

## Q25. How should an API-driven component represent its states?

**Interview answer:**

“I explicitly represent loading, success, empty, and error states. An empty list after a successful request is different from a request that has not completed. This makes the UI predictable and gives the user useful feedback.”

## Q26. What is the difference between fetch and Axios error handling?

**Interview answer:**

“With `fetch`, HTTP error responses such as 404 or 500 still fulfill the promise, so I check `response.ok`. Axios rejects non-2xx responses by default. In both cases, I handle network errors and validate the response shape before using it.”

## Q27. Why should form submission use onSubmit?

**Interview answer:**

“I prefer the form's `onSubmit` handler because it supports both clicking the submit button and pressing Enter in a field. I call `event.preventDefault()` when I want React to handle the submission without a browser page reload.”

## Q28. Why are name attributes important in forms?

**Interview answer:**

“The `name` attribute identifies a field in generic change handlers and in native form submission through `FormData`. If my handler reads `event.target.name`, a missing name means it cannot identify which state property to update.”

## Q29. How do you prevent duplicate form submissions?

**Interview answer:**

“I track a pending state, disable the submit button while the request is in progress, and prevent another submission from starting. For important operations such as payments or order creation, I also rely on backend idempotency because frontend disabling alone cannot guarantee that a request is processed only once.”

## Q30. Why are stable keys important in lists?

**Interview answer:**

“Keys help React identify which list item corresponds to which component between renders. Stable keys allow React to preserve the correct component identity when items are inserted, removed, or reordered. I prefer unique IDs from the data.”

```jsx
users.map(user => <UserRow key={user.id} user={user} />);
```

## Q31. Why can array index keys cause bugs?

**Interview answer:**

“If items are removed or reordered, an index can refer to a different record in the next render. React may then reuse component state for the wrong item. I use indexes only for truly static lists that never change order or membership.”

## Q32. How do you sort an array without mutating state?

**Interview answer:**

“JavaScript's `sort()` mutates the original array. I make a copy before sorting so the state array remains unchanged.”

```jsx
const sortedUsers = [...users].sort((a, b) =>
  a.name.localeCompare(b.name)
);
```

## Q33. How do you implement pagination?

**Interview answer:**

“I calculate the starting index using the current page and page size, then use `slice()` to get the visible records. I also reset or clamp the current page when filtering reduces the number of available pages.”

```js
const start = page * pageSize;
const pageItems = items.slice(start, start + pageSize);
```

## Q34. How do you distinguish empty, loading, and error states?

**Interview answer:**

“I use an explicit request status. While loading, I show a loader. If the request fails, I show an error and possibly a retry action. If it succeeds with no records, I show an empty state. Otherwise, I render the data.”

## Q35. What is a common conditional-rendering mistake with zero?

**Interview answer:**

“When I use `count && <Component />`, a count of zero makes the expression evaluate to zero. React can render that zero in the UI. I use an explicit boolean condition such as `count > 0 && ...`.”

## Q36. How do you decide when to create a separate component?

**Interview answer:**

“I extract a component when it has a clear responsibility, is reused, or makes a complex parent easier to understand. I avoid creating components for every small group of lines because excessive fragmentation can make data flow harder to follow.”

## Q37. How do protected routes work?

**Interview answer:**

“A protected route checks the authentication state before rendering a page. If the user is not authenticated, it redirects to login. If the user is authenticated but lacks the required role, it shows an unauthorized page or redirects to an allowed destination. It should also handle the session-loading state.”

## Q38. What is the difference between authentication and authorization?

**Interview answer:**

“Authentication verifies who the user is. Authorization determines what that authenticated user is allowed to access or perform. For example, login authenticates a user, while checking whether that user can open the admin dashboard is authorization. The backend must enforce authorization as well.”

## Q39. How do you avoid flashing protected content during session restoration?

**Interview answer:**

“I model authentication as more than a simple true or false value. I include a loading or initializing state while restoring the session. During that state, I render a loader rather than protected content. Once the check finishes, I render the route or redirect.”

## Q40. How can Context cause unnecessary re-renders?

**Interview answer:**

“When a provider passes a newly created object as its value, its identity changes on every render. Consumers can re-render even if the meaningful fields have not changed. A context value that updates frequently can also affect many consumers.”

## Q41. How can you optimize a Context provider?

**Interview answer:**

“I first measure the issue. I can memoize the provider value and stable callbacks, split unrelated contexts, and keep frequently changing state closer to the components that use it. Memoization should support a good state design rather than replace it.”

```jsx
const value = useMemo(
  () => ({ user, login, logout }),
  [user, login, logout]
);
```

## Q42. When would you choose Context versus a state-management library?

**Interview answer:**

“I use Context for values that need to be available across a subtree, such as authentication or theme. If the application has complex shared updates, selective subscriptions, middleware, or large workflows, a dedicated state-management library may be more appropriate. For remote server data, I also consider a server-state library because caching and invalidation are different concerns from local UI state.”

## Q43. What makes a modal accessible?

**Interview answer:**

“A modal needs dialog semantics, an accessible name, and correct keyboard focus behavior. Focus should move inside when it opens, remain within the modal while it is active, and return to the trigger when it closes. Escape behavior, background interaction, and screen-reader announcements should also be considered.”

## Q44. Why might a portal be useful for a modal?

**Interview answer:**

“A portal lets a component render into a DOM node outside its normal parent hierarchy. It is useful when an ancestor's overflow or stacking context interferes with the modal. The component still remains part of the same React tree, so context remains available.”

## Q45. What does an error boundary catch?

**Interview answer:**

“An error boundary catches errors thrown while rendering its descendant tree and in certain lifecycle paths. It can show a fallback UI instead of allowing the affected part of the interface to crash. It does not catch every kind of JavaScript error.”

## Q46. Can an error boundary catch errors in event handlers?

**Interview answer:**

“No. Errors in event handlers and ordinary asynchronous callbacks are not automatically caught by error boundaries. I handle those with `try/catch`, promise rejection handling, or component error state.”

## Q47. How do you investigate unnecessary renders?

**Interview answer:**

“I use React DevTools Profiler to identify which components render and how much time their commits take. Then I inspect state ownership, parent renders, context values, and prop identity. I optimize only when the render is measurably expensive or causes a real user-facing issue.”

## Q48. When should React.memo, useMemo, and useCallback be used?

**Interview answer:**

“`React.memo` can skip rendering a component when its props compare equal. `useMemo` caches a calculated value, and `useCallback` caches a function identity. I use them when profiling or component relationships show a benefit. They are performance optimizations, not tools required for correctness.”

## Q49. How do you handle a very large list?

**Interview answer:**

“I first consider server-side pagination or filtering so the browser does not need to receive every record. If many rows still need to be navigated, I use list virtualization to render only the visible items and a small buffer. I also profile row components and avoid unnecessary work.”

## Q50. What is code splitting and how does React.lazy help?

**Interview answer:**

“Code splitting divides an application bundle into smaller chunks that can be loaded when needed. `React.lazy` allows a component to be loaded dynamically, and `Suspense` displays a fallback while it loads. It is commonly used for route-level splitting.”

```jsx
const AdminPage = React.lazy(() => import("./AdminPage"));

<Suspense fallback={<p>Loading page...</p>}>
  <AdminPage />
</Suspense>
```

## Q51. Why can a component display zero instead of nothing?

**Interview answer:**

“Because JavaScript's `&&` operator returns an operand, not necessarily a boolean. If the left operand is zero, the expression returns zero. React renders that number. I use an explicit boolean condition.”

## Q52. What is the difference between == and ===?

**Interview answer:**

“`==` allows type coercion before comparison, while `===` compares without converting the types. I generally use strict equality because it avoids surprising conversions.”

```js
0 == "0"  // true
0 === "0" // false
```

## Q53. What is the difference between Object.is and ===?

**Interview answer:**

“They are similar for most values, but differ for `NaN` and signed zero. `Object.is(NaN, NaN)` is true, while `NaN === NaN` is false. `Object.is(+0, -0)` is false, while `+0 === -0` is true. React uses `Object.is` for certain comparisons, including state bailouts and effect dependency comparisons.”

## Q54. Why are two objects with the same properties not equal?

**Interview answer:**

“Objects are compared by reference, not by their contents, when using `===` or `Object.is`. Two object literals create two different references even if their properties match.”

```js
const a = { id: 1 };
const b = { id: 1 };
console.log(a === b); // false
```

## Q55. What is the difference between map and forEach?

**Interview answer:**

“`map()` creates and returns a new array containing the callback results. `forEach()` executes a callback for each item and returns undefined. In React rendering, I use `map()` to transform data into elements.”

## Q56. What is the difference between filter and find?

**Interview answer:**

“`filter()` returns a new array containing every item that matches the condition. `find()` returns the first matching item, or undefined if there is no match.”

## Q57. What is the difference between slice and splice?

**Interview answer:**

“`slice()` returns a shallow copy of a selected portion and does not modify the original array. `splice()` changes the original array by removing, replacing, or inserting items. I generally prefer non-mutating operations when working with React state.”

## Q58. What is the event loop?

**Interview answer:**

“The event loop coordinates synchronous JavaScript execution with queued asynchronous callbacks. The current call stack runs first. After the stack is clear, the runtime processes microtasks and then proceeds to tasks such as timer callbacks, subject to the event-loop model.”

## Q59. What is the difference between a microtask and a task?

**Interview answer:**

“Promise callbacks are scheduled as microtasks, while timer callbacks such as `setTimeout` are tasks. After the current synchronous code finishes, queued microtasks are processed before the next task is taken.”

## Q60. How do you test a React scenario?

**Interview answer:**

“I test observable behavior from the user's perspective. I cover the normal path, failure path, loading state, empty data, and important edge cases. I avoid testing internal implementation details unless they are part of a specific contract.”

## Q61. What is the difference between unit, integration, and end-to-end tests?

**Interview answer:**

“A unit test checks a small function or isolated behavior. An integration test checks how multiple parts work together, such as a form, context, and API layer. An end-to-end test exercises a realistic user journey through the application in a browser.”

## Q62. Why is client-side validation not enough for security?

**Interview answer:**

“Client-side validation can be bypassed because users control their browser and can send requests directly. It improves user experience, but the server must validate every untrusted input and enforce business rules.”

## Q63. Why is hiding an admin button not authorization?

**Interview answer:**

“Hiding a button only changes the interface. A user can still attempt the API request directly. The backend must verify the user's permissions on every protected endpoint and operation.”

## Q64. How do you handle token expiry?

**Interview answer:**

“I handle unauthorized responses consistently in the API layer, clear or refresh the session according to the authentication design, and redirect the user when re-authentication is required. I avoid exposing tokens in logs and make sure the backend validates every request.”

## Q65. How would you debug a page that works after navigation but fails on refresh?

**Interview answer:**

“I check whether the route is configured for direct loading on the server, whether the app restores authentication state after a reload, and whether required route data is only passed through navigation state. I also inspect the console and Network tab. A single-page application needs its hosting server to return the app entry point for valid client-side routes.”

## Q66. How would you debug a delete action that removes the wrong row?

**Interview answer:**

“I inspect the identifier passed by the clicked row and verify the delete endpoint receives the correct ID. Then I check the state update and list keys. I use stable record IDs rather than array indexes, and I update the local list by filtering on the deleted record's ID.”

```jsx
setUsers(previous => previous.filter(user => user.id !== deletedId));
```

## Q67. How would you debug a form that shows success but saves nothing?

**Interview answer:**

“I inspect whether the submit handler is called, whether the payload contains the correct values, and whether the request reaches the backend. I verify the response status and response body before showing success. I also check whether the UI is displaying stale local state instead of refetching or updating from the server response.”

## Q68. How would you debug a component that keeps fetching repeatedly?

**Interview answer:**

“I inspect the effect dependencies first. A newly created object or function in the dependency list can change on every render. I also check whether the effect updates state that changes its own dependencies, whether the component is remounting, and whether Strict Mode is exposing missing cleanup.”

## Q69. How would you debug a blank screen after an API call?

**Interview answer:**

“I check the browser console for rendering exceptions and inspect the Network response. I verify the response shape before accessing nested properties, and I make sure loading and error states are handled. A mismatch between the expected and actual API response is a common cause.”

## Q70. How would you debug a modal that closes when clicking inside it?

**Interview answer:**

“I check event propagation. If the backdrop has a click handler that closes the modal, clicks inside the dialog may bubble to it. I stop propagation on the dialog surface or compare the event target with the backdrop target. I also test Escape handling and focus restoration.”

## Q71. How would you debug a route that is accessible to the wrong role?

**Interview answer:**

“I inspect the role value restored from the session, the route guard condition, and the role mapping used by the application. I verify both the frontend route behavior and the backend authorization response. The server must reject unauthorized operations even if the frontend guard fails.”

## Q72. How would you debug a child component that does not update?

**Interview answer:**

“I check whether the parent is passing the updated value, whether state was mutated instead of replaced, and whether memoization is skipping the render because props appear unchanged. I also verify that the child is reading the correct prop rather than a stale copy in local state.”

## Q73. How would you debug a list that displays stale data after deletion?

**Interview answer:**

“I verify the delete request succeeded and that the local state is updated using the correct record ID. If the list is server-driven, I check whether the query is refetched or its cache is invalidated. I also check stable keys and whether a stale response overwrites the updated list.”

## Q74. How would you debug a search box that shows results for an old query?

**Interview answer:**

“I check whether multiple requests are racing. I cancel previous requests or ensure only the latest request can update the results. I also verify that the request uses the latest query value and that debouncing does not retain an outdated value.”

## Q75. How would you debug a page that becomes slow after adding many records?

**Interview answer:**

“I use the browser performance tools and React Profiler to find the expensive part. If the issue is a large DOM list, I consider pagination or virtualization. If it is repeated calculations or renders, I inspect state placement, component boundaries, and memoization opportunities.”

## Q76. What is the output of multiple state updates using the same snapshot?

**Code:**

```jsx
const [count, setCount] = useState(0);

function handleClick() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
}
```

**Answer:**

“After one click, the next count is 1, not 3. Each expression reads the same `count` snapshot from the current render and requests the same next value.”

## Q77. What is the output of functional state updates?

**Code:**

```jsx
function handleClick() {
  setCount(c => c + 1);
  setCount(c => c + 1);
  setCount(c => c + 1);
}
```

**Answer:**

“After one click, the count increases by 3. Each updater receives the result of the previous queued updater.”

## Q78. What is the output of a closure capturing a variable?

**Code:**

```js
function createCounter() {
  let count = 0;
  return function increment() {
    count += 1;
    return count;
  };
}

const counter = createCounter();
console.log(counter());
console.log(counter());
```

**Answer:**

“The output is 1 and then 2. The returned function closes over the same `count` variable created by `createCounter`, so the value is preserved between calls.”

## Q79. What is the output order of synchronous code and a Promise?

**Code:**

```js
console.log("A");

Promise.resolve().then(() => console.log("B"));

console.log("C");
```

**Answer:**

“The output is A, C, B. Synchronous statements run first. The Promise callback is a microtask and runs after the current call stack is empty.”

## Q80. What is the output of a map callback without a return?

**Code:**

```js
const result = [1, 2, 3].map(value => {
  value * 2;
});

console.log(result);
```

**Answer:**

“The result is `[undefined, undefined, undefined]` because the arrow function uses a block body and does not explicitly return the calculated value.”

**Correct version:**

```js
const result = [1, 2, 3].map(value => value * 2);
```

## Q81. What is the output of filter with Boolean?

**Code:**

```js
const values = [0, 1, "", "React", null, undefined, false];
console.log(values.filter(Boolean));
```

**Answer:**

“The result contains only truthy values: `[1, "React"]`. `Boolean` converts each item to its truthiness, and `filter` keeps the items that produce true. I use this only when removing all falsy values is actually the intended behavior.”

## Q82. What is the output of short-circuit rendering when a value is zero?

**Code:**

```jsx
function Cart({ items }) {
  return <div>{items.length && <p>Items available</p>}</div>;
}
```

**Answer:**

“If the array is empty, `items.length` is zero, so the expression evaluates to zero. React can display that zero. I would write `items.length > 0 && ...` to make the condition explicitly boolean.”

## Q83. What happens when a list uses index keys and its first item is removed?

**Answer:**

“React may reuse the component instance at index zero for what used to be the second item. If each row has local state, that state can appear against the wrong record. Stable IDs solve the identity problem.”

## Q84. What happens when an effect depends on a new object each render?

**Code:**

```jsx
function Example() {
  const options = { active: true };

  useEffect(() => {
    connect(options);
  }, [options]);
}
```

**Answer:**

“Every render creates a new object reference. React sees the dependency as changed and runs the effect again. I can move the object creation inside the effect or depend on the primitive values it needs.”

## Q85. What happens when a controlled input has value but no onChange?

**Answer:**

“The input is effectively read-only because React keeps rendering the supplied value and there is no handler to update it as the user types. I add an `onChange` handler or intentionally use `readOnly` if editing is not allowed.”

## Q86. What is the output of comparing two object literals?

**Code:**

```js
console.log({ value: 5 } === { value: 5 });
```

**Answer:**

“The output is false because each object literal creates a separate object reference. JavaScript does not compare their properties automatically.”

## Q87. What is the output of Promise.resolve followed by setTimeout?

**Code:**

```js
console.log("start");

setTimeout(() => console.log("timer"), 0);

Promise.resolve().then(() => console.log("promise"));

console.log("end");
```

**Answer:**

“The typical output is `start`, `end`, `promise`, and then `timer`. Synchronous code runs first, Promise callbacks are microtasks, and the timer callback is a task.”

## Q88. How do you explain a complete scenario solution in an interview?

**Interview answer:**

“I explain the symptom first, then the root cause, and finally the fix and verification. I avoid jumping straight to code without explaining why the bug occurs. For example, if a search page displays older results, I would explain that requests can finish out of order, show how I cancel or identify obsolete requests, and then describe how I test rapid typing and slow network conditions.”

**A useful structure:**
1. State the observed behavior.
2. Identify the relevant React or JavaScript concept.
3. Explain the root cause.
4. Show the smallest correct fix.
5. Mention trade-offs or edge cases.
6. Explain how the fix is tested.
