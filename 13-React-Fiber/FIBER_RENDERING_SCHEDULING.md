# How Fiber Divides, Pauses and Prioritizes Rendering Work

This document answers four questions, each with code and real output:

1. **How does Fiber divide rendering work, and what does it use to divide it?**
2. **How does it pause? When does it pause? Why does it pause?**
3. **How does it prioritize, and what does it use to prioritize?**
4. **How does all this help rendering?**

> **About the demos:** The code below is a small simulation of the idea, not real React. It uses a **virtual clock** (every component costs 1 ms), so the output is the same every time you run it. The *shape* of the behavior (slices, pauses, discarding a draft) matches how React works. The exact millisecond numbers are made up for learning. Real React code has many more details.

---

## Table of Contents

1. [The big picture](#1-the-big-picture)
2. [Dividing the work](#2-dividing-the-work)
3. [Pausing the work](#3-pausing-the-work)
4. [Prioritizing the work](#4-prioritizing-the-work)
5. [Before vs After: the full comparison](#5-before-vs-after-the-full-comparison)
6. [What Fiber cannot do (limits)](#6-what-fiber-cannot-do-limits)
7. [Summary](#7-summary)

---

## 1. The big picture

The browser has **one main thread**. It must do your JavaScript, handle clicks and typing, and paint the screen, all on the same thread. The browser wants to paint about every **16 ms** (60 frames per second). If React holds the thread longer than that, the user feels lag.

Fiber solves this with three tools that work together:

```
 Tool 1: DIVIDE      ->  split one big render into many tiny units (one fiber each)
 Tool 2: PAUSE       ->  stop after about 5 ms, let the browser work, then continue
 Tool 3: PRIORITIZE  ->  when several updates are waiting, do the most urgent one first
```

Each tool depends on the one before it. You cannot pause if the work is not divided. You cannot prioritize if you cannot pause and switch work.

---

## 2. Dividing the work

### What does Fiber use to divide the work?

It uses **the fiber itself**. One fiber (one component or one DOM element) is **one unit of work**. React never works on "the whole tree". It works on one fiber, then asks "what is next?".

### How does it know what comes next?

Every fiber has three pointers: `child`, `sibling`, `return`.

```
              App
               | child
               v
   Header --sibling--> Sidebar --sibling--> Main
                         |
                       child
                         v
                       Menu --sibling--> Profile
```

The rule to find the next unit of work, using only the current fiber:

```
1. Has a child?                    -> go down to the child
2. No child, but has a sibling?    -> go right to the sibling
3. Neither?                        -> go up (return) and try the sibling there
```

### Why this divides the work well

The old React used recursion, so the position in the tree was hidden inside the call stack, and you could not stop in the middle. With fibers, the position is **just one variable** (`workInProgress`). The work is naturally cut into pieces, with a cut point between every pair of fibers.

```js
// Simplified from React
function performUnitOfWork(fiber) {
  const next = beginWork(fiber);          // do the work for THIS ONE fiber
  if (next !== null) {
    workInProgress = next;                // go down
  } else {
    completeUnitOfWork(fiber);            // go right or up
  }
}
```

Children are also created **lazily**: React creates a fiber's child fibers only when it reaches that fiber (inside `beginWork`). So the work is discovered piece by piece, not planned all at once.

### Before: no division (old recursive style)

```js
// 1-before-blocking.js
let now = 0;                              // virtual clock in milliseconds
const COST = 1;                           // every component takes 1 ms to render
const log = (msg) => console.log(`t=${String(now).padStart(2)}ms  ${msg}`);

const tree = {
  name: "App",
  children: [
    { name: "Header" },
    { name: "Sidebar", children: [{ name: "Menu" }, { name: "Profile" }] },
    { name: "Main", children: [
      { name: "Feed", children: Array.from({ length: 10 }, (_, i) => ({ name: `Post${i + 1}` })) },
      { name: "Footer" },
    ]},
  ],
};

const events = [{ at: 3, name: "user clicks a button" }];
const rendered = [];

function renderRecursive(node) {          // the call stack remembers where we are
  now += COST;
  rendered.push(node.name);
  for (const child of node.children || []) renderRecursive(child);
}

log("render starts (recursive, cannot be paused)");
renderRecursive(tree);
log(`render finished (${rendered.length} components: ${rendered.join(", ")})`);

for (const e of events) {
  log(`handled: ${e.name}   (it happened at t=${e.at}ms, waited ${now - e.at}ms)`);
}
```

**Output:**

```
t= 0ms  render starts (recursive, cannot be paused)
t=18ms  render finished (18 components: App, Header, Sidebar, Menu, Profile, Main, Feed, Post1, Post2, Post3, Post4, Post5, Post6, Post7, Post8, Post9, Post10, Footer)
t=18ms  handled: user clicks a button   (it happened at t=3ms, waited 15ms)
```

**What this shows:** The user clicked at t=3 ms, but the click was only handled at t=18 ms. The click **waited 15 ms** because the recursive render could not be stopped.

---

## 3. Pausing the work

### How does Fiber pause?

Pausing is just **leaving the loop**. Because the position is saved in `workInProgress`, nothing is lost when the loop exits. To resume, React enters the loop again, and it continues from the same fiber.

```js
// Simplified from React
function workLoopConcurrent() {
  while (workInProgress !== null && !shouldYield()) {   // <-- the pause check
    performUnitOfWork(workInProgress);
  }
}
```

### When does Fiber pause?

React checks `shouldYield()` **after every single fiber**. It returns `true` when the current slice has used about **5 ms**.

```js
function shouldYield() {
  const timeUsed = getCurrentTime() - startOfThisSlice;
  return timeUsed >= 5;     // milliseconds
}
```

So the pause points are: **between fibers**, **once the 5 ms budget is used**.

There are two reasons a render stops, so keep them apart:

| Reason | What happens |
|---|---|
| **Time slice used up** (5 ms) | Pause, let the browser work, **continue from the same fiber**. Nothing is lost. |
| **More urgent update arrived** | Stop, **throw away the draft**, render the urgent update, restart the slow one later (see section 4). |

### Why does Fiber pause?

Because the browser needs the main thread for things that matter more than finishing React's draft:

- handling clicks and typing
- running animations
- painting the next frame (every ~16 ms)

React's slice is about 5 ms, which is comfortably less than one frame, so the browser always has time to respond.

### How does it resume? (the Scheduler)

When the loop pauses, React does not call itself directly. It asks its **Scheduler** to call it again. The Scheduler posts a message through a `MessageChannel`. That message becomes a new browser task, so the browser can handle clicks and painting **before** React's next slice runs.

```js
// Simplified from React's Scheduler
const channel = new MessageChannel();
channel.port1.onmessage = performWorkUntilDeadline;

function performWorkUntilDeadline() {
  startOfThisSlice = getCurrentTime();
  const hasMoreWork = flushWork();           // runs workLoopConcurrent until shouldYield()
  if (hasMoreWork) {
    channel.port2.postMessage(null);         // "call me again soon" -> new browser task
  }
}
```

### After: the same render, with pausing

```js
// 2-after-fiber-slicing.js
let now = 0;                              // virtual clock in milliseconds
const SLICE = 5;                          // time budget per slice (React uses ~5 ms)
const SLOW = {};                          // try: { Post5: 12 } to see a slow component
const costOf = (name) => SLOW[name] ?? 1; // default: every component costs 1 ms
const log = (msg) => console.log(`t=${String(now).padStart(2)}ms  ${msg}`);

const tree = {
  name: "App",
  children: [
    { name: "Header" },
    { name: "Sidebar", children: [{ name: "Menu" }, { name: "Profile" }] },
    { name: "Main", children: [
      { name: "Feed", children: Array.from({ length: 10 }, (_, i) => ({ name: `Post${i + 1}` })) },
      { name: "Footer" },
    ]},
  ],
};

const events = [{ at: 3, name: "user clicks a button", done: false }];
let sliceNames = [];

// ---- the fiber machinery ----------------------------------------------------
function beginWork(fiber) {               // "render" this component, create child fibers
  now += costOf(fiber.name);
  sliceNames.push(fiber.name);
  let prev = null;
  for (const c of fiber.node.children || []) {
    const child = { name: c.name, node: c, return: fiber, child: null, sibling: null };
    if (prev) prev.sibling = child; else fiber.child = child;
    prev = child;
  }
  return fiber.child;
}

function performUnitOfWork(fiber) {       // ONE unit of work = ONE fiber
  const next = beginWork(fiber);
  if (next) return next;                  // go down
  let f = fiber;
  while (f) {
    if (f.sibling) return f.sibling;      // go right
    f = f.return;                         // go up
  }
  return null;                            // whole tree finished
}

const shouldYield = (sliceStart) => now - sliceStart >= SLICE;

function browserTurn() {                  // the browser gets to run its own tasks
  for (const e of events) {
    if (!e.done && e.at <= now) {
      e.done = true;
      log(`  browser handles: ${e.name}   (happened at t=${e.at}ms, waited ${now - e.at}ms)`);
    }
  }
}

// ---- the work loop ----------------------------------------------------------
let workInProgress = { name: "App", node: tree, return: null, child: null, sibling: null };
let sliceNo = 0;
let total = 0;

while (workInProgress) {
  sliceNo++;
  const sliceStart = now;
  sliceNames = [];
  while (workInProgress && !shouldYield(sliceStart)) {      // <-- the pause check
    workInProgress = performUnitOfWork(workInProgress);
    total++;
  }
  log(`slice ${sliceNo}: ${sliceNames.join(", ")}   (${now - sliceStart}ms)`);
  if (workInProgress) {
    log(`  PAUSE -> next fiber to do is "${workInProgress.name}" (saved in workInProgress)`);
    browserTurn();
  }
}
log(`render finished (${total} fibers) -> commit`);
browserTurn();
```

**Output:**

```
t= 5ms  slice 1: App, Header, Sidebar, Menu, Profile   (5ms)
t= 5ms    PAUSE -> next fiber to do is "Main" (saved in workInProgress)
t= 5ms    browser handles: user clicks a button   (happened at t=3ms, waited 2ms)
t=10ms  slice 2: Main, Feed, Post1, Post2, Post3   (5ms)
t=10ms    PAUSE -> next fiber to do is "Post4" (saved in workInProgress)
t=15ms  slice 3: Post4, Post5, Post6, Post7, Post8   (5ms)
t=15ms    PAUSE -> next fiber to do is "Post9" (saved in workInProgress)
t=18ms  slice 4: Post9, Post10, Footer   (3ms)
t=18ms  render finished (18 fibers) -> commit
```

### How to read this output

- **Slice 1** rendered 5 fibers (5 ms), then hit the time limit and **paused**. The line `next fiber to do is "Main"` shows the saved position: that one variable is all React needs.
- At the pause, the browser got a turn and **handled the click that happened at t=3 ms**. It waited only **2 ms**.
- Slices 2 and 3 continued exactly where the last one stopped. The `Post4` and `Main` pauses show it never loses its place.
- The last slice only needed 3 ms, so it finished and moved to the commit phase.

### Before vs After (pausing)

| | Before (recursive) | After (Fiber slices) |
|---|---|---|
| Click happened at | t=3 ms | t=3 ms |
| Click handled at | t=18 ms | t=5 ms |
| **Click waited** | **15 ms** | **2 ms** |
| Total render time | 18 ms | 18 ms |

**Important:** The total work is the same (18 ms). Fiber does not make rendering faster. It makes the page **respond sooner** in the middle of the work.

---

## 4. Prioritizing the work

### What does Fiber use to prioritize?

It uses **lanes**. A lane is a **single bit** in a binary number. Each kind of update gets its own lane:

```js
const SyncLane            = 0b0000001;   // clicks, typing        (most urgent)
const InputContinuousLane = 0b0000100;   // drag, scroll, hover
const DefaultLane         = 0b0010000;   // normal setState
const TransitionLane      = 0b1000000;   // startTransition       (can wait)
const IdleLane            = 0b0100000000000000000000000000000; // lowest
```

A smaller bit means a higher priority.

### How does React decide which lane an update gets?

When you call `setState`, React looks at **what caused it**:

```js
function requestUpdateLane() {
  if (insideStartTransition)      return TransitionLane;     // you said it can wait
  if (eventIsClickOrKeypress)     return SyncLane;           // user is waiting right now
  if (eventIsScrollOrMouseMove)   return InputContinuousLane;
  return DefaultLane;                                        // everything else
}
```

The update is stored in the root's pending lanes as a bit:

```js
root.pendingLanes |= lane;      // "there is work waiting in this lane"
```

### How does React pick the most urgent lane?

With one bit trick: **the lowest set bit is the most urgent**.

```js
function getHighestPriorityLane(lanes) {
  return lanes & -lanes;        // keeps only the lowest set bit
}
```

Example: both a transition and a typing update are waiting.

```
pending lanes        = 1000001     (TransitionLane + SyncLane)
lanes & -lanes       = 0000001     (SyncLane wins)
```

Bits are used because this check is one fast CPU operation, and one number can hold many waiting lanes at once.

### What happens when an urgent update arrives during a slow render?

React cannot keep building a draft for old, low-priority work while urgent work is waiting. So it:

1. **Discards** the half-built draft (safe, because the draft never touched the screen).
2. **Renders and commits** the urgent update.
3. **Restarts** the low-priority render from the beginning.

This is safe because of the two-tree design: the render phase only edits the draft tree, never the one on screen.

> React also turns the lane into a Scheduler priority. That is how it decides *when* to run the callback: urgent lanes run right away, low lanes can wait.

### Before: no priorities (first come, first served)

```js
// 3b-priorities-before.js
let now = 0;
const log = (msg) => console.log(`t=${String(now).padStart(2)}ms  ${msg}`);

const updates = [
  { at: 0, label: "setFilter (big list, 12ms)",        cost: 12 },
  { at: 3, label: "setText: user typed a letter (2ms)", cost: 2 },
];

for (const u of updates) {
  log(`start rendering: ${u.label}`);
  now += u.cost;                                  // cannot be interrupted
  log(`COMMIT: ${u.label}`);
}
log(`typed letter appeared on screen at t=${now}ms, but the user typed it at t=3ms (waited ${now - 3}ms)`);
```

**Output:**

```
t= 0ms  start rendering: setFilter (big list, 12ms)
t=12ms  COMMIT: setFilter (big list, 12ms)
t=12ms  start rendering: setText: user typed a letter (2ms)
t=14ms  COMMIT: setText: user typed a letter (2ms)
t=14ms  typed letter appeared on screen at t=14ms, but the user typed it at t=3ms (waited 11ms)
```

**What this shows:** The tiny typing update was stuck behind the big list update. The user typed at t=3 ms, but saw the letter at t=14 ms. The input felt laggy.

### After: lanes + pausing + discarding

```js
// 3-priorities.js
let now = 0;
const SLICE = 5;
const log = (msg) => console.log(`t=${String(now).padStart(2)}ms  ${msg}`);

// Lanes are bits. A smaller bit = more urgent.
const SyncLane       = 0b0000001;         // 1   (typing, clicks)
const TransitionLane = 0b1000000;         // 64  (startTransition)
const laneName = (l) => (l === SyncLane ? "SyncLane" : "TransitionLane");

// the lowest set bit is the most urgent lane
const pickLane = (lanes) => lanes & -lanes;

// what each lane has to render (every unit costs 1 ms)
const work = {
  [SyncLane]: ["Input", "Preview"],
  [TransitionLane]: ["List", ...Array.from({ length: 11 }, (_, i) => `Item${i + 1}`)],
};

const events = [
  { at: 0, lane: TransitionLane, label: "setFilter inside startTransition (big list)", done: false },
  { at: 3, lane: SyncLane,       label: "setText: user typed a letter",                 done: false },
];

const root = { pending: 0, lane: 0, index: 0 };   // pending lanes, lane being rendered, progress
const startedBefore = {};

function deliverEvents() {                        // setState calls add bits to root.pending
  for (const e of events) {
    if (!e.done && e.at <= now) {
      e.done = true;
      root.pending |= e.lane;
      log(`update: ${e.label}  [${laneName(e.lane)}, pending bits = ${root.pending.toString(2).padStart(7, "0")}]`);
    }
  }
}

deliverEvents();
while (root.pending) {
  const lane = pickLane(root.pending);            // choose the most urgent lane
  if (lane !== root.lane) {                       // different lane than the draft we were building?
    if (root.index > 0) {
      log(`more urgent work found -> DISCARD the ${laneName(root.lane)} draft (${root.index}ms of work thrown away)`);
    }
    root.lane = lane;
    root.index = 0;
    log(`${startedBefore[lane] ? "RESTART" : "start"} rendering ${laneName(lane)}`);
    startedBefore[lane] = true;
  }

  const units = work[lane];
  const sliceStart = now;
  // urgent lanes render in one go; the transition lane yields every SLICE ms
  while (root.index < units.length && (lane === SyncLane || now - sliceStart < SLICE)) {
    now += 1;
    root.index++;
  }

  if (root.index < units.length) {
    log(`  PAUSE after ${root.index}/${units.length} units of ${laneName(lane)}`);
    deliverEvents();                              // browser turn: new updates may arrive
    continue;
  }
  log(`COMMIT ${laneName(lane)} -> screen updated`);
  root.pending &= ~lane;                          // clear this lane's bit
  root.lane = 0;
  root.index = 0;
  deliverEvents();
}
```

**Output:**

```
t= 0ms  update: setFilter inside startTransition (big list)  [TransitionLane, pending bits = 1000000]
t= 0ms  start rendering TransitionLane
t= 5ms    PAUSE after 5/12 units of TransitionLane
t= 5ms  update: setText: user typed a letter  [SyncLane, pending bits = 1000001]
t= 5ms  more urgent work found -> DISCARD the TransitionLane draft (5ms of work thrown away)
t= 5ms  start rendering SyncLane
t= 7ms  COMMIT SyncLane -> screen updated
t= 7ms  RESTART rendering TransitionLane
t=12ms    PAUSE after 5/12 units of TransitionLane
t=17ms    PAUSE after 10/12 units of TransitionLane
t=19ms  COMMIT TransitionLane -> screen updated
```

### How to read this output

| Time | What happened | Which concept |
|---|---|---|
| t=0 | The slow update starts in `TransitionLane` (bit `1000000`) | Lane assigned from `startTransition` |
| t=5 | Time slice is used up, so React **pauses** | `shouldYield()` |
| t=5 | The typing update is noticed. Pending bits are now `1000001` | Two lanes waiting |
| t=5 | `pickLane` returns the lowest bit (`SyncLane`), so React **discards** the transition draft | `lanes & -lanes`, discard draft |
| t=7 | The typing update is **committed**. The letter is on screen | Urgent work done first |
| t=7 | The transition **restarts** from unit 0 | Low-priority work resumes later |
| t=19 | The transition commits | Low-priority work finishes |

> **Why the update is "noticed" at t=5 and not t=3:** the user typed during React's slice. The browser can only handle the event at a pause point. That is exactly why short slices matter: the longer the slice, the longer an urgent update waits to be noticed.

### Before vs After (priorities)

| | Before (no priorities) | After (lanes) |
|---|---|---|
| User typed at | t=3 ms | t=3 ms |
| Letter on screen at | t=14 ms | t=7 ms |
| **Typing waited** | **11 ms** | **4 ms** |
| Big list finished at | t=12 ms | t=19 ms |
| Work thrown away | 0 ms | 5 ms |

**Honest trade-off:** The urgent update got faster (11 ms to 4 ms), but the slow update got **slower** (finished at t=19 instead of t=12), because 5 ms of its work was discarded and redone. Fiber is choosing to make **what the user is touching** fast, at the cost of **background work** being slower.

---

## 5. Before vs After: the full comparison

| Situation | Before Fiber | With Fiber | Which tool helped |
|---|---|---|---|
| A click arrives during a long render | Waits 15 ms | Waits 2 ms | **Divide + Pause** |
| A typing update arrives behind a big list update | Waits 11 ms | Waits 4 ms | **Prioritize + Pause + Discard** |
| Total work for one render | 18 ms | 18 ms (same) | None: Fiber doesn't reduce work |
| Slow, low-priority update | Finishes at 12 ms | Finishes at 19 ms | Trade-off: it waits so urgent work can go first |
| One component that is itself slow | Blocks | Still blocks (see below) | Not solved by Fiber |

**How the three tools work together on one update:**

```
setState called
     |
     v
[PRIORITIZE]  decide the lane (click -> SyncLane, startTransition -> TransitionLane)
     |
     v
[DIVIDE]      render one fiber at a time (one fiber = one unit of work)
     |
     v
[PAUSE]       after ~5 ms, stop, let the browser handle input and paint
     |
     v
  more urgent update arrived? --yes--> discard draft, do urgent work first, restart later
     |
     no
     v
 continue from workInProgress ... until the whole draft is finished -> commit
```

---

## 6. What Fiber cannot do (limits)

### Limit 1: Fiber cannot pause inside one component

The pause check only runs **between fibers**. If a single component takes a long time, React has no chance to stop. You can see this by making `Post5` slow. Change this one line in `2-after-fiber-slicing.js`:

```js
const SLOW = { Post5: 12 };      // Post5 now takes 12 ms instead of 1 ms
```

**Output:**

```
t= 5ms  slice 1: App, Header, Sidebar, Menu, Profile   (5ms)
t= 5ms    PAUSE -> next fiber to do is "Main" (saved in workInProgress)
t= 5ms    browser handles: user clicks a button   (happened at t=3ms, waited 2ms)
t=10ms  slice 2: Main, Feed, Post1, Post2, Post3   (5ms)
t=10ms    PAUSE -> next fiber to do is "Post4" (saved in workInProgress)
t=23ms  slice 3: Post4, Post5   (13ms)
t=23ms    PAUSE -> next fiber to do is "Post6" (saved in workInProgress)
t=28ms    ...
t=29ms  render finished (18 fibers) -> commit
```

Look at slice 3: it was supposed to be about 5 ms but took **13 ms**, because `Post5` ran for 12 ms and React could not stop inside it. If the user had clicked during that time, the click would have waited. Fiber can only pause **after** the slow component finishes.

**Fix:** make the slow component smaller (split it), move heavy calculations out of render or cache them with `useMemo`, virtualize long lists, or mark the update as a transition.

### Limit 2: Some work is never paused

- **The commit phase** always runs in one go. Half-updated DOM must never be visible.
- **Urgent updates** (SyncLane, such as a click) render without yielding, so they finish as fast as possible.

### Limit 3: Discarded work is wasted work

In the priority demo, 5 ms of work was thrown away and redone. This is the price paid for making urgent updates fast.

---

## 7. Summary

| Question | Answer |
|---|---|
| **What divides the work?** | One **fiber** = one unit of work. Fibers are linked by `child`, `sibling`, `return`, so React always knows the next unit using only the current fiber. |
| **How does it pause?** | It leaves the work loop. The position is saved in `workInProgress`, so nothing is lost. |
| **When does it pause?** | After each fiber, when `shouldYield()` says the ~5 ms slice is used up. |
| **Why does it pause?** | The browser has one thread and must handle input and paint about every 16 ms. |
| **How does it resume?** | The Scheduler posts a message with `MessageChannel`, which starts a new browser task, and the loop continues from `workInProgress`. |
| **What prioritizes?** | **Lanes**: bits in a number. Smaller bit = more urgent. `lanes & -lanes` picks the most urgent waiting lane. |
| **How is a lane chosen?** | By what caused the update: click or typing gets `SyncLane`, `startTransition` gets a `TransitionLane`, and so on. |
| **What if urgent work arrives mid-render?** | React discards the draft, renders the urgent update first, then restarts the slow one. |
| **How does it help rendering?** | The total work stays the same, but the user's clicks and typing are handled sooner, because React keeps stepping aside for them. |

**One-sentence version:**
Fiber cuts rendering into one-fiber pieces, checks the clock after each piece, and steps aside when its 5 ms is used up or when a more urgent update (picked by lane bits) needs the screen first.

