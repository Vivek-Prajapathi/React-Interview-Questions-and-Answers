# Web Security and Safe React Patterns

## 1. React escaping and XSS

React escapes text interpolated into JSX by default, which helps prevent many injection attacks. However, this protection is not a reason to trust arbitrary input everywhere.

Avoid `dangerouslySetInnerHTML` unless HTML is genuinely required and has been sanitized with a trusted sanitizer. Never concatenate untrusted strings into HTML.

## 2. Cross-Site Scripting (XSS)

XSS occurs when attacker-controlled content executes as script in another user's browser. Risks include unsafe HTML injection, unsafe URL handling, and third-party script compromise.

Defenses include context-appropriate output encoding, sanitizing permitted HTML, Content Security Policy, dependency hygiene, and avoiding unsafe DOM APIs.

## 3. Authentication and authorization

Authentication verifies who the user is. Authorization determines what the user is allowed to do. Hiding an admin button in React is not authorization; the backend must enforce permissions on every protected operation.

## 4. Token storage and sessions

Token storage involves trade-offs. JavaScript-accessible storage can be exposed by XSS. HttpOnly cookies reduce direct JavaScript access but require appropriate CSRF protections and cookie settings. Follow the application's security architecture rather than copying a universal storage rule.

Use HTTPS, secure cookie settings where applicable, short-lived credentials, refresh-token rotation where supported, and server-side revocation strategies.

## 5. CSRF and CORS

CSRF tricks a browser into sending an authenticated request to a site. Cookie-based authentication may need SameSite settings and CSRF tokens depending on the architecture.

CORS is a browser-enforced mechanism controlling which origins can read cross-origin responses. CORS is not authentication and does not prevent all cross-origin requests.

## 6. Secrets and environment variables

Do not put private API keys or server secrets in frontend environment variables. Values bundled into a client application are visible to users. Keep secrets on the server and expose only intentionally public configuration.

## 7. Dependency and URL safety

Keep dependencies updated, review package provenance, and validate untrusted URLs before navigation or embedding. Avoid constructing dangerous `javascript:` URLs from user input.

## 8. Common mistakes

- Assuming React makes the whole application XSS-proof.
- Using `dangerouslySetInnerHTML` with unsanitized content.
- Treating hidden UI as backend authorization.
- Putting private keys in frontend bundles.
- Assuming CORS is an authentication mechanism.
- Choosing token storage without considering XSS and CSRF risks.
