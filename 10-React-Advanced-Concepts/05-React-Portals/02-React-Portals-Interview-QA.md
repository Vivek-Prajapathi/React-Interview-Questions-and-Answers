# React Portals — Interview Questions and Answers

## Clickable Table of Contents

1. [What is a React Portal?](#1-what-is-a-react-portal)
2. [Why do we use portals?](#2-why-do-we-use-portals)
3. [Which API creates a portal?](#3-which-api-creates-a-portal)
4. [What is the syntax of createPortal?](#4-what-is-the-syntax-of-createportal)
5. [Does a portal change the React component tree?](#5-does-a-portal-change-the-react-component-tree)
6. [What is the difference between the React tree and DOM tree?](#6-what-is-the-difference-between-the-react-tree-and-dom-tree)
7. [What are common use cases for portals?](#7-what-are-common-use-cases-for-portals)
8. [How do events bubble through portals?](#8-how-do-events-bubble-through-portals)
9. [Does a portal support React context?](#9-does-a-portal-support-react-context)
10. [Can a portal render outside the root DOM element?](#10-can-a-portal-render-outside-the-root-dom-element)
11. [Does createPortal create a new React root?](#11-does-createportal-create-a-new-react-root)
12. [What happens if the target DOM node is missing?](#12-what-happens-if-the-target-dom-node-is-missing)
13. [How do you create a modal using a portal?](#13-how-do-you-create-a-modal-using-a-portal)
14. [How do you close a modal when the backdrop is clicked?](#14-how-do-you-close-a-modal-when-the-backdrop-is-clicked)
15. [How do you prevent clicks inside a modal from closing it?](#15-how-do-you-prevent-clicks-inside-a-modal-from-closing-it)
16. [How can you close a portal modal with Escape?](#16-how-can-you-close-a-portal-modal-with-escape)
17. [Does a portal solve every z-index issue?](#17-does-a-portal-solve-every-z-index-issue)
18. [Does CSS inheritance work the same way in a portal?](#18-does-css-inheritance-work-the-same-way-in-a-portal)
19. [Does a portal provide accessibility automatically?](#19-does-a-portal-provide-accessibility-automatically)
20. [What accessibility features should a modal provide?](#20-what-accessibility-features-should-a-modal-provide)
21. [What is focus trapping?](#21-what-is-focus-trapping)
22. [Why should focus return to the trigger after closing a modal?](#22-why-should-focus-return-to-the-trigger-after-closing-a-modal)
23. [How do you clean up portal content?](#23-how-do-you-clean-up-portal-content)
24. [Should we manually remove portal children from the DOM?](#24-should-we-manually-remove-portal-children-from-the-dom)
25. [Can portals be used with SSR?](#25-can-portals-be-used-with-ssr)
26. [Why should document not be accessed during server rendering?](#26-why-should-document-not-be-accessed-during-server-rendering)
27. [How do you resolve a portal host after mounting?](#27-how-do-you-resolve-a-portal-host-after-mounting)
28. [Can a portal be conditionally rendered?](#28-can-a-portal-be-conditionally-rendered)
29. [Can a portal contain stateful components?](#29-can-a-portal-contain-stateful-components)
30. [Can multiple portals use the same host node?](#30-can-multiple-portals-use-the-same-host-node)
31. [Can one component render multiple portals?](#31-can-one-component-render-multiple-portals)
32. [What is the purpose of the optional key argument?](#32-what-is-the-purpose-of-the-optional-key-argument)
33. [What is the difference between a portal and a fragment?](#33-what-is-the-difference-between-a-portal-and-a-fragment)
34. [What is the difference between a portal and createRoot?](#34-what-is-the-difference-between-a-portal-and-createroot)
35. [When should you avoid portals?](#35-when-should-you-avoid-portals)
36. [How do you test portal content?](#36-how-do-you-test-portal-content)
37. [Why might a test not find portal content inside the render container?](#37-why-might-a-test-not-find-portal-content-inside-the-render-container)
38. [How do you test backdrop behavior?](#38-how-do-you-test-backdrop-behavior)
39. [What is a common event propagation mistake with portals?](#39-what-is-a-common-event-propagation-mistake-with-portals)
40. [Can stopPropagation be used inside portal content?](#40-can-stoppropagation-be-used-inside-portal-content)
41. [What happens when the component rendering a portal unmounts?](#41-what-happens-when-the-component-rendering-a-portal-unmounts)
42. [Can a portal access providers above it?](#42-can-a-portal-access-providers-above-it)
43. [Can a portal escape overflow hidden?](#43-can-a-portal-escape-overflow-hidden)
44. [What is a stacking context?](#44-what-is-a-stacking-context)
45. [Why is a body-level portal host commonly used?](#45-why-is-a-body-level-portal-host-commonly-used)
46. [How do you handle a missing portal host safely?](#46-how-do-you-handle-a-missing-portal-host-safely)
47. [What is the difference between event.target and event.currentTarget?](#47-what-is-the-difference-between-eventtarget-and-eventcurrenttarget)
48. [How can event.target prevent accidental modal closing?](#48-how-can-eventtarget-prevent-accidental-modal-closing)
49. [Does a portal affect component state ownership?](#49-does-a-portal-affect-component-state-ownership)
50. [What are the most important portal best practices?](#50-what-are-the-most-important-portal-best-practices)
51. [What does this portal code render?](#51-what-does-this-portal-code-render)
52. [Will the parent click handler run in this example?](#52-will-the-parent-click-handler-run-in-this-example)
53. [How would you implement a reusable Portal component?](#53-how-would-you-implement-a-reusable-portal-component)
54. [How would you debug a portal that is not visible?](#54-how-would-you-debug-a-portal-that-is-not-visible)
55. [Can portal content inherit CSS variables from its original parent?](#55-can-portal-content-inherit-css-variables-from-its-original-parent)
56. [Are portals only useful for modals?](#56-are-portals-only-useful-for-modals)
57. [What is the relationship between portals and React reconciliation?](#57-what-is-the-relationship-between-portals-and-react-reconciliation)
58. [What should you consider when rendering a portal in a list?](#58-what-should-you-consider-when-rendering-a-portal-in-a-list)
59. [What is the biggest misconception about portals?](#59-what-is-the-biggest-misconception-about-portals)
60. [Explain React Portals in an interview.](#60-explain-react-portals-in-an-interview)

---

## 1. What is a React Portal?

A React Portal is a feature that allows me to render a component's children into a DOM node outside their normal DOM hierarchy. It is useful when UI elements such as modals or tooltips need to escape layout constraints of their parent.

## 2. Why do we use portals?

I use portals when a component needs to appear outside its parent's DOM structure. For example, a modal inside a container with `overflow: hidden` may get clipped. Rendering it into a top-level portal host avoids that particular DOM nesting problem.

## 3. Which API creates a portal?

React provides `createPortal`, which I import from `react-dom`.

```jsx
import { createPortal } from "react-dom";
```

## 4. What is the syntax of createPortal?

```jsx
createPortal(children, domNode, key);
```

The first argument is the React content, the second is the existing DOM destination, and the optional third argument is a key.

## 5. Does a portal change the React component tree?

No. A portal changes only where the content is placed in the DOM. The component remains in the same logical React tree, so context and React event relationships are preserved.

## 6. What is the difference between the React tree and DOM tree?

The React tree represents the logical component hierarchy managed by React. The DOM tree represents the actual HTML element hierarchy in the browser. A portal allows those two hierarchies to differ for a particular subtree.

## 7. What are common use cases for portals?

Common use cases include modal dialogs, tooltips, popovers, dropdown overlays, toast notifications, and context menus. These elements often need to escape clipping or stacking constraints.

## 8. How do events bubble through portals?

Events from portal content propagate according to the React component tree, not the physical DOM tree. Therefore, a click inside a portal can trigger an event handler on a React ancestor even if that ancestor is not a DOM ancestor.

## 9. Does a portal support React context?

Yes. A portal remains part of the same React tree, so it can access context values from providers above the component that rendered the portal.

## 10. Can a portal render outside the root DOM element?

Yes. I can render portal content into another DOM node, such as a sibling of the root element or a child of `document.body`, while keeping it part of the same React tree.

## 11. Does createPortal create a new React root?

No. `createPortal` does not create a separate React root. It renders content into another DOM node while preserving the existing React tree relationship.

## 12. What happens if the target DOM node is missing?

The portal has no valid destination. I should check that the target exists before calling `createPortal`, and handle the missing-node case safely rather than passing `null`.

## 13. How do you create a modal using a portal?

I create a portal host in the HTML, import `createPortal`, and return the modal JSX through it. I control visibility with state and pass `open` and `onClose` props to a reusable modal component.

```jsx
return open
  ? createPortal(<div role="dialog">Modal content</div>, portalRoot)
  : null;
```

## 14. How do you close a modal when the backdrop is clicked?

I attach the close handler to the backdrop. I make sure the dialog content does not trigger that backdrop handler, either by stopping propagation inside the dialog or by checking whether the event target is the backdrop itself.

## 15. How do you prevent clicks inside a modal from closing it?

I can call `event.stopPropagation()` on the dialog content's click handler. Another approach is to close only when `event.target === event.currentTarget` on the backdrop.

## 16. How can you close a portal modal with Escape?

I add a `keydown` listener while the modal is open, check whether `event.key` is `"Escape"`, and call the close callback. I remove the listener in the Effect cleanup.

## 17. Does a portal solve every z-index issue?

No. A portal can move content out of a problematic ancestor stacking context, but the portal destination and its ancestors can still create stacking contexts. I still need to understand the CSS stacking hierarchy.

## 18. Does CSS inheritance work the same way in a portal?

Not necessarily. The portaled element inherits CSS from its actual DOM ancestors at the destination, not from the DOM ancestors where it was originally declared in the React tree. React context is different and continues to work.

## 19. Does a portal provide accessibility automatically?

No. A portal only changes DOM placement. I still need to implement accessible dialog semantics, naming, keyboard interaction, focus management, and appropriate background interaction behavior.

## 20. What accessibility features should a modal provide?

A modal should have an accessible name, appropriate dialog semantics, focus moved inside on opening, keyboard focus contained while open, Escape behavior when appropriate, and focus restored to the trigger after closing. Background content should not remain interactable when the dialog is truly modal.

## 21. What is focus trapping?

Focus trapping keeps keyboard navigation within an open modal. When the user presses Tab or Shift+Tab, focus moves among the modal's focusable elements rather than moving into the page behind it.

## 22. Why should focus return to the trigger after closing a modal?

It preserves keyboard navigation and gives users a predictable place to continue from. After closing the dialog, focus should normally return to the element that opened it, if that element still exists and is available.

## 23. How do you clean up portal content?

When the component stops rendering the portal, React removes the portal's rendered children as part of normal rendering and unmounting. I clean up any additional resources I created, such as event listeners or a dynamically created host node.

## 24. Should we manually remove portal children from the DOM?

No. React should manage DOM nodes it rendered. Manually removing those nodes can conflict with React's reconciliation. I only manually remove a host element that my component itself created, with appropriate cleanup.

## 25. Can portals be used with SSR?

Yes, but browser DOM access must be handled carefully. The server does not have `document`, so I should resolve the portal host on the client or use a client-only rendering boundary.

## 26. Why should document not be accessed during server rendering?

Because `document` is a browser API and is not available in a normal server-rendering environment. Accessing it during server rendering can throw a reference error.

## 27. How do you resolve a portal host after mounting?

I can use `useEffect` to find the host after the component mounts, store it in state, and render the portal only after the host is available.

## 28. Can a portal be conditionally rendered?

Yes. I can return `null` when the modal is closed and call `createPortal` when it is open. This is a common way to control modal visibility.

## 29. Can a portal contain stateful components?

Yes. Components rendered through a portal can use state, Effects, refs, and other React features normally. A portal does not create a separate React application.

## 30. Can multiple portals use the same host node?

Yes. Multiple portal calls can target the same DOM node. React manages the portal content, although the application should structure and style the resulting content carefully.

## 31. Can one component render multiple portals?

Yes. A component can return multiple portals, for example one for a modal and another for a toast region, as long as each portal has a valid destination.

## 32. What is the purpose of the optional key argument?

The optional key helps React identify a portal when portals are rendered as part of a collection. It serves the same general identity purpose as keys for other elements in lists.

## 33. What is the difference between a portal and a fragment?

A Fragment groups multiple React elements without adding an extra DOM wrapper. A Portal renders children into a different DOM node. They solve different problems.

## 34. What is the difference between a portal and createRoot?

`createRoot` creates a new React root that manages a separate application entry point. `createPortal` renders content elsewhere in the DOM while keeping it inside the existing React tree.

## 35. When should you avoid portals?

I avoid portals when normal DOM placement works correctly. Portals add another DOM destination to reason about and can affect CSS inheritance and tests. They should solve an actual placement problem.

## 36. How do you test portal content?

I render the component and query the document for the portal content, preferably by accessible role and name. I also test interactions such as backdrop click, Escape, and focus behavior when implemented.

## 37. Why might a test not find portal content inside the render container?

Because the portal content is rendered into the separate target node, not necessarily inside the local container returned by the testing library. Document-level queries such as `screen` are usually appropriate.

## 38. How do you test backdrop behavior?

I click the backdrop and verify that the dialog closes. I separately click inside the dialog and verify that it remains open. This confirms that the two interactions are handled differently.

## 39. What is a common event propagation mistake with portals?

A common mistake is assuming that portal events follow the DOM tree. A React ancestor's handler can still run because event propagation follows the React tree.

## 40. Can stopPropagation be used inside portal content?

Yes. Calling `event.stopPropagation()` in a portal event handler stops that event from continuing to React ancestors. I use it only where that behavior is intended.

## 41. What happens when the component rendering a portal unmounts?

React unmounts the portal subtree and removes the DOM it rendered into the destination. Any external subscriptions or listeners created by the component still need proper cleanup.

## 42. Can a portal access providers above it?

Yes. Since the portal stays in the same React tree, it can access context providers above the component that creates the portal.

## 43. Can a portal escape overflow hidden?

Yes, if the portal is rendered into a destination outside the DOM ancestor applying `overflow: hidden`. The portal does not escape clipping if its destination is still inside the same clipping ancestor.

## 44. What is a stacking context?

A stacking context is a CSS concept that determines how elements are stacked along the z-axis. Certain CSS properties create independent stacking contexts, which can prevent a descendant's large `z-index` from appearing above elements outside that context.

## 45. Why is a body-level portal host commonly used?

A host near the document body is often outside layout containers that clip or constrain page content. This makes it a practical destination for modals and overlays, though its own stacking and styling still need to be considered.

## 46. How do you handle a missing portal host safely?

I check the result of `document.getElementById` and return `null` or show a controlled error if the host is missing. I also verify the HTML host ID and handle browser-only access correctly in SSR applications.

## 47. What is the difference between event.target and event.currentTarget?

`event.target` is the element where the event originated. `event.currentTarget` is the element whose handler is currently running.

## 48. How can event.target prevent accidental modal closing?

On the backdrop handler, I can check whether `event.target === event.currentTarget`. If they are equal, the user clicked the backdrop itself. If not, the click came from a descendant such as the dialog content.

## 49. Does a portal affect component state ownership?

No. State remains owned by the same React components. A parent can control whether portal content is rendered using props and state just like normally rendered children.

## 50. What are the most important portal best practices?

I use portals only when needed, keep the host predictable, let React manage portal DOM, clean up global listeners, implement complete accessibility behavior, and test the portal through document-level queries.

## 51. What does this portal code render?

```jsx
createPortal(<p>Hello</p>, document.getElementById("portal-root"));
```

It renders the paragraph inside the DOM element with the ID `portal-root`, assuming that element exists. It does not render the paragraph at the component's normal DOM location.

## 52. Will the parent click handler run in this example?

```jsx
function Parent() {
  return (
    <div onClick={() => console.log("Parent")}>
      {createPortal(
        <button onClick={() => console.log("Button")}>Click</button>,
        document.getElementById("portal-root")
      )}
    </div>
  );
}
```

Yes. Clicking the button logs `"Button"` and the React ancestor handler can also log `"Parent"`, because portal events propagate through the React tree.

## 53. How would you implement a reusable Portal component?

I would accept `children` and an optional target element or target ID, validate that the target exists, and return `createPortal(children, target)`. For SSR, I would resolve the target only on the client.

```jsx
import { createPortal } from "react-dom";

function Portal({ children, target }) {
  if (!target) return null;
  return createPortal(children, target);
}
```

## 54. How would you debug a portal that is not visible?

I would verify that the host element exists, inspect whether the portal content appears in that node, check CSS display and visibility, inspect stacking contexts, and check whether the portal is being conditionally rendered. I would also check browser console errors.

## 55. Can portal content inherit CSS variables from its original parent?

Not through the original DOM ancestry. CSS inheritance follows the actual DOM tree, so the portal content inherits variables from the portal destination's DOM ancestors. I can define shared variables on `:root` or the portal host.

## 56. Are portals only useful for modals?

No. They are also useful for tooltips, popovers, dropdown overlays, notifications, context menus, and other UI that needs a different DOM placement.

## 57. What is the relationship between portals and React reconciliation?

React continues to reconcile portal content as part of the existing component tree. React tracks the portal destination and its children, updating and removing them through its normal rendering lifecycle.

## 58. What should you consider when rendering a portal in a list?

I should provide stable keys so React can identify each portal across renders. I should also ensure each portal's content and destination are managed consistently.

## 59. What is the biggest misconception about portals?

The biggest misconception is that a portal creates a separate React tree. It does not. It changes DOM placement while preserving the component's React-tree relationship.

## 60. Explain React Portals in an interview.

React Portals allow us to render a component's children into a DOM node outside their normal DOM hierarchy. We use `createPortal` from `react-dom`. A common example is a modal that needs to escape a parent container with `overflow: hidden` or a stacking context.

The important point is that a portal changes the DOM placement but not the React component tree. Context is still available, and events bubble according to the React tree. Portals are useful for modals, tooltips, popovers, and notifications, but accessibility, focus management, keyboard behavior, and cleanup still need to be implemented.
