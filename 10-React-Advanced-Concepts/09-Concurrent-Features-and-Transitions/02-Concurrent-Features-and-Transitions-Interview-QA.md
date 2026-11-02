# Concurrent Features and Transitions — Interview Questions and Answers

## Table of Contents
1. [What is concurrent rendering?](#q1-what-is-concurrent-rendering)
2. [What is useTransition?](#q2-what-is-usetransition)
3. [What does isPending represent?](#q3-what-does-ispending-represent)
4. [What is useDeferredValue?](#q4-what-is-usedeferredvalue)
5. [useDeferredValue vs debounce?](#q5-usedeferredvalue-vs-debounce)
6. [Does a transition make computation faster?](#q6-does-a-transition-make-computation-faster)
7. [What is useOptimistic?](#q7-what-is-useoptimistic)
8. [When should an update remain urgent?](#q8-when-should-an-update-remain-urgent)
9. [useTransition vs startTransition?](#q9-usetransition-vs-starttransition)
10. [What happens if a transition is interrupted?](#q10-what-happens-if-a-transition-is-interrupted)

## Q1. What is concurrent rendering?
**Interview answer:** Concurrent rendering describes React's ability to prepare rendering work in a way that can be interrupted, restarted, or prioritized. It helps keep urgent interactions responsive while React works on less urgent UI. It does not mean component code automatically runs on multiple threads.

## Q2. What is useTransition?
**Interview answer:** `useTransition` lets me mark state updates as non-urgent. It returns a `startTransition` function and an `isPending` flag that I can use to show that the transition is still in progress.

## Q3. What does isPending represent?
**Interview answer:** `isPending` indicates that a transition started by that Hook is pending. I can use it to show a subtle loading indicator or disable an action when appropriate.

## Q4. What is useDeferredValue?
**Interview answer:** `useDeferredValue` gives me a version of a value that can lag behind the latest value. I use it when an expensive part of the UI should update less urgently than the input that drives it.

## Q5. useDeferredValue vs debounce?
**Interview answer:** Debounce is a time-based JavaScript technique that waits for a quiet period. `useDeferredValue` is a React scheduling feature that defers rendering work based on priority. It does not guarantee a fixed delay and does not automatically limit API requests.

## Q6. Does a transition make computation faster?
**Interview answer:** No. It does not reduce the algorithm's complexity. It lets React schedule the update as non-urgent so more important interactions can be handled first.

## Q7. What is useOptimistic?
**Interview answer:** `useOptimistic` lets the UI show an expected state while an asynchronous action is being completed. For example, a new comment can appear immediately with a sending indicator. The application still needs to handle server success or failure.

## Q8. When should an update remain urgent?
**Interview answer:** Direct interaction feedback, such as the value of a controlled text input, should normally remain urgent. I can defer the expensive results or secondary UI update instead.

## Q9. useTransition vs startTransition?
**Interview answer:** Both mark updates as transitions. `useTransition` is a Hook that also provides an `isPending` value for the component. The standalone `startTransition` API marks updates without providing that Hook-specific pending state.

## Q10. What happens if a transition is interrupted?
**Interview answer:** React can pause or abandon the in-progress transition work to handle a more urgent update, then continue with the latest relevant work. This is one reason render logic must be pure.
