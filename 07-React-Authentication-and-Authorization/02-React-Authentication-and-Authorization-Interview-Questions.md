# Chapter 7 – React Authentication and Authorization: Interview Questions and Answers

A structured interview bank with concise, interviewer-ready answers. The answers explain the reasoning, not just definitions.

## Clickable Table of Contents

- [A. Fundamentals](#a-fundamentals)
- [B. Login, Registration, and Sessions](#b-login-registration-and-sessions)
- [C. JWT and Token Management](#c-jwt-and-token-management)
- [D. Browser Storage and Cookies](#d-browser-storage-and-cookies)
- [E. Context API and React Implementation](#e-context-api-and-react-implementation)
- [F. Protected Routes and Authorization](#f-protected-routes-and-authorization)
- [G. Axios and Interceptors](#g-axios-and-interceptors)
- [H. Security](#h-security)
- [I. OAuth, OIDC, and 2FA](#i-oauth-oidc-and-2fa)
- [J. Advanced and Tricky Questions](#j-advanced-and-tricky-questions)
- [K. Scenario-Based Questions](#k-scenario-based-questions)

---

## A. Fundamentals

### Q1. What is authentication?

Authentication is the process of verifying a user's identity. For example, when a user logs in with an email and password, the backend verifies those credentials and establishes an authenticated session.

### Q2. What is authorization?

Authorization determines what an authenticated user is allowed to access or do. For example, an admin may manage users, while a regular user may only view their own profile.

### Q3. What is the difference between authentication and authorization?

Authentication answers “Who are you?” and verifies identity. Authorization answers “What are you allowed to do?” and checks permissions. Authentication generally precedes authorization decisions.

### Q4. Can a user be authenticated but not authorized?

Yes. A user can successfully log in but still lack permission to access an admin dashboard or perform a restricted operation.

### Q5. What is a credential?

A credential is evidence used to prove identity, such as a password, passkey, security key, or one-time code.

### Q6. What is a session?

A session represents an authenticated interaction between a user and an application. It may be maintained through a server-side session identifier in a cookie or through a token-based design.

### Q7. What is a role?

A role is a named grouping of permissions, such as `admin`, `manager`, or `user`. Applications use roles to simplify access-control decisions.

### Q8. What is the difference between role-based and permission-based access control?

Role-based access control assigns permissions through roles. Permission-based access control checks specific actions, such as `users:delete`. Permission checks provide more granular control.

### Q9. Is authentication handled by React?

React can collect credentials, display authentication state, and control the user interface. The backend must verify credentials, establish sessions, validate tokens, and enforce access to protected resources.

### Q10. Why is frontend-only authentication insecure?

Users can modify browser state, bypass UI checks, or call APIs directly. Therefore, hiding a page or button is not security. The backend must independently authenticate and authorize every protected request.

---

## B. Login, Registration, and Sessions

### Q11. Explain the registration flow.

The user submits registration details, the frontend performs usability validation, and the backend validates the request, checks account uniqueness, hashes the password securely, and creates the account. The backend may then require email or phone verification.

### Q12. Explain the login flow.

The frontend sends credentials over HTTPS. The backend verifies them and establishes a session or issues tokens. The frontend then loads the current user's information and displays authenticated UI.

### Q13. What happens during logout?

The application asks the backend to invalidate the session or revoke the relevant refresh credential when supported, clears local authentication state, and redirects the user. Clearing React state alone may not invalidate a server-side session.

### Q14. Why should passwords never be stored in plain text?

If a database is compromised, plaintext passwords immediately expose users' credentials. The backend should store salted password hashes produced by a password-hashing algorithm such as Argon2id or bcrypt.

### Q15. What is password hashing?

Hashing transforms a password into a one-way representation. A password-hashing algorithm uses a salt and configurable work factor to make guessing attacks more expensive. It is not the same as reversible encryption.

### Q16. Why should password hashing happen on the backend?

The backend controls the trusted credential store and can use a properly configured password-hashing algorithm. Frontend hashing does not replace secure backend password handling and may create a false sense of security.

### Q17. What is email verification?

Email verification confirms that a user can access the email address associated with an account. The backend typically sends a short-lived, single-use verification link or code.

### Q18. How should a forgot-password flow work?

The backend should return a generic response, send a short-lived single-use reset link or code when appropriate, validate it securely, and allow the user to set a new password. The reset credential must be invalidated after use.

### Q19. Why should login errors avoid revealing whether an email exists?

Specific errors can enable account enumeration. A generic message such as “Invalid email or password” reduces information available to attackers.

### Q20. How do you maintain login state after a page refresh?

The application checks a trusted session mechanism, for example by calling `/auth/me` with the session cookie. While the check is pending, it displays a loading state. It should not assume a user is authenticated merely because old UI state existed.

---

## C. JWT and Token Management

### Q21. What is a token in authentication?

A token is a credential presented by a client to prove an authenticated session or authority. The server validates it before allowing access to a protected resource.

### Q22. What is JWT?

JWT stands for JSON Web Token. It is a compact token format that carries claims and can be signed or encrypted. Signed JWTs are commonly used to convey claims that a server can verify.

### Q23. What are the three parts of a JWT?

A typical signed JWT has a header, payload, and signature, separated by dots. The header describes token metadata, the payload contains claims, and the signature allows integrity and authenticity verification.

### Q24. Is JWT encrypted?

Not necessarily. A typical signed JWT is encoded, not encrypted, so its header and payload can be read by anyone who obtains it. Signing protects integrity; it does not hide the contents.

### Q25. What is the purpose of a JWT signature?

The signature allows the receiver to verify that the token was issued by a trusted signer and has not been modified. The backend must validate the signature and relevant claims.

### Q26. What is an access token?

An access token is a credential used to access protected APIs. It is commonly short-lived to limit the impact of exposure.

### Q27. What is a refresh token?

A refresh token is used to obtain a new access token after the old one expires. It is more sensitive because it can extend a session, so it needs strong protection, rotation, and revocation controls.

### Q28. Access token vs refresh token?

An access token is presented to protected APIs and usually has a short lifetime. A refresh token is presented to a refresh endpoint to obtain a new access token and is usually longer-lived.

### Q29. What is token expiration?

Token expiration defines the time after which a token must no longer be accepted. For JWTs, the `exp` claim represents the expiration time, which the backend must validate.

### Q30. What happens when an access token expires?

The API should reject the expired token. If the application supports refresh tokens, it can attempt a controlled refresh and retry the original request once. If refresh fails, it should end the session and ask the user to log in again.

### Q31. What is token revocation?

Token revocation is the process of making a token or session invalid before its natural expiration. Depending on the architecture, this can involve server-side session state, refresh-token records, token deny-lists, or key rotation.

### Q32. Can you log out a user by deleting a JWT from localStorage?

Deleting the local copy removes it from that browser storage, but a copied token may remain valid until expiration unless the backend supports revocation or another invalidation strategy. Logout must be designed around the server's session/token model.

### Q33. What is the `Authorization` header?

It is an HTTP header used to send authentication credentials. A common bearer-token format is `Authorization: Bearer <access-token>`.

### Q34. What is the difference between decoding and verifying a JWT?

Decoding reads the token's encoded content. Verification checks its cryptographic signature and relevant claims such as issuer, audience, and expiration. Decoding in React does not establish trust.

### Q35. What are common JWT claims?

Common claims include `sub` for subject, `iss` for issuer, `aud` for audience, `exp` for expiration, `iat` for issued-at time, and `nbf` for not-before time.

---

## D. Browser Storage and Cookies

### Q36. Where can a browser application store authentication information?

Possible approaches include server-managed sessions in cookies, short-lived access tokens held in memory, and browser storage. The choice depends on the threat model and backend architecture. Sensitive long-lived credentials should not be casually exposed to JavaScript.

### Q37. What is localStorage?

localStorage is browser storage that persists across browser restarts until cleared. It is accessible to JavaScript and is not automatically attached to HTTP requests.

### Q38. What is sessionStorage?

sessionStorage stores data for a tab's browsing session and is generally cleared when that session ends. It is JavaScript-readable and is not automatically sent with requests.

### Q39. What is an HttpOnly cookie?

An HttpOnly cookie is not accessible through JavaScript APIs such as `document.cookie`. The browser can still send it automatically with matching requests, according to its cookie attributes.

### Q40. What does the Secure cookie attribute do?

It instructs the browser to send the cookie only over secure connections, normally HTTPS.

### Q41. What does SameSite do?

SameSite controls when cookies are sent with cross-site requests. `Strict`, `Lax`, and `None` provide different behaviors. `SameSite=None` requires `Secure` in modern browsers.

### Q42. localStorage vs sessionStorage vs cookies?

localStorage persists until cleared; sessionStorage is scoped to a tab session; cookies can be automatically sent to matching servers and can have HttpOnly, Secure, and SameSite attributes. localStorage and sessionStorage are readable by JavaScript, while HttpOnly cookies are not.

### Q43. Which is safer for session credentials: localStorage or HttpOnly cookies?

HttpOnly cookies prevent JavaScript from directly reading the credential, reducing token theft through some XSS scenarios. However, cookies are automatically sent and require appropriate CSRF defenses. Security depends on the complete design, not one storage choice alone.

### Q44. Do cookies eliminate XSS?

No. HttpOnly prevents scripts from reading the cookie value, but XSS can still execute actions in the user's browser. Output encoding, sanitization, CSP, and secure coding remain important.

### Q45. Do cookies create CSRF risk?

They can, because browsers automatically attach matching cookies to requests. Use appropriate SameSite settings, CSRF tokens, and origin checks based on the application's architecture.

---

## E. Context API and React Implementation

### Q46. Why use Context API for authentication?

Many components need access to the current user, authentication status, loading state, and login/logout functions. Context shares this state without passing props through every intermediate component.

### Q47. Is Context API an authentication mechanism?

No. Context is a React state-sharing mechanism. It can represent authentication information in the UI, but the backend must authenticate requests and enforce permissions.

### Q48. What should AuthContext contain?

Common values include the current user, loading status, authentication status, and functions such as login, logout, and possibly refresh or profile reload. Avoid putting unnecessary sensitive data in global state.

### Q49. Why do we need a loading state in AuthContext?

The application may need to verify an existing session when it starts. A loading state prevents the UI from redirecting users before that check finishes.

### Q50. Why create a custom `useAuth` hook?

A custom hook centralizes access to the authentication context and can provide a clear error if it is used outside its provider.

### Q51. Where should AuthProvider be placed?

It should wrap the parts of the component tree that need authentication state, commonly near the application root. Its position relative to the router depends on whether the provider needs router functionality.

### Q52. What is the purpose of `/auth/me`?

It is a common endpoint that returns the currently authenticated user's profile based on the session or token supplied with the request. It helps restore user state after a page refresh.

### Q53. Should the access token be stored in Context?

A short-lived access token can be held in memory in some architectures, but Context itself is not secure storage. Avoid exposing long-lived credentials unnecessarily and choose a storage strategy based on XSS and CSRF risks.

### Q54. How do you update the Navbar after login?

Store the authenticated user in shared state such as Context. The Navbar reads that state and renders the appropriate authenticated or unauthenticated UI.

### Q55. What happens to Context state after refreshing the browser?

React state is recreated, so the application must restore authentication from a trusted session mechanism, typically by calling the backend or using a server-provided initial state.

---

## F. Protected Routes and Authorization

### Q56. What is a protected route?

A protected route is a frontend route that requires a condition such as an authenticated user. It controls navigation and UI visibility, while the backend separately protects the underlying data and operations.

### Q57. How do you implement a protected route in React Router?

Create a wrapper that reads authentication state. While loading, show a loading UI; if unauthenticated, redirect to login; otherwise render an `Outlet` or the protected child element.

### Q58. What is the purpose of `Navigate`?

React Router's `Navigate` component performs a declarative navigation when rendered. It is commonly used to redirect unauthenticated users to a login route.

### Q59. What is the purpose of `Outlet` in a protected route?

`Outlet` renders the matching child route. A parent route can therefore check authentication once and render its nested protected routes when access is allowed.

### Q60. How do you redirect users to the page they originally requested after login?

Store the requested location in navigation state when redirecting to login. After successful login, navigate back to that location, using a safe validated internal destination.

### Q61. What is role-based access control?

RBAC grants access according to assigned roles. For example, an admin role may be allowed to manage users while a regular user may only view their profile.

### Q62. What is permission-based access control?

It checks specific actions or resources, such as `invoice:read` or `user:update`. It can provide finer-grained access than broad role checks.

### Q63. How do you protect an admin route?

The frontend can check the authenticated user's role or permissions and render the route only when the requirement is met. The backend must independently enforce the same restriction on admin APIs.

### Q64. What is the difference between 401 and 403?

401 indicates that valid authentication credentials are missing or unacceptable. 403 indicates that the server refuses the request, commonly because the authenticated user lacks permission.

### Q65. Can a user access an admin API by manually changing their role in React DevTools?

A correctly implemented backend will reject the request because it derives identity and permissions from trusted server-side validation, not from editable frontend state.

---

## G. Axios and Interceptors

### Q66. Why create an Axios instance?

It centralizes base URL, shared headers, credentials, timeouts, and interceptors. This keeps API configuration consistent and avoids repeating it across components.

### Q67. What is a request interceptor?

A request interceptor runs before an HTTP request is sent. It can attach an access token, add common headers, or apply shared request configuration.

### Q68. What is a response interceptor?

A response interceptor processes responses or errors before they reach the calling code. It can centralize handling for unauthorized responses, logging, or common API error formats.

### Q69. How do you attach a bearer token using an interceptor?

Read the current short-lived access token from the application's chosen token store and set `config.headers.Authorization` to `Bearer` followed by the token. The backend must validate it.

### Q70. How do you handle a 401 response globally?

A response interceptor can detect a 401, attempt a refresh if the architecture supports it, and retry the original request once. If refresh fails, clear authentication state and redirect or show a login flow.

### Q71. What is an interceptor cleanup/ejection?

Axios returns an interceptor ID when one is registered. Calling `eject` with that ID removes it. This is important when interceptors are registered inside components or effects to prevent duplicate handlers.

### Q72. Why can multiple refresh requests be a problem?

If many API calls fail at the same time, each may attempt to refresh independently. This can cause token rotation races and inconsistent state. Use a shared refresh operation or queue concurrent requests.

### Q73. Should every 401 trigger a refresh request?

No. A 401 from the login or refresh endpoint may mean credentials are invalid. Refresh logic should exclude relevant endpoints, avoid retry loops, and retry the original request at most once.

### Q74. Why should an Axios error be re-thrown?

Returning `Promise.reject(error)` allows the calling component or service to handle the failure. Swallowing the error can make failed requests appear successful or hide important failure states.

---

## H. Security

### Q75. What is XSS?

Cross-site scripting is a vulnerability where attacker-controlled script executes in a user's browser within the application's origin. It can steal JavaScript-accessible data or perform actions as the user.

### Q76. How can you prevent XSS in React?

Avoid unsafe HTML injection, render untrusted content as text, sanitize HTML when necessary, use a strong Content Security Policy, and keep dependencies updated. React's normal text rendering escapes content, but unsafe escape hatches still require care.

### Q77. What is CSRF?

Cross-site request forgery tricks a browser into sending an authenticated request to a site where the browser already has credentials, often because cookies are automatically attached.

### Q78. How do you prevent CSRF?

Use SameSite cookie settings, CSRF tokens where appropriate, and origin or referer validation. The exact controls depend on the session architecture and cross-site requirements.

### Q79. What is the difference between XSS and CSRF?

XSS executes attacker-controlled script in the trusted site's browser context. CSRF causes a browser to send an unwanted authenticated request to a site. They are different threats and require different defenses.

### Q80. What is brute-force login attack?

It is an attempt to guess credentials through repeated login attempts. Rate limiting, throttling, monitoring, MFA, and safe error responses help reduce the risk.

### Q81. What is credential stuffing?

Attackers use username/password combinations leaked from other services. Defenses include MFA, rate limiting, breached-password screening, and monitoring suspicious login activity.

### Q82. Is CORS an authentication mechanism?

No. CORS is a browser-enforced cross-origin access policy. It does not authenticate users or replace backend authorization, and non-browser clients are not constrained by browser CORS enforcement.

### Q83. Why should HTTPS be used for authentication?

HTTPS encrypts traffic in transit and helps protect credentials, cookies, and tokens from network interception and tampering.

### Q84. Why should secrets not be stored in React environment variables?

Frontend environment variables are generally bundled into client-side assets and can be inspected by users. Only public configuration belongs in the frontend; private secrets must remain on the server.

---

## I. OAuth, OIDC, and 2FA

### Q85. What is two-factor authentication?

2FA requires two different categories of evidence to verify identity, such as a password and a time-based code from an authenticator app.

### Q86. What is TOTP?

Time-based One-Time Password is an algorithm that generates short-lived codes based on a shared secret and current time. Authenticator applications commonly support it.

### Q87. What is OAuth 2.0?

OAuth 2.0 is an authorization framework that lets a client obtain limited access to resources on behalf of a resource owner. It is not, by itself, a user authentication protocol.

### Q88. What is OpenID Connect?

OpenID Connect is an identity layer built on OAuth 2.0. It provides a standardized way for a client to verify a user's identity and obtain identity claims.

### Q89. What is the Authorization Code Flow with PKCE?

It is a flow in which the client obtains an authorization code and exchanges it using a proof key. PKCE helps protect the authorization code from interception, especially for public clients such as browser and mobile applications.

### Q90. Why should a client secret not be placed in React?

React code is delivered to the browser, so users can inspect bundled assets and network requests. A client secret cannot be kept confidential in a public browser application.

---

## J. Advanced and Tricky Questions

### Q91. Is a decoded JWT a trusted JWT?

No. Decoding only reveals its contents. The backend must verify the signature and validate claims such as issuer, audience, and expiration before trusting it.

### Q92. If a JWT is signed, can its payload be changed?

Changing the payload invalidates the signature. A receiver that correctly verifies the signature will reject the modified token. The payload is still readable unless the token is encrypted.

### Q93. Why should access tokens be short-lived?

Short lifetimes reduce the period during which a stolen access token can be used. Refresh mechanisms can provide a smoother user experience while keeping access credentials short-lived.

### Q94. Does deleting a token from the browser revoke it?

Not necessarily. It removes that browser's copy, but a stolen token may remain valid until expiry unless the server supports revocation or session invalidation.

### Q95. Can HttpOnly cookies completely prevent token theft?

They prevent JavaScript from directly reading the cookie, but they do not eliminate every risk. XSS can still perform actions in the browser, and cookie-based sessions need CSRF defenses.

### Q96. Why is frontend role checking not enough?

Frontend checks can be modified or bypassed. The backend must derive the user's identity from validated credentials and enforce permissions on every protected operation.

### Q97. What is refresh-token rotation?

The server issues a new refresh token when the old one is used and invalidates the previous token. Reuse of an old token can be detected and may trigger session revocation.

### Q98. What is the principle of least privilege?

Users and services should receive only the permissions necessary to perform their tasks. This limits damage if an account or credential is compromised.

### Q99. Why should authentication state have a loading status?

Without it, the app may briefly show protected content or redirect a valid user before the session restoration request finishes. A loading state prevents incorrect decisions during initialization.

### Q100. What is the difference between a session-based and token-based authentication architecture?

In session-based authentication, the server maintains session state and the browser usually sends a session identifier cookie. In token-based authentication, the client presents a token that the server validates. Both approaches need secure credential handling, expiration, logout, and authorization controls.

### Q101. Can JWT be used for logout?

JWT itself does not define logout. An application can remove local credentials, revoke refresh tokens, maintain server-side session state, or use a deny-list strategy. The exact approach depends on the architecture.

### Q102. What is account enumeration?

It is discovering whether an account exists by observing differences in login, registration, or password-reset responses. Generic messages and consistent response behavior help reduce this risk.

### Q103. Why should password reset tokens be single-use?

A single-use token cannot be replayed after a successful password reset. Short expiration and secure storage further reduce the impact of token exposure.

### Q104. What is the difference between authentication and identity federation?

Authentication verifies a user's identity. Identity federation allows an application to trust an external identity provider to authenticate users under an agreed protocol, such as OIDC or SAML.

### Q105. What is session fixation?

Session fixation is an attack where an attacker causes a victim to use a session identifier known to the attacker. Applications should issue a new session identifier after authentication and other privilege changes.

---

## K. Scenario-Based Questions

### Q106. A user logs in successfully, but after refreshing the page they are logged out. What would you investigate?

I would check whether authentication state exists only in React memory. I would implement session restoration through a trusted backend endpoint, such as `/auth/me`, and keep a loading state until the check completes.

### Q107. A normal user manually enters `/admin` in the address bar. How would you handle it?

I would use a frontend role or permission guard to redirect the user to an unauthorized page. More importantly, I would ensure the backend rejects admin API requests from users without admin permission.

### Q108. The API returns 401 for several requests at the same time. How would you avoid multiple refresh calls?

I would use a shared refresh promise or queue. Only one refresh request should run; other failed requests wait for its result. After success, retry each original request once. If refresh fails, reject the queue and clear the session.

### Q109. A user's access token expires while they are using the dashboard. What should happen?

The API should reject the expired token. The client can attempt a controlled refresh if supported. If refresh succeeds, retry the request; otherwise, clear authentication state and ask the user to log in again.

### Q110. A developer stores the JWT in localStorage. What security concern would you raise?

JavaScript can read localStorage, so a successful XSS attack may steal the token. I would assess the threat model and consider HttpOnly cookies or short-lived in-memory access tokens with protected refresh credentials.

### Q111. The frontend hides the Delete button from normal users, but they can still delete records using Postman. Why?

The backend endpoint is not enforcing authorization. Hiding a button only changes the UI. The server must check the authenticated user's permission before performing the delete operation.

### Q112. Login works in Postman but fails in the browser. What would you inspect?

I would inspect the browser Network tab and backend CORS configuration, allowed origin, credentials setting, cookie attributes, HTTPS, preflight requests, request payload, and response errors. Postman does not enforce browser CORS rules.

### Q113. The browser does not send the authentication cookie. What could be wrong?

I would check the cookie's Domain, Path, Secure, SameSite, and expiration attributes, whether the request uses credentials, whether frontend and backend origins are configured correctly, and whether the request uses HTTPS when required.

### Q114. After logout, pressing the browser Back button shows the dashboard. Is the user still authenticated?

Not necessarily. The browser may display a cached page. I would ensure protected APIs reject the invalidated session, clear client state, and consider appropriate cache-control headers for sensitive pages. I would test a fresh protected API request.

### Q115. A user changes `role: "user"` to `role: "admin"` in DevTools. How do you prevent privilege escalation?

The backend must not trust client-supplied role values. It should load roles and permissions from trusted server-side records associated with the validated identity and enforce authorization for every protected action.

### Q116. An interceptor causes an infinite 401 retry loop. How would you fix it?

I would mark a request as already retried, exclude login and refresh endpoints from refresh handling, retry the original request only once, and end the session if refresh fails.

### Q117. Users are randomly logged out when many API calls happen together. What might be the cause?

Concurrent refresh requests may be rotating or invalidating the same refresh token. I would centralize refresh into a single shared operation and queue waiting requests.

### Q118. How would you design authentication for an admin and user dashboard in one React application?

I would maintain a shared authentication context, restore the current session on startup, use protected routes for authenticated pages, and add role or permission guards for admin pages. The backend would enforce the same access rules on every API endpoint.

### Q119. How would you test that protected APIs are actually secure?

I would call the endpoints directly without credentials, with invalid or expired credentials, and with a valid low-privilege user. I would verify that unauthorized and forbidden requests are rejected even when the frontend is bypassed.

### Q120. How would you explain your React authentication implementation in an interview?

I would explain the flow from login form submission to backend verification, session or token establishment, authentication state management through Context, session restoration, protected routes, role-based access, Axios request handling, logout, and backend authorization. I would also explain how I handle expired sessions and security risks such as XSS and CSRF.

---

## Final Interview Preparation Checklist

- [ ] I can explain authentication vs authorization clearly.
- [ ] I can describe registration, login, logout, and session restoration.
- [ ] I can explain JWT structure, signing, claims, and expiration.
- [ ] I can compare access tokens and refresh tokens.
- [ ] I can explain cookie attributes and browser storage trade-offs.
- [ ] I can implement AuthContext and a custom `useAuth` hook.
- [ ] I can implement protected routes with React Router.
- [ ] I can implement role-based and permission-based guards.
- [ ] I can explain why backend authorization is mandatory.
- [ ] I can configure Axios instances and interceptors.
- [ ] I can explain refresh-token rotation and concurrent refresh handling.
- [ ] I can explain XSS, CSRF, CORS, and brute-force attacks.
- [ ] I can explain OAuth 2.0, OIDC, PKCE, and 2FA.
- [ ] I can answer the scenario-based questions with implementation reasoning.
