# Advanced Rendering and Reconciliation in React

> **Purpose of this chapter:** Understand how React calculates UI, decides what should be preserved or changed, and applies updates. This chapter focuses on the mental models and practical rules that help explain rendering behaviour, state preservation, list keys, and performance.

---

## Table of contents

1. [Learning roadmap](#1-learning-roadmap)
2. [What rendering means in React](#2-what-rendering-means-in-react)
3. [What causes a component to render](#3-what-causes-a-component-to-render)
4. [Render phase and commit phase](#4-render-phase-and-commit-phase)
5. [React elements, components, and the DOM](#5-react-elements-components-and-the-dom)
6. [What reconciliation means](#6-what-reconciliation-means)
7. [How React matches elements](#7-how-react-matches-elements)
8. [Component identity and state preservation](#8-component-identity-and-state-preservation)
9. [Keys: purpose, rules, and correct usage](#9-keys-purpose-rules-and-correct-usage)
10. [Why array-index keys can cause bugs](#10-why-array-index-keys-can-cause-bugs)
11. [Changing component types and resetting state](#11-changing-component-types-and-resetting-state)
12. [Conditional rendering and component position](#12-conditional-rendering-and-component-position)
13. [Nested component definitions and identity bugs](#13-nested-component-definitions-and-identity-bugs)
14. [State snapshots, queued updates, and batching](#14-state-snapshots-queued-updates-and-batching)
15. [Parent and child rendering behaviour](#15-parent-and-child-rendering-behaviour)
16. [How memoization relates to rendering](#16-how-memoization-relates-to-rendering)
17. [Concurrent rendering and interruptible work](#17-concurrent-rendering-and-interruptible-work)
18. [Practical implementation: searchable, reorderable list](#18-practical-implementation-searchable-reorderable-list)
19. [Debugging unexpected renders and state resets](#19-debugging-unexpected-renders-and-state-resets)
20. [Common mistakes and best practices](#20-common-mistakes-and-best-practices)
21. [Quick revision summary](#21-quick-revision-summary)

---

# 1. Learning roadmap

Learn this topic in the following sequence:

1. Understand what a React component returns.
2. Understand the difference between rendering and updating the browser DOM.
3. Learn the common reasons React evaluates a component again.
4. Understand the render and commit phases.
5. Learn what reconciliation does.
6. Understand how element type, tree position, and keys help React preserve identity.
7. Learn how keys work in lists and why stable IDs matter.
8. Understand when local component state is preserved or reset.
9. Learn how state snapshots, queued updates, and batching affect rendering.
10. Understand how parent renders, `memo`, and referential equality interact.
11. Learn why concurrent rendering makes pure render logic important.
12. Build and debug a practical list example.

Do not begin by memorising internal implementation names. First understand the observable behaviour: **what causes a render, what React preserves, what React replaces, and why.**

---

# 2. What rendering means in React

## 2.1 Definition

Rendering is the process in which React calls a component to calculate the React element description for the current inputs.

Those inputs commonly include:

- Props passed by the parent.
- The component's current state.
- Context values consumed by the component.
- Values provided by supported external-store subscriptions.

Example:

```jsx
function Welcome({ name }) {
  return <h1>Welcome, {name}</h1>;
}
```

When React renders `Welcome` with `name="Asha"`, the component returns a description equivalent to:

```jsx
<h1>Welcome, Asha</h1>
```

This returned value describes what the UI should look like. It is not itself the browser DOM node.

## 2.2 Rendering is not the same as painting pixels

These terms describe different work:

| Term | Meaning |
|---|---|
| Component render | React calls a component and calculates its returned element description. |
| Reconciliation | React compares the new description with the previous one and determines what should be preserved or changed. |
| Commit | React applies the completed changes to the host environment, such as the browser DOM. |
| Browser paint | The browser draws the visual result on the screen. |

A component can render without React needing to change the DOM. A DOM update can also cause the browser to perform layout and paint work, but React rendering and browser painting are not interchangeable terms.

## 2.3 Example: a render that changes text

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}

export default Counter;
```

Initial render:

```text
Count: 0
```

After clicking the button:

1. The event handler requests a state update.
2. React schedules work for the component.
3. React calls `Counter` to calculate the next element description.
4. React compares the new output with the previous output.
5. React commits the required change, including the button's text.

React does not need to throw away and recreate the entire page just because the counter changed.

## 2.4 Why this distinction matters

If a developer sees a component's render log, they may assume the whole DOM was rebuilt. That conclusion is incorrect.

For performance debugging, distinguish these questions:

- Was the component function called?
- Did React commit a change for that component?
- Did the browser perform layout or paint work?
- Was the work actually expensive?

Use React DevTools Profiler and browser performance tools when measuring. A `console.log` inside a component can help identify calls, but it is not a complete performance measurement.

---

# 3. What causes a component to render

A component can be evaluated again for several reasons.

## 3.1 Its own state changes

```jsx
import { useState } from "react";

function LikeButton() {
  const [liked, setLiked] = useState(false);

  return (
    <button onClick={() => setLiked((previous) => !previous)}>
      {liked ? "Liked" : "Like"}
    </button>
  );
}
```

When `setLiked` requests a different state, React schedules an update. React then evaluates the component to calculate the next UI.

A setter does not guarantee a visible DOM change. If the next state is considered equal to the current state using React's state equality behaviour, React may skip rendering work for that update.

## 3.2 Its parent renders

```jsx
function Child() {
  console.log("Child evaluated");
  return <p>I am the child</p>;
}

function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount((value) => value + 1)}>
        Parent count: {count}
      </button>
      <Child />
    </>
  );
}
```

When `Parent` renders, React normally evaluates `Child` as part of calculating the parent's returned tree. The fact that `Child` has no props does not, by itself, guarantee that React skips it.

A memoized child may be skipped when its props compare equal. That is an optimization and should not be treated as a correctness rule.

## 3.3 A consumed context value changes

```jsx
import { createContext, useContext, useState } from "react";

const ThemeContext = createContext("light");

function ThemeLabel() {
  const theme = useContext(ThemeContext);
  return <p>Current theme: {theme}</p>;
}

function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={theme}>
      <button
        onClick={() =>
          setTheme((current) =>
            current === "light" ? "dark" : "light"
          )
        }
      >
        Change theme
      </button>

      <ThemeLabel />
    </ThemeContext.Provider>
  );
}
```

When the provider value changes, components that consume that context are updated so they can use the new value.

## 3.4 An external store reports a change

React provides `useSyncExternalStore` for subscribing to external stores in a way that integrates with React's rendering model. When the subscribed snapshot changes, React can update the component.

```jsx
import { useSyncExternalStore } from "react";

function subscribe(callback) {
  window.addEventListener("online", callback);
  window.addEventListener("offline", callback);

  return () => {
    window.removeEventListener("online", callback);
    window.removeEventListener("offline", callback);
  };
}

function getSnapshot() {
  return navigator.onLine;
}

function NetworkStatus() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);

  return <p>{isOnline ? "Online" : "Offline"}</p>;
}
```

This example reads the browser's online status. In server-rendered applications, provide an appropriate server snapshot as required by the `useSyncExternalStore` API.

## 3.5 Development Strict Mode checks

When Strict Mode is enabled, React may call component render logic an extra time in development to help expose impure rendering. This is a development check, not a promise that production will perform the same extra calls.

Do not “fix” a harmless development-only extra render by adding random memoization. First check whether the component is pure and whether there is a real performance problem.

## 3.6 Important: a render trigger is not always a DOM update

A component can be evaluated because its parent rendered, but React may find that the returned host output has not changed. Therefore, always distinguish the component call from the committed DOM changes.

---

# 4. Render phase and commit phase

React's work is commonly described using two broad phases: **render** and **commit**.

## 4.1 Render phase: calculate the next UI

During the render phase, React works out what the next UI should be.

At a conceptual level:

1. React receives or processes an update.
2. React evaluates the relevant component functions.
3. Components read their current props, state, and context.
4. Components return React elements.
5. React reconciles the new descriptions with the previous tree.
6. React determines the host changes required for the completed update.

The render phase should be pure. React may repeat or abandon render work, especially when using concurrent rendering capabilities.

## 4.2 Commit phase: apply the completed update

During the commit phase, React applies the completed result to the host environment.

For a browser application, commit work can include:

- Creating and inserting DOM nodes.
- Updating DOM properties and text.
- Removing DOM nodes.
- Updating refs.
- Running layout effects at the appropriate point in the commit lifecycle.
- Scheduling passive effects such as `useEffect`.

The full internal lifecycle has more detail than this high-level model. For application development, the key distinction is that render calculates the next result and commit applies it.

## 4.3 Example with logs

```jsx
import { useEffect, useLayoutEffect, useState } from "react";

function PhaseExample() {
  const [count, setCount] = useState(0);

  console.log("Render phase: component function");

  useLayoutEffect(() => {
    console.log("Layout effect");
  });

  useEffect(() => {
    console.log("Passive effect");
  });

  return (
    <button onClick={() => setCount((value) => value + 1)}>
      Count: {count}
    </button>
  );
}
```

A simplified way to understand an update is:

```text
State update requested
        |
        v
React renders the component
        |
        v
React calculates/reconciles the next tree
        |
        v
React commits the completed changes
        |
        v
Layout effects run in the commit lifecycle
        |
        v
Passive effects are processed after commit
```

Do not use this diagram as a promise of exact console ordering in every environment. Strict Mode, scheduling, and effect timing can affect observed logs.

## 4.4 Why render must not contain side effects

Incorrect:

```jsx
function Page() {
  document.title = "Dashboard"; // Avoid changing external state during render

  return <h1>Dashboard</h1>;
}
```

Correct:

```jsx
import { useEffect } from "react";

function Page() {
  useEffect(() => {
    document.title = "Dashboard";
  }, []);

  return <h1>Dashboard</h1>;
}
```

A render attempt might be repeated or abandoned. If it changes an external system during render, that side effect could happen even though the corresponding UI update is never committed.

## 4.5 When should each mechanism be used?

| Requirement | Suitable place |
|---|---|
| Calculate a value from props or state | During render |
| Respond to a user's click | Event handler |
| Subscribe to an external system | Usually `useEffect` |
| Measure or synchronously adjust layout after DOM commit | `useLayoutEffect`, only when necessary |
| Update the DOM manually | Usually avoid; use refs only for imperative integration cases |

Do not use an effect simply to copy one piece of React state into another when the value can be calculated during render. Derived values usually do not need their own state.

---

# 5. React elements, components, and the DOM

Understanding these three terms prevents many rendering misunderstandings.

## 5.1 Component

A component is a function (or class in older/class-based code) that describes part of the UI.

```jsx
function Profile({ name }) {
  return <h2>{name}</h2>;
}
```

`Profile` is the component function.

## 5.2 React element

A React element is the lightweight description produced by JSX or React APIs. It describes the type and props of something React should render.

```jsx
const element = <Profile name="Asha" />;
```

`element` is a React element description. It is not the `Profile` component function and it is not a DOM node.

## 5.3 DOM node

The browser DOM node is the actual object representing an element in the document.

For example, after React commits `<h2>Asha</h2>`, the browser has an `h2` DOM element.

## 5.4 Why the distinction matters

React can create a new element description during a render while preserving the existing DOM node. A new React element object does not automatically mean a new browser DOM element.

For example:

```jsx
function Heading({ title }) {
  return <h1 className="page-title">{title}</h1>;
}
```

If `title` changes, React can update the text content of the existing `h1` rather than replacing the whole page.

---

# 6. What reconciliation means

## 6.1 Definition

Reconciliation is the process React uses to compare the previous rendered element tree with the next element tree and determine what should be preserved, updated, inserted, or removed.

It answers practical questions such as:

- Is this still the same component?
- Can its state be preserved?
- Did a DOM element's type change?
- Which list item corresponds to which previous item?
- Which parts of the host tree need to change?

## 6.2 Example: same host element type

```jsx
function Greeting({ isLoggedIn }) {
  return isLoggedIn
    ? <h1>Welcome back</h1>
    : <h1>Please sign in</h1>;
}
```

Both branches return an `h1` at the same position. React can keep the existing `h1` and update its text.

## 6.3 Example: different host element type

```jsx
function Greeting({ isLoggedIn }) {
  return isLoggedIn
    ? <h1>Welcome back</h1>
    : <p>Please sign in</p>;
}
```

The element type changes from `h1` to `p`. React treats these as different host element types, so the old element is removed and the new one is created.

## 6.4 Reconciliation does not mean “React compares every DOM attribute in the entire page”

That is an oversimplification. React uses the structure of its element tree and its reconciliation algorithm to determine work. Developers should understand the observable identity rules rather than assume React performs a generic deep comparison of the entire DOM.

## 6.5 Why reconciliation is important

Reconciliation helps React:

- Preserve component state when identity remains stable.
- Reuse existing host elements when possible.
- Update only the parts that need changes.
- Match collection items across renders using keys.
- Replace subtrees when identity or type changes.

---

# 7. How React matches elements

A practical mental model for identity uses three important ideas:

1. **Element/component type**
2. **Position in the rendered tree**
3. **Key**, when supplied

This is a teaching model for reasoning about component state and list updates. It is not a complete description of all internal reconciler implementation details.

## 7.1 Same type, same position, same key

```jsx
function App({ title }) {
  return <ProfileCard title={title} />;
}
```

If `ProfileCard` remains at the same position with the same key (or no explicit key), React can preserve its component state while passing the updated `title` prop.

## 7.2 Different component type

```jsx
function App({ isAdmin }) {
  return isAdmin
    ? <AdminDashboard />
    : <UserDashboard />;
}
```

`AdminDashboard` and `UserDashboard` are different component types. Switching branches replaces one component identity with the other. Their local state is not transferred automatically.

## 7.3 Different key

```jsx
function App({ accountId }) {
  return <AccountEditor key={accountId} accountId={accountId} />;
}
```

When `accountId` changes, the key changes. React treats the editor as a different identity and resets its local state.

## 7.4 Position matters

```jsx
function App({ showFirst }) {
  return (
    <div>
      {showFirst ? <Counter /> : <p>Placeholder</p>}
      <Counter />
    </div>
  );
}
```

The first child position changes between a `Counter` and a `p`. The second `Counter` remains at the second child position. The first and second counters are separate component positions and have separate state.

Do not reason only from the component's name. Consider where it appears in the returned tree and whether its identity remains the same.

---

# 8. Component identity and state preservation

## 8.1 Where does component state belong?

It is tempting to think that state lives inside a component function. That is not how hooks work.

A function component is called again when React renders it. Ordinary local variables are recreated each time:

```jsx
function Example() {
  let count = 0;

  function handleClick() {
    count += 1;
    console.log(count);
  }

  return <button onClick={handleClick}>Click</button>;
}
```

This local variable does not persist across component renders.

React state is different:

```jsx
function Example() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount((value) => value + 1)}>
      {count}
    </button>
  );
}
```

React associates the hook state with the component's identity in the rendered tree.

## 8.2 When state is preserved

React normally preserves state when the same component type remains at the same position with the same key.

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount((value) => value + 1)}>
      Count: {count}
    </button>
  );
}

function App({ heading }) {
  return (
    <main>
      <h1>{heading}</h1>
      <Counter />
    </main>
  );
}
```

Changing `heading` does not remove or replace `Counter`. The counter's state is preserved.

## 8.3 When state is discarded

If a component is removed from the tree, its state is discarded.

```jsx
function App() {
  const [show, setShow] = useState(true);

  return (
    <>
      <button onClick={() => setShow((value) => !value)}>
        Toggle counter
      </button>

      {show && <Counter />}
    </>
  );
}
```

Steps:

1. Initially, `Counter` is mounted and its count is `0`.
2. The user increments it to `3`.
3. The user hides the counter.
4. `Counter` is removed from the tree.
5. Its local state is discarded.
6. The user shows it again.
7. A new `Counter` instance mounts with its initial count of `0`.

## 8.4 State is not preserved merely because the same component name appears

A component can have the same source-code name but a different identity if it is rendered at a different position, has a different key, or is a different function/type.

Think about the rendered tree, not only the JSX label.

---

# 9. Keys: purpose, rules, and correct usage

## 9.1 What is a key?

A key is a special React attribute that identifies an element among its siblings in a collection. It helps React match an item in the previous list with the corresponding item in the next list.

Example:

```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

Here, `user.id` is the identity for each list item.

## 9.2 Why keys are needed

Imagine the current list is:

```text
Asha
Ben
Chloe
```

Then a new item is inserted at the beginning:

```text
Daniel
Asha
Ben
Chloe
```

React needs to understand that Asha, Ben, and Chloe are still the same logical records and Daniel is the new record.

Stable keys provide that information.

## 9.3 Key rules

- A key should be stable across renders for the same logical item.
- A key should be unique among siblings in that list.
- Use a stable identifier from the data whenever possible.
- Do not generate a new random key during each render.
- Put the key on the element returned directly by the `map`.
- Keys are not passed to the component as ordinary props.
- A key does not need to be globally unique across the whole application.

## 9.4 Correct key placement

Correct:

```jsx
function ProductList({ products }) {
  return (
    <ul>
      {products.map((product) => (
        <ProductItem key={product.id} product={product} />
      ))}
    </ul>
  );
}
```

Incorrect placement:

```jsx
function ProductList({ products }) {
  return (
    <ul>
      {products.map((product) => (
        <ProductItem product={product} />
      ))}
    </ul>
  );
}
```

The second example has no key on the mapped sibling element.

## 9.5 Keys with fragments

If one list item returns multiple sibling elements, use an explicit keyed Fragment:

```jsx
import { Fragment } from "react";

function Glossary({ entries }) {
  return entries.map((entry) => (
    <Fragment key={entry.id}>
      <dt>{entry.term}</dt>
      <dd>{entry.description}</dd>
    </Fragment>
  ));
}
```

The shorthand fragment syntax `<>...</>` cannot receive a key. Use `<Fragment key={...}>` when a mapped item needs a fragment.

## 9.6 Keys are not component props

This does not work:

```jsx
function UserRow(props) {
  console.log(props.key); // Not the key supplied by React
  return <p>{props.user.name}</p>;
}
```

If the child needs the ID, pass it separately:

```jsx
<UserRow
  key={user.id}
  userId={user.id}
  user={user}
/>
```

---

# 10. Why array-index keys can cause bugs

## 10.1 What is an index key?

An index key uses the current position in an array:

```jsx
{users.map((user, index) => (
  <UserRow key={index} user={user} />
))}
```

The key identifies a position, not the logical record.

## 10.2 The state-mismatch problem

Suppose each row has its own input state.

```jsx
function UserRow({ user }) {
  const [note, setNote] = useState("");

  return (
    <label>
      {user.name}
      <input
        value={note}
        onChange={(event) => setNote(event.target.value)}
        placeholder="Add a note"
      />
    </label>
  );
}
```

The list initially contains:

```text
Index 0 -> Asha
Index 1 -> Ben
Index 2 -> Chloe
```

The user types a note into Ben's input.

Now insert Daniel at the beginning:

```text
Index 0 -> Daniel
Index 1 -> Asha
Index 2 -> Ben
Index 3 -> Chloe
```

If the index is used as the key, React can match the old component at index `1` with the new item at index `1`. The local input state that used to appear beside Ben may now appear beside Asha.

The problem is not that React randomly moves state. React is following the identity information supplied by the keys. The index keys describe positions, and the positions now refer to different records.

## 10.3 Correct implementation with stable IDs

```jsx
function UserList({ users }) {
  return users.map((user) => (
    <UserRow key={user.id} user={user} />
  ));
}
```

Now the component associated with Ben keeps Ben's identity even when Ben moves to another position in the list.

## 10.4 When can an index key be acceptable?

An index key can be acceptable when all of these conditions are true:

- The list is static.
- Items are never reordered.
- Items are never inserted or removed in the middle.
- Items are not filtered in a way that changes positions.
- The item component does not hold state that could become associated with another record.

If the list is dynamic, use stable IDs.

## 10.5 Why random keys are worse

Incorrect:

```jsx
{users.map((user) => (
  <UserRow key={Math.random()} user={user} />
))}
```

A new key is produced on every render. React sees every row as a new identity, so it may unmount the old rows and mount new ones. This can reset local state and cause unnecessary DOM work.

---

# 11. Changing component types and resetting state

## 11.1 Changing the component type

```jsx
function Page({ isAdmin }) {
  return isAdmin ? <AdminPanel /> : <CustomerPanel />;
}
```

These are different component types. When the condition changes, React replaces the rendered component identity. State from `AdminPanel` is not transferred to `CustomerPanel`.

## 11.2 Changing a key

```jsx
function ProfilePage({ profileId }) {
  return <ProfileEditor key={profileId} profileId={profileId} />;
}
```

If `profileId` changes, the key changes. React resets the component subtree's state by treating it as a new identity.

## 11.3 Why intentional reset is useful

Imagine an edit form for a selected employee.

- Employee 101 is selected.
- The form contains local input state for employee 101.
- The user selects employee 205.
- The product requirement says the form should start fresh for employee 205.

A key based on the employee ID is a clear way to express that requirement:

```jsx
function EmployeePage({ employee }) {
  return (
    <EmployeeForm
      key={employee.id}
      employee={employee}
    />
  );
}
```

## 11.4 When not to use a key reset

Do not change a key merely to silence a rendering issue. Changing a key remounts the subtree and can reset:

- Local component state.
- Uncontrolled input values.
- Focus and text selection.
- Child component state.
- Effects and subscriptions, which will be cleaned up and set up again as the subtree unmounts and mounts.

If only one field needs updating, an explicit state update may be more appropriate than resetting the entire subtree.

---

# 12. Conditional rendering and component position

## 12.1 A component removed by a condition loses its local state

```jsx
function App() {
  const [visible, setVisible] = useState(true);

  return (
    <>
      <button onClick={() => setVisible((value) => !value)}>
        Show or hide
      </button>

      {visible ? <Counter /> : null}
    </>
  );
}
```

When `visible` is false, `Counter` is not in the tree. Its state is discarded.

## 12.2 A nearby sibling changing does not necessarily reset another component

```jsx
function App({ showMessage }) {
  return (
    <main>
      <Counter />
      {showMessage && <p>Hello</p>}
    </main>
  );
}
```

`Counter` remains at the same position. Adding or removing the paragraph after it does not, by itself, remove the counter.

## 12.3 Same component type in two different positions

```jsx
function App({ swap }) {
  return (
    <div>
      {swap ? <Counter /> : <p>Placeholder</p>}
      <Counter />
    </div>
  );
}
```

There are two child positions. The counter in the second position is a separate instance from a counter in the first position. State belongs to each identity in the tree, not to the component's function name globally.

---

# 13. Nested component definitions and identity bugs

## 13.1 The problem

Avoid defining a component function inside another component function:

```jsx
function Parent() {
  function Child() {
    const [text, setText] = useState("");

    return (
      <input
        value={text}
        onChange={(event) => setText(event.target.value)}
      />
    );
  }

  return <Child />;
}
```

Every time `Parent` executes, JavaScript creates a new `Child` function object. React can see a different component type on the next render. The child can be remounted, which may reset its state and DOM identity.

## 13.2 Correct approach

Define the child component at module scope:

```jsx
function Child() {
  const [text, setText] = useState("");

  return (
    <input
      value={text}
      onChange={(event) => setText(event.target.value)}
    />
  );
}

function Parent() {
  return <Child />;
}
```

Now `Child` has a stable function identity between parent renders.

## 13.3 What if the child needs data from the parent?

Pass it through props:

```jsx
function Child({ placeholder }) {
  const [text, setText] = useState("");

  return (
    <input
      value={text}
      placeholder={placeholder}
      onChange={(event) => setText(event.target.value)}
    />
  );
}

function Parent() {
  return <Child placeholder="Enter your name" />;
}
```

Use props, context, or another appropriate data-sharing approach rather than creating a new component type inside the parent.

---

# 14. State snapshots, queued updates, and batching

Rendering and reconciliation are closely connected to how React processes state updates.

## 14.1 State is a snapshot

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
    console.log(count);
  }

  return <button onClick={handleClick}>Count: {count}</button>;
}
```

If the displayed count is `0`, clicking the button requests the next state as `1`. The `console.log` still reads `0`, because the currently executing event handler belongs to the render in which `count` was `0`.

The setter does not mutate the current variable. React schedules a future render with the next state.

## 14.2 Direct updates can use the same snapshot

```jsx
function handleClick() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
}
```

If `count` is `0` in this render, all three expressions calculate `1`. The final requested value is therefore `1`, not `3`.

## 14.3 Functional updates use the queued previous state

```jsx
function handleClick() {
  setCount((previous) => previous + 1);
  setCount((previous) => previous + 1);
  setCount((previous) => previous + 1);
}
```

React processes the updater functions in sequence:

```text
Initial state: 0
First updater:  0 -> 1
Second updater: 1 -> 2
Third updater:  2 -> 3
Final state:    3
```

Use functional updates whenever the next value depends on the previous value.

## 14.4 What is batching?

Batching means React groups multiple state updates so that it can process them together, reducing unnecessary rendering work.

```jsx
function ProfileEditor() {
  const [name, setName] = useState("");
  const [role, setRole] = useState("User");

  function handleSave() {
    setName("Asha");
    setRole("Admin");
  }

  return (
    <>
      <button onClick={handleSave}>Apply changes</button>
      <p>{name}</p>
      <p>{role}</p>
    </>
  );
}
```

React can batch the updates from this event so the UI is updated together rather than committing an intermediate UI after each setter.

Modern React also batches updates in more situations than older versions did. Avoid writing application logic that depends on an exact render count.

## 14.5 State update does not mean “mutate the old state object”

For objects and arrays, create a new value rather than mutating the existing state object.

Incorrect:

```jsx
user.name = "Asha";
setUser(user);
```

Correct:

```jsx
setUser((previousUser) => ({
  ...previousUser,
  name: "Asha",
}));
```

React uses identity comparisons in state update decisions. Mutating and reusing the same object reference can prevent the update from being recognised as expected and makes rendering behaviour harder to reason about.

---

# 15. Parent and child rendering behaviour

## 15.1 Parent renders can cause child components to be evaluated

```jsx
function Child() {
  console.log("Child rendered");
  return <p>Child content</p>;
}

function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount((value) => value + 1)}>
        {count}
      </button>
      <Child />
    </>
  );
}
```

When `Parent` renders, React normally evaluates the `Child` element as part of the new tree.

## 15.2 `memo` can skip some child renders

```jsx
import { memo, useState } from "react";

const Child = memo(function Child({ label }) {
  console.log("Child evaluated");
  return <p>{label}</p>;
});

function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount((value) => value + 1)}>
        Parent count: {count}
      </button>
      <Child label="Static label" />
    </>
  );
}
```

When `Parent` renders, the `label` prop remains the same string. `memo` can allow React to skip evaluating `Child`.

## 15.3 Why `memo` is not automatic for every child

A child may be cheap to render. Memoization also has comparison costs and can make code more complex. React does not require every component to be memoized.

Use it when profiling or a clear rendering pattern shows that skipping the work is beneficial.

---

# 16. How memoization relates to rendering

## 16.1 What `memo` does

`memo` is a higher-order component that can let React skip rendering a component when its props have not changed according to the comparison function.

```jsx
import { memo } from "react";

const UserCard = memo(function UserCard({ name }) {
  return <article>{name}</article>;
});
```

By default, React compares each prop with `Object.is`.

## 16.2 Object and array props

This creates a new object on each parent render:

```jsx
<UserCard user={{ name: "Asha" }} />
```

Even though the contents look the same, the object reference is new. A shallow prop comparison sees it as changed.

If this is a measured performance issue, stabilise the object:

```jsx
const user = useMemo(() => ({ name: "Asha" }), []);

return <UserCard user={user} />;
```

Do not add `useMemo` automatically. The extra memoization should solve a real problem.

## 16.3 Function props

A function declared inside a component is recreated when that component renders:

```jsx
function Parent() {
  const handleClick = () => {
    console.log("Clicked");
  };

  return <Child onClick={handleClick} />;
}
```

If `Child` is memoized, the new function reference can make its props appear changed. `useCallback` can preserve a function reference when that is useful:

```jsx
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

Again, use it when it has a meaningful benefit, not as a default requirement.

## 16.4 `memo` does not block every render

A memoized component can still render when:

- Its own state changes.
- A context value it consumes changes.
- Its props change according to the comparison.
- React has a reason to render it.

Memoization is an optimisation, not a semantic guarantee.

---

# 17. Concurrent rendering and interruptible work

## 17.1 What does concurrent rendering mean?

Concurrent rendering describes React's ability to schedule rendering work so urgent updates can be prioritised and less urgent work can be interrupted, restarted, or abandoned.

For example:

- Typing into an input should feel immediate.
- Updating a very large result list may be less urgent.
- React can prioritise the input interaction while working on the list update.

Concurrent rendering is not the same as rendering components simultaneously on multiple CPU threads.

## 17.2 Why purity becomes especially important

React may start rendering an update and later decide not to commit that attempt. If a component performs a side effect while rendering, the side effect can happen even though the rendered result is abandoned.

Therefore:

- Do not send network requests directly during render.
- Do not mutate global variables during render.
- Do not manually change the DOM during render.
- Do not subscribe to external systems during render.
- Use event handlers and effects for side effects.

## 17.3 Example: prioritising an update with `useTransition`

```jsx
import { useState, useTransition } from "react";

function SearchResults({ items }) {
  const [input, setInput] = useState("");
  const [query, setQuery] = useState("");
  const [isPending, startTransition] = useTransition();

  function handleChange(event) {
    const nextValue = event.target.value;

    // Urgent update: update the controlled input immediately.
    setInput(nextValue);

    // Non-urgent update: update the result query as a transition.
    startTransition(() => {
      setQuery(nextValue);
    });
  }

  const results = items.filter((item) =>
    item.toLowerCase().includes(query.toLowerCase())
  );

  return (
    <>
      <input value={input} onChange={handleChange} />

      {isPending && <p>Updating results...</p>}

      <ul>
        {results.map((item) => (
          <li key={item}>{item}</li>
        ))}
      </ul>
    </>
  );
}
```

This illustrates the scheduling intent: keep the input update urgent and mark the result-query update as non-urgent. For large datasets, also consider efficient filtering, deferred values, server-side search, or list virtualization.

## 17.4 What concurrent rendering does not mean

It does not mean:

- React commits half of a component tree.
- Every render is performed on another thread.
- Developers can put side effects in render.
- Every update automatically becomes faster.
- `useTransition` is a replacement for debouncing or efficient data processing.

It is a scheduling capability. The application still needs sensible state design and efficient rendering work.

---

# 18. Practical implementation: searchable, reorderable list

This example brings together rendering, state, stable keys, and component identity.

## 18.1 Requirement

Build a small user list that supports:

- Displaying users.
- Searching users.
- Moving a user to the top.
- Keeping each user's note associated with the correct user.
- Adding a user.

Each user has a stable ID. That ID is used as the React key.

## 18.2 Complete code

```jsx
import { useMemo, useState } from "react";

const initialUsers = [
  { id: "u-101", name: "Asha" },
  { id: "u-102", name: "Ben" },
  { id: "u-103", name: "Chloe" },
];

function UserRow({ user, onMoveToTop }) {
  const [note, setNote] = useState("");

  return (
    <li>
      <strong>{user.name}</strong>

      <input
        value={note}
        onChange={(event) => setNote(event.target.value)}
        placeholder={`Note for ${user.name}`}
      />

      <button onClick={() => onMoveToTop(user.id)}>
        Move to top
      </button>
    </li>
  );
}

export default function UserManager() {
  const [users, setUsers] = useState(initialUsers);
  const [search, setSearch] = useState("");

  const visibleUsers = useMemo(() => {
    const normalisedSearch = search.trim().toLowerCase();

    return users.filter((user) =>
      user.name.toLowerCase().includes(normalisedSearch)
    );
  }, [users, search]);

  function moveToTop(userId) {
    setUsers((currentUsers) => {
      const selectedUser = currentUsers.find(
        (user) => user.id === userId
      );

      if (!selectedUser) {
        return currentUsers;
      }

      return [
        selectedUser,
        ...currentUsers.filter((user) => user.id !== userId),
      ];
    });
  }

  function addUser() {
    const newUser = {
      id: crypto.randomUUID(),
      name: `User ${users.length + 1}`,
    };

    setUsers((currentUsers) => [...currentUsers, newUser]);
  }

  return (
    <section>
      <h2>User manager</h2>

      <input
        value={search}
        onChange={(event) => setSearch(event.target.value)}
        placeholder="Search users"
      />

      <button onClick={addUser}>Add user</button>

      <ul>
        {visibleUsers.map((user) => (
          <UserRow
            key={user.id}
            user={user}
            onMoveToTop={moveToTop}
          />
        ))}
      </ul>
    </section>
  );
}
```

## 18.3 How the example works

### Step 1: The initial state

`users` stores the user records. Each record has an ID that remains stable when the record moves.

### Step 2: The search state

`search` stores the text entered into the search field. Updating it causes `UserManager` to render again.

### Step 3: The visible list

`visibleUsers` is derived from `users` and `search`. It is calculated with `useMemo` here to demonstrate memoization. For a small list, a direct `filter` during render would also be perfectly reasonable.

### Step 4: Each row owns its note state

`UserRow` owns the note for that user. React can preserve the row's local state when the list order changes because the key remains tied to the user's ID.

### Step 5: Reordering

`moveToTop` creates a new array. It does not mutate the existing state array. React renders the new order, and stable keys allow React to match each row with the same user record.

### Step 6: Adding a user

A new ID is generated when the user is created, not during rendering. That ID is then reused as the key on later renders.

## 18.4 Important implementation notes

- `crypto.randomUUID()` is used at the time a record is created. Do not call it inside the `map` expression to generate keys during every render.
- In an application with a backend, use the stable ID returned by the backend when available.
- If a user is filtered out, that row is removed from the rendered tree. Its local row state is therefore discarded. If notes must survive filtering, store them in parent state or an external store keyed by user ID.
- `useMemo` is optional for a small list. It is included to illustrate a derived calculation, not because every filter operation requires memoization.

## 18.5 Experiment: replace the stable key with an index

Change:

```jsx
<UserRow key={user.id} user={user} onMoveToTop={moveToTop} />
```

to:

```jsx
<UserRow
  key={visibleUsers.indexOf(user)}
  user={user}
  onMoveToTop={moveToTop}
/>
```

Then type different notes and reorder the list. You may observe notes appearing beside the wrong user because identity is tied to the current position.

Restore the stable ID key after the experiment.

---

# 19. Debugging unexpected renders and state resets

Use this checklist when the UI behaves unexpectedly.

## 19.1 A component renders more often than expected

Check:

1. Did its own state change?
2. Did its parent render?
3. Did a context value it consumes change?
4. Is an external-store snapshot changing?
5. Is Strict Mode enabled in development?
6. Are props being recreated as new objects, arrays, or functions?
7. Is the component expensive, or is the render harmless?

Use React DevTools Profiler to inspect committed rendering work. Do not optimise based only on a high number of console messages.

## 19.2 An input loses its value after reordering a list

Check:

1. Is the list using array indexes as keys?
2. Is a random key generated during render?
3. Does the item have a stable ID?
4. Is the input component being removed and mounted again?
5. Is its parent changing the component type or key?

Use a stable key based on the data record.

## 19.3 A form resets when a parent updates

Check:

1. Is the form component defined inside the parent function?
2. Does the form's key change?
3. Is the form removed by conditional rendering?
4. Does the parent switch between different component types?
5. Is the form state being explicitly reset by an effect or event handler?

## 19.4 A memoized child still renders

Check:

1. Did a prop change?
2. Is an object or array created inline?
3. Is a callback recreated on each parent render?
4. Did the child's own state change?
5. Did a context value consumed by the child change?
6. Is the observed call a development Strict Mode check?

Remember that `memo` is an optimisation, not a guarantee.

## 19.5 A practical debugging technique

Temporarily add a render log:

```jsx
function UserCard({ user }) {
  console.log("UserCard render:", user.id);

  return <p>{user.name}</p>;
}
```

Then use React DevTools Components and Profiler panels to inspect the component tree and committed work. Remove noisy logs after debugging.

---

# 20. Common mistakes and best practices

| Mistake | Why it is a problem | Better practice |
|---|---|---|
| Assuming every render changes the DOM | Render and commit are different | Measure component renders and committed work separately |
| Using array indexes for dynamic lists | State can become associated with another item | Use stable data IDs |
| Generating random keys during render | Every render creates new identities | Generate an ID when creating the record |
| Defining components inside components | Component type identity can change | Define component types at module scope |
| Performing side effects during render | Render can repeat or be abandoned | Use event handlers or effects |
| Expecting state setters to mutate current variables | State is a render snapshot | Use the next render or functional updates |
| Mutating state objects and arrays | Reusing references can hide changes and cause bugs | Create new objects and arrays |
| Adding `memo` everywhere | Adds complexity without guaranteed benefit | Profile and optimise meaningful costs |
| Changing keys to force arbitrary updates | Remounts the subtree and resets state | Change keys only when a new identity/reset is intended |
| Storing easily derived values in state | Creates duplicate state that can become inconsistent | Calculate derived values during render |

## Best-practice checklist

- Keep component render logic pure.
- Use stable keys for dynamic collections.
- Keep component definitions at module scope.
- Use functional state updates when the next state depends on the previous state.
- Update arrays and objects immutably.
- Understand whether a component is being removed or merely receiving new props.
- Use keys intentionally when a subtree should reset.
- Use `memo`, `useMemo`, and `useCallback` only when they solve a meaningful performance problem.
- Use React DevTools Profiler rather than relying only on console logs.
- Do not depend on an exact render count for application correctness.

---

# 21. Quick revision summary

| Concept | Remember |
|---|---|
| Rendering | React evaluates components to calculate the next UI description |
| Reconciliation | React compares the previous and next element trees |
| Commit | React applies completed changes to the host environment |
| Render purity | Render must not perform side effects |
| Component identity | Type, position, and key are important to state preservation |
| Stable key | Identifies the same logical list item across renders |
| Index key | Risky when a list can change order or membership |
| Changed key | Creates a new identity and resets that subtree's state |
| Conditional removal | Removing a component discards its local state |
| State snapshot | A setter requests a future render; it does not mutate the current variable |
| Functional update | Calculates next state from the latest queued previous state |
| Batching | Groups updates to reduce unnecessary rendering work |
| `memo` | Can skip some renders when props compare equal; not a correctness guarantee |
| Concurrent rendering | Allows React to prioritise, interrupt, restart, or abandon render work |

## The central mental model

When React renders, it calculates what the UI should look like. During reconciliation, it works out which existing identities can be preserved and which elements need to change. During commit, it applies the completed changes.

When debugging, ask three questions:

1. **Why did React render this component?**
2. **Did its identity remain the same, or did its type, position, or key change?**
3. **What did React actually commit to the DOM?**

If you can answer these three questions, you can explain most everyday React rendering, reconciliation, list-key, and state-preservation behaviour.
