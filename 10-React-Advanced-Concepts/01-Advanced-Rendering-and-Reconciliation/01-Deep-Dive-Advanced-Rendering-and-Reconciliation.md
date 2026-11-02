# Advanced Rendering and Reconciliation

## 1. What rendering means in React

A React component is a function that describes UI for the current props and state. When React renders a component, it calls the component to calculate the next element tree. Rendering is not the same as updating the browser DOM. React first calculates what the UI should look like; during the commit phase it applies the necessary changes to the host environment.

A render can happen because:
- The component's state changes.
- Its parent renders and React evaluates the child again.
- A consumed Context value changes.
- An external store subscription reports a change.

A render does not guarantee that the DOM will change. If the resulting output is equivalent, React may have little or no DOM work to commit.

## 2. Render and commit phases

**Render phase:** React calls components, evaluates hooks, and determines the next UI. This phase should be pure: do not mutate external values, make network requests, or manually change the DOM during render. React may pause, restart, or discard render work.

**Commit phase:** React applies the completed changes to the DOM. Layout effects run synchronously after DOM mutations and before the browser paints. Passive effects (`useEffect`) generally run after the browser has had an opportunity to paint.

This distinction explains why side effects belong in event handlers or effects, not in the component body.

## 3. Reconciliation

Reconciliation is React's process for comparing the previous element description with the next one and deciding what needs to change. React uses element type, position, and keys to match children.

- Same component type at the same position: React can usually preserve its state and update its props.
- Different component type: React generally removes the previous subtree and mounts the new one.
- Different key: React treats the element as a different identity, resetting its state.
- Stable keys in lists help React match items when order changes.

Keys are not passed as ordinary props. If a child needs an ID, pass it separately.

## 4. Why keys matter

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <TodoItem key={todo.id} todo={todo} />
      ))}
    </ul>
  );
}
```

Using an array index as a key can cause state to appear on the wrong item after insertion, deletion, or sorting. Use an index only for a truly static list whose order and membership never change.

## 5. Component identity and state preservation

React associates state with a component's position and identity in the render tree. Moving a component to a different position or changing its key can reset state. This is useful when switching between records that should not share form state:

```jsx
<ProfileEditor key={selectedUser.id} user={selectedUser} />
```

When `selectedUser.id` changes, the editor is remounted with fresh local state.

## 6. Strict Mode

In development, Strict Mode intentionally performs additional checks. In React versions that use the modern Strict Mode behavior, React may call render logic more than once and run an extra effect setup/cleanup cycle to reveal impure rendering and missing cleanup. This is a development diagnostic, not a production double-render guarantee.

Do not silence Strict Mode symptoms with arbitrary flags. Make effects safe to set up and clean up, and keep rendering pure.

## 7. Common misconceptions

- A render does not mean every DOM node is recreated.
- `useMemo` is not a general command to stop rendering.
- Keys identify siblings; they are not globally unique IDs.
- State updates are scheduled and React may batch them.
- Rendering should be pure because React can restart render work.

## 8. Debugging checklist

1. Identify which state, prop, or Context value changed.
2. Use React DevTools Profiler to inspect commits and component render durations.
3. Check whether a parent render is causing child evaluation.
4. Check keys and component type stability.
5. Move side effects out of render.
6. Optimize only after measuring a real bottleneck.
