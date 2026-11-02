# Advanced Rendering and Reconciliation — Interview Questions and Answers

## Table of Contents
1. [What is the difference between rendering and committing?](#q1-what-is-the-difference-between-rendering-and-committing)
2. [What is reconciliation?](#q2-what-is-reconciliation)
3. [How does React use keys?](#q3-how-does-react-use-keys)
4. [Why can an array index be a bad key?](#q4-why-can-an-array-index-be-a-bad-key)
5. [Does every render update the DOM?](#q5-does-every-render-update-the-dom)
6. [How does a key reset component state?](#q6-how-does-a-key-reset-component-state)
7. [Why should render be pure?](#q7-why-should-render-be-pure)
8. [Why does Strict Mode run effects more than once in development?](#q8-why-does-strict-mode-run-effects-more-than-once-in-development)
9. [What causes a component to render?](#q9-what-causes-a-component-to-render)
10. [How would you investigate unnecessary rendering?](#q10-how-would-you-investigate-unnecessary-rendering)

## Q1. What is the difference between rendering and committing?
**Interview answer:** Rendering is when React calls components and calculates the next UI tree. Committing is when React applies the required changes to the DOM. A render does not necessarily cause a DOM mutation because React may determine that the output has not changed.

## Q2. What is reconciliation?
**Interview answer:** Reconciliation is React's process of comparing the previous element tree with the next one to determine which parts need to be updated, inserted, or removed. Element types, positions, and keys help React identify component identity.

## Q3. How does React use keys?
**Interview answer:** Keys give list elements stable identities among their siblings. They help React match an item between renders when the list changes. I normally use a database ID rather than an array index.

## Q4. Why can an array index be a bad key?
**Interview answer:** If an item is inserted or removed near the beginning, the same index can now refer to a different item. React may preserve local state for the wrong row, which can cause incorrect input values or UI state. An index is acceptable only for a static list that never changes order or membership.

## Q5. Does every render update the DOM?
**Interview answer:** No. Rendering calculates the next UI. React compares the result and commits only the necessary host changes. A component can render while the actual DOM remains unchanged.

## Q6. How does a key reset component state?
**Interview answer:** A key is part of a component's identity. When the key changes, React treats it as a different component instance, removes the old one, and mounts a new one. For example, I can key a profile editor by user ID to reset its local form state when the selected user changes.

## Q7. Why should render be pure?
**Interview answer:** React expects rendering to be a calculation from props, state, and Context. React may call rendering more than once, pause it, or discard work. Side effects during render could therefore happen unexpectedly or multiple times. I keep side effects in event handlers or effects.

## Q8. Why does Strict Mode run effects more than once in development?
**Interview answer:** Strict Mode performs extra development checks to expose effects that do not clean up correctly. The extra setup and cleanup cycle helps identify subscriptions, timers, and other resources that leak. I fix the cleanup rather than disabling the check.

## Q9. What causes a component to render?
**Interview answer:** A component can render when its state changes, its parent renders, a Context value it consumes changes, or an external store subscription updates. Whether that render causes a DOM change is a separate question.

## Q10. How would you investigate unnecessary rendering?
**Interview answer:** I would use React DevTools Profiler to identify which components rendered and how much time they took. Then I would inspect state placement, Context value identity, props, keys, and expensive calculations. I would optimize only after confirming a measurable issue.
