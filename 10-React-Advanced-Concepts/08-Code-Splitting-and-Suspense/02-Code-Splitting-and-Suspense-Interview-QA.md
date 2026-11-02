# Code Splitting and Suspense — Interview Questions and Answers

## Table of Contents
1. [What is code splitting?](#q1-what-is-code-splitting)
2. [What is React.lazy?](#q2-what-is-reactlazy)
3. [Why does lazy need Suspense?](#q3-why-does-lazy-need-suspense)
4. [What is Suspense?](#q4-what-is-suspense)
5. [Does Suspense catch errors?](#q5-does-suspense-catch-errors)
6. [Where should Suspense boundaries be placed?](#q6-where-should-suspense-boundaries-be-placed)
7. [How do you lazy-load routes?](#q7-how-do-you-lazy-load-routes)
8. [What happens if a lazy import fails?](#q8-what-happens-if-a-lazy-import-fails)
9. [Does Suspense automatically handle fetch in useEffect?](#q9-does-suspense-automatically-handle-fetch-in-useeffect)
10. [What are the disadvantages of excessive code splitting?](#q10-what-are-the-disadvantages-of-excessive-code-splitting)

## Q1. What is code splitting?
**Interview answer:** Code splitting divides an application's JavaScript into smaller chunks. Instead of downloading all features at the beginning, the application can load route or feature code when it is needed. This can reduce initial bundle size and startup work.

## Q2. What is React.lazy?
**Interview answer:** `React.lazy` lets me define a component whose module is loaded dynamically. It expects a promise that resolves to a module with a default component export, and it is commonly used with `Suspense`.

## Q3. Why does lazy need Suspense?
**Interview answer:** The dynamic import is asynchronous. While the module is loading, React needs a fallback to display. A surrounding Suspense boundary provides that fallback.

## Q4. What is Suspense?
**Interview answer:** Suspense lets a part of the UI show fallback content while a supported child is not ready. A common example is lazy-loaded route content displaying a loading message until its JavaScript chunk arrives.

## Q5. Does Suspense catch errors?
**Interview answer:** No. Suspense handles the pending state, not the error state. I use an Error Boundary or the framework's error handling to show fallback UI if a lazy import fails.

## Q6. Where should Suspense boundaries be placed?
**Interview answer:** I place boundaries around meaningful independent parts of the UI. This lets one section load while the rest remains visible. I avoid a boundary that unnecessarily replaces the entire application for a small feature.

## Q7. How do you lazy-load routes?
**Interview answer:** I define route components with `React.lazy` and wrap the route content or route outlet in Suspense with a suitable fallback. This creates route-level chunks that load when users navigate to those pages.

## Q8. What happens if a lazy import fails?
**Interview answer:** The import promise rejects, so the failure should be handled by an Error Boundary or a router/framework error mechanism. I provide a safe message and, where appropriate, a retry option.

## Q9. Does Suspense automatically handle fetch in useEffect?
**Interview answer:** No. A normal fetch inside `useEffect` uses explicit loading and error state. Suspense works with supported Suspense-enabled data sources and features; an effect-based request does not automatically suspend.

## Q10. What are the disadvantages of excessive code splitting?
**Interview answer:** Too many tiny chunks can create additional network requests and loading complexity. I choose meaningful split points such as routes or large optional features and measure the impact on initial load and navigation.
