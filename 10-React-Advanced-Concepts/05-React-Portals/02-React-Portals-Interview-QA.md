# React Portals — Interview Questions and Answers

## Table of Contents
1. [What is a React Portal?](#q1-what-is-a-react-portal)
2. [Why do we use portals?](#q2-why-do-we-use-portals)
3. [Does a portal change the React component tree?](#q3-does-a-portal-change-the-react-component-tree)
4. [How does event bubbling work with portals?](#q4-how-does-event-bubbling-work-with-portals)
5. [Does a portal automatically make a modal accessible?](#q5-does-a-portal-automatically-make-a-modal-accessible)
6. [Portal vs createRoot?](#q6-portal-vs-createroot)
7. [What happens if the portal target does not exist?](#q7-what-happens-if-the-portal-target-does-not-exist)
8. [Can Context be used inside a portal?](#q8-can-context-be-used-inside-a-portal)
9. [Why are portals useful for tooltips?](#q9-why-are-portals-useful-for-tooltips)
10. [What should a production modal handle?](#q10-what-should-a-production-modal-handle)

## Q1. What is a React Portal?
**Interview answer:** A portal allows React to render a component's children into a DOM node outside the component's normal DOM parent. I commonly use it for modals, tooltips, and overlays.

## Q2. Why do we use portals?
**Interview answer:** We use portals when an element needs to escape layout constraints such as `overflow: hidden`, stacking contexts, or ancestor positioning. A modal rendered near the document body is a common example.

## Q3. Does a portal change the React component tree?
**Interview answer:** No. It changes the DOM placement, but the component remains in the same React tree. It still receives Context and follows React's component relationships.

## Q4. How does event bubbling work with portals?
**Interview answer:** React events from portal content bubble according to the React tree. So a click inside a portal can reach a React parent even if that parent is not a DOM ancestor of the portal node.

## Q5. Does a portal automatically make a modal accessible?
**Interview answer:** No. A portal only controls DOM placement. I still need to manage the dialog's accessible name and role, focus entry, focus trapping, Escape behavior, focus restoration, and background interaction.

## Q6. Portal vs createRoot?
**Interview answer:** A portal renders into another DOM location while staying in the same React tree. `createRoot` creates a separate React root, so it has separate provider ancestry and root lifecycle.

## Q7. What happens if the portal target does not exist?
**Interview answer:** `createPortal` needs a valid DOM target. I make sure the target exists before rendering and handle a missing target intentionally. In SSR, I also avoid accessing `document` on the server.

## Q8. Can Context be used inside a portal?
**Interview answer:** Yes. Because the portal remains in the same React tree, it can read Context providers above the component that created it, even though its DOM is mounted elsewhere.

## Q9. Why are portals useful for tooltips?
**Interview answer:** A tooltip inside a container with clipping or a restrictive stacking context may be cut off. Rendering it through a portal near the document body lets it escape those DOM layout constraints.

## Q10. What should a production modal handle?
**Interview answer:** Beyond rendering through a portal, it should have correct dialog semantics, move focus inside, keep keyboard focus within the dialog, support Escape when appropriate, restore focus to the trigger, prevent background interaction, and clean up listeners and scroll locks.
