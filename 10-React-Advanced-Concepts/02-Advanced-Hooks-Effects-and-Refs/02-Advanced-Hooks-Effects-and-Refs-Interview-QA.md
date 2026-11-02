# Advanced Hooks, Effects, and Refs – Interview Questions and Answers

This question bank covers fundamentals, dependency arrays, cleanup, effect timing, refs, stale closures, custom Hooks, practical code, debugging, and advanced scenarios. Answers are written in a natural style that can be spoken during an interview.

## Table of contents

1. [What are React Hooks?](#q1-what-are-react-hooks)
2. [What are the Rules of Hooks?](#q2-what-are-the-rules-of-hooks)
3. [Why must Hooks be called in the same order?](#q3-why-must-hooks-be-called-in-the-same-order)
4. [Can we call a Hook inside a condition?](#q4-can-we-call-a-hook-inside-a-condition)
5. [What is useEffect?](#q5-what-is-useeffect)
6. [What is the purpose of useEffect?](#q6-what-is-the-purpose-of-useeffect)
7. [When does useEffect run?](#q7-when-does-useeffect-run)
8. [What happens if we omit the dependency array?](#q8-what-happens-if-we-omit-the-dependency-array)
9. [What does an empty dependency array mean?](#q9-what-does-an-empty-dependency-array-mean)
10. [What happens when dependencies are provided?](#q10-what-happens-when-dependencies-are-provided)
11. [How does React compare dependencies?](#q11-how-does-react-compare-dependencies)
12. [What are reactive values?](#q12-what-are-reactive-values)
13. [Why should every dependency be included?](#q13-why-should-every-dependency-be-included)
14. [What is an Effect cleanup function?](#q14-what-is-an-effect-cleanup-function)
15. [When does cleanup run?](#q15-when-does-cleanup-run)
16. [Why is cleanup important?](#q16-why-is-cleanup-important)
17. [Can useEffect be async?](#q17-can-useeffect-be-async)
18. [How do you fetch data inside an Effect?](#q18-how-do-you-fetch-data-inside-an-effect)
19. [How do you prevent stale async results?](#q19-how-do-you-prevent-stale-async-results)
20. [What is the difference between useEffect and useLayoutEffect?](#q20-what-is-the-difference-between-useeffect-and-uselayouteffect)
21. [When should useLayoutEffect be used?](#q21-when-should-uselayouteffect-be-used)
22. [Can useLayoutEffect affect performance?](#q22-can-uselayouteffect-affect-performance)
23. [What is useRef?](#q23-what-is-useref)
24. [Does changing ref.current cause a re-render?](#q24-does-changing-refcurrent-cause-a-re-render)
25. [What is the difference between useRef and useState?](#q25-what-is-the-difference-between-useref-and-usestate)
26. [How do you access a DOM element using useRef?](#q26-how-do-you-access-a-dom-element-using-useref)
27. [Can a ref be used to store a timer ID?](#q27-can-a-ref-be-used-to-store-a-timer-id)
28. [What is a stale closure in React?](#q28-what-is-a-stale-closure-in-react)
29. [How can you fix a stale closure?](#q29-how-can-you-fix-a-stale-closure)
30. [What is a custom Hook?](#q30-what-is-a-custom-hook)
31. [Do custom Hooks share state between components?](#q31-do-custom-hooks-share-state-between-components)
32. [What are the naming rules for custom Hooks?](#q32-what-are-the-naming-rules-for-custom-hooks)
33. [What is useImperativeHandle?](#q33-what-is-useimperativehandle)
34. [What is forwardRef?](#q34-what-is-forwardref)
35. [What changed about refs in React 19?](#q35-what-changed-about-refs-in-react-19)
36. [What does Strict Mode do to Effects in development?](#q36-what-does-strict-mode-do-to-effects-in-development)
37. [Why should Effects not be used for derived state?](#q37-why-should-effects-not-be-used-for-derived-state)
38. [How do you add and remove an event listener?](#q38-how-do-you-add-and-remove-an-event-listener)
39. [How do you implement an interval safely?](#q39-how-do-you-implement-an-interval-safely)
40. [Why can an Effect run repeatedly?](#q40-why-can-an-effect-run-repeatedly)
41. [What causes an infinite Effect loop?](#q41-what-causes-an-infinite-effect-loop)
42. [How do you debug an Effect that runs too often?](#q42-how-do-you-debug-an-effect-that-runs-too-often)
43. [Can Hooks be used in class components?](#q43-can-hooks-be-used-in-class-components)
44. [Can a custom Hook call another custom Hook?](#q44-can-a-custom-hook-call-another-custom-hook)
45. [What is the difference between an Effect and an event handler?](#q45-what-is-the-difference-between-an-effect-and-an-event-handler)
46. [How do you synchronize a third-party widget?](#q46-how-do-you-synchronize-a-third-party-widget)
47. [What is the difference between a ref and a callback ref?](#q47-what-is-the-difference-between-a-ref-and-a-callback-ref)
48. [What happens to a DOM ref when an element unmounts?](#q48-what-happens-to-a-dom-ref-when-an-element-unmounts)
49. [What are common mistakes with useRef?](#q49-what-are-common-mistakes-with-useref)
50. [Explain the overall relationship between state, refs, and Effects.](#q50-explain-the-overall-relationship-between-state-refs-and-effects)

---

## Fundamentals

### Q1. What are React Hooks?

**Answer:** React Hooks are functions that let function components use React features such as state, context, refs, and Effects. They also let us extract reusable stateful logic into custom Hooks. Examples include `useState`, `useEffect`, `useRef`, and `useContext`.

### Q2. What are the Rules of Hooks?

**Answer:** There are two main rules. First, I call Hooks only at the top level, not inside conditions, loops, or nested functions. Second, I call Hooks only from React function components or custom Hooks. These rules help React preserve the correct Hook call order between renders.

### Q3. Why must Hooks be called in the same order?

**Answer:** React relies on the order of Hook calls to associate each Hook with its stored data. If I call a Hook conditionally, the sequence can change between renders, and React may associate state with the wrong Hook. Keeping calls at the top level preserves that order.

### Q4. Can we call a Hook inside a condition?

**Answer:** No. A Hook should not be called inside an `if` statement, loop, or nested function. I call it unconditionally at the top level and put the condition inside the Hook's logic or in the component's returned UI.

### Q5. What is useEffect?

**Answer:** `useEffect` is a React Hook used to synchronize a component with an external system. I use it for tasks such as setting up subscriptions, timers, event listeners, connections, or synchronizing browser APIs with current component data.

### Q6. What is the purpose of useEffect?

**Answer:** Its purpose is to keep an external system synchronized with the component. For example, if a chat room ID changes, an Effect can disconnect from the previous room and connect to the new room. I avoid using it for values that can simply be calculated during rendering.

### Q7. When does useEffect run?

**Answer:** An Effect runs after React commits the relevant render. React runs its cleanup before setting up the Effect again when dependencies change, and it runs the final cleanup when the component is removed. The exact relationship with browser painting can vary with the interaction, so I do not rely on a universal fixed paint timing.

### Q8. What happens if we omit the dependency array?

**Answer:** If I omit the dependency array entirely, the Effect runs after every committed render of that component. I use this only when synchronization genuinely needs to happen after every commit, because it can otherwise cause unnecessary work.

### Q9. What does an empty dependency array mean?

**Answer:** An empty array means the Effect has no reactive dependencies. It runs after the component's initial commit, and its cleanup runs when the component is removed. In development Strict Mode, React can run an extra setup-and-cleanup cycle to help detect missing cleanup, so I do not treat it as an absolute exactly-once guarantee.

### Q10. What happens when dependencies are provided?

**Answer:** React runs the Effect after the initial commit and runs it again when one or more dependencies change. Before the new setup, React runs the previous cleanup. This lets the Effect stop synchronizing with old values and start synchronizing with the new ones.

### Q11. How does React compare dependencies?

**Answer:** React compares each dependency with its previous value using `Object.is`. Primitive values are compared by value, while objects and functions are compared by reference. That is why creating a new object or function on every render can cause an Effect to re-run.

### Q12. What are reactive values?

**Answer:** Reactive values are values that can change between renders, such as props, state, and variables or functions declared inside a component. If an Effect reads a reactive value, I normally include it in the dependency array so the Effect stays synchronized with the current value.

### Q13. Why should every dependency be included?

**Answer:** Every reactive value used by an Effect should be represented in its dependencies. Otherwise, the Effect may keep using an outdated value from an earlier render, which is called a stale closure. I follow the exhaustive-deps lint rule and restructure the code rather than suppressing warnings without a reason.

## Cleanup and asynchronous Effects

### Q14. What is an Effect cleanup function?

**Answer:** A cleanup function is the function returned from an Effect. It reverses or stops the external work started by that Effect. For example, it can remove an event listener, clear a timer, unsubscribe from a store, or disconnect a WebSocket.

### Q15. When does cleanup run?

**Answer:** Cleanup runs before React re-runs an Effect because its dependencies changed, and when the component is removed. In development Strict Mode, React may also run an extra setup-and-cleanup cycle to test whether the Effect is correctly reversible.

### Q16. Why is cleanup important?

**Answer:** Cleanup prevents resource leaks and duplicate external work. Without it, a component might leave old event listeners, intervals, or subscriptions active after its data changes or after it unmounts. Proper cleanup makes the Effect safe to re-run.

### Q17. Can useEffect be async?

**Answer:** I should not make the Effect callback itself `async`, because an async function returns a Promise, while React expects an Effect to return either a cleanup function or nothing. Instead, I define an async function inside the Effect and call it.

```jsx
useEffect(() => {
  async function loadData() {
    const response = await fetch("/api/data");
    const data = await response.json();
    setData(data);
  }

  loadData();
}, []);
```

### Q18. How do you fetch data inside an Effect?

**Answer:** I define an async function inside the Effect, call the API, check the response, and update state with the result. I also handle errors and make sure outdated requests cannot update the component after the relevant input changes. In larger applications, I consider a data-fetching library or framework.

### Q19. How do you prevent stale async results?

**Answer:** I can use an `ignore` flag in the cleanup so an older request cannot update state, or use `AbortController` when the API supports cancellation. This matters when a user changes an ID quickly and an earlier request finishes after a later one.

## Effect timing

### Q20. What is the difference between useEffect and useLayoutEffect?

**Answer:** `useEffect` is a passive Effect used for synchronization with external systems. `useLayoutEffect` runs after DOM mutations but before the browser repaints. I use `useLayoutEffect` when I must measure or adjust layout before the user sees the frame; otherwise, I prefer `useEffect`.

### Q21. When should useLayoutEffect be used?

**Answer:** I use it for work such as measuring an element's dimensions and applying a correction before paint, for example positioning a tooltip without visible flicker. I avoid it for ordinary subscriptions or network requests because those do not usually require pre-paint execution.

### Q22. Can useLayoutEffect affect performance?

**Answer:** Yes. Its work runs synchronously before the browser repaints, so expensive calculations can delay painting and make the interface feel less responsive. I use it only when pre-paint timing is genuinely required.

## Refs

### Q23. What is useRef?

**Answer:** `useRef` is a Hook that returns a persistent object with a `current` property. I use it to access DOM elements or store mutable values such as timer IDs. Updating `current` does not trigger a re-render.

### Q24. Does changing ref.current cause a re-render?

**Answer:** No. Changing `ref.current` does not tell React to render again. If a value must appear in the UI and update the UI when it changes, I use state instead.

### Q25. What is the difference between useRef and useState?

**Answer:** Both preserve information between renders, but they serve different purposes. Updating state schedules a re-render, while changing a ref does not. I use state for data that affects the UI and refs for DOM access or mutable values that do not need to trigger rendering.

### Q26. How do you access a DOM element using useRef?

**Answer:** I create a ref with `useRef(null)`, attach it to the element using the `ref` attribute, and access the DOM node through `ref.current`. For example, I can call `inputRef.current?.focus()` from a button handler to focus an input.

### Q27. Can a ref be used to store a timer ID?

**Answer:** Yes. A timer ID is a good example of mutable information that needs to persist but does not need to render the UI. I store it in a ref, clear it when stopping the timer, and clear it during Effect cleanup if the component is removed.

## Closures and custom Hooks

### Q28. What is a stale closure in React?

**Answer:** A stale closure happens when a callback keeps using values captured from an older render. For example, an interval created by an Effect with an empty dependency array may continue reading the initial state value even after the component state changes.

### Q29. How can you fix a stale closure?

**Answer:** The correct solution depends on the requirement. I can include the changing value in the dependency array, use a functional state update when I only need the previous state, or use a ref for a latest mutable value when a long-lived callback must read it without re-subscribing. I choose the simplest approach that matches the behavior.

### Q30. What is a custom Hook?

**Answer:** A custom Hook is a JavaScript function whose name starts with `use` and that can call other Hooks. It lets me extract reusable stateful behavior from components. For example, I can create `useWindowWidth` to manage a resize listener and return the current width.

### Q31. Do custom Hooks share state between components?

**Answer:** No, not automatically. Each component calling a custom Hook gets its own independent Hook state. A custom Hook shares logic, not one shared state instance. To share state, I can use Context, an external store, or another shared-state mechanism.

### Q32. What are the naming rules for custom Hooks?

**Answer:** A custom Hook's name should start with `use`, followed by a descriptive name, such as `useWindowWidth` or `useAuth`. This naming convention makes Hook usage recognizable to React tooling and developers. It should call other Hooks only at its top level.

### Q33. What is useImperativeHandle?

**Answer:** `useImperativeHandle` lets a component customize the value exposed through a ref. Instead of exposing its entire DOM node, a child can expose a limited API such as `focus()` or `clear()`. This is useful when a parent needs a specific imperative operation.

### Q34. What is forwardRef?

**Answer:** `forwardRef` is a React API traditionally used to let a function component receive a ref from its parent and pass it to a child DOM element or component. It is common in React 18 and earlier patterns and existing libraries.

### Q35. What changed about refs in React 19?

**Answer:** React 19 allows function components to receive `ref` as a prop, so new React 19 components generally do not need `forwardRef` just to receive a ref. `forwardRef` is still relevant in existing code and libraries, so I consider the React version when reading or writing a component.

## Strict Mode and design decisions

### Q36. What does Strict Mode do to Effects in development?

**Answer:** With Strict Mode enabled, React performs an additional setup-and-cleanup cycle for Effects in development. This helps reveal Effects that do not clean up correctly. I make the Effect safe to set up and clean up rather than trying to suppress the extra cycle with a ref flag.

### Q37. Why should Effects not be used for derived state?

**Answer:** Derived values can usually be calculated directly during rendering. If I copy a derived value into state through an Effect, I introduce an extra render and create two values that must stay synchronized. For example, I calculate `fullName` from `firstName` and `lastName` instead of storing it separately.

### Q38. How do you add and remove an event listener?

**Answer:** I add the listener in an Effect and return a cleanup function that removes the same listener. I use the same event type and callback reference so the browser can identify the listener to remove.

```jsx
useEffect(() => {
  function handleResize() {
    setWidth(window.innerWidth);
  }

  window.addEventListener("resize", handleResize);
  return () => window.removeEventListener("resize", handleResize);
}, []);
```

### Q39. How do you implement an interval safely?

**Answer:** I create the interval in an Effect and return cleanup that calls `clearInterval`. If the callback updates state based on its previous value, I use a functional update such as `setCount(current => current + 1)`. That avoids depending on a captured old state value.

### Q40. Why can an Effect run repeatedly?

**Answer:** An Effect can run again when a dependency changes. It can also run after every commit if no dependency array is provided. In development Strict Mode, React may perform an extra setup-and-cleanup cycle. I inspect the dependencies and the environment before deciding whether the behavior is a bug.

### Q41. What causes an infinite Effect loop?

**Answer:** A common cause is an Effect that updates state, causing a render, while one of its dependencies changes on every render. For example, depending on a newly created object can make the Effect run repeatedly. I check whether the state update is necessary and whether dependencies can be made stable or the Effect simplified.

### Q42. How do you debug an Effect that runs too often?

**Answer:** I inspect each dependency and check whether its value changes on every render. I look for objects and functions created inside the component, unnecessary state updates, and logic that should be calculated during render. I also check whether I am observing Strict Mode's development behavior.

### Q43. Can Hooks be used in class components?

**Answer:** No. Hooks are designed for function components and custom Hooks. Class components use class state and lifecycle methods instead. I can still use function components with Hooks inside an application that also contains class components.

### Q44. Can a custom Hook call another custom Hook?

**Answer:** Yes. A custom Hook can call built-in Hooks and other custom Hooks, provided it follows the Rules of Hooks. This lets me compose smaller reusable behaviors into a larger custom Hook.

### Q45. What is the difference between an Effect and an event handler?

**Answer:** An event handler runs because a specific interaction happened, such as a click or form submission. An Effect runs to synchronize an external system with the component's current state or props after a commit. I put click-specific actions in event handlers and synchronization logic in Effects.

### Q46. How do you synchronize a third-party widget?

**Answer:** I usually keep a ref to the DOM container or widget instance. I create or connect the widget in an Effect, update its configuration when relevant props change, and destroy or disconnect it in cleanup. I make sure the dependency list matches the values the widget needs to track.

### Q47. What is the difference between a ref and a callback ref?

**Answer:** An object ref created by `useRef` gives me a stable object whose `current` property React updates when it attaches or detaches the DOM node. A callback ref is a function that React calls with the node when it attaches and with `null` when it detaches. Callback refs are useful when I need to run logic at the moment the node is attached or removed.

### Q48. What happens to a DOM ref when an element unmounts?

**Answer:** React clears the ref when the referenced DOM element is removed, so the ref's `current` value becomes `null`. That is why I check that a ref exists before calling methods on it.

### Q49. What are common mistakes with useRef?

**Answer:** Common mistakes include using a ref for data that should trigger a UI update, forgetting that `current` may be `null`, and reading or mutating refs at inappropriate times during rendering. I use refs for imperative or mutable information and state for values that determine the rendered UI.

### Q50. Explain the overall relationship between state, refs, and Effects.

**Answer:** State stores data that affects what React renders. Refs store persistent mutable values or references without triggering a render. Effects synchronize the committed component with external systems. I use each for its intended purpose: state for UI, refs for imperative storage, and Effects for external synchronization.

---

## Practical code/output questions

### Q51. What will this Effect do?

```jsx
useEffect(() => {
  console.log("Hello");
});
```

**Answer:** It logs `Hello` after every committed render because there is no dependency array.

### Q52. What will this Effect do?

```jsx
useEffect(() => {
  console.log("Mounted");
}, []);
```

**Answer:** It runs after the initial commit. In development Strict Mode, React may run an additional setup-and-cleanup cycle, so the log can appear more than once during development checks.

### Q53. How many times does this Effect run when `count` changes?

```jsx
useEffect(() => {
  console.log(count);
}, [count]);
```

**Answer:** It runs after the initial commit and again whenever `count` changes. It does not run merely because an unrelated state value changes, assuming the dependency remains unchanged.

### Q54. Identify the problem.

```jsx
useEffect(() => {
  setFullName(firstName + " " + lastName);
}, [firstName, lastName]);
```

**Answer:** If `fullName` is only derived from the two names, storing it in state is unnecessary. I can calculate it during rendering:

```jsx
const fullName = `${firstName} ${lastName}`;
```

### Q55. Identify the problem.

```jsx
useEffect(async () => {
  const response = await fetch("/api/users");
  setUsers(await response.json());
}, []);
```

**Answer:** The Effect callback is async and therefore returns a Promise. React expects the callback to return a cleanup function or nothing. I define an inner async function and call it from the Effect.

### Q56. What is wrong with this code?

```jsx
if (isLoggedIn) {
  const [user, setUser] = useState(null);
}
```

**Answer:** The Hook is called conditionally, which violates the Rules of Hooks. I move the Hook to the top level and use the condition only for rendering or other logic.

### Q57. Why does this interval always log the initial count?

```jsx
useEffect(() => {
  const id = setInterval(() => console.log(count), 1000);
  return () => clearInterval(id);
}, []);
```

**Answer:** The interval callback closes over the `count` value from the render when the Effect was created. Since the dependency array is empty, the Effect is not recreated when count changes. I can include `count` as a dependency or use another pattern based on the intended behavior.

### Q58. How would you prevent an event listener from being registered multiple times?

**Answer:** I register it inside an Effect and return cleanup that removes the same callback. React then runs cleanup before re-running the Effect and when the component unmounts.

### Q59. A component needs to focus an input after a button click. Which Hook would you use?

**Answer:** I would use `useRef` to access the input DOM element, attach the ref to the input, and call its `focus()` method from the button's event handler.

### Q60. A component displays a calculated total from quantity and price. Should you use useEffect?

**Answer:** No. I would calculate `const total = quantity * price` during rendering. An Effect and a separate state variable would add unnecessary synchronization and an extra render.
