# Advanced Hooks, Effects, and Refs — Interview Questions and Answers

## Table of Contents
1. [What are the Rules of Hooks?](#q1-what-are-the-rules-of-hooks)
2. [When should I use useEffect?](#q2-when-should-i-use-useeffect)
3. [What is the purpose of an effect cleanup function?](#q3-what-is-the-purpose-of-an-effect-cleanup-function)
4. [useEffect vs useLayoutEffect?](#q4-useeffect-vs-uselayouteffect)
5. [useRef vs useState?](#q5-useref-vs-usestate)
6. [What is useImperativeHandle?](#q6-what-is-useimperativehandle)
7. [useMemo vs useCallback?](#q7-usememo-vs-usecallback)
8. [When should useReducer be preferred?](#q8-when-should-usereducer-be-preferred)
9. [Do custom Hooks share state?](#q9-do-custom-hooks-share-state)
10. [What causes an infinite effect loop?](#q10-what-causes-an-infinite-effect-loop)

## Q1. What are the Rules of Hooks?
**Interview answer:** I call Hooks only at the top level of a function component or custom Hook. I do not call them conditionally, inside loops, or in nested functions. This keeps the Hook call order consistent across renders.

## Q2. When should I use useEffect?
**Interview answer:** I use `useEffect` to synchronize a component with an external system, such as an event subscription, timer, browser API, or network connection. I do not use it merely to calculate a value that can be derived during rendering.

## Q3. What is the purpose of an effect cleanup function?
**Interview answer:** Cleanup releases resources created by an effect. For example, it removes an event listener, clears a timer, or disconnects a subscription. React runs cleanup before the effect is set up again and when the component is removed.

## Q4. useEffect vs useLayoutEffect?
**Interview answer:** `useEffect` is the default for synchronization that does not need to block painting. `useLayoutEffect` runs after DOM updates but before paint, so I use it for layout measurement or visual corrections that must happen before the user sees the screen. Because it can block paint, I use it sparingly.

## Q5. useRef vs useState?
**Interview answer:** I use state when a value affects what the user sees because updating state schedules a render. I use a ref for a mutable value that must persist between renders but does not itself need to trigger rendering, such as a DOM node or timer ID.

## Q6. What is useImperativeHandle?
**Interview answer:** It lets a component expose a controlled imperative API through a ref. Instead of exposing an entire DOM node, a child might expose only `focus` and `clear` methods. I prefer declarative props unless imperative access is genuinely needed.

## Q7. useMemo vs useCallback?
**Interview answer:** `useMemo` memoizes a computed value, while `useCallback` memoizes a function reference. Both are performance optimizations. I use them when profiling shows expensive recalculation or when stable identity helps a memoized child or dependency.

## Q8. When should useReducer be preferred?
**Interview answer:** I prefer `useReducer` when state transitions are related, involve several fields, or are easier to describe as actions. It puts transition logic in one pure reducer and can make complex state changes easier to test.

## Q9. Do custom Hooks share state?
**Interview answer:** No. A custom Hook shares reusable logic, not a single state instance. If two components call the same custom Hook, each normally has its own state. To share state, I would use Context, a common parent, or an external store.

## Q10. What causes an infinite effect loop?
**Interview answer:** A common cause is an effect that updates state and then reruns because one of its dependencies changes on every render, such as a newly created object or function. I inspect the dependency list, stabilize values only when appropriate, and reconsider whether the effect is needed.
