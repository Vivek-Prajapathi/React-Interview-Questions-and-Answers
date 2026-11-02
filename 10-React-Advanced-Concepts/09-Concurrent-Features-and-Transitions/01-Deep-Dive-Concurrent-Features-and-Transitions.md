# Concurrent Features, Transitions, and Deferred Values

## 1. Urgent and non-urgent updates

Some updates need immediate feedback, such as typing into an input. Other updates may be expensive, such as rendering a large filtered result list. React's concurrent rendering capabilities let it prioritize urgent work and keep the interface responsive.

Concurrent rendering is a scheduling capability, not a promise that every render runs in parallel on another thread.

## 2. useTransition

`useTransition` returns `isPending` and `startTransition`. Updates scheduled inside `startTransition` are marked as non-urgent.

```jsx
const [isPending, startTransition] = useTransition();

function handleChange(event) {
  const nextValue = event.target.value;
  setInputValue(nextValue); // urgent

  startTransition(() => {
    setSearchQuery(nextValue); // non-urgent
  });
}
```

The input remains responsive while React works on the expensive result update. Transition updates can be interrupted by more urgent updates.

## 3. useDeferredValue

`useDeferredValue` returns a deferred version of a value. The UI that uses the deferred value can lag behind the latest input while urgent updates proceed.

```jsx
const deferredQuery = useDeferredValue(query);

const results = useMemo(
  () => expensiveSearch(items, deferredQuery),
  [items, deferredQuery]
);
```

It does not debounce by a fixed number of milliseconds and does not prevent network requests by itself. It defers rendering work according to React scheduling.

## 4. useOptimistic

`useOptimistic` helps display an optimistic UI while an action is in progress. The interface can show the expected result immediately while the actual operation completes.

```jsx
const [optimisticMessages, addOptimisticMessage] =
  useOptimistic(messages, (current, newMessage) => [
    ...current,
    { ...newMessage, sending: true }
  ]);
```

The application must still perform the real operation and handle failure or reconciliation with the server result.

## 5. startTransition

The standalone `startTransition` API marks state updates as transitions when a Hook instance is not needed. `useTransition` additionally exposes pending status for a component.

Do not put the controlled input's urgent state update inside a transition if it makes typing lag.

## 6. Common misconceptions

- A transition does not make a slow algorithm faster.
- `useDeferredValue` is not a time-based debounce.
- Transitions are not for every state update.
- Optimistic UI still needs error handling and server reconciliation.
- Pending indicators should communicate meaningful work without making the UI feel blocked.
