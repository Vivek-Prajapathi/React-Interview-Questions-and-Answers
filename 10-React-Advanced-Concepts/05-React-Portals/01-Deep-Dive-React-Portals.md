# React Portals

## 1. What is a portal?

A portal lets a component render its children into a DOM node that exists outside the component's normal DOM parent. It is useful when UI must escape an ancestor's clipping or stacking context, especially for modals, dialogs, tooltips, popovers, and global notifications.

```jsx
import { createPortal } from "react-dom";

function Modal({ children }) {
  const modalRoot = document.getElementById("modal-root");
  if (!modalRoot) return null;

  return createPortal(
    <div className="modal-backdrop">
      <section className="modal">{children}</section>
    </div>,
    modalRoot
  );
}
```

The HTML document can contain:

```html
<div id="root"></div>
<div id="modal-root"></div>
```

## 2. DOM tree vs React tree

A portal changes where DOM nodes are placed, but it does not change their position in the React component tree.

Consequences:
- Context remains available through the React tree.
- React event propagation follows the React tree, not simply the DOM ancestry.
- Parent component state and props continue to work normally.

This is a frequent interview distinction.

## 3. Event propagation

If a click occurs inside a portal, React events can bubble to React ancestors even though the portal DOM node is mounted elsewhere. This can surprise developers who expect DOM ancestry to determine all event behavior.

Stop propagation only when the interaction design requires it; do not assume a portal automatically isolates events.

## 4. Modal responsibilities

A portal only changes DOM placement. It does not automatically provide a complete accessible modal. A production modal should consider:
- An accessible dialog role and accessible name.
- Focus moved into the dialog when opened.
- Focus contained within the dialog while modal.
- Escape-key behavior when appropriate.
- Focus restored to the trigger when closed.
- Background interaction prevented while modal.
- Scroll locking where appropriate.
- Correct cleanup on close and unmount.

Use a well-tested dialog library for complex production requirements unless implementing and testing the accessibility behavior is part of the task.

## 5. Portal target lifecycle

The target DOM node must exist when `createPortal` is called. A missing target should be handled intentionally. In server-rendered applications, direct `document` access must not happen during server rendering; access browser-only DOM nodes after the client has mounted or use an SSR-safe strategy.

## 6. Portal vs separate React root

A portal remains part of the same React tree and shares Context and event semantics. A separate `createRoot` creates another React root, which is a distinct tree with separate provider ancestry unless explicitly wired.

## 7. Common use cases

- Modal dialogs.
- Tooltips that must escape `overflow: hidden`.
- Dropdown menus near the document body.
- Toast notifications.
- Floating overlays.

## 8. Common mistakes

- Assuming a portal creates a separate React tree.
- Assuming React events stop bubbling at the portal boundary.
- Believing portals automatically manage focus or accessibility.
- Accessing `document` during server rendering.
- Forgetting to remove listeners or restore focus.
