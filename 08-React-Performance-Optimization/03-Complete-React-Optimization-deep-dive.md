We’ll learn **React Optimization from the inside**, not just memorize techniques.

I’ll teach you like an experienced React engineer: **what happens internally → why the problem occurs → how React handles it → optimization technique → code → how to identify it in DevTools → interview explanation.**

# 🚀 React Optimization — Part 1
## Understanding React Rendering Internals

Before `React.memo`, `useMemo`, `useCallback`, caching, virtualization, etc., you need to understand **what React actually does when something changes.**

---

# 1. First: What does "React renders" actually mean?

A very common misunderstanding is:

> "When React renders, React changes the DOM."

❌ That's not exactly correct.

Rendering and DOM updating are **two different stages**.

Think of it like this:

```text
State / Props / Context changes
            ↓
      React schedules work
            ↓
       Component renders
            ↓
   React creates new element tree
            ↓
     Reconciliation
            ↓
    React determines what changed
            ↓
       Commit phase
            ↓
      DOM is updated
```

This distinction is the foundation of React optimization.

---

# 2. Let's take a very simple example

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

Initially:

```text
count = 0
```

React produces something conceptually like:

```text
Counter
  │
  └── div
       ├── h1 → 0
       └── button → Increment
```

Now click the button.

```js
setCount(1)
```

What happens internally?

---

# 3. `setState` does NOT directly change the DOM

This is extremely important.

When you do:

```js
setCount(1);
```

React doesn't immediately say:

```text
DOM h1 = 1
```

Instead, React is informed:

> "The state for this component has changed. This component needs to be rendered again."

Conceptually:

```text
setCount(1)
      ↓
React receives update
      ↓
Update is scheduled
      ↓
Counter renders again
```

---

# 4. What does "render again" mean?

React calls your component function again.

Before:

```jsx
function Counter() {
  const count = 0;

  return <h1>{count}</h1>;
}
```

Conceptually the result is:

```jsx
<h1>0</h1>
```

After state update:

```jsx
function Counter() {
  const count = 1;

  return <h1>{count}</h1>;
}
```

The result becomes:

```jsx
<h1>1</h1>
```

React now has:

### Previous result

```text
<h1>0</h1>
```

### New result

```text
<h1>1</h1>
```

React compares them.

---

# 5. This is where Reconciliation comes in

React asks:

> "What changed between the previous React tree and the new React tree?"

Conceptually:

```text
Previous
<h1>0</h1>

       ↓ compare

New
<h1>1</h1>
```

React determines:

```text
Element type: same
        ↓
<h1> remains <h1>

Text content: changed
        ↓
0 → 1
```

Therefore React doesn't need to recreate the entire DOM.

It can perform the necessary DOM update:

```js
h1.textContent = "1";
```

Conceptually.

---

# 6. This gives us the first major optimization principle

### React rendering ≠ DOM updating

A component can **render** without causing a visible DOM change.

For example:

```jsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <h1>Hello</h1>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>
    </>
  );
}
```

When `count` changes:

```text
App renders again
        ↓
React generates new element tree
        ↓
React compares old vs new
        ↓
<h1>Hello</h1> is identical
        ↓
No DOM update for h1
```

Only the button text needs updating.

---

# 7. Then why do we care about unnecessary re-renders?

Excellent question.

Because **rendering itself has a cost**.

Imagine:

```text
App
│
├── Header
├── Sidebar
├── ProductList
│    ├── Product
│    ├── Product
│    ├── Product
│    ├── Product
│    └── Product
│
└── Footer
```

Suppose only a search input changes.

If your architecture is poor:

```text
Search changes
      ↓
App re-renders
      ↓
ProductList re-renders
      ↓
100 Product components render
```

Even if the DOM ultimately changes only one small part, React still had to perform rendering work.

That's where optimization becomes important.

---

# 8. Parent re-render → Child re-render

This is one of the most important concepts.

Consider:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child />
    </>
  );
}
```

And:

```jsx
function Child() {
  console.log("Child rendered");

  return <h2>Hello</h2>;
}
```

Click the button.

What happens?

```text
setCount()
   ↓
Parent renders
   ↓
Parent calls <Child />
   ↓
Child renders
```

So you'll see:

```text
Child rendered
Child rendered
Child rendered
...
```

even though `Child` has nothing to do with `count`.

This is an **unnecessary render from the application's perspective**.

---

# 9. Why does React do this?

This is a very important "inner working" question.

React doesn't automatically know:

> "Child doesn't care about this state."

From the parent's perspective, React sees:

```jsx
<Child />
```

The parent has produced a new React element tree.

React therefore needs to reconcile the child subtree.

Without optimization mechanisms, React follows the normal rendering model.

---

# 10. Now we meet `React.memo`

We can tell React:

> "If this component receives the same props, don't unnecessarily re-render it."

```jsx
const Child = React.memo(function Child() {
  console.log("Child rendered");

  return <h2>Hello</h2>;
});
```

Now:

```text
Parent state changes
        ↓
Parent renders
        ↓
Child receives props
        ↓
React.memo checks props
        ↓
Props unchanged
        ↓
Child render can be skipped
```

That's the fundamental idea behind `React.memo`.

---

# 11. But here's an important catch

Consider:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const user = {
    name: "Nikitha"
  };

  return <Child user={user} />;
}
```

And:

```jsx
const Child = React.memo(function Child({ user }) {
  console.log("Child rendered");

  return <h1>{user.name}</h1>;
});
```

You might think:

> "user didn't change, so Child shouldn't render."

But it **can render again**.

Why?

Because every render creates a new object.

```js
const user = {
  name: "Nikitha"
};
```

First render:

```text
user → Object #1
```

Second render:

```text
user → Object #2
```

They contain the same data, but they are different object references.

---

# 12. Referential Equality

JavaScript compares objects by reference.

```js
const a = { name: "Nikitha" };
const b = { name: "Nikitha" };

console.log(a === b);
```

Result:

```text
false
```

Because:

```text
a → Object #1

b → Object #2
```

Different references.

This concept is absolutely critical for React optimization.

---

# 13. This is why `useMemo` exists

We can preserve the object reference:

```jsx
const user = useMemo(() => {
  return {
    name: "Nikitha"
  };
}, []);
```

Now React can reuse the same object reference between renders.

Conceptually:

```text
First render
user → Object #1

Second render
user → Object #1

Third render
user → Object #1
```

Therefore:

```text
React.memo
     +
useMemo
     ↓
Child can skip unnecessary rendering
```

---

# 14. And this is why `useCallback` exists

Functions are also objects in JavaScript.

Consider:

```jsx
function Parent() {
  const handleClick = () => {
    console.log("clicked");
  };

  return <Child onClick={handleClick} />;
}
```

Every Parent render creates a new function:

```text
Render 1
handleClick → Function #1

Render 2
handleClick → Function #2

Render 3
handleClick → Function #3
```

Even though the function's code looks identical.

Therefore:

```jsx
React.memo(Child)
```

may not help if you're passing that function as a prop.

`useCallback` allows React to preserve the function reference:

```jsx
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

Conceptually:

```text
Render 1 → Function #1
Render 2 → Function #1
Render 3 → Function #1
```

---

# 15. Now you can understand the optimization family

These three are NOT random React APIs.

They solve related problems:

```text
                    React Optimization
                           │
          ┌────────────────┼────────────────┐
          ↓                ↓                ↓
    React.memo         useMemo         useCallback
          │                │                │
    Component           Value            Function
     rendering         reference         reference
```

### `React.memo`

> Can I skip rendering this component if its props haven't changed?

### `useMemo`

> Can I reuse this calculated value/reference?

### `useCallback`

> Can I reuse this function reference?

---

# 16. But don't start using them everywhere

This is where beginners often make a mistake.

❌ Bad mindset:

> "Optimization = useMemo + useCallback everywhere."

Correct mindset:

> "Optimization = identify expensive or unnecessary work, then reduce it."

For example:

```jsx
const number = useMemo(() => 10 + 20, []);
```

This is pointless.

Why?

Because:

```js
10 + 20
```

is incredibly cheap.

The memoization itself has overhead.

So you could actually make the code more complicated without gaining anything meaningful.

---

# 17. The Optimization Mental Model

From now on, whenever you see a React performance problem, ask these questions:

### Question 1

**What caused the render?**

```text
State?
Props?
Context?
Parent?
External store?
```

### Question 2

**Did the component actually need to render?**

### Question 3

**If it rendered, was the rendering expensive?**

### Question 4

**Did it cause expensive child renders?**

### Question 5

**Is the bottleneck CPU, memory, network, or DOM?**

### Question 6

**What is the cheapest solution?**

---

# 18. Your first optimization debugging example

Imagine:

```jsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <ExpensiveComponent />
    </>
  );
}
```

Suppose:

```jsx
function ExpensiveComponent() {
  // 500ms of expensive calculation
  return <div>Huge calculation</div>;
}
```

Every click causes:

```text
count changes
     ↓
App renders
     ↓
ExpensiveComponent renders
     ↓
500ms calculation
```

That's a performance problem.

Now you have several possible solutions depending on the situation.

For example:

```jsx
const ExpensiveComponent = React.memo(function ExpensiveComponent() {
  // expensive work
});
```

Now:

```text
count changes
     ↓
App renders
     ↓
React.memo checks ExpensiveComponent props
     ↓
No props changed
     ↓
ExpensiveComponent render skipped
```

That is a real optimization.

---

# 19. But there is an even deeper issue

What if the expensive calculation is **inside the parent**?

```jsx
function App() {
  const [count, setCount] = useState(0);

  const result = expensiveCalculation();

  return (...);
}
```

`React.memo` won't solve that.

Because the expensive calculation happens **before** the child even gets involved.

Here:

```text
App render
   ↓
expensiveCalculation()
   ↓
Child
```

You might use:

```jsx
const result = useMemo(() => {
  return expensiveCalculation();
}, [someDependency]);
```

Now the calculation is reused until its dependency changes.

This is the correct use case for `useMemo`.

---

# 20. The complete picture

You should now visualize React optimization like this:

```text
                    UPDATE
                      │
                      ↓
              React schedules work
                      │
                      ↓
                  RENDER
                      │
              ┌───────┴────────┐
              ↓                ↓
       Parent renders      Child renders
              │
              ↓
       New React tree
              │
              ↓
         RECONCILIATION
              │
              ↓
       What actually changed?
              │
              ↓
            COMMIT
              │
              ↓
          DOM updates
```

Optimization tries to reduce unnecessary work at different points:

```text
React.memo
    ↓
Reduce unnecessary component rendering

useMemo
    ↓
Reduce expensive recalculation

useCallback
    ↓
Preserve function identity

State colocation
    ↓
Reduce rendering scope

Context optimization
    ↓
Reduce unnecessary consumers

Debouncing
    ↓
Reduce excessive events/API requests

Caching
    ↓
Reduce network + computation

Code splitting
    ↓
Reduce initial JavaScript

Virtualization
    ↓
Reduce number of rendered DOM elements
```

---

# 🎯 What I want you to master FIRST

Don't move to 20 optimization techniques yet.

Our **first module** should be:

### Module 1 — React Rendering Internals

1. What is rendering?
2. What is re-rendering?
3. What triggers a render?
4. Render phase
5. Commit phase
6. Reconciliation
7. Virtual DOM
8. Fiber
9. Parent-child rendering
10. Component identity
11. Keys
12. Referential equality
13. Why unnecessary re-renders happen
14. How to detect them with React DevTools Profiler

Then:

### Module 2

```text
React.memo
   ↓
useMemo
   ↓
useCallback
   ↓
Referential equality
   ↓
Memoization pitfalls
```

Then we'll go into:

```text
State Optimization
       ↓
Context Optimization
       ↓
Debounce / Throttle
       ↓
API Caching
       ↓
Code Splitting
       ↓
Virtualization
       ↓
Concurrent React
       ↓
Browser Performance
       ↓
Pro-level Performance Architecture
```

**Next, I recommend we go very deep into `Render Phase vs Commit Phase` and `Reconciliation + Fiber`**, because once you genuinely understand those three, `React.memo`, `useMemo`, `useCallback`, and most React optimization techniques become much easier to understand rather than memorize.


# React Optimization — Part 2
# Render Phase vs Commit Phase + Reconciliation + Fiber

Now we go **one level deeper into React's internal working**.

I want you to understand this so well that later, when someone asks:

> "Why does `React.memo` improve performance?"

you can explain **what React is actually skipping internally**, instead of saying "it prevents re-render."

---

# 1. First, the complete React update pipeline

Suppose you click:

```jsx
<button onClick={() => setCount(count + 1)}>
  {count}
</button>
```

The simplified internal flow is:

```text
User clicks button
       ↓
Event handler executes
       ↓
setCount(1)
       ↓
React schedules an update
       ↓
RENDER PHASE
       ↓
React calls components
       ↓
New React element tree is produced
       ↓
Reconciliation
       ↓
React determines what changed
       ↓
COMMIT PHASE
       ↓
React applies necessary DOM changes
       ↓
Browser paints the result
```

There are **two major phases** you must remember:

```text
RENDER PHASE
     ↓
"WHAT should the UI look like?"

COMMIT PHASE
     ↓
"APPLY those changes to the actual DOM."
```

---

# 2. Render Phase

Let's start here.

Consider:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>{count}</h1>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

Initially:

```text
count = 0
```

React calls:

```text
Counter()
```

The component returns something conceptually like:

```text
div
 ├── h1 → 0
 └── button → Increment
```

This is the **render phase**.

---

# 3. Important: Render Phase doesn't mean DOM manipulation

This is a very important distinction.

During render:

```jsx
return (
  <h1>{count}</h1>
);
```

React is **not directly changing the browser DOM here**.

React is determining:

> "What should the UI look like based on the current state and props?"

Think of render phase as **calculation**.

```text
State + Props
     ↓
Component function
     ↓
React elements
     ↓
Desired UI
```

---

# 4. Then comes Reconciliation

React now has:

### Old tree

```text
<h1>0</h1>
```

### New tree

```text
<h1>1</h1>
```

React needs to determine:

> "What is actually different?"

This process is called **reconciliation**.

Conceptually:

```text
OLD TREE                 NEW TREE

<div>                     <div>
 └── <h1>0</h1>            └── <h1>1</h1>
       │                         │
       └──────── compare ────────┘
```

React notices:

```text
<div> → same
<h1>  → same element type
text  → changed
```

So React doesn't need to destroy the `<h1>` and create another one.

It knows:

```text
Update text:
0 → 1
```

---

# 5. Then comes Commit Phase

Now React takes the result of reconciliation and actually applies the required changes.

Conceptually:

```text
Reconciliation result
       ↓
"Change h1 text from 0 → 1"
       ↓
Commit
       ↓
DOM updated
```

So:

```text
Render Phase
    ↓
Calculate

Commit Phase
    ↓
Apply
```

---

# 6. Why are these phases separated?

This is where React becomes interesting.

React wants rendering work to be **interruptible**.

Imagine a huge application:

```text
10,000 components
```

Suppose React started rendering all of them and the browser suddenly needed to respond to:

```text
User typing
```

If React couldn't interrupt its work, the UI could become unresponsive.

React's modern architecture allows rendering work to be scheduled and prioritized.

This is where **Fiber** becomes important.

---

# 7. What is Fiber?

You will often hear:

> "React uses Fiber."

But beginners often memorize that without understanding it.

Think of Fiber as React's **internal unit of work and data structure for managing component work**.

Conceptually, instead of React thinking:

```text
"Render the entire application as one giant operation."
```

Fiber allows React to think more like:

```text
Work
 ↓
Component A
 ↓
Component B
 ↓
Component C
 ↓
Component D
```

Each component corresponds to a Fiber node representing information React needs about that component.

---

# 8. A simplified Fiber tree

Suppose your application is:

```jsx
<App>
  <Header />
  <Main>
    <ProductList />
    <Footer />
  </Main>
</App>
```

Conceptually:

```text
                App
                 │
          ┌──────┴──────┐
          ↓             ↓
       Header          Main
                        │
                  ┌─────┴─────┐
                  ↓           ↓
             ProductList    Footer
```

React maintains internal structures representing this tree.

Each Fiber contains information such as:

- Component/type
- Props
- State
- Relationships to other Fibers
- Pending work
- Effects/flags
- Alternate/current tree information

You don't directly manipulate Fibers in normal React development.

---

# 9. Why did React need Fiber?

The older React architecture was more difficult to interrupt during rendering.

Fiber was introduced to make React's rendering work more flexible.

It enables React to:

- Break work into units
- Schedule work
- Prioritize updates
- Pause rendering work
- Continue later
- Abandon unnecessary work
- Support concurrent rendering capabilities

This is one reason modern React can provide APIs such as:

```jsx
startTransition()
```

and:

```jsx
useTransition()
```

---

# 10. Important distinction: Fiber does NOT mean "React renders faster automatically"

Don't say:

> "Fiber makes React faster."

That's too simplistic.

A better explanation is:

> **Fiber gives React a more flexible architecture for scheduling and managing rendering work, allowing React to prioritize and interrupt work when appropriate.**

That is a much stronger interview answer.

---

# 11. Let's understand "work"

Suppose:

```text
App
├── Header
├── Sidebar
├── ProductList
│   ├── Product 1
│   ├── Product 2
│   ├── Product 3
│   └── Product 4
└── Footer
```

React may need to process many components.

Fiber lets React break this into units of work conceptually:

```text
Unit 1 → App
Unit 2 → Header
Unit 3 → Sidebar
Unit 4 → ProductList
Unit 5 → Product 1
Unit 6 → Product 2
...
```

This doesn't mean React literally performs one simple function call per Fiber in the exact way this diagram suggests, but it's a useful mental model.

---

# 12. Current Tree vs Work-in-Progress Tree

Now we're going deeper.

React maintains a representation of the currently committed UI.

Conceptually:

```text
CURRENT TREE
     ↓
What is currently on screen
```

When an update happens, React can build a new:

```text
WORK-IN-PROGRESS TREE
     ↓
What the UI should become
```

So imagine:

```text
CURRENT                         WORK-IN-PROGRESS

App                             App
 │                               │
 └── Counter                     └── Counter
       │                               │
      0                               1
```

React works on the new version.

Once the work is ready, React commits the necessary changes.

---

# 13. Why this matters for optimization

Now you can understand something important:

React doesn't necessarily need to throw away everything and rebuild the entire application.

It can determine:

```text
What changed?
What didn't?
Which subtree needs work?
Which subtree can be reused?
```

This is where techniques like:

```text
React.memo
keys
state placement
referential equality
```

become relevant.

---

# 14. Let's connect `React.memo` to reconciliation

Suppose:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child />
    </>
  );
}
```

Without memoization:

```text
count changes
    ↓
Parent update
    ↓
Parent renders
    ↓
Child is encountered
    ↓
Child may render
```

With:

```jsx
const Child = React.memo(function Child() {
  return <h1>Hello</h1>;
});
```

React can determine:

```text
Child's props?
     ↓
No change
     ↓
Memoized component can bail out
     ↓
No need to render Child again
```

The important word here is:

# Bailout

React can **bail out of unnecessary work** when it knows the subtree doesn't need to be recalculated.

---

# 15. "Bailout" is an important optimization concept

You will encounter this concept repeatedly.

A bailout essentially means:

> "I already have enough information to know that this part doesn't need to be recomputed."

Examples include optimizations involving:

- `React.memo`
- unchanged state
- unchanged props
- stable references
- certain Fiber reconciliation paths

---

# 16. Now let's understand keys

Consider:

```jsx
const users = [
  { id: 1, name: "A" },
  { id: 2, name: "B" },
  { id: 3, name: "C" }
];
```

Render:

```jsx
users.map(user => (
  <User key={user.id} user={user} />
))
```

React sees:

```text
User #1
User #2
User #3
```

Now suppose the order changes:

```text
User #3
User #1
User #2
```

Because the keys are stable:

```text
3 → same identity
1 → same identity
2 → same identity
```

React can understand that these are the same logical components moved around.

---

# 17. What if you use array index?

```jsx
users.map((user, index) => (
  <User key={index} user={user} />
))
```

Initially:

```text
index 0 → A
index 1 → B
index 2 → C
```

Now insert `X` at the beginning:

```text
index 0 → X
index 1 → A
index 2 → B
index 3 → C
```

React may interpret the identities as:

```text
old index 0 → new index 0
A → X
```

That's not the logical identity you wanted.

This can lead to:

- unnecessary work
- incorrect state preservation
- UI bugs in stateful list items

So keys aren't simply:

> "Something React needs to remove a warning."

Keys help React understand **component identity during reconciliation**.

---

# 18. Component identity

This is another major concept.

React doesn't just ask:

> "Does this look similar?"

It cares about identity based on things such as:

```text
Element type
+
Key
+
Position in the tree
```

This affects whether React:

```text
preserves a component
```

or:

```text
unmounts it and creates a new one
```

That directly affects state preservation.

---

# 19. Example of state preservation

Consider:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

If React considers the same component identity to remain in the same position, its state can be preserved.

But if you change its identity with a key:

```jsx
<Counter key={userId} />
```

and:

```text
userId: 1 → 2
```

React can treat it as a different component instance.

Conceptually:

```text
Counter key=1
     ↓
state = 5

key changes

Counter key=2
     ↓
new identity
     ↓
state resets
```

This is why keys are about **identity**, not just list rendering.

---

# 20. Now let's put everything together

When state changes:

```text
                STATE UPDATE
                     ↓
              React schedules work
                     ↓
                Fiber work
                     ↓
               RENDER PHASE
                     ↓
            Component functions run
                     ↓
          New React element tree
                     ↓
              RECONCILIATION
                     ↓
       Compare current vs new tree
                     ↓
           Identify necessary work
                     ↓
               COMMIT PHASE
                     ↓
             DOM mutations
                     ↓
             Browser paints
```

And optimization tries to reduce unnecessary work along this path.

---

# 21. Where each optimization fits

Now the roadmap should start making sense.

### `React.memo`

```text
Component rendering
       ↓
Can this subtree be skipped?
```

### `useMemo`

```text
Rendering
   ↓
Expensive calculation
   ↓
Can previous result be reused?
```

### `useCallback`

```text
Rendering
   ↓
Function created
   ↓
Can function reference remain stable?
```

### State colocation

```text
State update
   ↓
Reduce how much of the tree needs to respond
```

### Keys

```text
Reconciliation
   ↓
Correctly identify component instances
```

### Virtualization

```text
Large list
   ↓
Don't create/render thousands of unnecessary items
```

### `useTransition`

```text
Scheduling
   ↓
Mark non-urgent work
   ↓
Keep urgent interactions responsive
```

---

# 🧠 The most important mental model

I want you to remember this diagram:

```text
             SOMETHING CHANGES
                    ↓
              UPDATE SCHEDULED
                    ↓
             ┌──────────────┐
             │ RENDER PHASE │
             └──────────────┘
                    ↓
         "What should the UI be?"
                    ↓
          New React element tree
                    ↓
             RECONCILIATION
                    ↓
        "What actually changed?"
                    ↓
             ┌──────────────┐
             │ COMMIT PHASE │
             └──────────────┘
                    ↓
          Apply DOM mutations
                    ↓
               Browser UI
```

And:

```text
Fiber
  ↓
helps React manage/schedule rendering work

Reconciliation
  ↓
figures out how the new tree differs

Commit
  ↓
applies the required changes

Optimization
  ↓
reduces unnecessary work
```

---

# 🎯 One interview question you should now be able to answer

### "Does a React re-render mean the DOM is updated?"

A strong answer:

> **No. A React re-render means React executes the relevant component rendering work to produce a new React element tree. React then reconciles the new tree with the previous one and determines what actually changed. Only the necessary DOM mutations are applied during the commit phase. So a component can re-render without causing a DOM update.**

That's the level of understanding we're aiming for.

---

## Next: Part 3 🔥

The next concept is **"Exactly what causes a component to re-render?"**

We'll take one component tree and trace **every single trigger**:

```text
State change
Props change
Parent re-render
Context change
Reducer update
External store
Force update
          ↓
Which components render?
Which don't?
Why?
```

Then we'll use that understanding to introduce **`React.memo` properly**, including the part most tutorials skip: **what React actually compares and when the bailout happens.**

# React Optimization — Part 3
# What Exactly Causes a Component to Re-render?

Now we get to one of the **most important concepts in React performance**.

If you understand this properly, `React.memo`, `useMemo`, `useCallback`, Context optimization, Zustand selectors, etc. will become much easier.

---

# 1. First, remove one misconception

A lot of people say:

> "A component re-renders when its props change."

That's **incomplete**.

A component can re-render for several different reasons.

The major ones are:

```text
1. Its own state changes
2. Its parent renders
3. Its props change
4. Context it consumes changes
5. An external store subscription changes
```

Let's understand each one **internally**.

---

# 2. Case #1 — Component's own state changes

Consider:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  console.log("Counter rendered");

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

Initial render:

```text
Counter
count = 0
```

You click the button:

```text
setCount(1)
```

React receives a state update.

Conceptually:

```text
setCount(1)
    ↓
Update associated with Counter
    ↓
React schedules Counter
    ↓
Counter renders again
```

So:

```text
State changes
      ↓
Component re-renders
```

This is the simplest case.

---

# 3. But there is an interesting optimization

What if you do:

```jsx
setCount(0);
```

when `count` is already:

```text
0
```

React can recognize that the state value hasn't meaningfully changed.

Conceptually:

```text
Old state = 0
New state = 0

      ↓

No meaningful state change
      ↓
React can bail out
```

This is another example of a **bailout**.

---

# 4. Case #2 — Parent re-renders

This is extremely important.

Consider:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child />
    </>
  );
}
```

```jsx
function Child() {
  console.log("Child rendered");

  return <h2>Hello</h2>;
}
```

Now click the button.

You might think:

> "Only `Parent`'s state changed, so only Parent should render."

But normally:

```text
Parent state changes
       ↓
Parent renders
       ↓
React processes its children
       ↓
Child can render
```

So:

```text
Parent
  ↓
Child
```

The child doesn't need to have its own state change.

---

# 5. Why does the child render?

Because React's rendering model follows the component tree.

Parent produces:

```jsx
<Child />
```

When Parent renders again, React needs to reconcile the output of Parent.

Without memoization, React doesn't automatically assume:

> "This child must be exactly the same."

It processes the subtree according to React's reconciliation rules.

---

# 6. Very important distinction

When we say:

> "Parent re-render causes child re-render"

don't interpret that as:

> "The browser DOM of the child is recreated."

That's wrong.

Remember:

```text
Component render
       ≠
DOM recreation
```

The child may execute its component function again, while React ultimately performs **zero DOM changes** for that child.

---

# 7. Let's visualize it

Suppose:

```text
App
│
├── Header
├── Main
│    ├── Search
│    └── ProductList
│         ├── Product
│         ├── Product
│         └── Product
│
└── Footer
```

If `App` updates:

```text
App renders
   ↓
Header processed
   ↓
Main processed
   ↓
Search processed
   ↓
ProductList processed
   ↓
Product components processed
   ↓
Footer processed
```

Potentially a lot of work.

This is why **rendering scope** matters.

---

# 8. Case #3 — Props change

Now consider:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child count={count} />
    </>
  );
}
```

Child:

```jsx
function Child({ count }) {
  console.log("Child rendered");

  return <h2>{count}</h2>;
}
```

Now:

```text
count changes
     ↓
Parent renders
     ↓
Child receives new prop
     ↓
Child renders
```

Here the child actually **needs** the new value.

---

# 9. But here's the important question

What if the prop doesn't change?

```jsx
<Child name="Nikitha" />
```

Parent renders again.

Is the string prop different?

```text
"Nikitha"
"Nikitha"
```

No.

But without memoization, Child can still be processed as part of the parent's rendering.

Now we have a useful distinction:

### Parent rendering

and

### Child deciding whether it needs to render

are different things.

This is where `React.memo` comes in.

---

# 10. Case #4 — Context changes

Suppose:

```jsx
const ThemeContext = createContext();
```

Provider:

```jsx
function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext.Provider value={theme}>
      <Page />
    </ThemeContext.Provider>
  );
}
```

Consumer:

```jsx
function Button() {
  const theme = useContext(ThemeContext);

  return (
    <button className={theme}>
      Click
    </button>
  );
}
```

When:

```js
setTheme("dark");
```

happens:

```text
Theme changes
      ↓
Context value changes
      ↓
Components consuming that context
      ↓
need to update
```

This is why Context can become a performance problem if you put too much unrelated state into one context.

---

# 11. Context is NOT exactly the same as props

Suppose:

```jsx
const Child = React.memo(function Child() {
  const theme = useContext(ThemeContext);

  return <div>{theme}</div>;
});
```

You might think:

> "React.memo means Child can never re-render unless props change."

❌ No.

`React.memo` primarily controls updates coming through **props/parent rendering paths**.

If the component itself consumes a context and that context changes, the consumer needs to update.

So:

```text
React.memo
    ↓
Can prevent some parent-driven renders

BUT

Context changes
    ↓
Consumer may still need to render
```

This distinction becomes very important later.

---

# 12. Case #5 — External stores

Modern React applications often use state libraries.

Examples:

```text
Redux
Zustand
MobX
TanStack Query
```

A component might subscribe to some external state.

For example, conceptually:

```text
Global Store
     │
     ├── user
     ├── theme
     ├── cart
     └── products
```

If a component subscribes specifically to:

```text
cart
```

then ideally:

```text
theme changes
     ↓
Cart component
     ↓
should NOT need to render
```

This is why **selectors** are so important in state management optimization.

We'll study this later.

---

# 13. The complete trigger map

Remember this:

```text
                 COMPONENT UPDATE
                       │
       ┌───────────────┼────────────────┐
       ↓               ↓                ↓
    Own State       Parent           Context
       │             renders          changes
       ↓               │                ↓
    Render            ↓             Consumer
                 Child processed      updates
       
                       │
                       ↓
                 External Store
                    changes
                       ↓
                  Subscriber
                    updates
```

And props are involved when:

```text
Parent renders
      ↓
New props passed
      ↓
Child receives them
```

---

# 14. Let's build a real example

Consider:

```jsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Header />
      <User />
      <ProductList />
    </>
  );
}
```

Now click the button.

What happens?

```text
setCount()
   ↓
App updates
   ↓
App renders
   ↓
Header is processed
User is processed
ProductList is processed
```

Even though:

```text
Header doesn't care about count
User doesn't care about count
ProductList doesn't care about count
```

This is the kind of situation where optimization can become useful.

---

# 15. Solution #1 — Move the state down

Instead of:

```text
App
│
├── Counter
├── Header
├── User
└── ProductList
```

where App owns the counter state, move the state into Counter:

```text
App
│
├── Counter
│    └── count state
│
├── Header
├── User
└── ProductList
```

Now:

```text
Counter state changes
       ↓
Counter renders
```

rather than:

```text
App state changes
       ↓
Entire subtree potentially processes
```

This technique is called:

# State Colocation

And honestly, **state colocation is one of the most underrated React performance techniques**.

---

# 16. Optimization doesn't always mean memoization

This is a major lesson.

Beginners often see:

```text
unnecessary render
```

and immediately think:

```jsx
React.memo()
```

But sometimes the better solution is simply:

> **Move the state closer to where it's needed.**

For example:

```text
BAD ARCHITECTURE

App owns search state
      ↓
Entire application responds
```

Better:

```text
ProductPage
      ↓
SearchBar owns search input state
```

Now unrelated components don't need to participate in that update.

---

# 17. State colocation can be more powerful than memoization

Imagine:

```text
100 components
```

and only one needs a piece of state.

Option A:

```text
Global state
   ↓
100 components potentially involved
   ↓
memoization everywhere
```

Option B:

```text
Local state
   ↓
1 component
```

Often B is much simpler and more efficient.

This is why good React architecture is itself a performance optimization.

---

# 18. Another important concept: Render propagation

Think of a parent update as work flowing down the tree:

```text
Parent update
     ↓
Parent render
     ↓
Children become candidates for reconciliation
     ↓
Their subtrees are processed unless React can bail out
```

Optimization tries to stop unnecessary work from propagating too far.

For example:

```text
             App
              │
       ┌──────┴──────┐
       ↓             ↓
    Counter        Products
       │
    state update
```

Ideally, a Counter update shouldn't cause an expensive Products subtree to do unnecessary work.

---

# 19. Now we are ready for `React.memo`

Before we move on, you need to understand one phrase:

# "Bailout"

Suppose:

```jsx
const ProductList = React.memo(function ProductList({ products }) {
  ...
});
```

Parent renders.

React reaches ProductList.

It checks the relevant props.

Conceptually:

```text
Previous props
products → reference A

New props
products → reference A
```

Same reference.

React can say:

```text
"No relevant prop change.
I can reuse the previous result."
```

That's the basic optimization idea.

---

# 20. But this leads to a huge topic

What exactly does React compare?

Suppose:

```jsx
<Product
  name="Phone"
  price={50000}
  active={true}
/>
```

Does React:

```text
deep compare every object?
```

No.

Does it:

```text
compare every nested property recursively?
```

No.

Then what does it do?

This takes us directly into:

# Referential Equality + Shallow Comparison

And that is the key to understanding why:

```text
React.memo
      +
useMemo
      +
useCallback
```

work together.

---

# 🧠 Your mental model so far

You should now have this:

```text
WHAT CAN TRIGGER WORK?

1. Own state update
2. Parent render
3. New props
4. Context update
5. External store update
```

Then:

```text
React receives update
       ↓
Schedule work
       ↓
Render phase
       ↓
Reconciliation
       ↓
Can React bail out?
       ↓
Yes → reuse/skip appropriate work
No  → continue rendering
       ↓
Commit phase
       ↓
DOM mutations
```

And the biggest optimization question becomes:

> **"Can React avoid doing this work at all?"**

That's the core idea behind React optimization.

---

## Next lesson 🔥

**Part 4 — Referential Equality + Shallow Comparison + `React.memo`**

We'll take this exact example:

```jsx
const user = {
  name: "Nikitha"
};
```

and go inside:

```text
Object reference
       ↓
Shallow comparison
       ↓
React.memo
       ↓
Why object props break memoization
       ↓
useMemo fixes object reference
       ↓
Why functions break memoization
       ↓
useCallback fixes function reference
```

This is where the **real inner working of `React.memo + useMemo + useCallback`** starts.

# React Optimization — Part 4
# Referential Equality → Shallow Comparison → `React.memo`

Now we're at one of the **most important parts of React optimization**.

If you truly understand this lesson, you'll understand **why `React.memo`, `useMemo`, and `useCallback` are connected**.

---

# 1. First: What is Referential Equality?

Let's forget React for a moment and look at JavaScript.

Consider primitives:

```js
const a = 10;
const b = 10;

console.log(a === b);
```

Result:

```text
true
```

Because primitives are compared by their value.

Now:

```js
const a = { name: "Nikitha" };
const b = { name: "Nikitha" };

console.log(a === b);
```

Result:

```text
false
```

Why?

Because objects are compared by **reference**, not by their contents.

---

# 2. Visualize it in memory

Imagine:

```js
const a = { name: "Nikitha" };
```

Conceptually:

```text
a
│
└──────→ Object #101
          ├── name: "Nikitha"
```

Then:

```js
const b = { name: "Nikitha" };
```

creates another object:

```text
a
│
└──────→ Object #101
          name: "Nikitha"


b
│
└──────→ Object #202
          name: "Nikitha"
```

The values are identical.

But:

```text
Object #101 ≠ Object #202
```

Therefore:

```js
a === b // false
```

---

# 3. What about arrays?

Exactly the same.

```js
const arr1 = [1, 2, 3];
const arr2 = [1, 2, 3];

console.log(arr1 === arr2);
```

Result:

```text
false
```

Because:

```text
arr1 → Array #1

arr2 → Array #2
```

Different references.

---

# 4. Functions are also objects

This is extremely important for React.

```js
const fn1 = () => {};
const fn2 = () => {};

console.log(fn1 === fn2);
```

Result:

```text
false
```

Every time you create a function expression during execution, you get a new function object.

So:

```text
Object
Array
Function
```

are all reference types.

---

# 5. Why does this matter to React?

Because React needs to determine:

> "Did this prop change?"

Suppose:

```jsx
<Child user={user} />
```

React needs to compare the previous props with the new props.

For primitive:

```js
"John" === "John"
```

→ `true`

For object:

```js
{} === {}
```

→ `false`

For function:

```js
(() => {}) === (() => {})
```

→ `false`

This is where **shallow comparison** becomes important.

---

# 6. What is shallow comparison?

Suppose a component receives:

```jsx
<Child
  name="Nikitha"
  age={22}
/>
```

Conceptually:

```text
Previous props
{
  name: "Nikitha",
  age: 22
}

New props
{
  name: "Nikitha",
  age: 22
}
```

A shallow comparison checks the properties at the first level.

Conceptually:

```text
name
old → "Nikitha"
new → "Nikitha"
same ✓

age
old → 22
new → 22
same ✓
```

So React can determine that the props are equal.

---

# 7. But nested objects are different

Consider:

```jsx
<Child
  user={{
    name: "Nikitha"
  }}
/>
```

Previous:

```text
props
 └── user → Object #1
```

New:

```text
props
 └── user → Object #2
```

Even though:

```text
Object #1
{
  name: "Nikitha"
}

Object #2
{
  name: "Nikitha"
}
```

they are different references.

Therefore:

```text
old user === new user
```

is:

```text
false
```

---

# 8. Now introduce `React.memo`

Consider:

```jsx
const Child = React.memo(function Child({ name }) {
  console.log("Child rendered");

  return <h1>{name}</h1>;
});
```

`React.memo` tells React:

> "If this component receives equivalent props, try to avoid rendering it again."

By default, React performs a **shallow comparison of props**.

Conceptually:

```text
Previous props
       ↓
Compare
       ↑
New props
```

If they're equal:

```text
        ↓
    BAILOUT
        ↓
Skip unnecessary component rendering
```

---

# 9. Example with primitive props

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child name="Nikitha" />
    </>
  );
}
```

Child:

```jsx
const Child = React.memo(function Child({ name }) {
  console.log("Child rendered");

  return <h1>{name}</h1>;
});
```

When `count` changes:

```text
Parent renders
      ↓
Child receives "Nikitha"
      ↓
Previous: "Nikitha"
New:      "Nikitha"
      ↓
Equal
      ↓
Bailout
```

So Child's render can be skipped.

---

# 10. Now the famous object example

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const user = {
    name: "Nikitha"
  };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child user={user} />
    </>
  );
}
```

Child:

```jsx
const Child = React.memo(function Child({ user }) {
  console.log("Child rendered");

  return <h1>{user.name}</h1>;
});
```

You might expect:

```text
user didn't change
      ↓
Child shouldn't render
```

But that's not necessarily true.

Why?

---

# 11. Look at what happens during each Parent render

First render:

```js
const user = {
  name: "Nikitha"
};
```

Creates:

```text
Object #1
```

So:

```text
user → Object #1
```

Now `count` changes.

Parent executes again:

```js
const user = {
  name: "Nikitha"
};
```

This creates:

```text
Object #2
```

Now:

```text
Previous user → Object #1

New user      → Object #2
```

Therefore:

```js
previousUser === newUser
```

is:

```text
false
```

So `React.memo` sees a changed prop reference.

---

# 12. This is one of the most important React optimization concepts

The data is the same:

```text
name = Nikitha
```

but the **reference is different**.

Therefore:

```text
Same value
     ≠
Same reference
```

This is why people often say:

> "Objects break memoization."

More accurately:

> **Creating a new object reference on every render can prevent shallow prop comparison from bailing out.**

That's the technically correct explanation.

---

# 13. How `useMemo` helps

We can write:

```jsx
const user = useMemo(() => {
  return {
    name: "Nikitha"
  };
}, []);
```

Now React can preserve the memoized value between renders.

Conceptually:

```text
First Parent render
        ↓
Create Object #1

Second Parent render
        ↓
useMemo returns Object #1

Third Parent render
        ↓
useMemo returns Object #1
```

So:

```text
Previous user → Object #1
New user      → Object #1
```

Therefore:

```js
previousUser === newUser
```

becomes:

```text
true
```

And `React.memo` can potentially bail out.

---

# 14. This is the relationship between `useMemo` and `React.memo`

This is worth memorizing conceptually:

```text
             Parent
               │
               │ creates object
               ↓
          Object reference
               │
               ↓
          Child receives
               │
               ↓
          React.memo
               │
               ↓
        Shallow comparison
               │
        ┌──────┴──────┐
        ↓             ↓
      Same          Different
        ↓             ↓
     Bailout        Render
```

`useMemo` can help keep:

```text
Object reference stable
```

so that:

```text
React.memo
```

has a better chance to bail out.

---

# 15. Now let's talk about functions

Consider:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    console.log("clicked");
  };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child onClick={handleClick} />
    </>
  );
}
```

Child:

```jsx
const Child = React.memo(function Child({ onClick }) {
  console.log("Child rendered");

  return <button onClick={onClick}>Child</button>;
});
```

You might think:

> "handleClick didn't change."

But technically, the function reference **does change**.

---

# 16. Why?

Every time Parent executes:

```js
const handleClick = () => {
  console.log("clicked");
};
```

a new function object is created.

Conceptually:

```text
Render 1
handleClick → Function #1

Render 2
handleClick → Function #2

Render 3
handleClick → Function #3
```

Therefore:

```js
oldHandleClick === newHandleClick
```

is:

```text
false
```

So `React.memo` sees:

```text
onClick prop changed
```

and the child can render again.

---

# 17. `useCallback` solves function-reference stability

```jsx
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

Now conceptually:

```text
Render 1
handleClick → Function #1

Render 2
handleClick → Function #1

Render 3
handleClick → Function #1
```

The reference stays stable until a dependency changes.

Therefore:

```text
React.memo
     +
useCallback
     ↓
Stable function prop
     ↓
Better chance of bailout
```

---

# 18. Now understand the three together

This is the relationship:

### `React.memo`

Protects the **component**.

```text
"Don't render me if my props haven't meaningfully changed."
```

### `useMemo`

Stabilizes a **value/reference**.

```text
"Reuse this calculated value/reference."
```

### `useCallback`

Stabilizes a **function reference**.

```text
"Reuse this function reference."
```

---

# 19. One complete example

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const user = useMemo(() => ({
    name: "Nikitha"
  }), []);

  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child
        user={user}
        onClick={handleClick}
      />
    </>
  );
}
```

Child:

```jsx
const Child = React.memo(function Child({ user, onClick }) {
  console.log("Child rendered");

  return (
    <div>
      <h2>{user.name}</h2>
      <button onClick={onClick}>
        Click
      </button>
    </div>
  );
});
```

Now:

```text
count changes
     ↓
Parent renders
     ↓
useMemo returns same user reference
     ↓
useCallback returns same function reference
     ↓
Child receives same props
     ↓
React.memo comparison
     ↓
Props unchanged
     ↓
BAILOUT
```

So Child doesn't need to render again.

---

# 20. But here's the trap ⚠️

Don't write this everywhere:

```jsx
const user = useMemo(() => ({
  name: "Nikitha"
}), []);

const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

Why?

Because memoization itself isn't free.

React has to maintain:

- memoized value
- dependencies
- comparison logic
- bookkeeping

If the component is extremely cheap:

```jsx
function Child() {
  return <h1>Hello</h1>;
}
```

using three optimization mechanisms may provide no meaningful benefit.

Sometimes:

```text
Optimization overhead
       >
Actual work being optimized
```

That's worse.

---

# 21. Another common mistake

People often say:

> "`useMemo` prevents re-render."

❌ Not exactly.

`useMemo` does **not** prevent the component itself from rendering.

It memoizes a value.

Example:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const expensiveValue = useMemo(() => {
    return expensiveCalculation();
  }, []);

  console.log("Parent rendered");

  return <div>{count}</div>;
}
```

When `count` changes:

```text
Parent still renders
      ↓
useMemo returns cached value
      ↓
expensiveCalculation doesn't run again
```

So:

> **`useMemo` can prevent recalculation, not the component render itself.**

Very important.

---

# 22. Similarly, `useCallback` doesn't prevent rendering

This:

```jsx
const handleClick = useCallback(() => {}, []);
```

doesn't mean:

```text
Parent won't render
```

Parent can still render.

It means:

```text
The function reference can remain stable.
```

That becomes useful when the function is passed to a memoized child or used in dependency-sensitive logic.

---

# 23. And `React.memo` doesn't stop all renders

A memoized component can still render because of:

### Its own state

```jsx
setState()
```

### Context it consumes

```jsx
useContext()
```

### External store subscription

```text
store update
```

So:

```text
React.memo
```

is **not a magical "never render again" button**.

It primarily helps React skip renders caused by unchanged props/parent-driven updates.

---

# 24. The optimization hierarchy

Think like an experienced React developer:

```text
First:
Can I reduce the amount of state that changes?

        ↓

Can I colocate the state?

        ↓

Can I avoid unnecessary parent updates?

        ↓

Can I reduce expensive calculations?

        ↓

Can I stabilize references where useful?

        ↓

Can React.memo provide a useful bailout?

        ↓

If still slow:
Profile and find the actual bottleneck.
```

Don't jump directly to `useMemo`.

---

# 🧠 Final mental model

You should now see this:

```text
                 Parent renders
                       ↓
                New props created
                       ↓
              ┌────────┴────────┐
              ↓                 ↓
         Primitive           Object/function
              ↓                 ↓
       value comparison    reference comparison
              │                 │
              └────────┬────────┘
                       ↓
                 React.memo
                       ↓
                Shallow compare
                       ↓
             ┌─────────┴─────────┐
             ↓                   ↓
           Same              Different
             ↓                   ↓
          Bailout              Render
```

And:

```text
useMemo
   ↓
stable value/object reference

useCallback
   ↓
stable function reference

React.memo
   ↓
component-level bailout
```

---

# 🎯 Interview-level answer

If an interviewer asks:

### "Why do we use `useCallback` with `React.memo`?"

You can say:

> **React.memo performs a shallow comparison of a component's props. If I create a function inside the parent component, a new function reference is created on every parent render. Even if the function's implementation is identical, the references are different, so the memoized child can consider the prop changed and render again. `useCallback` can preserve the function reference between renders until its dependencies change, allowing `React.memo` to bail out when the other props are also unchanged.**

That's a proper understanding rather than a memorized definition.

---

## Next → Part 5

Now we should go into **`React.memo` itself at a deeper level**:

```text
React.memo
   ↓
What exactly gets memoized?
   ↓
Default comparison
   ↓
Object.is
   ↓
Shallow comparison
   ↓
Custom comparison function
   ↓
When React.memo helps
   ↓
When React.memo does NOT help
   ↓
When React.memo can actually hurt performance
   ↓
Real-world ProductList example
   ↓
React DevTools Profiler
```

That will complete the **first major optimization technique** properly.

# Part 5 — `React.memo()` Deep Dive 🔥

Now we go one level deeper. I don't want you to memorize **“React.memo prevents re-render.”** That statement is incomplete.

You should understand **exactly when React.memo allows React to skip work and when it cannot.**

---

## 1. First: What is `React.memo()` actually doing?

Suppose we have:

```jsx
const Child = React.memo(function Child({ name }) {
  console.log("Child rendered");

  return <h2>Hello {name}</h2>;
});
```

And:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child name="Nikitha" />
    </>
  );
}
```

When `count` changes:

```text
setCount()
   ↓
Parent updates
   ↓
Parent renders again
   ↓
React reaches <Child name="Nikitha" />
   ↓
React.memo checks Child's previous props vs new props
   ↓
name === name
   ↓
Props are equal
   ↓
BAILOUT
   ↓
Child function does not need to execute again
```

That's the important optimization.

### Without `React.memo`

```text
Parent update
     ↓
Parent renders
     ↓
Child renders
     ↓
React compares output
     ↓
DOM may or may not change
```

### With `React.memo`

```text
Parent update
     ↓
Parent renders
     ↓
React checks Child props
     ↓
Props equal?
     ↓
YES
     ↓
Skip Child rendering work
```

So think of `React.memo` as a **bailout mechanism**.

---

# 2. What exactly is being memoized?

This is a very important interview question.

People often say:

> "`React.memo` memoizes the component."

That's not precise enough.

Conceptually:

```jsx
React.memo(Component)
```

creates a memoized component whose rendering work can be **skipped when its props haven't changed**.

It does **not** mean:

- DOM is permanently stored
- component never renders again
- component's state is frozen
- component's context is frozen

Think:

> **React.memo gives React permission to reuse the previous rendered result when the relevant props are equal.**

---

# 3. How does React know whether props changed?

This is where **shallow comparison** comes in.

Suppose:

```jsx
<Child
  name="Nikitha"
  age={21}
/>
```

Previous:

```js
{
  name: "Nikitha",
  age: 21
}
```

Next:

```js
{
  name: "Nikitha",
  age: 21
}
```

React compares the individual props.

Conceptually:

```text
previous props       new props

name ── "Nikitha"    name ── "Nikitha"
          ↓                    ↓
       equal ✓

age ── 21            age ── 21
        ↓                    ↓
      equal ✓
```

Therefore:

```text
Props equal
    ↓
React.memo bailout
```

---

# 4. React uses `Object.is`-style comparison

This is a good interview-level detail.

For individual props, React's memo comparison uses `Object.is` semantics.

For normal values:

```js
Object.is(10, 10)
// true

Object.is("hello", "hello")
// true

Object.is(true, true)
// true
```

But objects are references:

```js
Object.is({}, {})
// false
```

Because these are two different objects.

Same with arrays:

```js
Object.is([], [])
// false
```

And functions:

```js
Object.is(
  () => {},
  () => {}
)
// false
```

---

# 5. This is why objects cause problems

Look at this:

```jsx
const Child = React.memo(function Child({ user }) {
  console.log("Child rendered");

  return <h2>{user.name}</h2>;
});
```

Parent:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const user = {
    name: "Nikitha"
  };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child user={user} />
    </>
  );
}
```

You might think:

> "`user.name` didn't change, so Child shouldn't render."

But look carefully.

Every time `Parent()` executes:

```js
const user = {
  name: "Nikitha"
};
```

creates a **new object**.

So:

### First render

```text
user ───────► Object A
               {name: "Nikitha"}
```

### Second render

```text
user ───────► Object B
               {name: "Nikitha"}
```

Even though:

```js
ObjectA.name === ObjectB.name
```

the objects themselves are different:

```js
ObjectA !== ObjectB
```

Therefore:

```text
Previous user reference ≠ New user reference
                     ↓
React.memo says:
"Prop changed"
                     ↓
Child renders
```

---

# 6. `useMemo` can solve this particular problem

```jsx
const user = useMemo(() => {
  return {
    name: "Nikitha"
  };
}, []);
```

Now:

```text
First render:

user ─────► Object A


Second render:

user ─────► Object A
```

Same reference.

Therefore:

```text
React.memo
    ↓
compare user
    ↓
same reference
    ↓
BAILOUT
```

This is the relationship you should understand:

```text
React.memo
     │
     │ needs stable props
     ↓
Object/function props
     │
     ├── useMemo → stable object/array
     │
     └── useCallback → stable function
```

---

# 7. What about functions?

Same problem.

```jsx
const Child = React.memo(function Child({ onClick }) {
  console.log("Child rendered");

  return <button onClick={onClick}>Click</button>;
});
```

Parent:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    console.log("clicked");
  };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child onClick={handleClick} />
    </>
  );
}
```

Every Parent render creates:

```js
new function
```

So:

```text
Render 1:
handleClick ──► Function A

Render 2:
handleClick ──► Function B
```

Therefore:

```js
FunctionA !== FunctionB
```

React.memo sees:

```text
onClick changed
```

and Child renders.

---

# 8. `useCallback` stabilizes the function reference

```jsx
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

Now:

```text
Render 1:
handleClick ──► Function A

Render 2:
handleClick ──► Function A

Render 3:
handleClick ──► Function A
```

So:

```text
React.memo
    ↓
onClick reference unchanged
    ↓
BAILOUT
```

---

# 9. Very important: `React.memo` does NOT stop every re-render

This is where many candidates make mistakes.

Consider:

```jsx
const Child = React.memo(function Child() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
});
```

Even though:

```jsx
React.memo(Child)
```

exists...

When Child's own state changes:

```text
Child state changes
      ↓
Child schedules update
      ↓
Child renders
```

`React.memo` does not prevent that.

---

# 10. Context also bypasses the idea of prop equality

Example:

```jsx
const ThemeContext = createContext();

const Child = React.memo(function Child() {
  const theme = useContext(ThemeContext);

  return <div>{theme}</div>;
});
```

If context changes:

```text
Context changes
      ↓
Child consumes that context
      ↓
Child updates
```

Even if:

```text
props didn't change
```

the component can still render.

So:

> `React.memo` primarily protects against unnecessary parent-driven rendering when props are equal. It does not make the component immune to its own state or consumed context updates.

---

# 11. What about a custom comparison?

You can do:

```jsx
const Child = React.memo(
  ChildComponent,
  arePropsEqual
);
```

Example:

```jsx
const Child = React.memo(
  function Child({ user }) {
    return <h2>{user.name}</h2>;
  },

  function arePropsEqual(oldProps, newProps) {
    return oldProps.user.name === newProps.user.name;
  }
);
```

Now you are telling React:

> "Use my logic to decide whether these props should be considered equal."

If:

```js
oldProps.user.name === newProps.user.name
```

then React can bail out.

---

# 12. But custom comparison is dangerous

Suppose:

```jsx
function Child({ user, onClick }) {
  // ...
}
```

And your comparison only checks:

```js
oldProps.user === newProps.user
```

but completely ignores:

```js
onClick
```

That can create bugs.

Why?

Because the new function might contain new state/closure values.

For example:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    console.log(count);
  };

  return (
    <Child
      value={count}
      onClick={handleClick}
    />
  );
}
```

If your custom comparison incorrectly says props are equal while `onClick` actually changed, Child could keep using an old function.

This is why custom comparison needs to be handled carefully.

---

# 13. Why not use `React.memo` everywhere?

Excellent question.

Because optimization itself has a cost.

Imagine:

```jsx
const TinyText = React.memo(({ name }) => {
  return <span>{name}</span>;
});
```

This component is extremely cheap.

React now has to perform:

```text
Parent renders
      ↓
Compare props
      ↓
Decide whether to render
```

If rendering the component is cheaper than performing the comparison, memoization may not provide meaningful benefit.

So the correct philosophy is:

> **Don't optimize because you can. Optimize because there is work worth avoiding.**

---

# 14. Real-world example: Product List

Imagine:

```jsx
function ProductList({ products, onSelect }) {
  return (
    <div>
      {products.map(product => (
        <Product
          key={product.id}
          product={product}
          onSelect={onSelect}
        />
      ))}
    </div>
  );
}
```

Suppose there are:

```text
1,000 products
```

Parent state changes for something unrelated:

```text
Cart count changes
      ↓
Parent renders
      ↓
ProductList renders
      ↓
1,000 Product components may be processed
```

If `Product` is:

```jsx
const Product = React.memo(function Product({
  product,
  onSelect
}) {
  return (
    <div>
      {product.name}
    </div>
  );
});
```

and its props remain stable:

```text
Parent update
     ↓
ProductList
     ↓
Product 1 → props same → bailout
Product 2 → props same → bailout
Product 3 → props same → bailout
...
Product 1000 → props same → bailout
```

That can be a significant optimization.

But if you do:

```jsx
<Product
  product={{ ...product }}
  onSelect={() => onSelect(product.id)}
/>
```

you just created:

```text
new object
new function
```

for every Product.

Now `React.memo` loses much of its effectiveness.

---

# 15. The most important mental model

Don't think:

> `React.memo = don't render this component.`

Think:

> **`React.memo = before doing parent-driven rendering work for this component, React can check whether the props are equivalent and potentially bail out.`**

That is a much more accurate mental model.

---

# 16. Complete flow

### Without memo

```text
Parent state changes
        ↓
Parent renders
        ↓
Child reached
        ↓
Child renders
        ↓
React creates new element output
        ↓
Reconciliation
        ↓
Commit only necessary DOM changes
```

### With memo + stable props

```text
Parent state changes
        ↓
Parent renders
        ↓
Child reached
        ↓
React.memo compares props
        ↓
Props equal
        ↓
BAILOUT
        ↓
Skip unnecessary Child rendering work
```

### With memo + unstable object

```text
Parent state changes
        ↓
Parent renders
        ↓
new object created
        ↓
React.memo compares references
        ↓
Different reference
        ↓
Child renders
```

---

# 17. Interview answer 🎯

If interviewer asks:

### "What is React.memo and how does it work?"

You can answer:

> "`React.memo` is a performance optimization that allows React to skip unnecessary rendering work for a component when its props have not changed. React compares the previous and next props, using shallow comparison with `Object.is` semantics for individual prop values. If the props are equivalent, React can bail out instead of rendering that component again. However, `React.memo` doesn't prevent updates caused by the component's own state, consumed context, or other subscriptions. It is most useful when a component renders frequently, its rendering is relatively expensive, and its props are stable."

That's an **interview-level answer**.

---

## One final distinction you must never forget

```text
React.memo
    ↓
"Should this component's rendering work be skipped?"

useMemo
    ↓
"Should I reuse this calculated value/reference?"

useCallback
    ↓
"Should I reuse this function reference?"
```

And these three work together because of **referential equality**.

---

### Next: Part 6 — `useMemo()` Deep Dive

We'll go inside:

**what `useMemo` actually caches → dependency array → when recalculation happens → primitive vs object → expensive calculations → `useMemo` vs `React.memo` → why `useMemo` can actually make performance worse → stale values → advanced patterns.**

# Part 6 — `useMemo()` Deep Dive 🔥

Now let's understand `useMemo` from the **inside**, not just:

> "`useMemo` is used for optimization."

That definition is too shallow.

---

# 1. First understand the problem `useMemo` solves

Imagine:

```jsx
function ProductList({ products }) {
  const expensiveResult = calculateSomething(products);

  return <div>{expensiveResult}</div>;
}
```

Every time `ProductList` renders:

```text
ProductList renders
      ↓
calculateSomething()
      ↓
calculation runs AGAIN
```

Even if `products` didn't change.

For an expensive calculation, this can become a problem.

Example:

```jsx
const filteredProducts = products
  .filter(...)
  .sort(...)
  .map(...);
```

If `products` contains:

```text
10,000 products
```

and the component renders frequently, you're repeatedly doing expensive work.

---

# 2. `useMemo` changes the flow

```jsx
const filteredProducts = useMemo(() => {
  return products
    .filter(...)
    .sort(...);
}, [products]);
```

Now conceptually:

```text
Component renders
      ↓
React checks dependencies
      ↓
Did products change?
      ↓
   NO ───────────── YES
    ↓                 ↓
reuse old value    calculate again
                      ↓
                  store result
```

That's the core idea.

---

# 3. What does `useMemo` actually return?

Look at:

```jsx
const result = useMemo(() => {
  return expensiveCalculation();
}, []);
```

`result` is **the value returned by the callback**.

For example:

```jsx
const total = useMemo(() => {
  return 100 + 200;
}, []);
```

Then:

```js
total === 300
```

Not the function.

Compare:

```jsx
useMemo(() => {
  return calculate();
}, []);
```

with:

```jsx
useCallback(() => {
  return calculate();
}, []);
```

### `useMemo`

```text
callback
   ↓
execute
   ↓
cache returned VALUE
```

### `useCallback`

```text
function
   ↓
cache FUNCTION REFERENCE
```

---

# 4. The internal mental model

Suppose:

```jsx
const result = useMemo(
  () => expensiveCalculation(data),
  [data]
);
```

React conceptually keeps something associated with this hook:

```text
Hook state

┌──────────────────────────┐
│ Previous dependencies    │
│ [data reference]         │
│                          │
│ Cached value             │
│ result                   │
└──────────────────────────┘
```

On the next render:

```text
New dependencies
      ↓
Compare with previous dependencies
      ↓
Same?
 ┌────┴────┐
YES        NO
 ↓          ↓
reuse      calculate
cached     again
value        ↓
            cache new value
```

This is why the dependency array matters so much.

---

# 5. Dependency array = "when should I recalculate?"

Example:

```jsx
const result = useMemo(() => {
  return expensiveCalculation(count);
}, [count]);
```

If:

```text
count = 10
```

calculation runs.

Next render:

```text
count = 10
```

React can reuse the previous value.

Then:

```text
count = 11
```

Dependency changed.

So:

```text
calculate again
```

---

# 6. Think in terms of dependency identity

This is where our previous lesson connects.

Suppose:

```jsx
const user = {
  name: "Nikitha"
};

const result = useMemo(() => {
  return calculate(user);
}, [user]);
```

Every render:

```text
new user object
      ↓
new reference
      ↓
dependency changed
      ↓
useMemo recalculates
```

So this:

```jsx
const user = {
  name: "Nikitha"
};
```

can defeat memoization.

---

# 7. `useMemo` does NOT prevent component rendering

This is extremely important.

Suppose:

```jsx
function App() {
  const [count, setCount] = useState(0);

  const result = useMemo(() => {
    return expensiveCalculation();
  }, []);

  return <div>{count}</div>;
}
```

When `count` changes:

```text
count changes
     ↓
App renders again
     ↓
useMemo runs its dependency check
     ↓
dependencies unchanged
     ↓
reuse cached result
```

So:

> **The component still renders.**

`useMemo` only prevents the calculation from being executed again.

---

# 8. This distinction is critical

### `React.memo`

```text
Can skip component rendering work
```

### `useMemo`

```text
Can skip recalculating a value
```

Example:

```jsx
const Child = React.memo(...);

const expensiveValue = useMemo(...);
```

They solve **different problems**.

---

# 9. Example: expensive calculation

Suppose:

```jsx
function ProductList({ products, search }) {
  const filteredProducts = products
    .filter(product =>
      product.name
        .toLowerCase()
        .includes(search.toLowerCase())
    )
    .sort((a, b) => a.price - b.price);

  return (
    <div>
      {filteredProducts.map(product => (
        <Product key={product.id} product={product} />
      ))}
    </div>
  );
}
```

Every render:

```text
render
 ↓
filter 10,000 items
 ↓
sort
 ↓
map
```

Now:

```jsx
const filteredProducts = useMemo(() => {
  return products
    .filter(product =>
      product.name
        .toLowerCase()
        .includes(search.toLowerCase())
    )
    .sort((a, b) => a.price - b.price);
}, [products, search]);
```

Now:

```text
Parent renders
      ↓
products/search changed?
      ↓
NO
      ↓
reuse filteredProducts
```

That's a good use case.

---

# 10. But here's something people misunderstand

This:

```jsx
const value = useMemo(() => 10 + 20, []);
```

is usually pointless.

Why?

Because:

```js
10 + 20
```

is incredibly cheap.

You're adding:

```text
memoization overhead
+
dependency comparison
+
memory for cached value
```

to avoid:

```text
30
```

which costs almost nothing to calculate.

So:

> **Memoization itself has a cost.**

---

# 11. This is why you should NOT use `useMemo` everywhere

Bad:

```jsx
const fullName = useMemo(() => {
  return firstName + " " + lastName;
}, [firstName, lastName]);
```

Usually unnecessary.

Better:

```jsx
const fullName = firstName + " " + lastName;
```

Because the calculation is trivial.

---

# 12. Good `useMemo` candidate

Something like:

```jsx
const sortedProducts = useMemo(() => {
  return [...products].sort((a, b) => a.price - b.price);
}, [products]);
```

Potentially useful because sorting a large collection can be expensive.

Especially if:

```text
large dataset
+
frequent renders
+
same dependencies most of the time
```

That's where memoization starts making sense.

---

# 13. Another important use: stable object references

Remember our `React.memo` problem?

```jsx
const user = {
  name: "Nikitha"
};
```

You can do:

```jsx
const user = useMemo(() => ({
  name: "Nikitha"
}), []);
```

Now:

```text
Render 1
user → Object A

Render 2
user → Object A

Render 3
user → Object A
```

Therefore:

```text
same reference
```

This can help a memoized child:

```jsx
const Child = React.memo(ChildComponent);
```

But remember:

> Don't use `useMemo` merely to make every object stable. Do it when that stability actually matters.

---

# 14. `useMemo` and referential equality

This is one of the most important connections in React optimization.

Suppose:

```jsx
const data = useMemo(() => {
  return {
    name: "Nikitha"
  };
}, []);
```

Then:

```js
previousData === currentData
```

can remain:

```text
true
```

This helps with:

- `React.memo`
- dependency arrays
- memoized selectors
- optimized child components

So `useMemo` isn't only about expensive calculations.

It can also be about **stable identity**.

---

# 15. Dependency arrays are not "run this every X time"

This is a common beginner misunderstanding.

This:

```jsx
useMemo(() => {
  calculate();
}, [count]);
```

does NOT mean:

> "Run whenever React feels like it."

It means conceptually:

```text
Compare count from previous render
          ↓
Has dependency changed?
          ↓
YES → recompute
NO  → reuse previous value
```

---

# 16. What does React compare?

For dependencies, React compares values using `Object.is` semantics.

Example:

```js
Object.is(10, 10)
// true
```

So no recalculation.

But:

```js
Object.is({}, {})
// false
```

So React considers them different.

This is why:

```jsx
const options = {
  sort: "price"
};

useMemo(() => {
  calculate(options);
}, [options]);
```

can recalculate every render if `options` is recreated every render.

---

# 17. A subtle problem: wrong dependencies

Suppose:

```jsx
function Component({ price, quantity }) {
  const total = useMemo(() => {
    return price * quantity;
  }, [price]);
}
```

This is wrong.

Because the calculation uses:

```text
price
quantity
```

but the dependency list only contains:

```text
price
```

If quantity changes:

```text
quantity changes
     ↓
useMemo doesn't know
     ↓
old cached total may be reused
```

You could get stale data.

Correct:

```jsx
const total = useMemo(() => {
  return price * quantity;
}, [price, quantity]);
```

---

# 18. Think of dependencies as a contract

When you write:

```jsx
useMemo(() => {
  return expensiveCalculation(a, b, c);
}, [a, b, c]);
```

you're effectively telling React:

> "The result depends on `a`, `b`, and `c`. If none of these changed, the previous result is valid."

That's the mental model I want you to remember.

---

# 19. `useMemo` vs `useCallback`

This gets asked constantly.

### `useMemo`

```jsx
const value = useMemo(() => {
  return calculate();
}, [dependencies]);
```

Caches:

```text
VALUE
```

### `useCallback`

```jsx
const fn = useCallback(() => {
  doSomething();
}, [dependencies]);
```

Caches:

```text
FUNCTION REFERENCE
```

And conceptually:

```jsx
useCallback(fn, deps)
```

is similar to:

```jsx
useMemo(() => fn, deps)
```

The purpose is clearer with `useCallback`, though.

---

# 20. `useMemo` + `React.memo`

Now let's combine everything.

```jsx
const Child = React.memo(function Child({ data }) {
  console.log("Child render");

  return <div>{data.name}</div>;
});
```

Parent:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const data = useMemo(() => ({
    name: "Nikitha"
  }), []);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child data={data} />
    </>
  );
}
```

Flow:

```text
count changes
      ↓
Parent renders
      ↓
useMemo checks dependencies
      ↓
dependencies unchanged
      ↓
same data reference
      ↓
React.memo checks Child props
      ↓
data reference same
      ↓
BAILOUT
      ↓
Child doesn't need to render
```

This is the real optimization chain.

---

# 21. But don't fall into this trap

Some developers write:

```jsx
const data = useMemo(() => ({
  name: "Nikitha"
}), []);

const value = useMemo(() => {
  return calculate();
}, []);

const fn = useCallback(() => {}, []);
```

everywhere.

That doesn't automatically make the application faster.

You should first ask:

```text
1. Is this component rendering unnecessarily?
2. Is this calculation expensive?
3. Is this value causing another component to rerender?
4. Are dependencies stable?
5. Did profiling show this as a bottleneck?
```

Then optimize.

---

# 22. One powerful mental model

Think of `useMemo` as a small cache:

```text
                 ┌────────────────────┐
                 │      useMemo       │
                 │                    │
dependencies ──► │ Should I reuse it? │
                 │                    │
                 │ cached value       │
                 └─────────┬──────────┘
                           │
                  ┌────────┴────────┐
                  │                 │
               unchanged          changed
                  │                 │
                  ↓                 ↓
              reuse value       calculate
```

That's essentially what you're learning.

---

# 23. Interview answer 🎯

If they ask:

### "What is useMemo?"

Say:

> "`useMemo` is a React Hook used to memoize the result of a calculation. React stores the previously calculated value and compares the dependency array on subsequent renders. If the dependencies haven't changed, React can reuse the previous value instead of recalculating it. It doesn't prevent the component itself from rendering; it only avoids repeating the memoized calculation. It's useful for expensive computations or when a stable object or array reference is important for preventing unnecessary downstream renders."

That's a strong answer.

---

# 24. The optimization chain so far

You now have these three concepts connected:

```text
                 React Optimization
                        │
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
     React.memo      useMemo      useCallback
          │             │             │
          ↓             ↓             ↓
   skip component   cache value    cache function
      work          / reference      reference
          │             │             │
          └─────────────┼─────────────┘
                        ↓
                Referential Equality
                        ↓
                 Stable References
                        ↓
                     Bailouts
```

This is the foundation of React performance optimization.

---

## Next — Part 7: `useCallback()` Deep Dive

We'll go deeper into **why functions are recreated, closures, dependency arrays, stale closures, `useCallback` + `React.memo`, when `useCallback` actually helps, and when it is completely useless**.



# Part 7 — `useCallback()` Deep Dive

The biggest mistake people make with `useCallback` is memorizing:

> "`useCallback` memoizes a function."

That is technically true, but it doesn't explain **why we need it**.

Let's build the concept from JavaScript first.

---

# 1. First understand the real problem: functions are objects

Consider:

```jsx
function Parent() {
  const handleClick = () => {
    console.log("Clicked");
  };

  return <Child onClick={handleClick} />;
}
```

You might look at this and think:

> "`handleClick` is the same function every time."

It isn't.

Every time `Parent()` executes, JavaScript creates a **new function object**.

Think about it like this.

### First render

```text
Parent()
   ↓
creates Function A

handleClick ───────► Function A
```

### Second render

```text
Parent()
   ↓
creates Function B

handleClick ───────► Function B
```

So:

```js
FunctionA === FunctionB
```

is:

```js
false
```

Even though the code looks identical.

---

# 2. Prove it with JavaScript

```js
const fn1 = () => {
  console.log("hello");
};

const fn2 = () => {
  console.log("hello");
};

console.log(fn1 === fn2);
```

Result:

```text
false
```

Why?

Because these are two separate function objects.

```text
fn1 ─────► Function Object A

fn2 ─────► Function Object B
```

The function **code looks the same**, but their **references are different**.

---

# 3. Why does this matter in React?

Now combine this with what we learned about `React.memo`.

Suppose:

```jsx
const Child = React.memo(function Child({ onClick }) {
  console.log("Child rendered");

  return <button onClick={onClick}>Click</button>;
});
```

Parent:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    console.log("Clicked");
  };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Child onClick={handleClick} />
    </>
  );
}
```

Now click the counter.

---

# 4. Follow the complete internal flow

Initially:

```text
Parent render
      ↓
handleClick = Function A
      ↓
Child receives Function A
```

Then:

```text
setCount()
      ↓
Parent state changes
      ↓
Parent renders again
      ↓
handleClick = Function B
      ↓
Child receives Function B
```

React.memo asks:

```text
Previous onClick === New onClick?
```

Result:

```js
FunctionA === FunctionB
// false
```

Therefore:

```text
Props changed
     ↓
React.memo cannot bail out
     ↓
Child renders
```

This happens even though the function's implementation didn't change.

---

# 5. Now `useCallback` enters

We can write:

```jsx
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

Now React can preserve the function reference between renders.

Conceptually:

### First render

```text
useCallback
    ↓
creates Function A
    ↓
stores Function A
    ↓
returns Function A
```

### Second render

```text
useCallback
    ↓
checks dependencies
    ↓
dependencies unchanged
    ↓
returns previously stored Function A
```

So:

```text
Render 1:

handleClick ───► Function A


Render 2:

handleClick ───► Function A
```

Now:

```js
previousOnClick === newOnClick
```

is:

```text
true
```

Therefore:

```text
React.memo
    ↓
props equal
    ↓
BAILOUT
```

---

# 6. The actual mental model of `useCallback`

Think of `useCallback` as:

> **"React, give me the same function reference until one of these dependencies changes."**

Example:

```jsx
const handleClick = useCallback(() => {
  console.log(count);
}, [count]);
```

You're essentially saying:

```text
If count is the same
    ↓
reuse the same function

If count changes
    ↓
create a new function
```

---

# 7. Why does the dependency array matter?

This is where things become more interesting.

Consider:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log(count);
  }, [count]);

  return (
    <button onClick={handleClick}>
      Click
    </button>
  );
}
```

The callback uses:

```text
count
```

Therefore `count` is a dependency.

---

# 8. Follow the renders

### Initial render

```text
count = 0

useCallback
    ↓
creates Function A
    ↓
Function A remembers count = 0
```

Now:

```text
handleClick → Function A
```

Click/update count:

```text
count = 1
```

React renders again.

Dependency changed:

```text
previous count = 0
new count = 1
```

Therefore:

```text
useCallback
      ↓
dependency changed
      ↓
create Function B
      ↓
Function B remembers count = 1
```

Now:

```text
handleClick → Function B
```

This is correct.

---

# 9. Why can't React keep the same function forever?

Because the function may depend on changing values.

Example:

```jsx
const handleClick = useCallback(() => {
  console.log(count);
}, [count]);
```

Suppose React kept Function A forever:

```text
Function A
   ↓
remembers count = 0
```

Even after:

```text
count = 5
```

the function could still use the old value.

That leads us to one of the most important React concepts:

# Closures

---

# 10. Understanding closures is essential for `useCallback`

Consider normal JavaScript:

```js
function createFunction() {
  let count = 10;

  return function () {
    console.log(count);
  };
}

const fn = createFunction();

fn();
```

The returned function remembers the environment in which it was created.

That's a closure.

In React:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    console.log(count);
  };
}
```

The function created during a particular render closes over the values from that render.

---

# 11. Think of every render as a snapshot

This is a very powerful React mental model.

Suppose:

```text
Render #1
count = 0
```

The function created there sees:

```text
count = 0
```

Then:

```text
Render #2
count = 1
```

The function created there sees:

```text
count = 1
```

Then:

```text
Render #3
count = 2
```

The function created there sees:

```text
count = 2
```

So:

```text
Render #1
 └── Function A → count = 0

Render #2
 └── Function B → count = 1

Render #3
 └── Function C → count = 2
```

This is why dependency management matters.

---

# 12. What happens if you give the wrong dependencies?

Consider:

```jsx
const handleClick = useCallback(() => {
  console.log(count);
}, []);
```

Notice:

```text
count is used
```

but:

```text
count is NOT in dependencies
```

Now imagine:

### Render 1

```text
count = 0

Function A
   ↓
remembers count = 0
```

Because dependency array is:

```js
[]
```

React keeps Function A.

Then:

```text
count = 1
```

Component renders again.

But:

```text
dependencies []
```

haven't changed.

So React reuses Function A.

Therefore:

```text
handleClick
    ↓
Function A
    ↓
count = 0
```

You expected:

```text
count = 1
```

This is a **stale closure**.

---

# 13. Stale closure

A stale closure means:

> A function is using an older value from a previous render because it retained a closure over that old value.

Example:

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log(count);
  }, []);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>

      <button onClick={handleClick}>
        Show count
      </button>
    </>
  );
}
```

After clicking increment five times, you might expect:

```text
5
```

But `handleClick` can still see:

```text
0
```

because the callback was created during the initial render and its dependency list tells React it never needs to be replaced.

---

# 14. Correct version

```jsx
const handleClick = useCallback(() => {
  console.log(count);
}, [count]);
```

Now:

```text
count changes
      ↓
dependency changes
      ↓
new callback
      ↓
new closure
      ↓
callback sees latest count
```

---

# 15. This gives us an important rule

> **Every reactive value used inside a callback generally needs to be represented in the callback's dependency relationship.**

For example:

```jsx
const handleClick = useCallback(() => {
  console.log(user.name);
  console.log(count);
  submit(data);
}, [user, count, data]);
```

Because the callback depends on:

```text
user
count
data
```

The dependency array describes when the callback needs to be recreated to capture updated values.

---

# 16. `useCallback` does NOT make a function execute faster

Very important.

This:

```jsx
const handleClick = useCallback(() => {
  expensiveCalculation();
}, []);
```

does **not** make:

```js
expensiveCalculation()
```

faster.

`useCallback` is primarily about **function identity/reference stability**.

It answers:

```text
"Is this the same function reference?"
```

not:

```text
"Does this function execute faster?"
```

---

# 17. `useCallback` becomes powerful with `React.memo`

This is the most common practical combination.

```jsx
const Button = React.memo(function Button({ onClick }) {
  console.log("Button rendered");

  return <button onClick={onClick}>Click</button>;
});
```

Parent:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>

      <Button onClick={handleClick} />
    </>
  );
}
```

Flow:

```text
count changes
     ↓
Parent renders
     ↓
useCallback checks []
     ↓
same dependency
     ↓
same function reference
     ↓
Button receives same onClick
     ↓
React.memo comparison
     ↓
props equal
     ↓
Button bails out
```

This is a meaningful optimization.

---

# 18. Without `React.memo`, `useCallback` may accomplish nothing

Suppose:

```jsx
function Button({ onClick }) {
  console.log("Button rendered");

  return <button onClick={onClick}>Click</button>;
}
```

Parent:

```jsx
const handleClick = useCallback(() => {
  console.log("clicked");
}, []);
```

The function reference is stable.

But if the parent renders:

```text
Parent renders
    ↓
Button renders
```

The child isn't memoized.

So there may be no bailout.

This is a key point:

```text
useCallback
    ↓
stable function reference
    ↓
useful when something cares about reference equality
```

For example:

- `React.memo`
- dependency arrays
- certain custom hooks
- subscription APIs

---

# 19. Another important use: dependency arrays

Consider:

```jsx
useEffect(() => {
  subscribe(handleClick);

  return () => {
    unsubscribe(handleClick);
  };
}, [handleClick]);
```

If `handleClick` is recreated every render:

```text
Render
 ↓
new handleClick
 ↓
useEffect dependency changed
 ↓
cleanup
 ↓
subscribe again
```

This can happen repeatedly.

Using:

```jsx
const handleClick = useCallback(() => {
  // ...
}, []);
```

can stabilize the dependency when appropriate.

But don't automatically add `useCallback`; first understand why the effect depends on that function.

---

# 20. `useCallback` and child components

Imagine:

```jsx
function Parent() {
  const [theme, setTheme] = useState("dark");

  const handleSubmit = () => {
    console.log("submit");
  };

  return (
    <Form onSubmit={handleSubmit} />
  );
}
```

If `Form` is:

```jsx
const Form = React.memo(...);
```

then changing theme can cause:

```text
theme changes
   ↓
Parent renders
   ↓
new handleSubmit function
   ↓
Form gets new prop
   ↓
Form renders
```

Even though Form doesn't care about theme.

With:

```jsx
const handleSubmit = useCallback(() => {
  console.log("submit");
}, []);
```

you get:

```text
theme changes
   ↓
Parent renders
   ↓
same handleSubmit reference
   ↓
Form props unchanged
   ↓
Form can bail out
```

---

# 21. But there is a catch

Suppose:

```jsx
const handleSubmit = useCallback(() => {
  submit(user);
}, [user]);
```

Now if `user` changes:

```text
user changes
   ↓
dependency changes
   ↓
new function
```

That's completely correct.

Don't try to force the function to remain stable if it genuinely depends on changing data.

The goal isn't:

> "Make function reference never change."

The goal is:

> **"Make the function reference change only when its behavior needs to change."**

That's a much better mental model.

---

# 22. A common mistake

People sometimes do:

```jsx
const handleClick = useCallback(() => {
  setCount(count + 1);
}, [count]);
```

This is valid.

But there's another approach:

```jsx
const handleClick = useCallback(() => {
  setCount(prev => prev + 1);
}, []);
```

Why can this work?

Because the updater function:

```js
prev => prev + 1
```

gets the latest state from React.

So the callback doesn't need to close over `count`.

Therefore:

```text
count dependency
     ↓
not needed
```

and the function can remain stable.

---

# 23. This is a powerful optimization pattern

Compare:

### Version 1

```jsx
const handleIncrement = useCallback(() => {
  setCount(count + 1);
}, [count]);
```

Every time count changes:

```text
new callback
```

### Version 2

```jsx
const handleIncrement = useCallback(() => {
  setCount(prev => prev + 1);
}, []);
```

The callback can remain stable.

Because:

```text
React gives updater latest state
```

This is one of the more advanced patterns you should understand.

---

# 24. `useCallback` itself has overhead

Just like `useMemo`, `useCallback` isn't free.

React has to:

```text
store callback
+
store dependencies
+
compare dependencies
+
maintain hook state
```

Therefore this:

```jsx
const hello = useCallback(() => {
  console.log("hello");
}, []);
```

doesn't automatically improve performance.

If the function isn't being passed to a memoized child or used in a situation where reference stability matters, you may be adding complexity without meaningful benefit.

---

# 25. The three-way relationship

Now put everything together.

### Problem

```text
Parent rerenders
      ↓
new function created
      ↓
memoized Child sees changed prop
      ↓
Child rerenders
```

### Solution

```text
useCallback
      ↓
stable function reference
      ↓
React.memo
      ↓
props remain equal
      ↓
Child bailout
```

So:

```text
useCallback
    +
React.memo
    +
stable dependencies
    =
potentially useful optimization
```

---

# 26. `useMemo` vs `useCallback` — now at a deeper level

| Hook | What it preserves | Main purpose |
|---|---|---|
| `useMemo` | Calculated value | Avoid expensive recalculation / stabilize value |
| `useCallback` | Function reference | Stabilize function identity |
| `React.memo` | Component rendering work | Bail out when props are equivalent |

Think:

```text
useMemo
   ↓
"Remember this VALUE."

useCallback
   ↓
"Remember this FUNCTION."

React.memo
   ↓
"Can I skip this COMPONENT'S rendering work?"
```

---

# 27. The deeper React optimization chain

At this point you should be able to mentally trace this:

```text
State update
     ↓
Parent renders
     ↓
Inline object/function recreated
     ↓
Reference changes
     ↓
Child prop considered changed
     ↓
React.memo cannot bail out
     ↓
Child renders
```

Optimization:

```text
State update
     ↓
Parent renders
     ↓
useMemo/useCallback
     ↓
Stable reference
     ↓
Child receives equivalent props
     ↓
React.memo
     ↓
Bailout
     ↓
Less rendering work
```

That's the **real reason** these APIs are connected.

---

# 28. One thing I want you to remember permanently

Don't ask:

> "Should I use `useCallback`?"

Ask:

> **"Does the identity of this function matter?"**

If the answer is no, you probably don't need it.

If the function is:

- passed to a `React.memo` child,
- used as a dependency where stable identity matters,
- passed to a subscription system,
- otherwise compared by reference,

then `useCallback` may be useful.

---

# 29. Interview answer 🎯

If the interviewer asks:

### "Why do we use useCallback?"

A strong answer is:

> "`useCallback` memoizes a function reference between renders based on its dependencies. This is useful when function identity matters, especially when passing callbacks to memoized child components or using them as dependencies. Without `useCallback`, a function defined inside a component is recreated on every render, producing a new reference. `useCallback` allows React to reuse the previous function until its dependencies change. However, it doesn't make the function execute faster, and using it everywhere can add unnecessary complexity and overhead."

---

# 30. Your optimization foundation is now strong

You now understand:

```text
Part 1
Rendering vs DOM updates
        ↓
Part 2
Render phase / Commit phase / Fiber
        ↓
Part 3
Why components rerender
        ↓
Part 4
Referential equality
        ↓
Part 5
React.memo
        ↓
Part 6
useMemo
        ↓
Part 7
useCallback
```

And these aren't separate topics anymore.

They're one system:

```text
                 REACT PERFORMANCE
                        │
                        ↓
                 Avoid unnecessary work
                        │
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
      React.memo     useMemo      useCallback
          │             │             │
          ↓             ↓             ↓
       Bailout      Cache value    Stable function
          │             │             │
          └─────────────┼─────────────┘
                        ↓
               Referential equality
                        ↓
                 Less React work
```

### Next: Part 8 — **State Optimization & State Colocation**

This is actually **more important than `useMemo`/`useCallback`** in real applications.

We'll go deeply into:

- Why state placement affects rendering
- State update propagation
- Parent state vs child state
- State colocation
- Lifting state up vs lifting state too far
- Splitting state
- Derived state
- Why unnecessary state causes unnecessary renders
- Component boundaries as optimization boundaries
- A real large-app example showing how moving **one `useState`** can dramatically reduce rendering work.