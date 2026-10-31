# Chapter 2: React Hooks
## Complete React Hooks Interview Questions and Answers – Beginner to Advanced

**Level:** Beginner | Intermediate | Advanced | Expert

**React Version:** React 19+ (including newer React Hook APIs)

**Topics Covered:**
- Introduction to React Hooks
- Rules of Hooks
- useState
- useEffect
- Dependency Arrays
- Cleanup Functions
- useContext
- useRef
- useMemo
- useCallback
- useReducer
- useLayoutEffect
- useImperativeHandle
- useId
- useTransition
- useDeferredValue
- useSyncExternalStore
- useInsertionEffect
- useDebugValue
- Custom Hooks
- React 19 Hooks
- useActionState
- useOptimistic
- useFormStatus
- useEffectEvent
- The React `use()` API
- Advanced scenarios
- Output-based questions
- Common mistakes and interview traps

---

# Table of Contents

## Section 1: Introduction to Hooks
1. What are React Hooks?
2. Why were Hooks introduced?
3. What problems did Hooks solve?
4. What are the advantages of Hooks?
5. What are the different types of Hooks?
6. What are the built-in Hooks in React?
7. What are the Rules of Hooks?
8. Why can't Hooks be called conditionally?
9. Can Hooks be used inside class components?
10. Can we create our own Hooks?
11. What is the difference between Hooks and lifecycle methods?
12. What is the difference between a Hook and a normal JavaScript function?
13. Why should Hooks start with the word `use`?
14. What happens internally when React executes Hooks?
15. What is the difference between React 18 and React 19 Hooks?

## Section 2: useState
16. What is useState?
17. What is the syntax of useState?
18. What does useState return?
19. How does useState work internally?
20. How do you update state?
21. What is the difference between direct state updates and functional updates?
22. Why should we use functional state updates?
23. Why does useState not update the value immediately?
24. What is state batching?
25. What is automatic batching?
26. What is lazy initialization in useState?
27. Can useState store objects and arrays?
28. How do you update an object in state?
29. How do you update an array in state?
30. Why should state be treated as immutable?
31. Can we use multiple useState Hooks in one component?
32. What happens when we call a state setter with the same value?
33. Can we store functions in useState?
34. What is the difference between useState and normal variables?
35. What is the difference between useState and useReducer?
36. What is a stale state closure?
37. How do you avoid stale state?
38. What happens when multiple state updates happen together?
39. Does updating state always cause a re-render?
40. Can state updates be asynchronous?

## Section 3: useEffect
41. What is useEffect?
42. Why do we use useEffect?
43. What is the syntax of useEffect?
44. What are the parameters of useEffect?
45. What does useEffect return?
46. What are the different syntaxes of useEffect?
47. What happens when useEffect has no dependency array?
48. What happens when useEffect has an empty dependency array?
49. What happens when useEffect has dependencies?
50. What is a dependency array?
51. How does React compare dependencies?
52. What is the cleanup function in useEffect?
53. When does the cleanup function execute?
54. What is the execution order of useEffect?
55. What is the difference between useEffect and componentDidMount?
56. What is the difference between useEffect and componentDidUpdate?
57. What is the difference between useEffect and componentWillUnmount?
58. What happens when useEffect runs in Strict Mode?
59. What is an infinite loop in useEffect?
60. What causes unnecessary useEffect executions?
61. How do you fetch API data using useEffect?
62. How do you handle API errors in useEffect?
63. How do you prevent race conditions in useEffect?
64. How do you cancel an API request in useEffect?
65. What is a stale closure in useEffect?
66. Why should we not make the useEffect callback async?
67. How do you implement a timer using useEffect?
68. How do you implement event listeners using useEffect?
69. How do you handle subscriptions using useEffect?
70. When should you NOT use useEffect?
71. What is the difference between useEffect and event handlers?
72. What is the difference between useEffect and useLayoutEffect?
73. What happens if dependencies are omitted accidentally?
74. What is the exhaustive-deps rule?

## Section 4: Other Important Hooks
75. What is useContext?
76. What problem does useContext solve?
77. How does useContext work?
78. What is Context Provider?
79. What happens when Context value changes?
80. Does useContext cause re-renders?
81. How do you avoid unnecessary Context re-renders?
82. What is the difference between useContext and Redux?
83. What is useRef?
84. What does useRef return?
85. What is the difference between useRef and useState?
86. Does updating useRef cause re-rendering?
87. How do you access DOM elements using useRef?
88. What is forwardRef?
89. Is forwardRef required in React 19?
90. What is useMemo?
91. Why do we use useMemo?
92. What is memoization?
93. What is the syntax of useMemo?
94. What is the difference between useMemo and useState?
95. What is the difference between useMemo and React.memo?
96. What is useCallback?
97. Why do we use useCallback?
98. What is the difference between useMemo and useCallback?
99. What is the difference between useCallback and React.memo?
100. Does useCallback prevent re-rendering?
101. What is useReducer?
102. Why do we use useReducer?
103. What are the parameters of useReducer?
104. What does useReducer return?
105. What is a reducer function?
106. What is an action in useReducer?
107. What is the difference between useReducer and useState?
108. What is lazy initialization in useReducer?
109. Can useReducer be combined with Context API?
110. What is useLayoutEffect?
111. When should we use useLayoutEffect?
112. What is the difference between useEffect and useLayoutEffect?
113. What is useImperativeHandle?
114. Why do we use useImperativeHandle?
115. What is useId?
116. Why should useId not be used as a list key?

## Section 5: Advanced React Hooks
117. What is useTransition?
118. What is a transition in React?
119. What does useTransition return?
120. What is isPending?
121. What is the difference between useTransition and startTransition?
122. What is the difference between useTransition and useDeferredValue?
123. What is useDeferredValue?
124. How does useDeferredValue work?
125. What is useSyncExternalStore?
126. Why do we need useSyncExternalStore?
127. What is an external store?
128. What is useInsertionEffect?
129. When should useInsertionEffect be used?
130. What is useDebugValue?
131. What is the purpose of useDebugValue?
132. What is the difference between useRef and useImperativeHandle?
133. What is the difference between useEffect and useInsertionEffect?
134. What is the difference between useLayoutEffect and useInsertionEffect?

## Section 6: Custom Hooks
135. What are Custom Hooks?
136. Why do we create Custom Hooks?
137. What are the rules of Custom Hooks?
138. What is the difference between Custom Hooks and components?
139. Do Custom Hooks share state?
140. How do you create a Custom Hook?
141. What is useFetch?
142. How do you create a useLocalStorage Hook?
143. How do you create a useDebounce Hook?
144. How do you create a useWindowSize Hook?
145. How do you create a useOnlineStatus Hook?
146. How do you create a useToggle Hook?
147. How do you create a usePrevious Hook?
148. How do you test Custom Hooks?

## Section 7: React 19 and Newer Hook APIs
149. What are the important new Hooks introduced in React 19?
150. What is useActionState?
151. Why do we use useActionState?
152. What are the parameters of useActionState?
153. What does useActionState return?
154. What is the difference between useState and useActionState?
155. What is a React Action?
156. What is the difference between synchronous and asynchronous Actions?
157. What is useOptimistic?
158. Why do we use useOptimistic?
159. What does useOptimistic return?
160. What is optimistic UI?
161. What happens when an optimistic update fails?
162. What is useFormStatus?
163. What does useFormStatus return?
164. What is the difference between useFormStatus and useActionState?
165. What is the difference between useOptimistic and useState?
166. What is useEffectEvent?
167. Why was useEffectEvent introduced?
168. What is the difference between useEffectEvent and useCallback?
169. What is the difference between useEffectEvent and event handlers?
170. What is the React `use()` API?
171. Is `use()` a Hook?
172. How is `use()` different from useEffect?
173. Can `use()` be called conditionally?
174. What is the difference between `use()` and useContext?
175. What is the difference between React Actions and traditional event handlers?

## Section 8: Practical and Scenario-Based Questions
176. How do you fetch data when a component mounts?
177. How do you execute an Effect only when a particular value changes?
178. How do you run cleanup when a component unmounts?
179. How do you avoid an infinite useEffect loop?
180. How do you prevent duplicate API requests?
181. How do you prevent stale API responses?
182. How do you implement search with debounce?
183. How do you implement pagination using Hooks?
184. How do you manage authentication using Context and Hooks?
185. How do you implement a modal using Hooks?
186. How do you manage multiple form fields?
187. How do you optimize a large search list?
188. How do you handle expensive calculations?
189. How do you avoid unnecessary child re-renders?
190. How do you share logic between components?
191. How do you manage global state using useReducer and Context?
192. How do you implement optimistic updates?
193. How do you handle loading, success and error states?
194. How do you synchronize a component with an external system?
195. How do you handle timers and intervals safely?

## Section 9: Tricky Output-Based Questions
196. What happens when state is updated multiple times in one event?
197. What is the output of multiple functional state updates?
198. Why does useEffect run after rendering?
199. What happens when an object is passed as a dependency?
200. Why does an empty object cause repeated Effect execution?
201. What happens when a state variable is used inside a stale closure?
202. What happens when useMemo has no dependencies?
203. What happens when useCallback has an empty dependency array?
204. Does changing a ref update the UI?
205. What happens when a component is wrapped in StrictMode?
206. Why can an Effect execute twice in development?
207. What happens when the dependency array contains a function?
208. What happens when a state setter is called inside useEffect?
209. What happens when a component is conditionally rendered?
210. What happens when a Custom Hook is called in a loop?

---

# Section 1: Introduction to React Hooks

### Q1. What are React Hooks?

**Interview Answer:**

React Hooks are special functions introduced in React 16.8 that allow functional components to use React features such as state management, side effects, context, refs and other React capabilities.

Before Hooks, state and lifecycle features were primarily associated with class components.

Hooks allow developers to use these capabilities inside functional components without writing class components.

**Examples:**

- `useState()` – Manages component state.
- `useEffect()` – Synchronizes components with external systems.
- `useContext()` – Reads Context values.
- `useRef()` – Stores mutable values and references DOM elements.
- `useMemo()` – Caches calculation results.
- `useCallback()` – Caches function definitions.
- `useReducer()` – Manages complex state logic.

**Example:**

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>{count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

Here, `useState` allows the functional component to manage state.

---

### Q2. Why were Hooks introduced?

**Interview Answer:**

Hooks were introduced to solve several problems associated with managing state and sharing logic in class components.

Before Hooks, developers commonly used class components for state and lifecycle methods.

As applications became larger, managing component logic across lifecycle methods became difficult.

Hooks introduced a simpler way to manage state and reuse logic in functional components.

**Main Reasons:**

1. To use state in functional components.
2. To reuse stateful logic between components.
3. To reduce complex class component structures.
4. To avoid confusion around the `this` keyword.
5. To organize related logic together.
6. To simplify lifecycle-related logic.
7. To improve code readability and maintainability.

---

### Q3. What problems did Hooks solve?

**Interview Answer:**

Hooks addressed three major problems in React development.

**1. Complex Components**

In class components, related logic could be distributed across lifecycle methods such as `componentDidMount`, `componentDidUpdate` and `componentWillUnmount`.

Hooks allow related synchronization logic to be organized in one Effect.

**2. Reusing Stateful Logic**

Before Hooks, developers often used Higher-Order Components and Render Props to reuse stateful logic.

Custom Hooks provide a simpler way to reuse logic.

**3. Confusing Class Components**

Class components required understanding `this`, constructors, binding and lifecycle methods.

Functional components with Hooks provide a simpler programming model.

---

### Q4. What are the advantages of Hooks?

**Interview Answer:**

1. Allow state management inside functional components.
2. Make stateful logic reusable through Custom Hooks.
3. Reduce class component boilerplate.
4. Help organize related logic.
5. Improve code readability.
6. Simplify many lifecycle-related patterns.
7. Support modern React features.
8. Make it easier to separate UI from reusable application logic.

---

### Q5. What are the different types of Hooks?

**Interview Answer:**

React Hooks can be grouped according to their purpose.

| Category | Hooks | Purpose |
|---|---|---|
| State Hooks | `useState`, `useReducer` | Manage component state |
| Context Hook | `useContext` | Read shared Context values |
| Ref Hooks | `useRef`, `useImperativeHandle` | Manage mutable references |
| Effect Hooks | `useEffect`, `useLayoutEffect`, `useInsertionEffect` | Synchronize with external systems |
| Performance Hooks | `useMemo`, `useCallback` | Cache calculations and functions |
| Transition Hooks | `useTransition`, `useDeferredValue` | Manage non-urgent UI updates |
| External Store Hook | `useSyncExternalStore` | Subscribe to external stores |
| Debug Hook | `useDebugValue` | Display custom Hook information |
| ID Hook | `useId` | Generate accessibility IDs |
| Action Hooks | `useActionState`, `useOptimistic`, `useFormStatus` | Manage Actions and form interactions |
| Effect Event Hook | `useEffectEvent` | Separate non-reactive Effect logic |

**Note:** `use()` is a React API, not a Hook under the Rules of Hooks, although it is commonly discussed alongside modern React Hooks.

---

### Q6. What are the built-in Hooks in React?

**Interview Answer:**

React provides built-in Hooks for managing state, context, references, effects, performance and other features.

**State Hooks:**
- `useState`
- `useReducer`

**Context Hook:**
- `useContext`

**Ref Hooks:**
- `useRef`
- `useImperativeHandle`

**Effect Hooks:**
- `useEffect`
- `useLayoutEffect`
- `useInsertionEffect`

**Performance Hooks:**
- `useMemo`
- `useCallback`

**Other Hooks:**
- `useId`
- `useTransition`
- `useDeferredValue`
- `useSyncExternalStore`
- `useDebugValue`

**React 19 and newer Hooks:**
- `useActionState`
- `useOptimistic`
- `useFormStatus`
- `useEffectEvent`

Custom Hooks can also be created by developers.

---

### Q7. What are the Rules of Hooks?

**Interview Answer:**

React Hooks must follow specific rules so React can correctly preserve Hook state and associate Hook calls with the correct component.

**Rule 1: Only Call Hooks at the Top Level**

Hooks should not be called inside:
- Conditions.
- Loops.
- Nested functions.
- Event handlers.
- Try/catch/finally blocks.

**Incorrect:**

```jsx
function App() {
  if (true) {
    const [count, setCount] = useState(0);
  }

  return <h1>Hello</h1>;
}
```

**Correct:**

```jsx
function App() {
  const [count, setCount] = useState(0);

  return <h1>{count}</h1>;
}
```

**Rule 2: Only Call Hooks from React Functions**

Hooks should be called from:
- React function components.
- Custom Hooks.

**Rule 3: Maintain the Same Hook Order**

Hooks must be called in the same order on every render.

**Important:** React's `use()` API is a special exception to the top-level restriction. It can be called conditionally and inside loops, but it still cannot be called inside try/catch blocks.

---

### Q8. Why can't Hooks be called conditionally?

**Interview Answer:**

React relies on the order of Hook calls to associate each Hook with its corresponding state or other internal data.

If Hooks are called conditionally, their order or number may change between renders.

This can cause React to associate the wrong Hook data with a component.

**Incorrect Example:**

```jsx
function App({ isLoggedIn }) {
  if (isLoggedIn) {
    const [user, setUser] = useState(null);
  }

  const [count, setCount] = useState(0);

  return <h1>{count}</h1>;
}
```

If `isLoggedIn` changes, the Hook call order changes.

**Correct Example:**

```jsx
function App({ isLoggedIn }) {
  const [user, setUser] = useState(null);

  const [count, setCount] = useState(0);

  if (!isLoggedIn) {
    return <h1>Please Login</h1>;
  }

  return <h1>{count}</h1>;
}
```

All Hooks are called before the conditional return.

---

### Q9. Can Hooks be used inside class components?

**Interview Answer:**

No. Hooks cannot be called directly inside class components.

Hooks are designed for functional components and Custom Hooks.

Class components use:
- `this.state`
- `this.setState()`
- Lifecycle methods.

Functional components use:
- `useState`
- `useEffect`
- `useContext`
- Other Hooks.

---

### Q10. Can we create our own Hooks?

**Interview Answer:**

Yes. Developers can create Custom Hooks to reuse stateful logic across multiple components.

A Custom Hook is a JavaScript function whose name starts with `use` and which can call other Hooks.

**Example:**

```jsx
import { useState } from "react";

function useToggle() {
  const [isOpen, setIsOpen] = useState(false);

  const toggle = () => {
    setIsOpen(previous => !previous);
  };

  return { isOpen, toggle };
}

export default useToggle;
```

Using the Custom Hook:

```jsx
function App() {
  const { isOpen, toggle } = useToggle();

  return (
    <div>
      <button onClick={toggle}>
        Toggle
      </button>

      {isOpen && <h1>Hello React</h1>}
    </div>
  );
}
```

---

### Q11. What is the difference between Hooks and lifecycle methods?

**Interview Answer:**

Lifecycle methods are used in class components to execute logic at different stages of a component's lifecycle.

Hooks provide a way to use state, synchronize with external systems and access other React features in functional components.

| Lifecycle Methods | Hooks |
|---|---|
| Used in class components | Used in function components and Custom Hooks |
| `componentDidMount` | Often modeled using `useEffect` |
| `componentDidUpdate` | Often modeled using `useEffect` |
| `componentWillUnmount` | Effect cleanup |
| `this.state` | `useState` |
| `this.setState()` | State setter |

**Important:** `useEffect` is not an exact replacement for all lifecycle methods. Effects are primarily designed to synchronize with external systems.

---

### Q12. What is the difference between a Hook and a normal JavaScript function?

**Interview Answer:**

A normal JavaScript function performs general programming tasks, whereas a React Hook allows a component or Custom Hook to use React features.

| Normal Function | Hook |
|---|---|
| Can be called from different JavaScript contexts | Follows the Rules of Hooks |
| Does not automatically access React state | Can use React state and other React features |
| Can be called conditionally | Generally must follow Hook call-order rules |
| Can use ordinary JavaScript logic | Can participate in React rendering and lifecycle behavior |
| Name can be anything | Custom Hooks must start with `use` |

---

### Q13. Why should Custom Hooks start with the word use?

**Interview Answer:**

Custom Hooks should start with `use` so React development tools, lint rules and developers can identify them as Hooks.

This naming convention also helps the Rules of Hooks linting system detect improper Hook usage.

Examples:

```jsx
useFetch()
useToggle()
useDebounce()
useAuth()
useLocalStorage()
```

---

### Q14. What happens internally when React executes Hooks?

**Interview Answer:**

React maintains internal data associated with each component's rendering process.

When a component renders, React tracks Hook calls in their order and associates each call with the corresponding internal Hook data.

During subsequent renders, React uses the same call order to retrieve and update the correct Hook data.

**Simplified Flow:**

```text
Component Render
      |
      v
React Executes Hooks
      |
      v
Hook Data Associated by Call Order
      |
      v
Component Returns UI
      |
      v
React Reconciles UI
      |
      v
Commit Necessary Updates
```

**Important:** This is a conceptual explanation. React's internal implementation is more sophisticated and includes Fiber and scheduling mechanisms.

---

### Q15. What is the difference between React 18 and React 19 Hooks?

**Interview Answer:**

React 19 introduced additional APIs and improvements to the way developers handle forms, Actions and asynchronous UI updates.

| React 18 | React 19 |
|---|---|
| `useState` | `useState` |
| `useEffect` | `useEffect` |
| `useContext` | `useContext` |
| `useReducer` | `useReducer` |
| `useTransition` | `useTransition` |
| `useDeferredValue` | `useDeferredValue` |
| Existing Hooks | `useActionState` |
| Existing Hooks | `useOptimistic` |
| Existing Hooks | `useFormStatus` in React DOM |
| Existing Hooks | `use()` API |

React 19 also improved support for asynchronous Actions and form handling.

Newer React versions additionally provide `useEffectEvent`, which is useful for separating non-reactive logic from Effects.

---

# Section 2: useState – State Management

### Q16. What is useState?

**Interview Answer:**

`useState` is a built-in React Hook used to add and manage state in functional components.

It allows a component to store data that can change over time.

When its setter function is called, React schedules a re-render so the component can display the updated value.

**Example:**

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>{count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

---

### Q17. What is the syntax of useState?

```jsx
const [state, setState] = useState(initialState);
```

**Explanation:**

| Part | Meaning |
|---|---|
| `useState` | React Hook |
| `initialState` | Initial value |
| `state` | Current state value |
| `setState` | Function used to update state |

Example:

```jsx
const [name, setName] = useState("Nikitha");
```

---

### Q18. What does useState return?

**Interview Answer:**

`useState` returns an array containing two elements:

1. The current state value.
2. A setter function used to update the state.

Example:

```jsx
const [count, setCount] = useState(0);
```

Here:
- `count` is the current value.
- `setCount` updates the value.

---

### Q19. How does useState work internally?

**Interview Answer:**

React associates state with a component's identity and its Hook call position.

When a component initially renders, React initializes its state.

When the setter is called, React queues an update and schedules the component to render again.

During the next render, React provides the updated state value.

**Flow:**

```text
Initial Render
     |
     v
useState(0)
     |
     v
count = 0
     |
     v
User Clicks Button
     |
     v
setCount(1)
     |
     v
React Schedules Update
     |
     v
Component Re-renders
     |
     v
count = 1
```

---

### Q20. How do you update state?

**Interview Answer:**

State is updated using the setter function returned by `useState`.

Example:

```jsx
const [count, setCount] = useState(0);

setCount(10);
```

This schedules an update that sets the state to 10.

We can also use a functional update:

```jsx
setCount(previousCount => previousCount + 1);
```

---

### Q21. What is the difference between direct state updates and functional updates?

**Interview Answer:**

Direct updates pass a new value to the setter, whereas functional updates pass a function that receives the previous state and calculates the next state.

**Direct Update:**

```jsx
setCount(count + 1);
```

**Functional Update:**

```jsx
setCount(previousCount => previousCount + 1);
```

**Difference:**

| Direct Update | Functional Update |
|---|---|
| Uses a value calculated from the current render | Uses the previous queued state |
| Can cause problems with repeated updates using stale values | Correctly calculates each queued update |
| Suitable for simple updates when current value is sufficient | Useful when next state depends on previous state |

---

### Q22. Why should we use functional state updates?

**Interview Answer:**

Functional state updates are useful when the next state depends on the previous state.

React processes queued updater functions in sequence, so each updater receives the result of the previous update.

**Example:**

```jsx
setCount(previous => previous + 1);
setCount(previous => previous + 1);
setCount(previous => previous + 1);
```

If the initial count is 0, the final count becomes 3.

If we instead write:

```jsx
setCount(count + 1);
setCount(count + 1);
setCount(count + 1);
```

All three updates use the same `count` value from the current render, so the final count becomes 1.

**Interview Tip:** Use functional updates whenever the new state depends on the previous state.

---

### Q23. Why does useState not update the value immediately?

**Interview Answer:**

Calling a state setter schedules an update rather than modifying the state variable in the currently executing render.

Each render has its own state snapshot.

Therefore, reading the state immediately after calling its setter still returns the value from the current render.

**Example:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);

    console.log(count);
  };

  return (
    <button onClick={handleClick}>
      Increment
    </button>
  );
}
```

When clicked, the console logs 0 for that handler invocation, even though React schedules the next render with count 1.

---

### Q24. What is State Batching?

**Interview Answer:**

State batching is a process in which React groups multiple state updates together to reduce unnecessary rendering work.

Instead of rendering separately for every state update, React can process multiple updates and render once.

**Example:**

```jsx
function App() {
  const [name, setName] = useState("");
  const [age, setAge] = useState(0);

  const updateUser = () => {
    setName("Nikitha");
    setAge(22);
  };

  return (
    <button onClick={updateUser}>
      Update
    </button>
  );
}
```

React can batch these updates into one render.

---

### Q25. What is Automatic Batching?

**Interview Answer:**

Automatic batching is a React feature that groups multiple state updates into a single render in more situations.

React 18 introduced automatic batching with the modern `createRoot` API.

It works for updates inside:
- React event handlers.
- Promises.
- `setTimeout`.
- Native event handlers.

**Example:**

```jsx
setTimeout(() => {
  setCount(count + 1);
  setName("React");
}, 1000);
```

With React 18's modern root API, these updates can be batched into one render.

**Important:** `flushSync` from `react-dom` can force a synchronous DOM update when genuinely required, but it should be used sparingly.

---

### Q26. What is Lazy Initialization in useState?

**Interview Answer:**

Lazy initialization means passing a function to `useState` so that React calls the function to calculate the initial state during initialization.

It is useful when calculating the initial state is expensive.

**Without Lazy Initialization:**

```jsx
const [value, setValue] = useState(
  expensiveCalculation()
);
```

Here, `expensiveCalculation()` is evaluated whenever the component function runs.

**With Lazy Initialization:**

```jsx
const [value, setValue] = useState(() => {
  return expensiveCalculation();
});
```

React uses the function's result as the initial state.

**Important:** The initializer should be pure. In development Strict Mode, React may call it more than once to detect impurities.

---

### Q27. Can useState store objects and arrays?

**Interview Answer:**

Yes. `useState` can store any JavaScript value, including:
- Numbers.
- Strings.
- Booleans.
- Arrays.
- Objects.
- Null.
- Functions.

Example:

```jsx
const [user, setUser] = useState({
  name: "Nikitha",
  age: 22
});
```

---

### Q28. How do you update an object in state?

**Interview Answer:**

We should create a new object using the spread operator and update the required property.

Example:

```jsx
const [user, setUser] = useState({
  name: "Nikitha",
  age: 22
});

const updateAge = () => {
  setUser(previousUser => ({
    ...previousUser,
    age: 23
  }));
};
```

**Important:** The spread operator performs a shallow copy. Nested objects must be copied at each level that needs updating.

Example:

```jsx
setUser(previous => ({
  ...previous,
  address: {
    ...previous.address,
    city: "Bangalore"
  }
}));
```

---

### Q29. How do you update an array in state?

**Interview Answer:**

We should use methods that create a new array rather than modifying the existing array.

Common methods:
- `map()`
- `filter()`
- Spread operator.
- `slice()`.

**Adding an item:**

```jsx
setItems(previous => [
  ...previous,
  "React"
]);
```

**Removing an item:**

```jsx
setItems(previous =>
  previous.filter(item => item !== "React")
);
```

**Updating an item:**

```jsx
setItems(previous =>
  previous.map(item =>
    item.id === 1
      ? { ...item, name: "Updated" }
      : item
  )
);
```

---

### Q30. Why should state be treated as immutable?

**Interview Answer:**

React state should be treated as immutable because React uses identity comparisons to determine whether state has changed.

Mutating an existing object or array preserves its reference, which can prevent React from recognizing the intended update.

Creating a new object or array provides a new reference and follows React's recommended state management approach.

**Incorrect:**

```jsx
user.name = "Rahul";

setUser(user);
```

**Correct:**

```jsx
setUser(previous => ({
  ...previous,
  name: "Rahul"
}));
```

---

### Q31. Can we use multiple useState Hooks in one component?

**Interview Answer:**

Yes. We can use multiple `useState` Hooks to manage different state values inside the same functional component.

Example:

```jsx
function UserForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [age, setAge] = useState(0);

  return (
    <div>
      <input
        value={name}
        onChange={e => setName(e.target.value)}
      />

      <input
        value={email}
        onChange={e => setEmail(e.target.value)}
      />
    </div>
  );
}
```

**Advantages:**
- Separate state management.
- Better readability.
- Easier updates.
- Logical separation of unrelated state.

---

### Q32. What happens when we call a state setter with the same value?

**Interview Answer:**

React compares the new state with the current state using `Object.is`.

If they are identical, React can skip rendering work.

Example:

```jsx
const [count, setCount] = useState(10);

setCount(10);
```

Since the value is unchanged, React can skip the update.

**Important:** Do not rely on a precise number of component function executions in this situation.

---

### Q33. Can we store functions in useState?

**Interview Answer:**

Yes. However, React treats a function passed to the `useState` initializer or setter specially.

A function passed to `useState` is treated as an initializer, and a function passed to the setter is treated as an updater.

If we want to store a function as the state value, we should wrap it in another function.

**Example:**

```jsx
const [callback, setCallback] = useState(
  () => () => console.log("Hello")
);
```

Updating it:

```jsx
setCallback(() => () => console.log("Updated"));
```

---

### Q34. What is the difference between useState and normal variables?

| useState | Normal Variable |
|---|---|
| Stores state between renders | Reinitialized when component function runs |
| Setter schedules an update | Assignment does not schedule a React update |
| Can update the UI | Does not automatically update the UI |
| Preserved while component identity remains | New local variable on each render |

---

### Q35. What is the difference between useState and useReducer?

**Interview Answer:**

Both Hooks manage component state.

`useState` is suitable for simple state management, whereas `useReducer` is useful when state transitions are complex or involve multiple related actions.

| useState | useReducer |
|---|---|
| Uses a state setter | Uses a reducer and dispatch |
| Good for simple state | Good for complex state logic |
| Update logic is usually in event handlers | Update logic is centralized in a reducer |
| Less boilerplate | More structured |
| Suitable for independent values | Suitable for related state transitions |

---

### Q36. What is a Stale State Closure?

**Interview Answer:**

A stale closure occurs when a function continues to use values captured from an older render instead of accessing the latest values.

This can happen in event callbacks, timers and Effects.

**Example:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      console.log(count);
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  return <h1>{count}</h1>;
}
```

The interval captures the initial value of `count`, which is 0.

**Solution using a functional update:**

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    setCount(previous => previous + 1);
  }, 1000);

  return () => clearInterval(timer);
}, []);
```

This does not require reading the captured count value.

---

### Q37. How do you avoid stale state?

**Interview Answer:**

We can avoid stale state using:

1. Functional state updates.
2. Correct Effect dependencies.
3. Refs when mutable values must be accessed without triggering rendering.
4. Effect Events when supported and appropriate for non-reactive Effect logic.
5. Correct callback dependencies.

Example:

```jsx
setCount(previous => previous + 1);
```

This ensures the update uses the latest queued state.

---

### Q38. What happens when multiple state updates happen together?

**Interview Answer:**

React queues state updates and processes them according to their type.

Direct value updates replace the state with the provided value, whereas functional updates calculate the next value from the previous queued state.

Example:

```jsx
setCount(5);
setCount(10);
setCount(15);
```

Final value: 15.

Another example:

```jsx
setCount(previous => previous + 1);
setCount(previous => previous + 1);
setCount(previous => previous + 1);
```

If the initial count is 0, the final value is 3.

---

### Q39. Does updating state always cause a re-render?

**Interview Answer:**

A state setter schedules an update. However, React may skip rendering work when the new state is identical to the previous state according to `Object.is`.

Also, React can batch multiple updates together.

Therefore, calling a setter does not necessarily mean that the browser DOM will change.

---

### Q40. Can state updates be asynchronous?

**Interview Answer:**

State setters are not synchronous assignments. They schedule updates that React processes according to its rendering and scheduling system.

The state variable in the current render remains unchanged.

It is better to describe state updates as scheduled and potentially batched rather than simply saying that every state update is asynchronous.

---

# Section 3: useEffect – Side Effects and Synchronization

### Q41. What is useEffect?

**Interview Answer:**

`useEffect` is a React Hook that allows functional components to synchronize with external systems.

External systems can include:
- APIs.
- Timers.
- Browser event listeners.
- WebSocket connections.
- Third-party libraries.
- Subscriptions.
- Browser APIs.

React runs the Effect after committing the component's UI to the screen.

**Example:**

```jsx
import { useEffect, useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

Whenever count changes, the document title is synchronized with the latest count.

---

### Q42. Why do we use useEffect?

**Interview Answer:**

We use `useEffect` when a component needs to synchronize with something outside React.

Common use cases:

1. Fetching data from an API.
2. Creating timers.
3. Adding event listeners.
4. Managing WebSocket connections.
5. Subscribing to external data sources.
6. Synchronizing browser APIs.
7. Connecting and disconnecting third-party libraries.

**Important:** Not every operation belongs inside `useEffect`. Calculations based on props or state should usually happen during rendering, and user-triggered operations generally belong in event handlers.

---

### Q43. What is the syntax of useEffect?

```jsx
useEffect(() => {
  // Effect logic

  return () => {
    // Cleanup logic
  };
}, [dependencies]);
```

**Explanation:**

- First argument: Effect setup function.
- Return value: Optional cleanup function.
- Second argument: Dependency array.

Example:

```jsx
useEffect(() => {
  console.log("Effect executed");

  return () => {
    console.log("Cleanup executed");
  };
}, []);
```

---

### Q44. What are the parameters of useEffect?

**Interview Answer:**

`useEffect` accepts two arguments.

**1. Setup Function**

The function containing the synchronization logic.

It can optionally return a cleanup function.

**2. Dependency Array**

An optional array containing reactive values used by the Effect.

Example:

```jsx
useEffect(() => {
  console.log(name);
}, [name]);
```

Here, `name` is a dependency.

---

### Q45. What does useEffect return?

**Interview Answer:**

`useEffect` itself returns `undefined`.

However, its setup function can return a cleanup function.

Example:

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running");
  }, 1000);

  return () => {
    clearInterval(timer);
  };
}, []);
```

The cleanup function clears the interval.

---

### Q46. What are the different syntaxes of useEffect?

There are three commonly discussed forms of `useEffect`.

**1. Without Dependency Array**

```jsx
useEffect(() => {
  console.log("Effect executed");
});
```

Behavior:

- Runs after every committed render of the component.
- Cleanup runs before the next setup when one exists.
- Cleanup also runs when the component is removed.

**2. Empty Dependency Array**

```jsx
useEffect(() => {
  console.log("Effect executed");
}, []);
```

Behavior:

- Runs after the initial commit.
- Does not re-run due to changes in ordinary props or state.
- Cleanup runs when the component is removed.

**Important:** In development Strict Mode, React may perform an extra setup and cleanup cycle.

**3. Dependency Array with Values**

```jsx
useEffect(() => {
  console.log("Name changed");
}, [name]);
```

Behavior:

- Runs after the initial commit.
- Runs again when a dependency changes.
- Cleanup runs before the next setup and on unmount.

---

### Q47. What happens when useEffect has no dependency array?

**Interview Answer:**

When the dependency array is omitted, the Effect runs after every committed render.

Example:

```jsx
useEffect(() => {
  console.log("Rendered");
});
```

If a state update causes another render, the Effect runs again.

**Important:** If the Effect updates state on every execution without proper conditions, it can create an infinite update loop.

---

### Q48. What happens when useEffect has an empty dependency array?

**Interview Answer:**

An empty dependency array tells React that the Effect has no reactive dependencies.

The Effect setup runs after the initial commit, and its cleanup runs when the component is removed.

Example:

```jsx
useEffect(() => {
  console.log("Component mounted");

  return () => {
    console.log("Component cleanup");
  };
}, []);
```

**Important:** The setup and cleanup may execute an additional cycle in development Strict Mode.

---

### Q49. What happens when useEffect has dependencies?

**Interview Answer:**

When dependencies are provided, React runs the Effect after the initial commit and re-runs it whenever one of the dependencies changes.

Example:

```jsx
useEffect(() => {
  console.log("Name changed:", name);
}, [name]);
```

If `name` changes, React runs the previous cleanup, if present, and then executes the new setup.

---

### Q50. What is a Dependency Array?

**Interview Answer:**

A dependency array is an array passed as the second argument to an Effect or another dependency-based Hook.

It tells React which reactive values the Effect depends on.

Example:

```jsx
useEffect(() => {
  console.log(user);
}, [user]);
```

When `user` changes according to `Object.is`, React re-runs the Effect.

**Important:** All reactive values used by the Effect must be included unless they are intentionally handled through another supported pattern.

---

### Q51. How does React compare dependencies?

**Interview Answer:**

React compares each dependency in the current render with the corresponding dependency from the previous render using `Object.is`.

If at least one dependency changes, React re-runs the Effect.

Example:

```jsx
useEffect(() => {
  console.log("Updated");
}, [count, name]);
```

The Effect runs again if either `count` or `name` changes.

**Important:** Objects and functions can have new references on every render even if their contents appear identical.

---

### Q52. What is the Cleanup Function in useEffect?

**Interview Answer:**

A cleanup function is a function returned from the Effect setup function.

It is used to undo or clean up the synchronization established by the Effect.

Common uses:
- Clearing timers.
- Removing event listeners.
- Closing connections.
- Unsubscribing from subscriptions.
- Aborting requests when appropriate.

**Example:**

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Timer running");
  }, 1000);

  return () => {
    clearInterval(timer);
  };
}, []);
```

---

### Q53. When does the Cleanup Function execute?

**Interview Answer:**

The cleanup function executes in two main situations:

1. Before React runs the Effect setup again because a dependency changed.
2. When the component is removed from the UI.

In development Strict Mode, React also performs an extra setup-cleanup cycle to help detect missing cleanup logic.

**Example:**

```jsx
useEffect(() => {
  console.log("Setup");

  return () => {
    console.log("Cleanup");
  };
}, [count]);
```

When `count` changes:

```text
Previous Cleanup
       |
       v
New Setup
```

---

### Q54. What is the execution order of useEffect?

**Interview Answer:**

For a normal client-side render:

1. React renders the component.
2. React commits the DOM changes.
3. The browser may paint the updated UI.
4. React runs the Effect setup.

For a dependency change:

1. React renders with the new values.
2. React commits the update.
3. React runs the previous cleanup.
4. React runs the new Effect setup.

**Important:** Effects generally run after the commit, but the exact relationship to browser painting can vary depending on the interaction and scheduling. Use `useLayoutEffect` when a visual measurement or correction must happen before paint.

---

### Q55. What is the difference between useEffect and componentDidMount?

**Interview Answer:**

`componentDidMount` is a class lifecycle method that runs after a class component mounts.

`useEffect` is a Hook used to synchronize a functional component with external systems after commit.

```jsx
useEffect(() => {
  // Setup logic
}, []);
```

This is a common pattern for mount-time synchronization, but it is not an exact lifecycle equivalent because Effects have their own setup and cleanup behavior.

---

### Q56. What is the difference between useEffect and componentDidUpdate?

**Interview Answer:**

`componentDidUpdate` runs after a class component updates.

`useEffect` can run after an update when one of its dependencies changes.

Example:

```jsx
useEffect(() => {
  console.log("Count changed");
}, [count]);
```

This Effect runs after the initial commit and whenever `count` changes.

If we need to skip the initial execution, we need additional logic.

---

### Q57. What is the difference between useEffect and componentWillUnmount?

**Interview Answer:**

`componentWillUnmount` is a class lifecycle method that executes just before a class component is removed.

In functional components, the cleanup function returned by `useEffect` performs similar cleanup responsibilities.

Example:

```jsx
useEffect(() => {
  const connection = connect();

  return () => {
    connection.disconnect();
  };
}, []);
```

---

### Q58. What happens when useEffect runs in Strict Mode?

**Interview Answer:**

In development Strict Mode, React runs an additional setup and cleanup cycle for Effects to help identify missing cleanup logic.

A typical sequence is:

```text
Setup
  |
Cleanup
  |
Setup
```

This helps developers ensure that their Effects correctly clean up resources.

This extra cycle is a development behavior and does not occur in production in the same way.

---

### Q59. What is an Infinite Loop in useEffect?

**Interview Answer:**

An infinite Effect loop occurs when an Effect updates state, causing a re-render, and that re-render causes the Effect to run again repeatedly.

**Example:**

```jsx
function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setCount(count + 1);
  });

  return <h1>{count}</h1>;
}
```

The Effect runs after every render and updates the state again.

**How to fix it:**

- Add correct dependencies.
- Avoid unnecessary state updates.
- Use functional updates when appropriate.
- Remove Effects that are not needed.

---

### Q60. What causes unnecessary useEffect executions?

**Interview Answer:**

Common reasons include:

1. Missing dependency arrays.
2. Object dependencies created on every render.
3. Function dependencies created on every render.
4. Parent components re-rendering and passing new references.
5. Incorrect dependency management.
6. Strict Mode's additional development cycle.

**Example:**

```jsx
function App() {
  const options = { category: "React" };

  useEffect(() => {
    console.log("Effect executed");
  }, [options]);

  return <h1>Hello</h1>;
}
```

Every render creates a new object reference.

A better solution is often to create the object inside the Effect:

```jsx
useEffect(() => {
  const options = { category: "React" };

  console.log(options);
}, []);
```

---

### Q61. How do you fetch API data using useEffect?

**Interview Answer:**

We can use `useEffect` to fetch data after a component is committed to the screen.

We usually maintain loading, data and error states.

**Example:**

```jsx
import { useEffect, useState } from "react";

function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/users"
        );

        if (!response.ok) {
          throw new Error("Failed to fetch users");
        }

        const data = await response.json();

        setUsers(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <p>Loading...</p>;

  if (error) return <p>{error}</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

export default Users;
```

**Important:** The Effect callback itself should not be declared `async`, because an async function returns a Promise rather than a cleanup function.

---

### Q62. How do you handle API errors in useEffect?

**Interview Answer:**

We can use `try...catch...finally` inside an asynchronous function called by the Effect.

- `try` handles the successful operation.
- `catch` handles errors.
- `finally` executes whether the operation succeeds or fails.

Example:

```jsx
useEffect(() => {
  const fetchData = async () => {
    try {
      const response = await fetch("/api/users");

      if (!response.ok) {
        throw new Error("API request failed");
      }

      const data = await response.json();

      setUsers(data);
    } catch (error) {
      setError(error.message);
    }
  };

  fetchData();
}, []);
```

---

### Q63. How do you prevent Race Conditions in useEffect?

**Interview Answer:**

A race condition can occur when multiple API requests are started and an older request finishes after a newer request.

The older response may incorrectly overwrite the latest data.

We can prevent this using `AbortController` or an ignore flag.

**Using AbortController:**

```jsx
useEffect(() => {
  const controller = new AbortController();

  const fetchUsers = async () => {
    try {
      const response = await fetch("/api/users", {
        signal: controller.signal
      });

      if (!response.ok) {
        throw new Error("Request failed");
      }

      const data = await response.json();

      setUsers(data);
    } catch (error) {
      if (error.name !== "AbortError") {
        setError(error.message);
      }
    }
  };

  fetchUsers();

  return () => {
    controller.abort();
  };
}, []);
```

When the Effect is cleaned up, the request is aborted.

---

### Q64. How do you cancel an API request in useEffect?

**Interview Answer:**

We can use the browser's `AbortController` API to cancel a fetch request.

Example:

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/products", {
    signal: controller.signal
  })
    .then(response => response.json())
    .then(data => setProducts(data))
    .catch(error => {
      if (error.name !== "AbortError") {
        console.error(error);
      }
    });

  return () => {
    controller.abort();
  };
}, []);
```

---

### Q65. What is a Stale Closure in useEffect?

**Interview Answer:**

A stale closure occurs when an Effect or callback continues to use values captured from an earlier render.

This commonly happens when dependencies are missing.

Example:

```jsx
useEffect(() => {
  console.log(count);
}, []);
```

If `count` changes later, this Effect does not re-run because its dependency list is empty.

If the Effect needs to react to count changes, use:

```jsx
useEffect(() => {
  console.log(count);
}, [count]);
```

---

### Q66. Why should we not make the useEffect callback async?

**Interview Answer:**

An `async` function always returns a Promise.

However, React expects the Effect setup function to return either nothing or a cleanup function.

Therefore, we should define an asynchronous function inside the Effect and call it.

**Incorrect:**

```jsx
useEffect(async () => {
  const response = await fetch("/api/users");
}, []);
```

**Correct:**

```jsx
useEffect(() => {
  const fetchUsers = async () => {
    const response = await fetch("/api/users");
    const data = await response.json();

    setUsers(data);
  };

  fetchUsers();
}, []);
```

---

### Q67. How do you implement a Timer using useEffect?

**Interview Answer:**

We can use `setInterval()` inside `useEffect` and clear the interval in the cleanup function.

```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setSeconds(previous => previous + 1);
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  return <h1>{seconds}</h1>;
}
```

The cleanup prevents the interval from continuing after the component is removed.

---

### Q68. How do you implement Event Listeners using useEffect?

**Interview Answer:**

We can register an event listener inside an Effect and remove it during cleanup.

```jsx
useEffect(() => {
  const handleResize = () => {
    console.log(window.innerWidth);
  };

  window.addEventListener("resize", handleResize);

  return () => {
    window.removeEventListener("resize", handleResize);
  };
}, []);
```

This prevents unnecessary event listeners and memory leaks.

---

### Q69. How do you handle Subscriptions using useEffect?

**Interview Answer:**

We can subscribe to an external data source inside an Effect and unsubscribe during cleanup.

```jsx
useEffect(() => {
  const subscription = chatService.subscribe(message => {
    setMessages(previous => [...previous, message]);
  });

  return () => {
    subscription.unsubscribe();
  };
}, []);
```

This ensures that the subscription is removed when it is no longer needed.

---

### Q70. When should you NOT use useEffect?

**Interview Answer:**

We should not use `useEffect` for operations that can be performed directly during rendering or inside event handlers.

**1. Derived State**

Unnecessary:

```jsx
useEffect(() => {
  setFullName(firstName + " " + lastName);
}, [firstName, lastName]);
```

Better:

```jsx
const fullName = firstName + " " + lastName;
```

**2. User-Triggered Operations**

Unnecessary:

```jsx
useEffect(() => {
  if (submitted) {
    saveData();
  }
}, [submitted]);
```

Better: Call `saveData()` directly inside the submit event handler.

**3. Simple Calculations**

Unnecessary:

```jsx
useEffect(() => {
  setTotal(price * quantity);
}, [price, quantity]);
```

Better:

```jsx
const total = price * quantity;
```

**Important:** Effects are for synchronization with external systems, not for automatically moving every operation outside rendering.

---

### Q71. What is the difference between useEffect and Event Handlers?

**Interview Answer:**

Event handlers execute in response to specific user interactions, whereas Effects synchronize the component with external systems after rendering.

| useEffect | Event Handler |
|---|---|
| Runs as part of Effect lifecycle | Runs when an event occurs |
| Synchronizes with external systems | Handles user actions |
| Uses dependency tracking | Triggered by events |
| Can return cleanup | Usually does not return cleanup |
| Example: WebSocket connection | Example: Submit button |

---

### Q72. What is the difference between useEffect and useLayoutEffect?

**Interview Answer:**

Both Hooks allow functional components to perform work after rendering, but they execute at different points in the rendering process.

`useEffect` generally runs after the browser paints the updated screen.

`useLayoutEffect` runs after React commits DOM changes but before the browser repaints.

| useEffect | useLayoutEffect |
|---|---|
| Generally runs after paint | Runs before paint |
| Does not block paint in the usual case | Can block browser painting |
| Suitable for API requests and subscriptions | Suitable for layout measurements |
| Preferred for most Effects | Used when visual measurement must happen before paint |

**Example using useLayoutEffect:**

```jsx
import { useLayoutEffect, useRef, useState } from "react";

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
    <div>
      <div ref={tooltipRef}>Tooltip content</div>
      <p>Height: {height}</p>
    </div>
  );
}
```

---

### Q73. What happens if dependencies are omitted accidentally?

**Interview Answer:**

If a reactive value is used inside an Effect but omitted from its dependency array, the Effect may continue using a stale value.

This can cause incorrect application behavior.

Example:

```jsx
useEffect(() => {
  console.log(user);
}, []);
```

If `user` changes, the Effect will not run again.

Correct:

```jsx
useEffect(() => {
  console.log(user);
}, [user]);
```

---

### Q74. What is the exhaustive-deps rule?

**Interview Answer:**

`exhaustive-deps` is a React Hooks ESLint rule that checks whether Effects and other dependency-based Hooks include the reactive values they use.

It helps prevent stale closures and incorrect dependency management.

Example:

```jsx
useEffect(() => {
  console.log(name);
}, []);
```

The linter warns that `name` is missing from the dependency array.

Correct:

```jsx
useEffect(() => {
  console.log(name);
}, [name]);
```

**Important:** Do not disable this rule simply to stop an Effect from running. First understand why the dependency is changing and restructure the code if necessary.

---

# Section 4: Other Important Hooks

# A. useContext

### Q75. What is useContext?

**Interview Answer:**

`useContext` is a React Hook used to read and subscribe to values from a Context.

It allows components to access shared data without passing props through every intermediate component.

Common use cases:
- Authentication.
- Theme management.
- Language preferences.
- User information.
- Application-wide settings.

**Example:**

```jsx
import { createContext, useContext } from "react";

const UserContext = createContext(null);

function Profile() {
  const user = useContext(UserContext);

  return <h1>{user.name}</h1>;
}

function App() {
  const user = { name: "Nikitha" };

  return (
    <UserContext value={user}>
      <Profile />
    </UserContext>
  );
}
```

This uses the React 19 Context provider syntax. In earlier React versions, use `<UserContext.Provider value={user}>`.

---

### Q76. What problem does useContext solve?

**Interview Answer:**

`useContext` helps solve Props Drilling.

Instead of passing the same data through multiple intermediate components, we can provide the data through Context and read it directly in the components that need it.

---

### Q77. How does useContext work?

**Interview Answer:**

Context allows a parent component to provide a value to its descendants.

A child component calls `useContext` with the Context object to read the closest matching provider value above it.

**Steps:**

1. Create Context using `createContext()`.
2. Provide a value using the Context provider.
3. Access the value using `useContext()`.
4. Use the returned value inside the component.

---

### Q78. What is Context Provider?

**Interview Answer:**

A Context Provider makes a value available to components inside its subtree.

Example:

```jsx
const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext value="dark">
      <Home />
    </ThemeContext>
  );
}
```

The `Home` component and its descendants can access the value using `useContext`.

---

### Q79. What happens when Context value changes?

**Interview Answer:**

When a Context provider receives a different value according to `Object.is`, React updates the components that consume that Context.

This allows consumers to receive the latest shared data.

---

### Q80. Does useContext cause re-renders?

**Interview Answer:**

Yes. When the Context value changes, components that consume that Context can re-render.

Even if a consumer is wrapped in `React.memo`, a Context change it directly consumes can still cause it to re-render.

---

### Q81. How do you avoid unnecessary Context re-renders?

**Interview Answer:**

We can optimize Context usage by:

1. Splitting unrelated Context values.
2. Keeping state close to components that need it.
3. Memoizing provider values where useful.
4. Memoizing callback functions when their identity matters.
5. Avoiding unnecessarily large global Context objects.
6. Considering an external state management library for complex applications.

Example:

```jsx
const value = useMemo(() => ({
  user,
  login
}), [user, login]);
```

This avoids creating a new provider value object on every render when its dependencies have not changed.

---

### Q82. What is the difference between useContext and Redux?

| useContext | Redux |
|---|---|
| Built-in React API | External state management library |
| Shares values through a provider | Uses a centralized store |
| Suitable for relatively simple shared state | Suitable for complex shared state and workflows |
| No built-in reducer middleware ecosystem | Provides middleware and developer tooling |
| Context changes can update all relevant consumers | Subscription-based state selection can limit updates |
| Requires less setup | Usually requires more setup |

**Important:** Context and Redux solve related but different problems. Context distributes values; Redux provides a complete state management architecture.

---

# B. useRef

### Q83. What is useRef?

**Interview Answer:**

`useRef` is a React Hook that returns a mutable object containing a `current` property.

It is commonly used to:
1. Access DOM elements.
2. Store mutable values between renders.
3. Keep timer IDs.
4. Store previous values.
5. Hold values that should not trigger re-rendering.

**Example:**

```jsx
import { useRef } from "react";

function App() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} />

      <button onClick={focusInput}>
        Focus
      </button>
    </div>
  );
}
```

---

### Q84. What does useRef return?

**Interview Answer:**

`useRef` returns an object with a mutable `current` property.

Example:

```jsx
const inputRef = useRef(null);
```

The returned object looks conceptually like:

```javascript
{
  current: null
}
```

React preserves the same ref object between renders while the component remains mounted.

---

### Q85. What is the difference between useRef and useState?

**Interview Answer:**

Both preserve information between renders, but they serve different purposes.

| useRef | useState |
|---|---|
| Stores mutable values | Stores component state |
| Updating `.current` does not trigger rendering | Calling setter schedules rendering |
| Useful for DOM access | Useful for displaying dynamic UI |
| Same ref object persists across renders | State value is provided for each render |
| Does not automatically update UI | Used to update UI |

---

### Q86. Does updating useRef cause re-rendering?

**Interview Answer:**

No. Updating `ref.current` does not trigger a React re-render.

Example:

```jsx
const countRef = useRef(0);

const increment = () => {
  countRef.current++;
};
```

The value changes, but React does not automatically update the UI.

---

### Q87. How do you access DOM elements using useRef?

**Interview Answer:**

We can create a ref using `useRef(null)` and attach it to a DOM element through the `ref` attribute.

Example:

```jsx
function App() {
  const inputRef = useRef(null);

  return (
    <input ref={inputRef} />
  );
}
```

After React commits the element, the DOM node is available through:

```jsx
inputRef.current
```

We can use it to focus, measure or interact with the DOM when necessary.

---

### Q88. What is forwardRef?

**Interview Answer:**

`forwardRef` is an API that allows a component to receive a ref from its parent and pass it to a child DOM element or another component.

In React versions before React 19, it was commonly used to expose DOM nodes through function components.

**React 18 Example:**

```jsx
import { forwardRef } from "react";

const CustomInput = forwardRef((props, ref) => {
  return <input ref={ref} {...props} />;
});
```

Parent:

```jsx
function App() {
  const inputRef = useRef(null);

  return (
    <CustomInput ref={inputRef} />
  );
}
```

---

### Q89. Is forwardRef required in React 19?

**Interview Answer:**

No. React 19 allows function components to receive `ref` as a regular prop.

Example:

```jsx
function CustomInput({ ref, ...props }) {
  return <input ref={ref} {...props} />;
}
```

This removes the need for `forwardRef` in many new React 19 components.

However, `forwardRef` remains supported for backward compatibility.

---

# C. useMemo

### Q90. What is useMemo?

**Interview Answer:**

`useMemo` is a React Hook that caches the result of a calculation between renders.

It can help avoid repeating expensive calculations when their dependencies have not changed.

**Example:**

```jsx
const expensiveValue = useMemo(() => {
  return calculateTotal(items);
}, [items]);
```

React reuses the cached result until a dependency changes.

---

### Q91. Why do we use useMemo?

**Interview Answer:**

We use `useMemo` when a calculation is expensive and should not be repeated unnecessarily on every render.

Common examples:
- Filtering large datasets.
- Sorting large arrays.
- Expensive calculations.
- Creating stable object values for memoized consumers.

**Important:** Do not use `useMemo` for every variable. It is a performance optimization, not a requirement for correct code.

---

### Q92. What is Memoization?

**Interview Answer:**

Memoization is an optimization technique where the result of a calculation is cached so that the same calculation does not need to be repeated when its inputs have not changed.

React provides:
- `useMemo` for calculation results.
- `useCallback` for function definitions.
- `React.memo` for component rendering optimization.

---

### Q93. What is the syntax of useMemo?

```jsx
const cachedValue = useMemo(() => {
  return calculateValue();
}, [dependencies]);
```

Parameters:
1. Calculation function.
2. Dependency array.

Return value:

The cached calculation result.

---

### Q94. What is the difference between useMemo and useState?

| useMemo | useState |
|---|---|
| Caches calculation results | Stores component state |
| Recalculates when dependencies change | Updates through setter functions |
| Does not independently trigger rendering | State setter schedules rendering |
| Used for performance optimization | Used for dynamic data |

---

### Q95. What is the difference between useMemo and React.memo?

**Interview Answer:**

`useMemo` is a Hook that caches a calculation result, whereas `React.memo` is a higher-order component that can skip rendering a component when its props are unchanged.

**useMemo:**

```jsx
const filteredUsers = useMemo(() => {
  return users.filter(user => user.active);
}, [users]);
```

**React.memo:**

```jsx
const User = React.memo(function User({ name }) {
  return <h1>{name}</h1>;
});
```

| useMemo | React.memo |
|---|---|
| Caches a value | Memoizes a component |
| Called inside a component | Wraps a component |
| Returns cached calculation result | Returns a memoized component |
| Depends on dependency array | Compares props by default |

---

# D. useCallback

### Q96. What is useCallback?

**Interview Answer:**

`useCallback` is a React Hook that caches a function definition between renders until its dependencies change.

It is useful when function identity matters, especially when passing callbacks to memoized child components or using functions as dependencies.

**Example:**

```jsx
const handleClick = useCallback(() => {
  console.log("Button clicked");
}, []);
```

---

### Q97. Why do we use useCallback?

**Interview Answer:**

We use `useCallback` when we need to preserve a function's identity between renders.

Common use cases:
1. Passing callbacks to memoized child components.
2. Preventing unnecessary Effect re-execution caused by function identity changes.
3. Maintaining stable callback references for certain subscriptions or integrations.

Example:

```jsx
const handleUpdate = useCallback(() => {
  setCount(previous => previous + 1);
}, []);
```

---

### Q98. What is the difference between useMemo and useCallback?

**Interview Answer:**

Both Hooks cache values between renders, but `useMemo` caches the result of a calculation and `useCallback` caches a function definition.

| useMemo | useCallback |
|---|---|
| Caches a calculated value | Caches a function |
| Executes a calculation function | Returns a cached function |
| Used for expensive calculations | Used for stable function identity |
| Returns the calculation result | Returns the function |

**Example:**

```jsx
const total = useMemo(() => {
  return price * quantity;
}, [price, quantity]);
```

```jsx
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

---

### Q99. What is the difference between useCallback and React.memo?

**Interview Answer:**

`useCallback` preserves function identity, whereas `React.memo` can skip re-rendering a component when its props remain unchanged.

They are often used together.

**Example:**

```jsx
const Child = React.memo(function Child({ onClick }) {
  console.log("Child rendered");

  return <button onClick={onClick}>Click</button>;
});

function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("Clicked");
  }, []);

  return (
    <>
      <Child onClick={handleClick} />

      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>
    </>
  );
}
```

When the parent re-renders, the callback identity remains stable, allowing `React.memo` to skip the child's re-render if its other props are also unchanged.

---

### Q100. Does useCallback prevent re-rendering?

**Interview Answer:**

No. `useCallback` does not directly prevent a component from re-rendering.

It preserves a function reference between renders when dependencies remain unchanged.

When used with `React.memo`, it can help prevent unnecessary child re-renders caused by a new function reference.

---

# E. useReducer

### Q101. What is useReducer?

**Interview Answer:**

`useReducer` is a React Hook used to manage component state through a reducer function.

It is particularly useful when state logic is complex or when multiple actions update related state.

**Example:**

```jsx
import { useReducer } from "react";

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };

    case "decrement":
      return { count: state.count - 1 };

    case "reset":
      return { count: 0 };

    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(
    reducer,
    { count: 0 }
  );

  return (
    <div>
      <h2>{state.count}</h2>

      <button onClick={() => dispatch({ type: "increment" })}>
        +
      </button>

      <button onClick={() => dispatch({ type: "decrement" })}>
        -
      </button>

      <button onClick={() => dispatch({ type: "reset" })}>
        Reset
      </button>
    </div>
  );
}
```

---

### Q102. Why do we use useReducer?

**Interview Answer:**

`useReducer` is useful when state updates involve multiple related actions or complex transition logic.

Advantages:
1. Centralizes state update logic.
2. Makes complex state transitions easier to understand.
3. Provides predictable updates.
4. Separates update logic from UI.
5. Works well with Context API.

---

### Q103. What are the parameters of useReducer?

```jsx
const [state, dispatch] = useReducer(
  reducer,
  initialArg,
  init
);
```

Parameters:

1. `reducer`: Function that calculates the next state.
2. `initialArg`: Initial state or argument passed to the initializer.
3. `init`: Optional initialization function.

---

### Q104. What does useReducer return?

**Interview Answer:**

`useReducer` returns an array containing:

1. Current state.
2. Dispatch function.

Example:

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

- `state` contains the current state.
- `dispatch` sends an action to the reducer.

---

### Q105. What is a Reducer Function?

**Interview Answer:**

A reducer is a pure function that receives the current state and an action and returns the next state.

Syntax:

```jsx
function reducer(state, action) {
  // Calculate next state

  return newState;
}
```

Example:

```jsx
function reducer(state, action) {
  if (action.type === "increment") {
    return state + 1;
  }

  return state;
}
```

A reducer should not mutate the existing state or perform side effects.

---

### Q106. What is an Action in useReducer?

**Interview Answer:**

An action is an object describing what update should happen to the state.

Example:

```jsx
dispatch({
  type: "increment"
});
```

An action can contain additional data through a payload.

```jsx
dispatch({
  type: "updateName",
  payload: "Nikitha"
});
```

The reducer checks the action type and returns the corresponding next state.

---

### Q107. What is the difference between useReducer and useState?

| useState | useReducer |
|---|---|
| Simple state management | Complex state management |
| Uses setter function | Uses dispatch function |
| Update logic commonly in component | Update logic centralized in reducer |
| Less code | More structured code |
| Good for independent state values | Good for related state transitions |

---

### Q108. What is Lazy Initialization in useReducer?

**Interview Answer:**

Lazy initialization allows us to calculate the initial reducer state using an initialization function.

Example:

```jsx
function init(initialCount) {
  return {
    count: initialCount
  };
}

const [state, dispatch] = useReducer(
  reducer,
  10,
  init
);
```

React calls `init(10)` to calculate the initial state.

---

### Q109. Can useReducer be combined with Context API?

**Interview Answer:**

Yes. We can combine `useReducer` with Context to share state and dispatch functionality across multiple components.

This is useful when multiple components need to read or update shared application state.

Example:

```jsx
const AppContext = createContext(null);

function AppProvider({ children }) {
  const [state, dispatch] = useReducer(
    reducer,
    { count: 0 }
  );

  return (
    <AppContext
      value={{ state, dispatch }}
    >
      {children}
    </AppContext>
  );
}
```

A child component can access the shared values using `useContext`.

---

# F. useLayoutEffect

### Q110. What is useLayoutEffect?

**Interview Answer:**

`useLayoutEffect` is a React Hook used to perform work after React commits DOM updates but before the browser repaints the screen.

It is useful when we need to measure or modify layout before the user sees the updated screen.

---

### Q111. When should we use useLayoutEffect?

**Interview Answer:**

Common use cases include:

1. Measuring DOM element dimensions.
2. Calculating element positions.
3. Positioning tooltips.
4. Adjusting layout before paint.
5. Preventing visual flickering caused by post-paint corrections.

---

### Q112. What is the difference between useEffect and useLayoutEffect?

| useEffect | useLayoutEffect |
|---|---|
| Usually runs after paint | Runs before paint |
| Suitable for external synchronization | Suitable for layout measurement |
| Does not normally block painting | Blocks painting while executing |
| Preferred in most cases | Used when necessary for visual correctness |

**Interview Tip:** Prefer `useEffect` unless you specifically need to perform work before the browser paints.

---

# G. useImperativeHandle

### Q113. What is useImperativeHandle?

**Interview Answer:**

`useImperativeHandle` is a React Hook used to customize the value exposed through a ref.

It allows a child component to expose specific methods or values to its parent instead of exposing its entire internal DOM implementation.

**Example for React 19:**

```jsx
import { useImperativeHandle, useRef } from "react";

function CustomInput({ ref }) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focus() {
      inputRef.current.focus();
    },

    clear() {
      inputRef.current.value = "";
    }
  }), []);

  return <input ref={inputRef} />;
}
```

Parent:

```jsx
function App() {
  const inputRef = useRef(null);

  return (
    <div>
      <CustomInput ref={inputRef} />

      <button onClick={() => inputRef.current.focus()}>
        Focus
      </button>

      <button onClick={() => inputRef.current.clear()}>
        Clear
      </button>
    </div>
  );
}
```

---

### Q114. Why do we use useImperativeHandle?

**Interview Answer:**

We use `useImperativeHandle` when a parent needs limited imperative control over a child component.

Examples:
- Focus an input.
- Clear an input.
- Play or pause media.
- Trigger a specific child operation.

It helps expose only the required methods instead of exposing the entire internal implementation.

---

# H. useId

### Q115. What is useId?

**Interview Answer:**

`useId` is a React Hook that generates a unique ID that can be used to associate related accessibility elements.

It is particularly useful for connecting labels with form controls and managing accessibility relationships.

**Example:**

```jsx
function PasswordInput() {
  const id = useId();

  return (
    <div>
      <label htmlFor={id}>
        Password
      </label>

      <input
        id={id}
        type="password"
      />
    </div>
  );
}
```

---

### Q116. Why should useId not be used as a list key?

**Interview Answer:**

`useId` is designed for accessibility-related IDs, not for identifying data items in lists.

List keys should come from stable data identifiers.

Example:

```jsx
users.map(user => (
  <User key={user.id} user={user} />
));
```

Do not use `useId()` as a replacement for stable data keys.

---

# Section 5: Advanced React Hooks

# A. useTransition

### Q117. What is useTransition?

**Interview Answer:**

`useTransition` is a React Hook that allows developers to mark certain state updates as non-urgent transitions.

This helps keep the interface responsive while React works on less urgent UI updates.

**Example:**

```jsx
const [isPending, startTransition] = useTransition();

const handleSearch = (value) => {
  setInput(value);

  startTransition(() => {
    setSearchQuery(value);
  });
};
```

Here:
- Input updates urgently.
- Search result updates are marked as a transition.
- `isPending` indicates whether the transition is pending.

---

### Q118. What is a Transition in React?

**Interview Answer:**

A Transition is a non-urgent UI update that React can prioritize below urgent interactions.

Examples:
- Updating a large filtered list.
- Switching complex views.
- Rendering expensive search results.
- Updating a large section of the UI.

Transitions help keep the interface responsive.

---

### Q119. What does useTransition return?

**Interview Answer:**

`useTransition` returns an array containing:

1. `isPending`: Boolean indicating whether a transition is pending.
2. `startTransition`: Function used to mark updates as transitions.

Syntax:

```jsx
const [isPending, startTransition] = useTransition();
```

---

### Q120. What is isPending?

**Interview Answer:**

`isPending` is a Boolean returned by `useTransition`.

It indicates whether a transition started by that Hook is currently pending.

Example:

```jsx
{isPending && <p>Updating...</p>}
```

It can be used to display loading indicators during non-urgent UI updates.

---

### Q121. What is the difference between useTransition and startTransition?

**Interview Answer:**

`useTransition` is a Hook that provides `isPending` and `startTransition`.

`startTransition` is a standalone React API that marks state updates as non-urgent transitions.

**useTransition:**

```jsx
const [isPending, startTransition] = useTransition();
```

**startTransition:**

```jsx
import { startTransition } from "react";

startTransition(() => {
  setPage(nextPage);
});
```

Use `useTransition` when the component needs to track the pending status of its transition.

---

### Q122. What is the difference between useTransition and useDeferredValue?

**Interview Answer:**

Both Hooks help improve responsiveness, but they solve different problems.

`useTransition` marks state updates as non-urgent.

`useDeferredValue` allows a value to lag behind its latest value while React works on rendering the updated UI.

| useTransition | useDeferredValue |
|---|---|
| Marks state updates as transitions | Defers a value |
| Returns `isPending` and `startTransition` | Returns a deferred value |
| Used when controlling the state update | Used when working with an existing value |
| Can control the update priority | Allows a part of the UI to lag behind |

---

# B. useDeferredValue

### Q123. What is useDeferredValue?

**Interview Answer:**

`useDeferredValue` is a React Hook that allows a component to defer updating a value until React has time to render the updated UI.

It is useful when a value drives an expensive part of the interface.

**Example:**

```jsx
function Search({ query }) {
  const deferredQuery = useDeferredValue(query);

  return (
    <SearchResults query={deferredQuery} />
  );
}
```

The results can update using the deferred value while the input uses the latest query.

---

### Q124. How does useDeferredValue work?

**Interview Answer:**

When a value changes, React initially allows the urgent UI to update.

The deferred value may temporarily retain its previous value.

React then attempts to render the UI using the latest deferred value in the background.

If another urgent update occurs, React can interrupt and restart the background rendering work.

**Important:** `useDeferredValue` does not debounce API requests. It defers rendering work, not network calls.

---

# C. useSyncExternalStore

### Q125. What is useSyncExternalStore?

**Interview Answer:**

`useSyncExternalStore` is a React Hook used to subscribe to an external store and read its current snapshot.

It is useful when React components need to stay synchronized with state managed outside React.

Examples:
- Third-party state management libraries.
- Browser APIs.
- External data stores.
- Custom subscription systems.

**Syntax:**

```jsx
const snapshot = useSyncExternalStore(
  subscribe,
  getSnapshot,
  getServerSnapshot
);
```

---

### Q126. Why do we need useSyncExternalStore?

**Interview Answer:**

React state management libraries and other external systems may manage data outside React.

`useSyncExternalStore` provides a standard way to subscribe to these stores and read consistent snapshots.

It also supports server rendering through an optional server snapshot function.

---

### Q127. What is an External Store?

**Interview Answer:**

An external store is a state management system that maintains data outside React's built-in component state.

Examples:
- Redux stores.
- Custom JavaScript stores.
- Browser APIs.
- Other subscription-based state systems.

`useSyncExternalStore` allows React components to subscribe to such stores.

---

# D. useInsertionEffect

### Q128. What is useInsertionEffect?

**Interview Answer:**

`useInsertionEffect` is a specialized React Hook designed primarily for CSS-in-JS library authors.

It runs before layout Effects and is intended for inserting styles before layout measurements occur.

**Example syntax:**

```jsx
useInsertionEffect(() => {
  // Insert dynamic styles
}, [dependencies]);
```

**Important:** Most application developers do not need this Hook.

---

### Q129. When should useInsertionEffect be used?

**Interview Answer:**

It is mainly used by CSS-in-JS libraries that need to inject dynamic styles into the document before layout Effects measure the DOM.

It should not be used for ordinary data fetching, event handling or general component logic.

---

### Q130. What is useDebugValue?

**Interview Answer:**

`useDebugValue` is a React Hook used to display custom debugging information for Custom Hooks in React Developer Tools.

It does not affect the rendered UI.

Example:

```jsx
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useDebugValue(
    isOnline ? "Online" : "Offline"
  );

  return isOnline;
}
```

---

### Q131. What is the purpose of useDebugValue?

**Interview Answer:**

It helps developers inspect Custom Hooks more easily through React Developer Tools.

It is especially useful when developing reusable Hooks that manage complex internal logic.

---

### Q132. What is the difference between useRef and useImperativeHandle?

| useRef | useImperativeHandle |
|---|---|
| Creates a mutable ref object | Customizes the value exposed through a ref |
| Used for DOM access and mutable values | Used to expose selected methods or values |
| Does not trigger re-rendering when changed | Does not independently trigger rendering |
| Commonly used directly in a component | Commonly used when building reusable child components |

---

### Q133. What is the difference between useEffect and useInsertionEffect?

| useEffect | useInsertionEffect |
|---|---|
| General synchronization Hook | Specialized style insertion Hook |
| Runs after commit | Runs before layout Effects |
| Used for APIs, subscriptions and timers | Used mainly by CSS-in-JS libraries |
| Can schedule state updates | Should not be used to update state |

---

### Q134. What is the difference between useLayoutEffect and useInsertionEffect?

| useLayoutEffect | useInsertionEffect |
|---|---|
| Runs after DOM mutations and before paint | Runs before layout Effects |
| Used for measuring DOM | Used for injecting styles |
| Can schedule state updates | Should not schedule state updates |
| Used by application developers when needed | Mainly used by styling library authors |

---

# Section 6: Custom Hooks

### Q135. What are Custom Hooks?

**Interview Answer:**

Custom Hooks are JavaScript functions created by developers to reuse stateful React logic across multiple functional components.

A Custom Hook starts with the word `use` and can call other React Hooks.

Examples:
- `useFetch`
- `useAuth`
- `useToggle`
- `useDebounce`
- `useLocalStorage`

---

### Q136. Why do we create Custom Hooks?

**Interview Answer:**

Custom Hooks help extract reusable logic from components.

Advantages:
1. Code reusability.
2. Better organization.
3. Less duplicated logic.
4. Easier maintenance.
5. Separation of UI and logic.
6. Easier testing of reusable behavior.

---

### Q137. What are the Rules of Custom Hooks?

**Interview Answer:**

Custom Hooks follow the same Rules of Hooks.

1. Their names must start with `use`.
2. They should be called at the top level of React components or other Custom Hooks.
3. They should not be called conditionally.
4. They should not be called inside loops.
5. They can call built-in Hooks and other Custom Hooks.

---

### Q138. What is the difference between Custom Hooks and Components?

| Custom Hooks | Components |
|---|---|
| Reuse logic | Render UI |
| Start with `use` | Start with uppercase |
| Return values or functions | Return React nodes |
| Called inside components or other Hooks | Used as JSX elements |
| Share behavior, not UI | Display UI |

---

### Q139. Do Custom Hooks share state?

**Interview Answer:**

No. Calling the same Custom Hook in different components does not automatically share the same state.

Each component gets its own independent Hook state.

If shared state is required, we can use:
- Context API.
- Redux.
- An external store.
- Another shared state management solution.

---

### Q140. How do you create a Custom Hook?

**Example: useToggle**

```jsx
import { useState } from "react";

function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = () => {
    setValue(previous => !previous);
  };

  return [value, toggle];
}

export default useToggle;
```

Using it:

```jsx
function App() {
  const [isOpen, toggle] = useToggle();

  return (
    <div>
      <button onClick={toggle}>
        Toggle
      </button>

      {isOpen && <h1>Hello</h1>}
    </div>
  );
}
```

---

### Q141. What is useFetch?

**Interview Answer:**

`useFetch` is a Custom Hook created to encapsulate reusable API fetching logic.

It commonly manages:
- Loading state.
- Data.
- Error handling.
- Request cancellation.
- Refetching.

**Example:**

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    async function fetchData() {
      setLoading(true);
      setError(null);

      try {
        const response = await fetch(url, {
          signal: controller.signal
        });

        if (!response.ok) {
          throw new Error("Request failed");
        }

        const result = await response.json();

        setData(result);
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err.message);
        }
      } finally {
        if (!controller.signal.aborted) {
          setLoading(false);
        }
      }
    }

    fetchData();

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}
```

**Important:** This is a basic reusable example. Production data fetching may require caching, deduplication, retries and server-state management.

---

### Q142. How do you create a useLocalStorage Hook?

**Interview Answer:**

A `useLocalStorage` Custom Hook synchronizes React state with browser local storage.

Example:

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const stored = localStorage.getItem(key);

      return stored !== null
        ? JSON.parse(stored)
        : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    localStorage.setItem(
      key,
      JSON.stringify(value)
    );
  }, [key, value]);

  return [value, setValue];
}
```

Usage:

```jsx
function App() {
  const [name, setName] = useLocalStorage(
    "username",
    ""
  );

  return (
    <input
      value={name}
      onChange={e => setName(e.target.value)}
    />
  );
}
```

**Important:** This simplified version assumes a browser environment. Server-rendered applications need guards for unavailable browser APIs and a strategy for avoiding server/client initial-value mismatches.

---

### Q143. How do you create a useDebounce Hook?

**Interview Answer:**

A debounce Hook delays updating a value until a specified amount of time has passed without another change.

It is commonly used in search functionality to reduce unnecessary API calls.

Example:

```jsx
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}
```

Usage:

```jsx
function Search() {
  const [search, setSearch] = useState("");

  const debouncedSearch = useDebounce(search, 500);

  useEffect(() => {
    if (debouncedSearch) {
      console.log("Search API:", debouncedSearch);
    }
  }, [debouncedSearch]);

  return (
    <input
      value={search}
      onChange={e => setSearch(e.target.value)}
    />
  );
}
```

---

### Q144. How do you create a useWindowSize Hook?

**Interview Answer:**

A `useWindowSize` Hook tracks the browser window's dimensions.

```jsx
function useWindowSize() {
  const [size, setSize] = useState(() => ({
    width: window.innerWidth,
    height: window.innerHeight
  }));

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    };

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return size;
}
```

**Important:** For server-rendered applications, avoid reading `window` during server rendering. Use a server-safe initial value or an appropriate external-store approach.

---

### Q145. How do you create a useOnlineStatus Hook?

**Interview Answer:**

A `useOnlineStatus` Hook tracks whether the browser reports an online or offline status.

```jsx
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(
    () => navigator.onLine
  );

  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);

    window.addEventListener("online", handleOnline);
    window.addEventListener("offline", handleOffline);

    return () => {
      window.removeEventListener("online", handleOnline);
      window.removeEventListener("offline", handleOffline);
    };
  }, []);

  return isOnline;
}
```

**Important:** `navigator.onLine` is only a browser connectivity hint. It does not guarantee that a particular API or server is reachable.

---

### Q146. How do you create a useToggle Hook?

**Answer:**

```jsx
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = () => {
    setValue(previous => !previous);
  };

  return [value, toggle];
}
```

This is useful for:
- Modals.
- Dropdowns.
- Password visibility.
- Accordions.
- Menus.

---

### Q147. How do you create a usePrevious Hook?

**Interview Answer:**

A `usePrevious` Custom Hook stores the previous value of a variable using a ref.

```jsx
function usePrevious(value) {
  const ref = useRef();

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref.current;
}
```

Usage:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const previousCount = usePrevious(count);

  return (
    <div>
      <h2>Current: {count}</h2>
      <h2>Previous: {previousCount}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

The previous value becomes available after the Effect from the preceding committed render has run.

---

### Q148. How do you test Custom Hooks?

**Interview Answer:**

Custom Hooks can be tested by rendering a component that uses the Hook or by using a Hook testing utility.

Common approaches include:
1. Render a test component that uses the Hook.
2. Use `renderHook` from React Testing Library.
3. Use `act()` to apply updates.
4. Assert returned values.
5. Test state updates and cleanup behavior.

Example using React Testing Library:

```jsx
import { renderHook, act } from "@testing-library/react";

test("useToggle toggles value", () => {
  const { result } = renderHook(() => useToggle());

  expect(result.current[0]).toBe(false);

  act(() => {
    result.current[1]();
  });

  expect(result.current[0]).toBe(true);
});
```

---

# Section 7: React 19 and Newer Hook APIs

# A. useActionState

### Q149. What are the important new Hooks introduced in React 19?

**Interview Answer:**

React 19 introduced important APIs for handling Actions, asynchronous forms and optimistic updates.

The major additions include:

1. `useActionState`
2. `useOptimistic`
3. `useFormStatus` in React DOM
4. The `use()` API

Newer React versions also provide `useEffectEvent`.

These APIs help manage asynchronous operations, form submissions and responsive UI updates.

---

### Q150. What is useActionState?

**Interview Answer:**

`useActionState` is a React Hook used to manage the state returned by an Action.

It allows a component to manage the result of an action, track whether it is pending and optionally display server validation errors.

It is commonly used with forms.

**Syntax:**

```jsx
const [state, formAction, isPending] = useActionState(
  action,
  initialState
);
```

**Parameters:**

1. `action`: Function that receives previous state and submitted form data.
2. `initialState`: Initial state value.
3. Optional permalink: Used for progressive enhancement with server-rendered forms.

**Return Values:**

1. `state`: Latest state returned by the Action.
2. `formAction`: Action function to pass to a form.
3. `isPending`: Indicates whether the Action is pending.

---

### Q151. Why do we use useActionState?

**Interview Answer:**

We use `useActionState` to manage the state of asynchronous Actions, particularly form submissions.

It helps manage:
- Submission results.
- Validation errors.
- Success messages.
- Pending states.
- Server responses.

---

### Q152. What are the parameters of useActionState?

**Interview Answer:**

The main parameters are:

**1. Action Function**

Receives the previous state and the submitted payload.

**2. Initial State**

Defines the initial value of the Action state.

**3. Optional Permalink**

Can provide a destination URL for progressive enhancement in server-rendered applications.

---

### Q153. What does useActionState return?

```jsx
const [state, formAction, isPending] = useActionState(
  action,
  initialState
);
```

| Return Value | Purpose |
|---|---|
| `state` | Latest Action result |
| `formAction` | Function passed to form action |
| `isPending` | Indicates pending Action |

---

### Q154. What is the difference between useState and useActionState?

| useState | useActionState |
|---|---|
| General component state | State associated with an Action |
| Uses a state setter | Uses an Action function |
| Does not provide pending status | Provides `isPending` |
| Suitable for counters and UI state | Suitable for form submissions and Action results |
| No built-in previous Action state argument | Action receives previous state |

---

### Q155. What is a React Action?

**Interview Answer:**

An Action is a function used to handle an operation that updates data or performs an application task.

React supports Actions in forms and transition-based updates.

Actions can be synchronous or asynchronous.

Common use cases:
- Form submissions.
- Updating user profiles.
- Saving data.
- Deleting records.
- Submitting comments.
- Performing server operations.

---

### Q156. What is the difference between synchronous and asynchronous Actions?

**Interview Answer:**

A synchronous Action performs its work without awaiting an asynchronous operation.

An asynchronous Action can use Promises and `async/await` to perform operations such as API requests.

Example:

```jsx
async function updateProfile(formData) {
  const name = formData.get("name");

  const response = await fetch("/api/profile", {
    method: "POST",
    body: JSON.stringify({ name })
  });

  if (!response.ok) {
    throw new Error("Update failed");
  }

  return response.json();
}
```

---

# B. useOptimistic

### Q157. What is useOptimistic?

**Interview Answer:**

`useOptimistic` is a React Hook that allows developers to display an optimistic UI update while an asynchronous Action is in progress.

Instead of waiting for the server response, the UI can immediately show the expected result.

**Syntax:**

```jsx
const [optimisticState, addOptimistic] = useOptimistic(
  state,
  updateFunction
);
```

---

### Q158. Why do we use useOptimistic?

**Interview Answer:**

We use `useOptimistic` to improve the perceived responsiveness of applications.

It is useful when:
- Sending messages.
- Adding comments.
- Liking posts.
- Updating shopping cart items.
- Performing quick form actions.

---

### Q159. What does useOptimistic return?

**Interview Answer:**

`useOptimistic` returns an array containing:

1. The optimistic state.
2. A function used to apply an optimistic update.

Example:

```jsx
const [optimisticMessages, addOptimisticMessage] =
  useOptimistic(messages, reducer);
```

---

### Q160. What is Optimistic UI?

**Interview Answer:**

Optimistic UI is a user interface technique in which the application immediately displays the expected result of an operation before receiving confirmation from the server.

This improves perceived performance.

**Example:**

When a user sends a message:
1. The message appears immediately.
2. The API request is sent.
3. If successful, the server confirms the operation.
4. If unsuccessful, the application can display an error and restore the appropriate UI.

---

### Q161. What happens when an Optimistic Update fails?

**Interview Answer:**

An optimistic update represents a temporary expected state.

When the Action finishes, React uses the actual state provided by the application.

If the operation fails and the source state was not updated, the optimistic value is no longer maintained as the temporary Action finishes.

The application should still handle the error and show an appropriate message.

**Example:**

```jsx
function LikeButton({ likes, saveLike }) {
  const [optimisticLikes, addOptimisticLike] =
    useOptimistic(
      likes,
      (currentLikes, amount) => currentLikes + amount
    );

  const handleLike = async () => {
    addOptimisticLike(1);

    await saveLike();
  };

  return (
    <button onClick={handleLike}>
      Likes: {optimisticLikes}
    </button>
  );
}
```

For real applications, the optimistic update should be performed within an Action or Transition, with appropriate error handling.

---

# C. useFormStatus

### Q162. What is useFormStatus?

**Interview Answer:**

`useFormStatus` is a React DOM Hook used to access the submission status of the nearest parent form.

It is useful for creating reusable form submission components.

Example:

```jsx
import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus();

  return (
    <button disabled={pending}>
      {pending ? "Submitting..." : "Submit"}
    </button>
  );
}
```

---

### Q163. What does useFormStatus return?

**Interview Answer:**

`useFormStatus` returns an object containing information about the parent form.

Important properties:

| Property | Meaning |
|---|---|
| `pending` | Whether the parent form is pending |
| `data` | FormData associated with the submission |
| `method` | Form submission method |
| `action` | Form action function or URL |

---

### Q164. What is the difference between useFormStatus and useActionState?

**Interview Answer:**

`useActionState` manages the state and pending status of an Action.

`useFormStatus` reads the submission status of a parent form.

| useActionState | useFormStatus |
|---|---|
| Manages Action state | Reads parent form status |
| Returns state, action and pending | Returns form status object |
| Used in the component managing the Action | Used inside a component rendered within the form |
| Can handle Action results | Does not manage Action results |

**Important:** `useFormStatus` must be called in a descendant component of the form whose status you want to read. Calling it in the component that renders the form does not read that form's submission status.

---

### Q165. What is the difference between useOptimistic and useState?

| useOptimistic | useState |
|---|---|
| Provides temporary optimistic state | Provides regular component state |
| Designed for optimistic UI | Designed for general state management |
| Usually used during Actions or transitions | Used in normal component interactions |
| Reverts to the current source state when the optimistic Action finishes | Remains until updated |
| Useful for immediate server-operation feedback | Useful for persistent UI state |

---

# D. useEffectEvent

### Q166. What is useEffectEvent?

**Interview Answer:**

`useEffectEvent` is a React Hook introduced in newer React versions that allows developers to separate non-reactive event logic from an Effect.

An Effect Event can read the latest props and state without making those values dependencies of the Effect.

It is useful when an Effect needs to establish a connection or subscription, but a callback associated with it must always read the latest values.

**Example:**

```jsx
function ChatRoom({ roomId, theme }) {
  const onConnected = useEffectEvent(() => {
    showNotification("Connected!", theme);
  });

  useEffect(() => {
    const connection = createConnection(roomId);

    connection.on("connected", () => {
      onConnected();
    });

    connection.connect();

    return () => connection.disconnect();
  }, [roomId]);

  return <h1>Chat Room</h1>;
}
```

Here:
- The connection depends on `roomId`.
- The notification uses the latest `theme`.
- Changing `theme` does not need to reconnect the chat room.

**Important:** Effect Events are intended to be called from Effects or other Effect Events, not passed around as ordinary event handlers.

---

### Q167. Why was useEffectEvent introduced?

**Interview Answer:**

It was introduced to help separate reactive synchronization logic from non-reactive logic inside Effects.

It is useful when a callback needs the latest props or state but should not cause the Effect to re-run whenever those values change.

---

### Q168. What is the difference between useEffectEvent and useCallback?

| useEffectEvent | useCallback |
|---|---|
| Reads latest props and state when invoked | Caches a function according to dependencies |
| Designed for Effect-related logic | Designed for stable function identity |
| Does not use a dependency array | Uses a dependency array |
| Not intended as a normal event handler | Can be passed as a callback |
| Separates non-reactive logic from Effects | Helps optimize callback identity |

---

### Q169. What is the difference between useEffectEvent and Event Handlers?

**Interview Answer:**

Normal event handlers respond to user interactions such as clicks and submissions.

Effect Events are used by Effects to access the latest values without making every value a dependency of the Effect.

They serve different purposes and should not be used interchangeably.

---

# E. The React use() API

### Q170. What is the React use() API?

**Interview Answer:**

`use()` is a React API that allows components to read resources such as Promises and Context.

When reading a Promise, React can suspend rendering until the Promise resolves.

When reading Context, it returns the Context value.

Example:

```jsx
function UserProfile({ userPromise }) {
  const user = use(userPromise);

  return <h1>{user.name}</h1>;
}
```

A component reading a Promise should generally be rendered inside a Suspense boundary.

---

### Q171. Is use() a Hook?

**Interview Answer:**

No. `use()` is a React API, not a Hook under the normal Rules of Hooks.

Unlike regular Hooks, it can be called conditionally and inside loops.

However, it must still follow its own restrictions, including not being called inside `try/catch`.

---

### Q172. How is use() different from useEffect?

| use() | useEffect |
|---|---|
| Reads resources such as Promises and Context | Synchronizes with external systems |
| Can suspend rendering while reading a Promise | Runs after commit |
| Can read Context conditionally | Must follow Hook call-order rules |
| Used during rendering | Used for post-commit synchronization |

---

### Q173. Can use() be called conditionally?

**Interview Answer:**

Yes. Unlike normal Hooks, `use()` can be called inside conditions and loops.

Example:

```jsx
function Theme({ showTheme }) {
  if (showTheme) {
    const theme = use(ThemeContext);

    return <h1>{theme}</h1>;
  }

  return null;
}
```

However, it cannot be called inside a `try/catch` block.

---

### Q174. What is the difference between use() and useContext?

**Interview Answer:**

Both can read Context values.

`useContext` follows the normal Rules of Hooks and must be called at the top level.

The `use()` API can read Context conditionally and inside loops.

Example:

```jsx
const theme = useContext(ThemeContext);
```

Compared with:

```jsx
const theme = use(ThemeContext);
```

For normal Context usage, `useContext` remains the familiar choice.

---

### Q175. What is the difference between React Actions and traditional Event Handlers?

**Interview Answer:**

Traditional event handlers respond to user interactions and contain the logic required to handle those interactions.

React Actions provide a pattern for managing operations that update data, particularly asynchronous form submissions and transition-based updates.

Actions can integrate with:
- `useActionState`
- `useOptimistic`
- `useFormStatus`
- Form `action` props.

---

# Section 8: Practical and Scenario-Based Questions

### Q176. How do you fetch data when a component mounts?

**Answer:**

Use `useEffect` with an empty dependency array for mount-time synchronization, including appropriate loading, error handling and cleanup.

```jsx
useEffect(() => {
  const controller = new AbortController();

  async function fetchData() {
    try {
      const response = await fetch("/api/users", {
        signal: controller.signal
      });

      if (!response.ok) {
        throw new Error("Request failed");
      }

      setUsers(await response.json());
    } catch (error) {
      if (error.name !== "AbortError") {
        setError(error.message);
      }
    }
  }

  fetchData();

  return () => controller.abort();
}, []);
```

---

### Q177. How do you execute an Effect only when a particular value changes?

**Answer:**

Include the value in the dependency array.

```jsx
useEffect(() => {
  console.log("Search changed");
}, [search]);
```

The Effect runs after the initial commit and when `search` changes.

---

### Q178. How do you run cleanup when a component unmounts?

**Answer:**

Return a cleanup function from `useEffect`.

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running");
  }, 1000);

  return () => clearInterval(timer);
}, []);
```

---

### Q179. How do you avoid an infinite useEffect loop?

**Answer:**

1. Identify which state update triggers the Effect.
2. Check whether the dependency array is correct.
3. Avoid unnecessary state updates.
4. Avoid creating changing object and function dependencies unnecessarily.
5. Calculate derived values during rendering.
6. Use functional state updates when suitable.

---

### Q180. How do you prevent duplicate API requests?

**Interview Answer:**

The solution depends on why duplicate requests are occurring.

Possible approaches:
1. Abort previous requests during cleanup.
2. Use request deduplication.
3. Use a caching library.
4. Use an appropriate data-fetching framework.
5. Prevent duplicate submissions through UI state.
6. Ensure Effects correctly handle Strict Mode.

**Important:** React Strict Mode may intentionally perform an extra Effect setup-cleanup cycle in development. Do not simply remove Strict Mode to hide duplicate requests.

---

### Q181. How do you prevent stale API responses?

**Answer:**

Use `AbortController` or an ignore flag to prevent an older request from updating the latest UI.

```jsx
useEffect(() => {
  let ignore = false;

  async function fetchData() {
    const response = await fetch(`/api/users?q=${search}`);

    const data = await response.json();

    if (!ignore) {
      setUsers(data);
    }
  }

  fetchData();

  return () => {
    ignore = true;
  };
}, [search]);
```

---

### Q182. How do you implement Search with Debounce?

**Answer:**

Use a Custom Hook to delay the search value before making an API request.

```jsx
function Search() {
  const [search, setSearch] = useState("");

  const debouncedSearch = useDebounce(search, 500);

  useEffect(() => {
    if (!debouncedSearch) return;

    console.log("API request:", debouncedSearch);
  }, [debouncedSearch]);

  return (
    <input
      value={search}
      onChange={e => setSearch(e.target.value)}
      placeholder="Search products"
    />
  );
}
```

---

### Q183. How do you implement Pagination using Hooks?

**Answer:**

Use state to track the current page and calculate which items should be displayed.

```jsx
function Pagination({ items }) {
  const [page, setPage] = useState(1);

  const pageSize = 5;

  const startIndex = (page - 1) * pageSize;

  const currentItems = items.slice(
    startIndex,
    startIndex + pageSize
  );

  const totalPages = Math.ceil(items.length / pageSize);

  return (
    <div>
      {currentItems.map(item => (
        <p key={item.id}>{item.name}</p>
      ))}

      <button
        disabled={page === 1}
        onClick={() => setPage(previous => previous - 1)}
      >
        Previous
      </button>

      <span>{page} / {totalPages}</span>

      <button
        disabled={page === totalPages}
        onClick={() => setPage(previous => previous + 1)}
      >
        Next
      </button>
    </div>
  );
}
```

---

### Q184. How do you manage Authentication using Context and Hooks?

**Answer:**

We can create an authentication Context and manage authentication state using `useState` or `useReducer`.

Example:

```jsx
const AuthContext = createContext(null);

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const login = (userData) => {
    setUser(userData);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <AuthContext
      value={{ user, login, logout }}
    >
      {children}
    </AuthContext>
  );
}

function useAuth() {
  return useContext(AuthContext);
}
```

Components can call `useAuth()` to access authentication information.

**Important:** Real authentication also requires secure token/session handling, backend validation and proper authorization.

---

### Q185. How do you implement a Modal using Hooks?

**Answer:**

Use `useState` to control whether the modal is open or closed.

```jsx
function Modal() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsOpen(true)}>
        Open Modal
      </button>

      {isOpen && (
        <div className="modal">
          <h2>Hello</h2>

          <button onClick={() => setIsOpen(false)}>
            Close
          </button>
        </div>
      )}
    </div>
  );
}
```

For a production modal, also consider:
- Focus management.
- Escape key handling.
- Backdrop interaction.
- Accessibility attributes.
- Focus restoration.
- Body scroll management.

---

### Q186. How do you manage multiple form fields?

**Answer:**

We can use one state object to manage multiple form fields.

```jsx
function Form() {
  const [formData, setFormData] = useState({
    name: "",
    email: "",
    password: ""
  });

  const handleChange = (event) => {
    const { name, value } = event.target;

    setFormData(previous => ({
      ...previous,
      [name]: value
    }));
  };

  return (
    <form>
      <input
        name="name"
        value={formData.name}
        onChange={handleChange}
      />

      <input
        name="email"
        value={formData.email}
        onChange={handleChange}
      />

      <input
        name="password"
        value={formData.password}
        onChange={handleChange}
      />
    </form>
  );
}
```

---

### Q187. How do you optimize a large Search List?

**Answer:**

We can use several optimization techniques:

1. Debouncing input.
2. `useMemo` for expensive filtering.
3. `useDeferredValue` for expensive rendering.
4. List virtualization.
5. Pagination.
6. Server-side filtering.
7. Avoiding unnecessary component re-renders.

---

### Q188. How do you handle expensive calculations?

**Answer:**

We can use `useMemo` to cache expensive calculation results.

```jsx
const total = useMemo(() => {
  return products.reduce(
    (sum, product) => sum + product.price,
    0
  );
}, [products]);
```

The calculation is repeated when the dependency changes.

---

### Q189. How do you avoid unnecessary child re-renders?

**Answer:**

We can use:

1. `React.memo`.
2. `useCallback`.
3. `useMemo`.
4. State colocation.
5. Proper component composition.
6. Stable props.

These techniques should be used when profiling or reasoning identifies a meaningful performance issue.

---

### Q190. How do you share logic between components?

**Answer:**

We can create Custom Hooks.

Example:

```jsx
function useCounter() {
  const [count, setCount] = useState(0);

  const increment = () => {
    setCount(previous => previous + 1);
  };

  return { count, increment };
}
```

Multiple components can use the same Hook to reuse the behavior.

Each Hook invocation has its own independent state.

---

### Q191. How do you manage Global State using useReducer and Context?

**Answer:**

We can combine `useReducer` with Context API.

- `useReducer` manages state transitions.
- Context shares the state and dispatch function.
- Custom Hooks provide convenient access.

This is useful when several components need to access the same state.

---

### Q192. How do you implement Optimistic Updates?

**Answer:**

Use `useOptimistic` with an Action to display the expected result before the server confirms it.

Example use cases:
- Sending messages.
- Adding comments.
- Updating likes.
- Adding cart items.

The application should still handle server failures and restore the correct source state.

---

### Q193. How do you handle Loading, Success and Error States?

**Answer:**

We can manage these using multiple state variables or a single state object.

Example:

```jsx
const [status, setStatus] = useState("idle");

const fetchData = async () => {
  setStatus("loading");

  try {
    await fetch("/api/users");

    setStatus("success");
  } catch {
    setStatus("error");
  }
};
```

The UI can render according to the current status.

---

### Q194. How do you synchronize a component with an external system?

**Answer:**

Use `useEffect` to establish synchronization after commit and return a cleanup function to undo it.

Examples:
- WebSocket connections.
- Browser event listeners.
- Third-party widgets.
- Timers.
- Subscriptions.

---

### Q195. How do you handle Timers and Intervals safely?

**Answer:**

Create the timer inside `useEffect` and clear it in the cleanup function.

Use functional state updates when updating state from an interval.

```jsx
useEffect(() => {
  const interval = setInterval(() => {
    setCount(previous => previous + 1);
  }, 1000);

  return () => clearInterval(interval);
}, []);
```

---

# Section 9: Tricky Output-Based Interview Questions

### Q196. What happens when state is updated multiple times in one event?

**Question:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  };

  return (
    <button onClick={handleClick}>
      {count}
    </button>
  );
}
```

**Question:** What will be the final count after one click?

**Answer:** 1.

**Explanation:** All three updates use the same `count` value from the current render, which is 0.

All three request a value of 1.

---

### Q197. What is the output of multiple functional state updates?

**Question:**

```jsx
const [count, setCount] = useState(0);

const handleClick = () => {
  setCount(previous => previous + 1);
  setCount(previous => previous + 1);
  setCount(previous => previous + 1);
};
```

**Answer:** 3.

**Explanation:** React processes the updater functions in order, passing each result to the next updater.

---

### Q198. Why does useEffect run after rendering?

**Answer:**

React Effects are designed to synchronize a committed component with external systems.

They run after React has committed the UI, rather than during the render phase.

---

### Q199. What happens when an object is passed as a dependency?

**Question:**

```jsx
function App() {
  const user = { name: "Nikitha" };

  useEffect(() => {
    console.log("Effect executed");
  }, [user]);

  return <h1>Hello</h1>;
}
```

**Answer:**

The Effect runs after every committed render because a new object is created on each render.

React compares object references using `Object.is`.

---

### Q200. Why does an empty object cause repeated Effect execution?

**Question:**

```jsx
function App() {
  const [count, setCount] = useState(0);

  const options = {};

  useEffect(() => {
    console.log("Effect executed");
  }, [options]);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

**Answer:**

Every render creates a new object reference.

Even though both objects are empty, they are not identical according to `Object.is`.

Therefore, the Effect runs again.

---

### Q201. What happens when a state variable is used inside a stale closure?

**Question:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      setCount(count + 1);
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  return <h1>{count}</h1>;
}
```

**Answer:**

The count becomes 1 and remains there.

**Explanation:** The interval captures the initial value of count, which is 0.

Each interval execution requests the value 1.

**Correct Solution:**

```jsx
setCount(previous => previous + 1);
```

---

### Q202. What happens when useMemo has no dependencies?

**Question:**

```jsx
const value = useMemo(() => {
  return calculateTotal();
});
```

**Answer:**

Without a dependency array, the calculation runs during every render.

There is no dependency-based reuse between renders.

---

### Q203. What happens when useCallback has an empty dependency array?

**Question:**

```jsx
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

**Answer:**

React returns the cached function on subsequent renders while the component retains its identity.

However, the function captures values from the render in which it was created.

If it reads changing props or state, this can cause stale closures.

---

### Q204. Does changing a ref update the UI?

**Question:**

```jsx
const countRef = useRef(0);

countRef.current++;

console.log(countRef.current);
```

**Answer:**

No. The ref value changes, but updating `.current` does not trigger a React re-render.

---

### Q205. What happens when a component is wrapped in StrictMode?

**Answer:**

React performs additional development checks.

These can include:
- Extra component rendering to detect impure rendering.
- Additional Effect setup and cleanup.
- Ref callback checks.

These checks help identify potential bugs.

---

### Q206. Why can an Effect execute twice in development?

**Answer:**

When Strict Mode is enabled, React performs an additional Effect setup-cleanup cycle in development.

This helps identify Effects that do not correctly clean up resources.

---

### Q207. What happens when the dependency array contains a function?

**Question:**

```jsx
function App() {
  const handleClick = () => {
    console.log("Clicked");
  };

  useEffect(() => {
    console.log("Effect");
  }, [handleClick]);

  return <button onClick={handleClick}>Click</button>;
}
```

**Answer:**

The Effect can run after every render because a new function is created on each render.

If stable identity is genuinely required, we can use `useCallback` or restructure the Effect.

---

### Q208. What happens when a state setter is called inside useEffect?

**Answer:**

Calling a state setter schedules another render.

If the Effect depends on the updated state or runs after every render, it can cause repeated updates.

---

### Q209. What happens when a component is conditionally rendered?

**Answer:**

When a component is removed from the React tree, its local state is discarded and its Effect cleanup functions execute.

When it is mounted again as a new component instance, its state is initialized again.

---

### Q210. What happens when a Custom Hook is called in a loop?

**Answer:**

Calling a Custom Hook inside a loop violates the Rules of Hooks.

React requires regular Hooks to be called in the same order on every render.

Instead, create a child component for each item or redesign the logic so the Hook is called at the top level.

---

# Section 10: Important Hook Comparisons

## Complete Comparison Table

| Hooks | Main Difference |
|---|---|
| `useState` vs `useReducer` | Simple state vs reducer-based state transitions |
| `useState` vs `useRef` | Render-triggering state vs mutable reference |
| `useEffect` vs `useLayoutEffect` | Post-commit synchronization vs pre-paint layout work |
| `useEffect` vs `useInsertionEffect` | General synchronization vs style insertion |
| `useMemo` vs `useCallback` | Cached value vs cached function |
| `useMemo` vs `React.memo` | Cached calculation vs memoized component |
| `useCallback` vs `React.memo` | Stable callback vs skipped component rendering |
| `useContext` vs Redux | Context distribution vs external state management |
| `useTransition` vs `useDeferredValue` | Mark state updates vs defer a value |
| `useRef` vs `useImperativeHandle` | Mutable reference vs customized exposed ref |
| `useEffect` vs event handler | External synchronization vs user interaction |
| `useActionState` vs `useState` | Action state vs general component state |
| `useOptimistic` vs `useState` | Temporary optimistic state vs regular state |
| `useFormStatus` vs `useActionState` | Parent form status vs Action state |
| `useEffectEvent` vs `useCallback` | Effect-specific non-reactive logic vs cached function |

---

# Section 11: Important Interview Traps

These are questions interviewers commonly use to test whether a candidate understands Hooks beyond definitions.

### 1. Does useEffect run before or after the DOM update?

**Answer:** After React commits DOM changes.

### 2. Does useEffect always run after browser paint?

**Answer:** Not necessarily. React may run interaction-related Effects before paint. Use `useLayoutEffect` when work must happen before repaint.

### 3. Is useEffect the same as componentDidMount?

**Answer:** No. It can model mount-time synchronization, but its setup and cleanup semantics are different.

### 4. Does useMemo guarantee that a value is permanently cached?

**Answer:** No. It is a performance optimization. React may discard the cached value in certain circumstances.

### 5. Does useCallback stop a component from rendering?

**Answer:** No. It only caches a function definition.

### 6. Does useRef preserve data between renders?

**Answer:** Yes. The same ref object is preserved between renders while the component remains mounted.

### 7. Does useRef cause re-rendering?

**Answer:** No. Changing `.current` does not trigger a render.

### 8. Can we call useState inside an if statement?

**Answer:** No. Regular Hooks must follow the Rules of Hooks.

### 9. Does every useEffect require cleanup?

**Answer:** No. Cleanup is needed when the Effect establishes something that must be undone, such as a timer, subscription or connection.

### 10. Is useEffect suitable for calculating derived state?

**Answer:** Usually no. Calculate derived values during rendering.

### 11. Does Context API automatically prevent unnecessary renders?

**Answer:** No. Consumers update when the Context value changes.

### 12. Can a Custom Hook share state between components?

**Answer:** Not automatically. Each invocation gets its own independent state.

### 13. Is useTransition used for API request debouncing?

**Answer:** No. It prioritizes UI updates, not network requests.

### 14. Does useDeferredValue delay an API request?

**Answer:** No. It defers rendering work associated with a value.

### 15. Can useActionState be used without forms?

**Answer:** Yes. It can manage Actions beyond traditional forms.

### 16. Can useFormStatus be called in the same component that renders the form?

**Answer:** It must be called in a descendant component to read the nearest parent form's status.

### 17. Is useId suitable for list keys?

**Answer:** No. Use stable IDs from your data.

### 18. Is use() subject to the normal Rules of Hooks?

**Answer:** It has special rules and can be called conditionally and in loops, unlike regular Hooks.

### 19. Can useEffect return a Promise?

**Answer:** No. It should return nothing or a cleanup function.

### 20. Does Strict Mode run twice in production?

**Answer:** The additional development checks are not performed in the same way in production.

---

# Section 12: Real-World Hook Selection Guide

| Requirement | Recommended Hook |
|---|---|
| Counter or toggle | `useState` |
| Complex state transitions | `useReducer` |
| Shared authentication data | `useContext` |
| Access an input DOM element | `useRef` |
| Store timer IDs | `useRef` |
| Expensive calculation | `useMemo` |
| Stable callback identity | `useCallback` |
| API synchronization | `useEffect` |
| Event listener management | `useEffect` |
| Layout measurement | `useLayoutEffect` |
| Custom methods exposed to parent | `useImperativeHandle` |
| Accessible unique IDs | `useId` |
| Non-urgent UI update | `useTransition` |
| Defer expensive UI rendering | `useDeferredValue` |
| External store subscription | `useSyncExternalStore` |
| Dynamic style insertion | `useInsertionEffect` |
| Custom Hook debugging | `useDebugValue` |
| Form Action result | `useActionState` |
| Optimistic UI | `useOptimistic` |
| Form pending status | `useFormStatus` |
| Latest values in Effect callbacks | `useEffectEvent` |
| Read Promise or Context resource | `use()` |

---

# Section 13: React Hooks Coding Practice

Practice these coding questions without looking at the solutions.

## Beginner Level

1. Create a counter using `useState`.
2. Create a toggle button.
3. Create a show/hide password feature.
4. Create a controlled input.
5. Create a form with multiple fields.
6. Create a character counter.
7. Create a shopping cart quantity counter.
8. Create a dark/light theme switch.
9. Create a modal using `useState`.
10. Create a previous/next pagination component.

## Intermediate Level

11. Fetch users using `useEffect`.
12. Implement API loading and error states.
13. Implement an interval timer with cleanup.
14. Create a search filter using `useMemo`.
15. Implement debounced search using a Custom Hook.
16. Build a reusable `useToggle` Hook.
17. Create a `usePrevious` Hook.
18. Create a `useLocalStorage` Hook.
19. Create a `useWindowSize` Hook.
20. Build a Todo application using `useReducer`.

## Advanced Level

21. Implement Context API authentication.
22. Create a reusable API fetching Hook.
23. Prevent stale API responses.
24. Implement an optimistic comment system.
25. Create a reusable modal with `useImperativeHandle`.
26. Optimize a large product list.
27. Implement search using `useDeferredValue`.
28. Implement non-urgent UI updates using `useTransition`.
29. Create a global state system using Context and `useReducer`.
30. Build a form using `useActionState` and `useFormStatus`.

---

# Section 14: Quick Revision Notes

## useState

- Manages component state.
- Returns current state and setter.
- State updates schedule renders.
- Functional updates are useful when next state depends on previous state.
- State should be treated as immutable.
- Supports lazy initialization.

## useEffect

- Synchronizes with external systems.
- Runs after commit.
- Dependency array controls re-execution.
- Cleanup runs before the next setup and on unmount.
- Does not automatically run only once in development Strict Mode.
- Should not be used for every calculation.

## useContext

- Reads Context values.
- Helps avoid Props Drilling.
- Consumers update when Context value changes.

## useRef

- Stores mutable values.
- Accesses DOM elements.
- Updating `.current` does not trigger rendering.

## useMemo

- Caches calculation results.
- Useful for expensive computations.
- Performance optimization, not a correctness requirement.

## useCallback

- Caches function definitions.
- Useful when function identity matters.
- Does not directly stop component rendering.

## useReducer

- Manages complex state.
- Uses reducer and dispatch.
- Keeps update logic centralized.

## useLayoutEffect

- Runs after DOM commit and before repaint.
- Useful for DOM measurements.
- Can block painting.

## useImperativeHandle

- Customizes the value exposed through a ref.
- Useful for exposing specific child methods.

## useId

- Generates accessibility-related IDs.
- Should not be used for list keys.

## useTransition

- Marks updates as non-urgent.
- Returns `isPending` and `startTransition`.

## useDeferredValue

- Defers a value.
- Useful for expensive UI rendering.
- Does not debounce network requests.

## useSyncExternalStore

- Subscribes to external stores.
- Provides consistent snapshots.

## useInsertionEffect

- Primarily for CSS-in-JS libraries.
- Runs before layout Effects.

## useDebugValue

- Helps debug Custom Hooks.

## useActionState

- Manages Action results.
- Provides pending status.

## useOptimistic

- Displays temporary optimistic state.
- Useful for responsive server interactions.

## useFormStatus

- Reads the nearest parent form's status.
- Used inside a descendant of the form.

## useEffectEvent

- Separates non-reactive logic from Effects.
- Reads the latest props and state when called.

## use()

- Reads resources such as Promises and Context.
- Can be called conditionally and inside loops.
- Is not a regular Hook.

---

# Section 15: Interview Preparation Checklist

## React Hooks Fundamentals

- [ ] Explain what Hooks are.
- [ ] Explain why Hooks were introduced.
- [ ] Understand the Rules of Hooks.
- [ ] Understand Hook call order.
- [ ] Differentiate Hooks and lifecycle methods.

## State Management

- [ ] Master useState.
- [ ] Understand functional state updates.
- [ ] Understand batching.
- [ ] Understand lazy initialization.
- [ ] Practice updating arrays and objects.
- [ ] Understand stale closures.
- [ ] Master useReducer.

## Effects

- [ ] Master all three useEffect syntaxes.
- [ ] Understand dependency arrays.
- [ ] Understand cleanup functions.
- [ ] Understand Effect execution order.
- [ ] Practice API calls.
- [ ] Practice AbortController.
- [ ] Understand race conditions.
- [ ] Understand Strict Mode.
- [ ] Understand exhaustive-deps.
- [ ] Know when not to use useEffect.

## Other Built-in Hooks

- [ ] useContext.
- [ ] useRef.
- [ ] useMemo.
- [ ] useCallback.
- [ ] useLayoutEffect.
- [ ] useImperativeHandle.
- [ ] useId.
- [ ] useTransition.
- [ ] useDeferredValue.
- [ ] useSyncExternalStore.
- [ ] useInsertionEffect.
- [ ] useDebugValue.

## Custom Hooks

- [ ] Create useToggle.
- [ ] Create useFetch.
- [ ] Create useDebounce.
- [ ] Create useLocalStorage.
- [ ] Create usePrevious.
- [ ] Create useWindowSize.
- [ ] Understand Custom Hook state isolation.

## React 19 and Newer APIs

- [ ] useActionState.
- [ ] useOptimistic.
- [ ] useFormStatus.
- [ ] useEffectEvent.
- [ ] Understand the use() API.
- [ ] Understand Actions and asynchronous form submissions.

## Interview Practice

- [ ] Explain each Hook without memorizing.
- [ ] Write important Hooks from scratch.
- [ ] Solve all output-based questions.
- [ ] Explain real-world scenarios.
- [ ] Understand every comparison table.
- [ ] Complete the coding practice exercises.

---

# Final Interview Advice

When an interviewer asks about any React Hook, structure your answer like this:

1. **Definition:** What is the Hook?
2. **Purpose:** Why do we use it?
3. **Syntax:** How do we write it?
4. **Parameters:** What arguments does it accept?
5. **Return Value:** What does it return?
6. **Working:** How does it work?
7. **Example:** Show a practical code example.
8. **Use Case:** Where would you use it in a real project?
9. **Limitations:** What mistakes should be avoided?
10. **Follow-up:** How is it different from similar Hooks?

**Important:** Understanding the difference between `useEffect`, `useLayoutEffect`, `useMemo`, `useCallback`, `useRef` and `useState` is especially important for technical interviews.

---

# End of Chapter 2

**Next Chapter: Chapter 3 – React Routing**

Upcoming topics:

- React Router fundamentals.
- BrowserRouter, Routes and Route.
- createBrowserRouter.
- useNavigate and useParams.
- useLocation and useSearchParams.
- Nested Routes and Outlet.
- Protected Routes.
- Public and Private Routes.
- Dynamic Routing.
- Authentication-based navigation.
- Role-based routing.
- Loaders and Actions.
- Error handling.
- React Router v6 vs v7.
- Practical routing scenarios.
- Tricky routing interview questions.

**Preparation Goal:** After completing this chapter, you should be able to explain every major React Hook, implement it independently, understand its execution behavior and answer advanced follow-up questions in an interview.