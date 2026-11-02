# SSR, Hydration, and React Server Components

## 1. Client-side rendering

In client-side rendering, the browser downloads JavaScript, runs React, and builds the UI in the browser. This can produce a slower initial meaningful display for some applications if the JavaScript bundle is large or the device is slow.

## 2. Server-side rendering

With SSR, the server renders HTML for a route and sends it to the browser. The browser can display the HTML before the client React code has fully initialized. This can improve initial content visibility and help crawlers that consume HTML, depending on the framework and page.

SSR does not remove the need for client JavaScript when the page is interactive.

## 3. Hydration

Hydration is the process where React attaches its client behavior to HTML that was rendered on the server. The initial client render must match the server-rendered output.

Hydration mismatch causes include:
- Rendering different values on server and client, such as random values or current time.
- Reading browser-only APIs during server rendering.
- Different locale/time-zone formatting.
- Invalid HTML nesting that the browser repairs.
- Data changing between server render and hydration without a consistent snapshot.

Use deterministic initial output and access browser-only APIs in effects or other client-only boundaries.

## 4. hydrateRoot vs createRoot

For server-rendered HTML, use `hydrateRoot` to hydrate the existing markup. `createRoot` is used to create a client-rendered root and is not a replacement for hydrating server markup.

## 5. React Server Components

React Server Components (RSC) are components rendered on the server as part of a framework-supported architecture. They can access server-side resources without shipping that component's implementation to the browser. They are different from traditional SSR: SSR sends HTML, while RSC introduces a server/client component model and a serialized component payload.

RSC support depends on the framework and build setup; it is not enabled merely by installing React in a basic Vite project.

## 6. Server and Client Components

In frameworks that support RSC, Server Components are rendered on the server and cannot use client-only Hooks such as `useState` or browser event handlers. Client Components are marked according to the framework convention and can use interactive state and browser APIs. A Client Component can be nested within a Server Component.

## 7. Benefits and trade-offs

SSR can improve first content visibility and SEO in suitable applications, but adds server infrastructure, caching considerations, request-time work, and hydration complexity. RSC can reduce client JavaScript for server-only UI and data access, but requires framework support and careful client/server boundaries.

## 8. Common mistakes

- Calling `createRoot` on server-rendered HTML instead of hydrating it.
- Assuming SSR makes a page interactive without hydration/client code.
- Reading `window` or `document` during server rendering.
- Producing different initial markup on server and client.
- Assuming a plain Vite React SPA automatically supports RSC.
