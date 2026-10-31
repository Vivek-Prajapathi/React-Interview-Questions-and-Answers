# Chapter 6 – React API Integration and Data Fetching

> A complete, practical guide to connecting React applications to backend services, handling asynchronous data, managing loading and error states, and building reliable API layers.

## Table of Contents

1. [What is API integration?](#1-what-is-api-integration)
2. [Client, server, API and HTTP fundamentals](#2-client-server-api-and-http-fundamentals)
3. [How a React API request works](#3-how-a-react-api-request-works)
4. [HTTP methods](#4-http-methods)
5. [HTTP status codes](#5-http-status-codes)
6. [Request and response structure](#6-request-and-response-structure)
7. [JSON and serialization](#7-json-and-serialization)
8. [Fetch API](#8-fetch-api)
9. [Promises and asynchronous JavaScript recap](#9-promises-and-asynchronous-javascript-recap)
10. [Async and await](#10-async-and-await)
11. [GET requests with Fetch](#11-get-requests-with-fetch)
12. [POST requests with Fetch](#12-post-requests-with-fetch)
13. [PUT, PATCH and DELETE requests](#13-put-patch-and-delete-requests)
14. [Handling loading, success and error states](#14-handling-loading-success-and-error-states)
15. [useEffect and data fetching](#15-useeffect-and-data-fetching)
16. [Preventing stale updates and race conditions](#16-preventing-stale-updates-and-race-conditions)
17. [Axios fundamentals](#17-axios-fundamentals)
18. [Installing and configuring Axios](#18-installing-and-configuring-axios)
19. [Axios GET, POST, PUT, PATCH and DELETE](#19-axios-get-post-put-patch-and-delete)
20. [Fetch vs Axios](#20-fetch-vs-axios)
21. [Reusable API service layer](#21-reusable-api-service-layer)
22. [Axios instances and base URLs](#22-axios-instances-and-base-urls)
23. [Request interceptors](#23-request-interceptors)
24. [Response interceptors](#24-response-interceptors)
25. [Authentication tokens and bearer headers](#25-authentication-tokens-and-bearer-headers)
26. [Token refresh and session expiry](#26-token-refresh-and-session-expiry)
27. [Environment variables](#27-environment-variables)
28. [CORS and preflight requests](#28-cors-and-preflight-requests)
29. [Query parameters, filtering, sorting and pagination](#29-query-parameters-filtering-sorting-and-pagination)
30. [Search with debounce](#30-search-with-debounce)
31. [Dependent and parallel API requests](#31-dependent-and-parallel-api-requests)
32. [Request cancellation](#32-request-cancellation)
33. [File uploads and downloads](#33-file-uploads-and-downloads)
34. [Form submission with an API](#34-form-submission-with-an-api)
35. [API error handling patterns](#35-api-error-handling-patterns)
36. [Custom hooks for data fetching](#36-custom-hooks-for-data-fetching)
37. [Data fetching libraries: TanStack Query and SWR](#37-data-fetching-libraries-tanstack-query-and-swr)
38. [Caching, refetching and invalidation](#38-caching-refetching-and-invalidation)
39. [Optimistic updates](#39-optimistic-updates)
40. [React 19 Actions and API mutations](#40-react-19-actions-and-api-mutations)
41. [Security best practices](#41-security-best-practices)
42. [Performance and reliability best practices](#42-performance-and-reliability-best-practices)
43. [Testing API integration](#43-testing-api-integration)
44. [Debugging API requests](#44-debugging-api-requests)
45. [Recommended project structure](#45-recommended-project-structure)
46. [Practical implementation: user management](#46-practical-implementation-user-management)
47. [Practical implementation: login flow](#47-practical-implementation-login-flow)
48. [Common mistakes](#48-common-mistakes)
49. [Final checklist](#49-final-checklist)

---

## 1. What is API integration?

API integration is the process of connecting a frontend application to a backend service so that the frontend can send requests, receive data, and perform operations.

In a React application, API integration is commonly used to:
- Display products, users, courses or orders from a database.
- Register and log in users.
- Create, update and delete records.
- Search, filter, sort and paginate data.
- Upload files and submit forms.
- Retrieve authenticated user information.

**Interview-ready explanation:** “API integration in React means communicating with a backend through HTTP requests. React sends a request using Fetch or a library such as Axios, receives a response, and updates component state so the UI reflects the latest data.”

React itself does not provide a dedicated HTTP client. Fetch is built into modern browsers; Axios and data-fetching libraries can be installed when needed.

## 2. Client, server, API and HTTP fundamentals

- **Client:** The application making a request. In this chapter, the React frontend running in the browser.
- **Server:** The backend application that receives requests, applies business logic and communicates with a database or other services.
- **API:** A defined interface through which software systems communicate.
- **Endpoint:** A particular API URL, such as `/api/users`.
- **HTTP:** The request-response protocol commonly used for browser-to-server communication.
- **HTTPS:** HTTP protected by TLS encryption.
- **Resource:** The data or entity an endpoint operates on, such as a user or product.

Typical flow:

```text
React component
      |
      | calls API service
      v
Fetch / Axios
      |
      | HTTP request
      v
Backend API
      |
      v
Business logic / Database
      |
      | HTTP response (often JSON)
      v
React updates state
      |
      v
UI re-renders
```

The browser does not normally connect directly to the database. It communicates with a backend API, which controls access to data.

## 3. How a React API request works

1. A component renders.
2. An event or lifecycle-related effect starts a request.
3. The browser sends an HTTP request to the endpoint.
4. The server processes it.
5. The server returns a status code, headers and possibly a response body.
6. The frontend checks the response and parses the body.
7. React state is updated.
8. React renders the new UI.

A network request is asynchronous. The browser does not freeze while waiting for the server.

## 4. HTTP methods

| Method | Purpose | Typical use |
|---|---|---|
| GET | Read a resource | Fetch users |
| POST | Submit data, often create a resource | Register a user |
| PUT | Replace a resource representation | Replace a user record |
| PATCH | Partially update a resource | Change a user's role |
| DELETE | Delete a resource | Remove a user |

GET should not be used to change server data. PUT is conventionally replacement-oriented; PATCH is partial-update-oriented. Actual backend behavior is defined by that API's contract.

## 5. HTTP status codes

| Code | Meaning | Typical frontend handling |
|---|---|---|
| 200 | OK | Use returned data |
| 201 | Created | Show success; use created resource |
| 202 | Accepted | Operation accepted but may not be complete |
| 204 | No Content | Success with no response body |
| 400 | Bad Request | Display validation/request feedback |
| 401 | Unauthorized | Authentication missing or invalid |
| 403 | Forbidden | User is authenticated but not allowed |
| 404 | Not Found | Resource or route not found |
| 409 | Conflict | Duplicate or conflicting state |
| 422 | Unprocessable Content | Often field validation errors |
| 429 | Too Many Requests | Respect retry guidance |
| 500 | Internal Server Error | Generic server error |
| 502/503/504 | Gateway/service unavailable/timeout | Retry policy or service-unavailable message |

Do not treat every non-2xx response as the same error. Use status and the backend's documented error shape to decide what the UI should display.

## 6. Request and response structure

A request can contain:
- URL and HTTP method.
- Headers, such as `Content-Type` and `Authorization`.
- Query parameters.
- A body, commonly JSON for POST, PUT and PATCH.
- Credentials or cookies, when required by the authentication design.

A response contains:
- Status code.
- Response headers.
- Optional response body.

Example request:

```http
POST /api/users
Content-Type: application/json
Authorization: Bearer <access-token>

{"name":"Asha","email":"asha@example.com"}
```

Example JSON response:

```json
{
  "id": 101,
  "name": "Asha",
  "email": "asha@example.com"
}
```

The exact fields and error format must follow the backend API documentation.

## 7. JSON and serialization

JSON (JavaScript Object Notation) is a text format frequently used to exchange structured data.

- `JSON.stringify(value)` converts a JavaScript value into a JSON string.
- `response.json()` reads and parses a Fetch response body as JSON.
- Axios normally parses JSON responses automatically when appropriate.

```js
const user = { name: "Asha", active: true };
const body = JSON.stringify(user);
const parsed = JSON.parse(body);
```

Do not call `response.json()` twice on the same response; response bodies are streams and are normally consumed once.

## 8. Fetch API

Fetch is a browser API for making network requests. It returns a Promise that resolves to a `Response` object.

```js
const response = await fetch("https://api.example.com/users");
const data = await response.json();
```

Important: Fetch generally does **not** reject its Promise for HTTP errors such as 404 or 500. It rejects for network-level failures, aborts and similar conditions. Check `response.ok` or `response.status` yourself.

```js
const response = await fetch(url);

if (!response.ok) {
  throw new Error(`Request failed: ${response.status}`);
}
```

## 9. Promises and asynchronous JavaScript recap

A Promise represents the eventual result of an asynchronous operation. Its states are:
- Pending.
- Fulfilled.
- Rejected.

```js
fetch("/api/users")
  .then((response) => {
    if (!response.ok) throw new Error("Request failed");
    return response.json();
  })
  .then((users) => console.log(users))
  .catch((error) => console.error(error));
```

`.then()` handles fulfillment, `.catch()` handles rejection, and `.finally()` runs after settlement regardless of outcome.

## 10. Async and await

`async` makes a function return a Promise. `await` pauses that async function until a Promise settles; it does not block the browser's main thread.

```js
async function getUsers() {
  try {
    const response = await fetch("/api/users");
    if (!response.ok) throw new Error(`HTTP ${response.status}`);
    return await response.json();
  } catch (error) {
    console.error(error);
    throw error;
  }
}
```

Use `try/catch` when you need to handle a rejected Promise. If you catch an error and do not rethrow or return an error result, callers may incorrectly assume the operation succeeded.

## 11. GET requests with Fetch

```js
async function fetchUsers() {
  const response = await fetch("https://api.example.com/users");

  if (!response.ok) {
    throw new Error(`Could not load users (${response.status})`);
  }

  return response.json();
}
```

In a component, represent the request lifecycle explicitly:

```jsx
import { useEffect, useState } from "react";

function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function loadUsers() {
      try {
        setLoading(true);
        setError("");
        const response = await fetch("https://jsonplaceholder.typicode.com/users");
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        setUsers(await response.json());
      } catch (err) {
        setError(err.message || "Something went wrong");
      } finally {
        setLoading(false);
      }
    }

    loadUsers();
  }, []);

  if (loading) return <p>Loading users...</p>;
  if (error) return <p role="alert">{error}</p>;

  return (
    <ul>
      {users.map((user) => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

The empty dependency array means this effect is scheduled after the component's initial mount in ordinary usage. Development Strict Mode may run an additional setup/cleanup cycle to help detect unsafe effects.

## 12. POST requests with Fetch

```js
async function createUser(user) {
  const response = await fetch("/api/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(user),
  });

  if (!response.ok) {
    const message = await response.text();
    throw new Error(message || `HTTP ${response.status}`);
  }

  // A 201 response commonly contains JSON; 204 has no body.
  if (response.status === 204) return null;
  return response.json();
}
```

Call it from an event handler, not by directly performing a POST as an uncontrolled side effect during rendering.

## 13. PUT, PATCH and DELETE requests

```js
async function replaceUser(id, user) {
  const response = await fetch(`/api/users/${id}`, {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(user),
  });
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.status === 204 ? null : response.json();
}

async function updateUser(id, changes) {
  const response = await fetch(`/api/users/${id}`, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(changes),
  });
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
  return response.status === 204 ? null : response.json();
}

async function deleteUser(id) {
  const response = await fetch(`/api/users/${id}`, { method: "DELETE" });
  if (!response.ok) throw new Error(`HTTP ${response.status}`);
}
```

A DELETE response may be 204 with no body. Do not blindly parse every response as JSON.

## 14. Handling loading, success and error states

A useful request state model is:

```text
idle -> loading -> success
                 -> error
```

Common state variables:

```js
const [data, setData] = useState(null);
const [loading, setLoading] = useState(false);
const [error, setError] = useState(null);
```

Render each state deliberately:

```jsx
if (loading) return <Spinner />;
if (error) return <ErrorMessage message={error} />;
if (!data) return <p>No data available.</p>;
return <DataView data={data} />;
```

For more complex workflows, use a reducer or a data-fetching library rather than many loosely related booleans. Avoid impossible combinations such as `loading === true` and `error` representing a completed failure unless the UI explicitly needs that state.

## 15. useEffect and data fetching

`useEffect` is appropriate when fetching is caused by a component being displayed or by a reactive value changing. It is not the only way to fetch data: event handlers, route loaders and data-fetching libraries are also valid.

Rules:
- Declare the effect at the top level of the component.
- Include reactive values used by the effect in its dependency array.
- Define an inner async function because an effect callback itself should not be `async` (it must return either nothing or a cleanup function).
- Cancel or ignore obsolete requests during cleanup.
- Do not omit dependencies merely to silence lint warnings.

```jsx
useEffect(() => {
  let ignore = false;

  async function load() {
    try {
      const response = await fetch(`/api/users/${userId}`);
      if (!response.ok) throw new Error("Failed to load user");
      const result = await response.json();
      if (!ignore) setUser(result);
    } catch (err) {
      if (!ignore) setError(err.message);
    }
  }

  load();
  return () => { ignore = true; };
}, [userId]);
```

## 16. Preventing stale updates and race conditions

A race condition can occur when request A starts first but request B finishes first. If A later overwrites B, the UI displays stale data.

Use `AbortController` to cancel obsolete Fetch requests:

```jsx
useEffect(() => {
  const controller = new AbortController();

  async function load() {
    try {
      const response = await fetch(`/api/search?q=${encodeURIComponent(query)}`, {
        signal: controller.signal,
      });
      if (!response.ok) throw new Error("Search failed");
      setResults(await response.json());
    } catch (error) {
      if (error.name !== "AbortError") setError(error.message);
    }
  }

  load();
  return () => controller.abort();
}, [query]);
```

Cancellation prevents unnecessary work where supported. Also ensure only the latest request is allowed to update the UI.

## 17. Axios fundamentals

Axios is a Promise-based HTTP client that can be used in browsers and Node.js. It provides a convenient API for requests, automatic JSON transformation in common cases, interceptors, request configuration, timeout settings and request cancellation.

```js
import axios from "axios";

const response = await axios.get("/api/users");
console.log(response.data);
```

Axios response commonly includes:
- `data`: response body.
- `status`: HTTP status.
- `statusText`: status text.
- `headers`: response headers.
- `config`: request configuration.
- `request`: underlying request object (environment-dependent).

## 18. Installing and configuring Axios

Install:

```bash
npm install axios
```

Create a reusable instance:

```js
// src/api/axiosInstance.js
import axios from "axios";

const axiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
  headers: { "Content-Type": "application/json" },
});

export default axiosInstance;
```

Example Vite environment file:

```env
VITE_API_BASE_URL=https://api.example.com
```

Restart the development server after changing environment variables. Only non-secret values should be exposed through `VITE_` variables because they are bundled into client-side code.

## 19. Axios GET, POST, PUT, PATCH and DELETE

```js
import axiosInstance from "./axiosInstance";

export const getUsers = async () => {
  const response = await axiosInstance.get("/users");
  return response.data;
};

export const createUser = async (payload) => {
  const response = await axiosInstance.post("/users", payload);
  return response.data;
};

export const replaceUser = async (id, payload) => {
  const response = await axiosInstance.put(`/users/${id}`, payload);
  return response.data;
};

export const updateUser = async (id, changes) => {
  const response = await axiosInstance.patch(`/users/${id}`, changes);
  return response.data;
};

export const deleteUser = async (id) => {
  const response = await axiosInstance.delete(`/users/${id}`);
  return response.data;
};
```

Axios normally places the response body in `response.data`. For a DELETE or other 204 response, `data` may be empty.

## 20. Fetch vs Axios

| Feature | Fetch | Axios |
|---|---|---|
| Availability | Built into modern browsers | Install package |
| HTTP 4xx/5xx | Promise resolves; check `ok` | Rejects by default |
| JSON response | Call `response.json()` | Usually parsed automatically |
| Request body | Convert object with `JSON.stringify` | Pass object directly for JSON |
| Interceptors | No built-in interceptor system | Request/response interceptors |
| Timeout | Use AbortController signal/timeout support | `timeout` option |
| Upload progress | More limited / stream-based approaches | Upload progress support in supported environments |
| Bundle dependency | None | Adds dependency |

Neither is universally better. Choose based on project requirements and team conventions.

## 21. Reusable API service layer

Keep HTTP logic out of UI components. A service module provides named functions that represent backend operations.

Suggested structure:

```text
src/
  api/
    axiosInstance.js
    userApi.js
    authApi.js
    courseApi.js
  components/
  pages/
  hooks/
  context/
```

Example:

```js
// src/api/userApi.js
import axiosInstance from "./axiosInstance";

export const userApi = {
  getAll: () => axiosInstance.get("/users"),
  getById: (id) => axiosInstance.get(`/users/${id}`),
  create: (payload) => axiosInstance.post("/users", payload),
  update: (id, payload) => axiosInstance.patch(`/users/${id}`, payload),
  remove: (id) => axiosInstance.delete(`/users/${id}`),
};
```

The component calls `userApi.getAll()` and manages presentation state. The service owns endpoint details. For larger systems, return `response.data` consistently or return the full response consistently—document the choice and do not mix conventions unexpectedly.

## 22. Axios instances and base URLs

`axios.create()` creates an independent configured Axios instance. Use it to share:
- Base URL.
- Timeout.
- Default headers.
- Credentials policy.
- Interceptors.

```js
const publicApi = axios.create({ baseURL: "/api" });
const privateApi = axios.create({ baseURL: "/api", timeout: 10000 });
```

Avoid creating a new instance on every component render. Define instances in a module.

## 23. Request interceptors

A request interceptor runs before a request is sent. Common uses include adding an access token, attaching a request ID or applying common headers.

```js
axiosInstance.interceptors.request.use(
  (config) => {
    const token = sessionStorage.getItem("accessToken");

    if (token) {
      config.headers = config.headers || {};
      config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
  },
  (error) => Promise.reject(error)
);
```

Important:
- Return the config so the request can continue.
- Reject errors so callers can handle them.
- Do not assume a token exists.
- Register interceptors once in a shared module, not repeatedly on every render.
- Eject an interceptor if it was registered in a lifecycle that can run more than once.

## 24. Response interceptors

A response interceptor runs when a response arrives or a request fails. It can centralize logging, normalize errors or handle authentication failures.

```js
axiosInstance.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Notify the authentication layer that the session may be invalid.
      // Avoid blindly redirecting or clearing state for every 401 in every app.
    }

    return Promise.reject(error);
  }
);
```

Do not swallow errors by returning nothing from the rejection handler. Do not show duplicate toast messages both in the interceptor and in every component.

## 25. Authentication tokens and bearer headers

A bearer token is a credential sent in the HTTP `Authorization` header:

```http
Authorization: Bearer <access-token>
```

With Axios:

```js
axiosInstance.get("/profile", {
  headers: { Authorization: `Bearer ${token}` },
});
```

The backend validates the token and decides whether the request is authorized. Hiding a button in React is not authorization; the backend must enforce permissions.

Storage considerations:
- `localStorage` and `sessionStorage` are accessible to JavaScript and can be stolen by successful XSS.
- An `HttpOnly`, `Secure`, appropriately `SameSite` cookie cannot be read by frontend JavaScript and can reduce token exposure, but cookie-based authentication requires CSRF-aware design.
- The right approach depends on the backend architecture and threat model.
- Never put secrets, private API keys or database credentials in frontend code.

## 26. Token refresh and session expiry

An access token may expire. Some systems issue a refresh token to obtain a new access token.

Typical conceptual flow:
1. Send request with access token.
2. Backend returns 401 because token expired.
3. Frontend attempts refresh using the refresh mechanism defined by the backend.
4. If refresh succeeds, retry the original request once with the new access token.
5. If refresh fails, clear authentication state and require login.

A production implementation must prevent infinite retry loops and handle simultaneous 401 responses (often with a shared refresh Promise/queue). Refresh-token storage and rotation should be designed securely, usually with backend support. Do not invent a refresh endpoint; follow the API contract.

## 27. Environment variables

Vite exposes client variables prefixed with `VITE_`:

```env
VITE_API_BASE_URL=https://api.example.com
```

Read them with:

```js
const baseURL = import.meta.env.VITE_API_BASE_URL;
```

Rules:
- Do not commit real secrets.
- Add local environment files to `.gitignore` when appropriate.
- Provide a safe `.env.example` containing placeholder values.
- Restart Vite after changing environment variables.
- Anything bundled into frontend JavaScript is public to users, even if stored in an environment file.

## 28. CORS and preflight requests

CORS (Cross-Origin Resource Sharing) is a browser security mechanism. If a frontend origin differs from the API origin, the browser checks whether the server permits the cross-origin request.

An origin is the combination of scheme, host and port. `http://localhost:5173` and `http://localhost:3000` are different origins.

The backend controls CORS response headers. React cannot fix a server's CORS policy by adding arbitrary frontend headers.

A preflight is an `OPTIONS` request the browser may send before the actual request, for example when using certain methods or non-simple headers such as `Authorization` or JSON content types. The server must respond with appropriate allowed-origin, allowed-method and allowed-header information.

CORS is not authentication. It is not a substitute for backend authorization.

## 29. Query parameters, filtering, sorting and pagination

Query parameters are values appended after `?` in a URL.

```text
/api/products?page=2&limit=10&sort=price&order=asc
```

Use `URLSearchParams`:

```js
const params = new URLSearchParams({
  page: String(page),
  limit: String(limit),
  search,
});

const response = await fetch(`/api/products?${params}`);
```

Axios:

```js
axiosInstance.get("/products", {
  params: { page: 2, limit: 10, sort: "price" },
});
```

Common pagination models:
- Page-based: `page=2&limit=10`.
- Offset-based: `offset=20&limit=10`.
- Cursor-based: `cursor=opaque-value&limit=10`.

The backend determines the supported model and response shape. Keep pagination state synchronized with the URL when users should be able to bookmark or share the current view.

## 30. Search with debounce

Debouncing delays an operation until a period of inactivity has passed. It prevents sending a request for every keystroke.

```jsx
import { useEffect, useState } from "react";

function ProductSearch() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (!query.trim()) {
      setResults([]);
      return;
    }

    const controller = new AbortController();
    const timer = setTimeout(async () => {
      try {
        const response = await fetch(
          `/api/products?search=${encodeURIComponent(query)}`,
          { signal: controller.signal }
        );
        if (!response.ok) throw new Error("Search failed");
        setResults(await response.json());
      } catch (error) {
        if (error.name !== "AbortError") console.error(error);
      }
    }, 400);

    return () => {
      clearTimeout(timer);
      controller.abort();
    };
  }, [query]);

  return (
    <>
      <input value={query} onChange={(event) => setQuery(event.target.value)} />
      <ul>{results.map((item) => <li key={item.id}>{item.name}</li>)}</ul>
    </>
  );
}
```

Debounce is different from throttle: debounce waits for inactivity; throttle limits how often an operation can run over time.

## 31. Dependent and parallel API requests

**Dependent requests:** The second request needs data from the first.

```js
const userResponse = await axiosInstance.get("/users/10");
const user = userResponse.data;
const ordersResponse = await axiosInstance.get(`/users/${user.id}/orders`);
const orders = ordersResponse.data;
```

**Parallel requests:** Requests are independent and can run together.

```js
const [usersResponse, coursesResponse] = await Promise.all([
  axiosInstance.get("/users"),
  axiosInstance.get("/courses"),
]);
```

`Promise.all()` rejects when any input Promise rejects. Use `Promise.allSettled()` when each result should be handled independently.

## 32. Request cancellation

Fetch supports `AbortController`:

```js
const controller = new AbortController();

fetch("/api/data", { signal: controller.signal });

// When no longer needed:
controller.abort();
```

Axios supports `AbortController` signals in modern versions:

```js
const controller = new AbortController();
axiosInstance.get("/users", { signal: controller.signal });
controller.abort();
```

Cancellation is useful when a component unmounts, a search term changes or a newer request replaces an older one. Treat cancellation as expected control flow, not necessarily as a user-facing error.

## 33. File uploads and downloads

For a file upload, use `FormData`:

```js
async function uploadFile(file) {
  const formData = new FormData();
  formData.append("file", file);

  const response = await axiosInstance.post("/upload", formData);
  return response.data;
}
```

Do not manually set a multipart `Content-Type` boundary in browser Axios/Fetch usage; allow the browser/client to set the appropriate boundary.

Input example:

```jsx
<input
  type="file"
  accept="image/*"
  onChange={(event) => {
    const file = event.target.files?.[0];
    if (file) uploadFile(file);
  }}
/>
```

Validate file type and size on both frontend and backend. The backend must not trust the filename or client-provided MIME type alone.

For downloads, the backend may return a file response. With Axios:

```js
const response = await axiosInstance.get("/reports/monthly", {
  responseType: "blob",
});
const url = URL.createObjectURL(response.data);
const link = document.createElement("a");
link.href = url;
link.download = "monthly-report.pdf";
link.click();
URL.revokeObjectURL(url);
```

For large downloads, consider browser navigation or streaming approaches rather than holding the entire file in memory.

## 34. Form submission with an API

A reliable form submission:
1. Prevents the browser's default page reload when using a client-side handler.
2. Validates user input.
3. Sets a submitting/loading state.
4. Sends the request.
5. Displays field or general errors.
6. Displays success and resets/navigates when appropriate.
7. Prevents duplicate submissions while pending.

```jsx
function RegisterForm() {
  const [form, setForm] = useState({ name: "", email: "" });
  const [pending, setPending] = useState(false);
  const [error, setError] = useState("");
  const [success, setSuccess] = useState("");

  async function handleSubmit(event) {
    event.preventDefault();
    setPending(true);
    setError("");
    setSuccess("");

    try {
      const response = await fetch("/api/register", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(form),
      });
      if (!response.ok) throw new Error("Registration failed");
      setSuccess("Registration successful");
      setForm({ name: "", email: "" });
    } catch (err) {
      setError(err.message);
    } finally {
      setPending(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={form.name}
        onChange={(e) => setForm({ ...form, name: e.target.value })}
        placeholder="Name"
      />
      <input
        type="email"
        value={form.email}
        onChange={(e) => setForm({ ...form, email: e.target.value })}
        placeholder="Email"
      />
      <button disabled={pending}>{pending ? "Submitting..." : "Register"}</button>
      {error && <p role="alert">{error}</p>}
      {success && <p>{success}</p>}
    </form>
  );
}
```

In real projects, also show backend field-level validation messages and ensure the backend validates all data.

## 35. API error handling patterns

Axios errors can have different shapes:

```js
try {
  await axiosInstance.get("/users");
} catch (error) {
  if (error.response) {
    // Server responded with a non-2xx status.
    console.error(error.response.status, error.response.data);
  } else if (error.request) {
    // Request was made but no response was received.
    console.error("No response received");
  } else {
    // Request setup or another client-side error.
    console.error(error.message);
  }
}
```

Create a consistent error message mapper:

```js
export function getApiErrorMessage(error) {
  if (error.response?.data?.message) return error.response.data.message;
  if (error.response?.status === 401) return "Please sign in again.";
  if (error.request) return "Unable to reach the server. Check your connection.";
  return error.message || "Something went wrong.";
}
```

Do not expose stack traces, internal server messages or sensitive response data to end users.

## 36. Custom hooks for data fetching

A custom hook packages reusable stateful logic. It must follow the Rules of Hooks and its name should start with `use`.

```jsx
import { useEffect, useState } from "react";

export function useUsers() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    const controller = new AbortController();

    async function load() {
      try {
        const response = await fetch("/api/users", { signal: controller.signal });
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        setUsers(await response.json());
      } catch (err) {
        if (err.name !== "AbortError") setError(err.message);
      } finally {
        if (!controller.signal.aborted) setLoading(false);
      }
    }

    load();
    return () => controller.abort();
  }, []);

  return { users, loading, error };
}
```

Use it:

```jsx
function UserList() {
  const { users, loading, error } = useUsers();
  if (loading) return <p>Loading...</p>;
  if (error) return <p role="alert">{error}</p>;
  return users.map((user) => <p key={user.id}>{user.name}</p>);
}
```

A custom hook shares logic, not automatically shared cached data. Separate hook instances can make separate requests unless caching/state is added.

## 37. Data fetching libraries: TanStack Query and SWR

Libraries such as TanStack Query (React Query) and SWR manage server state: data that lives on a server and is fetched, cached and synchronized by the client.

They commonly provide:
- Caching and request deduplication.
- Loading, error and success states.
- Background refetching.
- Cache invalidation.
- Mutations.
- Retry controls.
- Pagination/infinite-query patterns.

TanStack Query basic setup (API may differ by installed major version; follow the version's documentation):

```bash
npm install @tanstack/react-query
```

```jsx
import { QueryClient, QueryClientProvider, useQuery } from "@tanstack/react-query";

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Users />
    </QueryClientProvider>
  );
}

function Users() {
  const { data, isPending, isError, error } = useQuery({
    queryKey: ["users"],
    queryFn: async () => {
      const response = await axiosInstance.get("/users");
      return response.data;
    },
  });

  if (isPending) return <p>Loading...</p>;
  if (isError) return <p>{error.message}</p>;
  return data.map((user) => <p key={user.id}>{user.name}</p>);
}
```

Do not confuse server state with UI/client state. A modal's open/closed state is client state; a list of users from an API is server state.

## 38. Caching, refetching and invalidation

Caching stores previously fetched data so it can be reused without immediately making another request.

Cache invalidation marks cached data stale or removes it so fresh data can be fetched. After creating or updating a record, invalidate or update the relevant cache entries.

In TanStack Query:

```js
const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: (newUser) => userApi.create(newUser),
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ["users"] });
  },
});
```

Cache keys must uniquely describe the data. Include relevant parameters such as filters, page number and user identity where appropriate.

## 39. Optimistic updates

An optimistic update changes the UI before the server confirms the mutation, making the interface feel faster.

Typical steps:
1. Save a snapshot of the previous cached value.
2. Update the cache optimistically.
3. Send the mutation.
4. Roll back if it fails.
5. Invalidate/refetch to reconcile with the server.

Use optimistic updates only when the UI can safely predict the likely result. Handle rollback and concurrent updates carefully.

## 40. React 19 Actions and API mutations

React 19 supports Actions for handling mutations and form submissions. `useActionState` can manage a form action's result and pending state. React's `useOptimistic` can display optimistic UI while an Action is pending.

A simplified client-side example:

```jsx
import { useActionState } from "react";

async function saveProfile(previousState, formData) {
  const name = formData.get("name");

  const response = await fetch("/api/profile", {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name }),
  });

  if (!response.ok) return { error: "Could not save profile" };
  return { success: true };
}

function ProfileForm() {
  const [state, formAction, isPending] = useActionState(saveProfile, {});

  return (
    <form action={formAction}>
      <input name="name" required />
      <button disabled={isPending}>
        {isPending ? "Saving..." : "Save"}
      </button>
      {state.error && <p role="alert">{state.error}</p>}
      {state.success && <p>Saved successfully</p>}
    </form>
  );
}
```

Actions do not remove the need for API design, server validation, authorization or error handling. The example assumes a backend endpoint that accepts this request.

## 41. Security best practices

- Always use HTTPS in production.
- Validate and authorize every operation on the backend.
- Never place private secrets in frontend code or `VITE_` variables.
- Treat all client input and API response data as untrusted.
- Use secure cookie settings and CSRF protection when using cookie-based authentication.
- Protect against XSS; avoid unsafe HTML injection.
- Do not log access tokens, passwords, personal information or sensitive response bodies.
- Validate file uploads on the server.
- Apply rate limiting and abuse protection on the backend.
- Do not rely on frontend route guards or hidden buttons as security boundaries.

## 42. Performance and reliability best practices

- Fetch only the data needed by the current view.
- Use pagination for large collections.
- Debounce high-frequency search requests.
- Cancel obsolete requests.
- Avoid duplicate requests and use caching when appropriate.
- Use parallel requests for independent resources.
- Set reasonable timeouts and design retry behavior carefully.
- Avoid retrying non-idempotent mutations blindly.
- Keep API logic in service modules or custom hooks.
- Render loading, empty, error and success states.
- Use stable query keys when using a query library.
- Monitor request failures and latency without logging sensitive data.

## 43. Testing API integration

Test at multiple levels:
- Unit-test service functions with mocked HTTP responses.
- Test custom hooks and component states.
- Test successful responses, validation errors, unauthorized responses, server errors and network failures.
- Test loading and empty states.
- Test cancellation and duplicate-submission behavior where relevant.
- Use tools such as MSW (Mock Service Worker) to mock network requests at the network boundary.
- Keep a small number of end-to-end tests for critical flows such as login and checkout.

A test should verify user-visible behavior, not just that an internal function was called.

## 44. Debugging API requests

Use browser DevTools:
1. Open the Network tab.
2. Trigger the operation.
3. Select the request.
4. Check Request URL, method, query parameters, headers and payload.
5. Check status code, response headers and response body.
6. Compare the request with the backend API documentation.
7. Check Console for JavaScript errors.
8. Check backend logs if the request reached the server.

Common causes:
- Incorrect base URL or endpoint.
- Wrong HTTP method.
- Missing or incorrectly formatted token.
- Wrong request body shape.
- Missing `Content-Type` where required.
- CORS configuration.
- Expired token or insufficient permissions.
- Incorrect environment variable.
- Server unavailable.

Do not paste real tokens or private user data into public screenshots or issue trackers.

## 45. Recommended project structure

```text
src/
  api/
    axiosInstance.js
    authApi.js
    userApi.js
    courseApi.js
  components/
    Loader.jsx
    ErrorMessage.jsx
  hooks/
    useUsers.js
  pages/
    UsersPage.jsx
    LoginPage.jsx
  context/
    AuthContext.jsx
  App.jsx
  main.jsx
```

Keep responsibilities clear:
- `api/`: HTTP configuration and endpoint functions.
- `hooks/`: reusable stateful behavior.
- `components/`: reusable UI.
- `pages/`: route-level screens.
- `context/`: app-wide client state such as authentication state.

This is a suggested structure, not a mandatory rule. Adapt it to the size and conventions of the project.

## 46. Practical implementation: user management

This example demonstrates a small CRUD service using Axios. It assumes the backend exposes the listed endpoints and returns JSON in the expected shape.

**Step 1 – Axios instance**

```js
// src/api/axiosInstance.js
import axios from "axios";

const axiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
});

export default axiosInstance;
```

**Step 2 – User API service**

```js
// src/api/userApi.js
import axiosInstance from "./axiosInstance";

export const getAllUsers = async () =>
  (await axiosInstance.get("/users")).data;

export const addUser = async (payload) =>
  (await axiosInstance.post("/users", payload)).data;

export const editUser = async (id, payload) =>
  (await axiosInstance.patch(`/users/${id}`, payload)).data;

export const removeUser = async (id) =>
  (await axiosInstance.delete(`/users/${id}`)).data;
```

**Step 3 – Component**

```jsx
import { useEffect, useState } from "react";
import { addUser, getAllUsers, removeUser } from "../api/userApi";

export default function UsersPage() {
  const [users, setUsers] = useState([]);
  const [name, setName] = useState("");
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  async function loadUsers() {
    try {
      setLoading(true);
      setError("");
      setUsers(await getAllUsers());
    } catch {
      setError("Unable to load users.");
    } finally {
      setLoading(false);
    }
  }

  useEffect(() => {
    loadUsers();
  }, []);

  async function handleAdd(event) {
    event.preventDefault();
    if (!name.trim()) return;

    try {
      const created = await addUser({ name: name.trim() });
      setUsers((current) => [...current, created]);
      setName("");
    } catch {
      setError("Unable to add user.");
    }
  }

  async function handleDelete(id) {
    try {
      await removeUser(id);
      setUsers((current) => current.filter((user) => user.id !== id));
    } catch {
      setError("Unable to delete user.");
    }
  }

  if (loading) return <p>Loading...</p>;

  return (
    <section>
      <h1>Users</h1>
      {error && <p role="alert">{error}</p>}
      <form onSubmit={handleAdd}>
        <input value={name} onChange={(e) => setName(e.target.value)} />
        <button type="submit">Add user</button>
      </form>
      {users.length === 0 ? <p>No users found.</p> : (
        <ul>
          {users.map((user) => (
            <li key={user.id}>
              {user.name}
              <button onClick={() => handleDelete(user.id)}>Delete</button>
            </li>
          ))}
        </ul>
      )}
    </section>
  );
}
```

For production, add schema validation, confirmation before deletion, accessible labels, field-level errors, cancellation, and a response shape matching the real backend.

## 47. Practical implementation: login flow

A typical login process:
1. User submits credentials over HTTPS.
2. Frontend sends them to the backend login endpoint.
3. Backend validates credentials.
4. Backend returns the authentication result according to its contract (for example, a cookie or access token).
5. Frontend updates authentication state.
6. Protected screens request authorized data.
7. Logout invalidates/clears the session according to the backend design.

Example API function:

```js
// src/api/authApi.js
import axiosInstance from "./axiosInstance";

export async function loginApi(credentials) {
  const response = await axiosInstance.post("/auth/login", credentials);
  return response.data;
}
```

Example handler when the backend contract returns an access token:

```js
async function handleLogin(event) {
  event.preventDefault();

  try {
    const result = await loginApi({ email, password });

    // Demonstration only: choose storage based on your security architecture.
    // Prefer secure HttpOnly-cookie designs when supported by the backend.
    setAuthUser(result.user);
    // Do not assume the token field name; follow the API response contract.
  } catch (error) {
    setError(getApiErrorMessage(error));
  }
}
```

Do not store passwords. Do not assume every backend returns `accessToken`, `token` or `user`; inspect its documented response. A protected route improves navigation UX, but the backend must still authorize every API request.

## 48. Common mistakes

1. **Assuming Fetch rejects on 404/500.** Check `response.ok`.
2. **Calling `response.json()` twice.** A response body is normally consumed once.
3. **Making an effect callback async.** Define and call an inner async function.
4. **Missing effect dependencies.** Include reactive values used by the effect.
5. **Updating state after an obsolete request.** Cancel or ignore stale requests.
6. **Parsing a 204 response as JSON.** Handle no-content responses.
7. **Forgetting `JSON.stringify` with Fetch.** Convert JavaScript objects to JSON text.
8. **Manually setting multipart boundaries.** Let the browser/client create them for FormData.
9. **Duplicating API URLs in components.** Centralize them in service modules.
10. **Registering interceptors repeatedly.** Register shared interceptors once and clean them up when appropriate.
11. **Swallowing errors.** Re-throw or return a clear error result.
12. **Exposing secrets in frontend environment variables.** Client-bundled values are public.
13. **Treating CORS as authentication.** CORS and authorization solve different problems.
14. **Trusting frontend validation alone.** Validate on the backend too.
15. **Blindly retrying every mutation.** A repeated POST may create duplicate records.
16. **Not rendering empty states.** An empty result is not necessarily an error.
17. **Assuming a successful HTTP status means business success.** Follow the API response contract.

## 49. Final checklist

- [ ] Understand client/server/API/HTTP fundamentals.
- [ ] Know GET, POST, PUT, PATCH and DELETE.
- [ ] Understand common HTTP status codes.
- [ ] Use Fetch and check `response.ok`.
- [ ] Understand Promises, async/await and try/catch.
- [ ] Manage loading, success, empty and error states.
- [ ] Fetch data safely in effects and clean up obsolete requests.
- [ ] Configure Axios and reusable instances.
- [ ] Build API service modules.
- [ ] Understand request and response interceptors.
- [ ] Understand bearer tokens and authentication boundaries.
- [ ] Configure environment variables safely.
- [ ] Understand CORS and preflight requests.
- [ ] Implement query parameters, pagination and debounced search.
- [ ] Handle parallel requests and cancellation.
- [ ] Upload and download files.
- [ ] Understand custom hooks and server-state libraries.
- [ ] Apply caching, invalidation and optimistic updates.
- [ ] Test and debug requests using DevTools.
- [ ] Apply security and reliability best practices.

---

**End of Chapter 6 – React API Integration and Data Fetching**

**Next Chapter: Chapter 7 – React Authentication and Authorization (Login, Registration, JWT, Cookies, Protected Routes, Role-Based Access Control, Refresh Tokens and Session Management).**
