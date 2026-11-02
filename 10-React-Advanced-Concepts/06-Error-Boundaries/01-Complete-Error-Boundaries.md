# React Error Boundaries — Complete Concepts Guide

## Learning Order

1. Understand why runtime errors need to be isolated in a React application.
2. Learn what an Error Boundary is and what it catches.
3. Understand the class component APIs used to create one.
4. Learn how fallback UI and error reporting work.
5. Understand limitations: event handlers, asynchronous callbacks, server rendering, and errors thrown by the boundary itself.
6. Build reusable Error Boundaries and reset behavior.
7. Learn placement strategies, testing, and best practices.

---

## 1. What Is an Error Boundary?

An Error Boundary is a React component that catches JavaScript errors in its descendant component tree during rendering and certain React lifecycle methods. It prevents an error in one part of the UI from necessarily crashing the entire application and displays fallback UI instead.

**Interview explanation:** “An Error Boundary is a component that catches errors thrown by its child components during rendering and lifecycle processing. It lets us show a fallback interface and keep the rest of the application usable instead of displaying a broken screen.”

Error Boundaries are especially useful around independent parts of an application such as dashboards, widgets, routes, and complex third-party components.

## 2. Why Do We Need Error Boundaries?

Without an Error Boundary, an error during rendering can cause React to unmount the affected root's UI rather than leave a potentially corrupted interface visible.

An Error Boundary provides:
- A controlled fallback interface.
- Isolation of failures to a chosen part of the component tree.
- A place to report errors to monitoring services.
- A better recovery experience for users.

An Error Boundary does not prevent bugs. It handles certain errors after they occur.

## 3. How Error Boundaries Work

The basic flow is:

1. A descendant component throws an error during rendering or a covered lifecycle phase.
2. React searches upward in the component tree for the nearest Error Boundary.
3. React invokes the boundary's error-handling lifecycle methods.
4. The boundary updates its state to indicate an error.
5. The boundary renders its fallback UI instead of the failed descendant subtree.
6. The error can be logged or reported.

A boundary catches errors only in its descendants. It cannot catch errors thrown by itself.

## 4. Can Error Boundaries Be Function Components?

React's built-in Error Boundary mechanism is implemented with class component lifecycle methods. A function component cannot become an Error Boundary merely by using `useEffect` or `try/catch` around JSX.

A class component can use:
- `static getDerivedStateFromError(error)`
- `componentDidCatch(error, info)`

Libraries can provide a convenient function-component API around the underlying boundary implementation, but the built-in boundary lifecycle is class-based.

## 5. `getDerivedStateFromError`

`static getDerivedStateFromError(error)` is called when a descendant throws an error during a phase covered by Error Boundaries.

It is used to update state so the next render displays fallback UI.

Important points:
- It must be a static method.
- It receives the thrown error.
- It should return an object to update state.
- It should remain free of side effects.
- It does not receive the component instance as `this`.

Example:

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong.</h2>;
    }

    return this.props.children;
  }
}
```

## 6. `componentDidCatch`

`componentDidCatch(error, info)` is called after an error has been caught. It is commonly used for logging or sending error details to a monitoring service.

It receives:
- `error`: the thrown error.
- `info`: an object containing React component-stack information, typically available as `info.componentStack`.

Example:

```jsx
componentDidCatch(error, info) {
  console.error("Caught by Error Boundary:", error);
  console.error("Component stack:", info.componentStack);
}
```

Avoid showing sensitive information to end users. Send only the data permitted by your application's privacy and security requirements.

## 7. Difference Between the Two Lifecycle Methods

| Method | Main purpose | Typical responsibility |
|---|---|---|
| `getDerivedStateFromError` | Update state after a descendant error | Choose fallback UI |
| `componentDidCatch` | Perform side effects after catching an error | Log or report the error |

A common implementation uses both methods: one to switch to fallback UI and one to report the error.

## 8. Complete Error Boundary Example

```jsx
import React from "react";

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      hasError: false,
    };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    console.error("Error caught:", error);
    console.error("Component stack:", info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div role="alert">
          <h2>Something went wrong.</h2>
          <p>Please try again later.</p>
        </div>
      );
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

Use it around a component:

```jsx
import ErrorBoundary from "./ErrorBoundary";
import Dashboard from "./Dashboard";

function App() {
  return (
    <ErrorBoundary>
      <Dashboard />
    </ErrorBoundary>
  );
}

export default App;
```

If a descendant of `ErrorBoundary` throws a covered error, the boundary renders the fallback.

## 9. What Does an Error Boundary Catch?

Error Boundaries catch errors thrown by descendant components during:
- Rendering.
- Constructors of descendant class components.
- Class component lifecycle methods covered by React's Error Boundary behavior.

They are designed for errors in the React component tree, not every JavaScript error in the application.

## 10. What Error Boundaries Do Not Catch

An Error Boundary does not automatically catch:

1. **Event handler errors.** Errors thrown later inside a click or input handler are not caught by Error Boundaries.
2. **Asynchronous callback errors.** Errors in `setTimeout`, `setInterval`, or later asynchronous callbacks are not automatically caught.
3. **Rejected Promises.** A rejected Promise must be handled through Promise or async error handling unless an error is deliberately surfaced during rendering through a supported mechanism.
4. **Server-side rendering errors.** Client Error Boundaries do not catch errors during server rendering.
5. **Errors thrown by the boundary itself.** A boundary cannot catch its own errors.
6. **Errors in unrelated code outside the descendant tree.** The error must occur in a covered descendant execution path.

## 11. Handling Event Handler Errors

Use normal `try/catch` inside event handlers.

```jsx
function SaveButton() {
  function handleSave() {
    try {
      // Perform an operation that may throw.
      saveData();
    } catch (error) {
      console.error("Save failed:", error);
    }
  }

  return <button onClick={handleSave}>Save</button>;
}
```

If the operation is asynchronous, use `try/catch` with `async/await`:

```jsx
async function handleSave() {
  try {
    await saveData();
  } catch (error) {
    console.error("Save failed:", error);
  }
}
```

## 12. Handling Errors in Asynchronous Effects

Catch rejected asynchronous operations inside the asynchronous function started by the Effect.

```jsx
useEffect(() => {
  let cancelled = false;

  async function loadData() {
    try {
      const response = await fetch("/api/data");

      if (!response.ok) {
        throw new Error("Request failed");
      }

      const data = await response.json();

      if (!cancelled) {
        setData(data);
      }
    } catch (error) {
      if (!cancelled) {
        setError(error);
      }
    }
  }

  loadData();

  return () => {
    cancelled = true;
  };
}, []);
```

The component can render an inline error state. If an application intentionally wants a boundary to handle an asynchronous error, it can store the error and throw it during a later render; this is an explicit pattern, not automatic Promise catching.

## 13. Error Boundaries and Suspense

Error Boundaries and Suspense handle different conditions:
- Suspense displays a fallback while a descendant suspends, such as waiting for supported lazy-loaded code or data.
- Error Boundaries display fallback UI when a descendant throws a render-time error.

They are often composed:

```jsx
<ErrorBoundary>
  <Suspense fallback={<p>Loading...</p>}>
    <LazyPage />
  </Suspense>
</ErrorBoundary>
```

The Suspense fallback handles the loading state. The Error Boundary handles errors that reach it.

## 14. Where Should Error Boundaries Be Placed?

The placement depends on how much of the UI should fail together.

### Application-level boundary

Wraps most or all of the application. It provides a final fallback if a major part of the UI fails.

### Route-level boundary

Each route can have its own boundary so one broken page does not necessarily take down the whole application shell.

### Component-level boundary

Wraps independent widgets, charts, panels, or third-party components. A failure in one widget can leave neighboring widgets visible.

Example:

```jsx
function Dashboard() {
  return (
    <main>
      <Header />

      <ErrorBoundary>
        <RevenueChart />
      </ErrorBoundary>

      <ErrorBoundary>
        <RecentActivity />
      </ErrorBoundary>
    </main>
  );
}
```

## 15. Fallback UI Design

Fallback UI should:
- Explain that the section could not be displayed.
- Avoid exposing stack traces or sensitive internal details.
- Offer a useful next step when possible.
- Be accessible, for example by using `role="alert"` for an important error message.
- Match the scope of the boundary.

Do not show a technical stack trace to ordinary users. Keep detailed diagnostics in controlled logs.

## 16. Resetting an Error Boundary

After an error, the boundary's state remains in the error state until it is reset or remounted. A simple boundary can provide a reset button:

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  handleReset = () => {
    this.setState({ hasError: false });
  };

  render() {
    if (this.state.hasError) {
      return (
        <div role="alert">
          <p>Something went wrong.</p>
          <button onClick={this.handleReset}>Try again</button>
        </div>
      );
    }

    return this.props.children;
  }
}
```

Resetting the boundary does not guarantee that the underlying error is fixed. If the same child throws again, the fallback will appear again.

## 17. Resetting When the Content Changes

A common strategy is to reset the boundary when a route or resource identifier changes. Some Error Boundary libraries provide a `resetKeys` option. With a custom class boundary, the component can compare a reset-related prop in `componentDidUpdate` or be remounted with a changed `key`.

Example using a key:

```jsx
<ErrorBoundary key={routeId}>
  <RouteContent />
</ErrorBoundary>
```

When `routeId` changes, React remounts the boundary subtree. Use a key that represents the intended reset boundary, not a value that changes on every render.

## 18. Error Reporting and Monitoring

`componentDidCatch` is a natural place to send diagnostic information to a monitoring service.

```jsx
componentDidCatch(error, info) {
  reportError({
    message: error.message,
    componentStack: info.componentStack,
  });
}
```

A real reporting function should:
- Avoid sending secrets or personal data.
- Include useful context such as route and application version where appropriate.
- Avoid flooding the service with duplicate reports.
- Never allow a reporting failure to break the fallback UI.

## 19. Error Boundaries and React Strict Mode

Strict Mode can intentionally re-run certain development behaviors to help reveal unsafe side effects. Error reporting should be designed with development behavior in mind so developers do not mistake repeated development logs for multiple production incidents.

Strict Mode does not turn Error Boundaries into global JavaScript error handlers.

## 20. Common Mistakes

1. **Using `try/catch` around JSX.** It does not catch errors thrown later during React rendering.
2. **Assuming function components can define built-in boundaries with Hooks.** The built-in lifecycle API is class-based.
3. **Expecting event handler errors to be caught.** Handle them in the event handler.
4. **Expecting Promise rejections to be caught automatically.** Handle asynchronous failures explicitly.
5. **Forgetting fallback UI.** A boundary needs to render a useful fallback after setting its error state.
6. **Logging sensitive details in the UI.** Keep diagnostics in protected logs.
7. **Using one boundary for every tiny component.** Choose boundaries based on meaningful failure isolation.
8. **Assuming reset fixes the bug.** Reset only retries rendering; the underlying cause may remain.
9. **Forgetting error reporting.** Production applications should consider monitoring caught errors.
10. **Thinking boundaries catch their own errors.** A parent boundary is needed to catch errors from a boundary's fallback or implementation.

## 21. Testing Error Boundaries

A useful test should verify:
- Normal children render when there is no error.
- A descendant render error causes fallback UI to appear.
- The error is reported if reporting is part of the contract.
- Reset behavior works as intended.
- A parent boundary can handle an error thrown by a nested boundary's fallback.

Testing frameworks may print expected errors to the console. Tests can mock or spy on logging to keep output clear, while still verifying the behavior.

## 22. Error Boundary vs try/catch

| Feature | Error Boundary | `try/catch` |
|---|---|---|
| Main purpose | Catch covered errors in descendant React rendering/lifecycle | Catch errors in a synchronous code block, or awaited errors in an async function |
| JSX rendering errors | Yes, for covered descendants | Not by wrapping JSX returned by a component |
| Event handler errors | No, not automatically | Yes, when used inside the handler |
| Async errors | No, not automatically | Yes, with appropriate Promise/`await` handling |
| Fallback UI | Can render fallback for a failed subtree | Must be implemented manually |

## 23. Best Practices

- Place boundaries around meaningful UI regions.
- Provide clear, accessible fallback interfaces.
- Log errors with useful component-stack context.
- Handle event-handler and asynchronous errors separately.
- Keep sensitive diagnostics out of user-facing UI.
- Provide a retry/reset action only when retrying is meaningful.
- Test normal, failure, and recovery states.
- Consider a maintained Error Boundary library for reset keys and more advanced fallback patterns.

## 24. Quick Revision

- Error Boundaries catch covered errors in descendant rendering and lifecycle processing.
- The built-in implementation uses a class component.
- `getDerivedStateFromError` updates state for fallback rendering.
- `componentDidCatch` is used for logging and reporting.
- Event handlers and asynchronous callbacks are not automatically caught.
- Server-rendering errors are not caught by client Error Boundaries.
- A boundary cannot catch its own errors.
- Boundaries can be placed at app, route, or component level.
- Resetting a boundary retries rendering; it does not fix the underlying bug.

---

## Final Learning Checklist

- [ ] Explain what an Error Boundary does.
- [ ] Explain `getDerivedStateFromError`.
- [ ] Explain `componentDidCatch`.
- [ ] Build a class-based Error Boundary.
- [ ] Identify errors Error Boundaries do not catch.
- [ ] Handle event-handler and async errors with `try/catch`.
- [ ] Choose app-, route-, and component-level placement.
- [ ] Implement fallback and reset behavior.
- [ ] Explain Error Boundary vs Suspense.
- [ ] Test failure and recovery behavior.
