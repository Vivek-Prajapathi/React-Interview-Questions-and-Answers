# Debouncing and Throttling — Interview Questions and Answers

## Table of Contents
1. [What is debouncing?](#q1-what-is-debouncing)
2. [What is throttling?](#q2-what-is-throttling)
3. [What is the difference between debounce and throttle?](#q3-what-is-the-difference-between-debounce-and-throttle)
4. [How would you implement debounced search in React?](#q4-how-would-you-implement-debounced-search-in-react)
5. [Why should a debounce timer be cleaned up?](#q5-why-should-a-debounce-timer-be-cleaned-up)
6. [Does debouncing prevent stale API responses?](#q6-does-debouncing-prevent-stale-api-responses)
7. [When would you use throttling in React?](#q7-when-would-you-use-throttling-in-react)
8. [Why should the input update immediately?](#q8-why-should-the-input-update-immediately)
9. [What is a trailing-edge debounce?](#q9-what-is-a-trailing-edge-debounce)
10. [What edge cases should a reusable throttle handle?](#q10-what-edge-cases-should-a-reusable-throttle-handle)

## Q1. What is debouncing?
**Interview answer:** Debouncing delays a callback until a certain amount of time has passed without another event. Every new event resets the timer. I use it for search inputs so an API request happens after the user pauses typing instead of on every keystroke.

## Q2. What is throttling?
**Interview answer:** Throttling limits how often a callback can run during continuous events. For example, I can process scroll position at most once every 100 milliseconds rather than on every scroll event.

## Q3. What is the difference between debounce and throttle?
**Interview answer:** Debounce waits until events stop for the configured delay. Throttle allows execution at a controlled frequency while events continue. I choose debounce for search and throttle for continuous scroll or pointer tracking.

## Q4. How would you implement debounced search in React?
**Interview answer:** I keep the input controlled and update its state immediately. Then I derive a debounced query using a timer in an effect, clean up the previous timer, and trigger the request when the debounced value changes. I also handle loading, errors, and stale requests.

## Q5. Why should a debounce timer be cleaned up?
**Interview answer:** Cleanup prevents an old timer from firing after a newer value has arrived or after the component unmounts. Without cleanup, it can cause outdated work or attempt to update a component that is no longer relevant.

## Q6. Does debouncing prevent stale API responses?
**Interview answer:** No. Debouncing reduces how many requests are started, but requests can still finish out of order. I use `AbortController` or a request identity check so an older response cannot overwrite newer results.

## Q7. When would you use throttling in React?
**Interview answer:** I use throttling when I need periodic updates during continuous activity, such as tracking scroll position, resizing, or pointer movement. If I only need to run after the activity stops, I choose debounce instead.

## Q8. Why should the input update immediately?
**Interview answer:** The user expects typing to respond instantly. I debounce the expensive side effect, not the controlled input state. That keeps the UI responsive while reducing unnecessary work.

## Q9. What is a trailing-edge debounce?
**Interview answer:** A trailing-edge debounce runs the callback after the event stream has been quiet for the configured delay. The latest event's arguments are used. A leading-edge option instead runs at the start, and some utilities support both.

## Q10. What edge cases should a reusable throttle handle?
**Interview answer:** I would decide whether it supports leading and trailing execution, whether it can be cancelled or flushed, how it handles changing callbacks, and whether it preserves the latest arguments. I would also ensure timers are cleaned up when the component no longer needs them.
