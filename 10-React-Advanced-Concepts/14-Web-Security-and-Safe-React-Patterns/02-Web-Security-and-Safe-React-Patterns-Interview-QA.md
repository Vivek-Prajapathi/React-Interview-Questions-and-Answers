# Web Security in React — Interview Questions and Answers

## Table of Contents
1. [Does React prevent XSS?](#q1-does-react-prevent-xss)
2. [What is dangerouslySetInnerHTML?](#q2-what-is-dangerouslysetinnerhtml)
3. [How do you safely render user-provided HTML?](#q3-how-do-you-safely-render-user-provided-html)
4. [Authentication vs authorization?](#q4-authentication-vs-authorization)
5. [Is hiding an admin button sufficient authorization?](#q5-is-hiding-an-admin-button-sufficient-authorization)
6. [Where should private API secrets be stored?](#q6-where-should-private-api-secrets-be-stored)
7. [What is CSRF?](#q7-what-is-csrf)
8. [What is CORS?](#q8-what-is-cors)
9. [How do XSS and CSRF differ?](#q9-how-do-xss-and-csrf-differ)
10. [How should token storage be approached?](#q10-how-should-token-storage-be-approached)

## Q1. Does React prevent XSS?
**Interview answer:** React escapes text rendered through normal JSX interpolation, which protects against many common injection cases. But React does not make an application automatically secure. Unsafe HTML injection, dangerous URLs, third-party scripts, and other browser APIs still need careful handling.

## Q2. What is dangerouslySetInnerHTML?
**Interview answer:** It is a React escape hatch for inserting raw HTML into the DOM. It can create an XSS vulnerability if the HTML contains untrusted content, so I use it only when necessary and only with properly sanitized HTML.

## Q3. How do you safely render user-provided HTML?
**Interview answer:** My default is to render it as text. If the product requirement truly needs HTML, I sanitize it with a trusted sanitizer using an appropriate allowlist and avoid unsafe URL or script content. I also consider Content Security Policy as an additional layer.

## Q4. Authentication vs authorization?
**Interview answer:** Authentication answers who the user is. Authorization answers what that user is permitted to access or do. A user can be authenticated but still not have permission to perform an admin action.

## Q5. Is hiding an admin button sufficient authorization?
**Interview answer:** No. Hiding the button is only a user-interface decision. The backend must verify the user's role or permission on every protected API operation because a user can call the endpoint directly.

## Q6. Where should private API secrets be stored?
**Interview answer:** Private secrets belong on the server, not in frontend environment variables. Anything included in a browser bundle can be inspected by users. The frontend should call a backend that uses the secret securely.

## Q7. What is CSRF?
**Interview answer:** Cross-Site Request Forgery tricks a user's browser into sending an unwanted request to a site where the user is already authenticated. Depending on the session design, defenses can include SameSite cookies, CSRF tokens, and origin checks.

## Q8. What is CORS?
**Interview answer:** CORS is a browser mechanism that controls whether JavaScript from one origin can read responses from another origin. It is not an authentication or authorization system.

## Q9. How do XSS and CSRF differ?
**Interview answer:** XSS injects attacker-controlled script into a trusted site and runs it in the user's browser. CSRF tricks the browser into sending an authenticated request the user did not intend. They are different threats and need different defenses.

## Q10. How should token storage be approached?
**Interview answer:** I evaluate the full threat model. JavaScript-accessible storage can be exposed if an XSS vulnerability exists. HttpOnly cookies prevent JavaScript from reading the cookie but require appropriate CSRF protections and secure cookie configuration. I follow the backend's session architecture rather than assuming one storage choice fits every application.
