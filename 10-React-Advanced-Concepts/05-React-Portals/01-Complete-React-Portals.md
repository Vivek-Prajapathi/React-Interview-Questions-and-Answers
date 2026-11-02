# React Portals — Complete Concepts Guide

## Learning Order

1. Understand the problem portals solve.
2. Learn `createPortal` and its syntax.
3. Understand the difference between the React tree and DOM tree.
4. Learn event bubbling through portals.
5. Build practical portal-based UI such as modals and tooltips.
6. Handle accessibility, focus, keyboard interactions, and cleanup.
7. Understand common mistakes, testing, and best practices.

---

## 1. What Is a React Portal?

A React Portal lets a component render its children into a DOM node that exists outside the component's normal DOM hierarchy.

Normally, React renders a component's JSX inside the DOM element associated with its parent. Sometimes this creates layout or stacking problems. For example, a modal rendered inside a container with `overflow: hidden` may be clipped. A parent with a CSS `transform`, `filter`, or certain stacking contexts can also affect positioning.

Portals solve this by allowing the UI to be placed elsewhere in the DOM while keeping it part of the same React component tree.

**Interview explanation:** “A portal allows me to render a React component into a different DOM node without changing its position in the React component tree. It is useful for modals, tooltips, dropdown overlays, and other UI that needs to escape a parent container's layout or stacking constraints.”

## 2. Why Do We Need Portals?

Consider this structure:

```jsx
<div className="app">
  <div className="card">
    <Modal />
  </div>
</div>
```

If `.card` has `overflow: hidden`, the modal may be clipped. A very high `z-index` does not always solve the problem because stacking contexts can constrain descendants.

A portal can render the modal under a top-level DOM node such as `#modal-root`:

```html
<div id="root"></div>
<div id="modal-root"></div>
```

The modal is still logically a child of the component that rendered it, but its DOM nodes are placed under `#modal-root`.

### Common use cases

- Modal dialogs and confirmation windows
- Tooltips and popovers
- Dropdown menus that must escape clipping containers
- Toast notifications
- Context menus
- Floating panels and overlays

A portal is not required for every overlay. Use it when DOM placement solves a real layout or stacking problem.

## 3. `createPortal`

React provides `createPortal` from `react-dom`.

### Syntax

```jsx
import { createPortal } from "react-dom";

createPortal(children, domNode, key?);
```

- `children`: React content to render.
- `domNode`: Existing DOM node where the content should be placed.
- `key`: Optional key when rendering portals as part of a list.

The destination DOM node must already exist when the portal is rendered.

### Basic example

HTML:

```html
<div id="root"></div>
<div id="portal-root"></div>
```

React component:

```jsx
import { createPortal } from "react-dom";

function Notice() {
  const portalRoot = document.getElementById("portal-root");

  if (!portalRoot) return null;

  return createPortal(
    <div className="notice">Rendered through a portal</div>,
    portalRoot
  );
}

export default Notice;
```

The notice appears inside `#portal-root`, not inside the DOM element where `<Notice />` would normally render.

## 4. React Tree vs DOM Tree

This is the most important portal concept.

- **React tree:** The logical component hierarchy managed by React.
- **DOM tree:** The actual browser document hierarchy.

A portal changes where the DOM nodes are placed. It does not change the component's logical position in the React tree.

For example:

```jsx
function Parent() {
  return (
    <div onClick={() => console.log("Parent clicked")}>
      <Modal />
    </div>
  );
}
```

If `Modal` renders its content into `#portal-root`, the modal DOM is outside the parent's DOM element. However, the modal is still a React descendant of `Parent`.

## 5. Event Bubbling Through Portals

Events from portal content propagate according to the **React tree**, not according to the physical DOM tree.

That means a click inside a portal can trigger an `onClick` handler on a React ancestor, even when that ancestor is not a DOM ancestor of the portal content.

Example:

```jsx
import { createPortal } from "react-dom";

function Modal() {
  return createPortal(
    <button onClick={() => console.log("Portal button clicked")}>
      Click me
    </button>,
    document.getElementById("portal-root")
  );
}

function Parent() {
  return (
    <div onClick={() => console.log("Parent handler triggered")}>
      <Modal />
    </div>
  );
}
```

Clicking the portal button triggers both handlers through React's event propagation.

### Preventing propagation

If the click should not reach a React ancestor, stop propagation at the relevant element:

```jsx
<div onClick={(event) => event.stopPropagation()}>
  Modal content
</div>
```

Do not stop propagation everywhere by default. First understand which handlers should receive the event.

## 6. Building a Reusable Modal with a Portal

Create a reusable `Modal.jsx`:

```jsx
import { createPortal } from "react-dom";

function Modal({ open, onClose, children }) {
  if (!open) return null;

  const portalRoot = document.getElementById("portal-root");

  if (!portalRoot) {
    console.error('Missing element with id="portal-root"');
    return null;
  }

  return createPortal(
    <div className="modal-backdrop" onClick={onClose}>
      <div
        className="modal"
        role="dialog"
        aria-modal="true"
        onClick={(event) => event.stopPropagation()}
      >
        <button type="button" onClick={onClose} aria-label="Close dialog">
          ×
        </button>
        {children}
      </div>
    </div>,
    portalRoot
  );
}

export default Modal;
```

Use it from a parent:

```jsx
import { useState } from "react";
import Modal from "./Modal";

function App() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(true)}>Open modal</button>

      <Modal open={isOpen} onClose={() => setIsOpen(false)}>
        <h2>Confirm action</h2>
        <p>Are you sure you want to continue?</p>
      </Modal>
    </>
  );
}

export default App;
```

The backdrop handles outside clicks. The modal content stops the click from reaching the backdrop handler.

### Important production note

This example demonstrates portal rendering and basic close behavior. A production modal also needs focus management, Escape handling, focus restoration, and an appropriate accessible name.

## 7. Portal Host Element

Add the portal destination to the HTML document:

```html
<body>
  <div id="root"></div>
  <div id="portal-root"></div>
</body>
```

You can name the destination differently, such as `modal-root` or `overlay-root`. The ID in `getElementById` must match the HTML.

### Missing portal host

If `document.getElementById("portal-root")` returns `null`, `createPortal` cannot render into it. Check that:
- The host element exists in the HTML.
- The ID is spelled correctly.
- The component is not trying to use the node before it exists.

For applications that render on the server, avoid accessing `document` during server rendering. Resolve browser-only DOM nodes safely on the client.

## 8. Backdrop Click vs Content Click

A common modal interaction is:
- Clicking the backdrop closes the modal.
- Clicking inside the dialog does not close it.

Use separate handlers:

```jsx
<div className="backdrop" onClick={onClose}>
  <section
    role="dialog"
    aria-modal="true"
    onClick={(event) => event.stopPropagation()}
  >
    Dialog content
  </section>
</div>
```

An alternative is to compare `event.target` and `event.currentTarget`:

```jsx
function handleBackdropClick(event) {
  if (event.target === event.currentTarget) {
    onClose();
  }
}
```

This avoids closing the dialog when a nested element is clicked.

## 9. Styling Portals

Portal content is styled using normal CSS. Keep overlay styles in a predictable global or imported stylesheet.

```css
.modal-backdrop {
  position: fixed;
  inset: 0;
  display: grid;
  place-items: center;
  background: rgb(0 0 0 / 0.55);
  z-index: 1000;
}

.modal {
  width: min(90vw, 500px);
  max-height: 85vh;
  overflow: auto;
  padding: 24px;
  border-radius: 12px;
  background: white;
}
```

Remember:
- A portal changes DOM placement, not CSS inheritance rules.
- Styles scoped to a parent selector may no longer match the portaled DOM.
- CSS custom properties inherited only from the original DOM ancestors may not be available at the portal destination.
- A portal does not automatically solve every stacking issue; the destination and its ancestors still participate in CSS stacking contexts.

## 10. Accessibility Requirements for Modal Portals

A portal does not automatically make a modal accessible.

A modal dialog should generally:
1. Have `role="dialog"` or use the native `<dialog>` element appropriately.
2. Set `aria-modal="true"` when it behaves as a modal.
3. Have an accessible name, commonly with `aria-labelledby` or `aria-label`.
4. Move focus into the dialog when it opens.
5. Keep keyboard focus within the modal while it is open.
6. Close on Escape when appropriate.
7. Restore focus to the element that opened it when it closes.
8. Make background content unavailable to interaction while modal behavior requires it.

Example naming:

```jsx
<section
  role="dialog"
  aria-modal="true"
  aria-labelledby="dialog-title"
>
  <h2 id="dialog-title">Delete item?</h2>
  <p>This action cannot be undone.</p>
</section>
```

A focus trap and focus restoration require additional logic or a well-maintained accessible dialog library. Do not claim that `role="dialog"` alone provides those behaviors.

## 11. Closing a Portal with the Escape Key

A component can listen for Escape while open:

```jsx
import { useEffect } from "react";

useEffect(() => {
  if (!open) return;

  function handleKeyDown(event) {
    if (event.key === "Escape") {
      onClose();
    }
  }

  document.addEventListener("keydown", handleKeyDown);

  return () => {
    document.removeEventListener("keydown", handleKeyDown);
  };
}, [open, onClose]);
```

The cleanup removes the listener when the modal closes or dependencies change. Ensure `onClose` is stable when appropriate, or understand that a changed callback causes the Effect to resubscribe.

## 12. Portals and Component State

A portal does not create a separate React application. It remains part of the same React tree.

Therefore:
- State in the parent can control whether portal content is rendered.
- Context values are available to portal descendants.
- React props and component relationships continue to work.
- React event propagation follows the React tree.

Example:

```jsx
const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Modal />
    </ThemeContext.Provider>
  );
}
```

A component rendered inside `Modal` through a portal can still read the context provided above it in the React tree.

## 13. Portals and Cleanup

When the component stops rendering the portal, React removes the portal's rendered children from the destination node as part of normal reconciliation.

You usually should not manually remove React-managed portal children with `element.remove()` or `innerHTML = ""`. Doing so can conflict with React's ownership of the DOM.

If your component creates its own portal host dynamically, remove that host in an Effect cleanup:

```jsx
useEffect(() => {
  const host = document.createElement("div");
  host.className = "dynamic-portal-host";
  document.body.appendChild(host);

  setPortalHost(host);

  return () => {
    host.remove();
  };
}, []);
```

This pattern needs state to expose the host after creation. In many applications, a static host in `index.html` is simpler.

## 14. Portals in Server-Side Rendering

The browser DOM does not exist during server rendering, so code that directly reads `document` cannot run on the server.

Avoid evaluating `document.getElementById(...)` at module scope or unconditionally during server rendering.

Common approaches:
- Use a portal host that is resolved only in client-side code.
- Render the portal only after the component has mounted.
- Use framework-supported client-only boundaries when available.

Example client-ready pattern:

```jsx
import { useEffect, useState } from "react";
import { createPortal } from "react-dom";

function ClientPortal({ children }) {
  const [host, setHost] = useState(null);

  useEffect(() => {
    setHost(document.getElementById("portal-root"));
  }, []);

  if (!host) return null;

  return createPortal(children, host);
}
```

The initial render returns `null`; after mounting, the host is resolved and the portal is rendered.

## 15. Portal vs Normal Rendering

| Feature | Normal rendering | Portal rendering |
|---|---|---|
| DOM placement | Under the normal parent DOM node | Inside the specified destination node |
| React tree | Normal component hierarchy | Same component hierarchy |
| Context | Available from React ancestors | Available from React ancestors |
| Event propagation | Follows React tree | Also follows React tree |
| Typical use | Regular page content | Overlays and floating UI |

## 16. Portal vs Separate React Root

A portal and a separate React root are not the same.

- `createPortal` places content in another DOM node while preserving the existing React tree relationship.
- `createRoot` creates a separate React root with its own root-level rendering lifecycle.

Use a portal when content should remain part of the current React application and inherit its context and React event relationships.

## 17. Common Mistakes

1. **Thinking a portal changes the React parent.** It changes DOM placement only.
2. **Assuming events follow DOM ancestry.** Portal events propagate through the React tree.
3. **Forgetting the destination node.** `createPortal` needs an existing DOM element.
4. **Using only a large `z-index`.** Stacking contexts and clipping can still cause problems.
5. **Forgetting accessibility.** A portal does not provide focus trapping or keyboard behavior automatically.
6. **Removing portal children manually.** Let React manage the DOM it rendered.
7. **Accessing `document` during server rendering.** Resolve browser DOM nodes on the client.
8. **Assuming CSS inheritance is unchanged.** The portal inherits styles from its actual DOM ancestors.
9. **Closing on every click inside the dialog.** Separate backdrop and dialog click behavior.
10. **Adding global listeners without cleanup.** Remove listeners when the dialog closes or unmounts.

## 18. Best Practices

- Use portals for genuine DOM-placement needs, not by default.
- Keep the portal host predictable and clearly named.
- Build reusable overlay components with explicit `open` and `onClose` props.
- Separate backdrop interaction from dialog interaction.
- Provide accessible names and complete keyboard/focus behavior.
- Clean up document-level event listeners.
- Let React manage portal children.
- Test both React behavior and actual DOM placement.
- Consider an established accessible dialog library for production-critical dialogs.

## 19. Testing Portals

When testing a portal, remember that its DOM may be outside the component's rendered container.

A typical test should:
- Render the component.
- Find the dialog by its accessible role and name.
- Verify it appears in the document.
- Click inside the dialog and confirm it stays open.
- Click the backdrop and confirm it closes.
- Verify Escape behavior if implemented.
- Verify focus behavior if implemented.

Example with React Testing Library:

```jsx
render(<App />);

expect(
  screen.getByRole("dialog", { name: /confirm action/i })
).toBeInTheDocument();
```

Use queries against `screen` for document-level content rather than assuming every portal node is inside the local render container.

## 20. Quick Revision

- A portal renders children into a different DOM node.
- Import `createPortal` from `react-dom`.
- A portal changes DOM placement, not the React component tree.
- Context continues to work through portals.
- Events bubble according to the React tree.
- Modals, tooltips, popovers, and toasts are common use cases.
- A portal does not automatically provide accessibility or focus management.
- The destination DOM node must exist.
- React manages the portal's rendered children.
- Browser-only DOM access must be handled carefully in SSR.

---

## Final Learning Checklist

- [ ] Explain why portals are useful.
- [ ] Write `createPortal` syntax from memory.
- [ ] Explain React tree vs DOM tree.
- [ ] Explain event bubbling through portals.
- [ ] Build a modal using a portal.
- [ ] Implement backdrop click and Escape behavior.
- [ ] Explain focus management and accessibility requirements.
- [ ] Handle missing portal hosts and SSR safely.
- [ ] Test portal content outside the local render container.
