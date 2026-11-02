# React Error Boundaries

## 1. What is an Error Boundary?

An Error Boundary is a React component that catches errors thrown while rendering its descendant component tree, during descendant lifecycle methods, and in constructors. It displays fallback UI instead of allowing the affected part of the UI to crash the entire application.

Error Boundaries are class components using one or both of:
- `static getDerivedStateFromError(error)` to update state and render fallback UI.
- `componentDidCatch(error, info)` to report or log the error.

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    console.error("UI error:", error, info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong.</h2>;
    }
    return this.props.children;
  }
}
```

Wrap a risky section:

```jsx
<ErrorBoundary>
  <ProductDetails />
</ErrorBoundary>
```

## 2. What Error Boundaries do not catch

Traditional Error Boundaries do not catch:
- Errors in event handlers.
- Errors in asynchronous callbacks such as `setTimeout`.
- Rejected promises that occur outside the render/lifecycle path.
- Errors thrown by the boundary itself.
- Server-side rendering errors.

Handle event-handler and async errors with `try/catch`, promise rejection handling, or the relevant data-fetching framework's error mechanism.

## 3. Choosing boundary placement

A single boundary around the entire application provides a global fallback, but can hide the whole UI. Smaller boundaries around independent features allow one area to fail while the rest remains usable.

Examples:
- Dashboard widgets.
- Route-level content.
- Complex third-party components.
- Individual panels that can fail independently.

## 4. Resetting an Error Boundary

A boundary usually needs a reset mechanism so users can retry. One approach is to reset its state after a reset key changes:

```jsx
componentDidUpdate(previousProps) {
  if (previousProps.resetKey !== this.props.resetKey) {
    this.setState({ hasError: false });
  }
}
```

A production implementation must avoid resetting continuously and should coordinate retry behavior with the failed data or route.

## 5. Logging and user experience

`componentDidCatch` is a good place to send diagnostic information to an error monitoring service. Avoid exposing stack traces or sensitive internal details to end users. Show a helpful message and a recovery action.

## 6. Error Boundary vs try/catch

`try/catch` handles errors in the synchronous JavaScript execution inside its block. It does not catch a descendant component's render error merely because that component was returned from the block. Error Boundaries integrate with React's rendering process and isolate descendant UI failures.

## 7. Frameworks and modern React

Routing and data frameworks may provide route-level error elements or error handling conventions. Those are framework features and should be distinguished from a hand-written class Error Boundary. Some libraries also provide Hook-based APIs built on top of boundary components.

## 8. Common mistakes

- Expecting an Error Boundary to catch click-handler exceptions.
- Expecting it to catch every rejected Promise.
- Placing one boundary so high that a small widget failure hides the whole app.
- Showing raw internal error details to users.
- Forgetting a retry/reset path.
- Assuming a function component can declare `getDerivedStateFromError` as a Hook.
