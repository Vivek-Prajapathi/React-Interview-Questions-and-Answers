# Chapter 7 – React Authentication and Authorization

A complete learning reference for implementing authentication and authorization in React applications.

> **Learning approach:** Understand each concept, study its syntax and flow, implement the examples, then revise the interview questions in the companion file. Examples use React, React Router, Axios, and JavaScript. Adapt endpoint URLs and response shapes to your backend.

## Table of Contents

- [1. Authentication and Authorization Fundamentals](#1-authentication-and-authorization-fundamentals)
- [2. Identity, Credentials, and User Sessions](#2-identity-credentials-and-user-sessions)
- [3. Authentication Flow](#3-authentication-flow)
- [4. Password Handling and Registration](#4-password-handling-and-registration)
- [5. Login and Logout](#5-login-and-logout)
- [6. Tokens: JWT, Access Tokens, and Refresh Tokens](#6-tokens-jwt-access-tokens-and-refresh-tokens)
- [7. Cookies, Local Storage, and Session Storage](#7-cookies-local-storage-and-session-storage)
- [8. Context API for Authentication State](#8-context-api-for-authentication-state)
- [9. Protected Routes](#9-protected-routes)
- [10. Role-Based and Permission-Based Access Control](#10-role-based-and-permission-based-access-control)
- [11. Axios Instance and Interceptors](#11-axios-instance-and-interceptors)
- [12. Refresh Token and Expired Sessions](#12-refresh-token-and-expired-sessions)
- [13. Authentication Forms and Validation](#13-authentication-forms-and-validation)
- [14. Two-Factor Authentication](#14-two-factor-authentication)
- [15. OAuth and Social Login](#15-oauth-and-social-login)
- [16. Security Threats and Best Practices](#16-security-threats-and-best-practices)
- [17. Authentication Architecture and Folder Structure](#17-authentication-architecture-and-folder-structure)
- [18. Complete Practical Implementation](#18-complete-practical-implementation)
- [19. Testing Authentication](#19-testing-authentication)
- [20. Deployment and Production Checklist](#20-deployment-and-production-checklist)
- [21. Common Mistakes](#21-common-mistakes)
- [22. Final Revision Checklist](#22-final-revision-checklist)

---

## 1. Authentication and Authorization Fundamentals

### 1.1 What is authentication?

Authentication is the process of verifying the identity of a user. It answers: **Who are you?**

A user may prove their identity using an email and password, a one-time password (OTP), a passkey, or an identity provider.

### 1.2 What is authorization?

Authorization determines what an authenticated user is allowed to access or perform. It answers: **What are you allowed to do?**

For example, a normal user may view products, while an administrator may create, update, and delete products.

### 1.3 Authentication vs authorization

| Authentication | Authorization |
|---|---|
| Verifies identity | Checks permissions |
| Happens before access decisions | Follows identity verification |
| Example: login | Example: admin-only dashboard |
| Answers “Who are you?” | Answers “What can you do?” |

A user can be authenticated but not authorized to access a particular resource.

### 1.4 Related terms

- **Identity:** The account or person represented by the application.
- **Credential:** Evidence used to prove identity, such as a password or passkey.
- **Principal:** The authenticated identity used by a system when making access decisions.
- **Session:** The period or server-side record representing an authenticated interaction.
- **Permission:** A specific allowed action, such as `users:read`.
- **Role:** A named collection of permissions, such as `admin` or `member`.
- **Identity provider (IdP):** A system that authenticates users for an application.

---

## 2. Identity, Credentials, and User Sessions

A typical user record may contain an ID, name, email, password hash, role, and account status. The backend must never return a user's password or password hash to the browser.

A session can be represented by a server-side session record or by tokens. The browser presents the relevant session credential with requests so the server can identify the user.

**Important:** React state is not proof of identity. Hiding a button or changing a page in React does not secure a backend endpoint. The server must validate every protected request.

---

## 3. Authentication Flow

### 3.1 Registration flow

1. User enters registration details.
2. React validates required fields and basic input format.
3. React sends the data to the registration API over HTTPS.
4. Backend validates the data and checks whether the account already exists.
5. Backend securely hashes the password and stores the account.
6. Backend may send an email verification link or OTP.
7. User verifies the account if verification is required.

### 3.2 Login flow

1. User submits email and password.
2. React sends credentials to the login endpoint over HTTPS.
3. Backend verifies the credentials.
4. Backend creates a session or issues tokens.
5. The browser stores or receives the session credential according to the chosen security design.
6. React obtains the authenticated user's profile and updates the UI.
7. Protected API requests include the required credential.

### 3.3 Logout flow

1. User clicks Logout.
2. React calls the backend logout endpoint when the architecture supports server-side sessions or token revocation.
3. The server invalidates the session or refresh token as applicable.
4. The browser clears locally held authentication state and credentials it is responsible for clearing.
5. React redirects the user to a public route.

Clearing React state alone does not invalidate a server-side session or a copied token.

---

## 4. Password Handling and Registration

### 4.1 Password security

Passwords must be hashed on the server using a password-hashing algorithm designed for this purpose, such as Argon2id or bcrypt, with appropriate parameters and salts. Do not encrypt passwords for later recovery, and never store plaintext passwords.

React should not hash a password as a substitute for backend password hashing.

### 4.2 Registration component example

```jsx
import { useState } from "react";
import axios from "axios";

export default function Register() {
  const [form, setForm] = useState({
    name: "",
    email: "",
    password: "",
  });
  const [message, setMessage] = useState("");
  const [loading, setLoading] = useState(false);

  function handleChange(event) {
    setForm({ ...form, [event.target.name]: event.target.value });
  }

  async function handleSubmit(event) {
    event.preventDefault();
    setMessage("");

    if (!form.name.trim() || !form.email.trim() || !form.password) {
      setMessage("Please complete all fields.");
      return;
    }

    try {
      setLoading(true);
      await axios.post("/api/auth/register", form);
      setMessage("Registration successful. Check whether verification is required.");
    } catch (error) {
      setMessage(error.response?.data?.message || "Registration failed.");
    } finally {
      setLoading(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={form.name} onChange={handleChange}
        placeholder="Name" autoComplete="name" />
      <input name="email" type="email" value={form.email}
        onChange={handleChange} placeholder="Email" autoComplete="email" />
      <input name="password" type="password" value={form.password}
        onChange={handleChange} placeholder="Password"
        autoComplete="new-password" />
      <button disabled={loading}>
        {loading ? "Creating account..." : "Register"}
      </button>
      <p role="status">{message}</p>
    </form>
  );
}
```

This is a frontend example only. Server-side validation, rate limiting, password hashing, email verification, and abuse prevention are backend responsibilities.

---

## 5. Login and Logout

### 5.1 Login component example

The exact response shape depends on your backend. This example assumes the API returns a user object and an access token. It demonstrates the request flow, not a recommendation to store long-lived tokens in JavaScript-accessible storage.

```jsx
import { useState } from "react";
import axios from "axios";

export default function Login({ onLogin }) {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  async function handleSubmit(event) {
    event.preventDefault();
    setError("");

    try {
      setLoading(true);
      const response = await axios.post(
        "/api/auth/login",
        { email, password },
        { withCredentials: true }
      );

      // Example assumes the backend uses a secure cookie for the session.
      onLogin(response.data.user);
    } catch (err) {
      setError(err.response?.data?.message || "Invalid login details.");
    } finally {
      setLoading(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input type="email" value={email} onChange={e => setEmail(e.target.value)}
        autoComplete="username" required />
      <input type="password" value={password}
        onChange={e => setPassword(e.target.value)}
        autoComplete="current-password" required />
      <button disabled={loading}>
        {loading ? "Signing in..." : "Login"}
      </button>
      {error && <p role="alert">{error}</p>}
    </form>
  );
}
```

### 5.2 Logout

```js
async function logout() {
  try {
    await api.post("/auth/logout");
  } finally {
    // Clear application authentication state even if the request fails.
    setUser(null);
  }
}
```

For cookie-based sessions, the backend should expire the session cookie and invalidate the session. For refresh-token systems, the backend should revoke the refresh token where supported.

---

## 6. Tokens: JWT, Access Tokens, and Refresh Tokens

### 6.1 What is a token?

A token is a credential that a client presents to prove an authenticated session or authority. The server validates it before granting access.

### 6.2 What is JWT?

JWT means JSON Web Token. It is a compact token format commonly used to carry claims between parties.

A signed JWT commonly has three Base64URL-encoded parts separated by dots:

`header.payload.signature`

- **Header:** Metadata such as the signing algorithm and token type.
- **Payload:** Claims such as subject (`sub`), issuer (`iss`), audience (`aud`), and expiration (`exp`).
- **Signature:** Allows the receiver to verify that the token has not been modified and was signed by a trusted key.

A signed JWT is generally readable by anyone who obtains it. Signing does not encrypt the payload. Do not put passwords, secrets, or sensitive personal data in ordinary JWT claims.

### 6.3 Access token

An access token is a credential used to access protected APIs. It should generally have a short lifetime.

### 6.4 Refresh token

A refresh token is used to obtain a new access token without asking the user to log in again. It is usually longer-lived and must be protected carefully. Secure designs commonly use rotation and server-side revocation or reuse detection.

### 6.5 Access token vs refresh token

| Access token | Refresh token |
|---|---|
| Sent to protected APIs | Sent to token-refresh endpoint |
| Short-lived | Usually longer-lived |
| Grants API access | Obtains a new access token |
| Exposure can allow API access | Exposure can allow session renewal |

### 6.6 JWT validation

The backend must validate the signature and relevant claims, including expiration, issuer, and audience. It must use an explicitly allowed algorithm and trusted keys. Decoding a JWT in React is not validation and does not prove the token is genuine.

### 6.7 Authorization header

For bearer-token APIs, the conventional header is:

```http
Authorization: Bearer <access-token>
```

The backend must validate the token on every protected request.

---

## 7. Cookies, Local Storage, and Session Storage

### 7.1 localStorage

- Persists across browser restarts until cleared.
- Accessible to JavaScript on the same origin.
- Therefore, an XSS vulnerability can expose tokens stored there.
- Not automatically sent with HTTP requests.

### 7.2 sessionStorage

- Scoped to a tab's browsing session.
- Usually cleared when the tab session ends.
- Accessible to JavaScript, so it is also exposed to successful XSS.
- Not automatically sent with HTTP requests.

### 7.3 Cookies

Cookies can be configured with security attributes:

- `HttpOnly`: Prevents JavaScript from reading the cookie.
- `Secure`: Sends the cookie only over secure HTTPS connections.
- `SameSite`: Controls cross-site cookie sending behavior (`Strict`, `Lax`, or `None`).
- `Path` and `Domain`: Scope where the cookie is sent.
- `Expires` or `Max-Age`: Control lifetime.

An HttpOnly cookie cannot be read using `document.cookie`; the browser sends it automatically when cookie rules match.

### 7.4 Comparison

| Feature | localStorage | sessionStorage | HttpOnly cookie |
|---|---|---|---|
| JavaScript-readable | Yes | Yes | No |
| Persists after browser restart | Yes | Normally no | Depends on expiry |
| Automatically sent to matching server | No | No | Yes |
| XSS can directly read stored value | Yes | Yes | No |
| CSRF considerations | Usually less direct | Usually less direct | Important |

Cookie-based authentication still needs CSRF protection appropriate to the application. `SameSite` is useful but should not be treated as the only defense in every architecture.

### 7.5 Choosing a storage strategy

For many browser applications, a strong approach is to keep session credentials in Secure, HttpOnly cookies and use appropriate CSRF defenses. Another architecture uses short-lived access tokens held in memory with refresh tokens in protected cookies. The correct design depends on the backend, deployment topology, and threat model.

Do not claim that localStorage is secure merely because it is convenient. Do not assume cookies are automatically secure without correct attributes and CSRF controls.

---

## 8. Context API for Authentication State

### 8.1 Why use Context?

Authentication state is needed by many components: navigation bars, dashboards, protected routes, and profile menus. Context lets components read shared state without passing props through every intermediate component.

Context is a frontend state-sharing mechanism. It is not an authentication server and does not secure API endpoints.

### 8.2 Create AuthContext

```jsx
import { createContext, useContext, useEffect, useState } from "react";
import api from "../api/axios";

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function loadCurrentUser() {
      try {
        const response = await api.get("/auth/me");
        setUser(response.data.user);
      } catch {
        setUser(null);
      } finally {
        setLoading(false);
      }
    }

    loadCurrentUser();
  }, []);

  async function login(credentials) {
    const response = await api.post("/auth/login", credentials);
    setUser(response.data.user);
    return response.data;
  }

  async function logout() {
    try {
      await api.post("/auth/logout");
    } finally {
      setUser(null);
    }
  }

  const value = {
    user,
    loading,
    isAuthenticated: Boolean(user),
    login,
    logout,
  };

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error("useAuth must be used inside AuthProvider");
  }
  return context;
}
```

### 8.3 Wrap the application

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import App from "./App";
import { AuthProvider } from "./context/AuthContext";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <BrowserRouter>
      <AuthProvider>
        <App />
      </AuthProvider>
    </BrowserRouter>
  </StrictMode>
);
```

### 8.4 Use authentication state

```jsx
import { useAuth } from "../context/AuthContext";

function Navbar() {
  const { user, isAuthenticated, logout } = useAuth();

  return (
    <nav>
      {isAuthenticated ? (
        <>
          <span>Welcome, {user.name}</span>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <a href="/login">Login</a>
      )}
    </nav>
  );
}
```

For client-side navigation, use React Router's `Link` rather than a normal anchor when appropriate.

---

## 9. Protected Routes

### 9.1 What is a protected route?

A protected route is a route that the frontend displays only when the current authentication state meets a condition. The backend must independently protect the corresponding data and operations.

### 9.2 RequireAuth component

This example uses React Router's declarative routing API.

```jsx
import { Navigate, Outlet, useLocation } from "react-router-dom";
import { useAuth } from "../context/AuthContext";

export default function RequireAuth() {
  const { isAuthenticated, loading } = useAuth();
  const location = useLocation();

  if (loading) return <p>Checking session...</p>;

  if (!isAuthenticated) {
    return <Navigate to="/login" replace state={{ from: location }} />;
  }

  return <Outlet />;
}
```

### 9.3 Configure routes

```jsx
import { Routes, Route } from "react-router-dom";
import RequireAuth from "./components/RequireAuth";
import Login from "./pages/Login";
import Home from "./pages/Home";
import Dashboard from "./pages/Dashboard";

export default function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/login" element={<Login />} />

      <Route element={<RequireAuth />}>
        <Route path="/dashboard" element={<Dashboard />} />
      </Route>
    </Routes>
  );
}
```

### 9.4 Return to the originally requested page

```jsx
import { useLocation, useNavigate } from "react-router-dom";
import { useAuth } from "../context/AuthContext";

function LoginPage() {
  const { login } = useAuth();
  const navigate = useNavigate();
  const location = useLocation();

  const destination = location.state?.from?.pathname || "/dashboard";

  async function handleLogin(credentials) {
    await login(credentials);
    navigate(destination, { replace: true });
  }

  // Render a form that calls handleLogin with submitted credentials.
}
```

Validate any redirect destination. Do not accept arbitrary untrusted external URLs as post-login redirect targets.

---

## 10. Role-Based and Permission-Based Access Control

### 10.1 Role-based access control (RBAC)

RBAC grants permissions based on roles. Common examples are `admin`, `manager`, and `user`.

### 10.2 Role guard

```jsx
import { Navigate, Outlet } from "react-router-dom";
import { useAuth } from "../context/AuthContext";

export default function RequireRole({ allowedRoles }) {
  const { user, loading, isAuthenticated } = useAuth();

  if (loading) return <p>Loading...</p>;
  if (!isAuthenticated) return <Navigate to="/login" replace />;

  if (!allowedRoles.includes(user.role)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return <Outlet />;
}
```

### 10.3 Route configuration

```jsx
<Route element={<RequireAuth />}>
  <Route path="/dashboard" element={<Dashboard />} />

  <Route element={<RequireRole allowedRoles={["admin"]} />}>
    <Route path="/admin" element={<AdminDashboard />} />
    <Route path="/admin/users" element={<ManageUsers />} />
  </Route>
</Route>
```

### 10.4 Permissions

Roles can become too broad as an application grows. A permission model can express specific actions, such as `users:read`, `users:update`, or `reports:export`.

```js
const canManageUsers = user?.permissions?.includes("users:update");
```

Frontend checks improve the user experience, but the backend must enforce the same permissions using trusted server-side data. Never trust a role or permission sent by the browser without server validation.

### 10.5 401 vs 403

- **401 Unauthorized:** The request lacks valid authentication credentials or the credentials are invalid.
- **403 Forbidden:** The server understood the request but refuses access, commonly because the authenticated user lacks permission.

---

## 11. Axios Instance and Interceptors

### 11.1 Why create an Axios instance?

An Axios instance centralizes the API base URL, shared configuration, credentials, and interceptors. This avoids repeating configuration in every component.

### 11.2 Create an instance

For a cookie-based session:

```js
// src/api/axios.js
import axios from "axios";

const api = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  withCredentials: true,
  headers: {
    "Content-Type": "application/json",
  },
});

export default api;
```

Set `VITE_API_BASE_URL` in the appropriate environment file, for example:

```env
VITE_API_BASE_URL=https://api.example.com
```

Vite exposes variables prefixed with `VITE_` to client-side code. Never put private API secrets in frontend environment variables.

### 11.3 Request interceptor

A request interceptor runs before a request is sent. In a bearer-token architecture where the access token is held in memory:

```js
api.interceptors.request.use(
  (config) => {
    const token = authTokenStore.getAccessToken();

    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
  },
  (error) => Promise.reject(error)
);
```

`authTokenStore` is an illustrative module that your application must implement. Avoid reading a token from localStorage automatically without considering the XSS risk.

### 11.4 Response interceptor

A response interceptor can centralize common response handling:

```js
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Handle an expired or invalid session centrally.
      // Avoid redirect loops and duplicate logout handling.
    }

    return Promise.reject(error);
  }
);
```

Do not silently swallow errors. Return the rejected promise so calling code can handle it.

### 11.5 Interceptor cleanup

When registering interceptors inside a React component or effect, eject them during cleanup to avoid duplicate registrations. Prefer registering application-wide interceptors once in a dedicated module when possible.

---

## 12. Refresh Token and Expired Sessions

### 12.1 Why refresh tokens?

A short-lived access token limits the time window in which a stolen token can be used. A refresh token allows the application to obtain a new access token without asking the user to re-enter credentials each time.

### 12.2 Typical refresh flow

1. Client calls a protected endpoint with an access token.
2. Server returns an authentication error because the access token expired.
3. Client sends the refresh credential to the refresh endpoint.
4. Server validates the refresh credential and its session status.
5. Server issues a new access token and may rotate the refresh token.
6. Client retries the original request once.
7. If refresh fails, client clears authentication state and asks the user to log in again.

### 12.3 Important implementation requirements

- Prevent multiple simultaneous requests from starting multiple refresh operations.
- Retry the original request at most once.
- Do not attempt refresh for the refresh endpoint itself.
- Handle refresh failure by ending the local session.
- Use refresh-token rotation and server-side revocation where supported.
- Protect cookie-based refresh endpoints against CSRF.
- Avoid infinite retry loops.

### 12.4 Simplified refresh interceptor pattern

This is an architectural illustration; a production implementation needs a shared refresh promise or request queue and a backend contract.

```js
let isRefreshing = false;
let waitingRequests = [];

function resolveWaitingRequests(error, token = null) {
  waitingRequests.forEach(({ resolve, reject }) => {
    if (error) reject(error);
    else resolve(token);
  });
  waitingRequests = [];
}

api.interceptors.response.use(
  response => response,
  async error => {
    const originalRequest = error.config;

    if (
      error.response?.status !== 401 ||
      originalRequest?._retry ||
      originalRequest?.url?.includes("/auth/refresh")
    ) {
      return Promise.reject(error);
    }

    originalRequest._retry = true;

    if (isRefreshing) {
      return new Promise((resolve, reject) => {
        waitingRequests.push({
          resolve: token => {
            originalRequest.headers.Authorization = `Bearer ${token}`;
            resolve(api(originalRequest));
          },
          reject,
        });
      });
    }

    isRefreshing = true;

    try {
      const response = await api.post("/auth/refresh");
      const newToken = response.data.accessToken;
      authTokenStore.setAccessToken(newToken);

      resolveWaitingRequests(null, newToken);
      originalRequest.headers.Authorization = `Bearer ${newToken}`;
      return api(originalRequest);
    } catch (refreshError) {
      resolveWaitingRequests(refreshError);
      authTokenStore.clearAccessToken();
      return Promise.reject(refreshError);
    } finally {
      isRefreshing = false;
    }
  }
);
```

This pattern assumes the refresh endpoint can be called through the same instance without triggering a recursive refresh. In production, consider using a separate Axios instance for refresh requests and ensure all queued requests are rejected or resolved correctly.

---

## 13. Authentication Forms and Validation

Common authentication forms include:

- Registration
- Login
- Email verification
- Forgot password
- Reset password
- Change password
- OTP verification
- Two-factor challenge

### 13.1 Client-side validation

Client-side validation improves usability. Validate required fields, email format, password confirmation, and password policy feedback.

### 13.2 Server-side validation

The backend must repeat validation because frontend checks can be bypassed. It must also apply rate limits, safe error handling, and appropriate account recovery controls.

### 13.3 Password visibility toggle

```jsx
import { useState } from "react";

function PasswordField() {
  const [visible, setVisible] = useState(false);

  return (
    <div>
      <input
        type={visible ? "text" : "password"}
        autoComplete="current-password"
      />
      <button type="button" onClick={() => setVisible(value => !value)}>
        {visible ? "Hide password" : "Show password"}
      </button>
    </div>
  );
}
```

Use accessible labels and ensure the visibility button does not submit the form.

---

## 14. Two-Factor Authentication

Two-factor authentication (2FA) requires two different categories of evidence, such as a password plus a time-based one-time password (TOTP) from an authenticator app.

Common factor categories:

- Something you know: password or PIN.
- Something you have: authenticator device, security key.
- Something you are: biometric characteristic.

Typical TOTP flow:

1. User enters username and password.
2. Server verifies the password.
3. Server requests a second factor.
4. User enters a time-based code from an authenticator app.
5. Server validates the code and completes authentication.

Recovery codes should be generated and stored securely. Do not log OTPs, recovery codes, or secrets. SMS OTP has known SIM-swap and interception risks; phishing-resistant passkeys or security keys provide stronger protection.

---

## 15. OAuth and Social Login

OAuth 2.0 is an authorization framework that lets an application obtain limited access to resources. OpenID Connect (OIDC) adds an identity layer on top of OAuth 2.0 and is commonly used for login.

Common providers include Google, Microsoft, and GitHub.

### Authorization Code Flow with PKCE

1. User selects a provider.
2. Application redirects the user to the provider's authorization endpoint.
3. The application uses a `state` value to protect against request forgery and PKCE to protect the authorization code flow.
4. User authenticates and grants consent as needed.
5. Provider redirects back with an authorization code.
6. The code is exchanged for tokens according to the flow.
7. The application validates identity information through the appropriate trusted process.

For browser-based applications, use a well-reviewed OIDC client library and the provider's recommended flow. Do not put a client secret in React code. Validate issuer, audience, signature, nonce where applicable, and state through the correct protocol implementation.

OAuth is not itself an authentication protocol; OIDC is commonly used when the goal is user sign-in.

---

## 16. Security Threats and Best Practices

### 16.1 Cross-site scripting (XSS)

XSS occurs when attacker-controlled script executes in a user's browser in the context of an application.

Defenses include:
- Avoid unsafe HTML injection.
- Sanitize untrusted HTML with a trusted sanitizer when HTML rendering is required.
- Use a strong Content Security Policy.
- Keep dependencies updated.
- Prefer HttpOnly cookies for sensitive session credentials where appropriate.

### 16.2 Cross-site request forgery (CSRF)

CSRF tricks a browser into sending an authenticated request to a site where the browser already has credentials, commonly cookies.

Defenses include:
- Appropriate `SameSite` cookie settings.
- CSRF tokens for state-changing requests when required by the architecture.
- Origin/Referer validation where appropriate.
- Never use GET requests for state-changing operations.

### 16.3 Brute-force and credential stuffing

Attackers repeatedly try passwords or reuse leaked credentials.

Defenses include:
- Rate limiting and progressive throttling.
- Monitoring suspicious login patterns.
- Multi-factor authentication.
- Breached-password checks where appropriate.
- Generic login error messages that avoid account enumeration.

### 16.4 Other protections

- Always use HTTPS.
- Keep access tokens short-lived.
- Protect refresh tokens and support revocation.
- Validate all authorization on the backend.
- Avoid exposing secrets in source code or frontend environment variables.
- Do not log passwords, tokens, OTPs, or session cookies.
- Use secure password reset flows with short-lived, single-use tokens.
- Set suitable security headers.
- Apply least privilege.
- Keep dependencies and authentication libraries updated.

---

## 17. Authentication Architecture and Folder Structure

A maintainable example structure:

```text
src/
├── api/
│   └── axios.js
├── components/
│   ├── Navbar.jsx
│   ├── RequireAuth.jsx
│   └── RequireRole.jsx
├── context/
│   └── AuthContext.jsx
├── pages/
│   ├── Login.jsx
│   ├── Register.jsx
│   ├── ForgotPassword.jsx
│   ├── ResetPassword.jsx
│   ├── Dashboard.jsx
│   └── Unauthorized.jsx
├── services/
│   └── authService.js
├── utils/
│   └── validation.js
├── App.jsx
└── main.jsx
```

### 17.1 Auth service

```js
import api from "../api/axios";

export const authService = {
  register: data => api.post("/auth/register", data),
  login: data => api.post("/auth/login", data),
  logout: () => api.post("/auth/logout"),
  getCurrentUser: () => api.get("/auth/me"),
  forgotPassword: data => api.post("/auth/forgot-password", data),
  resetPassword: data => api.post("/auth/reset-password", data),
};
```

Keep API calls in services instead of duplicating endpoint logic throughout UI components.

---

## 18. Complete Practical Implementation

Build an authentication application with these requirements:

1. Registration page with validation.
2. Login page with loading and error states.
3. Backend session or token integration.
4. AuthContext that restores the current user on refresh.
5. Navbar that changes based on authentication state.
6. Protected dashboard route.
7. Admin-only route.
8. Logout that invalidates the server session where supported.
9. Forgot-password and reset-password screens.
10. A clear unauthorized page.

### Implementation order

1. Confirm backend endpoints and request/response shapes.
2. Create the Axios instance.
3. Create auth service functions.
4. Build registration and login forms.
5. Implement AuthContext and session restoration.
6. Wrap the application with AuthProvider.
7. Create protected routes.
8. Add role or permission guards.
9. Implement logout and expired-session handling.
10. Test all success and failure paths.

### Example route tree

```text
/
├── /login                    Public
├── /register                 Public
├── /forgot-password          Public
├── /reset-password           Public
├── /dashboard                Authenticated
├── /profile                  Authenticated
├── /unauthorized             Public
└── /admin                    Authenticated + Admin role
    ├── /admin/users
    └── /admin/settings
```

---

## 19. Testing Authentication

Test more than the happy path.

| Scenario | Expected behavior |
|---|---|
| Valid login | User becomes authenticated |
| Invalid password | Safe error message appears |
| Empty form | Client validation prevents submission |
| Refresh while logged in | Session is restored if still valid |
| Refresh while logged out | Protected route redirects to login |
| Expired access token | Refresh or re-login flow runs |
| Invalid refresh token | Session ends safely |
| Normal user opens admin route | Unauthorized page or forbidden response |
| Logout | Session is invalidated and protected UI disappears |
| Backend unavailable | Error state appears; app does not pretend login succeeded |
| Direct API call without credentials | Backend rejects the request |
| User changes role in browser | Backend still enforces trusted permissions |

Use unit tests for components and auth utilities, integration tests for API flows, and end-to-end tests for login, logout, route guards, and role access.

---

## 20. Deployment and Production Checklist

- [ ] Production API uses HTTPS.
- [ ] Backend hashes passwords with a suitable password-hashing algorithm.
- [ ] Authentication endpoints have rate limiting.
- [ ] Session cookies use appropriate `HttpOnly`, `Secure`, and `SameSite` settings.
- [ ] Cookie-based state-changing requests have CSRF protection.
- [ ] Backend validates credentials and permissions on every protected endpoint.
- [ ] Access tokens have appropriate short lifetimes.
- [ ] Refresh tokens are protected, rotated, and revocable where applicable.
- [ ] No private secrets are bundled into the React application.
- [ ] Authentication errors do not expose sensitive account details.
- [ ] Password reset links are short-lived and single-use.
- [ ] Logout invalidates the server session or refresh token as applicable.
- [ ] CORS allows only intended origins and credentials configuration is correct.
- [ ] Security headers and Content Security Policy are configured.
- [ ] Logs exclude passwords, tokens, OTPs, and session identifiers.
- [ ] Direct API access is tested independently of frontend route guards.

---

## 21. Common Mistakes

1. **Treating a protected React route as backend security.** A user can call an API directly; enforce authorization on the server.
2. **Storing passwords in browser storage.** Never store user passwords in localStorage, sessionStorage, or cookies.
3. **Assuming JWT payloads are encrypted.** A signed JWT payload is usually readable; do not place secrets in it.
4. **Putting long-lived tokens in localStorage without considering XSS.** Choose storage based on a threat model.
5. **Not restoring authentication on page refresh.** Initialize auth state from a trusted session endpoint or session mechanism.
6. **Redirecting before authentication loading completes.** Show a loading state while checking the session.
7. **Trusting a role from client-side state.** Backend authorization must use trusted identity and permission data.
8. **Not handling expired tokens.** Define refresh, retry, and logout behavior.
9. **Creating duplicate Axios interceptors.** Register once or eject interceptors during cleanup.
10. **Ignoring CSRF with cookie authentication.** Use defenses appropriate to the deployment and cookie settings.
11. **Logging credentials or tokens.** Redact sensitive values from logs and error reports.
12. **Using OAuth without validating protocol values.** Use a maintained OIDC library and validate the flow correctly.
13. **Using GET for logout or other state-changing actions.** Use appropriate HTTP methods and CSRF defenses.
14. **Showing overly specific login errors.** Avoid revealing whether an account exists.
15. **Assuming CORS is authentication.** CORS is a browser cross-origin policy, not an access-control system for all clients.

---

## 22. Final Revision Checklist

Use this checklist to track your learning. In GitHub Markdown, change `[ ]` to `[x]` in the file and commit the change when you complete an item.

- [ ] Explain authentication and authorization.
- [ ] Explain identity, credentials, sessions, roles, and permissions.
- [ ] Describe registration, login, logout, and session restoration.
- [ ] Explain secure password storage and password reset.
- [ ] Explain JWT structure and validation.
- [ ] Compare access tokens and refresh tokens.
- [ ] Compare cookies, localStorage, and sessionStorage.
- [ ] Explain HttpOnly, Secure, and SameSite cookie attributes.
- [ ] Implement authentication state using Context API.
- [ ] Implement a protected route using React Router.
- [ ] Implement role-based and permission-based route guards.
- [ ] Explain why backend authorization is mandatory.
- [ ] Create an Axios instance and explain interceptors.
- [ ] Explain token refresh, rotation, revocation, and retry handling.
- [ ] Explain 401 vs 403.
- [ ] Explain 2FA and TOTP.
- [ ] Explain OAuth 2.0 vs OpenID Connect.
- [ ] Explain XSS, CSRF, brute-force, and credential stuffing.
- [ ] Test login, logout, refresh, unauthorized access, and API protection.
- [ ] Review the production security checklist.

**Completion standard:** You should be able to design and implement a frontend authentication flow, explain the backend responsibilities, and justify the security choices in an interview.
