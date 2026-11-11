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
