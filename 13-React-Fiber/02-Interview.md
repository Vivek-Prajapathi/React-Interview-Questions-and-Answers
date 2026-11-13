# React Fiber: Interview Questions and Answers

Every answer is written in simple spoken English. You can read it almost word for word to an interviewer.

**How to use this file:** Each answer starts with a short **"Say this first"** line (the quick answer), followed by **"If they ask more"** (the deeper explanation). Start with the short line, and go deeper only if the interviewer wants more.

---

## Table of Contents

- [Basics](#basics)
- [Architecture](#architecture)
- [Render and Commit](#render-and-commit)
- [Reconciliation and Keys](#reconciliation-and-keys)
- [Hooks and Fiber](#hooks-and-fiber)
- [Priorities and Concurrent Features](#priorities-and-concurrent-features)
- [Tricky and Scenario Questions](#tricky-and-scenario-questions)
- [One-Minute Summary to Memorize](#one-minute-summary-to-memorize)

---

# Basics

### Q1. What is React Fiber?

**Say this first:**
Fiber is the engine inside React that decides what to change on the screen and when to change it. It is a rewrite of React's core algorithm, introduced in React 16, that lets React split rendering work into small pieces and pause between them.

**If they ask more:**
The word "fiber" has two meanings. First, a fiber is a plain JavaScript object that represents one component or one DOM element, and it stores the work React needs to do for it. Second, "Fiber" is the whole system: all those objects connected together, plus the algorithm that walks through them.

---

### Q2. Why was Fiber created? What problem did it solve?

**Say this first:**
The old React could not pause. Once it started updating a big tree, it had to finish everything in one go, which froze the page. Fiber was created so React can break the work into small chunks and let the browser breathe in between.

**If they ask more:**
The old reconciler used normal recursion. Recursion uses the JavaScript call stack, and you cannot stop a call stack in the middle and continue later. JavaScript has only one thread, so while React was busy, the browser could not handle typing, clicking, or animations. If an update took 200 ms, the page felt frozen for 200 ms.

Fiber replaced recursion with a loop over objects. A loop can stop after any step, because "where I am now" is saved in a variable instead of being hidden in the call stack. That one change is what makes smooth UI, priorities, Suspense, and transitions possible.

---

### Q3. What was the old reconciler called, and how is Fiber different?

**Say this first:**
The old one was called the Stack Reconciler. It used the call stack and recursion, so it was all-or-nothing. Fiber uses a linked structure and a loop, so work can be paused, resumed, or even thrown away.

**If they ask more:**
Here is the difference in a picture:

```
Stack reconciler:  [-------- one long uninterruptible task --------]
Fiber:             [work][work] pause [work][work] pause [work] done
```

Fiber also added priorities, so an urgent update like typing can go ahead of a slow, less important update.

---

### Q4. Is Fiber the same as the Virtual DOM?

**Say this first:**
No. The Virtual DOM is the idea of describing the UI as plain objects (React elements) and comparing them instead of touching the real DOM. Fiber is the actual implementation that does the comparing and scheduling. The fiber tree is more than a virtual DOM, because it also stores state, priorities, and the work to be done.

**If they ask more:**
When your component returns JSX, you get lightweight React elements, which are descriptions like "a div with this text". React turns those descriptions into fibers, compares them with the old fibers, and decides what to change in the real DOM. So elements are the input, and fibers are the working structure that React uses internally.

---

### Q5. What is a fiber, technically?

**Say this first:**
A fiber is a JavaScript object that represents one unit of work. There is one fiber for each component and each DOM element in your app.

**If they ask more:**
A fiber stores three kinds of information:

1. **What it is:** `type` (the component function or `'div'`), `key`, and `stateNode` (the real DOM node or class instance).
2. **Where it is:** `return` (parent), `child` (first child), `sibling` (next sibling).
3. **Work info:** `pendingProps`, `memoizedProps`, `memoizedState`, `flags` (what must be done to the DOM), and `lanes` (how urgent the work is).

It also has `alternate`, which points to its twin in the other tree. I explain that later.

---

# Architecture

### Q6. How are fibers connected to each other? Why not just use an array of children?

**Say this first:**
Fibers are connected with three pointers: `child`, `sibling`, and `return`. This makes the tree behave like a linked list, so React can move to the next piece of work using only the current fiber, without recursion.

**If they ask more:**
- `child` points to the first child.
- `sibling` points to the next brother or sister.
- `return` points back to the parent.

To find the next work, React follows a simple rule. If the fiber has a child, go down. If not, but it has a sibling, go right. If neither, go up using `return` and try the sibling there. React only needs to remember one thing: the fiber it is on right now. That is exactly what makes pausing and resuming easy.

---

### Q7. What are the "current" tree and the "workInProgress" tree?

**Say this first:**
React keeps two fiber trees. The current tree is what is on the screen right now. The workInProgress tree is a draft of the next screen that React builds in the background. When the draft is ready, React swaps them.

**If they ask more:**
Each fiber in one tree points to its twin in the other tree using the `alternate` field. React never edits the current tree during rendering. That is important: if React is interrupted halfway, the user never sees a half-finished screen, because the screen is still showing the safe, complete current tree.

When the draft is finished and written to the DOM, React just changes one pointer:

```js
root.current = finishedWorkInProgress;
```

The old current tree then becomes the spare, and React reuses its objects for the next draft to save memory. This technique is called **double buffering**, like a game that draws the next frame off-screen and then swaps.

---

### Q8. What is `alternate`?

**Say this first:**
`alternate` is the link between a fiber in the current tree and its twin in the workInProgress tree. Each fiber has exactly one alternate.

**If they ask more:**
On the first render, there is only the current fiber. On an update, React creates the twin and links both ways: `current.alternate = wip` and `wip.alternate = current`. On later updates, React does not create new objects. It reuses the old twin and just resets its values. This avoids creating lots of garbage and keeps rendering fast.

---

### Q9. How does Fiber walk through the tree without recursion?

**Say this first:**
It uses a loop with a variable called `workInProgress`. Each turn of the loop processes one fiber and then moves the variable to the next fiber using the child, sibling, and return pointers.

**If they ask more:**
In simple code it looks like this:

```js
while (workInProgress !== null) {
  performUnitOfWork(workInProgress);
}

function performUnitOfWork(fiber) {
  const next = beginWork(fiber);          // process this fiber, go down
  if (next) {
    workInProgress = next;                // has a child, continue down
  } else {
    completeUnitOfWork(fiber);            // no child, start going up
  }
}
```

`completeUnitOfWork` finishes the fiber, then goes to its sibling if there is one, or goes up to the parent. One fiber is one "unit of work". A parent is begun before its children and completed after all its children.

---

### Q10. How can React pause and resume rendering?

**Say this first:**
Because the position is stored in a variable (`workInProgress`), not in the call stack. To pause, React just exits the loop. To resume, it enters the loop again with the same variable.

**If they ask more:**
For non-urgent updates React uses a loop that checks the time after every fiber:

```js
while (workInProgress !== null && !shouldYield()) {
  performUnitOfWork(workInProgress);
}
```

`shouldYield()` returns true when React has used about 5 milliseconds. Then the loop stops and the browser gets a turn to handle clicks and paint. React asks its Scheduler to continue soon (the Scheduler uses `MessageChannel` so the browser can do its own work in between), and the loop continues from where it stopped. This is called **time slicing**.

One limit to mention: React can pause between fibers, but not in the middle of one component function. If a single component takes 100 ms to run, it still blocks.

---

### Q11. What is time slicing?

**Say this first:**
Time slicing means React does rendering work in small chunks of about 5 ms and gives control back to the browser between chunks, so the page stays responsive.

**If they ask more:**
Without it, one big render is a single long task that blocks the main thread. With it, the work looks like this:

```
|--5ms work--| browser paints / handles input |--5ms work--| browser |--work--| done
```

Time slicing is used for concurrent (non-urgent) renders. Urgent updates such as a click handler's state update are rendered synchronously, without yielding, so they feel instant.

---

# Render and Commit

### Q12. What are the two phases of an update in React?

**Say this first:**
The render phase and the commit phase. In the render phase React figures out what changed. In the commit phase it applies those changes to the real DOM.

**If they ask more:**

| | Render phase | Commit phase |
|---|---|---|
| Job | Call components, compare, mark changes | Update the DOM, run effects |
| Can be paused or restarted? | Yes | No, runs in one go |
| Can run more than once for one update? | Yes | No |
| Touches the real DOM? | No | Yes |

---

### Q13. Why must components be "pure"?

**Say this first:**
Because in the render phase React may call your component many times, pause it, or throw the result away completely. If your component does side effects while rendering, such as changing the DOM or calling an API, those effects would happen at the wrong time or more than once.

**If they ask more:**
This is why side effects belong in event handlers or in `useEffect`, which only run in the commit phase. It is also why React StrictMode in development intentionally renders components twice: to help you catch impure code.

---

### Q14. What happens inside `beginWork`?

**Say this first:**
`beginWork` is the "going down" step. It does the main work for one fiber and creates or updates the fiber's children.

**If they ask more:**
- For a **function component**, it calls your function (this is when your component actually runs), takes the JSX you returned, and reconciles it into child fibers.
- For a **class component**, it calls `render()`.
- For a **DOM element** like `div`, it reconciles the children.
- If **nothing changed** (same props, no pending state update), it takes a shortcut called a **bailout** and skips the fiber, and often the whole subtree below it.

---

### Q15. What happens inside `completeWork`?

**Say this first:**
`completeWork` is the "coming back up" step. It runs for a fiber after all its children are done. For DOM fibers it prepares the real DOM node in memory, without putting it on the page yet.

**If they ask more:**
- For a **new** DOM element, it creates the node (`document.createElement`) and attaches the children's DOM nodes to it.
- For an **existing** DOM element, it compares old and new props and records what needs to change.
- It also **bubbles up flags**: it collects the `flags` of all children into `subtreeFlags` on the parent. This lets the commit phase quickly skip branches that have no changes.

---

### Q16. What are flags (effect tags)?

**Say this first:**
Flags are small markers on a fiber that say what must be done to it in the commit phase. Examples are Placement (insert or move), Update (change props), and ChildDeletion (remove a child).

**If they ask more:**
The render phase does not touch the DOM. It only writes these flags, like making a to-do list. Then the commit phase reads the flags and does the real DOM work. Flags are stored as bits, so many can be combined on one fiber:

```js
fiber.flags |= Placement;
fiber.flags |= Update;
```

---

### Q17. Walk me through the commit phase.

**Say this first:**
The commit phase runs in one go and has three steps: before mutation, mutation, and layout. After that, passive effects like `useEffect` run later.

**If they ask more:**
1. **Before mutation:** React reads from the DOM before changing it, for example `getSnapshotBeforeUpdate`.
2. **Mutation:** React performs the real DOM work: inserts, updates, and deletes nodes. It also runs the cleanup functions of deleted components.
3. **Swap:** `root.current` is set to the finished tree. The draft becomes the current tree.
4. **Layout:** React runs `useLayoutEffect` and `componentDidMount/DidUpdate`. The DOM is now updated, but the browser has not painted yet.
5. **Passive effects:** `useEffect` callbacks run, normally after the browser has painted.

---

### Q18. Why is the tree swapped between the mutation and layout steps?

**Say this first:**
Because each step needs to see a different tree. Cleanup of deleted components must see the old tree, and layout effects of new components must see the new tree.

**If they ask more:**
The mutation step still deals with things being removed, so the current tree should still be the old one. By the time layout effects run, the DOM is already updated, so `root.current` should already point to the new tree. That is why the pointer switches exactly between the two.

---

### Q19. What is the difference between `useEffect` and `useLayoutEffect`?

**Say this first:**
`useLayoutEffect` runs right after the DOM is updated but before the browser paints, and it blocks painting. `useEffect` runs after the browser has painted, so it does not block the screen.

**If they ask more:**
Use `useLayoutEffect` when you must measure or change the DOM before the user sees it, for example reading an element's size to position a tooltip, so there is no flicker. Use `useEffect` for almost everything else, such as fetching data or subscriptions, because it keeps the UI fast.

---

### Q20. Can the commit phase be interrupted?

**Say this first:**
No. The commit phase is synchronous and runs from start to finish. Only the render phase can be paused.

**If they ask more:**
If the commit could stop in the middle, the user could see a half-updated DOM. The commit is also fast, because the heavy comparing work was already done in the render phase. The commit only has to apply the prepared changes.

---

# Reconciliation and Keys

### Q21. What is reconciliation?

**Say this first:**
Reconciliation is how React compares the old UI with the new UI to find the smallest set of changes needed. In Fiber, it is done by comparing the old child fibers with the new React elements your component returned.

**If they ask more:**
The key point is that reconciliation does not change the DOM. It only creates or reuses fibers and sets flags on them. The DOM is changed later in the commit phase.

---

### Q22. What are the rules React uses when comparing?

**Say this first:**
There are three main rules. Same type and same key means reuse the fiber and update it. Different type means destroy the old one and build a new one. For lists, React uses `key` to match items.

**If they ask more:**
- **Same type, same key:** React keeps the fiber and the real DOM node, and only updates changed props. State is preserved.
- **Different type:** For example `<div>` changes to `<span>`. React removes the old fiber and everything under it (state is lost) and creates a new one.
- **Lists:** React matches old and new items by key, so it can move items instead of recreating them.

---

### Q23. Why do we need keys in lists?

**Say this first:**
Keys give each list item a stable identity. Without keys, React compares items by position, so inserting or reordering items makes React think many items changed. With keys, React knows which item is which and can just move them.

**If they ask more:**
Here is how React matches by key:

```
before: [A(key=a), B(key=b), C(key=c)]
after:  [C(key=c), A(key=a), B(key=b)]
```

1. React puts the old fibers in a map by key.
2. For each new item, it finds the old fiber with the same key and reuses it.
3. It tracks the highest old position it has placed so far. If a reused item's old position is lower than that, it must be moved.
4. Old fibers that were not matched are deleted.

Bad keys cause real bugs. If you use the array index as a key and the list gets reordered, an item's state such as input text can jump to the wrong item.

---

### Q24. What happens to state when the component type changes at the same position?

**Say this first:**
The state is lost. When the type changes, React unmounts the old component and mounts a new one, which starts fresh.

**If they ask more:**
You can use this on purpose. Giving a component a different `key` forces React to treat it as a new component and reset its state. This is a common way to reset a form when the user switches to another record.

---

### Q25. What is a bailout?

**Say this first:**
A bailout is when React skips work for a fiber because nothing changed, and often skips its whole subtree.

**If they ask more:**
React skips a fiber when its props are the same as before and it has no pending update on itself. It then checks `childLanes` to see if any descendant has work. If no descendant has work, the whole subtree is skipped. If some descendant does, React clones the children and keeps going down only to find them. This is how React avoids re-rendering the entire app for a small change.

---

# Hooks and Fiber

### Q26. Where is a function component's state stored?

**Say this first:**
On its fiber, in the field `memoizedState`. For hooks, it is a linked list of hook objects.

**If they ask more:**
Each hook call creates one hook object, and they are chained in the order you called them:

```
Counter fiber
  memoizedState -> [hook 1: useState] -> [hook 2: useState] -> [hook 3: useEffect]
```

---

### Q27. How does React know which state belongs to which `useState` call?

**Say this first:**
By call order. React does not know variable names. The first `useState` call gets the first hook object, the second call gets the second, and so on.

**If they ask more:**
During render, React keeps a pointer to the current hook and moves it to the next hook every time you call a hook. This is exactly why the Rules of Hooks exist.

---

### Q28. Why can't we call hooks inside `if` statements or loops?

**Say this first:**
Because hooks are matched by order. If a hook is skipped in one render, every hook after it shifts position and gets the wrong data.

**If they ask more:**

```jsx
if (isOpen) {
  const [x, setX] = useState(0);   // sometimes the 1st hook, sometimes missing
}
const [y, setY] = useState(1);     // sometimes the 1st hook, sometimes the 2nd -> wrong!
```

The list of hooks must be exactly the same, in the same order, on every render.

---

### Q29. What exactly happens when I call `setState`?

**Say this first:**
It does not change the state immediately. It adds an update to a queue, marks the fiber and its ancestors as having work, and asks React to schedule a re-render. The new state is calculated during the next render.

**If they ask more:**
1. `setState` creates an update object `{ action, lane }` and puts it in the hook's queue.
2. It walks up from the fiber to the root using `return`, marking `lanes` on the fiber and `childLanes` on every ancestor.
3. It asks the root to schedule a render at the right priority.
4. During the render, `useState` reads the queue, applies each update in order, and returns the new value.

This is why you cannot read the new value on the very next line after calling `setState`.

---

### Q30. Why does `setCount(count + 1)` three times add only 1, but `setCount(c => c + 1)` three times adds 3?

**Say this first:**
Because `count` is a fixed value for that render. The first form uses the same old value three times. The function form receives the latest value from the queue each time.

**If they ask more:**
With `setCount(count + 1)` where count is 0, the queue holds "set to 1" three times, so the result is 1. With `setCount(c => c + 1)`, the queue holds three functions. React runs them one after another: 0 to 1, 1 to 2, 2 to 3. The final result is 3.

---

### Q31. Does React batch state updates? How does Fiber help?

**Say this first:**
Yes. If you call `setState` several times in the same event, React collects the updates and renders only once. In React 18 and later this batching happens automatically everywhere, including in timeouts and promises.

**If they ask more:**
Each `setState` just adds an update to a queue and marks lanes. Nothing renders yet. After your code finishes, React starts one render that processes all the queued updates together. That is why several `setState` calls cost only one render.

---

# Priorities and Concurrent Features

### Q32. What are lanes?

**Say this first:**
Lanes are how Fiber represents priority. Each lane is one bit in a binary number, and different kinds of updates use different lanes. Lower bits mean higher priority.

**If they ask more:**
Examples, from most to least urgent: SyncLane (clicks and discrete events), InputContinuousLane (dragging, scrolling), DefaultLane (normal updates), TransitionLanes (`startTransition`), and IdleLane (lowest).

Bits are used because bit operations are very fast and one number can hold several lanes at once, for example `fiber.lanes = SyncLane | DefaultLane`. React checks for work with a simple `lanes & SomeLane`.

---

### Q33. What are `lanes` and `childLanes` on a fiber?

**Say this first:**
`lanes` says "this fiber itself has pending work at these priorities." `childLanes` says "somewhere below me there is pending work at these priorities."

**If they ask more:**
When `setState` is called on a deep component, React marks `lanes` on that component and `childLanes` on all its ancestors up to the root. During rendering, if a fiber has no `lanes` but has `childLanes`, React does not re-run it but still goes down to find the component that needs work. If there are no `childLanes` either, it skips the whole subtree. This is how React finds the exact component to update without checking everything.

---

### Q34. How does React handle an urgent update arriving while it is busy with a slow one?

**Say this first:**
React throws away the half-finished draft, renders the urgent update first, and then starts the slow one again from the beginning.

**If they ask more:**
Example: a slow list filter is rendering as a transition, and the user types a letter.

1. The transition render is in progress.
2. A high-priority update arrives.
3. React discards the unfinished workInProgress tree.
4. It renders and commits the urgent update (typing stays instant).
5. It restarts the slow render.

Throwing away the draft is safe because the render phase has no side effects and the current tree was never touched. This is the main benefit of the two-tree design.

---

### Q35. What is `useTransition` and how does it relate to Fiber?

**Say this first:**
`useTransition` lets you mark a state update as non-urgent. Fiber gives it a low-priority lane, so React renders it in small slices and can interrupt it if something more urgent comes.

**If they ask more:**

```jsx
const [isPending, startTransition] = useTransition();

function onChange(e) {
  setText(e.target.value);              // urgent: input updates instantly
  startTransition(() => {
    setFilter(e.target.value);          // not urgent: can be interrupted
  });
}
```

The input feels instant because the typing update has a high-priority lane. The heavy list update has a transition lane, so it never blocks typing. `isPending` tells you when the transition is still working, so you can show a small loading hint.

---

### Q36. What is the difference between `useTransition` and `useDeferredValue`?

**Say this first:**
Both mark work as low priority. With `useTransition` you wrap the state update that causes the work. With `useDeferredValue` you wrap a value you receive, and React gives you a "lagging" copy of it.

**If they ask more:**
Use `useTransition` when you own the code that calls `setState`. Use `useDeferredValue` when the value comes from somewhere else, such as a prop, and you cannot wrap the update. In both cases, React renders the urgent part first and updates the slow part later.

---

### Q37. How does Suspense work with Fiber?

**Say this first:**
When a component is not ready (for example, it is waiting for data), it tells React to wait. React stops rendering that part, shows the fallback UI of the nearest Suspense boundary, and continues rendering when the data is ready.

**If they ask more:**
Fiber makes this possible because the render phase can be abandoned or restarted. React can render a tree, find that one part is not ready, show a fallback for that part, and keep the rest of the screen working. When the data arrives, React retries rendering that part. A fully synchronous, non-pausable renderer could not do this cleanly.

---

### Q38. Can low-priority updates get starved forever?

**Say this first:**
No. React raises the priority of an update the longer it waits. After a certain time the update expires and is treated as urgent, so it gets rendered.

**If they ask more:**
Each lane has an expiration time. When React sees that a low-priority lane has waited too long, it marks it as expired and renders it without yielding. This guarantees that background work does not wait forever just because urgent updates keep coming.

---

# Tricky and Scenario Questions

### Q39. If Fiber can pause rendering, why is my slow component still freezing the page?

**Say this first:**
Because React can only pause between fibers, not in the middle of a component function. If one component takes a long time to run, React cannot interrupt it.

**If they ask more:**
Fix it by making the slow work smaller: split the component, move heavy calculations out of render (or cache them with `useMemo`), virtualize long lists, or use `useTransition` so the slow update is rendered as low priority.

---

### Q40. Why might my component function run twice in development?

**Say this first:**
That is React StrictMode. In development it intentionally calls components twice to help find impure code. It does not happen in production.

**If they ask more:**
It matches the fact that React is allowed to run, pause, or discard a render at any time. If your component works correctly even when called twice, it is pure and safe for concurrent rendering.

---

### Q41. Walk me through everything that happens when a user clicks a button that updates state.

**Say this first:**
The click calls `setState`, which queues an update and marks lanes up to the root. React then starts a render, rebuilds a draft tree with only the changed parts, marks what needs to change, and in the commit phase updates the DOM and swaps the trees.

**If they ask more:**
1. The click event is caught and marked as high priority.
2. `setCount(1)` creates an update in the hook's queue and marks `lanes` / `childLanes` up to the root.
3. React schedules a render for that lane.
4. **Render phase:** React creates the workInProgress tree and walks down. Fibers with no work are skipped using lanes.
5. It reaches the component, calls the function again, `useState` reads the queue and returns the new value, and the component returns new JSX.
6. Reconciliation compares old and new, finds only the text changed, and sets an `Update` flag.
7. `completeWork` runs on the way up and bubbles flags to the parents.
8. **Commit phase:** the DOM text is updated, `root.current` is swapped, layout effects run, and `useEffect` is scheduled after paint.
9. The screen shows the new value.

---

### Q42. What happens if a component throws an error during render?

**Say this first:**
React walks up the fiber tree looking for the nearest error boundary. If it finds one, it renders that boundary's fallback UI instead. If there is none, it unmounts the whole tree.

**If they ask more:**
Because fibers have the `return` pointer, React can easily walk up from the failing fiber to its ancestors. The render of the failed part is abandoned. Since nothing was committed yet, the user never sees broken output.

---

### Q43. How does Fiber help performance in practice?

**Say this first:**
It keeps the app responsive by splitting work into small slices, prioritizing urgent updates, skipping unchanged subtrees, and reusing fiber objects.

**If they ask more:**
- **Time slicing** keeps the main thread free for input and animation.
- **Lanes** make sure urgent updates are rendered first.
- **Bailouts** skip parts of the tree where nothing changed.
- **Double buffering** reuses old fiber objects instead of creating new ones every time.
- **Batching** combines many state updates into one render.

---

### Q44. What are the limits or downsides of Fiber?

**Say this first:**
Fiber does not make code faster by itself. It makes the app feel more responsive. It also adds complexity, and it requires components to be pure.

**If they ask more:**
Because renders can be repeated or discarded, impure components can behave strangely. Fiber also cannot interrupt a single heavy component, and the total work is not reduced, only spread out. You still need good practices such as keeping renders light and keys correct.

---

### Q45. Can you explain Fiber to a beginner in one minute?

**Say this first:**
Imagine React has a to-do list of UI changes. The old React did the whole list in one go, and nothing else could happen meanwhile. Fiber turns each item into a small object linked to the next one. React does a few items, checks if the browser needs something urgent like a click, and then continues. It prepares all the changes on a draft first, and only then applies them to the screen in one quick step.

---

# One-Minute Summary to Memorize

> React Fiber is React's core engine, introduced in version 16. Before Fiber, React used recursion, which could not be paused, so big updates froze the page. Fiber turns every component and DOM element into a small object called a fiber. Fibers are linked with `child`, `sibling`, and `return` pointers, so React can walk the tree with a simple loop and remember its position in one variable. That is how it can pause after about 5 milliseconds, let the browser handle input, and continue later.
>
> React keeps two trees: the current tree on screen and a workInProgress draft, linked by `alternate`. An update has two phases. In the **render phase**, React runs components (`beginWork`), compares old and new (reconciliation), marks what must change with flags, and builds DOM nodes in memory (`completeWork`). This phase can be paused, restarted, or discarded, so components must be pure. In the **commit phase**, React applies the flags to the real DOM in one uninterruptible step, swaps the draft in as the current tree, and runs effects.
>
> State from hooks is stored on the fiber as a linked list matched by call order, which is why hooks must not be called conditionally. Priorities are handled by **lanes**, so urgent updates like typing can interrupt slow ones like a big filtered list. This is the foundation of concurrent features such as `useTransition` and Suspense.

---

## Quick Revision: Keywords to Remember

| Word | One-line meaning |
|---|---|
| Fiber | A JS object for one unit of work (one per component/element) |
| child / sibling / return | The three pointers that link fibers |
| workInProgress | The draft tree being built |
| current | The tree currently on the screen |
| alternate | Link between a fiber and its twin |
| beginWork | Going down: run component, reconcile children |
| completeWork | Coming up: build DOM nodes, bubble flags |
| flags | To-do markers for the commit phase |
| reconciliation | Comparing old fibers with new elements |
| key | Stable identity for list items |
| commit phase | Apply changes to the DOM, run effects (no pause) |
| time slicing | Work in ~5 ms chunks, then yield |
| lanes | Priority bits (`lanes`, `childLanes`) |
| bailout | Skipping work when nothing changed |
| memoizedState | Where hooks/state live on a fiber |
