# Advanced React Forms — Interview Questions and Answers

## Table of Contents
1. [Controlled vs uncontrolled components?](#q1-controlled-vs-uncontrolled-components)
2. [When would you choose an uncontrolled form?](#q2-when-would-you-choose-an-uncontrolled-form)
3. [How do you validate a form?](#q3-how-do-you-validate-a-form)
4. [Why is server-side validation still required?](#q4-why-is-server-side-validation-still-required)
5. [What are touched and errors in a form?](#q5-what-are-touched-and-errors-in-a-form)
6. [How do you handle async form submission?](#q6-how-do-you-handle-async-form-submission)
7. [What is useActionState?](#q7-what-is-useactionstate)
8. [What is useFormStatus?](#q8-what-is-useformstatus)
9. [Why are name attributes important?](#q9-why-are-name-attributes-important)
10. [How do you make validation accessible?](#q10-how-do-you-make-validation-accessible)

## Q1. Controlled vs uncontrolled components?
**Interview answer:** A controlled input gets its value from React state and updates through `onChange`. An uncontrolled input stores its value in the DOM and can be read using a ref or `FormData`. I use controlled inputs when I need immediate React-driven behavior and uncontrolled inputs when I only need values at submission.

## Q2. When would you choose an uncontrolled form?
**Interview answer:** I may choose an uncontrolled form for a simple form where I do not need to react to every keystroke. It can reduce state boilerplate, and I can read values with the browser's `FormData` API.

## Q3. How do you validate a form?
**Interview answer:** I combine native browser constraints with client-side validation for immediate feedback. I track field errors and show them clearly, but the backend also validates every submitted value because client-side validation can be bypassed.

## Q4. Why is server-side validation still required?
**Interview answer:** Client-side validation is for user experience, not security. A user can bypass browser code or send requests directly. The server must enforce data format, permissions, and business rules.

## Q5. What are touched and errors in a form?
**Interview answer:** `touched` tracks whether a user has interacted with or blurred a field. `errors` stores validation messages. I use them to avoid showing every error before the user has had a chance to enter data, while still showing all relevant errors on submit.

## Q6. How do you handle async form submission?
**Interview answer:** I track a pending state, prevent duplicate submissions, await the request, and handle success and failure. If the server returns field-specific validation errors, I display them beside the corresponding fields.

## Q7. What is useActionState?
**Interview answer:** `useActionState` manages the result state of an action and exposes a dispatch function and pending status. It integrates with React form Actions. In a client-only application, I still need to understand the supported action setup rather than assuming server actions exist automatically.

## Q8. What is useFormStatus?
**Interview answer:** `useFormStatus` reads the status of the nearest parent form from a component inside that form. It is useful for a submit button that displays a pending state or prevents repeated submission.

## Q9. Why are name attributes important?
**Interview answer:** The `name` attribute identifies a field in form submission data and in `FormData`. If my change handler reads `event.target.name`, the input must have a matching name; otherwise the handler cannot update the intended field.

## Q10. How do you make validation accessible?
**Interview answer:** I associate each error with its input using `aria-describedby`, set `aria-invalid` when the value is invalid, provide a visible label, and make submission feedback understandable to assistive technology.
