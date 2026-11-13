# React Fiber: How It Works Inside

This document explains React Fiber from the ground up, in simple words. Every claim is followed by *how* it happens, with small code and diagrams.

> The code shown here is **simplified** so you can see the idea. Real React code has many more details, but the logic and the names are the same.

---

## Table of Contents

1. [What is Fiber?](#1-what-is-fiber)
2. [The problem Fiber solved](#2-the-problem-fiber-solved)
3. [A fiber is just a JavaScript object](#3-a-fiber-is-just-a-javascript-object)
4. [How fibers connect: the Fiber tree](#4-how-fibers-connect-the-fiber-tree)
5. [Two trees: current and work-in-progress](#5-two-trees-current-and-work-in-progress)
6. [The two phases: Render and Commit](#6-the-two-phases-render-and-commit)
7. [Render phase in detail](#7-render-phase-in-detail)
8. [How React can pause and resume](#8-how-react-can-pause-and-resume)
9. [Reconciliation: how React compares old and new](#9-reconciliation-how-react-compares-old-and-new)
10. [Commit phase in detail](#10-commit-phase-in-detail)
11. [Hooks inside a fiber](#11-hooks-inside-a-fiber)
12. [Priorities: Lanes](#12-priorities-lanes)
13. [Full walkthrough: what happens when you click a button](#13-full-walkthrough-what-happens-when-you-click-a-button)
14. [Build a tiny Fiber loop yourself](#14-build-a-tiny-fiber-loop-yourself)
15. [Summary](#15-summary)

---

## 1. What is Fiber?

Fiber is the **engine inside React** that decides *what to change on the screen* and *when to change it*.

It has two meanings:

1. **A fiber** = one small object that represents one component (or one DOM element) and the work React must do for it.
2. **Fiber (the architecture)** = the whole system of those objects, plus the algorithm that walks through them.

Fiber was introduced in React 16 and is still the core of React today (React 18 and 19 are built on it).

---

## 2. The problem Fiber solved

### Before Fiber (the "Stack Reconciler")

Old React compared the old UI and new UI using **normal recursion**:

```js
function updateComponent(component) {
  const children = component.render();
  children.forEach(child => updateComponent(child)); // calls itself
}
```

Recursion uses the JavaScript **call stack**. Once it starts, you cannot stop it in the middle. You must finish the entire tree.

### Why that is a problem

JavaScript has only one thread. That thread does everything: your code, React's work, clicks, typing, animations, painting the screen.

```
Big update starts ──────────────────────────────► finishes
        [ React is working, nothing else can run ]
        user types  → ignored (waiting)
        animation   → frozen
```

If the update takes 200 ms, the page **freezes for 200 ms**. The user feels lag.

### What Fiber changed

Fiber replaces recursion with a **loop over objects**. A loop can be stopped after any step and continued later, because *where we are* is stored in a variable, not hidden inside the call stack.

```
Fiber:  [work][work] pause → browser handles click/paint → [work][work] pause → [work] done
```

This one idea (work that can be paused) makes everything else possible: smooth UI, priorities, Suspense, transitions.

---

## 3. A fiber is just a JavaScript object

Each component and each DOM element in your app gets one fiber object. Here are the most important fields:

```js
{
  // WHAT is this?
  tag: FunctionComponent,   // kind: function component, class, div, text...
  type: App,                // the function/class itself, or 'div' for DOM nodes
  key: null,                // the key you give in lists
  stateNode: null,          // the real DOM node (for 'div') or class instance

  // WHERE is it in the tree? (pointers to other fibers)
  return: parentFiber,      // parent
  child: firstChildFiber,   // first child
  sibling: nextSiblingFiber,// next brother/sister
  index: 0,                 // position among siblings

  // DATA
  pendingProps: {...},      // new props that came in for this render
  memoizedProps: {...},     // props used in the last finished render
  memoizedState: ...,       // state from the last render (for hooks: first hook object)
  updateQueue: ...,         // queued updates / effects

  // WORK TO DO
  flags: Placement | Update,// what must be done to the DOM (add, change, delete...)
  subtreeFlags: ...,        // do any descendants have flags?
  lanes: 0b0001,            // priority of work waiting on THIS fiber
  childLanes: 0b0000,       // priority of work waiting in DESCENDANTS

  // DOUBLE BUFFER
  alternate: otherFiber     // the "twin" fiber (see section 5)
}
```

You do not need to memorize these. Just remember three groups: **identity** (what it is), **links** (where it is), **work info** (what must be done).

---

## 4. How fibers connect: the Fiber tree

Fibers are not connected with arrays (`children: [a, b, c]`). They use **three pointers**: `child`, `sibling`, `return`.

Take this app:

```jsx
function App() {
  return (
    <div>
      <Header />
      <Main />
    </div>
  );
}
```

The fiber tree looks like this:

```
              App
               │ child
               ▼
              div
               │ child
               ▼
   Header ──sibling──► Main
```

Every arrow going down is `child`. Every arrow going right is `sibling`. Every fiber also has `return` pointing back up to its parent.

```js
App.child      = div
div.child      = Header
Header.sibling = Main
Header.return  = div
Main.return    = div
```

### Why this shape?

With `child` / `sibling` / `return`, React can move to the next piece of work using **only the current fiber**:

- Has a child? → go down.
- No child but has a sibling? → go right.
- Neither? → go up (`return`) and try the sibling there.

No recursion, no call stack. React just remembers **one variable**: "the fiber I am on now". That is what makes pausing possible.

---

## 5. Two trees: current and work-in-progress

React keeps **two** fiber trees at the same time:

| Tree | Meaning |
|---|---|
| **current** | What is on the screen right now. |
| **workInProgress** | A draft of the next screen that React is building. |

Each fiber in one tree points to its twin in the other tree using `alternate`:

```
 current tree            workInProgress tree
     App  ◄──alternate──►  App'
      │                     │
     div  ◄──alternate──►  div'
```

### How an update uses them

1. React **never edits the current tree** while rendering. That would break what the user sees if React gets interrupted halfway.
2. It builds/updates the **workInProgress** tree (reusing the twin object to save memory):

```js
function createWorkInProgress(current, newProps) {
  let wip = current.alternate;
  if (wip === null) {
    wip = createFiber(current.tag, newProps, current.key); // first time: make a twin
    wip.alternate = current;
    current.alternate = wip;
  } else {
    wip.pendingProps = newProps; // later times: reuse the old twin
    wip.flags = 0;
  }
  wip.child = current.child;
  wip.memoizedState = current.memoizedState;
  // ...copy other fields
  return wip;
}
```

3. When the draft is finished and written to the DOM, React flips one pointer:

```js
root.current = finishedWorkInProgressTree;
```

The draft is now the current tree, and the old current becomes the next draft. This is called **double buffering** (like a game that draws the next frame off-screen, then swaps).

**Result:** if React is interrupted or the draft is thrown away, the user never sees a half-finished screen.

---

## 6. The two phases: Render and Commit

Every update in React has two phases:

```
 ┌───────────────────────────┐        ┌───────────────────────────┐
 │       RENDER PHASE        │  ───►  │       COMMIT PHASE        │
 │  figure out what changed  │        │  apply changes to the DOM │
 │  can be paused/restarted  │        │  runs in ONE go, no pause │
 │  no side effects allowed  │        │  effects run here         │
 └───────────────────────────┘        └───────────────────────────┘
```

| | Render phase | Commit phase |
|---|---|---|
| Job | Call your components, compare, mark changes | Touch the real DOM, run effects |
| Can be interrupted? | **Yes** | **No** |
| Can run twice? | Yes (React may throw it away and redo it) | No |
| Touches DOM? | No | Yes |

This is why **component functions must be "pure"** (no DOM changes, no network calls while rendering): React may call them many times, or abandon the result.

---

## 7. Render phase in detail

The render phase walks the fiber tree doing two jobs on each fiber: `beginWork` (going down) and `completeWork` (coming back up).

### The loop

```js
function workLoopSync() {
  while (workInProgress !== null) {
    performUnitOfWork(workInProgress);
  }
}

function performUnitOfWork(fiber) {
  const next = beginWork(fiber);        // go DOWN: process this fiber

  if (next !== null) {
    workInProgress = next;              // it has a child → continue with the child
  } else {
    completeUnitOfWork(fiber);          // no child → start going UP
  }
}

function completeUnitOfWork(fiber) {
  let f = fiber;
  while (f !== null) {
    completeWork(f);                    // finish this fiber

    if (f.sibling !== null) {
      workInProgress = f.sibling;       // has a sibling → do it next
      return;
    }
    f = f.return;                       // no sibling → go up to the parent
  }
  workInProgress = null;                // reached the top: render is done
}
```

`workInProgress` is the "current position" variable. **One unit of work = one fiber.**

### What `beginWork` does

`beginWork(fiber)` is "do the work for this fiber and create/update its children."

- **Function component:** calls your function (this is where `App()` actually runs), gets the returned elements, then reconciles them into child fibers.
- **Class component:** calls `render()`.
- **DOM element (`div`):** just reconciles its children.
- **Bailout:** if props and state did not change and no pending update exists, it **skips** this fiber and often the whole subtree.

```js
function beginWork(current, wip) {
  if (current !== null) {
    const sameProps = current.memoizedProps === wip.pendingProps;
    const hasWork = includesSomeLane(wip.lanes);

    if (sameProps && !hasWork) {
      // Nothing changed here. Do any children have work?
      if (!includesSomeLane(wip.childLanes)) {
        return null;                    // skip whole subtree
      }
      cloneChildFibers(wip);            // reuse children, keep going to find the work
      return wip.child;
    }
  }

  switch (wip.tag) {
    case FunctionComponent: {
      const nextChildren = renderWithHooks(wip);  // runs YOUR component
      reconcileChildren(current, wip, nextChildren);
      return wip.child;
    }
    case HostComponent: {                          // 'div', 'span'...
      reconcileChildren(current, wip, wip.pendingProps.children);
      return wip.child;
    }
  }
}
```

This is how React avoids re-rendering everything: `lanes` and `childLanes` tell it which parts of the tree have work.

### What `completeWork` does

`completeWork(fiber)` runs when a fiber's children are all finished.

- **For DOM fibers (new):** creates the real DOM node in memory (`document.createElement('div')`), attaches the children's DOM nodes to it. It is **not** put on the page yet.
- **For DOM fibers (existing):** compares old and new props and records what must change.
- **Bubbles up flags:** collects `flags` from children into `subtreeFlags`, so the commit phase can quickly know which branches have changes.

```js
function completeWork(current, wip) {
  switch (wip.tag) {
    case HostComponent: {
      if (current !== null && wip.stateNode !== null) {
        // existing DOM node: find what changed
        wip.updateQueue = diffProps(current.memoizedProps, wip.pendingProps);
        if (wip.updateQueue) wip.flags |= Update;
      } else {
        // new DOM node: build it off-screen
        const dom = document.createElement(wip.type);
        appendAllChildren(dom, wip);    // attach child DOM nodes
        wip.stateNode = dom;
      }
    }
  }
  bubbleProperties(wip);                // merge children's flags into subtreeFlags
}
```

### Walkthrough: the order of work

For this tree:

```
App
 └─ div
     ├─ Header
     └─ Main
```

The loop visits like this:

```
beginWork(App)
  beginWork(div)
    beginWork(Header)
    completeWork(Header)      ← no child, so complete, then go to sibling
    beginWork(Main)
    completeWork(Main)        ← no child, no sibling, so go up
  completeWork(div)
completeWork(App)             ← top reached, render phase over
```

**Rule:** a fiber is *begun* before its children and *completed* after all its children. Parents wait for children.

---

## 8. How React can pause and resume

Section 7 showed `workLoopSync`, which never stops. For non-urgent updates React uses a different loop with one extra condition:

```js
function workLoopConcurrent() {
  while (workInProgress !== null && !shouldYield()) {
    performUnitOfWork(workInProgress);
  }
}
```

`shouldYield()` asks: "Have I used up my time slice?" The slice is about **5 milliseconds**.

```js
function shouldYield() {
  const timeUsed = getCurrentTime() - startTime;
  return timeUsed >= 5;   // ms
}
```

### What happens when it says yes

1. The loop exits. `workInProgress` still points to the next fiber to do.
2. React hands control back to the browser. The browser can now handle clicks, typing, animations and paint.
3. React asks the **Scheduler** to call it again. The Scheduler uses `MessageChannel` to run a new task soon (it is a macrotask, so the browser gets a turn in between).
4. The loop starts again from `workInProgress` and continues exactly where it stopped.

```
|--5ms work--|  browser paints/handles input  |--5ms work--| browser |--work--| DONE → commit
```

**Why this works:** the position is stored in the variable `workInProgress` (a fiber pointer), not in the call stack. Pausing is just "exit the loop," and resuming is "enter the loop again."

> The 5 ms is about work *between* fibers. A single component function that takes 100 ms still blocks, because React cannot stop in the middle of your function.

---

## 9. Reconciliation: how React compares old and new

`reconcileChildren` is where React compares **old child fibers** with **new React elements** (what your component just returned) and decides what to keep, change, add, or remove.

### Rule 1: same type and same key → reuse the fiber

```js
// before:  <div className="a">Hi</div>
// after:   <div className="b">Hi</div>
```

Same `type` (`div`), same `key` → React keeps the fiber and the real DOM node, updates props, and sets `flags |= Update`.

### Rule 2: different type → throw away and rebuild

```js
// before:  <div><Counter /></div>
// after:   <span><Counter /></span>
```

`div` ≠ `span`. React deletes the old `div` fiber (flag `ChildDeletion`) and everything under it, **including state**, then creates a brand new `span` subtree (flag `Placement`).

### Rule 3: lists use `key`

React walks the old list and the new list. For each new item it tries to find an old fiber with the same `key`:

```js
// before: [A(key=a), B(key=b), C(key=c)]
// after:  [C(key=c), A(key=a), B(key=b)]
```

Step by step:

1. Put old fibers in a map: `{ a: fiberA, b: fiberB, c: fiberC }`.
2. Go through the new list. For `C`: found in the map, reuse it. Its old index (2) is bigger than the last placed index (0), so it **stays**. `lastPlacedIndex = 2`.
3. For `A`: found, its old index (0) is smaller than `lastPlacedIndex` (2), so it **moves** (flag `Placement`).
4. For `B`: same, it **moves**.
5. Any old fibers left in the map are **deleted**.

Without keys, React uses the position (index), so inserting at the top makes every item look "changed." With keys, React knows who is who and moves them instead.

### What reconciliation produces

It does **not** touch the DOM. It only creates fibers and writes `flags` on them:

```js
fiber.flags |= Placement;      // insert/move this DOM node
fiber.flags |= Update;         // change props/text
parentFiber.flags |= ChildDeletion;
parentFiber.deletions = [oldFiber];
```

These flags are the "to-do list" for the commit phase.

---

## 10. Commit phase in detail

When the render phase finishes the whole workInProgress tree, React has:

- a complete draft tree,
- `flags` on fibers that need DOM work,
- `subtreeFlags` on ancestors so it can skip untouched branches quickly.

Now the **commit phase** applies everything **synchronously** (no pauses) in three steps:

```js
function commitRoot(root) {
  const finished = root.finishedWork;

  // 1. BEFORE MUTATION: read from the DOM before changing it
  commitBeforeMutationEffects(finished);   // e.g. getSnapshotBeforeUpdate

  // 2. MUTATION: change the real DOM
  commitMutationEffects(finished);         // insert, update, delete DOM nodes

  root.current = finished;                 // ← the swap: draft becomes current

  // 3. LAYOUT: DOM is updated, run code that needs to measure it
  commitLayoutEffects(finished);           // useLayoutEffect, componentDidMount/Update

  // later, after the browser paints:
  // 4. PASSIVE EFFECTS: useEffect callbacks run
}
```

### What the mutation step does

It walks fibers that have flags and performs the real DOM operation:

```js
function commitMutationEffects(fiber) {
  if (fiber.flags & Placement) {
    parentDom.insertBefore(fiber.stateNode, beforeNode);
  }
  if (fiber.flags & Update) {
    applyPropChanges(fiber.stateNode, fiber.updateQueue); // setAttribute, textContent...
  }
  if (fiber.deletions) {
    fiber.deletions.forEach(removeFromDom);
    // also runs cleanup of useEffect/useLayoutEffect and componentWillUnmount
  }
}
```

### Why the swap is between steps 2 and 3

- During **mutation**, effects of *deleted* components must see the **old** tree.
- During **layout**, effects of *new/updated* components must see the **new** tree.

So `root.current = finished` is placed exactly between them.

### `useLayoutEffect` vs `useEffect`

| | When it runs | Blocks painting? |
|---|---|---|
| `useLayoutEffect` | Layout step, right after DOM change, **before** paint | Yes |
| `useEffect` | After the browser has painted (scheduled separately) | No |

That is why `useLayoutEffect` is used for measuring the DOM, and `useEffect` for everything else.

---

## 11. Hooks inside a fiber

A function component has no `this`, so where is its state kept? **On its fiber**, in `fiber.memoizedState`.

### Hooks are a linked list

Each hook call creates a **hook object**, and they are chained in the order you call them:

```jsx
function Counter() {
  const [count, setCount] = useState(0);   // hook 1
  const [name, setName]   = useState('');  // hook 2
  useEffect(() => { ... }, [count]);       // hook 3
}
```

```
Counter fiber
  memoizedState ─► [hook1: count=0] ─next─► [hook2: name=''] ─next─► [hook3: effect]
```

A hook object looks like this:

```js
{
  memoizedState: 0,      // the current value (state, memo value, ref, effect...)
  baseState: 0,
  queue: {               // pending updates (setState calls)
    pending: null,
    dispatch: setCount
  },
  next: nextHook         // the next hook in the list
}
```

### How `useState` finds its data

React keeps a pointer to "the hook I am on." Every time you call a hook during render, it **moves to the next one in the list**:

```js
function updateWorkInProgressHook() {
  // move to the next hook of the previous render
  currentHook = currentHook === null
    ? currentFiber.memoizedState          // first hook
    : currentHook.next;                   // next hook
  // ...clone it into the workInProgress fiber and return it
}
```

React matches hooks **by call order only**. It does not know names. The 2nd `useState` call gets the 2nd hook object.

### That is why "Rules of Hooks" exist

```jsx
// BAD
if (isOpen) {
  const [x, setX] = useState(0);  // sometimes 1st hook, sometimes skipped
}
const [y, setY] = useState(1);    // now sometimes 1st, sometimes 2nd → wrong data!
```

If order changes between renders, React hands the wrong hook object to the wrong call. So hooks must always run in the same order, never inside `if`, loops, or after early returns.

### What `setCount(5)` really does

```js
function dispatchSetState(fiber, queue, action) {
  const lane = requestUpdateLane();           // decide priority (see section 12)
  const update = { lane, action, next: null };

  enqueueUpdate(queue, update);               // add to this hook's queue

  markUpdateLaneFromFiberToRoot(fiber, lane); // walk up via `return`, set lanes/childLanes
  scheduleUpdateOnFiber(root, fiber, lane);   // ask React to render soon
}
```

It does **not** change the state immediately. It only stores an update and schedules a render. On the next render, `useState` reads the queue and calculates the new value:

```js
function updateState(hook) {
  let newState = hook.baseState;
  for (const update of hook.queue.pending) {
    newState = typeof update.action === 'function'
      ? update.action(newState)      // setCount(c => c + 1)
      : update.action;               // setCount(5)
  }
  hook.memoizedState = newState;
  return [newState, hook.queue.dispatch];
}
```

This is also why calling `setCount(count + 1)` three times in a row gives +1, but `setCount(c => c + 1)` three times gives +3: the first form uses the same old `count` each time, while the function form uses the latest value from the queue.

---

## 12. Priorities: Lanes

Not all updates are equally urgent. Typing in an input must feel instant. Loading a big filtered list can wait a little.

Fiber represents priority using **lanes**. A lane is one bit in a binary number (a bitmask).

```js
const SyncLane           = 0b0000000000000000000000000000001; // click, discrete events
const InputContinuousLane= 0b0000000000000000000000000000100; // drag, scroll, mouse move
const DefaultLane        = 0b0000000000000000000000000010000; // normal setState
const TransitionLane1    = 0b0000000000000000000000001000000; // startTransition
const IdleLane           = 0b0100000000000000000000000000000; // lowest priority
```

Smaller bit = higher priority.

### Why bits?

Bit operations are fast and let one number hold many lanes:

```js
fiber.lanes = SyncLane | DefaultLane;          // has two kinds of work waiting
includesSomeLane(fiber.lanes, SyncLane);       // (lanes & SyncLane) !== 0
```

### How lanes are marked

When you call `setState`, `markUpdateLaneFromFiberToRoot` walks up using `return`:

```
 root
  └─ App          childLanes |= SyncLane
      └─ List     childLanes |= SyncLane
          └─ Item  lanes |= SyncLane   ← the component whose setState was called
```

Now during rendering, `beginWork` on `App` sees `lanes == 0` but `childLanes != 0`, so it knows "I don't need to re-run, but something below me does." It skips straight down to `Item` and skips siblings that have no work. This is the bailout from section 7.

### How an urgent update interrupts a slow one

Example: a slow list filter is rendering (low priority, using `startTransition`), then the user types a letter (high priority).

```
1. Transition render in progress...            [work][work][work]
2. User types → SyncLane update arrives
3. React sees a higher priority lane than what it is rendering
4. React THROWS AWAY the half-built workInProgress tree
5. Renders the typing update first   (fast, then commit)
6. Starts the transition render again from scratch
```

Step 4 is safe because the render phase has no side effects and the current tree was never touched (section 5). That is the real value of the two-tree design.

```jsx
const [text, setText] = useState('');
const [filter, setFilter] = useState('');
const [isPending, startTransition] = useTransition();

function onChange(e) {
  setText(e.target.value);               // urgent: SyncLane → input stays instant
  startTransition(() => {
    setFilter(e.target.value);           // not urgent: TransitionLane → can be interrupted
  });
}
```

---

## 13. Full walkthrough: what happens when you click a button

Code:

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

The user clicks the button. Here is the complete journey:

**1. Event** – React's event system catches the click and marks it as a high-priority (discrete) event.

**2. `setCount(1)` is called** → `dispatchSetState`:
   - lane = `SyncLane`
   - creates `update = { action: 1, lane: SyncLane }` and adds it to the hook's queue
   - walks up with `return` marking `lanes` on `Counter` and `childLanes` on its ancestors, up to the root

**3. Schedule** – `scheduleUpdateOnFiber` → `ensureRootIsScheduled` queues a render for `SyncLane` (run as soon as the event finishes).

**4. Render phase begins** – React clones the root into a workInProgress tree and sets `workInProgress = rootWIP`.

**5. Walk down** – `beginWork` runs on each fiber. Fibers with no work are skipped using `lanes` / `childLanes`. It reaches `Counter`.

**6. `Counter` runs again** – `renderWithHooks` calls `Counter()`. `useState` moves to hook 1, reads the queue, finds `action: 1`, and returns `[1, setCount]`. The function returns a new `<button>` element with `1` as text.

**7. Reconcile** – the old `button` fiber and the new `<button>` element have the same type → reuse the fiber. Child text changed from `"0"` to `"1"` → flag `Update`.

**8. Walk up** – `completeWork` on the text/button fiber prepares the change list; flags bubble up through `subtreeFlags`.

**9. Render finished** – `root.finishedWork` = the new tree.

**10. Commit phase** (one go):
   - mutation: DOM `button.textContent = "1"`
   - `root.current` now points to the new tree
   - layout effects run
   - passive effects (`useEffect`) are scheduled to run after paint

**11. Screen shows `1`.** The old tree becomes the spare for the next update.

---

## 14. Build a tiny Fiber loop yourself

This is a small, runnable demo of the core idea: a loop over fibers that **pauses and resumes**. Save as `demo.js` and run `node demo.js`.

```js
// A plain description of a tree (like what components return)
const tree = {
  name: "App",
  children: [
    { name: "Header" },
    { name: "Main", children: [{ name: "List" }] },
  ],
};

// Create the root fiber
let nextUnitOfWork = { name: "App", node: tree, return: null, child: null, sibling: null };

// beginWork: make child fibers for this fiber, return the first child
function beginWork(fiber) {
  console.log("begin   ", fiber.name);
  let prev = null;
  for (const c of fiber.node.children || []) {
    const child = { name: c.name, node: c, return: fiber, child: null, sibling: null };
    if (prev) prev.sibling = child;   // link siblings
    else fiber.child = child;         // first child
    prev = child;
  }
  return fiber.child;
}

function completeWork(fiber) {
  console.log("complete", fiber.name);
}

// One unit of work = one fiber
function performUnitOfWork(fiber) {
  const next = beginWork(fiber);
  if (next) return next;                    // go down

  let f = fiber;
  while (f) {
    completeWork(f);
    if (f.sibling) return f.sibling;        // go right
    f = f.return;                           // go up
  }
  return null;                              // finished the whole tree
}

// The loop: do 2 units, then give the "browser" a turn
function workLoop() {
  let budget = 2;                           // pretend time slice
  while (nextUnitOfWork && budget-- > 0) {
    nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
  }

  if (nextUnitOfWork) {
    console.log("--- paused: browser can paint / handle clicks ---");
    setTimeout(workLoop, 0);                // resume later
  } else {
    console.log("render done -> commit phase");
  }
}

workLoop();
```

Output:

```
begin    App
begin    Header
complete Header
--- paused: browser can paint / handle clicks ---
begin    Main
begin    List
complete List
complete Main
complete App
render done -> commit phase
```

Notice that the work stopped after two fibers and continued from `Main`. The only thing remembered between the pause and the resume is the variable `nextUnitOfWork`. That is the entire idea behind Fiber.

---

## 15. Summary

| Concept | One-line explanation | How it works |
|---|---|---|
| **Fiber** | One unit of work, one JS object per component/element | Holds type, props, state, links, flags, lanes |
| **Fiber tree** | Linked list-style tree | `child`, `sibling`, `return` pointers instead of arrays/recursion |
| **Work loop** | Walks the tree one fiber at a time | `performUnitOfWork` moves down, right, then up |
| **Pause/resume** | Stop anytime, continue later | Position stored in `workInProgress` variable; `shouldYield()` checks the ~5ms slice |
| **Double buffering** | Two trees: current and draft | `alternate` links twins; `root.current` swaps at commit |
| **Render phase** | Decide what changes | `beginWork` (run components, reconcile) + `completeWork` (build DOM nodes, bubble flags) |
| **Reconciliation** | Compare old fibers vs new elements | Same type+key reuse; different type rebuild; keys for lists; sets `flags` |
| **Commit phase** | Apply changes to the DOM | Before-mutation → mutation → swap `current` → layout → passive effects |
| **Hooks** | State on the fiber | Linked list in `fiber.memoizedState`, matched by call order |
| **Lanes** | Priorities | Bitmask on `lanes`/`childLanes`; urgent updates can discard and restart a slow render |

**The whole story in four sentences:**
React turns your UI into a tree of fiber objects. When something changes, it builds a draft copy of that tree by walking fibers one by one, in a loop that can pause so the browser stays responsive. The draft only *marks* what must change (flags). Then, in one quick uninterruptible step, React applies those changes to the real DOM and swaps the draft in as the new current tree.
