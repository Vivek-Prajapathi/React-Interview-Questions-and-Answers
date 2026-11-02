# SSR, Hydration, and Server Components — Interview Questions and Answers

## Table of Contents
1. [What is server-side rendering?](#q1-what-is-server-side-rendering)
2. [What is hydration?](#q2-what-is-hydration)
3. [SSR vs CSR?](#q3-ssr-vs-csr)
4. [What causes hydration mismatches?](#q4-what-causes-hydration-mismatches)
5. [hydrateRoot vs createRoot?](#q5-hydrateroot-vs-createroot)
6. [Does SSR remove the need for JavaScript?](#q6-does-ssr-remove-the-need-for-javascript)
7. [What are React Server Components?](#q7-what-are-react-server-components)
8. [Server Components vs SSR?](#q8-server-components-vs-ssr)
9. [Can a Server Component use useState?](#q9-can-a-server-component-use-usestate)
10. [Does Vite automatically enable Server Components?](#q10-does-vite-automatically-enable-server-components)

## Q1. What is server-side rendering?
**Interview answer:** Server-side rendering means generating the page's HTML on the server and sending it to the browser. The browser can display the initial content before the client-side React application has fully initialized. It is often used for initial performance and SEO requirements.

## Q2. What is hydration?
**Interview answer:** Hydration is when React attaches its client-side behavior to HTML that was already rendered on the server. The initial client output must match the server HTML so React can hydrate it correctly.

## Q3. SSR vs CSR?
**Interview answer:** With CSR, the browser runs React to create the UI. With SSR, the server sends rendered HTML first, and the client later hydrates it for interactivity. The right choice depends on performance, SEO, infrastructure, and application requirements.

## Q4. What causes hydration mismatches?
**Interview answer:** A mismatch happens when the initial client render differs from the server HTML. Examples include random values, timestamps, browser-only data, locale differences, and invalid HTML nesting. I make initial rendering deterministic and move browser-only work to the client.

## Q5. hydrateRoot vs createRoot?
**Interview answer:** `hydrateRoot` attaches React to existing server-rendered HTML. `createRoot` creates a new client-rendered root. For SSR markup, I use hydration rather than replacing the server HTML with a fresh client render.

## Q6. Does SSR remove the need for JavaScript?
**Interview answer:** No. SSR can provide initial HTML, but client JavaScript is still needed for React interactions that require hydration. Static content can be visible before hydration, but interactive behavior depends on the client setup.

## Q7. What are React Server Components?
**Interview answer:** React Server Components are components rendered on the server in a framework-supported architecture. They can access server resources and avoid sending their component implementation to the browser. They are part of a server/client component model, not just another name for SSR.

## Q8. Server Components vs SSR?
**Interview answer:** SSR describes rendering HTML on the server. Server Components describe where components execute and how their output participates in the React tree. A framework can use both technologies together.

## Q9. Can a Server Component use useState?
**Interview answer:** No. Server Components do not run as interactive client components and cannot use client state Hooks such as `useState` or browser event handlers. I put interactivity in a Client Component.

## Q10. Does Vite automatically enable Server Components?
**Interview answer:** No. A standard Vite React SPA does not automatically have React Server Components architecture. RSC requires framework or build-system support and an appropriate server/client setup.
