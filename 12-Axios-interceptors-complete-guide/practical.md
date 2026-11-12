# PROMPT: Create an Extremely Detailed Practical Axios Interceptors & Authentication Markdown Guide

Create a **very detailed, practical, implementation-focused Markdown file** titled:

# Axios Interceptors — Practical Implementation, Axios Instances & Token Authentication

This MD file is the **next chapter after my existing theoretical Axios Interceptors guide**.

The previous guide already explains:

- What Axios interceptors are
- Why interceptors exist
- Normal request/response lifecycle
- Request interceptors
- Response interceptors
- `axios.interceptors.request.use()`
- `axios.interceptors.response.use()`
- `use()` and registration
- Callback functions
- Why callbacks are passed instead of immediately called
- Request `config` object
- Response `response` object
- `error.response`
- Dot notation
- `config.headers`
- `response.data`
- `response.status`
- `response.config`
- Request/response flow

DO NOT repeat that material shallowly.

Instead, this MD should take that understanding and teach me **how we actually build and use Axios interceptors in a real React application**.

The goal is not memorization.

The goal is:

> "I should understand exactly what code we write, why we write every line, what Axios is doing internally at a conceptual level, and how all the pieces work together in a real authentication system."

---

# PART 1 — FROM THEORY TO REAL PROJECT

Start by explaining:

## 1. Why do we need an Axios instance?

First show the normal approach:

```js
import axios from "axios";

axios.get("https://api.example.com/users");
axios.post("https://api.example.com/login");
axios.get("https://api.example.com/products");
```

Then explain the problems:

- Repeating the base URL
- Repeating headers
- Repeating configuration
- Repeating authentication logic
- Difficult centralized error handling
- Difficult token handling
- Difficult refresh-token handling

Then introduce:

```js
const axiosInstance = axios.create({
    baseURL: "https://api.example.com"
});
```

Explain extremely deeply:

- What `axios.create()` is
- Why `create()` exists
- What it returns
- Why we call the returned value an Axios instance
- Difference between `axios` and `axiosInstance`
- Whether the instance is a new Axios object/configuration context
- What configuration belongs to the instance
- How `baseURL` works
- How defaults work
- Why an instance is useful in large applications

Do NOT just say:

> "Axios instance is a customized Axios object."

Explain what that actually means.

---

# PART 2 — `axios` VS `axiosInstance`

Create a detailed comparison.

Explain:

```js
axios.get(...)
```

versus:

```js
axiosInstance.get(...)
```

Explain:

- Where the methods come from
- Why both have `.get()`, `.post()`, etc.
- Why interceptors can be attached to an instance
- Why we often prefer an instance in production
- When using the global Axios object is okay
- When an instance is better

Show:

```js
import axios from "axios";

const axiosInstance = axios.create({
    baseURL: "https://api.example.com"
});
```

Then show:

```js
axios.interceptors.request.use(...)
```

and:

```js
axiosInstance.interceptors.request.use(...)
```

Explain the practical difference.

---

# PART 3 — PROJECT FOLDER STRUCTURE

Show a realistic React project structure.

For example:

```text
src/
│
├── api/
│   ├── axiosInstance.js
│   ├── authApi.js
│   └── userApi.js
│
├── context/
│   └── AuthContext.jsx
│
├── components/
│   ├── Login.jsx
│   ├── Dashboard.jsx
│   └── Navbar.jsx
│
├── routes/
│   └── ProtectedRoute.jsx
│
├── App.jsx
└── main.jsx
```

Explain:

- Why we separate Axios configuration
- Why API functions should be separated
- Why authentication state belongs in Context
- Why protected routing is separate
- Why this structure scales better

---

# PART 4 — BUILD THE AXIOS INSTANCE FROM ZERO

Create:

```text
src/api/axiosInstance.js
```

Start with the simplest implementation:

```js
import axios from "axios";

const axiosInstance = axios.create({
    baseURL: "https://api.example.com"
});

export default axiosInstance;
```

Explain EVERY line.

Especially:

```js
axios.create(...)
```

and:

```js
export default axiosInstance;
```

Explain what another file receives when it does:

```js
import axiosInstance from "./api/axiosInstance";
```

---

# PART 5 — USING THE INSTANCE

Create an API function:

```js
import axiosInstance from "./axiosInstance";

export const getUsers = () => {
    return axiosInstance.get("/users");
};
```

Then show how the component calls it:

```js
const response = await getUsers();

console.log(response.data);
```

Explain the complete flow:

```text
Component
   ↓
getUsers()
   ↓
axiosInstance.get("/users")
   ↓
Axios instance
   ↓
baseURL + URL
   ↓
HTTP request
   ↓
Server
   ↓
response
   ↓
Component
```

Explain where the Axios instance participates.

---

# PART 6 — ADDING REQUEST INTERCEPTOR

Now add:

```js
axiosInstance.interceptors.request.use(
    (config) => {
        console.log("Request interceptor");
        return config;
    }
);
```

Explain:

- Why interceptor is attached to `axiosInstance`
- Why `.interceptors` exists
- Why `.request` exists
- Why `.use()` is called
- Why we pass a function
- When Axios executes that function
- What `config` contains
- Why we return config

Do NOT repeat the old theoretical explanation excessively.

Instead connect it to the actual project.

---

# PART 7 — LOGGING THE REQUEST

Show:

```js
axiosInstance.interceptors.request.use((config) => {
    console.log("Method:", config.method);
    console.log("URL:", config.url);
    console.log("Headers:", config.headers);

    return config;
});
```

Explain what we will see in DevTools/console.

Show the actual runtime sequence.

---

# PART 8 — ADDING AUTHENTICATION TOKEN

Now introduce:

```js
const token = sessionStorage.getItem("token");
```

Explain:

- Why the token is stored
- Why we retrieve it in the interceptor
- Why we don't manually add it to every API call
- Why the interceptor is the correct centralized location

Then:

```js
if (token) {
    config.headers.Authorization = `Bearer ${token}`;
}
```

Explain EVERY part:

```js
config
```

```js
config.headers
```

```js
config.headers.Authorization
```

```js
`Bearer ${token}`
```

Explain the resulting HTTP header:

```text
Authorization: Bearer eyJ...
```

Explain what "Bearer" means conceptually.

Do not assume I already understand authentication headers.

---

# PART 9 — WHY INTERCEPTOR IS BETTER THAN MANUALLY ADDING TOKEN

Compare:

### Without interceptor

```js
axios.get("/users", {
    headers: {
        Authorization: `Bearer ${token}`
    }
});
```

Then every API call would need the same logic.

Compare with:

```js
axiosInstance.get("/users");
axiosInstance.get("/products");
axiosInstance.get("/orders");
```

where the interceptor automatically adds the token.

Explain the advantages:

- DRY
- centralized authentication
- consistency
- easier maintenance
- easier token changes
- centralized error handling

---

# PART 10 — RESPONSE INTERCEPTOR IN REAL PROJECT

Add:

```js
axiosInstance.interceptors.response.use(
    (response) => {
        return response;
    },
    (error) => {
        return Promise.reject(error);
    }
);
```

Explain:

- Why there are TWO callback functions
- First = successful response
- Second = rejected/error path
- When each runs
- Why we return `response`
- Why we return `Promise.reject(error)`

Connect it to the previous callback/registration explanation instead of treating it as new magic.

---

# PART 11 — CENTRALIZED ERROR HANDLING

Implement:

```js
axiosInstance.interceptors.response.use(
    (response) => response,
    (error) => {
        if (error.response?.status === 401) {
            // handle unauthorized
        }

        return Promise.reject(error);
    }
);
```

Explain:

- What `401` means
- Why a 401 might happen
- Why checking it in the interceptor is useful
- Why every component should not individually check `401`
- What centralized error handling means

Show before/after comparison.

---

# PART 12 — ACCESS TOKEN VS REFRESH TOKEN

Now introduce token authentication properly.

Explain:

## Access Token

What it is.

Why APIs use it.

Why it is short-lived.

Example:

```text
Access Token
     ↓
Used to access protected APIs
```

## Refresh Token

What it is.

Why it exists.

Why it usually lives longer.

What it is used for.

Example:

```text
Access Token expires
        ↓
Refresh Token
        ↓
Authentication server
        ↓
New Access Token
```

Explain clearly that:

> A refresh token is not normally sent to every protected API request.

Instead, it is used specifically to obtain a new access token.

---

# PART 13 — COMPLETE AUTHENTICATION FLOW

Create a complete diagram:

```text
LOGIN
  ↓
User enters credentials
  ↓
POST /login
  ↓
Server validates credentials
  ↓
Server returns tokens
  ↓
Access Token
Refresh Token
  ↓
Store according to application security design
```

Then:

```text
Normal API Request
        ↓
Request Interceptor
        ↓
Read Access Token
        ↓
Authorization: Bearer <token>
        ↓
Server
        ↓
Successful response
```

Then:

```text
Access Token Expired
        ↓
Server returns 401
        ↓
Response Interceptor
        ↓
Use Refresh Token
        ↓
POST /refresh
        ↓
Server validates Refresh Token
        ↓
New Access Token
        ↓
Retry original request
        ↓
Original API succeeds
```

Explain this flow VERY carefully.

---

# PART 14 — REFRESH TOKEN IMPLEMENTATION

Build a realistic implementation step by step.

Start with the naive implementation.

Example:

```js
axiosInstance.interceptors.response.use(
    (response) => response,
    async (error) => {

        if (error.response?.status === 401) {

            const refreshToken =
                sessionStorage.getItem("refreshToken");

            const response = await axios.post(
                "/refresh",
                {
                    refreshToken
                }
            );

            const newAccessToken =
                response.data.accessToken;

            sessionStorage.setItem(
                "token",
                newAccessToken
            );

            return axiosInstance(error.config);
        }

        return Promise.reject(error);
    }
);
```

DO NOT just give the code.

Break it down line by line.

Especially:

```js
error.config
```

Explain why this is extremely important.

Explain:

> `error.config` represents the configuration of the request that failed.

Then explain:

```js
return axiosInstance(error.config);
```

Explain why we can retry the original request using its configuration.

---

# PART 15 — HOW DOES `axiosInstance(error.config)` WORK?

This section MUST be very detailed.

Explain:

```js
axiosInstance(error.config)
```

versus:

```js
axiosInstance.get(...)
```

Explain that Axios can accept a configuration object directly.

Show conceptually:

```js
axiosInstance({
    method: "get",
    url: "/users",
    headers: {
        Authorization: "Bearer ..."
    }
});
```

Then explain why:

```js
axiosInstance(error.config)
```

can recreate the original request.

Show:

```text
Original Request
      ↓
config
      ↓
Server
      ↓
401
      ↓
error.config
      ↓
Refresh token
      ↓
New access token
      ↓
Modify error.config
      ↓
axiosInstance(error.config)
      ↓
Retry original request
```

---

# PART 16 — IMPORTANT PROBLEM: INFINITE LOOP

Explain the problem with naive refresh-token code.

Example:

```text
API request
   ↓
401
   ↓
refresh request
   ↓
refresh also returns 401
   ↓
response interceptor sees 401
   ↓
refresh again
   ↓
401
   ↓
refresh again
   ↓
INFINITE LOOP
```

Explain why this happens.

---

# PART 17 — `_retry` FLAG

Introduce:

```js
if (error.response?.status === 401 && !error.config._retry) {
    error.config._retry = true;
    ...
}
```

Explain:

- Why `_retry` is added
- Why it is attached to `error.config`
- Why it prevents repeated retrying
- Why it is not a standard Axios property
- Why developers use custom properties such as `_retry`

Show:

```text
First 401
   ↓
_retry is undefined
   ↓
Set _retry = true
   ↓
Refresh token
   ↓
Retry

Second 401
   ↓
_retry === true
   ↓
DO NOT refresh again
   ↓
Reject / logout
```

---

# PART 18 — DO NOT USE THE SAME INSTANCE CARELESSLY FOR REFRESH

Explain why the refresh request can create problems if it goes through the same interceptor.

For example:

```js
axiosInstance.post("/refresh");
```

Explain the possibility of the refresh request itself passing through:

```js
response interceptor
```

and triggering the refresh logic again.

Explain approaches such as:

```js
axios.post(...)
```

using the base Axios client for refresh, OR

creating a separate authentication client.

Explain the trade-offs.

---

# PART 19 — ACCESS TOKEN UPDATE

After refreshing:

```js
const newAccessToken = response.data.accessToken;

sessionStorage.setItem(
    "token",
    newAccessToken
);
```

Explain why we update the stored token.

Then explain why we must also update the token in the failed request before retrying.

For example:

```js
error.config.headers.Authorization =
    `Bearer ${newAccessToken}`;
```

Explain:

> Updating storage alone does not automatically modify the already-created `error.config`.

This distinction must be explained very clearly.

---

# PART 20 — COMPLETE REFRESH TOKEN CODE

Build a complete implementation.

Use a clean realistic version such as:

```js
import axios from "axios";

const axiosInstance = axios.create({
    baseURL: "https://api.example.com"
});

axiosInstance.interceptors.request.use(
    (config) => {

        const token = sessionStorage.getItem("token");

        if (token) {
            config.headers.Authorization =
                `Bearer ${token}`;
        }

        return config;
    }
);

axiosInstance.interceptors.response.use(
    (response) => response,

    async (error) => {

        const originalRequest = error.config;

        if (
            error.response?.status === 401 &&
            !originalRequest._retry
        ) {

            originalRequest._retry = true;

            try {

                const refreshToken =
                    sessionStorage.getItem("refreshToken");

                const response = await axios.post(
                    "https://api.example.com/refresh",
                    {
                        refreshToken
                    }
                );

                const newAccessToken =
                    response.data.accessToken;

                sessionStorage.setItem(
                    "token",
                    newAccessToken
                );

                originalRequest.headers.Authorization =
                    `Bearer ${newAccessToken}`;

                return axiosInstance(originalRequest);

            } catch (refreshError) {

                sessionStorage.removeItem("token");
                sessionStorage.removeItem("refreshToken");

                return Promise.reject(refreshError);
            }
        }

        return Promise.reject(error);
    }
);

export default axiosInstance;
```

Then explain every block deeply.

---

# PART 21 — IMPORTANT: REAL-WORLD REFRESH TOKEN CONCURRENCY

Introduce the advanced problem:

What happens if 10 API requests fail with 401 at approximately the same time?

Without protection:

```text
Request 1 → 401 → refresh
Request 2 → 401 → refresh
Request 3 → 401 → refresh
Request 4 → 401 → refresh
...
```

This could create multiple refresh requests.

Explain why that is undesirable.

Then introduce the concept of:

```text
isRefreshing
failedQueue
```

Explain the idea conceptually before showing code.

---

# PART 22 — REFRESH QUEUE PATTERN

Show a robust conceptual pattern:

```js
let isRefreshing = false;
let failedQueue = [];
```

Explain:

- What `isRefreshing` represents
- What `failedQueue` represents
- Why promises are placed in the queue
- Why later requests wait for the same refresh request

Show the flow:

```text
Request A → 401
           ↓
      Start refresh

Request B → 401
           ↓
      Wait in queue

Request C → 401
           ↓
      Wait in queue

Refresh succeeds
           ↓
New access token
           ↓
Resolve queued requests
           ↓
Retry A
Retry B
Retry C
```

Then provide a complete implementation.

Explain the implementation line by line.

---

# PART 23 — REFRESH TOKEN STORAGE & SECURITY

Explain that the exact storage strategy depends on the application's security architecture.

Discuss:

- `sessionStorage`
- `localStorage`
- HttpOnly cookies
- Secure cookies
- SameSite cookies
- XSS concerns
- CSRF considerations
- Why storing long-lived refresh tokens in browser storage can be risky

Do NOT present one storage method as universally correct.

Clearly separate:

> "This is useful for learning/demo implementation"

from:

> "Production security requires a deliberate architecture."

---

# PART 24 — LOGOUT FLOW

Explain how logout interacts with interceptors.

Example:

```js
const logout = () => {
    sessionStorage.removeItem("token");
    sessionStorage.removeItem("refreshToken");
};
```

Explain:

- Why tokens must be cleared
- What happens to future requests
- What happens if a request is already in progress
- What should happen when refresh fails
- Why the user should usually be redirected to login

---

# PART 25 — AUTH CONTEXT + AXIOS INTERCEPTOR

Explain how Axios interceptors and React Context work together.

Show a conceptual structure:

```text
AuthContext
   ↓
stores authentication state

Axios Instance
   ↓
handles HTTP requests

Request Interceptor
   ↓
adds access token

Response Interceptor
   ↓
handles 401 / refresh

Protected Route
   ↓
controls UI access
```

Explain the difference between:

### Authentication state

and:

### HTTP authentication handling

Do not mix them.

---

# PART 26 — PROTECTED ROUTES VS AXIOS INTERCEPTORS

Explain:

```text
ProtectedRoute
```

and:

```text
Axios interceptor
```

solve different problems.

Protected route:

> Controls whether a user can access a frontend route.

Interceptor:

> Controls how HTTP requests/responses are handled.

Show examples.

---

# PART 27 — API MODULE DESIGN

Show:

```js
// userApi.js

import axiosInstance from "./axiosInstance";

export const getUsers = () => {
    return axiosInstance.get("/users");
};

export const getUserById = (id) => {
    return axiosInstance.get(`/users/${id}`);
};

export const deleteUser = (id) => {
    return axiosInstance.delete(`/users/${id}`);
};
```

Explain why components should ideally not contain repetitive Axios configuration.

Then:

```jsx
const response = await getUsers();
```

Explain the separation:

```text
Component
   ↓
API function
   ↓
Axios instance
   ↓
Interceptors
   ↓
Server
```

---

# PART 28 — REQUEST INTERCEPTOR VS API FUNCTION

Explain exactly what belongs where.

### API function

Should define:

```text
WHAT endpoint to call
WHAT data to send
```

### Axios interceptor

Should handle cross-cutting concerns such as:

```text
authentication headers
logging
common request configuration
centralized error handling
token refresh
```

Explain why this separation is useful.

---

# PART 29 — MULTIPLE AXIOS INSTANCES

Explain why an application might have:

```js
const apiClient = axios.create(...);

const authClient = axios.create(...);
```

For example:

```text
apiClient
→ authenticated APIs

authClient
→ login / refresh / registration
```

Explain why separate instances can prevent authentication interceptors from accidentally affecting login/refresh endpoints.

---

# PART 30 — INTERCEPTOR REGISTRATION AND EJECTION

Explain that Axios interceptors can also be removed.

Show:

```js
const interceptorId =
    axiosInstance.interceptors.request.use(...);

axiosInstance.interceptors.request.eject(
    interceptorId
);
```

Explain:

- What ID is returned
- Why we might store it
- What `eject()` does
- When ejecting can be useful
- How interceptor registration can become problematic if done repeatedly

Especially discuss the React-specific issue of registering interceptors inside components.

---

# PART 31 — DO NOT REGISTER INTERCEPTORS REPEATEDLY IN COMPONENT RENDER

Explain why this is problematic:

```jsx
function App() {

    axiosInstance.interceptors.request.use(...);

    return <div>...</div>;
}
```

Explain:

- React renders can happen multiple times
- Each render can register another interceptor
- Multiple interceptors can accumulate
- The same logic may execute multiple times

Then explain appropriate places to register interceptors.

Discuss:

```text
axiosInstance.js
```

as the simplest approach for a static interceptor.

Also discuss `useEffect` + cleanup/eject when dynamic registration is genuinely needed.

---

# PART 32 — COMMON INTERCEPTOR MISTAKES

Cover all of these:

### Mistake 1

Forgetting:

```js
return config;
```

### Mistake 2

Forgetting:

```js
return response;
```

### Mistake 3

Forgetting:

```js
return Promise.reject(error);
```

### Mistake 4

Using:

```js
config.headers.Authorization
```

when headers may not exist in some unusual configuration contexts.

### Mistake 5

Refreshing endlessly on every 401.

### Mistake 6

Not using `_retry`.

### Mistake 7

Sending the refresh request through the same interceptor without thinking about recursion.

### Mistake 8

Updating the token in storage but not updating the failed request's headers.

### Mistake 9

Registering interceptors repeatedly in React renders.

### Mistake 10

Putting all API logic directly inside components.

### Mistake 11

Confusing frontend route protection with API authentication.

### Mistake 12

Assuming every error has:

```js
error.response
```

### Mistake 13

Ignoring network errors where there is no server response.

---

# PART 33 — DEBUGGING INTERCEPTORS

Teach me how to debug interceptors step by step.

Show useful logs:

```js
console.log("REQUEST INTERCEPTOR");
console.log(config);

console.log("RESPONSE INTERCEPTOR");
console.log(response);

console.log("ERROR INTERCEPTOR");
console.log(error);
```

Explain how to inspect:

```text
config.url
config.method
config.headers
response.status
response.data
error.response?.status
error.config
```

Also explain how to use:

- Browser Network tab
- Request Headers
- Response Headers
- Request Payload
- Response
- Status Code
- Console logs

Show how to trace:

```text
Component
→ API function
→ Request interceptor
→ Network request
→ Server
→ Response interceptor
→ Component
```

---

# PART 34 — COMPLETE END-TO-END PROJECT

Build a complete small authentication project.

Requirements:

```text
Login
Register
Access token
Refresh token
Axios instance
Request interceptor
Response interceptor
Protected API
Protected route
Logout
Token refresh
401 handling
```

Use a realistic folder structure.

Provide all relevant files:

```text
src/
├── api/
│   ├── axiosInstance.js
│   ├── authApi.js
│   └── userApi.js
│
├── context/
│   └── AuthContext.jsx
│
├── components/
│   ├── Login.jsx
│   ├── Dashboard.jsx
│   └── Navbar.jsx
│
├── routes/
│   └── ProtectedRoute.jsx
│
├── App.jsx
└── main.jsx
```

Do NOT just dump the code.

For every file explain:

1. Why the file exists
2. What responsibility it has
3. Why the code is placed there
4. How it connects to other files

---

# PART 35 — COMPLETE END-TO-END FLOW

At the end, create a complete diagram:

```text
USER
 ↓
LOGIN FORM
 ↓
authApi.login()
 ↓
axiosInstance
 ↓
POST /login
 ↓
SERVER
 ↓
ACCESS TOKEN + REFRESH TOKEN
 ↓
STORE TOKENS
 ↓
DASHBOARD
 ↓
GET /users
 ↓
REQUEST INTERCEPTOR
 ↓
READ ACCESS TOKEN
 ↓
ADD Authorization HEADER
 ↓
SERVER
 ↓
200
 ↓
RESPONSE INTERCEPTOR
 ↓
COMPONENT
```

Then expired token:

```text
GET /users
 ↓
REQUEST INTERCEPTOR
 ↓
OLD ACCESS TOKEN
 ↓
SERVER
 ↓
401
 ↓
RESPONSE INTERCEPTOR
 ↓
CHECK _retry
 ↓
REFRESH TOKEN
 ↓
/refresh
 ↓
NEW ACCESS TOKEN
 ↓
UPDATE TOKEN STORAGE
 ↓
UPDATE ORIGINAL REQUEST
 ↓
RETRY ORIGINAL REQUEST
 ↓
SERVER
 ↓
200
 ↓
COMPONENT
```

Then refresh failure:

```text
401
 ↓
Refresh token
 ↓
Refresh fails
 ↓
Clear authentication
 ↓
Logout
 ↓
Login page
```

---

# PART 36 — INTERVIEW QUESTIONS

At the bottom of the MD file, include a very detailed interview section.

Cover ALL possible interview questions related to:

## Axios

- What is Axios?
- Why use Axios instead of fetch?
- What is an Axios instance?
- Why use `axios.create()`?
- Difference between Axios and Axios instance?
- What is `baseURL`?

## Interceptors

- What are Axios interceptors?
- Why do we use request interceptors?
- Why do we use response interceptors?
- How does interceptor registration work?
- What does `.use()` do?
- Why are callbacks passed to `.use()`?
- Why do we return `config`?
- Why do we return `response`?
- Why do we return `Promise.reject(error)`?
- Can we have multiple interceptors?
- In what order do interceptors execute?
- How do we eject an interceptor?

## Config

- What is the Axios config object?
- What is inside config?
- What is `config.url`?
- What is `config.method`?
- What is `config.headers`?
- What is `config.data`?
- What is `config.params`?
- What is `config.baseURL`?
- Why is `error.config` useful?

## Response

- What is the Axios response object?
- What is `response.data`?
- What is `response.status`?
- What is `response.headers`?
- What is `response.config`?
- What is `response.request`?

## Authentication

- What is an access token?
- What is a refresh token?
- Why do access tokens expire?
- Why do we need refresh tokens?
- Where should tokens be stored?
- What does `Authorization: Bearer <token>` mean?
- Why add the token in an interceptor?

## Refresh Token

- How does refresh-token authentication work?
- How do you handle 401 responses?
- How do you retry the original request?
- What is `error.config`?
- What is `_retry`?
- Why do we need `_retry`?
- How can refresh logic cause an infinite loop?
- How do you prevent multiple refresh requests?
- What is a refresh queue?
- How do you handle refresh failure?
- What happens if the refresh token expires?
- Why might we use a separate Axios instance for refresh?

## React

- Where should Axios interceptors be registered?
- Why shouldn't we register interceptors directly inside component render?
- How can `useEffect` be used with interceptors?
- When should we eject an interceptor?
- How does Axios work with Context API?
- Difference between protected routes and API authentication?

For EVERY interview question:

1. Give a detailed answer.
2. Keep the explanation consistent with the theory already explained earlier.
3. Use the same terminology wherever possible.
4. Explain the "why", not just the "what".
5. Include code where useful.
6. Explain the code.
7. Give the practical flow.
8. Do NOT give a completely different answer style from the theory section.

The interview answers should feel like:

> "The same concepts I learned in the theory section, now explained as an interview answer."

---

# PART 37 — INTERVIEW ANSWER LENGTH

For important questions, provide approximately:

### 30-second answer

Short interview response.

### 1-minute answer

More complete explanation.

### 2–3 minute answer

Detailed interview-ready answer.

Do this especially for:

- What is an Axios interceptor?
- What is an Axios instance?
- Why use Axios instances?
- How does request interceptor work?
- How does response interceptor work?
- How does token authentication work with Axios?
- How does refresh-token handling work?
- How do you retry a failed request?
- How do you prevent infinite refresh loops?
- How do you handle concurrent 401 requests?

---

# PART 38 — "WHY" QUESTIONS

Include a separate section:

# Why Do We Write It This Way?

Explain questions such as:

### Why:

```js
axios.create()
```

### Why:

```js
axiosInstance.interceptors.request.use()
```

### Why:

```js
(config) => {}
```

### Why:

```js
return config;
```

### Why:

```js
config.headers.Authorization
```

### Why:

```js
error.response?.status
```

### Why:

```js
error.config
```

### Why:

```js
error.config._retry
```

### Why:

```js
return axiosInstance(error.config)
```

### Why:

```js
Promise.reject(error)
```

### Why:

```js
sessionStorage.setItem("token", newAccessToken)
```

### Why:

```js
originalRequest.headers.Authorization =
    `Bearer ${newAccessToken}`;
```

Explain every one from the JavaScript/object/runtime perspective.

---

# PART 39 — VISUAL MENTAL MODELS

Include clear diagrams for:

## Axios instance

```text
axios
 ↓
create()
 ↓
axiosInstance
 ↓
baseURL
interceptors
defaults
methods
```

## Request

```text
Component
 ↓
API function
 ↓
Axios instance
 ↓
Request interceptor
 ↓
config
 ↓
Server
```

## Response

```text
Server
 ↓
response
 ↓
Response interceptor
 ↓
Component
```

## Refresh

```text
401
 ↓
Response interceptor
 ↓
Refresh token
 ↓
New access token
 ↓
Retry original request
```

## Concurrent requests

```text
A ──401──┐
B ──401──┤
C ──401──┤
         ↓
    One refresh
         ↓
   New token
         ↓
 A / B / C retry
```

---

# PART 40 — FINAL COMPLETE MENTAL MODEL

End the document with a section:

# Axios Authentication — Complete Mental Model

Explain:

```text
Axios
  ↓
Axios Instance
  ↓
API Functions
  ↓
Request Interceptor
  ↓
Request Config
  ↓
Authentication Header
  ↓
Server
  ↓
Response
  ↓
Response Interceptor
  ↓
Success OR Error
  ↓
401?
  ↓
Refresh Token
  ↓
New Access Token
  ↓
Retry Original Request
  ↓
Application
```

Explain every stage in natural language.

The final explanation should make it possible for me to reconstruct the implementation from understanding rather than memorizing code.

---

# CRITICAL WRITING REQUIREMENTS

Follow these requirements strictly:

## 1. EXTREMELY DETAILED

Do not create a shallow tutorial.

I want to understand:

- What
- Why
- How
- When
- What happens internally at a conceptual level
- What happens at runtime
- How objects move through the system
- How callbacks are executed
- How requests are retried
- How tokens move through the application

---

## 2. DO NOT TEACH BY MEMORIZATION

Whenever introducing code, explain:

> "Why does this line exist?"

For example, do not simply write:

```js
const axiosInstance = axios.create(...)
```

Explain why we need an instance and what problem it solves.

---

## 3. CONNECT EVERYTHING TO THE PREVIOUS THEORY

The previous MD already taught:

```text
config
response
callbacks
use()
registration
dot notation
interceptors
```

The new MD should build on that foundation.

Do not contradict the previous theory.

---

## 4. DISTINGUISH CONCEPTUAL MODELS FROM ACTUAL AXIOS INTERNALS

If using simplified examples such as:

```js
callbacks.push(...)
```

clearly label them as:

> Conceptual model — simplified for understanding.

Do not present simplified pseudocode as the actual Axios source code.

---

## 5. EVERY IMPORTANT CODE BLOCK MUST BE EXPLAINED

Do not dump large code blocks without explanation.

After each important implementation:

- Explain the code
- Explain the flow
- Explain why it works
- Explain what happens at runtime

---

## 6. USE CONSISTENT TERMINOLOGY

Always consistently use:

```text
config → request configuration
response → server response object
error → Axios error object
error.config → failed request configuration
access token → token used for protected API requests
refresh token → token used to obtain a new access token
interceptor → centralized request/response processing function
Axios instance → configured Axios client
```

---

## 7. PRACTICAL FIRST

This chapter should feel like:

> "I am building a real React application and learning Axios authentication while building it."

Not:

> "I am reading a theoretical definition."

---

## 8. DO NOT SKIP EDGE CASES

Cover:

- 401
- 403
- Network errors
- No `error.response`
- Expired refresh token
- Refresh failure
- Infinite loops
- Multiple simultaneous 401s
- Duplicate interceptor registration
- Logout during requests
- Retry failures

---

## 9. USE BEGINNER-FRIENDLY EXPLANATION BUT INTERVIEW-LEVEL DEPTH

Explain simply first.

Then explain technically.

For example:

> Simple explanation

followed by:

> Technical explanation

This should help me understand first and then speak confidently in interviews.

---

# FINAL REQUIREMENT

The final Markdown file should be **very long and comprehensive**.

It should be sufficient to take me from:

```text
"I understand what an interceptor is"
```

to:

```text
"I can build Axios instances, request/response interceptors,
authentication headers, token handling, refresh-token logic,
retry failed requests, handle concurrent 401s, debug the system,
and explain the entire architecture in an interview."
```

Do not reduce sections to summaries.

Do not skip implementation details.

Do not assume knowledge that has not already been established.

Build the understanding step by step.


# NEXT PART
# Axios Interceptors --- Practical Build Guide

> **Learning style:** We build the Axios authentication system step by
> step.\
> For every code block, the explanation comes immediately after it:
>
> **What are we doing? → What did the code do? → Why did we do it? →
> Runtime flow → What if we remove it?**

------------------------------------------------------------------------

# 1. Create the React Project and Install Axios

We first need Axios because Axios will be responsible for making our
HTTP requests.

``` bash
npm install axios
```

### What did we just do?

We installed the Axios package into the React project.

After installation, Axios becomes available for import:

``` js
import axios from "axios";
```

### Why did we do this?

Because we don't want to use the browser's `fetch()` API for this
project. We want Axios because it provides features such as:

-   Axios instances
-   request interceptors
-   response interceptors
-   request configuration
-   response/error handling
-   easier centralized HTTP configuration

------------------------------------------------------------------------

# 2. Create an Axios Instance

Instead of using Axios directly everywhere, we first create our own
configured Axios client.

### Code

``` js
import axios from "axios";

const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});

export default axiosInstance;
```

## What did we just do?

We created a separate Axios instance called `axiosInstance`.

Think of it as:

``` text
axios
   ↓
our configured copy/client
   ↓
axiosInstance
```

### `axios.create()`

``` js
axios.create(...)
```

creates a new Axios instance with configuration that belongs to our
application.

### `baseURL`

``` js
baseURL: "https://api.example.com"
```

tells Axios the common starting URL for our API requests.

So instead of:

``` js
axios.get("https://api.example.com/users");
```

we can write:

``` js
axiosInstance.get("/users");
```

Axios combines them:

``` text
baseURL
https://api.example.com

+
/users

=

https://api.example.com/users
```

### Why did we create an instance?

Because later we want to attach our interceptors to this specific
client.

Our architecture becomes:

``` text
axiosInstance
      ↓
request interceptor
      ↓
server
      ↓
response interceptor
```

### What if we don't create an instance?

We could still use Axios, but our application would not have one
centralized configured client for this API.

------------------------------------------------------------------------

# 3. Use the Axios Instance for an API Request

Now let's actually make a request.

### Code

``` js
import axiosInstance from "./api/axiosInstance";

const getUsers = async () => {
  const response = await axiosInstance.get("/users");

  console.log(response.data);
};
```

## What did we just do?

We called the `/users` endpoint using our Axios instance.

``` js
axiosInstance.get("/users")
```

means:

> Make a GET request to the `/users` endpoint using the configuration
> stored in `axiosInstance`.

Because our instance has:

``` js
baseURL: "https://api.example.com"
```

the actual URL becomes:

``` text
https://api.example.com/users
```

### What is `response`?

Axios gives us the server response.

Conceptually:

``` js
{
  data: ...,
  status: 200,
  headers: ...,
  config: ...
}
```

So:

``` js
response.data
```

means:

``` text
response
   ↓
data
```

### Runtime

``` text
getUsers()
   ↓
axiosInstance.get("/users")
   ↓
Axios prepares request
   ↓
Server
   ↓
Response
   ↓
response
   ↓
response.data
```

At this point, we have a normal Axios request.

Now we have a problem.

------------------------------------------------------------------------

# 4. The Problem: Protected APIs Need a Token

Suppose `/users` is protected.

The server expects:

``` text
Authorization: Bearer ACCESS_TOKEN
```

We could manually write:

``` js
axiosInstance.get("/users", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

But then every protected API would need the same code.

For example:

``` js
axiosInstance.get("/users", ...);
axiosInstance.get("/orders", ...);
axiosInstance.get("/profile", ...);
axiosInstance.get("/products", ...);
```

We don't want authentication logic repeated everywhere.

So now we introduce a request interceptor.

------------------------------------------------------------------------

# 5. Add a Request Interceptor

### Code

``` js
axiosInstance.interceptors.request.use((config) => {
  const token = sessionStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

## What are we trying to do?

We want this to happen automatically:

``` text
API request
    ↓
Get token
    ↓
Add token to request
    ↓
Send request
```

We don't want every component to manually perform those steps.

------------------------------------------------------------------------

## What did we just do?

### 1. `axiosInstance.interceptors`

We accessed the interceptor system belonging to our Axios instance.

### 2. `.request`

We selected the **request stage**.

That means our function will run while Axios is preparing a request,
before the request reaches the server.

### 3. `.use(...)`

We registered a callback.

We are essentially telling Axios:

> "Whenever a request goes through this instance, call this function at
> the request-interceptor stage."

### 4. `(config) => {}`

Axios calls our callback and gives us the current request configuration.

That object is called `config`.

Conceptually:

``` js
{
  method: "GET",
  url: "/users",
  headers: {}
}
```

The actual Axios config contains more information, but this is the
mental model we need.

### 5. Read the token

``` js
const token = sessionStorage.getItem("token");
```

We retrieve the access token that was stored after authentication.

### 6. Add the Authorization header

``` js
config.headers.Authorization = `Bearer ${token}`;
```

We modify the request configuration.

Before:

``` js
config.headers = {}
```

After:

``` js
config.headers = {
  Authorization: "Bearer ACCESS_TOKEN"
}
```

### 7. Return the config

``` js
return config;
```

We give the processed request configuration back to Axios.

Axios can now continue sending the request.

------------------------------------------------------------------------

# 6. Understand the Request Interceptor Runtime

When this happens:

``` js
axiosInstance.get("/users");
```

the flow is now:

``` text
Component
   ↓
axiosInstance.get("/users")
   ↓
Axios creates/prepares request config
   ↓
Request interceptor receives config
   ↓
Read token
   ↓
Modify config.headers.Authorization
   ↓
return config
   ↓
Axios sends request
   ↓
Server
```

The important point is:

> The interceptor did not create the request. Axios already had the
> request. The interceptor got an opportunity to work on its
> configuration before Axios sent it.

------------------------------------------------------------------------

# 7. Why Do We Pass a Function to `.use()`?

Look at:

``` js
axiosInstance.interceptors.request.use((config) => {
  ...
});
```

We are passing a function.

We are **not** executing it ourselves.

Axios stores the callback and calls it later when a request reaches this
stage.

This is the same JavaScript idea as:

``` js
button.addEventListener("click", handleClick);
```

We give the function now.

Something else calls it later.

So:

``` js
use(myFunction)
```

means:

> Register this function for later.

Whereas:

``` js
use(myFunction())
```

would mean:

> Execute the function now and pass its return value.

That is not what we want.

------------------------------------------------------------------------

# 8. What If We Don't Return `config`?

The interceptor's job is to process the request configuration and pass
it forward.

So the normal pattern is:

``` js
(config) => {
  // modify config
  return config;
}
```

Think:

``` text
Axios
  ↓
config
  ↓
interceptor modifies config
  ↓
config returned
  ↓
Axios continues
```

------------------------------------------------------------------------

# 9. Add a Response Interceptor

Now our request can reach the server.

But what happens when the server responds?

We want a central place to handle responses and errors.

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => {
    return response;
  },
  (error) => {
    return Promise.reject(error);
  }
);
```

## What did we just do?

We registered a **response interceptor**.

Notice that `.use()` receives two functions here.

``` js
.use(
  successCallback,
  errorCallback
)
```

The first callback handles the successful response.

The second callback handles the rejected/error path.

------------------------------------------------------------------------

## First callback

``` js
(response) => {
  return response;
}
```

Axios gives us the successful response.

We return it so the response can continue to the code that called the
API.

------------------------------------------------------------------------

## Second callback

``` js
(error) => {
  return Promise.reject(error);
}
```

Axios gives us the error.

If we don't recover from it, we reject the Promise so the error
continues to the caller.

For example:

``` js
try {
  await getUsers();
} catch (error) {
  console.log(error);
}
```

------------------------------------------------------------------------

# 10. Understand `response`

A successful Axios response can conceptually look like:

``` js
{
  data: {...},
  status: 200,
  statusText: "OK",
  headers: {...},
  config: {...}
}
```

Therefore:

``` js
response.data
```

means:

``` text
response
   ↓
data
```

And:

``` js
response.status
```

means:

``` text
response
   ↓
status
```

------------------------------------------------------------------------

# 11. Understand `error.response.status`

When a request fails with an HTTP response, Axios error information can
contain:

``` js
error.response
```

Inside that:

``` js
error.response.status
```

So:

``` js
error.response.status
```

means:

``` text
error
  ↓
response
  ↓
status
```

If the server returned:

``` text
401 Unauthorized
```

we can check:

``` js
error.response?.status === 401
```

The `?.` prevents an error if `response` is absent.

------------------------------------------------------------------------

# 12. Handle a 401

Now we have the actual authentication problem.

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => {
    return response;
  },
  async (error) => {
    if (error.response?.status === 401) {
      // authentication recovery
    }

    return Promise.reject(error);
  }
);
```

## What did we just do?

We told the response interceptor:

> If the server says `401`, perform authentication-recovery logic.

We are not saying that every `401` automatically means "the access token
expired."

We are saying that **in our authentication design**, a `401` is the
condition we are checking for possible recovery.

------------------------------------------------------------------------

# 13. Why Do We Need the Original Request?

Suppose the user requested:

``` text
GET /users
```

and the access token was expired.

The server returns:

``` text
401
```

We want to do:

``` text
Refresh token
     ↓
Get new access token
     ↓
Retry GET /users
```

But how do we know what the failed request was?

Axios gives us:

``` js
error.config
```

### Code

``` js
const originalRequest = error.config;
```

## What did we just do?

We stored the configuration of the request that failed.

We call it:

``` js
originalRequest
```

because this is the request we eventually want to retry.

Conceptually:

``` js
originalRequest = {
  method: "GET",
  url: "/users",
  headers: {
    Authorization: "Bearer OLD_TOKEN"
  }
}
```

------------------------------------------------------------------------

# 14. Why Is `error.config` So Important?

Imagine the failed endpoint could be anything:

``` text
/users
/orders
/profile
/products
```

The interceptor should not need separate code for each endpoint.

`error.config` gives us the exact request that failed.

So we can do:

``` js
axiosInstance(error.config);
```

instead of reconstructing:

``` js
axiosInstance.get("/users");
```

The retry mechanism therefore works generically.

------------------------------------------------------------------------

# 15. Add the Retry Protection

There is a problem.

Suppose:

``` text
Request
  ↓
401
  ↓
Refresh
  ↓
Retry
  ↓
401 again
```

If the interceptor keeps refreshing forever, we get:

``` text
401
 ↓
refresh
 ↓
retry
 ↓
401
 ↓
refresh
 ↓
retry
 ↓
...
```

So we need a marker.

### Code

``` js
if (
  error.response?.status === 401 &&
  !error.config._retry
) {
  error.config._retry = true;

  // refresh and retry
}
```

## What did we just do?

We check two things:

### Condition 1

``` js
error.response?.status === 401
```

The server returned an authentication failure.

### Condition 2

``` js
!error.config._retry
```

This request has not already gone through our retry process.

Then:

``` js
error.config._retry = true;
```

marks the request.

Important:

> `_retry` is **not an Axios keyword**. It is a custom property that we
> add to the request configuration.

------------------------------------------------------------------------

# 16. Why Do We Need `_retry`?

Without it:

``` text
Request A
   ↓
401
   ↓
refresh
   ↓
retry
   ↓
401
   ↓
refresh
   ↓
retry
   ↓
...
```

With it:

``` text
Request A
   ↓
401
   ↓
_retry = false
   ↓
refresh
   ↓
_retry = true
   ↓
retry
   ↓
401
   ↓
already retried
   ↓
stop recovery
```

The marker protects us from an infinite recovery loop.

------------------------------------------------------------------------

# 17. Create the Refresh Client

There is another problem.

Suppose our refresh request itself fails.

If we send the refresh request through the same Axios instance and the
same response interceptor:

``` text
Refresh request
      ↓
401
      ↓
Response interceptor
      ↓
Refresh again
      ↓
401
      ↓
Refresh again
      ↓
...
```

To avoid this recursive situation, we can create a separate Axios client
for the refresh operation.

### Code

``` js
const refreshClient = axios.create({
  baseURL: "https://api.example.com"
});
```

## What did we just do?

We created another Axios client.

This client is specifically useful for the refresh operation.

It does not automatically inherit the interceptors registered on
`axiosInstance`.

So our architecture becomes:

``` text
Normal API requests
       ↓
axiosInstance
       ↓
interceptors

Refresh request
       ↓
refreshClient
       ↓
refresh endpoint
```

------------------------------------------------------------------------

# 18. Send the Refresh Request

Now we need to use the refresh credential.

### Code

``` js
const refreshToken =
  sessionStorage.getItem("refreshToken");

const refreshResponse = await refreshClient.post(
  "/refresh",
  { refreshToken }
);
```

## What did we just do?

First:

``` js
sessionStorage.getItem("refreshToken");
```

gets the refresh token.

Then:

``` js
refreshClient.post("/refresh", { refreshToken });
```

sends that refresh token to the backend's refresh endpoint.

The backend validates the refresh credential.

If valid, it can return a new access token.

------------------------------------------------------------------------

# 19. Extract the New Access Token

### Code

``` js
const newAccessToken =
  refreshResponse.data.accessToken;
```

## What did we just do?

The refresh response contains the new access token.

Conceptually:

``` js
refreshResponse = {
  data: {
    accessToken: "NEW_TOKEN"
  }
}
```

So:

``` js
refreshResponse.data.accessToken
```

means:

``` text
refreshResponse
      ↓
data
      ↓
accessToken
```

We store it in:

``` js
newAccessToken
```

------------------------------------------------------------------------

# 20. Store the New Access Token

### Code

``` js
sessionStorage.setItem(
  "token",
  newAccessToken
);
```

## What did we just do?

We replaced the old access token in storage.

Before:

``` text
token → OLD_TOKEN
```

After:

``` text
token → NEW_TOKEN
```

Why?

Because future API requests will read the token from storage in the
request interceptor.

So future requests can automatically use the new token.

------------------------------------------------------------------------

# 21. But There Is a Very Important Problem

Updating storage is **not enough**.

Remember:

``` js
const originalRequest = error.config;
```

That request already exists.

Its headers may still contain:

``` text
Authorization: Bearer OLD_TOKEN
```

Changing:

``` js
sessionStorage
```

does not magically change:

``` js
originalRequest.headers
```

So we must update the failed request separately.

------------------------------------------------------------------------

# 22. Update the Original Request

### Code

``` js
originalRequest.headers.Authorization =
  `Bearer ${newAccessToken}`;
```

## What did we just do?

We changed the Authorization header of the request that previously
failed.

Before:

``` text
Authorization: Bearer OLD_TOKEN
```

After:

``` text
Authorization: Bearer NEW_TOKEN
```

Now the request is ready to be retried with the new access token.

This is one of the most important practical concepts in token refresh.

------------------------------------------------------------------------

# 23. Retry the Original Request

### Code

``` js
return axiosInstance(originalRequest);
```

## What did we just do?

We sent the failed request again.

But instead of rebuilding the request manually, we use:

``` js
originalRequest
```

which already contains:

-   method
-   URL
-   headers
-   request body
-   query parameters
-   other request configuration

So:

``` js
axiosInstance(originalRequest)
```

means:

> Use this configuration to make the HTTP request again.

------------------------------------------------------------------------

# 24. Complete Basic Refresh Interceptor

Now all the pieces fit together.

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => {
    return response;
  },

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status === 401 &&
      !originalRequest._retry
    ) {
      originalRequest._retry = true;

      const refreshToken =
        sessionStorage.getItem("refreshToken");

      const refreshResponse = await refreshClient.post(
        "/refresh",
        { refreshToken }
      );

      const newAccessToken =
        refreshResponse.data.accessToken;

      sessionStorage.setItem(
        "token",
        newAccessToken
      );

      originalRequest.headers.Authorization =
        `Bearer ${newAccessToken}`;

      return axiosInstance(originalRequest);
    }

    return Promise.reject(error);
  }
);
```

## What did we just build?

We built this flow:

``` text
API request
     ↓
Request interceptor
     ↓
Attach access token
     ↓
Server
     ↓
401
     ↓
Response interceptor
     ↓
Get original request
     ↓
Check _retry
     ↓
Get refresh token
     ↓
Call refresh endpoint
     ↓
Receive new access token
     ↓
Update storage
     ↓
Update original request
     ↓
Retry original request
     ↓
Server
     ↓
Success
```

------------------------------------------------------------------------

# 25. Why Does the Request Interceptor Run Again on Retry?

This is important.

When we do:

``` js
return axiosInstance(originalRequest);
```

we are making another request through `axiosInstance`.

That means the request goes through the request interceptor again.

So conceptually:

``` text
Original request
    ↓
Request interceptor
    ↓
401
    ↓
Response interceptor
    ↓
Refresh
    ↓
Retry using axiosInstance
    ↓
Request interceptor again
    ↓
Server
```

This is another reason why the authentication flow must be designed
carefully.

The original request has already been given the new token, and the
request interceptor can also see the current token from storage.

------------------------------------------------------------------------

# 26. What If Refresh Fails?

We must not pretend authentication recovery succeeded.

### Code

``` js
try {
  const refreshResponse = await refreshClient.post(
    "/refresh",
    { refreshToken }
  );

  // update token and retry
} catch (refreshError) {
  sessionStorage.removeItem("token");
  sessionStorage.removeItem("refreshToken");

  return Promise.reject(refreshError);
}
```

## What did we just do?

If the refresh operation itself fails, we remove the authentication
information and reject the error.

The exact logout/navigation behavior depends on the application's
authentication design.

The important principle is:

> If we cannot obtain a valid new access token, we should not endlessly
> retry the original request.

------------------------------------------------------------------------

# 27. What Happens With Multiple 401 Requests?

Now consider:

``` text
/users     → 401
/orders    → 401
/profile   → 401
```

all happening around the same time.

A naive implementation could do:

``` text
/users   → refresh
/orders  → refresh
/profile → refresh
```

That means three refresh requests.

We generally want:

``` text
/users   → 401
/orders  → 401
/profile → 401
             ↓
       ONE refresh
             ↓
         NEW TOKEN
             ↓
       retry all failed requests
```

This requires coordination.

------------------------------------------------------------------------

# 28. Start With `isRefreshing`

### Code

``` js
let isRefreshing = false;
```

## What did we just do?

We created a variable that tells us whether a refresh request is
currently running.

Possible states:

``` text
false → no refresh happening
true  → refresh in progress
```

This is not an Axios feature.

It is our own application-level state.

------------------------------------------------------------------------

# 29. Create a Failed Request Queue

### Code

``` js
let failedQueue = [];
```

## What did we just do?

We created an array to hold requests that receive `401` while another
request is already refreshing the token.

Conceptually:

``` text
Refresh in progress
        ↓
Request B → wait
Request C → wait
Request D → wait
```

The queue represents those waiting requests.

------------------------------------------------------------------------

# 30. Queue Helper

A common implementation uses a helper like:

``` js
const processQueue = (error, token = null) => {
  failedQueue.forEach((promise) => {
    if (error) {
      promise.reject(error);
    } else {
      promise.resolve(token);
    }
  });

  failedQueue = [];
};
```

## What did we just do?

We created a function that tells all waiting requests what happened.

If refresh failed:

``` js
processQueue(error);
```

the waiting requests are rejected.

If refresh succeeded:

``` js
processQueue(null, newAccessToken);
```

the waiting requests receive the new token and can retry.

### Why clear the queue?

After processing:

``` js
failedQueue = [];
```

we don't want old requests remaining in memory and being processed
again.

------------------------------------------------------------------------

# 31. The Coordinated Refresh Pattern

A common practical structure is:

``` js
if (error.response?.status === 401 && !originalRequest._retry) {
  if (isRefreshing) {
    return new Promise((resolve, reject) => {
      failedQueue.push({
        resolve,
        reject
      });
    }).then((token) => {
      originalRequest.headers.Authorization =
        `Bearer ${token}`;

      return axiosInstance(originalRequest);
    });
  }

  originalRequest._retry = true;
  isRefreshing = true;

  try {
    const refreshToken =
      sessionStorage.getItem("refreshToken");

    const refreshResponse = await refreshClient.post(
      "/refresh",
      { refreshToken }
    );

    const newAccessToken =
      refreshResponse.data.accessToken;

    sessionStorage.setItem(
      "token",
      newAccessToken
    );

    processQueue(null, newAccessToken);

    originalRequest.headers.Authorization =
      `Bearer ${newAccessToken}`;

    return axiosInstance(originalRequest);
  } catch (refreshError) {
    processQueue(refreshError, null);
    throw refreshError;
  } finally {
    isRefreshing = false;
  }
}
```

## What did we just build?

There are now two possible paths.

### First 401

``` text
401
 ↓
isRefreshing = false
 ↓
Start refresh
 ↓
isRefreshing = true
```

### Second 401 while refresh is running

``` text
401
 ↓
isRefreshing = true
 ↓
Don't start another refresh
 ↓
Put request into failedQueue
 ↓
Wait
```

### Refresh succeeds

``` text
New token
 ↓
processQueue(...)
 ↓
waiting requests receive token
 ↓
retry
```

### Refresh fails

``` text
Refresh fails
 ↓
processQueue(error)
 ↓
waiting requests reject
```

------------------------------------------------------------------------

# 32. Why `finally`?

### Code

``` js
finally {
  isRefreshing = false;
}
```

## What did we just do?

We guarantee that the refresh state returns to:

``` text
false
```

whether the refresh succeeds or fails.

Without resetting it, the application could incorrectly think:

``` text
refresh is still happening
```

even after the operation has finished.

------------------------------------------------------------------------

# 33. Full Practical File

At this point, the Axios instance can look like this:

### `src/api/axiosInstance.js`

``` js
import axios from "axios";

const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});

const refreshClient = axios.create({
  baseURL: "https://api.example.com"
});

let isRefreshing = false;
let failedQueue = [];

const processQueue = (error, token = null) => {
  failedQueue.forEach((promise) => {
    if (error) {
      promise.reject(error);
    } else {
      promise.resolve(token);
    }
  });

  failedQueue = [];
};

axiosInstance.interceptors.request.use(
  (config) => {
    const token = sessionStorage.getItem("token");

    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
  }
);

axiosInstance.interceptors.response.use(
  (response) => {
    return response;
  },

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status !== 401 ||
      originalRequest._retry
    ) {
      return Promise.reject(error);
    }

    if (isRefreshing) {
      return new Promise((resolve, reject) => {
        failedQueue.push({
          resolve,
          reject
        });
      }).then((token) => {
        originalRequest.headers.Authorization =
          `Bearer ${token}`;

        return axiosInstance(originalRequest);
      });
    }

    originalRequest._retry = true;
    isRefreshing = true;

    try {
      const refreshToken =
        sessionStorage.getItem("refreshToken");

      const refreshResponse = await refreshClient.post(
        "/refresh",
        { refreshToken }
      );

      const newAccessToken =
        refreshResponse.data.accessToken;

      sessionStorage.setItem(
        "token",
        newAccessToken
      );

      processQueue(null, newAccessToken);

      originalRequest.headers.Authorization =
        `Bearer ${newAccessToken}`;

      return axiosInstance(originalRequest);
    } catch (refreshError) {
      processQueue(refreshError, null);

      sessionStorage.removeItem("token");
      sessionStorage.removeItem("refreshToken");

      throw refreshError;
    } finally {
      isRefreshing = false;
    }
  }
);

export default axiosInstance;
```

## What did we just build?

This one file now handles the common HTTP authentication lifecycle.

### Request side

``` text
Request
 ↓
Read access token
 ↓
Attach Authorization header
 ↓
Send
```

### Response side

``` text
Success
 ↓
Return response
```

or:

``` text
401
 ↓
Check retry
 ↓
If another refresh is running → wait
 ↓
Otherwise → refresh
 ↓
Get new token
 ↓
Update storage
 ↓
Release queued requests
 ↓
Retry failed request
```

------------------------------------------------------------------------

# 34. API Functions Become Simple

Once the Axios instance handles authentication, API functions don't need
to manually attach tokens.

### Code

``` js
import axiosInstance from "./axiosInstance";

export const getUsers = () => {
  return axiosInstance.get("/users");
};

export const getOrders = () => {
  return axiosInstance.get("/orders");
};
```

## What did we just do?

We created small functions representing API operations.

The functions don't contain:

``` js
sessionStorage.getItem("token")
```

or:

``` js
Authorization: `Bearer ${token}`
```

because the interceptor handles that centrally.

So:

``` js
getUsers()
```

simply means:

> Call the users API.

------------------------------------------------------------------------

# 35. Component Usage

### Code

``` jsx
import { useEffect, useState } from "react";
import { getUsers } from "./api/userApi";

function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const loadUsers = async () => {
      const response = await getUsers();
      setUsers(response.data);
    };

    loadUsers();
  }, []);

  return (
    <div>
      {users.map((user) => (
        <p key={user.id}>{user.name}</p>
      ))}
    </div>
  );
}

export default Users;
```

## What did we just achieve?

The React component knows only:

``` text
I need users
 ↓
call getUsers()
```

It does not need to know:

-   where the token is stored
-   how the token is attached
-   what happens when the token expires
-   how refresh works
-   how retry works

That logic lives in the HTTP layer.

This is the main practical benefit of interceptors.

------------------------------------------------------------------------

# 36. AuthContext Has a Different Responsibility

Suppose we have:

``` js
AuthContext
```

It can manage:

``` text
user
isAuthenticated
login()
logout()
```

The Axios interceptor manages:

``` text
access token
request headers
401
refresh
retry
```

So:

``` text
AuthContext
      ↓
Application authentication state

Axios interceptor
      ↓
HTTP authentication behavior
```

They are not replacements for each other.

------------------------------------------------------------------------

# 37. Protected Routes Have Another Responsibility

A protected route might do:

``` text
User visits /dashboard
       ↓
Is authenticated?
     ↙     ↘
   yes      no
    ↓        ↓
dashboard   login
```

This controls frontend navigation.

The interceptor controls API communication.

So:

``` text
ProtectedRoute
→ Can the user access this frontend route?

Interceptor
→ How should this HTTP request/response be handled?
```

------------------------------------------------------------------------

# 38. What Happens If We Use Plain `axios`?

Suppose our interceptors are attached to:

``` js
axiosInstance
```

but a component does:

``` js
axios.get("/users");
```

That request is not automatically using the interceptors registered on:

``` js
axiosInstance
```

So the practical rule is:

``` text
Application API calls
        ↓
Use axiosInstance
```

not randomly:

``` text
axios
axiosInstance
axios
axiosInstance
```

unless there is a deliberate reason.

------------------------------------------------------------------------

# 39. Debugging the Request Interceptor

Temporarily add:

### Code

``` js
axiosInstance.interceptors.request.use((config) => {
  console.log("REQUEST CONFIG:", config);

  const token = sessionStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

## What did we just add?

A log before the token modification.

Now when the request runs, we can inspect:

``` text
method
url
headers
```

This helps us see what Axios is actually processing.

------------------------------------------------------------------------

# 40. Debugging the Response Interceptor

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => {
    console.log("SUCCESS RESPONSE:", response);
    return response;
  },

  (error) => {
    console.log("ERROR:", error);
    return Promise.reject(error);
  }
);
```

## What did we just add?

We can now see whether the response went through:

``` text
success callback
```

or:

``` text
error callback
```

------------------------------------------------------------------------

# 41. Network Tab Verification

Open:

``` text
Browser DevTools
→ Network
```

For a normal authenticated request, inspect:

``` text
Request Headers
    ↓
Authorization: Bearer ...
```

If the token expires, a refresh flow may look conceptually like:

``` text
/users      401
/refresh    200
/users      200
```

The second `/users` is the retry.

This is much better than simply trusting a console message because the
Network tab shows what was actually sent to the server.

------------------------------------------------------------------------

# 42. Important "What If?" Questions

## What if I remove the request interceptor?

The token will not automatically be attached.

Protected APIs may reject the request.

## What if I remove `return config`?

The interceptor does not correctly pass its processed configuration
forward.

## What if I remove the response interceptor?

There is no centralized location for the refresh/recovery behavior.

## What if I update storage but don't update `originalRequest`?

The failed request can still contain the old token.

## What if I don't retry?

The original API operation remains failed even after obtaining a new
token.

## What if I don't use `_retry`?

A repeated `401` can repeatedly trigger refresh logic.

## What if I use the same interceptor for refresh?

The refresh request can recursively trigger the same refresh mechanism.

## What if every 401 starts a refresh?

Multiple requests can create multiple refresh operations.

## What if I use `axios` instead of `axiosInstance`?

That request may bypass the interceptors attached to the instance.

------------------------------------------------------------------------

# 43. The Entire Runtime Flow

## Normal Request

``` text
Component
   ↓
getUsers()
   ↓
axiosInstance.get("/users")
   ↓
Request interceptor
   ↓
Read token
   ↓
Add Authorization header
   ↓
Server
   ↓
200
   ↓
Response interceptor success callback
   ↓
return response
   ↓
Component
```

## Expired Access Token

``` text
Component
   ↓
getUsers()
   ↓
Request interceptor
   ↓
OLD_TOKEN
   ↓
Server
   ↓
401
   ↓
Response error interceptor
   ↓
error.config
   ↓
_check _retry
   ↓
Refresh token
   ↓
Refresh endpoint
   ↓
NEW_TOKEN
   ↓
Update storage
   ↓
Update originalRequest
   ↓
Retry originalRequest
   ↓
Request interceptor
   ↓
Server
   ↓
200
   ↓
Component
```

## Multiple 401s

``` text
Request A → 401
                \
Request B → 401 → ONE refresh
                /
Request C → 401

                ↓

            NEW TOKEN
                ↓
       retry A, B, C
```

------------------------------------------------------------------------

# 44. Interview Answer Based on What We Built

## 30-second answer

> Axios interceptors are functions that run automatically during the
> Axios request and response lifecycle. In our application, the request
> interceptor reads the access token and adds it to the Authorization
> header before the request is sent. The response interceptor handles
> successful responses and authentication errors. When a protected
> request returns 401, we can use the refresh token to obtain a new
> access token, update the failed request's configuration, and retry it.
> We also use a retry marker to prevent infinite refresh loops.

------------------------------------------------------------------------

## 1-minute answer

> I use an Axios instance so that the application's API configuration
> and interceptor behavior are centralized. The request interceptor
> receives Axios's request `config`, reads the access token from
> storage, adds it to `config.headers.Authorization`, and returns the
> config so Axios can send the request.
>
> On the response side, the interceptor has a success callback and an
> error callback. If the server returns 401, I access `error.config`,
> which represents the request that failed. I use a custom `_retry`
> property to prevent repeated refresh attempts. Then I send the refresh
> token to a refresh endpoint using a separate Axios client, receive the
> new access token, update storage, update the original request's
> Authorization header, and retry the original request. If multiple
> requests fail with 401 at the same time, I can coordinate them with an
> `isRefreshing` flag and a queue so that only one refresh request is
> performed.

------------------------------------------------------------------------

## 2--3-minute answer

> In a React application, I prefer to create a dedicated Axios instance
> using `axios.create()` because it gives me one configured HTTP client
> for the application's API. I can put the base URL and interceptor
> behavior in one place.
>
> The first interceptor is the request interceptor. It runs before the
> request reaches the server. Axios gives the interceptor the request
> configuration through the `config` parameter. I read the current
> access token from storage and add it to
> `config.headers.Authorization`. Then I return the config so Axios can
> continue with the modified request.
>
> The response interceptor runs when Axios receives a server response.
> It has a success callback and an error callback. For normal successful
> responses, we simply return the response. For an error, we can inspect
> `error.response.status`. If the backend uses 401 to indicate an
> expired authentication state, we can start a refresh flow.
>
> The important part is `error.config`. It represents the configuration
> of the request that failed. I store it as `originalRequest` because
> after getting a new access token, I want to retry that exact request
> rather than manually reconstructing it.
>
> Before refreshing, I use a custom `_retry` marker. This is not an
> Axios keyword. It is simply a property we add to the request
> configuration to make sure the same request does not continuously
> enter the refresh logic if it receives another 401.
>
> We then use the refresh token to call the refresh endpoint. I prefer a
> separate Axios client for this operation so the refresh request itself
> does not accidentally trigger the same refresh interceptor
> recursively.
>
> When the refresh succeeds, I update the stored access token. But
> updating storage alone is not enough because the failed request
> already has its own configuration object, which can still contain the
> old Authorization header. Therefore, I update
> `originalRequest.headers.Authorization` as well and retry it using
> `axiosInstance(originalRequest)`.
>
> If several requests receive 401 at the same time, starting a separate
> refresh for every request is inefficient. We can use an `isRefreshing`
> flag and a queue so that one request performs the refresh while the
> others wait. Once the new token is available, the queued requests are
> retried.
>
> This architecture keeps authentication behavior out of individual
> React components. Components call API functions, API functions use the
> Axios instance, and the Axios interceptors handle the common HTTP
> authentication lifecycle.

------------------------------------------------------------------------

# 45. Final Practical Mental Model

Do not memorize the complete code first.

Understand this chain:

``` text
1. Create Axios instance
        ↓
2. API calls use that instance
        ↓
3. Request interceptor receives config
        ↓
4. Add access token
        ↓
5. Return config
        ↓
6. Server receives request
        ↓
7. Response comes back
        ↓
8. Response interceptor receives response/error
        ↓
9. If 401 → get failed request from error.config
        ↓
10. Check _retry
        ↓
11. Refresh access token
        ↓
12. Update storage
        ↓
13. Update original request
        ↓
14. Retry original request
        ↓
15. If multiple 401s → coordinate with queue
```

The key objects are:

``` text
config
→ request Axios is preparing

response
→ successful server response

error
→ failed Axios operation

error.response
→ server response associated with the error

error.response.status
→ HTTP status

error.config
→ request configuration that failed

originalRequest
→ our variable referring to error.config

newAccessToken
→ newly issued access token
```

And the key responsibilities are:

``` text
Request interceptor
→ Prepare request

Response interceptor
→ Process response/error

Refresh logic
→ Obtain new access token

Original request
→ Retry failed operation

Queue
→ Coordinate simultaneous 401s
```

------------------------------------------------------------------------

# 46. The Most Important Thing to Remember

Don't memorize:

``` js
axiosInstance.interceptors.response.use(...)
```

as a random piece of syntax.

Understand what is happening:

``` text
axiosInstance
      ↓
its interceptor system
      ↓
response stage
      ↓
register a callback
      ↓
Axios calls that callback when a response arrives
```

And don't memorize:

``` js
error.config
```

as random dot notation.

Understand:

``` text
error
 ↓
information about the failed operation
 ↓
response
 ↓
server's response
```

and:

``` text
error
 ↓
config
 ↓
configuration of the request that failed
```

That understanding is what lets you write the code yourself.
