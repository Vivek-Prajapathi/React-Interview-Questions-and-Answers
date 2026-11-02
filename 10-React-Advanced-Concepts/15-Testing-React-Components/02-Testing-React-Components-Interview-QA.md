# Testing React Components — Interview Questions and Answers

## Table of Contents
1. [What should React component tests verify?](#q1-what-should-react-component-tests-verify)
2. [What is React Testing Library?](#q2-what-is-react-testing-library)
3. [Why query by role?](#q3-why-query-by-role)
4. [getBy vs queryBy vs findBy?](#q4-getby-vs-queryby-vs-findby)
5. [How do you test a user interaction?](#q5-how-do-you-test-a-user-interaction)
6. [How do you test asynchronous UI?](#q6-how-do-you-test-asynchronous-ui)
7. [How do you test API calls?](#q7-how-do-you-test-api-calls)
8. [What is MSW?](#q8-what-is-msw)
9. [How do you test form validation?](#q9-how-do-you-test-form-validation)
10. [What makes a test flaky?](#q10-what-makes-a-test-flaky)

## Q1. What should React component tests verify?
**Interview answer:** I test what the user can observe: rendered content, accessible controls, interactions, loading states, validation, and error messages. I avoid coupling tests to internal state or implementation details because those can change without changing the user experience.

## Q2. What is React Testing Library?
**Interview answer:** React Testing Library provides utilities to render components and query the DOM in ways that encourage testing from the user's perspective. It works well with a test runner and user-event for realistic interactions.

## Q3. Why query by role?
**Interview answer:** Queries such as `getByRole` reflect the semantic roles that users and assistive technologies rely on. They encourage accessible markup and make tests less dependent on implementation-specific selectors.

## Q4. getBy vs queryBy vs findBy?
**Interview answer:** `getBy` returns a matching element immediately or throws if it is missing. `queryBy` returns null when no match exists, which is useful for asserting absence. `findBy` returns a promise and waits for an element to appear asynchronously.

## Q5. How do you test a user interaction?
**Interview answer:** I render the component, create a `userEvent` instance, perform the interaction such as a click or typing, and assert the resulting visible behavior. This is closer to how a user operates the application.

## Q6. How do you test asynchronous UI?
**Interview answer:** I use `findBy` for elements that should appear after asynchronous work and `waitFor` for more complex conditions. I avoid arbitrary sleeps because they make tests slow and unreliable.

## Q7. How do you test API calls?
**Interview answer:** I mock the network boundary and test the component's loading, success, and error states. Mock Service Worker is useful because it intercepts requests without requiring the component to know that the response is mocked.

## Q8. What is MSW?
**Interview answer:** Mock Service Worker intercepts network requests and provides controlled responses. It lets tests exercise the real request code while controlling success, failure, and delayed responses.

## Q9. How do you test form validation?
**Interview answer:** I interact with the fields as a user would, submit invalid and valid values, and assert that the correct accessible error messages or success behavior appear. I also test pending and server-error states when they are part of the form.

## Q10. What makes a test flaky?
**Interview answer:** Common causes include arbitrary timeouts, unawaited asynchronous interactions, shared state between tests, uncontrolled network calls, and depending on implementation details. I make tests deterministic, isolate setup, and wait for observable conditions.
