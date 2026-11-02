# Testing React Components

## 1. Testing goals

React tests should verify observable behavior rather than implementation details. A user should be able to interact with a component and receive the expected result regardless of whether the component uses a particular Hook or internal variable.

Common test levels:
- Unit tests for pure functions and isolated logic.
- Component tests for user-visible component behavior.
- Integration tests for multiple components working together.
- End-to-end tests for complete user journeys in a browser.

## 2. Common tools

A typical React testing setup may use:
- Vitest or Jest as the test runner.
- React Testing Library to render components and query the UI.
- `@testing-library/user-event` to simulate realistic user interactions.
- Mock Service Worker (MSW) to mock network requests at the network boundary.
- Browser automation such as Playwright or Cypress for end-to-end tests.

Exact setup depends on the project toolchain.

## 3. Query by accessible behavior

Prefer queries that resemble how users and assistive technologies find elements:
- `getByRole`
- `getByLabelText`
- `getByText`
- `findByRole` for asynchronously appearing elements
- `queryBy...` when asserting absence

Avoid selecting elements by internal class names when a semantic query is available.

## 4. Example component test

```jsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import Counter from "./Counter";

test("increments the displayed count", async () => {
  const user = userEvent.setup();
  render(<Counter />);

  await user.click(screen.getByRole("button", { name: /increment/i }));

  expect(screen.getByText("Count: 1")).toBeInTheDocument();
});
```

This test checks user-visible behavior rather than inspecting component state.

## 5. Async UI testing

Use `findBy` queries when an element should appear asynchronously. For more complex waiting conditions, use `waitFor` carefully. Avoid arbitrary timeouts because they make tests slow and flaky.

For network behavior, mock requests at the network boundary and assert loading, success, and error states.

## 6. Testing forms

Test:
- Labels and required-field behavior.
- Validation messages.
- Successful submission.
- Server-side error display.
- Pending state and duplicate-submit prevention.
- Keyboard interaction where relevant.

## 7. Testing effects and cleanup

Test observable outcomes such as a subscription update or event response. For cleanup, verify that listeners or timers no longer produce behavior after unmount. Use fake timers only when testing timer logic, and restore real timers after the test.

## 8. Common mistakes

- Testing internal state instead of user-visible behavior.
- Using arbitrary sleeps for asynchronous UI.
- Over-mocking every child component.
- Forgetting to await user-event interactions.
- Depending on fragile CSS selectors.
- Ignoring accessibility queries and keyboard behavior.
