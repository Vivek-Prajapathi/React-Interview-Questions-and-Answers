# Code Splitting, lazy Loading, and Suspense

## 1. Why split code?

A large single JavaScript bundle can increase download, parse, and execution time. Code splitting creates separate chunks so users do not need to load every feature before seeing the initial screen.

Common split points:
- Routes.
- Large optional features.
- Admin-only screens.
- Heavy editors, charts, or maps.

## 2. React.lazy

`lazy` accepts a function that returns a promise resolving to a module with a default component export.

```jsx
import { lazy, Suspense } from "react";

const ReportsPage = lazy(() => import("./ReportsPage"));

function App() {
  return (
    <Suspense fallback={<p>Loading reports...</p>}>
      <ReportsPage />
    </Suspense>
  );
}
```

The component is loaded when React first attempts to render it. The bundler creates a separate chunk for the dynamic import.

## 3. Suspense

Suspense displays fallback UI while a supported child is not ready. `React.lazy` integrates with Suspense for code loading. Suspense is also used by supported data-fetching frameworks and React features, but wrapping an arbitrary fetch call in an effect does not automatically make it Suspense-enabled.

Place boundaries at useful UI levels. A single boundary around the whole app may replace too much UI with one fallback. Nested boundaries can let independent sections load separately.

## 4. Error handling

Suspense handles the pending state; it is not itself an error boundary. A failed dynamic import should be handled by an Error Boundary or framework error mechanism. Consider a retry path for transient chunk/network failures.

## 5. Route-level splitting

Route-level splitting is a common strategy because users often visit only a subset of application routes in one session. Configure lazy components at route boundaries and provide an appropriate loading fallback.

## 6. Preloading and user experience

For important likely-next features, a bundler or application can preload chunks strategically. Avoid loading every chunk eagerly, which defeats the purpose. Loading indicators should avoid unnecessary flicker for very short waits.

## 7. Common mistakes

- Forgetting to wrap a lazy component in Suspense.
- Expecting Suspense to catch errors.
- Using a module without a default export with `React.lazy` without adapting it.
- Splitting tiny components excessively.
- Providing a blank or disruptive fallback.
- Assuming all data fetching automatically suspends.
