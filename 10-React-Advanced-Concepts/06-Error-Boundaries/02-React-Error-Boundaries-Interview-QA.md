# React Error Boundaries — Interview Questions and Answers

## Table of Contents
1. [What is an Error Boundary?](#q1-what-is-an-error-boundary)
2. [Which lifecycle methods are used?](#q2-which-lifecycle-methods-are-used)
3. [Can a function component be an Error Boundary?](#q3-can-a-function-component-be-an-error-boundary)
4. [What errors are not caught by Error Boundaries?](#q4-what-errors-are-not-caught-by-error-boundaries)
5. [Can an Error Boundary catch event-handler errors?](#q5-can-an-error-boundary-catch-event-handler-errors)
6. [Can it catch asynchronous errors?](#q6-can-it-catch-asynchronous-errors)
7. [Where should Error Boundaries be placed?](#q7-where-should-error-boundaries-be-placed)
8. [Error Boundary vs try/catch?](#q8-error-boundary-vs-trycatch)
9. [How do you reset an Error Boundary?](#q9-how-do-you-reset-an-error-boundary)
10. [How do you log errors?](#q10-how-do-you-log-errors)

## Q1. What is an Error Boundary?
**Interview answer:** An Error Boundary is a React component that catches rendering and lifecycle errors in its descendant tree and displays fallback UI. It helps isolate a failure so the entire application does not have to become unusable.

## Q2. Which lifecycle methods are used?
**Interview answer:** `static getDerivedStateFromError` updates state so the boundary can render fallback UI. `componentDidCatch` is used to log the error and component stack information. A boundary can use both.

## Q3. Can a function component be an Error Boundary?
**Interview answer:** The built-in Error Boundary mechanism is implemented with class lifecycle methods. A function component cannot become a boundary simply by calling a Hook. I can use a library that provides a function-friendly wrapper, but the boundary mechanism is still provided by a boundary component.

## Q4. What errors are not caught by Error Boundaries?
**Interview answer:** They do not catch errors in event handlers, asynchronous callbacks, server-side rendering, or errors thrown by the boundary itself. Those cases need their own error handling.

## Q5. Can an Error Boundary catch event-handler errors?
**Interview answer:** No. If a click handler throws, I handle it inside the handler with `try/catch` or handle a returned promise with `.catch`. Error Boundaries handle errors in React's descendant rendering and lifecycle process.

## Q6. Can it catch asynchronous errors?
**Interview answer:** Not automatically. A rejected request or timer callback happens outside the render path. I handle it with promise error handling and update an error state, or use the error-handling mechanism provided by the framework or data library.

## Q7. Where should Error Boundaries be placed?
**Interview answer:** I place boundaries around independent parts of the UI, such as route content or dashboard widgets. That way one failing widget can show fallback UI while the rest of the page remains available. I may also have a top-level boundary as a final safety net.

## Q8. Error Boundary vs try/catch?
**Interview answer:** `try/catch` handles JavaScript errors thrown synchronously inside its block. An Error Boundary integrates with React and catches errors while rendering descendant components. A try/catch around JSX creation does not catch a later descendant render failure.

## Q9. How do you reset an Error Boundary?
**Interview answer:** I provide a retry action or reset key that clears the boundary's error state. The reset should also make sense for the underlying failure, such as retrying data loading or navigating to a different route.

## Q10. How do you log errors?
**Interview answer:** I use `componentDidCatch` to send the error and component stack to an error monitoring service. The user sees a safe, helpful fallback message rather than internal stack traces or sensitive details.
