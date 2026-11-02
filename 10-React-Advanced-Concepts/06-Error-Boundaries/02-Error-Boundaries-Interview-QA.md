# React Error Boundaries — Interview Questions and Answers

## Clickable Table of Contents

1. [What is an Error Boundary in React?](#1-what-is-an-error-boundary-in-react)
2. [Why do we need Error Boundaries?](#2-why-do-we-need-error-boundaries)
3. [How does an Error Boundary work?](#3-how-does-an-error-boundary-work)
4. [Can a function component be an Error Boundary?](#4-can-a-function-component-be-an-error-boundary)
5. [Which lifecycle methods are used by Error Boundaries?](#5-which-lifecycle-methods-are-used-by-error-boundaries)
6. [What is getDerivedStateFromError?](#6-what-is-getderivedstatefromerror)
7. [What is componentDidCatch?](#7-what-is-componentdidcatch)
8. [What is the difference between the two lifecycle methods?](#8-what-is-the-difference-between-the-two-lifecycle-methods)
9. [Can getDerivedStateFromError perform side effects?](#9-can-getderivedstatefromerror-perform-side-effects)
10. [What arguments does componentDidCatch receive?](#10-what-arguments-does-componentdidcatch-receive)
11. [What types of errors do Error Boundaries catch?](#11-what-types-of-errors-do-error-boundaries-catch)
12. [Do Error Boundaries catch event handler errors?](#12-do-error-boundaries-catch-event-handler-errors)
13. [Do Error Boundaries catch asynchronous errors?](#13-do-error-boundaries-catch-asynchronous-errors)
14. [Do Error Boundaries catch rejected Promises?](#14-do-error-boundaries-catch-rejected-promises)
15. [Do Error Boundaries catch errors during server-side rendering?](#15-do-error-boundaries-catch-errors-during-server-side-rendering)
16. [Can an Error Boundary catch its own error?](#16-can-an-error-boundary-catch-its-own-error)
17. [How do you create a basic Error Boundary?](#17-how-do-you-create-a-basic-error-boundary)
18. [What is fallback UI?](#18-what-is-fallback-ui)
19. [Where should Error Boundaries be placed?](#19-where-should-error-boundaries-be-placed)
20. [What is an application-level Error Boundary?](#20-what-is-an-application-level-error-boundary)
21. [What is a route-level Error Boundary?](#21-what-is-a-route-level-error-boundary)
22. [What is a component-level Error Boundary?](#22-what-is-a-component-level-error-boundary)
23. [Can we use multiple Error Boundaries?](#23-can-we-use-multiple-error-boundaries)
24. [What happens when a child component throws an error?](#24-what-happens-when-a-child-component-throws-an-error)
25. [How does React find the Error Boundary?](#25-how-does-react-find-the-error-boundary)
26. [What happens if there is no Error Boundary?](#26-what-happens-if-there-is-no-error-boundary)
27. [How do you handle errors in event handlers?](#27-how-do-you-handle-errors-in-event-handlers)
28. [How do you handle errors in async functions?](#28-how-do-you-handle-errors-in-async-functions)
29. [How can an async error be surfaced to an Error Boundary?](#29-how-can-an-async-error-be-surfaced-to-an-error-boundary)
30. [What is the difference between an Error Boundary and try/catch?](#30-what-is-the-difference-between-an-error-boundary-and-trycatch)
31. [What is the difference between Error Boundaries and Suspense?](#31-what-is-the-difference-between-error-boundaries-and-suspense)
32. [Can Error Boundaries catch errors from lazy-loaded components?](#32-can-error-boundaries-catch-errors-from-lazy-loaded-components)
33. [How do you reset an Error Boundary?](#33-how-do-you-reset-an-error-boundary)
34. [Does resetting an Error Boundary fix the original bug?](#34-does-resetting-an-error-boundary-fix-the-original-bug)
35. [How can a boundary reset when a route changes?](#35-how-can-a-boundary-reset-when-a-route-changes)
36. [What is the purpose of a key on an Error Boundary?](#36-what-is-the-purpose-of-a-key-on-an-error-boundary)
37. [How do you report errors from an Error Boundary?](#37-how-do-you-report-errors-from-an-error-boundary)
38. [What is componentStack?](#38-what-is-componentstack)
39. [Should stack traces be displayed to users?](#39-should-stack-traces-be-displayed-to-users)
40. [Can the fallback UI itself throw an error?](#40-can-the-fallback-ui-itself-throw-an-error)
41. [How do nested Error Boundaries work?](#41-how-do-nested-error-boundaries-work)
42. [How do Error Boundaries behave in Strict Mode?](#42-how-do-error-boundaries-behave-in-strict-mode)
43. [Can an Error Boundary catch errors in Effects?](#43-can-an-error-boundary-catch-errors-in-effects)
44. [What is the best placement strategy for a dashboard?](#44-what-is-the-best-placement-strategy-for-a-dashboard)
45. [How should fallback UI be designed?](#45-how-should-fallback-ui-be-designed)
46. [What are common Error Boundary mistakes?](#46-what-are-common-error-boundary-mistakes)
47. [How do you test an Error Boundary?](#47-how-do-you-test-an-error-boundary)
48. [Why might a test print an error to the console?](#48-why-might-a-test-print-an-error-to-the-console)
49. [Can an Error Boundary handle errors from a third-party component?](#49-can-an-error-boundary-handle-errors-from-a-third-party-component)
50. [What is the difference between error handling and error recovery?](#50-what-is-the-difference-between-error-handling-and-error-recovery)
51. [What does this Error Boundary render?](#51-what-does-this-error-boundary-render)
52. [Will this try/catch catch a rendering error?](#52-will-this-trycatch-catch-a-rendering-error)
53. [Why does this boundary not catch a button click error?](#53-why-does-this-boundary-not-catch-a-button-click-error)
54. [How would you implement a reusable Error Boundary?](#54-how-would-you-implement-a-reusable-error-boundary)
55. [How do you prevent error reporting from causing another failure?](#55-how-do-you-prevent-error-reporting-from-causing-another-failure)
56. [Should every component have its own Error Boundary?](#56-should-every-component-have-its-own-error-boundary)
57. [What should happen when the boundary's fallback fails?](#57-what-should-happen-when-the-boundarys-fallback-fails)
58. [Can a function component use a library-provided Error Boundary API?](#58-can-a-function-component-use-a-library-provided-error-boundary-api)
59. [What is the biggest limitation of Error Boundaries?](#59-what-is-the-biggest-limitation-of-error-boundaries)
60. [Explain Error Boundaries in an interview.](#60-explain-error-boundaries-in-an-interview)

---

## 1. What is an Error Boundary in React?

An Error Boundary is a React component that catches certain JavaScript errors in its descendant component tree, mainly during rendering and lifecycle processing. It displays fallback UI instead of allowing that failed part of the interface to remain broken.

## 2. Why do we need Error Boundaries?

We use Error Boundaries to isolate UI failures. If one dashboard widget crashes, for example, a boundary around that widget can show a fallback while the rest of the dashboard remains available.

## 3. How does an Error Boundary work?

When a descendant throws a covered error, React finds the nearest Error Boundary above it. The boundary updates its state and renders fallback UI. It can also log the error for debugging and monitoring.

## 4. Can a function component be an Error Boundary?

The built-in Error Boundary API uses class component lifecycle methods. A function component cannot become a built-in Error Boundary just by using Hooks. However, a library can expose a function-oriented API that uses a class boundary internally.

## 5. Which lifecycle methods are used by Error Boundaries?

The two lifecycle methods are `static getDerivedStateFromError(error)` and `componentDidCatch(error, info)`. The first updates state for fallback rendering, and the second is commonly used for logging.

## 6. What is getDerivedStateFromError?

It is a static lifecycle method called when a descendant throws a covered error. It returns state that tells the boundary to render fallback UI. It should not perform side effects.

## 7. What is componentDidCatch?

It is a lifecycle method called after an error is caught. I use it to log the error and report useful diagnostic information, such as the React component stack, to an error-monitoring service.

## 8. What is the difference between the two lifecycle methods?

`getDerivedStateFromError` updates state so the next render can show fallback UI. `componentDidCatch` is for side effects such as logging and reporting. They solve different parts of error handling.

## 9. Can getDerivedStateFromError perform side effects?

No. It should remain pure and return the state update needed for fallback rendering. Side effects such as logging belong in `componentDidCatch`.

## 10. What arguments does componentDidCatch receive?

It receives the error object and an `info` object. The `info.componentStack` property contains the React component stack associated with the error.

## 11. What types of errors do Error Boundaries catch?

They catch errors thrown by descendant components during rendering and certain lifecycle processing, including constructor and lifecycle execution covered by React's Error Boundary behavior.

## 12. Do Error Boundaries catch event handler errors?

No, not automatically. Event handlers run in response to user interactions. I handle those errors with `try/catch` inside the handler and show an appropriate error state.

## 13. Do Error Boundaries catch asynchronous errors?

No, not automatically. Errors from timers, later callbacks, and asynchronous operations need explicit error handling, usually with `try/catch` or Promise rejection handlers.

## 14. Do Error Boundaries catch rejected Promises?

A rejected Promise is not automatically caught by an Error Boundary. I handle the rejection explicitly. If I want a boundary to display the failure, I can store the error and deliberately throw it during a later render.

## 15. Do Error Boundaries catch errors during server-side rendering?

No. Client-side Error Boundaries do not catch server-rendering errors. Server-side failures need to be handled by the server-rendering framework or server application.

## 16. Can an Error Boundary catch its own error?

No. A boundary cannot catch errors thrown by its own implementation or fallback UI. A parent Error Boundary can catch errors thrown by a nested boundary.

## 17. How do you create a basic Error Boundary?

I create a class component with an error state, implement `getDerivedStateFromError` to update that state, and render fallback UI when the state indicates an error. I can also implement `componentDidCatch` for logging.

## 18. What is fallback UI?

Fallback UI is the alternative interface displayed when the boundary catches an error. It should explain that the affected section is unavailable and provide a useful next step without exposing sensitive technical details.

## 19. Where should Error Boundaries be placed?

I place them around meaningful failure regions, such as the application shell, routes, dashboard widgets, or third-party components. The right location depends on how much of the UI should fail together.

## 20. What is an application-level Error Boundary?

It wraps most or all of the application and provides a final fallback for major UI failures. It is useful as a safety net, but it may not provide the best user experience for every individual feature.

## 21. What is a route-level Error Boundary?

It wraps a route or page. If that page fails, the user can see a page-specific fallback while shared navigation or other application areas may remain available.

## 22. What is a component-level Error Boundary?

It wraps an independent component or widget, such as a chart or activity panel. If that component crashes, the boundary can isolate the failure to that region.

## 23. Can we use multiple Error Boundaries?

Yes. Error Boundaries can be nested. React uses the nearest boundary above the component that throws the error, which allows different parts of the UI to have different fallback experiences.

## 24. What happens when a child component throws an error?

React looks upward for the nearest Error Boundary. If one is found, React invokes its error-handling lifecycle methods and renders its fallback instead of the failed child subtree.

## 25. How does React find the Error Boundary?

React searches upward through the component tree from the component that threw the error and uses the nearest ancestor that acts as an Error Boundary.

## 26. What happens if there is no Error Boundary?

There is no application-defined fallback boundary to handle that error. In modern React, an uncaught rendering error can cause the affected root's UI to be unmounted rather than leaving a potentially corrupted interface on screen.

## 27. How do you handle errors in event handlers?

I use `try/catch` inside the event handler. For asynchronous handlers, I use `try/catch` with `async/await` or attach a rejection handler to the Promise.

## 28. How do you handle errors in async functions?

I wrap the awaited operation in `try/catch`, update an error state, and render an appropriate message. I also consider cancellation or stale-response handling when the operation is tied to a component's lifecycle.

## 29. How can an async error be surfaced to an Error Boundary?

I can catch the asynchronous error, store it in component state, and then throw that stored error during rendering. The boundary can catch the render-time throw. This is an explicit pattern; the boundary does not catch the original Promise rejection automatically.

## 30. What is the difference between an Error Boundary and try/catch?

An Error Boundary handles covered errors in descendant React rendering and lifecycle processing and can replace a failed subtree with fallback UI. `try/catch` handles errors in the code block where it is used, such as event handlers and asynchronous functions with `await`.

## 31. What is the difference between Error Boundaries and Suspense?

Suspense displays fallback UI while a descendant is waiting for a supported resource, such as lazy-loaded code. An Error Boundary displays fallback UI when a descendant throws an error. They can be composed to handle loading and failure states separately.

## 32. Can Error Boundaries catch errors from lazy-loaded components?

Yes. If a lazy-loaded component fails to load and the resulting error is thrown during rendering, an Error Boundary above it can display fallback UI. Suspense handles the loading state while the component is pending.

## 33. How do you reset an Error Boundary?

I can add a reset handler that changes the boundary's error state back to its normal state. The children are then rendered again. A library may also provide reset keys or a reset callback.

## 34. Does resetting an Error Boundary fix the original bug?

No. Resetting only allows React to try rendering the children again. If the underlying problem still exists, the same error can happen again.

## 35. How can a boundary reset when a route changes?

I can use a route identifier as the boundary's `key`. When the key changes, React remounts the boundary and its subtree. Some Error Boundary libraries also support a `resetKeys` option.

## 36. What is the purpose of a key on an Error Boundary?

A changed key tells React that the boundary represents a different component identity, so React remounts it and resets its local state. I use a stable key tied to the content or route that should trigger a reset.

## 37. How do you report errors from an Error Boundary?

I use `componentDidCatch` to send the error and relevant component-stack information to a monitoring service. I make sure the report does not include secrets or personal information that should not be collected.

## 38. What is componentStack?

`componentStack` is diagnostic information supplied in the `info` object passed to `componentDidCatch`. It describes the React component hierarchy associated with the error and helps locate the failing area.

## 39. Should stack traces be displayed to users?

No. Users should receive a clear, safe message. Detailed stack traces and internal diagnostics should be kept in protected developer logs or monitoring systems.

## 40. Can the fallback UI itself throw an error?

Yes. An Error Boundary cannot catch its own fallback rendering error. A parent boundary can catch an error thrown by a nested boundary's fallback.

## 41. How do nested Error Boundaries work?

If a child inside the inner boundary throws, the inner boundary handles it first. If the inner boundary itself fails, an outer boundary can handle that failure.

## 42. How do Error Boundaries behave in Strict Mode?

Strict Mode can re-run certain development behaviors to help identify problems. Error Boundaries still handle the same categories of errors; Strict Mode does not make them global error handlers. Logging should account for development-only behavior.

## 43. Can an Error Boundary catch errors in Effects?

Errors thrown synchronously while React runs certain lifecycle processing are within the boundary model, but asynchronous work started by an Effect is not automatically caught. For async work, catch failures in the async function and manage an error state.

## 44. What is the best placement strategy for a dashboard?

I would use an application-level safety boundary and smaller boundaries around independent dashboard widgets. That way, a chart failure does not necessarily hide the rest of the dashboard.

## 45. How should fallback UI be designed?

It should be clear, accessible, and appropriate to the affected region. It can offer a retry action if retrying makes sense, but it should not expose stack traces or sensitive internal details.

## 46. What are common Error Boundary mistakes?

Common mistakes include expecting boundaries to catch event-handler or asynchronous errors, trying to create a built-in boundary with a function component, putting side effects in `getDerivedStateFromError`, forgetting fallback UI, and assuming reset fixes the underlying bug.

## 47. How do you test an Error Boundary?

I test that normal children render without errors and that a descendant render error causes the fallback to appear. I also test logging and reset behavior when those are part of the component's requirements.

## 48. Why might a test print an error to the console?

React may report the intentionally thrown test error while exercising the boundary. I can spy on or mock the expected console output to keep test output clear, while still verifying that the fallback appears.

## 49. Can an Error Boundary handle errors from a third-party component?

Yes, if the third-party component throws an error during a phase covered by Error Boundaries. Wrapping unreliable or complex third-party UI is a useful isolation strategy.

## 50. What is the difference between error handling and error recovery?

Error handling detects or responds to a failure, for example by displaying fallback UI. Recovery attempts to restore normal operation, such as retrying a request or resetting a boundary. A reset does not guarantee that the root cause has been fixed.

## 51. What does this Error Boundary render?

```jsx
static getDerivedStateFromError() {
  return { hasError: true };
}

render() {
  if (this.state.hasError) {
    return <h2>Failed</h2>;
  }

  return this.props.children;
}
```

When a descendant throws a covered error, the boundary updates `hasError` to `true` and renders the `Failed` heading instead of its children.

## 52. Will this try/catch catch a rendering error?

```jsx
function Page() {
  try {
    return <Child />;
  } catch (error) {
    return <p>Failed</p>;
  }
}
```

No, not reliably. The child component's rendering is performed by React after this component returns its element. A surrounding Error Boundary is the appropriate mechanism for descendant rendering errors.

## 53. Why does this boundary not catch a button click error?

```jsx
function Child() {
  return (
    <button onClick={() => {
      throw new Error("Click failed");
    }}>
      Click
    </button>
  );
}
```

The error occurs later in an event handler, not during descendant rendering or covered lifecycle processing. I need to handle it inside the event handler with `try/catch`.

## 54. How would you implement a reusable Error Boundary?

I would create a class component that accepts `children` and optionally a fallback. It would use `getDerivedStateFromError` for fallback state and `componentDidCatch` for reporting. I would also define how the boundary can be reset.

## 55. How do you prevent error reporting from causing another failure?

I keep the reporting path defensive. For example, I can wrap a monitoring call in its own `try/catch` and ensure reporting failure does not prevent the fallback UI from rendering.

## 56. Should every component have its own Error Boundary?

No. Too many boundaries add complexity. I place them around meaningful independent regions where isolating a failure improves the user experience.

## 57. What should happen when the boundary's fallback fails?

A parent Error Boundary should provide another layer of protection. A boundary cannot catch errors from its own fallback, so nested boundaries are useful when fallback components are complex.

## 58. Can a function component use a library-provided Error Boundary API?

Yes. Some libraries expose a function component or Hook-based helper that works with an underlying class Error Boundary. The built-in React lifecycle implementation itself remains class-based.

## 59. What is the biggest limitation of Error Boundaries?

They do not catch every JavaScript error. In particular, event handlers, asynchronous callbacks, rejected Promises, server-rendering errors, and errors thrown by the boundary itself need other handling strategies.

## 60. Explain Error Boundaries in an interview.

An Error Boundary is a React component that catches errors in its descendant component tree during rendering and certain lifecycle phases. It allows us to show fallback UI instead of letting a failed part of the interface break the user experience.

The built-in implementation uses a class component with `getDerivedStateFromError` to update state and `componentDidCatch` to log or report the error. Error Boundaries do not automatically catch event-handler errors, asynchronous errors, rejected Promises, or server-rendering errors. I usually place them around routes or independent widgets and handle other failures with appropriate `try/catch` logic.
