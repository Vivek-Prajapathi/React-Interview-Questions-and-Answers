# React Accessibility — Interview Questions and Answers

## Table of Contents
1. [What does accessibility mean in React?](#q1-what-does-accessibility-mean-in-react)
2. [Why prefer semantic HTML?](#q2-why-prefer-semantic-html)
3. [How do you make a form field accessible?](#q3-how-do-you-make-a-form-field-accessible)
4. [How do you make a custom control keyboard accessible?](#q4-how-do-you-make-a-custom-control-keyboard-accessible)
5. [What is focus management?](#q5-what-is-focus-management)
6. [How should a modal manage focus?](#q6-how-should-a-modal-manage-focus)
7. [What is aria-live used for?](#q7-what-is-aria-live-used-for)
8. [What is aria-expanded?](#q8-what-is-aria-expanded)
9. [Is ARIA a replacement for semantic HTML?](#q9-is-aria-a-replacement-for-semantic-html)
10. [How do you test accessibility?](#q10-how-do-you-test-accessibility)

## Q1. What does accessibility mean in React?
**Interview answer:** Accessibility means building interfaces that people can use with different abilities and input methods, including keyboards and screen readers. In React, I apply the same web accessibility principles and ensure interactive behavior remains accessible as the UI changes.

## Q2. Why prefer semantic HTML?
**Interview answer:** Native elements provide built-in roles, keyboard behavior, and browser semantics. A button already behaves like a button, so I prefer `<button>` over a clickable `div` rather than rebuilding those behaviors manually.

## Q3. How do you make a form field accessible?
**Interview answer:** I provide a visible label associated with the input, connect help or error text using `aria-describedby`, and set `aria-invalid` when validation fails. I also ensure the error is understandable and not communicated by color alone.

## Q4. How do you make a custom control keyboard accessible?
**Interview answer:** I define which keys operate it, make it reachable in a logical tab order, provide visible focus, and use correct semantics and ARIA states. If a native HTML control already provides the behavior, I use that instead.

## Q5. What is focus management?
**Interview answer:** Focus management means controlling where keyboard focus moves as the interface changes. It is important for dialogs, menus, route transitions, and validation so users do not lose their place.

## Q6. How should a modal manage focus?
**Interview answer:** When opened, focus should move into the dialog. While it is modal, keyboard focus should remain within it. When it closes, focus should return to the element that opened it, unless the workflow has a better next focus target.

## Q7. What is aria-live used for?
**Interview answer:** `aria-live` marks a region whose updates can be announced by assistive technology. I use it for important dynamic feedback such as a form submission result, but avoid announcing every minor UI change.

## Q8. What is aria-expanded?
**Interview answer:** `aria-expanded` communicates whether a control's associated content is currently expanded or collapsed. I update it to match the actual UI state.

## Q9. Is ARIA a replacement for semantic HTML?
**Interview answer:** No. ARIA adds accessibility information when needed, but it does not replace native semantics and behavior. I start with the correct HTML element and add ARIA only when it provides missing information.

## Q10. How do you test accessibility?
**Interview answer:** I test keyboard-only navigation, visible focus, labels, form errors, and dialog behavior. I also use automated accessibility tools and, when possible, screen readers. Automated checks are useful but do not replace manual testing.
