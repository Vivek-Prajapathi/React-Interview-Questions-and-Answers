# Advanced State Management and Context — Interview Questions and Answers

## Table of Contents
1. [How do you decide where state should live?](#q1-how-do-you-decide-where-state-should-live)
2. [What is lifting state up?](#q2-what-is-lifting-state-up)
3. [When should you use Context?](#q3-when-should-you-use-context)
4. [Is Context a complete state management solution?](#q4-is-context-a-complete-state-management-solution)
5. [How can Context cause performance issues?](#q5-how-can-context-cause-performance-issues)
6. [Why combine useReducer with Context?](#q6-why-combine-usereducer-with-context)
7. [What is server state?](#q7-what-is-server-state)
8. [Client state vs server state?](#q8-client-state-vs-server-state)
9. [What is useSyncExternalStore?](#q9-what-is-usesyncexternalstore)
10. [Should every state value be global?](#q10-should-every-state-value-be-global)

## Q1. How do you decide where state should live?
**Interview answer:** I first identify who needs the value and who owns its lifecycle. If one component needs it, I keep it local. If siblings need it, I lift it to their common parent. If many descendants need it, Context or an external store may be appropriate. Remote API data may be better handled as server state.

## Q2. What is lifting state up?
**Interview answer:** Lifting state up means moving shared state to the closest common parent of the components that need it. The parent passes the current value and callbacks down, so there is one source of truth.

## Q3. When should you use Context?
**Interview answer:** I use Context when a value is needed by many descendants across different levels, such as theme, authenticated user, or locale. It avoids passing the same prop through components that do not otherwise need it.

## Q4. Is Context a complete state management solution?
**Interview answer:** Context is a value distribution mechanism. It can work with `useState` or `useReducer` for state management, but it does not automatically provide features such as selectors, caching, persistence, or server-state synchronization.

## Q5. How can Context cause performance issues?
**Interview answer:** When a provider value changes, its consumers are notified. If the provider creates a new object every render, that can cause unnecessary consumer updates. I can stabilize values where useful, split unrelated contexts, and profile the result.

## Q6. Why combine useReducer with Context?
**Interview answer:** A reducer centralizes complex state transitions into named actions, while Context makes state and dispatch available to descendants. This can be a clean pattern for shared application state with several related transitions.

## Q7. What is server state?
**Interview answer:** Server state is data owned by a remote backend. It is asynchronous and can become stale or change outside the current component. It often needs caching, refetching, invalidation, and error handling.

## Q8. Client state vs server state?
**Interview answer:** Client state is owned by the current UI session, such as an open menu or selected tab. Server state comes from a backend, such as products or user records, and needs synchronization with the remote source. I choose tools based on those different requirements.

## Q9. What is useSyncExternalStore?
**Interview answer:** `useSyncExternalStore` lets a React component subscribe to an external store and read its snapshot in a way that is compatible with React's rendering model. It is useful for state libraries or custom stores outside React.

## Q10. Should every state value be global?
**Interview answer:** No. Globalizing every value increases coupling and makes updates harder to reason about. I keep state as local as possible and share it only when multiple parts of the application genuinely need it.
