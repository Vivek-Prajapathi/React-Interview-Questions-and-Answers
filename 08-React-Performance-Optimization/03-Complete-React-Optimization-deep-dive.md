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