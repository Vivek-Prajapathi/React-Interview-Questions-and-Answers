# Accessibility and Keyboard Interactions in React

## 1. Accessibility is part of component behavior

Accessible React UI should work with keyboards, screen readers, zoom, and different input methods. Prefer native HTML elements because they already provide semantics and keyboard behavior.

Use a `<button>` for an action, an `<a>` for navigation, a `<label>` for an input, and headings in a meaningful hierarchy. Do not use a clickable `<div>` when a native control expresses the behavior.

## 2. Labels and form errors

```jsx
<label htmlFor="email">Email address</label>
<input
  id="email"
  type="email"
  aria-describedby="email-error"
  aria-invalid={Boolean(error)}
/>
{error && <p id="email-error">{error}</p>}
```

A visible label gives the field an accessible name. `aria-describedby` connects help or error text to the field.

## 3. Keyboard support

Interactive elements should be reachable and operable using the keyboard. Native buttons support Enter and Space activation. Custom widgets need appropriate keyboard interaction, focus management, and ARIA semantics.

Do not add `tabIndex` indiscriminately. Positive tab order values usually create confusing navigation.

## 4. Focus management

Focus management matters for dialogs, menus, route changes, and validation. A modal should move focus inside when opened, keep focus within while modal, and restore focus to the invoking element when closed.

Refs are commonly used to focus DOM elements after a state transition. Use effects or layout effects depending on whether the timing must occur before paint.

## 5. ARIA live regions

Use `aria-live` or role-based live regions for dynamic messages that should be announced, such as form submission results or newly loaded content. Do not make every changing region live; excessive announcements can overwhelm users.

## 6. Semantic state

Use `aria-expanded` for expandable controls, `aria-controls` to reference controlled content when appropriate, and `aria-current` for the current navigation item. ARIA supplements semantic HTML; it does not replace correct element choice.

## 7. Testing accessibility

- Navigate the entire flow using only a keyboard.
- Check visible focus indicators.
- Verify labels and error associations.
- Test dialog focus entry, containment, and restoration.
- Use automated tools as a supplement, not a replacement for manual testing.
- Test with screen readers where possible.

## 8. Common mistakes

- Using clickable `div` elements without keyboard support.
- Removing focus outlines without replacement.
- Using placeholder text instead of a label.
- Applying ARIA roles that conflict with native semantics.
- Failing to restore focus after closing a modal.
