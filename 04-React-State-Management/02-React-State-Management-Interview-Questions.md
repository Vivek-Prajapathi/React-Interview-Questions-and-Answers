# Chapter 4 -- React State Management: Interview Questions and Answers

> Comprehensive interview preparation from fundamentals to advanced
> state-management concepts. Questions are grouped by topic and
> difficulty.

## Table of Contents

-   [A. State Management Fundamentals](#a-state-management-fundamentals)
    -   [Q1. What is state in React?](#q1-what-is-state-in-react)
    -   [Q2. What is state management?](#q2-what-is-state-management)
    -   [Q3. Why does React need state?](#q3-why-does-react-need-state)
    -   [Q4. What happens when state
        changes?](#q4-what-happens-when-state-changes)
    -   [Q5. What is the difference between state and
        props?](#q5-what-is-the-difference-between-state-and-props)
    -   [Q6. What are the different types of
        state?](#q6-what-are-the-different-types-of-state)
    -   [Q7. What is a state snapshot?](#q7-what-is-a-state-snapshot)
    -   [Q8. Does setting state immediately change its
        value?](#q8-does-setting-state-immediately-change-its-value)
-   [B. useState and State Updates](#b-usestate-and-state-updates)
    -   [Q9. What is useState?](#q9-what-is-usestate)
    -   [Q10. What does useState
        return?](#q10-what-does-usestate-return)
    -   [Q11. Can useState store different data
        types?](#q11-can-usestate-store-different-data-types)
    -   [Q12. How do you update an object in
        state?](#q12-how-do-you-update-an-object-in-state)
    -   [Q13. How do you update an array in
        state?](#q13-how-do-you-update-an-array-in-state)
    -   [Q14. Why should state not be mutated
        directly?](#q14-why-should-state-not-be-mutated-directly)
    -   [Q15. What is a functional state
        update?](#q15-what-is-a-functional-state-update)
    -   [Q16. When should you use a functional
        update?](#q16-when-should-you-use-a-functional-update)
    -   [Q17. What is lazy initialization in
        useState?](#q17-what-is-lazy-initialization-in-usestate)
    -   [Q18. Can state be updated multiple times in one
        event?](#q18-can-state-be-updated-multiple-times-in-one-event)
    -   [Q19. Why does logging state immediately after setting it show
        the old
        value?](#q19-why-does-logging-state-immediately-after-setting-it-show-the-old-value)
    -   [Q20. How do you avoid redundant
        state?](#q20-how-do-you-avoid-redundant-state)
-   [C. Sharing State and Prop
    Drilling](#c-sharing-state-and-prop-drilling)
    -   [Q21. What is lifting state up?](#q21-what-is-lifting-state-up)
    -   [Q22. When should state be lifted
        up?](#q22-when-should-state-be-lifted-up)
    -   [Q23. What is prop drilling?](#q23-what-is-prop-drilling)
    -   [Q24. Is prop drilling always
        bad?](#q24-is-prop-drilling-always-bad)
    -   [Q25. How can prop drilling be
        avoided?](#q25-how-can-prop-drilling-be-avoided)
    -   [Q26. What is component
        composition?](#q26-what-is-component-composition)
    -   [Q27. What is the single source of
        truth?](#q27-what-is-the-single-source-of-truth)
-   [D. Context API](#d-context-api)
    -   [Q28. What is Context API?](#q28-what-is-context-api)
    -   [Q29. What problem does Context
        solve?](#q29-what-problem-does-context-solve)
    -   [Q30. What is createContext?](#q30-what-is-createcontext)
    -   [Q31. What is useContext?](#q31-what-is-usecontext)
    -   [Q32. What is the purpose of a
        Provider?](#q32-what-is-the-purpose-of-a-provider)
    -   [Q33. What happens if a component is outside the
        Provider?](#q33-what-happens-if-a-component-is-outside-the-provider)
    -   [Q34. What happens when a Context value
        changes?](#q34-what-happens-when-a-context-value-changes)
    -   [Q35. Can Context replace
        Redux?](#q35-can-context-replace-redux)
    -   [Q36. What are the limitations of Context
        API?](#q36-what-are-the-limitations-of-context-api)
    -   [Q37. How can Context re-renders be
        optimized?](#q37-how-can-context-re-renders-be-optimized)
    -   [Q38. Why create a custom Context
        hook?](#q38-why-create-a-custom-context-hook)
-   [E. useReducer and Reducer
    Patterns](#e-usereducer-and-reducer-patterns)
    -   [Q39. What is useReducer?](#q39-what-is-usereducer)
    -   [Q40. What is a reducer
        function?](#q40-what-is-a-reducer-function)
    -   [Q41. What are state and action in a
        reducer?](#q41-what-are-state-and-action-in-a-reducer)
    -   [Q42. What is dispatch?](#q42-what-is-dispatch)
    -   [Q43. useState vs useReducer?](#q43-usestate-vs-usereducer)
    -   [Q44. Can useReducer be combined with
        Context?](#q44-can-usereducer-be-combined-with-context)
    -   [Q45. Why must reducers be
        pure?](#q45-why-must-reducers-be-pure)
-   [F. Redux Fundamentals](#f-redux-fundamentals)
    -   [Q46. What is Redux?](#q46-what-is-redux)
    -   [Q47. What problem does Redux
        solve?](#q47-what-problem-does-redux-solve)
    -   [Q48. What are the core principles of
        Redux?](#q48-what-are-the-core-principles-of-redux)
    -   [Q49. What is a Redux store?](#q49-what-is-a-redux-store)
    -   [Q50. What is an action?](#q50-what-is-an-action)
    -   [Q51. What is a reducer in
        Redux?](#q51-what-is-a-reducer-in-redux)
    -   [Q52. What is dispatch in
        Redux?](#q52-what-is-dispatch-in-redux)
    -   [Q53. What is a selector?](#q53-what-is-a-selector)
    -   [Q54. Explain the Redux data
        flow.](#q54-explain-the-redux-data-flow)
    -   [Q55. What is the difference between Redux and Context
        API?](#q55-what-is-the-difference-between-redux-and-context-api)
-   [G. Redux Toolkit](#g-redux-toolkit)
    -   [Q56. What is Redux Toolkit?](#q56-what-is-redux-toolkit)
    -   [Q57. Why is Redux Toolkit
        recommended?](#q57-why-is-redux-toolkit-recommended)
    -   [Q58. What is configureStore?](#q58-what-is-configurestore)
    -   [Q59. What is createSlice?](#q59-what-is-createslice)
    -   [Q60. What are reducers and generated actions in a
        slice?](#q60-what-are-reducers-and-generated-actions-in-a-slice)
    -   [Q61. Why does mutation-like code work in
        createSlice?](#q61-why-does-mutation-like-code-work-in-createslice)
    -   [Q62. What is Provider from
        react-redux?](#q62-what-is-provider-from-react-redux)
    -   [Q63. What is useSelector?](#q63-what-is-useselector)
    -   [Q64. What is useDispatch?](#q64-what-is-usedispatch)
    -   [Q65. What is the difference between useSelector and
        useDispatch?](#q65-what-is-the-difference-between-useselector-and-usedispatch)
    -   [Q66. What is createAsyncThunk?](#q66-what-is-createasyncthunk)
    -   [Q67. What are pending, fulfilled, and rejected
        actions?](#q67-what-are-pending-fulfilled-and-rejected-actions)
    -   [Q68. What is extraReducers?](#q68-what-is-extrareducers)
    -   [Q69. How do you handle API errors in Redux
        Toolkit?](#q69-how-do-you-handle-api-errors-in-redux-toolkit)
    -   [Q70. What is RTK Query?](#q70-what-is-rtk-query)
    -   [Q71. RTK Query vs
        createAsyncThunk?](#q71-rtk-query-vs-createasyncthunk)
-   [H. Zustand and Library
    Comparisons](#h-zustand-and-library-comparisons)
    -   [Q72. What is Zustand?](#q72-what-is-zustand)
    -   [Q73. How does Zustand work?](#q73-how-does-zustand-work)
    -   [Q74. How do you update Zustand
        state?](#q74-how-do-you-update-zustand-state)
    -   [Q75. Redux Toolkit vs Zustand?](#q75-redux-toolkit-vs-zustand)
    -   [Q76. When would you choose Context, Redux Toolkit, or
        Zustand?](#q76-when-would-you-choose-context-redux-toolkit-or-zustand)
-   [I. Persistence, Server State, and Advanced
    Concepts](#i-persistence-server-state-and-advanced-concepts)
    -   [Q77. Why does React state reset after a page
        refresh?](#q77-why-does-react-state-reset-after-a-page-refresh)
    -   [Q78. localStorage vs
        sessionStorage?](#q78-localstorage-vs-sessionstorage)
    -   [Q79. How can state be
        persisted?](#q79-how-can-state-be-persisted)
    -   [Q80. What is server state?](#q80-what-is-server-state)
    -   [Q81. Client state vs server
        state?](#q81-client-state-vs-server-state)
    -   [Q82. What is derived state?](#q82-what-is-derived-state)
    -   [Q83. What is state
        normalization?](#q83-what-is-state-normalization)
    -   [Q84. What is optimistic
        updating?](#q84-what-is-optimistic-updating)
    -   [Q85. What is a state machine?](#q85-what-is-a-state-machine)
    -   [Q86. How do you optimize state-management
        performance?](#q86-how-do-you-optimize-state-management-performance)
    -   [Q87. What is URL state?](#q87-what-is-url-state)
    -   [Q88. How would you implement undo and
        redo?](#q88-how-would-you-implement-undo-and-redo)
-   [J. Practical, Scenario-Based, and Tricky
    Questions](#j-practical-scenario-based-and-tricky-questions)
    -   [Q89. A counter must increase by three on one click. How would
        you implement
        it?](#q89-a-counter-must-increase-by-three-on-one-click-how-would-you-implement-it)
    -   [Q90. Two sibling components need the same state. What would you
        do?](#q90-two-sibling-components-need-the-same-state-what-would-you-do)
    -   [Q91. A deeply nested component needs the logged-in user. What
        options do you
        have?](#q91-a-deeply-nested-component-needs-the-logged-in-user-what-options-do-you-have)
    -   [Q92. Why is an object state not updating after changing one
        property?](#q92-why-is-an-object-state-not-updating-after-changing-one-property)
    -   [Q93. Why does a Context consumer
        re-render?](#q93-why-does-a-context-consumer-re-render)
    -   [Q94. How would you manage a shopping
        cart?](#q94-how-would-you-manage-a-shopping-cart)
    -   [Q95. How would you manage authentication
        state?](#q95-how-would-you-manage-authentication-state)
    -   [Q96. How would you manage API loading, success, and error
        states?](#q96-how-would-you-manage-api-loading-success-and-error-states)
    -   [Q97. Why should API calls not happen inside
        reducers?](#q97-why-should-api-calls-not-happen-inside-reducers)
    -   [Q98. Is Redux required for every React
        application?](#q98-is-redux-required-for-every-react-application)
    -   [Q99. What happens if a Redux selector returns a new object
        every
        time?](#q99-what-happens-if-a-redux-selector-returns-a-new-object-every-time)
    -   [Q100. How do you decide where state should
        live?](#q100-how-do-you-decide-where-state-should-live)

------------------------------------------------------------------------

# A. State Management Fundamentals

### Q1. What is state in React?

State is data that a component remembers between renders. When state is
updated, React schedules a render so the UI can reflect the new value.

### Q2. What is state management?

State management is the process of storing, updating, and sharing
application data in a predictable way so that components display the
correct UI.

### Q3. Why does React need state?

Ordinary local variables do not persist as component state between
renders, and changing them does not tell React to update the UI. State
provides persistent component data and a mechanism for requesting a
re-render.

### Q4. What happens when state changes?

React schedules an update, renders the component using the new state
snapshot, and commits the necessary UI changes. React may batch updates
rather than rendering after every setter call.

### Q5. What is the difference between state and props?

State is managed by its owner and updated through state mechanisms.
Props are inputs passed from a parent to a child. A component should
treat its props as read-only.

### Q6. What are the different types of state?

Common architectural categories are local/UI state, shared state, global
client state, server state, form state, and URL state. These categories
help decide where data should live; they are not separate React APIs.

### Q7. What is a state snapshot?

A state snapshot is the value of state for a particular render. Event
handlers created during that render see that render's state values, even
if a setter schedules a future update.

### Q8. Does setting state immediately change its value?

No. A setter schedules an update. The currently executing code continues
to see the state snapshot from its render. The updated value is
available in a subsequent render.

# B. useState and State Updates

### Q9. What is useState?

`useState` is a React Hook that lets a function component add a state
variable.

``` jsx
const [count, setCount] = useState(0);
```

### Q10. What does useState return?

It returns an array containing two values: 1. The current state value.
2. A setter function used to request an update.

Array destructuring is commonly used to read them.

### Q11. Can useState store different data types?

Yes. It can store numbers, strings, booleans, arrays, objects, and other
JavaScript values. Choose a state shape that makes updates clear and
predictable.

### Q12. How do you update an object in state?

Create a new object and copy the properties that should remain:

``` jsx
setUser((previous) => ({
  ...previous,
  name: "Asha"
}));
```

For nested objects, copy each changed level.

### Q13. How do you update an array in state?

Use methods that create a new array, such as `map`, `filter`, and spread
syntax.

``` jsx
setItems((previous) => [...previous, newItem]);
setItems((previous) => previous.filter((item) => item.id !== id));
```

### Q14. Why should state not be mutated directly?

React relies on state updates and identity comparisons to determine when
work is needed. Direct mutation can preserve the same object reference,
make updates harder to reason about, and cause bugs. Treat objects and
arrays in state as immutable.

### Q15. What is a functional state update?

It passes a function to the setter. React calls the updater with the
pending previous state and uses the returned value as the next state.

``` jsx
setCount((previous) => previous + 1);
```

### Q16. When should you use a functional update?

Use it whenever the next state depends on the previous state, especially
when multiple updates may be queued or when an event handler could
otherwise use a stale render snapshot.

### Q17. What is lazy initialization in useState?

Instead of passing the initial value directly, pass a function that
calculates it:

``` jsx
const [value, setValue] = useState(() => expensiveCalculation());
```

This avoids running the calculation on every render. The initializer
should be pure.

### Q18. Can state be updated multiple times in one event?

Yes. React can batch updates. If each update depends on the previous
result, use functional updates:

``` jsx
setCount((n) => n + 1);
setCount((n) => n + 1);
setCount((n) => n + 1);
```

### Q19. Why does logging state immediately after setting it show the old value?

The setter schedules a future render; it does not rewrite the state
variable in the current render's closure. Log the value during the next
render or use an effect when you need to respond to a committed state
change.

### Q20. How do you avoid redundant state?

Do not store values that can be calculated from existing props or state.
Derive them during rendering or memoize an expensive calculation when
needed.

# C. Sharing State and Prop Drilling

### Q21. What is lifting state up?

Lifting state up means moving state into the closest common parent of
components that need to share it. The parent passes the value and update
callbacks to its children.

### Q22. When should state be lifted up?

Lift state when sibling components need to read or update the same
information and must stay synchronized. Do not lift unrelated state
unnecessarily.

### Q23. What is prop drilling?

Prop drilling is passing props through intermediate components that do
not need the data, simply so a deeply nested component can receive it.

### Q24. Is prop drilling always bad?

No. Passing props through a small number of levels is often
straightforward and makes dependencies explicit. It becomes a
maintenance concern when many intermediate components must forward
unrelated props.

### Q25. How can prop drilling be avoided?

Options include component composition, lifting state to a suitable
owner, Context API, or a state-management library such as Redux Toolkit
or Zustand. Choose the simplest approach that fits the component tree.

### Q26. What is component composition?

Component composition means building larger UI structures by combining
smaller components and passing elements or components through props such
as `children`. It can reduce the need to pass data through every
intermediate component.

### Q27. What is the single source of truth?

It means keeping one authoritative owner for a particular piece of state
rather than maintaining multiple independent copies that can become
inconsistent.

# D. Context API

### Q28. What is Context API?

Context is a React feature that allows a component to provide a value to
descendant components without manually passing it through every
intermediate component.

### Q29. What problem does Context solve?

It helps avoid prop drilling when many components at different depths
need the same value, such as a theme, locale, or authenticated-user
information.

### Q30. What is createContext?

`createContext(defaultValue)` creates a context object. The default
value is used when a consumer has no matching provider above it.

### Q31. What is useContext?

`useContext(SomeContext)` reads the value from the nearest matching
provider above the component calling the Hook.

### Q32. What is the purpose of a Provider?

A provider supplies a context value to its descendant tree. Consumers
below it can read that value using `useContext`.

### Q33. What happens if a component is outside the Provider?

It receives the context's default value. If the default is `null`, the
component receives `null`; a custom hook can detect this and throw a
helpful error.

### Q34. What happens when a Context value changes?

Consumers of that context are updated when the provider value changes
according to `Object.is`. A newly created object value can therefore
trigger updates even if its contents look the same.

### Q35. Can Context replace Redux?

Context can handle many shared-state use cases, but it does not
automatically provide all Redux capabilities, such as a centralized
action/reducer workflow, Redux DevTools integration, middleware
ecosystem, and RTK Query. The right choice depends on complexity and
requirements.

### Q36. What are the limitations of Context API?

Context can cause broad consumer updates when values change. It does not
itself provide server-state caching, request lifecycle management, or a
complete debugging and middleware architecture.

### Q37. How can Context re-renders be optimized?

Split unrelated contexts, keep provider values stable when appropriate
with `useMemo`, keep state close to its consumers, and avoid placing
frequently changing unrelated data in one context. Measure performance
before adding complexity.

### Q38. Why create a custom Context hook?

A custom hook centralizes context access, improves readability, and can
check that the component is rendered inside the required provider.

# E. useReducer and Reducer Patterns

### Q39. What is useReducer?

`useReducer` is a React Hook for managing state through a reducer
function and dispatched actions. It is useful when state transitions are
more complex or action-driven.

### Q40. What is a reducer function?

A reducer is a function that receives the current state and an action
and returns the next state.

``` jsx
function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    default:
      return state;
  }
}
```

### Q41. What are state and action in a reducer?

-   `state` is the current state value.
-   `action` describes the event or requested change, usually with a
    `type` and optional `payload`.

### Q42. What is dispatch?

`dispatch(action)` sends an action to the reducer. React uses the
reducer's returned value as the next state.

### Q43. useState vs useReducer?

Use `useState` for straightforward independent values. Use `useReducer`
when many related updates, action types, or complex transitions make a
reducer easier to understand and test.

### Q44. Can useReducer be combined with Context?

Yes. A provider can call `useReducer` and share the state and dispatch
function through Context. This provides shared state with reducer-based
update logic.

### Q45. Why must reducers be pure?

A pure reducer returns the same result for the same inputs and does not
cause side effects. This makes state transitions predictable and easier
to test. API calls and other side effects belong outside reducers.

# F. Redux Fundamentals

### Q46. What is Redux?

Redux is a predictable state container used to manage shared application
state. In modern Redux applications, Redux Toolkit is the recommended
way to write Redux logic.

### Q47. What problem does Redux solve?

It provides a structured way to manage shared state, coordinate updates,
and inspect state transitions in applications where local state and
simple Context patterns are no longer sufficient.

### Q48. What are the core principles of Redux?

1.  A single source of truth in the store.
2.  State is read-only; changes are requested by dispatching actions.
3.  State changes are calculated by pure reducers.

### Q49. What is a Redux store?

The store holds the Redux state tree and provides methods such as
dispatch and subscription. In React applications, React Redux connects
components to the store.

### Q50. What is an action?

An action is a plain object that describes what happened. It commonly
contains a `type` property and may contain a `payload`.

### Q51. What is a reducer in Redux?

A reducer is a pure function that receives the current state and an
action and returns the next state.

### Q52. What is dispatch in Redux?

Dispatch sends an action to the Redux store so the reducer logic can
calculate the next state.

### Q53. What is a selector?

A selector is a function that reads or derives a value from the Redux
state. Components can use selectors to subscribe to the data they need.

### Q54. Explain the Redux data flow.

A component dispatches an action. The reducer processes the action and
calculates the next state. The store updates, and components subscribed
to selected state are notified and may re-render.

### Q55. What is the difference between Redux and Context API?

Context is a React mechanism for passing values through a component
tree. Redux is a state-management architecture with a store, actions,
reducers, and a tooling ecosystem. They solve related but not identical
problems.

# G. Redux Toolkit

### Q56. What is Redux Toolkit?

Redux Toolkit (RTK) is the official recommended toolset for writing
Redux logic. It provides utilities such as `configureStore`,
`createSlice`, `createAsyncThunk`, and RTK Query.

### Q57. Why is Redux Toolkit recommended?

It reduces repetitive code, provides sensible store defaults, simplifies
reducer and action creation, and includes utilities for async logic and
data fetching.

### Q58. What is configureStore?

`configureStore` creates a Redux store with useful defaults, including
reducer setup, middleware configuration, and development checks.

### Q59. What is createSlice?

`createSlice` creates a slice of Redux state using a name, initial
state, and reducer functions. It automatically generates action creators
and action types.

### Q60. What are reducers and generated actions in a slice?

Reducers define how a slice changes for particular actions. Redux
Toolkit generates corresponding action creators, which components can
dispatch.

### Q61. Why does mutation-like code work in createSlice?

Redux Toolkit uses Immer internally. It lets reducer code use a
mutation-like syntax while producing immutable state updates. This
behavior applies inside the relevant Redux Toolkit reducer logic, not
ordinary React state.

### Q62. What is Provider from react-redux?

`Provider` makes the Redux store available to React components beneath
it. It is usually placed near the application root.

### Q63. What is useSelector?

`useSelector` reads a selected value from the Redux store and subscribes
the component to changes in that selection.

### Q64. What is useDispatch?

`useDispatch` returns the store's dispatch function so a component can
dispatch actions or thunks.

### Q65. What is the difference between useSelector and useDispatch?

`useSelector` reads state from the store. `useDispatch` sends actions to
the store.

### Q66. What is createAsyncThunk?

`createAsyncThunk` creates a thunk action creator for an asynchronous
operation and generates lifecycle actions for pending, fulfilled, and
rejected outcomes.

### Q67. What are pending, fulfilled, and rejected actions?

-   `pending`: the async operation has started.
-   `fulfilled`: the operation completed successfully.
-   `rejected`: the operation failed or was rejected.

They are commonly handled in a slice's `extraReducers`.

### Q68. What is extraReducers?

`extraReducers` lets a slice respond to actions defined elsewhere,
including the lifecycle actions generated by `createAsyncThunk`.

### Q69. How do you handle API errors in Redux Toolkit?

Track a request status and error value in state. Handle the thunk's
rejected action in `extraReducers`. Use `rejectWithValue` when you want
to return a custom error payload that reducers can read from
`action.payload`.

### Q70. What is RTK Query?

RTK Query is a data-fetching and caching tool included in Redux Toolkit.
It generates hooks and manages request status, cached data,
subscriptions, and cache invalidation.

### Q71. RTK Query vs createAsyncThunk?

`createAsyncThunk` provides a general way to run async logic and handle
its lifecycle; you define how results are stored and managed. RTK Query
is purpose-built for fetching and caching server data and automates much
of that lifecycle.

# H. Zustand and Library Comparisons

### Q72. What is Zustand?

Zustand is a lightweight state-management library for React with a
hook-based API.

### Q73. How does Zustand work?

You create a store with state and update functions. Components call the
store hook and select the values they need.

### Q74. How do you update Zustand state?

Use the store's `set` function inside an action:

``` jsx
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 }))
}));
```

### Q75. Redux Toolkit vs Zustand?

Redux Toolkit offers a more structured architecture and a rich ecosystem
for larger workflows. Zustand offers a smaller API and less setup for
shared client state. The choice depends on complexity, team conventions,
debugging, and project needs.

### Q76. When would you choose Context, Redux Toolkit, or Zustand?

-   Context: a value such as theme or locale is needed by descendants.
-   Redux Toolkit: complex shared state, structured action flows,
    middleware, debugging, or RTK Query are useful.
-   Zustand: a lightweight shared store with a simple API is a good fit.

# I. Persistence, Server State, and Advanced Concepts

### Q77. Why does React state reset after a page refresh?

Component state exists in the running React application. A full page
refresh reloads the application and creates a new component tree, so
state is initialized again unless it is restored from persistent storage
or another source.

### Q78. localStorage vs sessionStorage?

`localStorage` persists until it is cleared. `sessionStorage` is scoped
to a page session and is generally cleared when that tab/session ends.
Both store string data and are accessible to JavaScript running on the
origin.

### Q79. How can state be persisted?

Serialize the selected state to storage such as localStorage or
sessionStorage, then read and parse it during initialization. Persist
only what is necessary and consider data validation, versioning, and
security.

### Q80. What is server state?

Server state is data owned by a remote server and accessed over a
network, such as users, products, or orders. It introduces concerns such
as loading, caching, stale data, errors, and refetching.

### Q81. Client state vs server state?

Client state is owned by the UI/application, such as whether a modal is
open. Server state is owned by a backend and fetched or updated through
network requests. They often need different management strategies.

### Q82. What is derived state?

Derived state is a value calculated from existing props or state. For
example, completed todos can be calculated by filtering the todos array.
Avoid storing duplicate derived values unless there is a specific
reason.

### Q83. What is state normalization?

Normalization means storing related entities in a flat structure, often
keyed by ID, rather than duplicating nested copies. It can make updates
and relationships easier to manage.

### Q84. What is optimistic updating?

An optimistic update changes the UI before the server confirms the
operation. If the request fails, the application must reconcile or roll
back the optimistic change.

### Q85. What is a state machine?

A state machine models an application as a set of explicit states and
allowed transitions. For example, a request can move from `idle` to
`loading`, then to `success` or `error`.

### Q86. How do you optimize state-management performance?

Keep state close to where it is used, split unrelated contexts, select
only required store values, avoid unnecessary derived state, and use
memoization when profiling shows it helps.

### Q87. What is URL state?

URL state is application state represented in the route path or query
string. Examples include a product ID, search term, selected category,
or page number. It is useful when state should be shareable or
bookmarkable.

### Q88. How would you implement undo and redo?

Keep a history containing past states, the current state, and future
states. On a new change, add the current state to history and clear the
future stack. Undo moves the current state to the future stack and
restores the latest past state; redo performs the reverse.

# J. Practical, Scenario-Based, and Tricky Questions

### Q89. A counter must increase by three on one click. How would you implement it?

Use functional updates so each update uses the latest pending value:

``` jsx
function incrementByThree() {
  setCount((count) => count + 1);
  setCount((count) => count + 1);
  setCount((count) => count + 1);
}
```

Alternatively, use `setCount((count) => count + 3)`.

### Q90. Two sibling components need the same state. What would you do?

Move the state into their closest common parent. Pass the value to both
children and pass an update callback to the child that needs to change
it.

### Q91. A deeply nested component needs the logged-in user. What options do you have?

For a value needed across many descendants, Context is one option. For
complex application-wide client state, Redux Toolkit or Zustand may be
suitable. For a small tree, ordinary props may be simpler.

### Q92. Why is an object state not updating after changing one property?

A common cause is direct mutation:

``` jsx
user.name = "Asha";
setUser(user);
```

The object reference has not changed. Instead, create a new object:

``` jsx
setUser((previous) => ({
  ...previous,
  name: "Asha"
}));
```

### Q93. Why does a Context consumer re-render?

A consumer updates when the nearest provider's value changes according
to `Object.is`. If the provider creates a new object or function on each
render, the context value may be considered changed even when its
contents are equivalent.

### Q94. How would you manage a shopping cart?

For a small app, Context with `useReducer` can share cart state and
named actions. For a larger application with complex shared workflows,
Redux Toolkit may be appropriate. Store product IDs and quantities
carefully, and derive totals from the cart items.

### Q95. How would you manage authentication state?

Keep authentication state in a clearly defined application-level owner
or store, expose the necessary user/session status to relevant
components, and coordinate login/logout with the backend. Protect routes
on the client for navigation and enforce authorization on the server.
Choose token and cookie handling based on the application's security
architecture.

### Q96. How would you manage API loading, success, and error states?

Represent the request lifecycle explicitly, for example with
`status: "idle" | "loading" | "succeeded" | "failed"` and an error
field. With Redux Toolkit, use `createAsyncThunk` or RTK Query. With
local component data, local state may be sufficient.

### Q97. Why should API calls not happen inside reducers?

Reducers should be pure and predictable. API calls are side effects and
can complete at unpredictable times. Handle them in a thunk, an event
handler, an effect, or a data-fetching tool such as RTK Query.

### Q98. Is Redux required for every React application?

No. React's built-in state tools are enough for many applications. Add
Redux Toolkit when shared-state complexity, workflows, tooling, or team
requirements justify it.

### Q99. What happens if a Redux selector returns a new object every time?

A selector that creates a new object on each evaluation can appear
changed by reference even when its fields are the same. This can cause
unnecessary component updates and, depending on the selector/equality
behavior, development warnings. Select individual values or use memoized
selectors and an appropriate equality function.

### Q100. How do you decide where state should live?

Ask: 1. Which components need the data? 2. Is it local to one component
or shared by siblings? 3. Is it needed across distant parts of the app?
4. Is it client-owned, server-owned, or represented in the URL? 5. Does
it need persistence? 6. How complex are its update rules?

Start with the simplest suitable solution: local state, then lifting
state, then Context or a library when the requirements call for it.

------------------------------------------------------------------------

## Final Revision Checklist

-   [ ] State, props, and render snapshots
-   [ ] `useState` and immutable updates
-   [ ] Functional updates and lazy initialization
-   [ ] Lifting state up and prop drilling
-   [ ] Context API and provider behavior
-   [ ] `useReducer` and pure reducers
-   [ ] Redux store, actions, reducers, and dispatch
-   [ ] Redux Toolkit slices and store configuration
-   [ ] `useSelector` and `useDispatch`
-   [ ] `createAsyncThunk` and RTK Query
-   [ ] Zustand
-   [ ] Persistence and server state
-   [ ] Performance and advanced patterns
-   [ ] Scenario-based implementation questions
