# Debouncing and Throttling – Interview Questions and Answers

## Table of Contents

1. [What is debouncing?](#1-what-is-debouncing)
2. [What is throttling?](#2-what-is-throttling)
3. [Why are debounce and throttle used?](#3-why-are-debounce-and-throttle-used)
4. [What is the main difference between them?](#4-what-is-the-main-difference-between-them)
5. [What happens in a debounce timer when another call arrives?](#5-what-happens-in-a-debounce-timer-when-another-call-arrives)
6. [What are common debounce use cases?](#6-what-are-common-debounce-use-cases)
7. [What are common throttle use cases?](#7-what-are-common-throttle-use-cases)
8. [Implement debounce in JavaScript.](#8-implement-debounce-in-javascript)
9. [Explain the debounce implementation.](#9-explain-the-debounce-implementation)
10. [What is a closure and why is it important here?](#10-what-is-a-closure-and-why-is-it-important-here)
11. [Why do we use clearTimeout?](#11-why-do-we-use-cleartimeout)
12. [Why use rest parameters?](#12-why-use-rest-parameters)
13. [Why use apply?](#13-why-use-apply)
14. [Implement a leading-edge throttle.](#14-implement-a-leading-edge-throttle)
15. [Explain the timestamp-based throttle.](#15-explain-the-timestamp-based-throttle)
16. [What is a trailing-edge debounce?](#16-what-is-a-trailing-edge-debounce)
17. [What are leading and trailing execution?](#17-what-are-leading-and-trailing-execution)
18. [Can debounce execute immediately?](#18-can-debounce-execute-immediately)
19. [Does throttle execute every event?](#19-does-throttle-execute-every-event)
20. [Which technique is better for a search input?](#20-which-technique-is-better-for-a-search-input)
21. [Which technique is better for scroll tracking?](#21-which-technique-is-better-for-scroll-tracking)
22. [Why can creating debounce inside a React component be a problem?](#22-why-can-creating-debounce-inside-a-react-component-be-a-problem)
23. [How can useMemo help?](#23-how-can-usememo-help)
24. [How can useRef help?](#24-how-can-useref-help)
25. [How do you debounce a search input in React?](#25-how-do-you-debounce-a-search-input-in-react)
26. [What is a debounced value hook?](#26-what-is-a-debounced-value-hook)
27. [Explain cleanup in a debounced value hook.](#27-explain-cleanup-in-a-debounced-value-hook)
28. [Why should pending timers be cancelled on unmount?](#28-why-should-pending-timers-be-cancelled-on-unmount)
29. [Does removing an event listener cancel its timer?](#29-does-removing-an-event-listener-cancel-its-timer)
30. [What is a stale closure?](#30-what-is-a-stale-closure)
31. [How can stale closures be avoided?](#31-how-can-stale-closures-be-avoided)
32. [Does debounce cancel an in-flight API request?](#32-does-debounce-cancel-an-in-flight-api-request)
33. [How do you cancel an in-flight fetch?](#33-how-do-you-cancel-an-in-flight-fetch)
34. [How do you avoid out-of-order search responses?](#34-how-do-you-avoid-out-of-order-search-responses)
35. [What is the difference between debounce and request cancellation?](#35-what-is-the-difference-between-debounce-and-request-cancellation)
36. [What is requestAnimationFrame?](#36-what-is-requestanimationframe)
37. [How is requestAnimationFrame different from throttle?](#37-how-is-requestanimationframe-different-from-throttle)
38. [What is a common mistake when using removeEventListener?](#38-what-is-a-common-mistake-when-using-removeeventlistener)
39. [What happens if delay changes?](#39-what-happens-if-delay-changes)
40. [How does React Strict Mode affect timer-related Effects?](#40-how-does-react-strict-mode-affect-timer-related-effects)
41. [What should a debounce utility's cancel method do?](#41-what-should-a-debounce-utilitys-cancel-method-do)
42. [How would you test debounce?](#42-how-would-you-test-debounce)
43. [How would you test throttle?](#43-how-would-you-test-throttle)
44. [What is maxWait?](#44-what-is-maxwait)
45. [What is the difference between leading-only and trailing-only throttle?](#45-what-is-the-difference-between-leading-only-and-trailing-only-throttle)
46. [Can debounce improve performance by itself?](#46-can-debounce-improve-performance-by-itself)
47. [Should every input use debounce?](#47-should-every-input-use-debounce)
48. [How would you debounce autosave?](#48-how-would-you-debounce-autosave)
49. [How would you throttle a resize handler?](#49-how-would-you-throttle-a-resize-handler)
50. [What would you choose for a button that must not be submitted repeatedly?](#50-what-would-you-choose-for-a-button-that-must-not-be-submitted-repeatedly)
51. [What is the output of this debounce example?](#51-what-is-the-output-of-this-debounce-example)
52. [What is wrong with this React code?](#52-what-is-wrong-with-this-react-code)
53. [Why might a debounced callback use old state?](#53-why-might-a-debounced-callback-use-old-state)
54. [Does a simple throttle queue suppressed calls?](#54-does-a-simple-throttle-queue-suppressed-calls)
55. [When should a utility library be preferred?](#55-when-should-a-utility-library-be-preferred)
56. [Can debounce and throttle be combined?](#56-can-debounce-and-throttle-be-combined)
57. [What are the trade-offs of a long debounce delay?](#57-what-are-the-trade-offs-of-a-long-debounce-delay)
58. [How would you explain both techniques in a real project?](#58-how-would-you-explain-both-techniques-in-a-real-project)
59. [What are the key React best practices?](#59-what-are-the-key-react-best-practices)
60. [Give a concise interview summary.](#60-give-a-concise-interview-summary)

---

## 1. What is debouncing?

Debouncing is a technique that delays a function until a specified time has passed without another call. Whenever a new call arrives, the previous timer is reset. I use it when I want an operation to happen after the user pauses, such as search suggestions while typing.

## 2. What is throttling?

Throttling limits how frequently a function can execute. Even if an event fires many times, the function runs only at controlled intervals. I commonly use it for scroll and resize processing.

## 3. Why are debounce and throttle used?

They reduce unnecessary work caused by high-frequency events. This can mean fewer API calls, fewer expensive calculations, and less repeated processing. They control frequency; they do not make the underlying operation itself faster.

## 4. What is the main difference between them?

Debouncing waits for a quiet period after the last call. Throttling allows execution at a limited rate while calls continue. I choose debounce when the final value after a pause matters, and throttle when periodic updates are useful.

## 5. What happens in a debounce timer when another call arrives?

The existing timer is cleared and a new timer is scheduled. Therefore, the callback runs only after the latest call has been followed by the full delay with no additional call.

## 6. What are common debounce use cases?

Search suggestions, autosaving a draft after typing stops, validating a field after the user pauses, and applying filters after a user finishes changing them.

## 7. What are common throttle use cases?

Scroll position tracking, resize processing, pointer movement handling, and limiting repeated actions that should not run on every event.

## 8. Implement debounce in JavaScript.

```js
function debounce(callback, delay) {
  let timerId;

  return function (...args) {
    clearTimeout(timerId);
    timerId = setTimeout(() => callback.apply(this, args), delay);
  };
}
```

## 9. Explain the debounce implementation.

The returned function stores a timer ID in its closure. Each call clears the previous timer and schedules a new one. If no further call arrives during the delay, the callback executes with the latest arguments.

## 10. What is a closure and why is it important here?

A closure allows a function to access variables from its outer lexical scope even after that outer function has returned. In debounce, the returned function retains access to `timerId`, so it can clear and replace the pending timer across multiple calls.

## 11. Why do we use clearTimeout?

`clearTimeout` cancels a scheduled timeout. Debounce uses it to cancel the previous pending execution whenever a newer call arrives.

## 12. Why use rest parameters?

Rest parameters collect all arguments passed to the returned function into an array. This allows the utility to work with callbacks that accept different numbers of arguments.

## 13. Why use apply?

`apply` invokes the callback with a chosen `this` value and an array of arguments. It helps forward the original calling context and arguments. If a utility does not need to preserve `this`, a simpler arrow-function call may be sufficient.

## 14. Implement a leading-edge throttle.

```js
function throttle(callback, delay) {
  let lastTime = 0;

  return function (...args) {
    const now = Date.now();

    if (now - lastTime >= delay) {
      lastTime = now;
      callback.apply(this, args);
    }
  };
}
```

## 15. Explain the timestamp-based throttle.

It stores the time when the callback last executed. On every call, it compares the current time with that timestamp. The callback runs only if at least the configured delay has passed. Calls inside the interval are ignored.

## 16. What is a trailing-edge debounce?

A trailing-edge debounce executes after the last call in a burst, once the full delay has passed without another call. It is the common behavior used for search inputs.

## 17. What are leading and trailing execution?

Leading execution runs at the beginning of an event burst. Trailing execution runs after the burst ends and the delay passes. Some utilities support either behavior or both.

## 18. Can debounce execute immediately?

Yes. A debounce utility can support a leading option that executes on the first call. Its exact behavior depends on how leading and trailing execution are implemented.

## 19. Does throttle execute every event?

No. A throttle limits execution frequency. A simple leading-only implementation ignores calls that occur inside the interval. Other implementations may schedule a trailing call, but they still do not execute every event.

## 20. Which technique is better for a search input?

Debouncing is usually better. I want to wait until the user pauses typing before starting a search request, which avoids sending a request for every keystroke.

## 21. Which technique is better for scroll tracking?

Throttling is often better when I need periodic updates while scrolling continues. If I only need to react after scrolling has stopped, debouncing may be more appropriate.

## 22. Why can creating debounce inside a React component be a problem?

The component function runs again on each render. If I create the debounced function on every render, I create a new timer-owning function each time. This can break the intended shared timer behavior. I preserve the instance with `useMemo` or `useRef`.

## 23. How can useMemo help?

I can use `useMemo` to create a debounced or throttled function and preserve that instance between renders until its dependencies change. I also need cleanup for pending timers.

## 24. How can useRef help?

A ref can preserve mutable data without triggering a render. I can use it to store the latest callback or a timer ID. This is useful when a stable scheduled function needs to access the latest callback logic.

## 25. How do you debounce a search input in React?

I update the input state immediately, then schedule the search using a stable debounced callback or derive a debounced value with a custom Hook. I cancel pending timers during cleanup. For real requests, I also handle errors, cancellation, and response ordering.

## 26. What is a debounced value hook?

It is a custom Hook that returns a value only after it has remained unchanged for a specified delay. It is useful when an Effect should react to a settled value rather than every immediate input update.

```jsx
function useDebouncedValue(value, delay) {
  const [result, setResult] = useState(value);

  useEffect(() => {
    const id = setTimeout(() => setResult(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);

  return result;
}
```

## 27. Explain cleanup in a debounced value hook.

Whenever `value` or `delay` changes, React runs the previous Effect cleanup before running the new Effect setup. The cleanup clears the old timer, so only the latest scheduled value can be committed after its delay.

## 28. Why should pending timers be cancelled on unmount?

The component may no longer need the delayed work. Cancelling prevents obsolete callbacks from running after unmount and avoids unnecessary work. It also makes lifecycle behavior predictable.

## 29. Does removing an event listener cancel its timer?

No. Removing a listener prevents future events from invoking that listener. If an earlier event already scheduled a timeout, that timeout remains pending unless it is explicitly cancelled.

## 30. What is a stale closure?

A stale closure occurs when a callback uses state or props captured from an older render. A debounced function created once can keep referring to old values if it reads them directly.

## 31. How can stale closures be avoided?

I can pass the latest value as an argument to the debounced function. If the stable callback must read the latest callback logic, I can keep the latest callback in a ref and invoke `callbackRef.current`.

## 32. Does debounce cancel an in-flight API request?

No. Debounce delays when a request starts. Once a request has started, debounce does not cancel it. I need request cancellation, such as `AbortController`, for that.

## 33. How do you cancel an in-flight fetch?

I can create an `AbortController`, pass its `signal` to `fetch`, and call `controller.abort()` when the request is no longer needed.

```js
const controller = new AbortController();

fetch("/api/search?q=react", { signal: controller.signal });

// When the request is obsolete:
controller.abort();
```

## 34. How do you avoid out-of-order search responses?

I can abort the previous request when a new query starts. I can also use a request ID or sequence number and apply a response only if it belongs to the latest query. Debouncing alone does not guarantee response order.

## 35. What is the difference between debounce and request cancellation?

Debounce controls when work begins. Request cancellation stops work that has already started, when the underlying API supports cancellation. They solve different parts of an API-search problem and can be used together.

## 36. What is requestAnimationFrame?

`requestAnimationFrame` asks the browser to run a callback before a future repaint. It is useful for coordinating visual updates with the browser's rendering cycle.

## 37. How is requestAnimationFrame different from throttle?

Throttle enforces a time-based execution limit. `requestAnimationFrame` schedules work around the browser's next repaint. For visual scroll effects, animation-frame scheduling may be more suitable; for limiting API calls, throttle or debounce is more appropriate.

## 38. What is a common mistake when using removeEventListener?

Creating a new anonymous function during removal. The function reference must match the one used during registration, and relevant options must also match.

## 39. What happens if delay changes?

If the delay is a dependency of a memoized utility, React creates a new utility when the delay changes. The old utility's pending timer should be cancelled during cleanup. The exact behavior depends on the implementation.

## 40. How does React Strict Mode affect timer-related Effects?

In development, Strict Mode can run an extra setup-and-cleanup cycle for Effects to reveal missing cleanup. Correct Effects should remove listeners and cancel pending timers so this extra cycle does not leave duplicate work.

## 41. What should a debounce utility's cancel method do?

It should clear the pending timeout and reset its timer ID. Calling it should ensure that the scheduled callback does not run.

## 42. How would you test debounce?

I would use fake timers. I would verify that the callback does not run before the delay, that repeated calls reset the timer, that only the latest arguments are used, and that cancellation prevents execution.

## 43. How would you test throttle?

I would use fake timers or controlled timestamps to verify the exact permitted execution times. I would test the intended leading and trailing behavior, including calls inside the interval.

## 44. What is maxWait?

`maxWait` is an option in some debounce implementations that sets a maximum time to wait before invoking the callback, even if calls keep arriving. It prevents continuous activity from postponing execution indefinitely. It is not part of every basic debounce implementation.

## 45. What is the difference between leading-only and trailing-only throttle?

A leading-only throttle executes at the start of an allowed interval and ignores calls during the rest of that interval. A trailing-only throttle schedules an execution at the end of the interval. A combined implementation can support both.

## 46. Can debounce improve performance by itself?

It can reduce how often expensive work starts, which may improve responsiveness and reduce network traffic. It does not optimize the callback's internal algorithm or guarantee that the callback is inexpensive.

## 47. Should every input use debounce?

No. Inputs that need immediate local feedback should update immediately. I debounce only the expensive side effect, such as a server search or costly validation, rather than delaying the visible input value unnecessarily.

## 48. How would you debounce autosave?

I would update the editor state immediately and schedule a save after the user pauses. I would cancel an obsolete pending timer when new edits arrive and handle network errors and save ordering. I would also consider flushing or saving on navigation if the product requires it.

## 49. How would you throttle a resize handler?

I would register one stable throttled handler in an Effect, use it to perform the required resize calculation at a controlled rate, and remove the exact same handler in cleanup. If the work is visual, I would consider `requestAnimationFrame`.

## 50. What would you choose for a button that must not be submitted repeatedly?

I would first use application state to disable the button while the submission is pending and make the server operation safe against duplicate submissions where necessary. Throttling or debouncing can reduce repeated clicks, but neither replaces correct submission state or server-side protection.

## 51. What is the output of this debounce example?

```js
const log = debounce((value) => console.log(value), 300);

log("A");
log("B");
log("C");
```

Assuming all three calls happen within 300 ms of one another and no later call occurs, the output is `C` after approximately 300 ms from the final call.

## 52. What is wrong with this React code?

```jsx
function Search() {
  const search = debounce((value) => fetchResults(value), 300);

  return <input onChange={(event) => search(event.target.value)} />;
}
```

The debounced function is recreated on every render. I would preserve it across renders with `useMemo` or a ref, and cancel pending work during cleanup. I would also handle request errors and stale responses.

## 53. Why might a debounced callback use old state?

Because the callback may have captured state from the render in which it was created. I can pass the current value as an argument or use a ref that is updated with the latest callback.

## 54. Does a simple throttle queue suppressed calls?

Not necessarily. The timestamp-based leading-only implementation shown in this chapter ignores calls inside the interval. A trailing throttle is a different implementation that schedules a later call.

## 55. When should a utility library be preferred?

When the project needs well-tested advanced behavior such as leading and trailing options, `maxWait`, cancellation, flushing, or consistent cross-project semantics. A small custom utility is reasonable when requirements are simple and clearly tested.

## 56. Can debounce and throttle be combined?

Yes, but I would do so only when the interaction needs both behaviors. For example, a system may need periodic updates during continuous activity and a final update after activity stops. The timing contract should be clearly defined.

## 57. What are the trade-offs of a long debounce delay?

It reduces how often the callback runs, but it makes the user wait longer for results. A very short delay can still generate many requests. I choose a delay based on the interaction and test the user experience.

## 58. How would you explain both techniques in a real project?

For a product search field, I would debounce the API search so it runs after the user pauses typing. For a scroll-progress indicator, I might throttle updates so they happen periodically during scrolling. In both cases, I would preserve the handler instance and clean up timers or listeners.

## 59. What are the key React best practices?

I preserve timer-owning functions across renders, avoid stale closures, pass current values explicitly when possible, cancel pending timers in cleanup, remove event listeners using the same reference, and manage in-flight requests separately from debounce.

## 60. Give a concise interview summary.

Debouncing waits until calls stop for a configured delay, while throttling limits how often a function executes during repeated events. I use debounce for search and autosave, and throttle for continuous events such as scroll tracking. In React, I keep the utility stable across renders, clean up timers and listeners, and handle stale closures and API request cancellation separately.
