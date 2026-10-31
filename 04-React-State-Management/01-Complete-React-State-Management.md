# Chapter 4 -- React State Management: Complete Concepts

> A structured learning guide covering state from the fundamentals
> through Context API, reducers, Redux Toolkit, Zustand, server state,
> and advanced patterns.

## Table of Contents

-   [1. Introduction to State
    Management](#1-introduction-to-state-management)
-   [2. What Is State in React?](#2-what-is-state-in-react)
-   [3. Types of State](#3-types-of-state)
-   [4. The `useState` Hook](#4-the-usestate-hook)
-   [5. Updating State Correctly](#5-updating-state-correctly)
-   [6. State vs Props](#6-state-vs-props)
-   [7. Sharing State Between
    Components](#7-sharing-state-between-components)
-   [8. Lifting State Up](#8-lifting-state-up)
-   [9. Prop Drilling](#9-prop-drilling)
-   [10. Context API](#10-context-api)
-   [11. Context API with `useReducer`](#11-context-api-with-usereducer)
-   [12. Understanding Reducers](#12-understanding-reducers)
-   [13. When to Use a State Management
    Library](#13-when-to-use-a-state-management-library)
-   [14. Redux Fundamentals](#14-redux-fundamentals)
-   [15. Redux Toolkit](#15-redux-toolkit)
-   [16. `configureStore` and
    `createSlice`](#16-configurestore-and-createslice)
-   [17. `useSelector` and
    `useDispatch`](#17-useselector-and-usedispatch)
-   [18. Async Logic with
    `createAsyncThunk`](#18-async-logic-with-createasyncthunk)
-   [19. RTK Query](#19-rtk-query)
-   [20. Zustand](#20-zustand)
-   [21. Context API vs Redux Toolkit vs
    Zustand](#21-context-api-vs-redux-toolkit-vs-zustand)
-   [22. Local Storage and State
    Persistence](#22-local-storage-and-state-persistence)
-   [23. Server State vs Client State](#23-server-state-vs-client-state)
-   [24. State Management Performance](#24-state-management-performance)
-   [25. Advanced State Management
    Patterns](#25-advanced-state-management-patterns)
-   [26. Common Mistakes](#26-common-mistakes)
-   [27. Best Practices](#27-best-practices)
-   [28. Practical Projects](#28-practical-projects)
-   [29. Recommended Learning Order](#29-recommended-learning-order)

------------------------------------------------------------------------

## 1. Introduction to State Management

State management is the process of storing, updating, and sharing data
that affects an application's user interface.

Examples include: - A counter value. - Whether a modal is open. - The
currently logged-in user. - Products in a shopping cart. - Selected
filters on a product listing page. - Data received from an API.

React provides built-in tools such as `useState`, `useReducer`, and
Context API. Larger applications may also use libraries such as Redux
Toolkit or Zustand.

### Why do we need state management?

A React application is made of components. As an application grows,
components may need to share information. State management gives us a
predictable way to update that information and keep the UI in sync.

### Basic flow

1.  The user performs an action.
2.  An event handler requests a state update.
3.  React processes the update.
4.  React renders the component with the new state.
5.  The UI reflects the latest state.

**Important:** State management is not only about global state. Most
state should remain local unless multiple components genuinely need to
share it.

## 2. What Is State in React?

State is data that a component remembers between renders. Unlike an
ordinary local variable, updating state tells React that the component
may need to render again.

``` jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

In this example: - `count` is the current state value. - `setCount` is
the setter function. - `0` is the initial state. - `useState` is a React
Hook.

### State is a snapshot

Each render receives a snapshot of state. Calling a setter does not
change the value inside the already-running render. It schedules an
update for a subsequent render.

``` jsx
function handleClick() {
  setCount(count + 1);
  console.log(count); // The value from the current render
}
```

Do not expect the `console.log` immediately after the setter to show the
updated value.

## 3. Types of State

These are useful categories for deciding where data should live. They
are architectural categories, not separate React APIs.

  -----------------------------------------------------------------------
  Type                    Meaning                 Example
  ----------------------- ----------------------- -----------------------
  Local/UI state          Used by one component   Modal open/closed
                          or a small component    
                          area                    

  Shared state            Needed by sibling or    Selected product
                          related components      

  Global/client state     Needed across distant   Authenticated user,
                          parts of the app        cart

  Server state            Data owned by a server  Products, orders
                          and fetched by the app  

  URL state               State represented in    Search term, page
                          the URL                 number

  Form state              Values, errors, touched Registration form
                          and submission status   
  -----------------------------------------------------------------------

### Choosing a location

-   Keep state local when only one component needs it.
-   Lift state to the closest common parent when siblings need it.
-   Use Context or a state library when many distant components need the
    same client state.
-   Treat API data as server state; consider a data-fetching/cache
    solution.
-   Put shareable navigation state in the URL when appropriate.

## 4. The `useState` Hook

`useState` lets a function component add a state variable.

### Syntax

``` jsx
const [state, setState] = useState(initialValue);
```

### Example: string state

``` jsx
import { useState } from "react";

function NameEditor() {
  const [name, setName] = useState("Nikitha");

  return (
    <>
      <p>Hello, {name}</p>
      <input
        value={name}
        onChange={(event) => setName(event.target.value)}
      />
    </>
  );
}
```

### Example: boolean state

``` jsx
function Toggle() {
  const [isVisible, setIsVisible] = useState(false);

  return (
    <>
      <button onClick={() => setIsVisible((current) => !current)}>
        Toggle
      </button>
      {isVisible && <p>Now you can see this message.</p>}
    </>
  );
}
```

### Example: object state

``` jsx
function ProfileForm() {
  const [profile, setProfile] = useState({
    name: "",
    email: ""
  });

  function updateName(event) {
    setProfile((previous) => ({
      ...previous,
      name: event.target.value
    }));
  }

  return (
    <input value={profile.name} onChange={updateName} />
  );
}
```

When updating an object, create a new object. The spread operator copies
the existing top-level properties; it does not perform a deep clone.

### Example: array state

``` jsx
function TodoList() {
  const [todos, setTodos] = useState([]);

  function addTodo() {
    setTodos((previous) => [
      ...previous,
      { id: crypto.randomUUID(), text: "Learn state" }
    ]);
  }

  function removeTodo(id) {
    setTodos((previous) =>
      previous.filter((todo) => todo.id !== id)
    );
  }

  return (
    <>
      <button onClick={addTodo}>Add todo</button>
      {todos.map((todo) => (
        <p key={todo.id}>
          {todo.text}
          <button onClick={() => removeTodo(todo.id)}>Remove</button>
        </p>
      ))}
    </>
  );
}
```

### Lazy initial state

If calculating the initial state is expensive, pass an initializer
function:

``` jsx
const [items, setItems] = useState(() => {
  return JSON.parse(localStorage.getItem("items") || "[]");
});
```

React calls the initializer to obtain the initial value. Initializers
should be pure.

## 5. Updating State Correctly

### Never mutate state directly

Incorrect:

``` jsx
user.name = "Sam";
setUser(user);
```

Correct:

``` jsx
setUser((previous) => ({
  ...previous,
  name: "Sam"
}));
```

### Use functional updates when the next value depends on the previous value

``` jsx
setCount((previousCount) => previousCount + 1);
```

This is especially important when scheduling multiple updates:

``` jsx
setCount((value) => value + 1);
setCount((value) => value + 1);
setCount((value) => value + 1);
```

Each updater receives the pending state produced by the previous
updater.

### Updating nested objects

``` jsx
const [user, setUser] = useState({
  name: "Asha",
  address: {
    city: "Bengaluru",
    pin: "560001"
  }
});

function updateCity() {
  setUser((previous) => ({
    ...previous,
    address: {
      ...previous.address,
      city: "Mysuru"
    }
  }));
}
```

Copy each level that changes.

### State updates are scheduled

React may batch multiple updates. Do not use state setters as if they
were synchronous assignments.

## 6. State vs Props

  -----------------------------------------------------------------------
  State                               Props
  ----------------------------------- -----------------------------------
  Managed by a component or state     Passed from parent to child
  owner                               

  Updated using a setter/reducer      Child should not directly modify
                                      parent props

  Can change over time                Read-only from the receiving
                                      component's perspective

  Used for data a component needs to  Used to configure or provide data
  remember                            to a component
  -----------------------------------------------------------------------

``` jsx
function Parent() {
  const [name, setName] = useState("Asha");

  return <Child name={name} />;
}

function Child({ name }) {
  return <h2>Hello, {name}</h2>;
}
```

Here, `name` is state in `Parent` and a prop in `Child`.

## 7. Sharing State Between Components

If two components need the same information, they should usually read it
from one shared owner rather than maintaining unrelated duplicate
copies.

For sibling components, the common parent can own the state and pass: -
The current value to components that display it. - A callback to
components that need to request a change.

This leads to the lifting state up pattern.

## 8. Lifting State Up

Lifting state up means moving state from child components into their
closest common parent so multiple children can share and coordinate it.

### Example

``` jsx
import { useState } from "react";

function TemperatureInput({ temperature, onTemperatureChange }) {
  return (
    <input
      value={temperature}
      onChange={(event) => onTemperatureChange(event.target.value)}
    />
  );
}

function TemperatureApp() {
  const [temperature, setTemperature] = useState("");

  return (
    <>
      <TemperatureInput
        temperature={temperature}
        onTemperatureChange={setTemperature}
      />
      <p>Current temperature: {temperature}</p>
    </>
  );
}
```

### When should you lift state up?

-   Sibling components need the same value.
-   Components must stay synchronized.
-   One component updates data displayed by another.

### Avoid lifting everything

Keep state as close as practical to where it is used. Moving every state
variable to the root creates unnecessary coupling and can make the app
harder to maintain.

## 9. Prop Drilling

Prop drilling happens when data is passed through multiple intermediate
components that do not need the data themselves, only to deliver it to a
deeply nested component.

``` jsx
function App() {
  const user = { name: "Asha" };
  return <Dashboard user={user} />;
}

function Dashboard({ user }) {
  return <Profile user={user} />;
}

function Profile({ user }) {
  return <UserInfo user={user} />;
}

function UserInfo({ user }) {
  return <h2>{user.name}</h2>;
}
```

### Problems

-   Intermediate components receive props they do not use.
-   Adding or changing data may require editing many component
    signatures.
-   Component trees become more tightly coupled.

### Ways to address it

-   Component composition: pass a component or JSX as `children`.
-   Lift state only to the necessary common owner.
-   Context API for values needed by many descendants.
-   Redux Toolkit or Zustand for suitable application-wide client state.

Prop drilling is not automatically bad. Passing props through one or two
levels is often simple and clear.

## 10. Context API

Context lets a parent make a value available to descendant components
without manually passing props through every intermediate level.

### Step 1: Create a context

``` jsx
import { createContext } from "react";

export const ThemeContext = createContext(null);
```

### Step 2: Provide a value

``` jsx
import { useState } from "react";
import { ThemeContext } from "./ThemeContext";

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const value = { theme, setTheme };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}
```

### Step 3: Consume the context

``` jsx
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

function ThemeButton() {
  const context = useContext(ThemeContext);

  if (!context) {
    throw new Error("ThemeButton must be used within ThemeProvider");
  }

  const { theme, setTheme } = context;

  return (
    <button
      onClick={() =>
        setTheme((current) => current === "light" ? "dark" : "light")
      }
    >
      Current theme: {theme}
    </button>
  );
}
```

### Wrap the application

``` jsx
import { createRoot } from "react-dom/client";
import App from "./App";
import { ThemeProvider } from "./ThemeProvider";

createRoot(document.getElementById("root")).render(
  <ThemeProvider>
    <App />
  </ThemeProvider>
);
```

### `createContext` vs `useContext`

-   `createContext(defaultValue)` creates a context object.
-   `useContext(Context)` reads the nearest matching provider value
    above the calling component.

The default value is used only when there is no matching provider above
the consumer.

### Context re-render behavior

When a provider receives a different `value` (compared using
`Object.is`), consumers of that context are updated. A newly created
object value on every provider render can therefore cause avoidable
context updates.

For example:

``` jsx
const value = useMemo(
  () => ({ theme, setTheme }),
  [theme]
);
```

Memoization can help when appropriate, but it does not replace good
state placement or component design.

### Context limitations

-   Context is not automatically a complete state-management solution.
-   A changing context value can update all consumers of that context.
-   Large unrelated values in one context can create unnecessary
    updates.
-   Context does not itself provide server-state caching, request
    deduplication, or Redux-style developer tooling.

## 11. Context API with `useReducer`

`useReducer` is useful when state transitions are more complex or
several actions update the same state.

A reducer receives the current state and an action, then returns the
next state.

``` jsx
function cartReducer(state, action) {
  switch (action.type) {
    case "itemAdded":
      return [...state, action.item];

    case "itemRemoved":
      return state.filter((item) => item.id !== action.id);

    case "cartCleared":
      return [];

    default:
      return state;
  }
}
```

Use it with Context to share both state and dispatch:

``` jsx
import { createContext, useContext, useReducer } from "react";

const CartStateContext = createContext(null);
const CartDispatchContext = createContext(null);

function CartProvider({ children }) {
  const [cart, dispatch] = useReducer(cartReducer, []);

  return (
    <CartStateContext.Provider value={cart}>
      <CartDispatchContext.Provider value={dispatch}>
        {children}
      </CartDispatchContext.Provider>
    </CartStateContext.Provider>
  );
}

function useCart() {
  const context = useContext(CartStateContext);
  if (context === null) {
    throw new Error("useCart must be used within CartProvider");
  }
  return context;
}

function useCartDispatch() {
  const context = useContext(CartDispatchContext);
  if (context === null) {
    throw new Error("useCartDispatch must be used within CartProvider");
  }
  return context;
}
```

This pattern separates reading state from dispatching actions. It is
still important to consider how often context consumers re-render.

## 12. Understanding Reducers

A reducer is a pure function that calculates the next state from the
current state and an action.

``` jsx
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
```

Use it with `useReducer`:

``` jsx
import { useReducer } from "react";

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
    </>
  );
}
```

### Important reducer rules

-   Keep reducers pure.
-   Do not mutate existing state.
-   Do not perform API calls or other side effects inside a reducer.
-   Return the next state.
-   Handle actions consistently; returning the current state for an
    unknown action is a common approach.

## 13. When to Use a State Management Library

Consider a library when: - Many unrelated components need the same
client state. - State transitions are complex. - You need predictable
centralized updates or debugging tools. - The app needs established
patterns for async workflows. - Context and local state have become
difficult to maintain.

Do not add a library just because an application has multiple
components. Built-in React state is sufficient for many applications.

## 14. Redux Fundamentals

Redux is a predictable state container commonly used to manage shared
application state. Redux Toolkit is the recommended way to write Redux
logic in modern Redux applications.

### Core concepts

-   **Store:** Holds the application state.
-   **Action:** Describes what happened.
-   **Reducer:** Calculates the next state.
-   **Dispatch:** Sends an action to the store.
-   **Selector:** Reads data from the store.

### Typical data flow

1.  A component dispatches an action.
2.  Redux sends the action to the reducer.
3.  The reducer calculates the next state.
4.  The store saves the new state.
5.  Subscribed components select the data they need and update as
    required.

### Redux principles

1.  A single source of truth for the Redux store.
2.  State is read-only; updates are requested by dispatching actions.
3.  Changes are made with pure reducer logic.

## 15. Redux Toolkit

Redux Toolkit (RTK) is the official, recommended approach for writing
Redux logic. It reduces boilerplate and includes useful defaults and
utilities.

Install:

``` bash
npm install @reduxjs/toolkit react-redux
```

Main APIs: - `configureStore` - `createSlice` - `createAsyncThunk` -
`createApi` and `fetchBaseQuery` for RTK Query

## 16. `configureStore` and `createSlice`

### Create a slice

A slice groups a feature's name, initial state, reducers, and generated
actions.

``` jsx
// features/counter/counterSlice.js
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment(state) {
      state.value += 1;
    },
    decrement(state) {
      state.value -= 1;
    },
    incrementByAmount(state, action) {
      state.value += action.payload;
    },
    reset(state) {
      state.value = 0;
    }
  }
});

export const {
  increment,
  decrement,
  incrementByAmount,
  reset
} = counterSlice.actions;

export default counterSlice.reducer;
```

The apparent mutation syntax is safe here because Redux Toolkit uses
Immer internally to produce immutable updates. Do not assume the same
mutation style is safe in ordinary React state or a hand-written
reducer.

### Configure the store

``` jsx
// app/store.js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "../features/counter/counterSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer
  }
});
```

### Provide the store to React

``` jsx
// main.jsx
import { Provider } from "react-redux";
import { createRoot } from "react-dom/client";
import { store } from "./app/store";
import App from "./App";

createRoot(document.getElementById("root")).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

## 17. `useSelector` and `useDispatch`

React Redux provides hooks for interacting with the store.

-   `useSelector` reads selected data from the Redux store.
-   `useDispatch` returns the store's dispatch function.

``` jsx
import { useDispatch, useSelector } from "react-redux";
import { decrement, increment } from "./counterSlice";

function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <>
      <p>{count}</p>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(increment())}>+</button>
    </>
  );
}
```

Selectors should return the smallest useful piece of state. Avoid
creating a new object or array on every selector call unless you use an
appropriate memoized selector or equality strategy.

## 18. Async Logic with `createAsyncThunk`

`createAsyncThunk` generates a thunk action creator that handles the
lifecycle of an asynchronous operation. It creates `pending`,
`fulfilled`, and `rejected` action types.

``` jsx
import { createAsyncThunk, createSlice } from "@reduxjs/toolkit";

export const fetchUsers = createAsyncThunk(
  "users/fetchUsers",
  async (_, thunkAPI) => {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");

    if (!response.ok) {
      return thunkAPI.rejectWithValue("Failed to fetch users");
    }

    return response.json();
  }
);

const usersSlice = createSlice({
  name: "users",
  initialState: {
    data: [],
    status: "idle",
    error: null
  },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => {
        state.status = "loading";
        state.error = null;
      })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.status = "succeeded";
        state.data = action.payload;
      })
      .addCase(fetchUsers.rejected, (state, action) => {
        state.status = "failed";
        state.error = action.payload || action.error.message;
      });
  }
});

export default usersSlice.reducer;
```

Dispatch it from a component:

``` jsx
const dispatch = useDispatch();

useEffect(() => {
  dispatch(fetchUsers());
}, [dispatch]);
```

Render loading, success, and error states separately. In a real
application, also consider cancellation, duplicate requests, caching,
and whether RTK Query is a better fit.

## 19. RTK Query

RTK Query is a data-fetching and caching tool included in Redux Toolkit.
It helps manage server data, including request status and cached
results.

### Define an API service

``` jsx
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

export const usersApi = createApi({
  reducerPath: "usersApi",
  baseQuery: fetchBaseQuery({
    baseUrl: "https://jsonplaceholder.typicode.com/"
  }),
  endpoints: (builder) => ({
    getUsers: builder.query({
      query: () => "users"
    })
  })
});

export const { useGetUsersQuery } = usersApi;
```

### Add the API reducer and middleware

``` jsx
import { configureStore } from "@reduxjs/toolkit";
import { usersApi } from "./usersApi";

export const store = configureStore({
  reducer: {
    [usersApi.reducerPath]: usersApi.reducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(usersApi.middleware)
});
```

### Use the generated hook

``` jsx
function Users() {
  const {
    data: users,
    isLoading,
    isError,
    error
  } = useGetUsersQuery();

  if (isLoading) return <p>Loading...</p>;
  if (isError) return <p>Error: {String(error)}</p>;

  return users.map((user) => (
    <p key={user.id}>{user.name}</p>
  ));
}
```

RTK Query can handle caching, request status, subscriptions, and cache
invalidation. It is designed for server data, not as a replacement for
every kind of local UI state.

## 20. Zustand

Zustand is a small state-management library with a hook-based API. It
can be useful when shared state is needed without the structure of a
larger Redux setup.

Install:

``` bash
npm install zustand
```

### Create a store

``` jsx
import { create } from "zustand";

const useCounterStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 })
}));

export default useCounterStore;
```

### Use the store in a component

``` jsx
import useCounterStore from "./counterStore";

function Counter() {
  const count = useCounterStore((state) => state.count);
  const increment = useCounterStore((state) => state.increment);

  return (
    <>
      <p>{count}</p>
      <button onClick={increment}>Increment</button>
    </>
  );
}
```

Zustand components can select individual pieces of state. Selecting only
what a component needs helps avoid unnecessary updates.

## 21. Context API vs Redux Toolkit vs Zustand

  -----------------------------------------------------------------------
  Feature           Context API       Redux Toolkit     Zustand
  ----------------- ----------------- ----------------- -----------------
  Included with     Yes               No                No
  React                                                 

  Additional        No                Yes               Yes
  installation                                          

  Typical use       Share values      Structured        Lightweight
                    through a tree    app-wide state    shared client
                                      and workflows     state

  Boilerplate       Low to moderate   Moderate, with    Low
                                      RTK reducing it   

  Async/server data Needs additional  Thunks or RTK     Needs additional
                    patterns          Query             patterns or
                                                        integrations

  DevTools          React DevTools    Redux DevTools    DevTools
                                      support           middleware
                                                        available

  Best fit          Theme, locale,    Complex shared    Small-to-medium
                    simple auth       state and         shared state with
                    context           predictable       a simple API
                                      workflows         
  -----------------------------------------------------------------------

There is no universally best choice. Decide based on application
requirements, team familiarity, debugging needs, and the nature of the
data.

## 22. Local Storage and State Persistence

React state normally resets when the page is refreshed. Persistence
means saving selected state outside the component lifecycle so it can be
restored later.

### Basic `localStorage` example

``` jsx
import { useEffect, useState } from "react";

function Preferences() {
  const [theme, setTheme] = useState(() => {
    return localStorage.getItem("theme") || "light";
  });

  useEffect(() => {
    localStorage.setItem("theme", theme);
  }, [theme]);

  return (
    <button onClick={() =>
      setTheme((current) => current === "light" ? "dark" : "light")
    }>
      Theme: {theme}
    </button>
  );
}
```

### Storage choices

-   `localStorage`: persists across browser sessions until cleared.
-   `sessionStorage`: lasts for the page session/tab.
-   Cookies: can be sent with HTTP requests and support server-side
    settings such as `HttpOnly`, `Secure`, and `SameSite`.

Do not store sensitive credentials or secrets in browser storage without
carefully evaluating the security implications. For authentication,
follow the backend and application security design.

## 23. Server State vs Client State

  -----------------------------------------------------------------------
  Client state                        Server state
  ----------------------------------- -----------------------------------
  Owned by the UI/application         Owned by a remote server

  Example: open modal                 Example: list of orders

  Updated by user interaction or app  Fetched and changed through network
  logic                               requests

  Often managed with React state,     Often managed with a fetching/cache
  Context, Redux, Zustand             tool such as RTK Query
  -----------------------------------------------------------------------

Server state has additional concerns: - Loading and error states. -
Caching and stale data. - Refetching and invalidation. - Network
failures. - Concurrent requests. - Pagination and synchronization.

Avoid copying every API response into a separate global store without a
clear reason.

## 24. State Management Performance

### Keep state close to where it is used

A local state update usually affects a smaller component area than a
broadly shared state update.

### Split unrelated contexts

Avoid putting unrelated high-frequency values into one large context.
Separate contexts by responsibility when it improves update behavior and
maintainability.

### Select only the data needed

With Redux or Zustand, select the smallest relevant value instead of
subscribing to the entire store.

### Use memoization when it solves a measured problem

-   `React.memo` can skip rendering a component when its props have not
    changed.
-   `useMemo` can cache a calculation or value between renders.
-   `useCallback` can cache a function reference between renders.

Memoization is not automatically beneficial. Profile first and keep
dependencies correct.

### Normalize relational data when appropriate

For complex collections, storing entities by ID and keeping ordered ID
lists can simplify updates and reduce duplication.

## 25. Advanced State Management Patterns

### 25.1 Reducer pattern

Represent state transitions as named actions handled by a reducer.
Useful when many related events update one state object.

### 25.2 State normalization

Avoid deeply nested duplicated entities. Store entities by ID and refer
to them by ID where appropriate.

### 25.3 Derived state

Calculate values from existing state instead of storing duplicate
values.

``` jsx
const completedTodos = todos.filter((todo) => todo.completed);
```

If a value can be calculated during rendering cheaply, it usually does
not need its own state variable.

### 25.4 State machines

For workflows with distinct states and transitions, a state-machine
model can make valid transitions explicit. Example:
`idle → loading → success` or `idle → loading → error`.

### 25.5 Optimistic updates

An optimistic update changes the UI before the server confirms the
operation, then reconciles or rolls back if the request fails. Use it
when the interaction benefits from immediate feedback and failure
handling is designed carefully.

### 25.6 Undo and redo

Maintain past, present, and future snapshots or action history. This is
useful in editors and complex forms, but it requires deliberate history
and memory management.

### 25.7 URL-driven state

Use path parameters and query parameters for state that should be
shareable, bookmarkable, or navigable with browser history, such as
search terms, filters, and pagination.

## 26. Common Mistakes

1.  **Mutating state directly:** Create new objects and arrays instead.
2.  **Using state for values that can be calculated:** Derive values
    during render when practical.
3.  **Lifting all state to the root:** Keep state close to its
    consumers.
4.  **Using Context for every value:** Context is useful for shared
    values, but it is not always the right global store.
5.  **Putting unrelated data into one context:** Split by responsibility
    when needed.
6.  **Ignoring loading and error states:** Async UI should handle
    pending, success, and failure.
7.  **Putting side effects in reducers:** Keep reducers pure; perform
    effects in event handlers, thunks, or appropriate data-fetching
    tools.
8.  **Selecting the entire Redux store:** Select only what a component
    needs.
9.  **Persisting sensitive data carelessly:** Understand browser storage
    security.
10. **Adding a library too early:** Start with built-in React state and
    introduce tools when the requirements justify them.

## 27. Best Practices

-   Start with local component state.
-   Lift state to the closest common parent when necessary.
-   Use Context for values needed by many descendants.
-   Use `useReducer` for complex transitions.
-   Use Redux Toolkit when structured, centralized client state is
    justified.
-   Consider RTK Query for API data when using Redux Toolkit.
-   Consider Zustand when a lightweight shared store fits the project.
-   Keep reducers pure and state updates immutable.
-   Derive values instead of duplicating state.
-   Keep server state and client state conceptually separate.
-   Handle loading, error, empty, and success UI states.
-   Add persistence only for data that needs to survive reloads.
-   Choose tools based on requirements rather than popularity.

## 28. Practical Projects

Build these in increasing order of complexity.

### Project 1: Counter App -- `useState`

Requirements: - Increment, decrement, and reset. - Prevent values below
zero. - Add a step-size input.

### Project 2: Todo App -- Arrays and Objects

Requirements: - Add, edit, delete, and complete todos. - Filter all,
active, and completed. - Derive the remaining-task count.

### Project 3: Theme Switcher -- Context API

Requirements: - Light and dark themes. - Share theme across Navbar,
Home, and Settings. - Persist the selected theme.

### Project 4: Shopping Cart -- Context API + `useReducer`

Requirements: - Add and remove products. - Increase and decrease
quantity. - Calculate subtotal and total. - Keep cart state shared
across product and cart pages.

### Project 5: User Dashboard -- Redux Toolkit

Requirements: - Configure the Redux store. - Create authentication or
user slices. - Dispatch actions and select state. - Implement loading
and error states for an async operation.

### Project 6: API Dashboard -- RTK Query

Requirements: - Fetch users or products. - Display loading, error, and
success states. - Add a detail view. - Implement a mutation and cache
invalidation.

### Project 7: Global Wishlist -- Zustand

Requirements: - Add and remove products. - Share wishlist state across
pages. - Select only the state each component needs.

## 29. Recommended Learning Order

Follow this sequence instead of trying to learn every library at once:

1.  Understand state and re-rendering.
2.  Master `useState` with strings, numbers, booleans, objects, and
    arrays.
3.  Learn immutable updates and functional updates.
4.  Understand props and state.
5.  Practise sharing state and lifting state up.
6.  Understand prop drilling and component composition.
7.  Learn Context API.
8.  Learn `useReducer`.
9.  Understand Redux concepts.
10. Learn Redux Toolkit: store, slices, actions, selectors, and
    dispatch.
11. Learn async logic with `createAsyncThunk`.
12. Learn RTK Query for server data.
13. Learn Zustand.
14. Compare the tools and build practical projects.
15. Study advanced patterns, performance, and best practices.

------------------------------------------------------------------------

**Chapter 4 concept guide complete.** The separate interview-question
file should be studied after the concepts, and should cover beginner,
intermediate, advanced, practical, scenario-based, output-based, and
tricky questions.
