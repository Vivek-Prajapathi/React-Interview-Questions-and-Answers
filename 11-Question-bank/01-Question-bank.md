# React Fundamentals — Interview Questions & Answers

## Questions

1. [What is React?](#1-what-is-react)
2. [Why was React created?](#2-why-was-react-created)
3. [What are the main features of React?](#3-what-are-the-main-features-of-react)
4. [What is a component?](#4-what-is-a-component)
5. [What is the difference between Functional and Class Components?](#5-what-is-the-difference-between-functional-and-class-components)
6. [What is JSX?](#6-what-is-jsx)
7. [Why do we use JSX?](#7-why-do-we-use-jsx)
8. [Is JSX HTML?](#8-is-jsx-html)
9. [How does JSX convert to JavaScript?](#9-how-does-jsx-convert-to-javascript)
10. [What is React.createElement()?](#10-what-is-reactcreateelement)
11. [What is the Virtual DOM?](#11-what-is-the-virtual-dom)
12. [Why is React called a library?](#12-why-is-react-called-a-library)
13. [What is declarative programming?](#13-what-is-declarative-programming)
14. [What does declarative mean in React?](#14-what-does-declarative-mean-in-react)
15. [What is component composition?](#15-what-is-component-composition)
16. [What is the difference between Props and State?](#16-what-is-the-difference-between-props-and-state)
17. [What is one-way data flow?](#17-what-is-one-way-data-flow)
18. [Why do we create reusable components?](#18-why-do-we-create-reusable-components)
19. [What is the purpose of key in React?](#19-what-is-the-purpose-of-key-in-react)
20. [Why should we not use array index as key?](#20-why-should-we-not-use-array-index-as-key)
21. [What happens if a key changes?](#21-what-happens-if-a-key-changes)
22. [Why does React need keys?](#22-why-does-react-need-keys)
23. [How does React use keys during reconciliation?](#23-how-does-react-use-keys-during-reconciliation)

---

# Questions & Answers

## 1. What is React?

### Interview Answer

> React is a JavaScript library developed by Meta for building user interfaces, especially dynamic and interactive web applications.
>
> React is component-based and declarative. We build the UI using reusable components, describe what the UI should look like for a particular state, and React handles updating the actual DOM efficiently.
>
> React also uses a reconciliation process to determine what changes are required when the state or props change.

### Example

```jsx
function App() {
  return <h1>Hello React</h1>;
}
```

Here, `App` is a React component that describes what should appear on the screen.

---

## 2. Why was React created?

### Interview Answer

> React was created to make it easier to build and maintain complex, highly interactive user interfaces.
>
> In traditional JavaScript applications, developers often had to manually find DOM elements and update them whenever application data changed. As applications became larger, keeping the UI synchronized with application state became difficult.
>
> React introduced a declarative and component-based approach where developers describe the desired UI, and React determines the necessary updates.

### Example

```javascript
document.getElementById("count").textContent = count;
```

In traditional JavaScript, we manually update the DOM.

In React:

```jsx
function Counter({ count }) {
  return <h1>{count}</h1>;
}
```

We describe what the UI should look like based on `count`.

---

## 3. What are the main features of React?

### Interview Answer

The main features of React are:

1. Component-based architecture
2. Declarative programming
3. JSX
4. One-way data flow
5. Reconciliation
6. Hooks
7. Reusable components
8. Strong ecosystem

### Component-Based Architecture

```jsx
function Navbar() {
  return <nav>Navbar</nav>;
}
```

### Declarative Programming

We describe what the UI should look like rather than manually manipulating the DOM.

### JSX

```jsx
const element = <h1>Hello</h1>;
```

### One-Way Data Flow

```text
Parent
  ↓
Props
  ↓
Child
```

### Hooks

```jsx
const [count, setCount] = useState(0);
```

---

## 4. What is a component?

### Interview Answer

> A component is an independent, reusable piece of UI. It contains the logic and structure required to render a particular part of an application.
>
> In modern React, components are usually JavaScript functions that return React elements.

### Example

```jsx
function Welcome() {
  return <h1>Welcome to React</h1>;
}
```

We can reuse it:

```jsx
function App() {
  return (
    <>
      <Welcome />
      <Welcome />
    </>
  );
}
```

---

## 5. What is the difference between Functional and Class Components?

### Functional Component

```jsx
function User() {
  return <h1>User</h1>;
}
```

Modern React uses functional components with Hooks.

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

### Class Component

```jsx
class Counter extends React.Component {
  state = {
    count: 0
  };

  render() {
    return <h1>{this.state.count}</h1>;
  }
}
```

### Main Difference

| Functional | Class |
|---|---|
| JavaScript function | JavaScript class |
| Uses Hooks | Uses lifecycle methods |
| Modern approach | Mostly legacy |
| Simpler syntax | More verbose |
| No `this` | Uses `this` |
| Preferred for new code | Still supported |

### Interview Answer

> Functional components are the modern approach in React because Hooks allow them to use state, effects, context, and other React features without classes. Class components are still supported but are mainly seen in older codebases and some legacy patterns.

---

## 6. What is JSX?

### Interview Answer

> JSX stands for JavaScript XML. It is a syntax extension for JavaScript that allows us to write UI-like markup inside JavaScript code.
>
> JSX is not HTML. It is transformed into JavaScript during the build process.

### Example

```jsx
const element = <h1>Hello World</h1>;
```

---

## 7. Why do we use JSX?

### Interview Answer

> JSX allows us to describe the UI and its logic close together. It makes component structure easier to understand and provides a syntax that looks similar to HTML while still being JavaScript.

### Without JSX

```jsx
React.createElement(
  "h1",
  null,
  "Hello World"
);
```

### With JSX

```jsx
<h1>Hello World</h1>
```

---

## 8. Is JSX HTML?

### Interview Answer

> No, JSX is not HTML. JSX is a syntax extension for JavaScript that looks similar to HTML.
>
> It gets transformed into JavaScript expressions that create React elements.

For example:

```jsx
<h1>Hello</h1>
```

is transformed into JavaScript by the JSX toolchain.

### JSX Differences

```jsx
className
```

instead of:

```html
class
```

And:

```jsx
onClick
```

instead of:

```html
onclick
```

---

## 9. How does JSX convert to JavaScript?

### Interview Answer

> JSX is transformed by the React/JavaScript build toolchain. Depending on the configured JSX transform, JSX can compile to calls such as `jsx()` or, with the classic transform, `React.createElement()`.

### JSX

```jsx
const element = <h1>Hello</h1>;
```

### Classic transformation

```jsx
const element = React.createElement(
  "h1",
  null,
  "Hello"
);
```

Modern React projects commonly use the automatic JSX runtime.

### Flow

```text
JSX
 ↓
JSX Transform
 ↓
JavaScript
 ↓
React Elements
 ↓
React Rendering
 ↓
DOM
```

---

## 10. What is React.createElement()?

### Interview Answer

> `React.createElement()` is an API that creates a React element description. It does not directly create a DOM element.
>
> React later uses these element descriptions during rendering and reconciliation.

### Syntax

```jsx
React.createElement(
  type,
  props,
  children
);
```

### Example

```jsx
React.createElement(
  "h1",
  { className: "title" },
  "Hello"
);
```

### JSX Equivalent

```jsx
<h1 className="title">
  Hello
</h1>
```

### Important Difference

| React.createElement | document.createElement |
|---|---|
| Creates React element description | Creates actual DOM element |
| Used by React | Browser DOM API |
| Doesn't directly modify DOM | Creates a DOM node |

---

## 11. What is the Virtual DOM?

### Interview Answer

> The Virtual DOM is a conceptual representation of the UI that React uses during its rendering and reconciliation process.
>
> When state or props change, React produces a new representation of the UI, compares it with the previous one, and determines the necessary changes to apply to the host environment, such as the browser DOM.

### Flow

```text
State / Props Change
        ↓
Render
        ↓
New React Element Tree
        ↓
Reconciliation
        ↓
Determine Necessary Changes
        ↓
Commit
        ↓
Browser DOM Updated
```

### Important Interview Point

Do **not** say:

> "Virtual DOM makes React always faster."

A better answer is:

> React uses rendering and reconciliation to determine the necessary updates to the host environment. Performance depends on the application and its rendering patterns.

---

## 12. Why is React called a library?

### Interview Answer

> React is primarily focused on the UI layer. It provides the core tools for building and rendering components, but it does not prescribe a complete application architecture.
>
> Other concerns such as routing, data fetching, and state management can be handled using separate libraries or frameworks.

### Example

```text
React
 ├── UI
 ├── Components
 └── Rendering

React Router
 └── Routing

Axios / Fetch
 └── API Requests

Redux / Zustand / Context
 └── State Management
```

---

## 13. What is declarative programming?

### Interview Answer

> Declarative programming means describing what result we want rather than manually describing every step required to achieve that result.

### Imperative

```javascript
const button = document.querySelector("button");

button.addEventListener("click", () => {
  document.querySelector("#message").textContent = "Hello";
});
```

We manually control the DOM.

### Declarative

```jsx
function App({ loggedIn }) {
  return (
    <div>
      {loggedIn ? <Dashboard /> : <Login />}
    </div>
  );
}
```

We describe what UI should exist for the current state.

---

## 14. What does declarative mean in React?

### Interview Answer

> In React, declarative means that we describe what the UI should look like based on the current state and props. We don't manually tell React which DOM nodes to create, remove, or update for every state change.

Example:

```jsx
function Counter({ count }) {
  return <h1>{count}</h1>;
}
```

If:

```text
count = 0
```

the UI represents:

```text
Count: 0
```

If:

```text
count = 1
```

the UI represents:

```text
Count: 1
```

React handles the update process.

---

## 15. What is component composition?

### Interview Answer

> Component composition means building complex UI by combining smaller, reusable components instead of creating one large component.
>
> React encourages composition because it makes components easier to reuse, test, maintain, and understand.

### Example

```jsx
function Navbar() {
  return <nav>Navbar</nav>;
}

function Sidebar() {
  return <aside>Sidebar</aside>;
}

function Content() {
  return <main>Content</main>;
}

function App() {
  return (
    <>
      <Navbar />
      <Sidebar />
      <Content />
    </>
  );
}
```

### Using `children`

```jsx
function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}
```

Usage:

```jsx
<Card>
  <h2>Hello</h2>
  <p>Welcome</p>
</Card>
```

### Interview Point

> React generally favors composition over inheritance for sharing UI structure and behavior.

---

## 16. What is the difference between Props and State?

| Props | State |
|---|---|
| Passed from parent | Managed by component |
| Read-only from child's perspective | Updated using React state APIs |
| Used to configure a component | Represents changing data |
| Parent controls the value | Component owns the state |
| Flows into the component | Changes can trigger re-render |

### Props Example

```jsx
function User({ name }) {
  return <h1>Hello {name}</h1>;
}

function App() {
  return <User name="Nikitha" />;
}
```

### State Example

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

### Interview Answer

> Props are inputs passed from a parent to a child, while state is data managed by a component. Props are read-only from the receiving component's perspective, while state can be updated through React's state APIs.

---

## 17. What is one-way data flow?

### Interview Answer

> React follows one-way data flow, meaning data generally flows from parent components to child components through props.

```text
Parent
  ↓
Props
  ↓
Child
```

### Example

```jsx
function Parent() {
  const name = "Nikitha";

  return <Child name={name} />;
}

function Child({ name }) {
  return <h1>{name}</h1>;
}
```

### How can a child update parent state?

The parent can pass a callback:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <Child
      count={count}
      onIncrement={() => setCount(count + 1)}
    />
  );
}
```

```jsx
function Child({ count, onIncrement }) {
  return (
    <button onClick={onIncrement}>
      {count}
    </button>
  );
}
```

### Flow

```text
Parent owns state
      ↓
Passes state as props
      ↓
Child
      ↓
Child calls callback
      ↓
Parent updates state
      ↓
Parent re-renders
```

---

## 18. Why do we create reusable components?

### Interview Answer

> Reusable components help us avoid duplication and make the application easier to maintain. Instead of writing the same UI and logic repeatedly, we create a component once and reuse it with different props.

### Example

```jsx
function Button({ children, onClick }) {
  return (
    <button onClick={onClick}>
      {children}
    </button>
  );
}
```

Then:

```jsx
<Button>Save</Button>
<Button>Delete</Button>
<Button>Cancel</Button>
```

### Benefits

- Less duplication
- Easier maintenance
- Consistent UI
- Better testing
- Better organization
- Easier scalability

---

## 19. What is the purpose of `key` in React?

### Interview Answer

> A `key` gives React a stable identity for an element among its siblings when rendering lists. React uses keys during reconciliation to determine which items correspond to previous items, which were added, removed, or moved, and which component instances can retain their state.

### Example

```jsx
const users = [
  { id: 1, name: "A" },
  { id: 2, name: "B" },
  { id: 3, name: "C" }
];

function Users() {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          {user.name}
        </li>
      ))}
    </ul>
  );
}
```

### Important

The `key` is primarily for React's reconciliation process.

It is **not automatically passed as a normal prop**.

```jsx
<User key={user.id} />
```

If the component needs the ID:

```jsx
<User
  key={user.id}
  id={user.id}
/>
```

---

## 20. Why should we not use array index as key?

### Interview Answer

> Array indexes can be used as keys when a list is completely static and never changes. However, they can cause problems when items are inserted, deleted, reordered, or filtered because the index represents the item's position rather than its identity.

Suppose:

```text
Index    Item

0        A
1        B
2        C
```

After removing `A`:

```text
Index    Item

0        B
1        C
```

The indexes changed.

If we use:

```jsx
key={index}
```

React may associate the previous key `0` with `A` and the new key `0` with `B`.

That can cause incorrect component identity and state preservation.

### Better

```jsx
key={user.id}
```

### When index can be acceptable

Index can be acceptable when:

- The list is static.
- Items are never reordered.
- Items are never inserted or deleted.
- The order never changes.
- Item identity does not need to be preserved independently of position.

---

## 21. What happens if a key changes?

### Interview Answer

> When a component's key changes, React treats it as a different identity. The old component can be removed and a new component can be created, which means the component's local state is reset.

Example:

```jsx
function App() {
  const [id, setId] = useState(1);

  return <User key={id} />;
}
```

If:

```text
key = 1
```

changes to:

```text
key = 2
```

React treats them as different component identities.

Conceptually:

```text
Old component
     ↓
Unmount
     ↓
New component
     ↓
Mount
```

Therefore, local state is not preserved between those identities.

### Important Interview Point

A changing key can intentionally be used to reset component state.

```jsx
<Form key={userId} />
```

When `userId` changes, the form can be recreated with fresh local state.

---

## 22. Why does React need keys?

### Interview Answer

> React needs keys to identify elements across renders. Without stable keys, React has less information to determine whether an item is the same item as before or whether it has been inserted, removed, or replaced.

### Previous

```text
A
B
C
```

### New

```text
X
A
B
C
```

With stable keys:

```text
X → new
A → existing
B → existing
C → existing
```

React can preserve the identity of the existing items.

---

## 23. How does React use keys during reconciliation?

### Interview Answer

> During reconciliation, React compares the new element tree with the previous one. For elements in the same list of siblings, keys help React determine which new element corresponds to which previous element.
>
> If the key and element type match appropriately, React can preserve the existing component instance and its state. If the key identifies a different item, React can treat it as a different identity and insert, remove, or recreate the corresponding element.

### Example

Previous:

```text
1 → A
2 → B
3 → C
```

New:

```text
3 → C
1 → A
2 → B
```

The positions changed, but the identities did not.

React can use the keys to understand that:

```text
C moved
A moved
B moved
```

rather than treating every position as a completely new item.

### Simplified Flow

```text
Previous UI
    ↓
Previous elements + keys
    ↓
New UI
    ↓
New elements + keys
    ↓
Compare identity
    ↓
Match existing elements
    ↓
Detect additions / removals / moves
    ↓
Preserve state where identity is preserved
    ↓
Commit required changes
```

---

# Quick Revision

## React

```text
React
  ↓
JavaScript UI Library
  ↓
Component-Based
  ↓
Declarative
  ↓
Props + State
  ↓
Render
  ↓
Reconciliation
  ↓
Commit
  ↓
DOM
```

## JSX

```text
JSX
  ↓
JSX Transform
  ↓
JavaScript
  ↓
React Elements
```

## Props

```text
Parent
  ↓
Props
  ↓
Child
```

## State Update

```text
State Update
     ↓
Schedule Update
     ↓
Render
     ↓
Reconciliation
     ↓
Commit
     ↓
UI Update
```

## Keys

```text
Key
 ↓
Identity
 ↓
Reconciliation
 ↓
Match Old/New Elements
 ↓
Preserve State
```

---

# Important Interview Traps

### ❌ Don't say:

> Virtual DOM directly updates the DOM.

### ✅ Say:

> React uses rendering and reconciliation to determine the necessary updates to the host environment.

### ❌ Don't say:

> JSX is HTML.

### ✅ Say:

> JSX is a JavaScript syntax extension that looks similar to HTML and is transformed into JavaScript.

### ❌ Don't say:

> Keys are only used for performance.

### ✅ Say:

> Keys primarily provide stable identity during reconciliation.

### ❌ Don't say:

> Index keys are always wrong.

### ✅ Say:

> Index keys can be problematic for dynamic lists, but they can be acceptable for static lists whose order never changes.

### ❌ Don't say:

> Changing a key only causes a re-render.

### ✅ Say:

> Changing a key changes component identity, so React can treat it as a different component and recreate it, resetting its local state.

---

# Core Concepts

| Concept | Main Idea |
|---|---|
| React | JavaScript library for building UIs |
| Component | Reusable UI unit |
| JSX | JavaScript syntax extension |
| React Element | Description of what should be rendered |
| Props | Parent-provided inputs |
| State | Component-managed changing data |
| Declarative | Describe what UI should look like |
| One-way data flow | Parent → Child |
| Composition | Build large UI from smaller components |
| Key | Stable identity for list elements |
| Reconciliation | Comparing previous and new UI descriptions |
| Commit | Applying necessary changes to the host environment |

---

# Strong 30-Second React Fundamentals Answer

> React is a JavaScript library for building user interfaces using reusable components. It follows a declarative programming model, where we describe what the UI should look like based on the current state and props.
>
> When state or props change, React renders a new UI representation and uses reconciliation to compare it with the previous one. It then commits the necessary changes to the host environment, such as the browser DOM.
>
> React also provides features such as JSX, Hooks, one-way data flow, component composition, and keys for maintaining identity during reconciliation.


# React Rendering, Reconciliation & Internals — Interview Questions & Answers

This chapter focuses on **how React actually works internally**. The answers are written in a way that you can explain them naturally in an interview instead of giving only definitions.

---

# 📌 Questions

1. [What happens when a React component renders?](#1-what-happens-when-a-react-component-renders)
2. [What is rendering in React?](#2-what-is-rendering-in-react)
3. [Does rendering mean React directly updates the DOM?](#3-does-rendering-mean-react-directly-updates-the-dom)
4. [What is the Virtual DOM?](#4-what-is-the-virtual-dom)
5. [Why does React use an internal representation of the UI?](#5-why-does-react-use-an-internal-representation-of-the-ui)
6. [What is reconciliation in React?](#6-what-is-reconciliation-in-react)
7. [How does React compare the old and new UI?](#7-how-does-react-compare-the-old-and-new-ui)
8. [What is React Fiber?](#8-what-is-react-fiber)
9. [Why was Fiber introduced?](#9-why-was-fiber-introduced)
10. [What is a Fiber node?](#10-what-is-a-fiber-node)
11. [What causes a component to re-render?](#11-what-causes-a-component-to-re-render)
12. [Does a parent re-render cause its children to re-render?](#12-does-a-parent-re-render-cause-its-children-to-re-render)
13. [Does a re-render mean the DOM is updated?](#13-does-a-re-render-mean-the-dom-is-updated)
14. [How does React update the DOM efficiently?](#14-how-does-react-update-the-dom-efficiently)
15. [What is the difference between render phase and commit phase?](#15-what-is-the-difference-between-render-phase-and-commit-phase)
16. [What is batching in React?](#16-what-is-batching-in-react)
17. [Why doesn't React immediately update state?](#17-why-doesnt-react-immediately-update-state)
18. [What is the difference between an event handler and a render?](#18-what-is-the-difference-between-an-event-handler-and-a-render)
19. [How does React preserve component state between renders?](#19-how-does-react-preserve-component-state-between-renders)
20. [How do keys affect component identity?](#20-how-do-keys-affect-component-identity)
21. [What happens when a key changes?](#21-what-happens-when-a-key-changes)
22. [What is Strict Mode and why does React render components twice in development?](#22-what-is-strict-mode-and-why-does-react-render-components-twice-in-development)
23. [What is Object.is and where is it relevant in React?](#23-what-is-objectis-and-where-is-it-relevant-in-react)
24. [What is the complete React rendering flow?](#24-what-is-the-complete-react-rendering-flow)
25. [Give an interview-ready explanation of React's rendering architecture.](#25-give-an-interview-ready-explanation-of-reacts-rendering-architecture)

---

# Questions & Answers

## 1. What happens when a React component renders?

### Interview Answer

When a React component renders, React first **calls the component function** to figure out what the UI should look like for the current props and state.

The component returns React elements, which describe the desired UI. React then uses its internal representation and reconciliation process to compare the new result with the previous one.

If something actually changed, React prepares the required DOM updates and applies them during the **commit phase**.

So I would describe the flow as:

**Component function → React elements → reconciliation → commit → DOM update**

An important point is that **rendering itself does not mean the DOM is immediately changed**. Rendering is mainly React calculating what the UI should be.

---

## 2. What is rendering in React?

### Interview Answer

Rendering in React means **React calls a component to determine what the UI should look like based on its current props and state**.

For example:

```jsx
function Counter({ count }) {
  return <h1>{count}</h1>;
}
```

If `count` changes from `1` to `2`, React renders the component again and gets:

```jsx
<h1>2</h1>
```

React then compares this result with the previous result and determines whether the actual DOM needs to change.

So rendering is basically the **calculation of the next UI**, not necessarily a DOM update.

---

## 3. Does rendering mean React directly updates the DOM?

### Interview Answer

No.

This is a very important distinction.

When React renders a component, it calculates what the next UI should look like. React then compares that result with the previous UI representation during reconciliation.

Only after React determines what actually needs to change does it apply those changes to the real DOM during the commit phase.

So:

```text
Render
  ↓
Calculate next UI
  ↓
Reconciliation
  ↓
Determine changes
  ↓
Commit
  ↓
Update DOM
```

This is why saying **"every re-render directly updates the DOM"** is incorrect.

---

## 4. What is the Virtual DOM?

### Interview Answer

The Virtual DOM is a conceptual way of describing React's representation of the UI before React applies changes to the actual DOM.

Instead of manually telling the browser:

```javascript
element.textContent = "Hello";
element.style.color = "red";
```

we describe the UI using React components and elements.

For example:

```jsx
<h1>Hello</h1>
```

React can compare the previous and next UI descriptions and determine what needs to change in the actual DOM.

The important thing is that **Virtual DOM is not a copy of the entire browser DOM sitting somewhere that React blindly updates**. Modern React's architecture is more accurately described in terms of **React elements, Fiber nodes, reconciliation, and the commit phase**.

---

## 5. Why does React use an internal representation of the UI?

### Interview Answer

The main reason is that React wants to separate **describing the UI** from **actually applying changes to the UI**.

When application state changes, React can calculate the next UI, compare it with the previous one, and determine the smallest set of necessary changes.

This gives React a structured way to manage complex UI updates.

It also allows React's rendering system to support features such as scheduling, prioritization, interruption of rendering work, and different rendering environments.

---

## 6. What is reconciliation in React?

### Interview Answer

Reconciliation is the process React uses to **compare the previous rendered UI with the new rendered UI and determine what needs to change**.

For example, suppose the previous result is:

```jsx
<h1>Hello</h1>
```

and the new result is:

```jsx
<h1>Hello World</h1>
```

React doesn't need to recreate the entire page. It identifies that the text inside the `h1` changed and updates the necessary part.

Keys are also important during reconciliation because they help React identify which list items represent the same logical component between renders.

---

## 7. How does React compare the old and new UI?

### Interview Answer

React uses the structure and identity of elements to determine what can be reused and what needs to change.

For example, if the element type stays the same:

```jsx
<div>Hello</div>
```

becomes:

```jsx
<div>Hi</div>
```

React can keep the existing DOM node and update its content.

But if the type changes:

```jsx
<div>Hello</div>
```

becomes:

```jsx
<section>Hello</section>
```

React treats that as a different element type and may replace the corresponding subtree.

For lists, React uses `key` values to preserve identity between renders.

So the major things React considers include:

- Element type
- Position in the tree
- Key
- Props and children
- Component identity

---

## 8. What is React Fiber?

### Interview Answer

React Fiber is the internal architecture React uses to represent and manage rendering work.

A Fiber is essentially a data structure that represents a unit of work associated with a component or element in the React tree.

Fiber allows React to break rendering work into smaller units and gives React more control over how that work is scheduled.

This architecture is especially important for features such as:

- Prioritizing updates
- Interrupting rendering work
- Resuming work
- Concurrent rendering
- Tracking component state and relationships

So when I say **Fiber**, I am referring both to the internal data structure and the architecture built around it.

---

## 9. Why was Fiber introduced?

### Interview Answer

Before Fiber, React's rendering work was more difficult to interrupt and prioritize.

If React had a large amount of rendering work, that work could potentially occupy the JavaScript thread for too long.

Fiber introduced a more flexible architecture where React can break rendering work into units and schedule it.

This means React can decide which work is more important and, when appropriate, pause lower-priority rendering work and continue later.

The main goal was to make React's rendering system more flexible and responsive, especially for complex applications.

---

## 10. What is a Fiber node?

### Interview Answer

A Fiber node is an internal JavaScript object that represents a unit in React's component tree.

A Fiber can contain information such as:

- Component type
- Key
- Props
- State-related information
- Parent relationship
- Child relationship
- Sibling relationship
- Pending work
- Effects or flags related to updates

For example, conceptually the tree can look like:

```text
App Fiber
   |
   +-- Header Fiber
   |
   +-- Main Fiber
   |     |
   |     +-- Product Fiber
   |     +-- Product Fiber
   |
   +-- Footer Fiber
```

Fiber gives React a structure that it can use to track and process rendering work.

---

## 11. What causes a component to re-render?

### Interview Answer

A component can re-render when React receives an update that affects that component.

Common causes include:

1. Its state changes.
2. Its parent renders and React renders the child as part of that process.
3. Its context value changes.
4. An external store or subscription used by the component triggers an update.
5. A component receives new props.

For example:

```jsx
const [count, setCount] = useState(0);

setCount(1);
```

The state update schedules React to render the component again.

However, a re-render does **not automatically mean the DOM changes**. React still has to determine whether the resulting UI is different.

---

## 12. Does a parent re-render cause its children to re-render?

### Interview Answer

By default, when a parent component renders, React will generally render its child components as part of that rendering process.

For example:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child />
    </>
  );
}
```

When `Parent` renders again, `Child` can also be rendered again.

However, React provides optimization techniques such as `memo` that can allow a child to skip rendering when its relevant inputs haven't changed.

So I would not say **"parent re-render always causes DOM updates in children."**

It can cause child rendering, but the DOM is updated only if React determines that an actual change is necessary.

---

## 13. Does a re-render mean the DOM is updated?

### Interview Answer

No.

A re-render means React executes the rendering logic again and calculates the next UI.

React then compares the new result with the previous result.

If the output is effectively the same, React may not need to make any DOM changes.

For example:

```jsx
function App() {
  return <h1>Hello</h1>;
}
```

Even if this component renders again, React doesn't need to recreate the `h1` DOM element just because the component function executed again.

This distinction is important:

**Re-render ≠ DOM update.**

---

## 14. How does React update the DOM efficiently?

### Interview Answer

React follows a process where it first calculates the next UI, then reconciles it with the previous UI, and finally commits the required changes.

For example:

```jsx
<h1>Hello</h1>
```

changes to:

```jsx
<h1>Hello World</h1>
```

React can reuse the existing `h1` DOM node and update only the changed text.

It doesn't need to remove and recreate the entire page.

This is one reason React applications can manage complex dynamic interfaces without requiring developers to manually synchronize every DOM change.

---

## 15. What is the difference between render phase and commit phase?

### Interview Answer

React's update process can be understood in two major phases: the **render phase** and the **commit phase**.

### Render phase

During the render phase, React determines what the next UI should look like.

It can:

- Call components
- Process hooks
- Build or update Fiber work
- Reconcile the new result with the previous one
- Determine what changes are required

The render phase should be treated as having no direct DOM side effects.

### Commit phase

In the commit phase, React applies the required changes to the host environment, such as the browser DOM.

This is where React performs the actual DOM mutations and runs the appropriate commit-related effects.

So the simple explanation is:

```text
Render phase
    ↓
"What should the UI look like?"

Commit phase
    ↓
"Apply those changes."
```

---

## 16. What is batching in React?

### Interview Answer

Batching means React can group multiple state updates together and process them as a single rendering update rather than rendering after every individual state update.

For example:

```jsx
setCount(1);
setName("Nikitha");
```

React can batch these updates so that the component doesn't need to go through separate rendering work for each update.

Modern React also performs automatic batching for many updates beyond traditional React event handlers.

The main benefit is reducing unnecessary rendering work and improving performance.

---

## 17. Why doesn't React immediately update state?

### Interview Answer

When I call a state setter such as:

```jsx
setCount(5);
```

I shouldn't think of it as directly changing the current `count` variable.

Instead, I'm asking React to schedule an update.

The current render continues using the state values that belong to that render.

React then processes the update, renders the component with the new state, reconciles the result, and commits any required changes.

That's why code like this can be surprising:

```jsx
console.log(count);
setCount(count + 1);
console.log(count);
```

Both logs can show the old value during that event handler.

The new state becomes available in the next render.

---

## 18. What is the difference between an event handler and a render?

### Interview Answer

An event handler is code that runs in response to an event, such as a click.

For example:

```jsx
<button onClick={handleClick}>Click</button>
```

The `handleClick` function runs because the user clicked the button.

A render is different. A render happens when React executes the component to determine the UI for the current state and props.

The flow can be:

```text
User clicks button
       ↓
Event handler runs
       ↓
setState()
       ↓
React schedules update
       ↓
Component renders
       ↓
Reconciliation
       ↓
Commit
```

So the event handler triggers the update, while rendering is React's process of calculating the next UI.

---

## 19. How does React preserve component state between renders?

### Interview Answer

React preserves state based on the **identity and position of a component in the rendered tree**.

For example:

```jsx
function App() {
  return <Counter />;
}
```

If React continues to see the same `Counter` component in the same logical position, it can associate the existing state with that component.

This is why state normally survives ordinary re-renders.

However, if the component's identity changes, React can treat it as a new component and its state can be reset.

Keys are especially important when dealing with lists because they help React identify which component instance corresponds to which item.

---

## 20. How do keys affect component identity?

### Interview Answer

Keys tell React which list item represents the same logical item between renders.

For example:

```jsx
users.map(user => (
  <User key={user.id} user={user} />
))
```

Suppose the list changes from:

```text
A
B
C
```

to:

```text
C
A
B
```

If the keys are stable IDs, React can understand that these are the same three logical components, just in a different order.

This helps React preserve the correct component identity and state.

So I think of a key as an **identity marker for elements among their siblings**.

---

## 21. What happens when a key changes?

### Interview Answer

When a component's key changes, React can treat it as a different component identity.

For example:

```jsx
<Profile key={userId} />
```

If:

```text
userId = 1
```

changes to:

```text
userId = 2
```

React can treat the new `Profile` as a new instance rather than continuing the previous instance.

That means its local state can be reset.

This behavior can actually be used intentionally.

For example:

```jsx
<Form key={userId} />
```

Changing the key can force the form to start with fresh component state.

So keys are not just about removing warnings; they can control component identity.

---

## 22. What is Strict Mode and why does React render components twice in development?

### Interview Answer

React Strict Mode is a development-time feature that helps identify unsafe patterns and potential bugs.

In certain development configurations, React may intentionally invoke rendering-related logic more than once to expose code that incorrectly depends on side effects happening only once.

For example:

```jsx
function App() {
  console.log("render");
  return <h1>Hello</h1>;
}
```

You may see the log more than once in development when Strict Mode is enabled.

This does not mean React is randomly rendering the application twice in production.

The purpose is to help developers find problems such as:

- Impure rendering
- Missing cleanup
- Incorrect side effects
- Code that is not resilient to React's rendering behavior

So if I see a double render during development, I first check whether Strict Mode is enabled.

---

## 23. What is Object.is and where is it relevant in React?

### Interview Answer

`Object.is` is a JavaScript comparison algorithm that React uses in several places where it needs to determine whether values are the same according to React's equality semantics.

For example, React can use `Object.is` when deciding whether a state update represents a meaningful value change.

It is also relevant when React compares dependency values for hooks such as:

```jsx
useEffect(() => {
  // effect
}, [value]);
```

React compares dependency values between renders using `Object.is` semantics.

This is important for understanding why object and array dependencies can cause effects to run again when their references change.

For example:

```jsx
const user = { name: "Nikitha" };
```

Creating a new object on every render gives it a new reference, even if the contents look identical.

---

## 24. What is the complete React rendering flow?

### Interview Answer

I would explain the complete flow like this:

Suppose I have:

```jsx
const [count, setCount] = useState(0);

<button onClick={() => setCount(count + 1)}>
  {count}
</button>
```

Initially, React renders the component with:

```text
count = 0
```

Then the user clicks the button.

The event handler calls:

```jsx
setCount(count + 1);
```

React schedules an update.

React then renders the component again with:

```text
count = 1
```

During the render process, React calculates the new UI and reconciles it with the previous UI.

React identifies that the button's displayed text changed from:

```text
0
```

to:

```text
1
```

React then enters the commit phase and applies the required DOM update.

The overall flow is:

```text
Initial render
     ↓
Component executes
     ↓
React creates/updates Fiber work
     ↓
UI is committed

User interaction
     ↓
setState()
     ↓
Update scheduled
     ↓
Render phase
     ↓
Reconciliation
     ↓
Commit phase
     ↓
DOM updated
```

---

## 25. Give an interview-ready explanation of React's rendering architecture.

### Strong Interview Answer

"React's rendering architecture is based on separating the calculation of the UI from actually applying changes to the DOM.

When a component initially renders, React executes the component and gets the React elements describing the UI. Internally, React represents this work using Fiber nodes.

When state or props change, React schedules an update and performs another render. During the render phase, React calculates the new UI and reconciles it with the previous one.

React uses things such as element type and keys to determine which parts of the existing component tree can be reused and which parts need to change.

Once React knows what needs to be updated, it enters the commit phase, where it applies the required changes to the DOM.

So the simplified flow is:

Component update → render → reconciliation → commit → DOM update.

The important distinction is that a re-render doesn't necessarily mean a DOM update. React can render and determine that there is nothing that needs to change in the actual DOM."

---

# 🔥 Quick Revision

```text
Rendering
→ React calculates the next UI.

Reconciliation
→ React compares previous and next UI.

Fiber
→ React's internal architecture for representing and scheduling rendering work.

Fiber node
→ A data structure representing a unit in the React tree.

Render phase
→ Calculate what should change.

Commit phase
→ Apply the required changes.

Re-render
→ Component rendering again.

DOM update
→ Actual changes applied to the DOM.

Batching
→ Group multiple state updates into fewer rendering operations.

Key
→ Helps React preserve identity among siblings.

Changing key
→ Can create a new component identity and reset state.

Strict Mode
→ Development feature for detecting unsafe patterns.

Object.is
→ Equality semantics used in several React comparisons.
```

---

# ⚠️ Important Interview Traps

### ❌ Don't say:

> "Every time React re-renders, it updates the DOM."

### ✅ Say:

> "A re-render means React calculates the next UI. After reconciliation, React only commits the DOM changes that are necessary."

---

### ❌ Don't say:

> "Virtual DOM is a complete copy of the real DOM."

### ✅ Say:

> "Virtual DOM is a conceptual representation of UI used to reason about changes. Modern React's implementation involves React elements, Fiber, reconciliation, and the commit phase."

---

### ❌ Don't say:

> "React is fast because Virtual DOM is always faster than the DOM."

### ✅ Say:

> "React provides a declarative rendering model and efficiently determines and commits the necessary UI changes."

---

### ❌ Don't say:

> "setState immediately changes the state variable."

### ✅ Say:

> "A state setter schedules an update. The new state is available when React renders the component again."

---

### ❌ Don't say:

> "Keys are only needed to remove the warning."

### ✅ Say:

> "Keys provide stable identity among siblings and help React preserve the correct component state during reconciliation."

---

# 🎯 30-Second Interview Summary

> "When a React component renders, React executes the component to calculate the next UI. React internally tracks this work using Fiber. When state or props change, React schedules an update and performs the render phase, where it calculates and reconciles the new UI with the previous one. React then enters the commit phase and applies only the necessary changes to the DOM. Keys help React preserve component identity, especially in lists. So an important distinction is that a re-render doesn't necessarily mean a DOM update; rendering is the calculation, while committing is when the actual UI changes are applied."
