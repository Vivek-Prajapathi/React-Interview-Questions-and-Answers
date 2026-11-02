# Advanced Rendering and Reconciliation – Interview Questions and Answers

> **How to use this file:** Practise explaining the answers aloud. The answers are written in a natural interview style, with code and scenarios where they make the explanation clearer. Start with fundamentals, then move to implementation, debugging, and tricky questions.

---

## Table of contents

### Fundamentals
1. [What is rendering in React?](#1-what-is-rendering-in-react)
2. [Does every render update the browser DOM?](#2-does-every-render-update-the-browser-dom)
3. [What is the difference between rendering, reconciliation, committing, and painting?](#3-what-is-the-difference-between-rendering-reconciliation-committing-and-painting)
4. [What are the render and commit phases?](#4-what-are-the-render-and-commit-phases)
5. [What is reconciliation in React?](#5-what-is-reconciliation-in-react)
6. [Why does React use reconciliation?](#6-why-does-react-use-reconciliation)
7. [What is the difference between a React component, a React element, and a DOM node?](#7-what-is-the-difference-between-a-react-component-a-react-element-and-a-dom-node)
8. [What can cause a component to render again?](#8-what-can-cause-a-component-to-render-again)
9. [Does changing props always reset a component's state?](#9-does-changing-props-always-reset-a-components-state)
10. [Does a parent render normally cause its children to render?](#10-does-a-parent-render-normally-cause-its-children-to-render)

### Reconciliation and component identity
11. [How does React decide whether to preserve or recreate a component?](#11-how-does-react-decide-whether-to-preserve-or-recreate-a-component)
12. [What is component identity?](#12-what-is-component-identity)
13. [When does React preserve component state?](#13-when-does-react-preserve-component-state)
14. [What causes component state to reset?](#14-what-causes-component-state-to-reset)
15. [What happens when an element type changes?](#15-what-happens-when-an-element-type-changes)
16. [How does conditional rendering affect component state?](#16-how-does-conditional-rendering-affect-component-state)
17. [Why should components generally be defined outside other components?](#17-why-should-components-generally-be-defined-outside-other-components)
18. [How can a key be used to reset a form?](#18-how-can-a-key-be-used-to-reset-a-form)

### Keys and lists
19. [What are keys in React?](#19-what-are-keys-in-react)
20. [Why are keys important when rendering lists?](#20-why-are-keys-important-when-rendering-lists)
21. [What makes a good key?](#21-what-makes-a-good-key)
22. [Why can using an array index as a key cause bugs?](#22-why-can-using-an-array-index-as-a-key-cause-bugs)
23. [When is an index key acceptable?](#23-when-is-an-index-key-acceptable)
24. [Why should we avoid random keys?](#24-why-should-we-avoid-random-keys)
25. [Can a component access `props.key`?](#25-can-a-component-access-propskey)
26. [Where should a key be placed when using `map()`?](#26-where-should-a-key-be-placed-when-using-map)
27. [How do keys work with fragments?](#27-how-do-keys-work-with-fragments)
28. [Do keys need to be globally unique?](#28-do-keys-need-to-be-globally-unique)

### State updates and batching
29. [What is a state snapshot in React?](#29-what-is-a-state-snapshot-in-react)
30. [Why does logging state immediately after calling a setter show the old value?](#30-why-does-logging-state-immediately-after-calling-a-setter-show-the-old-value)
31. [What is the difference between direct and functional state updates?](#31-what-is-the-difference-between-direct-and-functional-state-updates)
32. [What is batching in React?](#32-what-is-batching-in-react)
33. [Why should state objects and arrays be updated immutably?](#33-why-should-state-objects-and-arrays-be-updated-immutably)

### Memoization and concurrent rendering
34. [What does `memo` do?](#34-what-does-memo-do)
35. [How does `memo` compare props by default?](#35-how-does-memo-compare-props-by-default)
36. [Why can an inline object or function cause a memoized child to render?](#36-why-can-an-inline-object-or-function-cause-a-memoized-child-to-render)
37. [Does `memo` guarantee that a component will never render?](#37-does-memo-guarantee-that-a-component-will-never-render)
38. [What is concurrent rendering?](#38-what-is-concurrent-rendering)
39. [Can React interrupt or abandon rendering work?](#39-can-react-interrupt-or-abandon-rendering-work)
40. [Why must component rendering be pure?](#40-why-must-component-rendering-be-pure)
41. [What is the role of `useTransition` in rendering?](#41-what-is-the-role-of-usetransition-in-rendering)

### Practical and scenario-based questions
42. [A child component renders when its parent state changes. Is that expected?](#42-a-child-component-renders-when-its-parent-state-changes-is-that-expected)
43. [A list row's input value appears beside the wrong item after reordering. What is the likely cause?](#43-a-list-rows-input-value-appears-beside-the-wrong-item-after-reordering-what-is-the-likely-cause)
44. [A form loses all its local state when switching between records. How would you investigate it?](#44-a-form-loses-all-its-local-state-when-switching-between-records-how-would-you-investigate-it)
45. [A memoized component still renders. What would you check?](#45-a-memoized-component-still-renders-what-would-you-check)
46. [How would you investigate an unexpected re-render?](#46-how-would-you-investigate-an-unexpected-re-render)
47. [How would you keep row-specific notes when filtering a list?](#47-how-would-you-keep-row-specific-notes-when-filtering-a-list)
48. [What happens if a component is removed and then rendered again?](#48-what-happens-if-a-component-is-removed-and-then-rendered-again)
49. [What is the difference between a render and a remount?](#49-what-is-the-difference-between-a-render-and-a-remount)
50. [What is the most important mental model for React reconciliation?](#50-what-is-the-most-important-mental-model-for-react-reconciliation)

---

# Fundamentals

## 1. What is rendering in React?

**Interview answer:**

Rendering is the process in which React calls a component to calculate what the UI should look like for the current props, state, and context. The component returns React elements that describe the desired UI. React then uses that description to work out whether anything needs to change in the browser DOM. So rendering means calculating the UI; it does not automatically mean that the DOM is updated.

## 2. Does every render update the browser DOM?

**Interview answer:**

No. A component can render and React can determine that its output does not require any DOM changes. For example, a parent may render again while a child returns the same host output. React may evaluate the child, but it does not need to recreate the child's DOM node. I distinguish a component render from a committed DOM mutation.

## 3. What is the difference between rendering, reconciliation, committing, and painting?

**Interview answer:**

These are related but different stages or activities. Rendering means evaluating components to calculate the next React element description. Reconciliation compares the previous and next descriptions to determine what should be preserved, updated, inserted, or removed. Commit is when React applies the completed changes to the host environment, such as the DOM. Painting is the browser drawing the visual result on the screen. I avoid using these terms interchangeably.

## 4. What are the render and commit phases?

**Interview answer:**

I explain a React update using two broad phases. During the render phase, React evaluates components and calculates the next UI. During the commit phase, React applies the completed changes to the DOM and performs commit-related work, such as updating refs and running layout effects at the appropriate point. The render phase must be pure because React may repeat or abandon render work.

## 5. What is reconciliation in React?

**Interview answer:**

Reconciliation is React's process of comparing the previous element tree with the next element tree to decide what should be preserved, updated, inserted, or removed. React uses element types, tree positions, and keys to help match elements. This process is important because it allows React to preserve component identity and state when the same logical component remains in the tree.

## 6. Why does React use reconciliation?

**Interview answer:**

React uses reconciliation to work out the changes needed between two UI descriptions instead of blindly rebuilding the whole interface. It can preserve existing elements and component state when identity remains stable, and replace only the parts whose identity or structure has changed. This helps avoid unnecessary work and prevents avoidable loss of browser state, such as focus.

## 7. What is the difference between a React component, a React element, and a DOM node?

**Interview answer:**

A component is the function or class that describes part of the UI. A React element is the lightweight description produced by JSX, such as `<Profile name="Asha" />`. A DOM node is the actual browser object created or updated when React commits the UI. A new React element description does not necessarily mean React creates a new DOM node.

## 8. What can cause a component to render again?

**Interview answer:**

Common reasons include a change to the component's own state, a parent rendering, a context value the component consumes changing, or a subscribed external-store snapshot changing. In development, Strict Mode can also invoke render logic extra times to help detect impure code. I check the actual cause before trying to optimise the component.

## 9. Does changing props always reset a component's state?

**Interview answer:**

No. Changing props does not automatically reset local state. If the component remains the same type at the same position with the same key, React normally preserves its state and provides the new props. If the component needs a complete reset when a record changes, I can deliberately change its key or implement a targeted state update.

## 10. Does a parent render normally cause its children to render?

**Interview answer:**

When a parent component renders, React normally evaluates its child components as part of calculating the new tree. This can happen even if a child has no props. However, `memo` can allow React to skip a child render when its props compare equal. I treat that as a performance optimisation, not as a correctness guarantee.

---

# Reconciliation and component identity

## 11. How does React decide whether to preserve or recreate a component?

**Interview answer:**

A useful mental model is to look at the component type, its position in the rendered tree, and its key. If the identity remains stable, React can preserve the component and its state. If the type changes, the component is removed, or its key changes, React generally treats it as a new identity and mounts a new instance.

## 12. What is component identity?

**Interview answer:**

Component identity describes which component instance in the rendered tree React is dealing with. It is not determined only by the component's name. The component type, its position in the tree, and its key all matter. React uses identity to decide whether state should be preserved or whether a new component instance should be created.

## 13. When does React preserve component state?

**Interview answer:**

React normally preserves state when the same component type remains at the same position in the tree with the same key. For example, if a parent changes a heading but keeps rendering the same `Counter` in the same position, the counter's state is preserved. The component function may run again, but its hook state remains associated with that component identity.

## 14. What causes component state to reset?

**Interview answer:**

State is generally reset when the component is removed from the tree, when its type changes, or when its key changes. In those cases React treats the next component as a new identity. The new instance starts with its initial state rather than inheriting the old instance's local state.

## 15. What happens when an element type changes?

**Interview answer:**

If an element type changes at the same position, React generally removes the old element and creates the new type. For example, changing from `<h1>` to `<p>` means React replaces the host element. Switching from one component type to another also creates a different component identity, so the old component's local state is not automatically transferred.

## 16. How does conditional rendering affect component state?

**Interview answer:**

If a condition removes a component from the rendered tree, React discards that component's local state. If the condition becomes true again later, React mounts a new instance with fresh initial state. If the component remains in the tree at the same identity while nearby content changes, its state can be preserved.

## 17. Why should components generally be defined outside other components?

**Interview answer:**

If I define a component function inside another component, a new function object is created every time the parent renders. React can interpret that as a different component type, which may cause the child to remount and lose its local state. I normally define component functions at module scope and pass required data through props.

**Example:**

```jsx
// Prefer this structure.
function Child({ label }) {
  const [value, setValue] = useState("");

  return (
    <input
      value={value}
      placeholder={label}
      onChange={(event) => setValue(event.target.value)}
    />
  );
}

function Parent() {
  return <Child label="Enter a value" />;
}
```

## 18. How can a key be used to reset a form?

**Interview answer:**

I can use the selected record's stable ID as the form component's key. When the selected ID changes, the key changes, so React treats the form as a new component identity and resets its local state.

```jsx
function EditPage({ employee }) {
  return (
    <EmployeeForm
      key={employee.id}
      employee={employee}
    />
  );
}
```

I use this when the whole form should start fresh. If only a few fields need updating, I may prefer an explicit state update instead.

---

# Keys and lists

## 19. What are keys in React?

**Interview answer:**

Keys are special identifiers that React uses to match elements among siblings in a collection. When I render a list using `map`, I normally use a stable ID from the data as the key. This helps React recognise the same logical item across renders, even when the list order changes.

```jsx
{users.map((user) => (
  <UserCard key={user.id} user={user} />
))}
```

## 20. Why are keys important when rendering lists?

**Interview answer:**

Keys help React identify which list item was added, removed, or moved. Without stable keys, React may rely on positions to match items. If the list changes, a component's local state can become associated with a different data item. Stable keys make list reconciliation more predictable.

## 21. What makes a good key?

**Interview answer:**

A good key is stable for the lifetime of the logical item and unique among its siblings. I prefer an ID from the backend or a stable identifier in the data. I avoid generating a new random key during every render because that makes React treat the item as new each time.

## 22. Why can using an array index as a key cause bugs?

**Interview answer:**

An array index identifies an item's current position, not the item itself. If I insert, delete, or reorder an item, the indexes of other items change. React may then reuse a row component for a different record, which can make local state or input values appear beside the wrong item. That is why I use stable IDs for dynamic lists.

## 23. When is an index key acceptable?

**Interview answer:**

An index can be acceptable for a static list where items will never be inserted, removed, filtered, or reordered, and where item components do not hold state that could become associated with a different record. For a dynamic list, a stable data ID is safer.

## 24. Why should we avoid random keys?

**Interview answer:**

A random key changes on every render. React therefore sees each item as a new identity and may unmount and remount the rows repeatedly. This can reset local state, lose focus, and create unnecessary work. I generate an ID when a record is created and then keep that ID stable.

## 25. Can a component access `props.key`?

**Interview answer:**

No. `key` is a special React attribute used by React during reconciliation. It is not passed to the component as a normal prop. If the component needs the ID, I pass it separately.

```jsx
<UserRow
  key={user.id}
  userId={user.id}
  user={user}
/>
```

## 26. Where should a key be placed when using `map()`?

**Interview answer:**

The key should be placed on the outermost element returned directly from the `map` callback, because that is the element React needs to identify among its siblings.

```jsx
{users.map((user) => (
  <UserRow key={user.id} user={user} />
))}
```

Putting a key on an element inside `UserRow` does not provide the key for the mapped sibling.

## 27. How do keys work with fragments?

**Interview answer:**

If each mapped item needs to return multiple sibling elements, I use the explicit `Fragment` component and put the key on it. The shorthand fragment syntax cannot receive a key.

```jsx
import { Fragment } from "react";

{users.map((user) => (
  <Fragment key={user.id}>
    <h3>{user.name}</h3>
    <p>{user.email}</p>
  </Fragment>
))}
```

## 28. Do keys need to be globally unique?

**Interview answer:**

No. Keys need to be unique among the siblings in the same collection. Two separate lists can use the same key values without a problem because React matches keys within their respective sibling sets.

---

# State updates and batching

## 29. What is a state snapshot in React?

**Interview answer:**

A state variable is a snapshot belonging to a particular render. When React calls a component, the component receives the state values for that render. Calling a setter requests another render with a new state value; it does not mutate the variable in the currently executing function.

## 30. Why does logging state immediately after calling a setter show the old value?

**Interview answer:**

The event handler is still running with the state snapshot from the render that created it. Calling the setter schedules an update, but it does not change that snapshot. The next render receives the updated value.

```jsx
function handleClick() {
  setCount(count + 1);
  console.log(count); // Current render's value
}
```

## 31. What is the difference between direct and functional state updates?

**Interview answer:**

A direct update calculates the next value from the state snapshot currently available in the handler. A functional update receives the latest queued previous state from React. When the next value depends on the previous value, I prefer the functional form.

```jsx
// Direct update
setCount(count + 1);

// Functional update
setCount((previousCount) => previousCount + 1);
```

If I call the direct form three times using the same snapshot, I may only request the same next value three times. Three functional increments are processed in sequence.

## 32. What is batching in React?

**Interview answer:**

Batching means React groups multiple state updates so it can process them together and avoid unnecessary intermediate renders. For example, if one event handler updates a name and a role, React can apply those updates in one render cycle. I avoid depending on an exact render count because scheduling and development checks can affect what I observe.

## 33. Why should state objects and arrays be updated immutably?

**Interview answer:**

React state should be treated as immutable. If I mutate an existing object and pass the same reference back, React may not recognise the update as expected, and other parts of the application can observe unexpected shared mutations. I create a new object or array so the change is explicit.

```jsx
// Avoid mutation
user.name = "Asha";
setUser(user);

// Create a new object
setUser((previous) => ({
  ...previous,
  name: "Asha",
}));
```

---

# Memoization and concurrent rendering

## 34. What does `memo` do?

**Interview answer:**

`memo` is a performance optimisation that can let React skip rendering a component when its props have not changed according to the comparison. It is useful when a component renders often with the same props and its rendering work is meaningful. It is not required for correctness.

```jsx
const UserCard = memo(function UserCard({ name }) {
  return <p>{name}</p>;
});
```

## 35. How does `memo` compare props by default?

**Interview answer:**

By default, `memo` compares each prop in the previous render with the corresponding prop in the next render using `Object.is`. This is a shallow comparison of the prop values, not a deep comparison of nested objects.

## 36. Why can an inline object or function cause a memoized child to render?

**Interview answer:**

An object or function created inside a parent render gets a new reference when the parent renders again. Even if the contents or function logic look identical, `Object.is` sees the new reference as different. Therefore, the memoized child can render because its prop changed.

```jsx
// New object reference on each Parent render
<Child options={{ theme: "dark" }} />
```

If profiling shows this matters, I can stabilise the value with `useMemo` or a callback with `useCallback`.

## 37. Does `memo` guarantee that a component will never render?

**Interview answer:**

No. `memo` is an optimisation, not a guarantee. The component can still render when its own state changes, when a context value it consumes changes, or when its props compare as changed. I never rely on memoization for application correctness.

## 38. What is concurrent rendering?

**Interview answer:**

Concurrent rendering is React's ability to schedule rendering work with different priorities. It allows urgent updates, such as typing into an input, to be prioritised over less urgent updates, such as rendering a large result list. React may pause, restart, or abandon render work so the interface remains responsive. It does not mean components are necessarily rendered simultaneously on multiple threads.

## 39. Can React interrupt or abandon rendering work?

**Interview answer:**

Yes. React can start rendering an update and later pause, restart, or abandon that work if a more urgent update arrives or the result is no longer needed. React commits a completed update rather than exposing a partially committed tree. This is one reason component rendering must be pure.

## 40. Why must component rendering be pure?

**Interview answer:**

Rendering must be pure because React may call a component more than once or abandon a render attempt before commit. If I send a request, mutate a global variable, or manually change the DOM during render, that side effect could happen even when the corresponding UI is never committed. I keep side effects in event handlers or effect hooks.

## 41. What is the role of `useTransition` in rendering?

**Interview answer:**

`useTransition` lets me mark certain state updates as non-urgent transitions. This can help React keep urgent interactions responsive while it works on less urgent UI updates. For example, I can update a controlled input immediately and mark the result-list update as a transition. It does not make expensive calculations disappear, so I still need efficient code.

```jsx
const [isPending, startTransition] = useTransition();

function handleChange(event) {
  const value = event.target.value;

  setInput(value); // Urgent update

  startTransition(() => {
    setQuery(value); // Non-urgent update
  });
}
```

---

# Practical and scenario-based questions

## 42. A child component renders when its parent state changes. Is that expected?

**Interview answer:**

Yes, that is normal React behaviour. When the parent renders, React normally evaluates its child components as part of calculating the next tree. If the child is expensive and its props remain unchanged, I can consider `memo` after profiling. I would not assume every child render is a performance problem.

## 43. A list row's input value appears beside the wrong item after reordering. What is the likely cause?

**Interview answer:**

The first thing I would check is the list key. If the rows use array indexes as keys, React may reuse a row component for a different data record after reordering. Since the input's local state belongs to the row component identity, the value can appear beside the wrong record. I would use a stable ID from the data as the key.

## 44. A form loses all its local state when switching between records. How would you investigate it?

**Interview answer:**

I would check whether the form is being removed from the tree, whether its component type changes, and whether its key changes. I would also check whether the component is defined inside its parent. If the requirement is to reset the form for every selected record, using the record ID as the key is a clear solution. If the form should preserve some fields, I would manage those fields explicitly instead of remounting the entire form.

## 45. A memoized component still renders. What would you check?

**Interview answer:**

I would check whether any prop has a new reference, especially inline objects, arrays, or functions. I would also check whether the component's own state changed or whether it consumes a context value that changed. Finally, I would consider development Strict Mode. `memo` only skips some renders; it does not guarantee that the component will never render.

## 46. How would you investigate an unexpected re-render?

**Interview answer:**

I would use React DevTools to locate the component and the Profiler to understand committed rendering work. Then I would inspect its state, props, context, and parent. I would look for newly created object or function props, unnecessary state updates, and context values that change too broadly. After identifying the cause, I would choose the smallest appropriate optimisation and measure again.

## 47. How would you keep row-specific notes when filtering a list?

**Interview answer:**

If a row is filtered out, it is removed from the rendered tree, so its local component state is discarded. If the notes must survive filtering, I would store them in the parent or a suitable store, indexed by the user's stable ID. Then the note data remains available even when the row component is temporarily not rendered.

**Example:**

```jsx
const [notesByUserId, setNotesByUserId] = useState({});

function updateNote(userId, note) {
  setNotesByUserId((previous) => ({
    ...previous,
    [userId]: note,
  }));
}
```

## 48. What happens if a component is removed and then rendered again?

**Interview answer:**

When a component is removed from the tree, React unmounts it and discards its local state. If it is rendered again later, React mounts a new instance with its initial state. If some information needs to survive hiding and showing, I would store it in a component that remains mounted or in a shared state store.

## 49. What is the difference between a render and a remount?

**Interview answer:**

A render means React evaluates a component to calculate its next UI. The component can render many times while remaining the same component instance and preserving its state. A remount means the previous instance was removed and a new instance was created. A remount resets local state and runs the mounting lifecycle again.

## 50. What is the most important mental model for React reconciliation?

**Interview answer:**

The most important mental model is that React uses component or element type, position in the rendered tree, and keys to determine identity. If identity stays stable, React can preserve state and update what changed. If identity changes or the component is removed, React generally creates a new instance and the previous local state is discarded. This mental model explains most everyday rendering, list-key, and state-reset behaviour.

---

# Final interview revision checklist

Before an interview, make sure you can explain these without memorising the wording:

- [ ] Rendering is not the same as a DOM update.
- [ ] Render phase calculates; commit phase applies completed changes.
- [ ] Reconciliation compares the previous and next element descriptions.
- [ ] Component identity influences state preservation.
- [ ] Type, position, and key are important identity clues.
- [ ] Stable keys are essential for dynamic lists.
- [ ] Index keys can associate state with the wrong list item.
- [ ] Changing a key can intentionally reset a component subtree.
- [ ] Removing a component discards its local state.
- [ ] Nested component definitions can cause identity changes.
- [ ] State setters do not mutate the current render's snapshot.
- [ ] Functional updates are useful when next state depends on previous state.
- [ ] Batching groups updates.
- [ ] `memo` is an optimisation, not a correctness guarantee.
- [ ] Concurrent rendering makes pure render logic essential.
- [ ] React DevTools Profiler is more useful than relying only on console logs for performance analysis.

## A strong closing explanation

If an interviewer asks you to summarise rendering and reconciliation, you can say:

“React rendering is the process of calculating the next UI from props, state, and context. Reconciliation compares that new UI description with the previous one and determines what should be preserved or changed. React uses component identity, tree position, and keys to decide whether state can be preserved or whether a component should be recreated. Finally, the commit phase applies the completed changes to the DOM. Understanding these ideas helps me debug unnecessary renders, list-key bugs, and unexpected state resets.”
