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

Absolutely. From now on, whenever a new concept appears, I’ll first give you a **clear definition**, then explain the **why → how → internal flow → example → optimization impact → interview angle**. I’ll also go deeper than the previous parts.

# Part 8 — State Optimization & State Colocation

This is one of the **most important optimization topics in React**.

And I want you to understand something before we start:

> **A lot of React performance problems can be solved by putting state in the right place, without using `useMemo`, `useCallback`, or `React.memo` at all.**

---

# 1. First: What is state?

### Definition

**State is data owned by a component that can change over time and whose change can cause React to render that component again.**

Example:

```jsx
const [count, setCount] = useState(0);
```

Here:

```text
count
  ↓
state value

setCount
  ↓
state update mechanism
```

When:

```jsx
setCount(1);
```

happens:

```text
state changes
    ↓
React schedules an update
    ↓
component rendering work
```

So state isn't just "a variable."

It's data that participates in React's rendering system.

---

# 2. The first optimization rule

Suppose your application looks like this:

```text
App
├── Header
├── Sidebar
├── ProductList
├── SearchBox
└── Footer
```

Now imagine you put search state in `App`:

```jsx
function App() {
  const [search, setSearch] = useState("");

  return (
    <>
      <Header />
      <Sidebar />
      <SearchBox search={search} setSearch={setSearch} />
      <ProductList search={search} />
      <Footer />
    </>
  );
}
```

Every time the user types:

```text
search changes
    ↓
App renders
    ↓
Header is processed
Sidebar is processed
SearchBox is processed
ProductList is processed
Footer is processed
```

But ask yourself:

> Does `Header` actually care about search?

No.

> Does `Footer` care?

No.

Yet the state update originates at the top.

This is where **state placement** becomes a performance concern.

---

# 3. State colocation

### Definition

**State colocation means keeping state as close as possible to the components that actually need that state.**

Instead of unnecessarily keeping state high in the tree, place it at the lowest appropriate level.

Think:

```text
State
  ↓
should live near
  ↓
the components that use it
```

This is called **colocation**.

---

# 4. Why does state placement affect performance?

Let's build the tree.

### Bad placement

```text
App
│
├── Header
├── Sidebar
├── Search
├── ProductList
└── Footer
```

State:

```text
App
└── search state
```

Flow:

```text
User types
   ↓
search state updates
   ↓
App updates
   ↓
App renders
   ↓
work propagates through descendants
```

Even if only Search and ProductList care about that state.

---

# 5. Better placement

Suppose only the search section needs the state:

```text
App
│
├── Header
├── Sidebar
├── SearchSection
│     ├── SearchBox
│     └── ProductList
│
└── Footer
```

Now:

```jsx
function SearchSection() {
  const [search, setSearch] = useState("");

  return (
    <>
      <SearchBox
        search={search}
        setSearch={setSearch}
      />

      <ProductList search={search} />
    </>
  );
}
```

Now:

```text
search changes
     ↓
SearchSection updates
     ↓
SearchBox / ProductList work
```

The unrelated parts:

```text
Header
Sidebar
Footer
```

are outside the state-owning subtree.

This is a much better rendering boundary.

---

# 6. This is the deeper idea

State placement determines the **scope of an update**.

Think of:

```text
State location
     ↓
Update originates here
     ↓
Potential rendering work starts here
     ↓
Descendant subtree can be affected
```

Therefore:

> **The higher you place frequently changing state, the larger the subtree that can potentially be affected by that state update.**

This is why state colocation is an optimization technique.

---

# 7. Example: typing into a search box

Imagine a large dashboard:

```text
App
│
├── Navbar
├── Sidebar
├── AnalyticsDashboard
│   ├── Chart1
│   ├── Chart2
│   └── Chart3
├── SearchBox
└── Footer
```

Suppose:

```jsx
function App() {
  const [search, setSearch] = useState("");

  return (
    <>
      <Navbar />
      <Sidebar />
      <AnalyticsDashboard />
      <SearchBox
        value={search}
        onChange={setSearch}
      />
      <Footer />
    </>
  );
}
```

Every keystroke:

```text
"a"
 ↓
App update

"ab"
 ↓
App update

"abc"
 ↓
App update
```

Potentially:

```text
App
 ↓
Navbar
Sidebar
AnalyticsDashboard
SearchBox
Footer
```

That's a lot of rendering work for a tiny interaction.

---

# 8. Move the state down

Create:

```jsx
function SearchSection() {
  const [search, setSearch] = useState("");

  return (
    <>
      <SearchBox
        value={search}
        onChange={setSearch}
      />

      <SearchResults search={search} />
    </>
  );
}
```

Then:

```jsx
function App() {
  return (
    <>
      <Navbar />
      <Sidebar />
      <AnalyticsDashboard />

      <SearchSection />

      <Footer />
    </>
  );
}
```

Now:

```text
Typing
  ↓
SearchSection state changes
  ↓
SearchSection renders
  ↓
SearchBox
SearchResults
```

The update doesn't originate from `App`.

That's **state colocation**.

---

# 9. Important: "Lowest possible component" doesn't mean literally lowest

Don't interpret colocation as:

> "Always put state inside the smallest component."

That's wrong.

Suppose two components need the same state:

```text
SearchBox
ProductList
```

Then putting state inside only `SearchBox` won't work cleanly.

You need a common owner:

```text
SearchSection
├── SearchBox
└── ProductList
```

So:

```text
State should live at the lowest common ancestor
of the components that need it.
```

This is the practical rule.

---

# 10. Lowest Common Ancestor

### Definition

The **lowest common ancestor** is the nearest component in the component tree that contains all components that need the same state.

Example:

```text
App
│
├── Header
│
└── ProductPage
    │
    ├── SearchBox
    └── ProductList
```

If both:

```text
SearchBox
ProductList
```

need `search`, then:

```text
ProductPage
```

is the appropriate state owner.

Not necessarily:

```text
App
```

---

# 11. Lifting state up

Now we reach another important concept.

### Definition

**Lifting state up means moving state from a child component to a common parent so multiple components can share and coordinate that state.**

Example:

Initially:

```text
SearchBox
└── search state
```

Then ProductList also needs it.

You move it upward:

```text
ProductPage
└── search state
    ├── SearchBox
    └── ProductList
```

This is correct.

But here's where developers often make a mistake.

---

# 12. Lifting state too far

Suppose:

```text
App
│
├── Header
├── Sidebar
├── ProductPage
│   ├── SearchBox
│   └── ProductList
└── Footer
```

You could put search state in:

```text
App
```

But why?

Only:

```text
ProductPage
```

needs it.

So:

```text
❌ App
   └── search state
```

is unnecessary lifting.

Better:

```text
✅ ProductPage
   └── search state
```

This is why we say:

> **Lift state only as high as necessary, not as high as possible.**

That's a very good interview statement.

---

# 13. State colocation vs lifting state

These aren't contradictory.

They work together.

### Colocation says:

> Keep state close to where it is needed.

### Lifting state says:

> If multiple components need the same state, move it to their common parent.

Together:

```text
Find all consumers
      ↓
Find their lowest common ancestor
      ↓
Put state there
```

That's the practical algorithm.

---

# 14. State splitting

Now another optimization technique.

Suppose:

```jsx
const [state, setState] = useState({
  name: "",
  email: "",
  theme: "dark",
  notifications: true
});
```

One state object contains unrelated concerns.

You might instead use:

```jsx
const [name, setName] = useState("");
const [email, setEmail] = useState("");
const [theme, setTheme] = useState("dark");
const [notifications, setNotifications] = useState(true);
```

This is **state splitting**.

### Definition

**State splitting means separating independent pieces of state so they can update independently instead of being bundled into one large state value.**

---

# 15. Why can state splitting help?

Imagine:

```text
name changes
```

but:

```text
theme
notifications
email
```

didn't change.

If everything is represented as one large object, updating it typically creates a new object:

```jsx
setState({
  ...state,
  name: "Nikitha"
});
```

Now:

```text
state reference changed
```

This can matter when that object is passed to children or contexts.

With independent state:

```jsx
setName("Nikitha");
```

you have a more focused state update.

However, don't blindly split every property into separate `useState` calls. Related state can sometimes be better managed together, especially when it must change atomically.

---

# 16. Derived state

Now one of the most important concepts.

### Definition

**Derived state is data that can be calculated from existing props or state instead of being stored separately as another state value.**

Bad:

```jsx
const [firstName, setFirstName] = useState("");
const [lastName, setLastName] = useState("");
const [fullName, setFullName] = useState("");
```

Why store `fullName`?

Because:

```text
fullName = firstName + lastName
```

It's already derivable.

Instead:

```jsx
const fullName = `${firstName} ${lastName}`;
```

---

# 17. Why is unnecessary state bad?

Suppose:

```jsx
const [items, setItems] = useState([]);
const [count, setCount] = useState(0);
```

You could do:

```jsx
const [itemCount, setItemCount] = useState(0);
```

and manually keep it synchronized.

Now you have two pieces of data representing related information.

Potential problem:

```text
items = [A, B, C]
itemCount = 2
```

Your application is inconsistent.

Instead:

```jsx
const itemCount = items.length;
```

Now:

```text
items
  ↓
items.length
  ↓
itemCount
```

One source of truth.

---

# 18. "Single source of truth"

### Definition

**Single source of truth means storing a piece of information in one authoritative place and deriving other representations from it.**

Example:

```jsx
const [items, setItems] = useState([]);
```

Then:

```jsx
const itemCount = items.length;
const hasItems = items.length > 0;
```

Instead of:

```jsx
const [itemCount, setItemCount] = useState(0);
const [hasItems, setHasItems] = useState(false);
```

This reduces synchronization problems.

---

# 19. But what about expensive derived calculations?

Suppose:

```jsx
const filteredProducts = products
  .filter(...)
  .sort(...);
```

This is derived data.

Usually:

```jsx
const filteredProducts = ...
```

is enough.

If the calculation is expensive:

```jsx
const filteredProducts = useMemo(() => {
  return products
    .filter(...)
    .sort(...);
}, [products, search]);
```

Now notice the order:

```text
First:
Avoid unnecessary state

Then:
Derive data

Then, if derivation is expensive:
Consider useMemo
```

Don't jump immediately to `useMemo`.

---

# 20. This is a major optimization hierarchy

When you see a performance problem:

```text
             PERFORMANCE
                  │
                  ↓
       Can I remove unnecessary state?
                  │
                  ↓
       Can I colocate the state?
                  │
                  ↓
       Can I reduce update scope?
                  │
                  ↓
       Can I derive instead of storing?
                  │
                  ↓
       Can I split unrelated state?
                  │
                  ↓
       THEN consider memoization
```

This is much more powerful than blindly adding `useMemo`.

---

# 21. Real-world example

Imagine:

```text
App
│
├── Navbar
├── Sidebar
├── Dashboard
│   ├── RevenueChart
│   ├── SalesChart
│   └── UserChart
├── Modal
└── Footer
```

You have:

```jsx
function App() {
  const [isModalOpen, setIsModalOpen] = useState(false);

  return (
    <>
      <Navbar />
      <Sidebar />
      <Dashboard />
      <Modal
        open={isModalOpen}
        onClose={() => setIsModalOpen(false)}
      />
      <Footer />
    </>
  );
}
```

The modal state belongs to `App` because:

```text
App
└── Modal
```

But suppose the modal is only needed inside:

```text
Dashboard
```

Then you could have:

```text
Dashboard
└── Modal
```

and move:

```jsx
const [isModalOpen, setIsModalOpen] = useState(false);
```

into `Dashboard`.

Now:

```text
Modal interaction
     ↓
Dashboard update
```

instead of:

```text
Modal interaction
     ↓
App update
```

This reduces the scope of the update.

---

# 22. A subtle but important point

Don't interpret:

```text
App rerender
```

as:

```text
entire browser DOM recreated
```

We already established this.

If `App` renders:

```text
Navbar
Sidebar
Dashboard
Footer
```

React doesn't destroy and recreate all those DOM nodes simply because App rendered.

But:

```text
less React rendering/reconciliation work
```

is still valuable.

Optimization is about reducing unnecessary work at multiple levels.

---

# 23. State placement and `React.memo`

Suppose:

```text
App
│
├── ExpensiveChart
└── Counter
```

If counter state is in App:

```jsx
function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <ExpensiveChart />
      <Counter count={count} />
    </>
  );
}
```

Every counter update causes App to render.

You could use:

```jsx
const ExpensiveChart = React.memo(...);
```

But there's another solution.

Move state down:

```text
App
│
├── ExpensiveChart
└── Counter
      └── count state
```

Now:

```text
Counter state changes
       ↓
Counter updates
```

`App` doesn't need to participate in that state update.

This can be better than memoizing `ExpensiveChart`.

---

# 24. This is why architecture beats memoization

Compare:

### Approach A

```text
State too high
    ↓
Large subtree affected
    ↓
Add React.memo everywhere
```

versus:

### Approach B

```text
State correctly colocated
    ↓
Small subtree affected
    ↓
Less need for memoization
```

Approach B is usually cleaner.

That's an important senior-level mindset.

---

# 25. A practical rule for state placement

Whenever you create:

```jsx
const [something, setSomething] = useState(...)
```

ask:

### Question 1

Who needs this state?

### Question 2

Which components actually read it?

### Question 3

Which components update it?

### Question 4

What is their lowest common ancestor?

### Question 5

Can the state live lower?

### Question 6

Does this state actually need to exist?

Maybe it's derived data.

---

# 26. The complete state optimization model

```text
                    STATE
                      │
             ┌────────┴────────┐
             ↓                 ↓
       Does it need         Can it be
       to be stored?        derived?
             │                 │
            YES               YES
             │                 ↓
             │           Don't store it
             ↓
       Who needs it?
             │
             ↓
      Find lowest common
          ancestor
             │
             ↓
       Colocate state
             │
             ↓
     Is state unrelated?
        /           \
      YES           NO
       ↓             ↓
 Split state      Keep together
       │
       ↓
  Minimize update
      scope
```

---

# 27. Interview answer 🎯

If asked:

### "What is state colocation and why is it important for performance?"

You can say:

> "State colocation means keeping state as close as possible to the components that actually need it. If frequently changing state is unnecessarily placed high in the component tree, updates can cause rendering work across a larger subtree. By moving the state to the lowest appropriate common ancestor, we reduce the scope of updates and reduce the need for memoization. I generally try to colocate state first and use memoization only when profiling shows additional optimization is necessary."

That's a strong senior-style answer.

---

# 28. The most important sentence from this part

Remember this:

> **Before trying to optimize a component's rendering, ask whether the component should be affected by that state update in the first place.**

That's the deeper principle behind state colocation.

---

## Next Part — Context API Optimization

This is where things become **very interesting**.

We'll understand:

```text
Context Provider
      ↓
value changes
      ↓
which consumers update?
      ↓
why React.memo doesn't automatically save them
      ↓
context propagation
      ↓
unstable provider values
      ↓
useMemo + useCallback with Context
      ↓
splitting contexts
      ↓
state context vs dispatch context
      ↓
context selectors
      ↓
how libraries like Redux/Zustand avoid broad updates
```

And I'll explain the **internal update flow**, not just "useMemo the provider value."

# Part 9 — Context API Optimization: What Actually Happens Internally

## 1. Why Context Can Affect Rendering

React Context makes values available deeply in the component tree without passing them through every intermediate component as props.

The important optimization question is:

> **What actually happens when a Context value changes?**

A common misconception is:

> "`React.memo` will prevent a component from rendering when Context changes."

That is not correct for a component that **consumes that Context**.

A component that reads Context with `useContext` has a dependency on that Context.

---

## 2. What Is a Context Consumer?

A Context consumer is a component that reads a Context value.

```jsx
const ThemeContext = createContext();

function Button() {
  const theme = useContext(ThemeContext);

  return <button>{theme}</button>;
}
```

`Button` is now dependent on `ThemeContext`.

Conceptually:

```text
Button
  ↓
depends on ThemeContext
```

When the relevant Context value changes, React can schedule work for that consumer.

---

## 3. Basic Context Update Flow

```text
Provider value changes
        ↓
React detects Context value change
        ↓
React identifies components consuming that Context
        ↓
Those consumers can receive update work
        ↓
Consumer component renders
```

Context propagation is a different update mechanism from ordinary parent-to-child prop comparison.

---

## 4. Why React.memo Does Not Automatically Stop Context Updates

```jsx
const User = React.memo(function User() {
  const user = useContext(UserContext);

  return <div>{user.name}</div>;
});
```

It is tempting to think:

```text
React.memo
    ↓
props didn't change
    ↓
skip rendering
```

But the component also has a Context dependency.

If the consumed Context changes, the Context update can cause the component to receive update work.

So:

```text
React.memo
    ↓
can compare props

useContext
    ↓
creates a Context dependency
```

Therefore, `React.memo` is not a universal "never render this component again" mechanism.

---

# 5. Provider Value Identity Matters

Consider:

```jsx
function App() {
  const [user, setUser] = useState(null);

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <Dashboard />
    </UserContext.Provider>
  );
}
```

The object:

```jsx
{ user, setUser }
```

is created again whenever `App` renders.

Conceptually:

```text
Render 1:
{ user, setUser } → object A

Render 2:
{ user, setUser } → object B
```

Even if `user` has not meaningfully changed:

```text
object A !== object B
```

The provider's value identity has changed.

That can cause Context consumers to be considered for Context update work.

---

# 6. Stabilizing the Provider Value with useMemo

```jsx
function App() {
  const [user, setUser] = useState(null);

  const value = useMemo(
    () => ({ user, setUser }),
    [user]
  );

  return (
    <UserContext.Provider value={value}>
      <Dashboard />
    </UserContext.Provider>
  );
}
```

Now React can reuse the previous object when `user` has not changed.

Conceptually:

```text
user unchanged
      ↓
useMemo reuses previous value object
      ↓
Context value identity remains stable
```

When `user` changes:

```text
user changed
      ↓
useMemo creates a new value object
      ↓
Context value changed
      ↓
Consumers depending on it can receive update work
```

Important:

> `useMemo` does not prevent legitimate Context updates. It prevents unnecessary Context value identity changes.

---

# 7. Why setUser Does Not Need to Be a Dependency Here

React state setter functions have stable identity.

Therefore:

```jsx
const value = useMemo(
  () => ({ user, setUser }),
  [user]
);
```

is sufficient in this example.

The changing value is `user`.

The setter:

```jsx
setUser
```

does not get recreated as a new function on every render in the same way as an inline function would.

---

# 8. The Bigger Problem: Giant Contexts

Consider:

```jsx
const AppContext = createContext();

const value = {
  user,
  theme,
  language,
  cart,
  notifications,
  permissions,
  settings
};
```

Now many unrelated pieces of application state live inside one Context.

Conceptually:

```text
                AppContext
                    |
      --------------------------------
      |       |       |       |      |
     user   theme   cart   language  ...
```

If one part changes, the Context value can change.

That creates a broad update domain.

For example:

```text
cart changes
   ↓
AppContext value changes
   ↓
Context consumers depending on AppContext
   ↓
potentially broad update work
```

Even if a component only cares about `theme`, it is still consuming the same Context.

---

# 9. Context Splitting

A common optimization is to split unrelated Contexts.

```jsx
<UserContext.Provider value={user}>
  <ThemeContext.Provider value={theme}>
    <CartContext.Provider value={cart}>
      <App />
    </CartContext.Provider>
  </ThemeContext.Provider>
</UserContext.Provider>
```

Now the update domains are more focused:

```text
User changes
   ↓
UserContext consumers

Theme changes
   ↓
ThemeContext consumers

Cart changes
   ↓
CartContext consumers
```

The goal is not "more Contexts are always better."

The goal is:

> **Separate unrelated update domains when that makes the subscription scope more focused.**

---

# 10. State Context and Dispatch Context

Another useful pattern is separating state from the function used to update that state.

```jsx
const CountStateContext = createContext();
const CountDispatchContext = createContext();
```

Provider:

```jsx
function CountProvider({ children }) {
  const [count, setCount] = useState(0);

  return (
    <CountStateContext.Provider value={count}>
      <CountDispatchContext.Provider value={setCount}>
        {children}
      </CountDispatchContext.Provider>
    </CountStateContext.Provider>
  );
}
```

A component that only needs the setter can consume:

```jsx
const setCount = useContext(CountDispatchContext);
```

while a component that needs the value consumes:

```jsx
const count = useContext(CountStateContext);
```

Because the state setter identity is stable, the dispatch Context does not need to change merely because `count` changes.

Conceptually:

```text
count changes
     ↓
CountStateContext changes
     ↓
state consumers can update

CountDispatchContext
     ↓
setter identity remains stable
     ↓
dispatch-only consumers don't need a Context-value change
```

---

# 11. Context Selectors

Normal `useContext` subscribes the component to the Context value.

For example:

```jsx
const value = useContext(AppContext);
```

Suppose:

```jsx
const value = {
  user,
  theme,
  cart
};
```

If the component only needs:

```jsx
value.theme
```

normal Context consumption does not automatically turn the subscription into:

```text
only theme
```

Destructuring does not change the subscription granularity:

```jsx
const { theme } = useContext(AppContext);
```

still consumes the Context as a whole.

Conceptually, selector-based approaches allow something like:

```jsx
useContextSelector(
  AppContext,
  value => value.theme
);
```

The idea is:

```text
Component A → subscribes to user
Component B → subscribes to theme
Component C → subscribes to cart
```

instead of every component effectively depending on the whole Context value.

---

# 12. Context vs Selector-Based State Libraries

Libraries such as Redux or Zustand can provide selector-based subscriptions.

Conceptually:

```text
Normal Context
    ↓
consumer depends on Context value

Selector-based store
    ↓
component can subscribe to selected slice
```

For example:

```jsx
const theme = useStore(state => state.theme);
```

The component is interested in a particular slice rather than simply consuming a large Context value.

---

# 13. A Common Optimization Mistake

A common mistake is:

```jsx
const value = useMemo(
  () => ({
    user,
    theme,
    cart,
    notifications,
    permissions
  }),
  [user, theme, cart, notifications, permissions]
);
```

and then concluding:

> "My Context is optimized because I used useMemo."

`useMemo` only solves one problem:

```text
unnecessary recreation of the Context value object
```

It does **not** solve:

```text
the Context contains many unrelated pieces of state
```

It does **not** automatically create fine-grained subscriptions.

Therefore:

```text
useMemo
    ↓
stabilizes Context value identity

Context splitting
    ↓
reduces update scope

Selectors
    ↓
provide finer-grained subscriptions
```

These solve different problems.

---

# 14. Internal Mental Model

When optimizing Context:

```text
Provider renders
      ↓
Provider computes value
      ↓
useMemo checks dependencies
      ↓
same dependencies?
      ├── YES → reuse previous value object
      │
      └── NO  → create new value object
      ↓
Did Context value identity change?
      ↓
React identifies consumers depending on that Context
      ↓
Those consumers can receive update work
      ↓
Consumer rendering/reconciliation happens
```

This is the important internal model.

---

# 15. Compare the Different Update Mechanisms

## Props

```text
Parent renders
      ↓
Child receives props
      ↓
React.memo can compare props
      ↓
If props are equivalent → possible bailout
```

## Context

```text
Provider value changes
      ↓
React tracks Context consumers
      ↓
Consumers depending on the changed Context
can receive update work
```

## State

```text
setState
   ↓
State owner receives update
   ↓
React schedules work
   ↓
Component renders/reconciles
```

These are different update paths.

---

# 16. Important Connection with React.memo

```jsx
const Child = React.memo(function Child() {
  const theme = useContext(ThemeContext);

  return <div>{theme}</div>;
});
```

There are two different concepts:

```text
React.memo
    ↓
optimizes based on props

useContext
    ↓
creates dependency on Context
```

The correct mental model is not:

> "`React.memo` means the component can never render."

Instead:

> "`React.memo` allows React to bail out when the component's props have not changed, but a consumed Context update is a separate source of update work."

---

# 17. Context Optimization Hierarchy

When a Context is causing unnecessary rendering, investigate in this order.

### 1. Is the Provider value recreated unnecessarily?

If yes:

```jsx
useMemo(...)
```

may help.

### 2. Is the Context too broad?

If yes:

```text
split the Context
```

### 3. Do components need only a small part of the state?

If yes:

```text
consider selector-based subscriptions
```

### 4. Do consumers only need the dispatch/setter?

If yes:

```text
consider separate state and dispatch Contexts
```

### 5. Is memoization actually necessary?

Do not add `useMemo` everywhere.

For example, memoizing a primitive value that is already stable or cheap to produce usually adds unnecessary complexity.

---

# 18. The Real Performance Question

Do not ask only:

> "How can I stop this component from rendering?"

Ask:

> **"Why did this component become part of this update?"**

Then ask:

```text
What caused the update?
        ↓
State?
Props?
Context?
External store?
        ↓
Why is this component affected?
        ↓
Can the update scope be reduced?
        ↓
Can identity be stabilized?
        ↓
Can a bailout be created?
```

This is a much stronger optimization mindset than blindly adding `React.memo`.

---

# 19. Key Takeaways

- Context provides values deeply without prop drilling.
- A component using `useContext` has a dependency on that Context.
- `React.memo` does not automatically prevent Context-driven updates.
- Provider value identity matters.
- Inline objects/functions used as Provider values can create new identities on every render.
- `useMemo` can stabilize a Provider value when its dependencies have not changed.
- `useMemo` does not prevent legitimate Context updates.
- A giant Context can create a broad update domain.
- Splitting Contexts can make update boundaries more focused.
- State and dispatch can be separated into different Contexts when useful.
- Normal `useContext` does not automatically subscribe to only a selected property.
- Destructuring a Context value does not change its subscription granularity.
- Selector-based approaches can provide finer-grained subscriptions.
- `useMemo`, Context splitting, and selectors solve different optimization problems.
- The goal is to reduce unnecessary update scope, not simply to add more memoization.

---

# Interview Answer

### Q: How do you optimize Context API performance in React?

**Answer:**

> "First, I check whether the Provider value is being recreated unnecessarily. If the value is an object or contains functions, I can use `useMemo` and `useCallback` where appropriate to stabilize its identity. Then I check whether the Context is too broad. If unrelated state such as user, theme, cart, and notifications is inside one Context, I can split it into smaller Contexts so updates affect more focused groups of consumers. If components only need specific parts of a large state tree, selector-based subscriptions can provide finer-grained updates. I can also separate state and dispatch Contexts when consumers only need the setter. The key is to reduce the update scope rather than blindly adding `React.memo`."

---

# Final Mental Model

```text
Context optimization is about:

        WHY DID THIS CONSUMER UPDATE?
                    ↓
        Is the Provider value changing?
                    ↓
        Is the value recreated unnecessarily?
                    ↓
              useMemo?
                    ↓
        Is the Context too broad?
                    ↓
          Split the Context?
                    ↓
        Need only part of the state?
                    ↓
       Selector-based subscription?
                    ↓
        Need only dispatch/setter?
                    ↓
      Separate state + dispatch?
```

The most important principle is:

> **Do not optimize the component first. Optimize the update boundary first.**




# PART 10 — EVENT OPTIMIZATION

## 1. First: What is an Event?

An **event** is something that happens in the browser that JavaScript can respond to.

Examples:

```text
User types       → input event
User clicks      → click event
User scrolls     → scroll event
User resizes     → resize event
Mouse moves      → mousemove event
Keyboard press   → keydown event
```

For example:

```jsx
<input onChange={handleChange} />
```

Whenever the user types, React receives an event and calls:

```js
handleChange(event)
```

So:

```text
User action
    ↓
Browser detects event
    ↓
React event system
    ↓
Your event handler
    ↓
Your code runs
```

The important problem is:

> **Some events can happen extremely frequently.**

And that's where event optimization becomes important.

---

# 2. Why Do We Need Event Optimization?

Consider a search box.

```jsx
<input onChange={handleSearch} />
```

User types:

```text
r
re
rea
reac
react
```

That's **5 events**.

If your handler makes an API request:

```js
function handleSearch(e) {
  fetch(`/api/search?q=${e.target.value}`);
}
```

you could get:

```text
r       → API call
re      → API call
rea     → API call
reac    → API call
react   → API call
```

The user only wanted:

```text
react → API call
```

So we generated unnecessary work.

Now imagine scroll.

A user scrolls for 2 seconds.

The browser may fire many `scroll` events during that time.

If every event does:

```js
calculateSomething();
setState(...);
API call();
```

we may create a lot of unnecessary work.

So event optimization tries to control:

> **How often expensive work is allowed to happen when events occur frequently.**

The three major tools we'll learn are:

```text
Debouncing
Throttling
requestAnimationFrame
```

---

# 3. Before Debounce: Understand `setTimeout`

You need to understand this first.

```js
setTimeout(() => {
  console.log("Hello");
}, 1000);
```

This means:

> Ask the browser to execute this callback after at least approximately 1000 ms, when the JavaScript thread is available.

It does **not** mean:

> "Pause JavaScript for 1 second."

JavaScript does not stop.

Conceptually:

```text
JavaScript
   ↓
setTimeout(callback, 1000)
   ↓
Timer registered with browser
   ↓
JavaScript continues
   ↓
~1000ms passes
   ↓
callback becomes eligible to run
   ↓
Event loop
   ↓
callback executes when call stack is available
```

This is important for understanding debounce.

---

# 4. What is Debouncing?

### Definition

**Debouncing means delaying execution until a period of inactivity has passed.**

In simple words:

> "Keep waiting while the event keeps happening. Execute only after the event stops for a certain amount of time."

Suppose debounce delay = `500ms`.

User types:

```text
r
```

Timer starts.

```text
r
│
└── wait 500ms
```

But before 500ms:

```text
re
```

The previous timer is cancelled.

New timer starts.

Then:

```text
rea
```

Previous timer cancelled.

New timer starts.

Then:

```text
reac
```

Previous timer cancelled.

New timer starts.

Then:

```text
react
```

Previous timer cancelled.

New timer starts.

Finally user stops typing.

After 500ms:

```text
API CALL
```

So:

```text
r       → timer
re      → cancel previous → new timer
rea     → cancel previous → new timer
reac    → cancel previous → new timer
react   → cancel previous → new timer

500ms silence
      ↓
API call
```

That's debounce.

---

# 5. Why Is It Called "Debounce"?

Think of a mechanical button.

If you physically press a button, the electrical signal can rapidly fluctuate:

```text
ON OFF ON OFF ON OFF
```

Instead of reacting to every tiny fluctuation, you wait until the signal becomes stable.

Debouncing in software follows the same basic idea:

> **Wait for the activity to settle before performing the action.**

---

# 6. Debounce with Search

Let's write it in React.

```jsx
function Search() {
  const [search, setSearch] = useState("");

  useEffect(() => {
    const timer = setTimeout(() => {
      console.log("API CALL:", search);
    }, 500);

    return () => {
      clearTimeout(timer);
    };
  }, [search]);

  return (
    <input
      value={search}
      onChange={(e) => setSearch(e.target.value)}
    />
  );
}
```

Now let's understand this **very carefully**.

---

# 7. What Actually Happens Internally?

Initial render:

```text
search = ""
```

User types:

```text
r
```

This executes:

```js
setSearch("r")
```

React schedules a state update.

React renders again.

Now:

```text
search = "r"
```

The effect runs:

```js
setTimeout(() => {
   API("r");
}, 500);
```

Timer created:

```text
Timer #1
```

Now user types:

```text
e
```

State becomes:

```text
"re"
```

React renders again.

Before running the new effect, React runs the previous effect's cleanup:

```js
clearTimeout(timer);
```

So:

```text
Timer #1 ❌ cancelled
```

Then new effect runs:

```js
setTimeout(() => {
   API("re");
}, 500);
```

Timer #2.

Then user types:

```text
a
```

Same thing:

```text
Timer #2 ❌ cancelled

Timer #3 created
```

Eventually:

```text
react
```

Timer created.

Then user stops typing.

No new render happens.

So nobody cancels the final timer.

After 500ms:

```text
Timer executes
      ↓
API call
```

This is the core of debouncing.

---

# 8. The Most Important Part of Debounce

The important mechanism is:

```js
clearTimeout(previousTimer);
setTimeout(newTimer);
```

Every new event:

```text
Cancel old timer
      ↓
Create new timer
```

Therefore the callback only survives when events stop.

---

# 9. Debounce from Scratch

Let's build it without React.

```js
function debounce(fn, delay) {
  let timer;

  return (...args) => {
    clearTimeout(timer);

    timer = setTimeout(() => {
      fn(...args);
    }, delay);
  };
}
```

Usage:

```js
const search = debounce((value) => {
  console.log("API:", value);
}, 500);
```

Then:

```js
search("r");
search("re");
search("rea");
search("reac");
search("react");
```

Internally:

```text
search("r")
   ↓
timer = Timer 1

search("re")
   ↓
clear Timer 1
timer = Timer 2

search("rea")
   ↓
clear Timer 2
timer = Timer 3

search("reac")
   ↓
clear Timer 3
timer = Timer 4

search("react")
   ↓
clear Timer 4
timer = Timer 5

500ms
 ↓
Timer 5 executes
 ↓
fn("react")
```

Only the last call survives.

---

# 10. Why Does `timer` Remember Its Value?

This is where **closure** comes in.

Remember:

> A closure allows a function to retain access to variables from its surrounding lexical scope even after that outer function has finished executing.

Here:

```js
function debounce(fn, delay) {
  let timer;

  return (...args) => {
    clearTimeout(timer);

    timer = setTimeout(...);
  };
}
```

`debounce()` returns the inner function.

The `debounce()` function finishes.

But the returned function still remembers:

```js
timer
```

So every time you call:

```js
search("r")
search("re")
search("react")
```

the same `timer` variable is accessed.

That's how debounce can cancel the previous timer.

---

# 11. Debounce — Real-World Use Cases

Debounce is excellent when you care about the **final result after activity stops**.

Common examples:

### Search

```text
User typing → wait → API request
```

### Autosave

```text
User editing document
        ↓
wait until user stops typing
        ↓
save
```

### Filter

```text
User changing filter
        ↓
wait
        ↓
expensive filtering
```

### Resize

```text
User resizing window
        ↓
wait until resize stops
        ↓
perform expensive calculation
```

---

# 12. Now Throttling

Debouncing asks:

> "Has the event stopped?"

Throttling asks:

> **"How often am I allowed to run?"**

### Definition

**Throttling limits a function to execute at most once within a specified time interval.**

Suppose:

```text
throttle delay = 1000ms
```

And events happen:

```text
0ms
100ms
200ms
300ms
400ms
500ms
600ms
...
```

Throttle says:

```text
Run
 ↓
block execution for 1000ms
 ↓
after allowed interval, run again
```

So instead of:

```text
event event event event event event event
↓     ↓     ↓     ↓     ↓     ↓     ↓
run   run   run   run   run   run   run
```

you might get:

```text
event event event event event event
↓
RUN
                  ↓
                RUN
                              ↓
                            RUN
```

---

# 13. Debounce vs Throttle — The Core Difference

This is extremely important.

### Debounce

> **Wait until events stop.**

Example:

```text
typing → typing → typing → typing → STOP
                                      ↓
                                    RUN
```

Best for:

```text
Search
Autosave
Final resize calculation
```

---

### Throttle

> **Allow execution periodically while events continue.**

Example:

```text
scroll scroll scroll scroll scroll scroll
   ↓
 RUN        RUN        RUN        RUN
```

Best for:

```text
Scroll tracking
Mouse movement
Resize tracking
Continuous user activity
```

---

# 14. Simple Analogy

Imagine someone keeps knocking on your door.

### Debounce

You say:

> "I'll open the door only when they stop knocking."

```text
knock
knock
knock
knock
...
silence
↓
open door
```

### Throttle

You say:

> "I'll open the door at most once every 5 minutes."

```text
knock
↓
open

knock
knock
knock
↓
ignore

5 minutes
↓
open again
```

That's the difference.

---

# 15. Throttle Implementation

Basic version:

```js
function throttle(fn, delay) {
  let lastTime = 0;

  return (...args) => {
    const now = Date.now();

    if (now - lastTime >= delay) {
      lastTime = now;
      fn(...args);
    }
  };
}
```

Usage:

```js
const handleScroll = throttle(() => {
  console.log("scroll");
}, 1000);
```

Now suppose scroll generates:

```text
0ms
100ms
200ms
300ms
1000ms
1100ms
2000ms
```

The function executes approximately:

```text
0ms     → RUN
100ms   → skip
200ms   → skip
300ms   → skip
1000ms  → RUN
1100ms  → skip
2000ms  → RUN
```

So execution is limited.

---

# 16. Why Does Throttle Work?

Again, closure.

```js
let lastTime = 0;
```

The returned function remembers `lastTime`.

Every event:

```js
const now = Date.now();

if (now - lastTime >= delay) {
   lastTime = now;
   fn();
}
```

So it asks:

```text
How much time passed since the last execution?
```

If enough time passed:

```text
execute
```

Otherwise:

```text
ignore
```

---

# 17. Leading and Trailing Execution

Now we're getting into more advanced throttle/debounce behavior.

There are two concepts:

```text
Leading
Trailing
```

### Leading

Execute at the **beginning** of the event burst.

Example:

```text
event
 ↓
RUN immediately
```

### Trailing

Execute at the **end** of the event burst.

Example:

```text
event event event event
                    ↓
                  STOP
                    ↓
                  RUN
```

---

# 18. Debounce Usually Means Trailing

Typical debounce:

```text
User types
 ↓
wait
 ↓
wait
 ↓
wait
 ↓
user stops
 ↓
execute
```

That's **trailing execution**.

But debounce implementations can also support:

```text
leading: true
```

meaning:

```text
first event → execute immediately
subsequent events → ignored/reset timer
```

Libraries such as Lodash provide configurable leading/trailing behavior.

---

# 19. Why Not Just Use Debounce Everywhere?

Because debounce can make continuous interactions feel wrong.

Imagine scroll.

User is scrolling continuously:

```text
scroll
scroll
scroll
scroll
scroll
scroll
```

With debounce:

```text
scroll → reset timer
scroll → reset timer
scroll → reset timer
scroll → reset timer
```

If the user keeps scrolling, the callback may **never run until scrolling stops**.

That's bad if you need continuous feedback.

For example:

```text
scroll position → update progress bar
```

You don't want:

```text
scroll...
scroll...
scroll...
STOP
↓
finally update progress
```

You want periodic updates while scrolling.

That's where throttle or `requestAnimationFrame` becomes useful.

---

# 20. Scroll Example

Without optimization:

```jsx
window.addEventListener("scroll", () => {
  calculateSomethingExpensive();
});
```

Potentially:

```text
scroll event
scroll event
scroll event
scroll event
scroll event
scroll event
...
```

Every event can invoke expensive JavaScript.

With throttle:

```text
many scroll events
       ↓
throttle
       ↓
one execution per interval
```

With rAF:

```text
many scroll events
       ↓
schedule visual update
       ↓
browser's next animation frame
       ↓
update visual state
```

We'll get to rAF now.

---

# 21. What Is `requestAnimationFrame`?

### Definition

`requestAnimationFrame()` tells the browser:

> **"Run this callback before the browser performs the next repaint/animation frame."**

Example:

```js
requestAnimationFrame(() => {
  console.log("Update visual state");
});
```

The browser controls when the callback runs based on its rendering cycle.

This makes it particularly useful for:

```text
Animations
Visual updates
Scroll-linked visual effects
Dragging
Mouse movement visuals
```

---

# 22. Browser Rendering Loop

At a high level, the browser repeatedly goes through work like:

```text
JavaScript
   ↓
Style calculation
   ↓
Layout
   ↓
Paint
   ↓
Composite
   ↓
Next frame
```

For a display running around 60Hz, the conceptual frame budget is approximately:

```text
1000 / 60 ≈ 16.67ms
```

That doesn't mean every browser must render exactly every 16.67ms, but it's a useful mental model.

`requestAnimationFrame` allows you to coordinate visual JavaScript work with that rendering cycle.

---

# 23. `setTimeout` vs `requestAnimationFrame`

### setTimeout

```js
setTimeout(update, 16);
```

You're saying:

> "Try to run this after about 16ms."

The timer isn't synchronized with the browser's paint cycle.

---

### requestAnimationFrame

```js
requestAnimationFrame(update);
```

You're saying:

> "Run this when the browser is preparing the next visual frame."

So:

```text
setTimeout
   ↓
timer-based scheduling
```

while:

```text
requestAnimationFrame
   ↓
browser rendering-cycle scheduling
```

For visual updates, rAF is generally the more appropriate primitive.

---

# 24. Why rAF Is Good for Animation

Imagine moving an element.

Bad conceptual approach:

```js
setInterval(() => {
  element.style.transform = ...
}, 10);
```

You're basically saying:

> Run this on my timer.

But the browser has its own rendering schedule.

You can end up doing work:

```text
JavaScript update
        ↓
browser isn't ready to paint yet
        ↓
another update
        ↓
paint
```

rAF aligns the callback with the browser's animation frame:

```text
requestAnimationFrame
        ↓
browser schedules callback
        ↓
callback performs visual update
        ↓
browser renders frame
```

This helps avoid unnecessary visual updates.

---

# 25. Very Important: rAF Is NOT Debounce

They solve different problems.

### Debounce

```text
Events
↓↓↓↓↓↓↓↓↓↓

wait for silence

                 ↓
               execute
```

### Throttle

```text
Events
↓↓↓↓↓↓↓↓↓↓

execute periodically

↓       ↓       ↓       ↓
RUN     RUN     RUN     RUN
```

### requestAnimationFrame

```text
Events
↓↓↓↓↓↓↓↓↓↓

schedule visual work
        ↓
next browser frame
        ↓
RUN
        ↓
next frame
        ↓
RUN
```

---

# 26. React-Specific Example: Search

This is a very important distinction.

Suppose:

```jsx
<input
  value={search}
  onChange={(e) => setSearch(e.target.value)}
/>
```

Should we debounce `setSearch`?

Usually, **no** if `search` controls the input.

You generally want:

```text
User types
 ↓
setSearch immediately
 ↓
input updates immediately
```

The UI should feel responsive.

Instead, debounce the expensive side effect:

```text
User types
 ↓
setSearch immediately
 ↓
React updates input
 ↓
debounced API request
```

That's a much better architecture.

---

# 27. Correct React Search Architecture

```jsx
function SearchBox() {
  const [search, setSearch] = useState("");

  useEffect(() => {
    const timer = setTimeout(() => {
      if (search.trim()) {
        fetchResults(search);
      }
    }, 500);

    return () => clearTimeout(timer);
  }, [search]);

  return (
    <input
      value={search}
      onChange={(e) => setSearch(e.target.value)}
    />
  );
}
```

The flow:

```text
User types "r"
       ↓
setSearch("r")
       ↓
Render
       ↓
Effect starts timer

User types "re"
       ↓
setSearch("re")
       ↓
Render
       ↓
cleanup
       ↓
cancel old timer
       ↓
new timer

...

User stops
       ↓
500ms
       ↓
fetchResults("react")
```

This gives us:

```text
Fast UI
+
Reduced API calls
```

---

# 28. What Happens If We Don't Debounce?

```text
r
 ↓
API

re
 ↓
API

rea
 ↓
API

reac
 ↓
API

react
 ↓
API
```

Five requests.

With debounce:

```text
r
re
rea
reac
react
 ↓
wait 500ms
 ↓
ONE API request
```

This reduces:

- network requests
- server load
- response processing
- React state updates caused by responses
- unnecessary rendering
- race-condition opportunities

---

# 29. Another Important Problem: Race Conditions

Suppose you make API requests for:

```text
r
re
rea
react
```

Requests can complete in an unexpected order.

For example:

```text
Request A: "r"
Request B: "re"
Request C: "react"
```

Network:

```text
react → finishes first
r     → finishes last
```

Then the old `"r"` response might overwrite the newer `"react"` result.

Debouncing reduces the number of requests.

But it does **not completely solve cancellation/race conditions**.

That's why later in the roadmap we'll learn:

```text
AbortController
request cancellation
stale response handling
```

This is an important distinction.

---

# 30. Cleanup Is Critical in React

Whenever you create:

```text
timer
event listener
subscription
WebSocket
observer
```

you need to think about cleanup.

Example:

```jsx
useEffect(() => {
  const timer = setTimeout(() => {
    doSomething();
  }, 500);

  return () => {
    clearTimeout(timer);
  };
}, [search]);
```

Why?

Because when `search` changes:

```text
old effect
   ↓
cleanup
   ↓
clear old timer
   ↓
new effect
   ↓
new timer
```

This is exactly what gives us the debounce behavior.

---

# 31. Event Listener Cleanup

Suppose:

```jsx
useEffect(() => {
  function handleScroll() {
    console.log(window.scrollY);
  }

  window.addEventListener("scroll", handleScroll);

  return () => {
    window.removeEventListener("scroll", handleScroll);
  };
}, []);
```

Without cleanup, you can accidentally keep listeners around when the component is removed or when an effect is recreated.

That can lead to:

```text
memory leaks
+
duplicate handlers
+
unnecessary work
```

We'll cover memory optimization later in the roadmap.

---

# 32. Debounce vs Throttle vs rAF

Memorize this table:

| Technique | Main idea | Best use |
|---|---|---|
| Debounce | Run after activity stops | Search, autosave |
| Throttle | Run at most once per interval | Scroll, resize, mouse events |
| rAF | Run with browser's next visual frame | Animations, visual updates |

Think:

```text
DEBOUNCE
"Wait until they stop."

THROTTLE
"Slow down how often I respond."

rAF
"Coordinate visual work with the browser's frame."
```

---

# 33. Internal Flow Comparison

### Without optimization

```text
Event
 ↓
Handler
 ↓
Expensive work
 ↓
React update
 ↓
Render
```

Repeated many times:

```text
Event → work
Event → work
Event → work
Event → work
Event → work
```

---

### Debounce

```text
Event
 ↓
cancel previous timer
 ↓
start timer
 ↓
new event?
 ├─ YES → cancel + restart
 └─ NO
      ↓
   execute
```

---

### Throttle

```text
Event
 ↓
check last execution
 ↓
enough time?
 ├─ NO → ignore
 └─ YES
      ↓
    execute
```

---

### requestAnimationFrame

```text
Event
 ↓
schedule frame update
 ↓
browser reaches next frame
 ↓
callback
 ↓
visual update
```

---

# 34. One More Important Concept: Event Optimization ≠ React Optimization Only

This is bigger than React.

Suppose:

```js
window.addEventListener("scroll", handler);
```

Even without React, excessive event handling can hurt performance.

Because JavaScript executes on the browser's main thread.

So:

```text
Frequent events
      ↓
More JavaScript execution
      ↓
Main thread becomes busy
      ↓
Rendering can be delayed
      ↓
UI becomes less responsive
```

React adds another layer:

```text
Event
 ↓
setState
 ↓
React render work
 ↓
reconciliation
 ↓
possible commit
```

So frequent events can cause both:

```text
Browser work
+
React work
```

---

# 35. Example: Scroll + React State

Bad:

```jsx
function ScrollTracker() {
  const [scrollY, setScrollY] = useState(0);

  useEffect(() => {
    const handleScroll = () => {
      setScrollY(window.scrollY);
    };

    window.addEventListener("scroll", handleScroll);

    return () => {
      window.removeEventListener("scroll", handleScroll);
    };
  }, []);

  return <p>{scrollY}</p>;
}
```

This may update React state very frequently during scrolling.

Sometimes that's fine.

But if the component tree is large or the work is expensive, it can become costly.

We can throttle:

```text
scroll events
      ↓
throttle
      ↓
limited setState calls
      ↓
fewer React renders
```

Or use rAF when the goal is a visual update synchronized with frames.

---

# 36. But Don't Optimize Just Because an Event Is Frequent

This is important.

Not every frequent event is a performance problem.

For example:

```jsx
<input onChange={...} />
```

A simple state update is usually completely fine.

Don't automatically think:

> "Input event = debounce."

Instead ask:

### What work happens because of the event?

If it's:

```text
simple state update
```

probably fine.

If it's:

```text
API request
expensive calculation
large filtering
heavy rendering
```

then optimization may be useful.

This follows the larger React optimization principle:

> **Optimize the expensive work, not merely the existence of an event.**

---

# 37. How Event Optimization Connects to Everything We've Learned

This is where the previous parts connect.

Suppose:

```jsx
function Parent() {
  const [search, setSearch] = useState("");

  const results = expensiveSearch(products, search);

  return <ProductList products={results} />;
}
```

Typing causes:

```text
Input event
 ↓
setSearch
 ↓
Parent render
 ↓
expensiveSearch()
 ↓
ProductList render
```

Now we can optimize at different levels.

### Event optimization

Debounce API/search work:

```text
typing → wait → search
```

### State optimization

Colocate search state so fewer components are affected.

### useMemo

```jsx
const results = useMemo(
  () => expensiveSearch(products, search),
  [products, search]
);
```

Avoid unnecessary recalculation.

### React.memo

```jsx
const ProductList = memo(ProductList);
```

Potentially avoid unnecessary child rendering.

### useCallback

Keep callback references stable if passed to memoized children.

So optimization is often a chain:

```text
Event optimization
       ↓
Fewer updates
       ↓
Less React work
       ↓
Less calculation
       ↓
Less component rendering
       ↓
Better responsiveness
```

---

# 38. Interview Definitions

### What is debouncing?

> Debouncing is a technique where a function executes only after a specified period of inactivity. Each new event resets the timer. It is commonly used for search inputs, autosave, and expensive operations triggered by rapid input.

### What is throttling?

> Throttling limits how frequently a function can execute. Even if events occur continuously, the function is allowed to run at most once within a specified interval. It is commonly used for scroll, resize, and mouse movement events.

### What is requestAnimationFrame?

> `requestAnimationFrame` schedules a callback to run before the browser's next repaint, making it suitable for animations and other visual updates that should be synchronized with the browser's rendering cycle.

### Difference between debounce and throttle?

> Debouncing waits until events stop before executing, whereas throttling allows execution periodically while events continue.

### Why use debounce in search?

> Because users can generate multiple input events while typing. Debouncing delays the expensive API request until the user pauses, reducing unnecessary network requests and processing.

### Why use throttle for scroll?

> Scroll events can occur frequently. Throttling limits how often the handler performs expensive work while still allowing periodic updates during scrolling.

### Why use requestAnimationFrame?

> It is useful when JavaScript is updating something visual because the browser schedules the callback around its rendering cycle, making it more appropriate than arbitrary timers for animation-related work.

---

# 39. The Mental Model You Should Remember

Don't memorize random definitions.

Think like this:

```text
                FREQUENT EVENT
                      │
          ┌───────────┼───────────┐
          │           │           │
       Debounce     Throttle     rAF
          │           │           │
      Wait for      Limit        Sync with
       silence      frequency    visual frame
          │           │           │
       Search       Scroll       Animation
       Autosave     Resize       Dragging
```

And the deeper React connection:

```text
Frequent event
      ↓
JavaScript handler
      ↓
Maybe setState
      ↓
React render work
      ↓
Reconciliation
      ↓
Possible DOM update
```

Event optimization tries to reduce unnecessary work **before it becomes expensive React/browser work**.

---

# 40. The One-Line Difference

If you remember nothing else:

> **Debounce = "Wait until the user stops."**

> **Throttle = "Run at a controlled frequency while the user continues."**

> **requestAnimationFrame = "Run visual work in sync with the browser's next frame."**

And one very important React rule:

> **Don't debounce the UI state update just because the event is frequent; usually debounce the expensive side effect such as an API call.**

That distinction is something interviewers often look for. 


# PART 11 — React 18/19 Scheduling & Concurrent Rendering

This part is important because now we're moving from **“how do I reduce the number of events?”** to:

> **“What happens when React has multiple pieces of work, and some work is more urgent than other work?”**

We'll build this from zero.

---

# 1. The Problem: Not All Updates Are Equally Important

Consider a search page:

```jsx
function SearchPage() {
  const [input, setInput] = useState("");
  const [results, setResults] = useState([]);

  return (
    <>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />

      <HugeResultList results={results} />
    </>
  );
}
```

When the user types:

```text
r
```

two things might need to happen:

```text
1. Update the input
2. Search/filter a huge list
```

But these aren't equally urgent.

### Input update

The user expects:

```text
type "r"
↓
input immediately shows "r"
```

This is **urgent**.

### Huge list update

The result list can potentially be updated slightly later.

This is **less urgent**.

So ideally:

```text
URGENT
↓
Input update

LESS URGENT
↓
Huge result list update
```

This is where React's scheduling capabilities become important.

---

# 2. What Is Scheduling?

### Definition

**Scheduling is the process of deciding when different pieces of work should be performed.**

Instead of thinking:

```text
Update happens
↓
Do everything immediately
```

React can conceptually think:

```text
I have multiple pieces of work.

Which one is more important?

Do urgent work first.
Do less urgent work when appropriate.
```

This is one of the major ideas behind React 18's concurrent features.

---

# 3. Important Clarification: Concurrent Rendering Does NOT Mean Parallel Rendering

This is a very common interview trap.

When we say React supports **concurrent rendering**, it does not mean:

```text
Component A → CPU core 1
Component B → CPU core 2
```

React's JavaScript work still generally runs on the JavaScript thread.

Instead, concurrency means React can conceptually:

> **Start rendering work, pause/yield it, and continue or abandon it later so more important work can be handled.**

Think:

```text
Low-priority work
       ↓
React starts it
       ↓
Higher-priority work appears
       ↓
React can prioritize the important work
       ↓
Continue low-priority work later
```

That's the important idea.

---

# 4. Why Was This Needed?

Imagine a huge list:

```text
10,000 items
```

Suppose changing a search input causes React to calculate a huge result tree.

Without prioritization:

```text
User types
   ↓
Huge rendering work
   ↓
Main thread busy
   ↓
Input feels delayed
```

The user experiences:

```text
"I typed, but the UI isn't responding."
```

The problem isn't necessarily that React can't calculate the list.

The problem is:

> **The expensive work is competing with urgent interaction work.**

---

# 5. React 18 Introduced Important Scheduling APIs

The major concepts we'll learn are:

```text
startTransition
useTransition
useDeferredValue
```

And React 19 builds on this model with concepts such as:

```text
Actions
useActionState
useOptimistic
useFormStatus
```

You've already learned some of these individually.

Now we're going to understand **why they exist and how they fit together**.

---

# 6. Urgent vs Non-Urgent Updates

React allows us to conceptually separate updates into:

### Urgent update

An update where the user expects immediate feedback.

Examples:

```text
Typing into input
Clicking a button
Opening a menu
Typing into a form
```

### Transition / non-urgent update

An update that can be interrupted or completed later without making the interface feel unresponsive.

Examples:

```text
Rendering a large search result
Switching a large tab
Filtering a huge dataset
Updating a complex visualization
```

So:

```text
User interaction
      ↓
┌──────────────────────┐
│ Urgent work          │
│ "Respond now"        │
└──────────────────────┘

      +

┌──────────────────────┐
│ Transition work      │
│ "Can happen later"   │
└──────────────────────┘
```

---

# 7. What Is a Transition?

### Definition

A **transition** tells React that certain state updates represent non-urgent UI work.

You're basically telling React:

> "This update matters, but keeping the interface responsive is more important than finishing this update immediately."

The API is:

```js
startTransition(() => {
  setSomething(...);
});
```

---

# 8. `startTransition`

Basic example:

```jsx
import { startTransition, useState } from "react";

function Search() {
  const [input, setInput] = useState("");
  const [results, setResults] = useState([]);

  function handleChange(e) {
    const value = e.target.value;

    setInput(value);

    startTransition(() => {
      setResults(searchHugeList(value));
    });
  }

  return (
    <>
      <input
        value={input}
        onChange={handleChange}
      />

      <ResultList results={results} />
    </>
  );
}
```

Now let's understand what this actually means.

---

# 9. What Does `startTransition()` Actually Do?

This:

```js
setInput(value);
```

is a normal state update.

This:

```js
startTransition(() => {
  setResults(...);
});
```

marks the update inside it as a **transition update**.

Conceptually:

```text
setInput
   ↓
Urgent update

setResults
   ↓
Transition / non-urgent update
```

React can then prioritize accordingly.

---

# 10. Think of a Queue

Imagine React has:

```text
URGENT WORK
────────────
Input update

TRANSITION WORK
───────────────
Huge search results
```

When the user types again:

```text
Input "r"
```

then:

```text
Input "re"
```

then:

```text
Input "rea"
```

React can prioritize the latest urgent interaction rather than blindly treating all rendering work equally.

Conceptually:

```text
Urgent
  ↓
Urgent
  ↓
Urgent
  ↓
Transition work
```

This helps maintain responsiveness.

---

# 11. Very Important: `startTransition` Does NOT Make Code Faster

This is one of the most important points.

Suppose:

```js
setResults(expensiveCalculation(data));
```

The calculation itself:

```js
expensiveCalculation(data)
```

still takes the time it takes.

`startTransition` doesn't magically turn:

```text
500ms calculation
```

into:

```text
5ms calculation
```

Instead, it changes the **priority/scheduling treatment of the resulting update**.

So:

```text
Optimization
≠
Make expensive calculation mathematically faster
```

Scheduling optimization is:

```text
Control when expensive work gets priority.
```

---

# 12. What Does "Interruptible" Mean?

Suppose React is working on a large update:

```text
Transition rendering
      ↓
Component A
Component B
Component C
Component D
...
```

Then an urgent update arrives:

```text
USER TYPES
```

React's concurrent rendering architecture can allow rendering work to be interrupted/yielded and the more important update to receive attention.

Conceptually:

```text
Transition work
   ↓
A
B
C
   ↓
URGENT UPDATE ARRIVES
   ↓
Pause/yield transition work
   ↓
Handle urgent work
   ↓
Continue/restart transition work if needed
```

This is why concurrent rendering is powerful.

---

# 13. Render Work Can Be Abandoned

This is another important internal concept.

Suppose React starts calculating:

```text
Old transition
```

Then something newer happens.

The previous work may no longer be useful.

React doesn't necessarily have to commit that unfinished result.

Conceptually:

```text
Render A starts
     ↓
Render A partially calculated
     ↓
New update arrives
     ↓
Render A becomes obsolete
     ↓
React can abandon it
```

This is possible because **rendering is separate from committing**.

Remember our earlier lesson:

```text
Render
  ↓
Reconciliation
  ↓
Commit
```

React can do rendering work without immediately changing the DOM.

That separation is extremely important for concurrency.

---

# 14. Why the Commit Phase Matters

React should not randomly leave the DOM half-updated.

So the high-level idea is:

```text
Render phase
→ can be interrupted/restarted

Commit phase
→ applies the final changes
```

This is one of the fundamental reasons React can support concurrent rendering.

Think:

```text
Render = "calculate what should happen"

Commit = "apply the final result"
```

---

# 15. `useTransition`

Now we have:

```js
startTransition(...)
```

But React also gives:

```js
const [isPending, startTransition] = useTransition();
```

### Definition

`useTransition` lets you mark updates as transitions **and gives you a pending state**.

Example:

```jsx
const [isPending, startTransition] = useTransition();
```

Then:

```jsx
startTransition(() => {
  setTab("posts");
});
```

And:

```jsx
{isPending && <p>Loading...</p>}
```

---

# 16. Why Do We Need `isPending`?

Suppose you click:

```text
Posts
```

and rendering the Posts screen takes some time.

We can tell the user:

```text
Posts
Loading...
```

while the transition is pending.

So:

```text
User clicks
   ↓
startTransition
   ↓
Transition begins
   ↓
isPending = true
   ↓
React works on transition
   ↓
Transition completes
   ↓
isPending = false
```

---

# 17. `startTransition` vs `useTransition`

This distinction is easy.

### `startTransition`

```js
startTransition(() => {
  setState(...);
});
```

Use it when you simply want to mark an update as a transition.

### `useTransition`

```js
const [isPending, startTransition] = useTransition();
```

Use it when you also want to know:

```text
Is this transition currently pending?
```

So:

```text
startTransition
    ↓
Mark update as transition

useTransition
    ↓
Mark update as transition
+
Track pending state
```

---

# 18. Example: Tab Switching

Imagine:

```jsx
function App() {
  const [tab, setTab] = useState("home");
  const [isPending, startTransition] = useTransition();

  function selectTab(nextTab) {
    startTransition(() => {
      setTab(nextTab);
    });
  }

  return (
    <>
      <button onClick={() => selectTab("home")}>
        Home
      </button>

      <button onClick={() => selectTab("posts")}>
        Posts
      </button>

      {isPending && <p>Loading...</p>}

      {tab === "home" && <Home />}
      {tab === "posts" && <Posts />}
    </>
  );
}
```

The important part:

```js
startTransition(() => {
  setTab(nextTab);
});
```

means:

> "Changing the displayed tab is allowed to be treated as non-urgent transition work."

---

# 19. Now `useDeferredValue`

This one is different.

### Definition

`useDeferredValue` lets you use a **deferred version of a value**, allowing React to prioritize the current value while updating the expensive consumer of that value later.

Example:

```jsx
const [query, setQuery] = useState("");

const deferredQuery = useDeferredValue(query);
```

Now you have:

```text
query
deferredQuery
```

They may temporarily differ.

---

# 20. Why Would They Differ?

Suppose:

```text
query = "react"
```

User types:

```text
react h
```

The immediate value can become:

```text
query = "react h"
```

while the deferred value might temporarily still be:

```text
deferredQuery = "react"
```

Later:

```text
deferredQuery = "react h"
```

So:

```text
Immediate value
     ↓
updates quickly

Deferred value
     ↓
may lag temporarily
```

That's the whole idea.

---

# 21. `useDeferredValue` Example

```jsx
function SearchPage() {
  const [query, setQuery] = useState("");

  const deferredQuery = useDeferredValue(query);

  return (
    <>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />

      <HugeResultList query={deferredQuery} />
    </>
  );
}
```

Now:

```text
User types
   ↓
query updates immediately
   ↓
input remains responsive

HugeResultList
   ↓
uses deferredQuery
   ↓
can update later
```

---

# 22. `startTransition` vs `useDeferredValue`

This is an important interview question.

### `startTransition`

You control the **state update**:

```js
startTransition(() => {
  setResults(results);
});
```

You're saying:

> "Treat this update as non-urgent."

### `useDeferredValue`

You already have a value and want a **lower-priority/deferred version** of it:

```js
const deferredQuery = useDeferredValue(query);
```

You're saying:

> "Let me use a version of this value that can lag behind."

---

# 23. Easy Way to Remember

```text
startTransition
     ↓
"I control the update."

useDeferredValue
     ↓
"I control which version of the value I consume."
```

Or:

```text
startTransition
→ defer UPDATE

useDeferredValue
→ defer VALUE
```

That's a very useful mental model.

---

# 24. A Real Search Example

Suppose:

```jsx
function Search() {
  const [query, setQuery] = useState("");

  const deferredQuery = useDeferredValue(query);

  return (
    <>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />

      <HugeSearchResults query={deferredQuery} />
    </>
  );
}
```

Flow:

```text
User types "r"
       ↓
query = "r"
       ↓
input updates
       ↓
deferredQuery eventually becomes "r"

User types "re"
       ↓
query = "re"
       ↓
input updates
       ↓
deferredQuery may still be "r"
       ↓
later becomes "re"
```

So the input remains responsive while the expensive consumer can lag.

---

# 25. Important: Deferred Does NOT Mean Debounced

These are completely different.

### Debounce

```text
wait for inactivity
```

### Deferred value

```text
allow a lower-priority render to lag behind
```

Example:

```text
Debounce:

r
re
rea
reac
react
↓
wait 500ms
↓
API call
```

Whereas:

```text
Deferred value:

query = react
↓
input responds immediately
↓
expensive result UI catches up later
```

No timer is involved in the basic concept of `useDeferredValue`.

This is a **React scheduling mechanism**, not a timer-based event-control mechanism.

---

# 26. This Difference Is Extremely Important

You could have:

```text
Debounce
+
useDeferredValue
```

in the same application because they solve different problems.

For example:

```text
User typing
      ↓
setQuery immediately
      ↓
debounce API request
      ↓
results arrive
      ↓
defer expensive rendering of result list
```

Different layers:

```text
Debounce
→ controls frequency of side effects

Transition/deferred value
→ controls rendering priority
```

---

# 27. React's Scheduling Mental Model

Now combine everything.

Suppose a user types.

```text
USER EVENT
    ↓
State update
    ↓
React schedules work
    ↓
┌──────────────────────────────┐
│ Urgent update                 │
│ Input                         │
└──────────────────────────────┘
             +
┌──────────────────────────────┐
│ Transition/deferred work      │
│ Huge result list              │
└──────────────────────────────┘
    ↓
React prioritizes work
    ↓
Render
    ↓
Possible interruption/restart
    ↓
Commit final result
```

---

# 28. Why Fiber Matters Here

Remember Fiber from Part 2.

A **Fiber** is React's internal representation of a unit of work/component in the tree.

Fiber architecture allows React to represent rendering work in smaller units.

Conceptually:

```text
Large render
    ↓
Fiber A
Fiber B
Fiber C
Fiber D
Fiber E
...
```

Because work is represented in units, React's scheduler can manage that work rather than treating the entire render as one indivisible operation.

This is part of what enables concurrent rendering behavior.

---

# 29. The Big Connection

Earlier we learned:

```text
React.memo
→ skip unnecessary rendering work
```

Now:

```text
startTransition
→ prioritize rendering work differently
```

And:

```text
useDeferredValue
→ allow expensive consumers to lag
```

So optimization has two broad strategies:

### Reduce work

```text
React.memo
useMemo
useCallback
state colocation
context optimization
```

### Schedule work better

```text
startTransition
useTransition
useDeferredValue
```

This distinction is very important.

---

# 30. Optimization vs Scheduling

Suppose rendering takes:

```text
100ms
```

### Memoization

May allow you to avoid that work entirely.

```text
100ms
↓
0ms
```

if the component can bail out.

### Scheduling

Doesn't necessarily make that 100ms disappear.

Instead:

```text
100ms work
↓
perform it as lower-priority work
↓
keep urgent interaction responsive
```

So:

> **Memoization reduces unnecessary work. Scheduling controls when work gets priority.**

Excellent interview statement.

---

# 31. `useTransition` Is Not a Loading API

This is another common mistake.

People see:

```js
const [isPending, startTransition] = useTransition();
```

and think:

> "This is another way to fetch data."

No.

`useTransition` is fundamentally about **marking state updates as transitions and exposing pending state**.

It doesn't automatically:

```text
fetch API
manage cache
cancel API
```

Those are separate concerns.

---

# 32. `startTransition` Is Not Debouncing

This is another interview trap.

```js
startTransition(() => {
  setQuery(value);
});
```

doesn't mean:

```text
wait 500ms
```

There is no debounce timer.

Instead:

```text
startTransition
→ scheduling priority

debounce
→ time-based event control
```

---

# 33. What About React 19 Actions?

Now we can connect React 19 to this model.

React 19 introduced a broader concept of **Actions** around async transitions and form/state workflows.

The important conceptual progression is:

```text
React 18
↓
Transitions
↓
Non-urgent updates
↓
React 19
↓
Actions build more structured async workflows around transitions
```

You've already learned:

```text
useActionState
useOptimistic
useFormStatus
```

Now you can understand why these APIs exist.

They are not random new hooks.

They're part of React's move toward:

```text
interactive UI
+
async state
+
pending state
+
optimistic state
+
transition-based updates
```

---

# 34. `useOptimistic` Connection

Suppose user likes a post.

Without optimistic UI:

```text
Click Like
   ↓
API request
   ↓
wait
   ↓
server responds
   ↓
update UI
```

The UI feels delayed.

With optimistic UI:

```text
Click Like
   ↓
Immediately show Like
   ↓
API request
   ↓
success → keep it
failure → rollback
```

This is another example of improving perceived responsiveness.

So our optimization concepts now span:

```text
Memoization
→ reduce work

State architecture
→ reduce update scope

Event optimization
→ reduce frequency

Scheduling
→ prioritize work

Optimistic UI
→ improve perceived latency
```

---

# 35. One Important Caveat About `startTransition`

You shouldn't blindly put every state update inside:

```js
startTransition(...)
```

For example, this generally doesn't make sense:

```js
startTransition(() => {
  setInputValue(e.target.value);
});
```

if the input is controlled and must update immediately.

You don't want the user's typing itself to become unnecessarily deferred.

Instead:

```text
Urgent:
setInputValue(value)

Non-urgent:
expensive result update
```

That's the pattern to remember.

---

# 36. When Should You Use What?

### Use debounce when:

```text
User generates many events
+
You only care about final activity
```

Examples:

```text
Search API
Autosave
Expensive filtering after typing
```

---

### Use throttle when:

```text
Events continue
+
You need periodic updates
```

Examples:

```text
Scroll tracking
Resize tracking
Mouse movement
```

---

### Use requestAnimationFrame when:

```text
You are doing visual updates
+
You want to align with browser rendering
```

Examples:

```text
Animation
Dragging
Scroll-linked visual movement
```

---

### Use `startTransition` when:

```text
An update is expensive
+
It is not urgent
+
You want React to prioritize urgent interactions
```

Examples:

```text
Large tab switch
Large filtering result
Complex UI update
```

---

### Use `useDeferredValue` when:

```text
You have an important immediate value
+
An expensive consumer can lag behind
```

Examples:

```text
Search input
Large result list
Complex visualization
```

---

# 37. The Complete Mental Model

You now have a much stronger optimization map:

```text
USER ACTION
     ↓
EVENT
     ↓
┌──────────────────────────────────┐
│ Is event happening too frequently?│
└──────────────────────────────────┘
       ↓                 ↓
      YES                NO
       ↓                 ↓
Debounce/Throttle      Normal handling
       │
       ↓
State update?
       ↓
┌───────────────────────────────┐
│ Is update expensive?          │
└───────────────────────────────┘
       ↓
      YES
       ↓
Can work be avoided?
       ↓
React.memo / useMemo / architecture
       ↓
If work still needed:
       ↓
Can it be non-urgent?
       ↓
startTransition / useTransition
       ↓
Can consumer safely lag?
       ↓
useDeferredValue
       ↓
Render
       ↓
Reconciliation
       ↓
Commit
```

That is the optimization thinking you should develop.

---

# 38. Interview Question: What is Concurrent Rendering?

A strong answer:

> **Concurrent rendering is a React capability that allows rendering work to be scheduled, interrupted, restarted, or abandoned so that more important updates can receive priority. It does not mean React renders components in parallel on multiple threads. The main benefit is maintaining UI responsiveness when there is expensive rendering work.**

---

# 39. Interview Question: What Does `startTransition` Do?

> **`startTransition` marks state updates as non-urgent transition updates. React can prioritize more urgent interactions, such as typing or clicking, over that transition work. It doesn't make the underlying computation faster; it changes how React schedules the resulting rendering work.**

---

# 40. Interview Question: Difference Between `startTransition` and `useDeferredValue`?

> **`startTransition` is used when I control the state update and want to mark that update as non-urgent. `useDeferredValue` is used when I already have a value and want a deferred version of that value so that expensive consumers can lag behind the immediate value.**

---

# 41. Interview Question: Does `startTransition` Make React Faster?

The correct answer:

> **Not necessarily. It doesn't reduce the computational cost of the update itself. Instead, it allows React to schedule that work as non-urgent, so urgent interactions can remain responsive.**

---

# 42. Interview Question: Does `useDeferredValue` Debounce a Value?

**No.**

Correct answer:

> **No. `useDeferredValue` is not debouncing. Debouncing waits for a period of inactivity using a time-based mechanism. `useDeferredValue` allows React to defer rendering work associated with a value according to its scheduling model. The deferred value may temporarily lag behind the current value.**

---

# 43. The Most Important Things to Remember From Part 11

```text
Concurrent rendering
→ React can manage rendering work so urgent work gets priority.

startTransition
→ mark a state update as non-urgent.

useTransition
→ start a transition + know whether it is pending.

useDeferredValue
→ create a value that can temporarily lag behind.

Debounce
→ wait until activity stops.

Throttle
→ limit execution frequency.

requestAnimationFrame
→ synchronize visual work with browser frames.
```

And the deepest distinction:

```text
React.memo / useMemo
        ↓
REDUCE unnecessary work

Debounce / Throttle
        ↓
REDUCE frequency of work

startTransition / useDeferredValue
        ↓
CONTROL PRIORITY of work

requestAnimationFrame
        ↓
ALIGN visual work with browser rendering
```

That distinction is the real **Part 11 takeaway**.

# PART 12 — API & Network Optimization in React

Now we move from **optimizing React rendering** to optimizing something equally important:

> **How efficiently your application communicates with the backend.**

A React app can have perfectly optimized components and still feel slow because it makes too many API calls, downloads too much data, waits unnecessarily, or repeats the same requests.

We'll start from zero and build all the way to advanced concepts.

---

# 1. First Understand the API Request Lifecycle

Suppose you have:

```jsx
useEffect(() => {
  fetch("/api/products");
}, []);
```

A simplified flow is:

```text
React Component
      ↓
fetch()
      ↓
Browser creates HTTP request
      ↓
Network
      ↓
Server
      ↓
Database / backend logic
      ↓
Server creates response
      ↓
Network
      ↓
Browser receives response
      ↓
JavaScript processes response
      ↓
setState()
      ↓
React render
      ↓
Commit
      ↓
UI
```

So API performance isn't just:

```text
"How fast is fetch?"
```

There are many stages.

---

# 2. What Makes an API Call Expensive?

An API request can involve:

```text
DNS lookup
   ↓
Connection
   ↓
TLS
   ↓
Request
   ↓
Server processing
   ↓
Database query
   ↓
Response
   ↓
Network transfer
   ↓
JSON parsing
   ↓
React state update
   ↓
Rendering
```

Therefore, when optimizing APIs, we can optimize several things:

```text
1. Number of requests
2. Duplicate requests
3. Amount of data
4. Request timing
5. Caching
6. Cancellation
7. Pagination
8. Prefetching
9. Parallel requests
10. Optimistic updates
```

---

# 3. The First Rule: Don't Make Unnecessary API Calls

Consider:

```jsx
useEffect(() => {
  fetchUsers();
});
```

What's wrong?

There is no dependency array.

So the effect can run after renders repeatedly.

Potentially:

```text
Render
 ↓
API
 ↓
setState
 ↓
Render
 ↓
API
 ↓
setState
 ↓
Render
 ↓
API
```

This can become disastrous.

Usually:

```jsx
useEffect(() => {
  fetchUsers();
}, []);
```

means:

```text
Initial mount
    ↓
API request
```

---

# 4. But `[]` Doesn't Mean "API Only Once Forever"

It means the effect has no dependencies.

Also remember development behavior with React Strict Mode can cause effects to be invoked in ways that expose unsafe side effects during development.

So don't build an API architecture that assumes:

> "This code can literally never execute twice."

A robust application should handle duplicate requests appropriately.

This becomes important with caching and request deduplication.

---

# 5. What Is API Caching?

### Definition

**Caching means storing previously fetched data so that future requests can reuse that data instead of unnecessarily fetching the same resource again.**

Imagine:

```text
GET /products
```

returns:

```json
[
  { "id": 1, "name": "Laptop" },
  { "id": 2, "name": "Phone" }
]
```

Without caching:

```text
Component A → API
Component B → API
Component C → API
```

With caching:

```text
Component A
    ↓
API
    ↓
Cache

Component B
    ↓
Cache

Component C
    ↓
Cache
```

Huge difference.

---

# 6. Why Is Caching Important?

Suppose an API takes:

```text
300ms
```

and you call it 10 times unnecessarily.

You're creating:

```text
10 × network work
10 × server work
10 × response processing
```

Caching can turn:

```text
10 requests
```

into:

```text
1 request + 9 cache reads
```

---

# 7. React Does Not Automatically Cache Fetch Results

This is important.

If you write:

```js
fetch("/api/products");
```

React doesn't automatically say:

> "I've already requested this, so I'll reuse it."

You need a caching strategy.

Possible approaches include:

```text
Browser HTTP cache
In-memory cache
localStorage
sessionStorage
IndexedDB
TanStack Query
SWR
Redux-based caching
Custom cache
```

Each has different behavior.

---

# 8. Browser HTTP Cache vs Application Cache

These are not the same.

### Browser HTTP cache

The browser can cache resources based on HTTP caching headers.

For example:

```text
Cache-Control
ETag
Last-Modified
Expires
```

The browser can potentially reuse the response without your React code manually managing it.

---

### Application-level cache

Your React application or data library stores data.

Conceptually:

```js
cache.set("/products", products);
```

Then:

```js
const products = cache.get("/products");
```

Libraries such as TanStack Query provide sophisticated application-level caching.

We'll reach that later.

---

# 9. What Is Request Deduplication?

This is extremely important.

### Definition

**Request deduplication means preventing multiple identical requests from being sent when the same request is already in progress.**

Imagine:

```text
Component A → GET /users
Component B → GET /users
Component C → GET /users
```

All three render around the same time.

Without deduplication:

```text
GET /users
GET /users
GET /users
```

With deduplication:

```text
GET /users
    ↓
request in progress
    ↓
A ─┐
B ─┼→ share same request/result
C ─┘
```

Only one network request is needed.

---

# 10. Cache vs Deduplication

Very important distinction.

### Cache

Deals with:

> **"Do we already have the data?"**

```text
Request
 ↓
Cache has data?
 ↓
YES → use cached data
```

### Deduplication

Deals with:

> **"Is someone already requesting this data?"**

```text
Request
 ↓
Same request already running?
 ↓
YES → reuse existing request
```

So:

```text
Cache
→ reuse completed data

Deduplication
→ reuse in-flight request
```

---

# 11. In-Flight Request

### Definition

An **in-flight request** is a network request that has started but hasn't finished yet.

Example:

```text
GET /products
       ↓
Request sent
       ↓
[ IN FLIGHT ]
       ↓
Response
```

If another component asks for the exact same resource while it's in flight:

```text
Component A
    ↓
GET /products
    ↓
IN FLIGHT

Component B
    ↓
GET /products
```

Instead of creating another request, we can allow B to subscribe to the existing request.

---

# 12. Why Duplicate API Calls Are Bad

They cause:

```text
Network usage ↑
Server load ↑
Battery usage ↑
Processing ↑
Potential race conditions ↑
Loading states become complicated
```

And in React:

```text
API response
 ↓
setState
 ↓
render
```

Multiple unnecessary responses can also cause unnecessary state updates.

---

# 13. Request Cancellation

Now suppose the user searches:

```text
react
```

You send:

```text
GET /search?q=react
```

Then immediately changes it to:

```text
react hooks
```

You don't care about the old request anymore.

Ideally:

```text
GET /search?q=react
        ↓
CANCEL
```

Then:

```text
GET /search?q=react hooks
```

This is where `AbortController` comes in.

---

# 14. What Is `AbortController`?

### Definition

`AbortController` is a browser API that lets you signal that an asynchronous operation, such as a `fetch`, should be aborted.

Example:

```js
const controller = new AbortController();

fetch("/api/users", {
  signal: controller.signal
});

controller.abort();
```

Flow:

```text
Create controller
      ↓
Pass signal to fetch
      ↓
Request starts
      ↓
controller.abort()
      ↓
Fetch receives abort signal
      ↓
Request is aborted
```

---

# 15. React Search + AbortController

A strong pattern:

```jsx
useEffect(() => {
  const controller = new AbortController();

  async function fetchResults() {
    try {
      const response = await fetch(
        `/api/search?q=${search}`,
        {
          signal: controller.signal
        }
      );

      const data = await response.json();

      setResults(data);
    } catch (error) {
      if (error.name === "AbortError") {
        return;
      }

      console.error(error);
    }
  }

  fetchResults();

  return () => {
    controller.abort();
  };
}, [search]);
```

Now let's understand the flow.

---

# 16. Internal Flow of AbortController

First render:

```text
search = "react"
```

Effect:

```text
Create Controller A
      ↓
Fetch "react"
      ↓
Request A in flight
```

User changes search:

```text
search = "react hooks"
```

React runs cleanup for previous effect:

```text
cleanup
 ↓
controllerA.abort()
```

Therefore:

```text
Request A ❌ aborted
```

Then new effect:

```text
Create Controller B
      ↓
Fetch "react hooks"
```

So:

```text
react request       ❌ cancelled
react hooks request ✅ active
```

This is much safer.

---

# 17. Why Cancellation Matters

Without cancellation:

```text
react
react h
react ho
react hoo
react hooks
```

You could have:

```text
5 requests in flight
```

With cancellation:

```text
react       ❌
react h     ❌
react ho    ❌
react hoo   ❌
react hooks ✅
```

This reduces unnecessary work.

---

# 18. Cancellation and Debouncing Are Different

Don't confuse them.

### Debounce

Controls **when you start** the request.

```text
Typing
 ↓
wait
 ↓
start API
```

### AbortController

Controls **whether an existing request should continue**.

```text
API started
 ↓
new search
 ↓
cancel old API
```

They can work together:

```text
Typing
 ↓
Debounce
 ↓
API request
 ↓
new search
 ↓
Abort old request
```

Excellent architecture.

---

# 19. Pagination

Suppose your database contains:

```text
1,000,000 products
```

Would you fetch all one million?

Obviously no.

Instead:

```text
Page 1 → 20 products
Page 2 → next 20
Page 3 → next 20
```

### Definition

**Pagination means dividing a large dataset into smaller pages and fetching only the portion needed at a time.**

---

# 20. Why Pagination Improves Performance

Without pagination:

```text
1,000,000 records
 ↓
huge response
 ↓
large JSON parsing
 ↓
large memory usage
 ↓
large rendering workload
```

With pagination:

```text
20 records
 ↓
small response
 ↓
small parsing
 ↓
small memory usage
 ↓
small rendering workload
```

This improves:

```text
Network
Memory
Parsing
Rendering
Initial load time
```

---

# 21. Offset Pagination

A common API might use:

```text
GET /products?page=1&limit=20
```

Then:

```text
GET /products?page=2&limit=20
```

Conceptually:

```text
Page 1
1–20

Page 2
21–40

Page 3
41–60
```

This is called **offset/page-based pagination**.

---

# 22. Cursor Pagination

Another approach is cursor-based pagination.

Example:

```text
GET /products?cursor=abc123
```

Server responds:

```json
{
  "data": [...],
  "nextCursor": "xyz789"
}
```

Then:

```text
GET /products?cursor=xyz789
```

This is useful for large or frequently changing datasets.

---

# 23. Offset vs Cursor

### Offset

```text
page=5
limit=20
```

Easy to understand.

Good for:

```text
Traditional tables
Admin dashboards
Simple pagination
```

### Cursor

```text
cursor=abc123
```

Often better for:

```text
Large datasets
Feeds
Infinite scrolling
Frequently changing data
```

---

# 24. Infinite Scroll

Instead of:

```text
Page 1
Page 2
Page 3
```

the user sees:

```text
Products
Products
Products
Products
       ↓
near bottom
       ↓
load more
       ↓
more products
```

This is infinite scrolling.

Flow:

```text
Initial data
    ↓
User scrolls
    ↓
Near bottom?
    ↓
YES
    ↓
Fetch next page
    ↓
Append results
```

But there's an optimization problem:

> Don't wait until the user literally reaches the bottom.

You can use an **IntersectionObserver** to detect when a sentinel element approaches the viewport.

We'll cover that when we reach advanced browser optimization.

---

# 25. Prefetching

### Definition

**Prefetching means fetching data before the user explicitly requests it because we predict they will need it soon.**

Example:

User is viewing:

```text
Home
```

You know they're likely to click:

```text
Products
```

You can start loading Products before they click.

```text
Home
 ↓
Prefetch Products
 ↓
User clicks Products
 ↓
Data already available / partially available
```

The perceived navigation becomes faster.

---

# 26. Prefetching vs Preloading

These are related but different.

### Prefetch

> "We may need this later."

### Preload

> "We definitely need this important resource soon."

Example:

```html
<link rel="prefetch" href="..." />
```

vs:

```html
<link rel="preload" href="..." />
```

We'll study browser resource prioritization later.

---

# 27. Parallel API Requests

Suppose you need:

```text
User
Orders
Notifications
```

If they don't depend on each other, don't necessarily do:

```js
const user = await fetchUser();
const orders = await fetchOrders();
const notifications = await fetchNotifications();
```

This creates:

```text
User
 ↓
Orders
 ↓
Notifications
```

Sequential requests.

Instead:

```js
const [user, orders, notifications] =
  await Promise.all([
    fetchUser(),
    fetchOrders(),
    fetchNotifications()
  ]);
```

Now conceptually:

```text
User ──────────┐
Orders ────────┼──→ results
Notifications ─┘
```

They can progress concurrently.

---

# 28. Why Parallel Requests Can Be Faster

Suppose:

```text
User = 300ms
Orders = 400ms
Notifications = 200ms
```

Sequential:

```text
300 + 400 + 200
= 900ms
```

Parallel:

```text
max(300, 400, 200)
≈ 400ms
```

assuming network/server conditions allow it.

Huge difference.

But don't parallelize requests that depend on each other.

---

# 29. Dependent Requests

Suppose:

```text
First get user ID
       ↓
Then get user's orders
```

You cannot do:

```js
Promise.all([
  fetchUser(),
  fetchOrders(userId)
]);
```

if `userId` doesn't exist yet.

You need:

```text
Get user
   ↓
Get user ID
   ↓
Get orders
```

So:

> **Parallelize independent requests; keep dependent requests sequential.**

Excellent interview point.

---

# 30. API Response Size

Suppose backend returns:

```json
{
  "id": 1,
  "name": "Nikitha",
  "email": "...",
  "address": "...",
  "phone": "...",
  "preferences": "...",
  "history": "...",
  "notifications": "...",
  "orders": [...]
}
```

But your screen only needs:

```text
id
name
email
```

Sending everything is wasteful.

Ideally the API should support:

```text
select=id,name,email
```

or a dedicated endpoint/response shape.

This is an architectural optimization:

> **Don't transfer data the client doesn't need.**

---

# 31. API Optimization Is Not Just Frontend Work

This is important for interviews.

Frontend developers can optimize:

```text
Caching
Deduplication
Cancellation
Pagination
Prefetching
Request timing
Rendering
```

But backend can optimize:

```text
Database indexes
Query optimization
Response compression
API response shape
Server caching
CDN
Connection pooling
```

So API performance is often:

```text
Frontend
+
Network
+
Backend
+
Database
```

---

# 32. Stale Data

Caching creates another problem.

Suppose cache contains:

```text
Products:
Laptop ₹50,000
```

But server now has:

```text
Laptop ₹45,000
```

Your cache is old.

This is called **stale data**.

### Definition

**Stale data is cached data that no longer represents the current server state.**

Therefore caching requires a policy.

For example:

```text
How long can this data be considered fresh?
```

---

# 33. TTL

### Definition

**TTL — Time To Live — defines how long cached data can be considered valid before it expires.**

Example:

```text
TTL = 5 minutes
```

Flow:

```text
10:00
 ↓
Fetch API
 ↓
Cache data

10:02
 ↓
Cache still fresh

10:04
 ↓
Cache still fresh

10:06
 ↓
Cache expired
 ↓
Fetch again
```

TTL is one simple caching strategy.

---

# 34. Cache Invalidation

This is a famous engineering problem.

You update:

```text
Product price
```

But cache still contains:

```text
Old price
```

You need to invalidate or update the cache.

Common strategies:

```text
Time-based expiration
Manual invalidation
Mutation-triggered invalidation
Versioned keys
Stale-while-revalidate
```

The famous phrase:

> **"There are only two hard things in Computer Science: cache invalidation and naming things..."**

You don't need to memorize the quote for interviews, but understand why cache invalidation is difficult.

---

# 35. Stale-While-Revalidate

This is a powerful caching pattern.

Conceptually:

```text
Cache has old data
       ↓
Show it immediately
       ↓
Fetch fresh data in background
       ↓
Update cache
       ↓
Update UI
```

So:

```text
User
 ↓
gets fast cached response
 ↓
background refresh
 ↓
fresh data
```

This gives:

```text
Fast perceived response
+
Eventually fresh data
```

Libraries like TanStack Query are very useful for these kinds of patterns.

---

# 36. Why TanStack Query Exists

You can manually build:

```text
loading
error
data
cache
retry
refetch
deduplication
stale time
pagination
infinite queries
mutation handling
```

But this becomes a lot of code.

TanStack Query gives a server-state management system around these concerns.

Conceptually:

```text
Component
   ↓
useQuery()
   ↓
Query cache
   ↓
API
```

Then another component asking for the same query can potentially use the cached/in-flight data.

We'll do a **deep TanStack Query section later**, because it's a major optimization/state-management topic.

---

# 37. Client State vs Server State

This distinction is extremely important.

### Client state

State primarily owned by the UI:

```text
Modal open/closed
Selected tab
Theme
Input value
Sidebar open
```

### Server state

Data owned by the backend:

```text
Users
Products
Orders
Claims
Appointments
```

Server state has special problems:

```text
Fetching
Caching
Stale data
Synchronization
Retries
Pagination
Refetching
Deduplication
```

That's why tools like TanStack Query exist.

---

# 38. The API Optimization Hierarchy

When you have a slow API-driven page, think in this order:

```text
1. Am I making unnecessary requests?
        ↓
2. Are requests duplicated?
        ↓
3. Can I cache?
        ↓
4. Can I cancel obsolete requests?
        ↓
5. Can independent requests run in parallel?
        ↓
6. Can I reduce response size?
        ↓
7. Can I paginate?
        ↓
8. Can I prefetch predictable data?
        ↓
9. Can I optimize rendering of the received data?
```

This is much better than blindly adding `useMemo`.

---

# 39. A Complete Search Architecture

Let's combine everything.

User searches products.

### Step 1 — Immediate UI

```text
User types
 ↓
setSearch()
 ↓
input updates immediately
```

### Step 2 — Debounce

```text
wait until typing pauses
```

### Step 3 — Cancel previous request

```text
AbortController
```

### Step 4 — API

```text
GET /products?q=react
```

### Step 5 — Cache

```text
Does cache contain this query?
```

If yes:

```text
use cached data
```

If no:

```text
request server
```

### Step 6 — Response

```text
server
 ↓
data
 ↓
cache
 ↓
React
```

### Step 7 — Expensive rendering

If the result list is huge:

```text
useDeferredValue
```

or:

```text
startTransition
```

may help prioritize rendering.

So the complete architecture becomes:

```text
                USER TYPES
                    ↓
              setSearch()
                    ↓
              Immediate UI
                    ↓
                 Debounce
                    ↓
             Check cache
               ↙       ↘
           HIT           MISS
            ↓              ↓
        use data       API request
                           ↓
                    Abort old request
                           ↓
                        Response
                           ↓
                         Cache
                           ↓
                    React rendering
                           ↓
                 Deferred/optimized UI
```

That's a real-world performance architecture.

---

# 40. Interview-Ready Answers

### What is API caching?

> API caching is the process of storing previously fetched server data so that subsequent requests can reuse that data instead of repeatedly fetching the same resource.

### What is request deduplication?

> Request deduplication prevents multiple identical requests from being sent when the same request is already in progress. Multiple consumers can share the same in-flight request or its result.

### What is AbortController?

> AbortController is a browser API that allows us to signal cancellation of asynchronous operations such as fetch requests. In React, it's useful for cancelling obsolete requests when dependencies change or when a component unmounts.

### Why use pagination?

> Pagination prevents the client from downloading and rendering a very large dataset at once. It reduces network transfer, memory usage, JSON parsing, and rendering work.

### What is prefetching?

> Prefetching means fetching data before the user explicitly requests it, based on the expectation that the data will be needed soon. It can reduce perceived navigation latency.

### Why use Promise.all?

> Promise.all allows independent asynchronous operations to progress concurrently rather than waiting for each one sequentially. It's useful when requests don't depend on each other's results.

---

# 41. The Most Important Mental Model

Don't think:

> **"API optimization = make fetch faster."**

Think:

```text
API OPTIMIZATION
       │
       ├── Fewer requests
       │
       ├── No duplicate requests
       │
       ├── Cache reusable data
       │
       ├── Cancel obsolete requests
       │
       ├── Parallelize independent requests
       │
       ├── Reduce response size
       │
       ├── Pagination
       │
       ├── Prefetch predictable data
       │
       └── Optimize rendering after data arrives
```

And the deepest connection to everything we've learned:

```text
                 PERFORMANCE
                      │
       ┌──────────────┼──────────────┐
       ↓              ↓              ↓
   EVENT WORK      NETWORK WORK    RENDER WORK
       │              │              │
 debounce          caching        React.memo
 throttle          dedupe         useMemo
 rAF               abort          useCallback
                   pagination      state colocation
                   prefetch       Context optimization
                                  transitions
                                  deferred values
```

So far, we've learned how to optimize **three major sources of work**:

> **Event optimization → control how frequently work starts.**

> **API optimization → control how much network/server work happens.**

> **React optimization → control how much rendering work happens.**

The next major section is **Part 13 — Code Splitting & Bundle Optimization**, where we'll go inside **JavaScript bundles, `React.lazy`, `Suspense`, dynamic imports, tree shaking, chunking, route-based splitting, and why downloading less JavaScript can dramatically improve initial load performance.**