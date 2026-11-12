# Axios Interceptors --- Practical Code Examples for Interviews

This guide is designed so I can **show the code to an interviewer and
explain what I am doing while writing it**.

The approach is:

**Build something → show the code → immediately explain what we just did
→ explain runtime behavior → move to the next step.**

------------------------------------------------------------------------

## 1. Install Axios

### What are we doing?

First, I install Axios because I want to use it as the HTTP client for
communicating with my backend APIs.

``` bash
npm install axios
```

### What did we just do?

I installed the Axios package into my React project.

Now I can import Axios and use methods such as:

-   `axios.get()`
-   `axios.post()`
-   `axios.put()`
-   `axios.delete()`

### What would I say to the interviewer?

> "I am using Axios as my HTTP client. Instead of directly using fetch
> everywhere, I can centralize API configuration and authentication
> behavior using an Axios instance and interceptors."

------------------------------------------------------------------------

# 2. Create an Axios Instance

### What are we doing?

Instead of writing the backend URL repeatedly in every API call, I
create one reusable Axios instance.

``` js
// api/axiosInstance.js

import axios from "axios";

const axiosInstance = axios.create({
  baseURL: "https://api.example.com",
});

export default axiosInstance;
```

### What did we just do?

``` js
axios.create(...)
```

`axios.create()` is a **method provided by Axios**.

It creates and returns a new Axios instance.

So here:

``` js
const axiosInstance = axios.create(...)
```

-   `axios` → the Axios library object.
-   `create()` → method used to create a custom Axios instance.
-   `axiosInstance` → the object returned by `create()`.
-   `baseURL` → the common URL that Axios automatically uses for
    requests made through this instance.

For example:

``` js
axiosInstance.get("/users");
```

will be sent to:

``` text
https://api.example.com/users
```

### What would I say to the interviewer?

> "I am creating a custom Axios instance so that common configuration
> such as the base URL, headers, timeout, and interceptors can be
> maintained in one place."

------------------------------------------------------------------------

# 3. Use the Axios Instance

### What are we doing?

Now I use the instance to make an API request.

``` js
import axiosInstance from "./axiosInstance";

const getUsers = async () => {
  const response = await axiosInstance.get("/users");

  return response.data;
};
```

### What did we just do?

``` js
axiosInstance.get("/users")
```

I am calling the `GET /users` endpoint using my custom Axios instance.

Because the instance already has:

``` js
baseURL: "https://api.example.com"
```

Axios combines them.

So the actual request becomes:

``` text
GET https://api.example.com/users
```

The returned value is stored in:

``` js
response
```

Axios gives us information such as:

``` js
response.data
response.status
response.headers
```

I return:

``` js
response.data
```

because normally my application needs the actual response body.

### Runtime flow

``` text
getUsers()
   ↓
axiosInstance.get("/users")
   ↓
Axios creates the HTTP request
   ↓
GET https://api.example.com/users
   ↓
Backend responds
   ↓
response
   ↓
response.data
```

### What would I say?

> "I am using the custom instance instead of the global Axios object.
> This means all requests made through this instance can share the same
> configuration and later the same interceptors."

------------------------------------------------------------------------

# 4. Add a Request Interceptor

### What are we doing?

Now I want to automatically attach the access token to every
authenticated request.

Instead of manually writing the Authorization header in every API
function, I use a request interceptor.

``` js
axiosInstance.interceptors.request.use((config) => {
  const token = sessionStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

### What did we just do?

Let's understand the code from the inside out.

### `axiosInstance.interceptors`

I am accessing the interceptor system of my Axios instance.

Axios provides interceptors for two stages:

``` js
interceptors.request
interceptors.response
```

Here I am working with the request stage.

### `.request`

``` js
axiosInstance.interceptors.request
```

This means:

> "I want to intercept requests before Axios sends them to the server."

### `.use(...)`

``` js
.use((config) => {
   ...
})
```

`use()` registers a callback function.

Axios will call this callback whenever a request is about to be sent.

### `config`

``` js
(config) => {
```

`config` is the configuration object of the current request.

It contains information about the request, such as:

``` js
config.url
config.method
config.headers
config.params
config.data
```

The important point is:

> This is the request that Axios is currently preparing to send.

### Get the token

``` js
const token = sessionStorage.getItem("token");
```

I retrieve the access token that I previously stored in
`sessionStorage`.

### Add the token

``` js
config.headers.Authorization = `Bearer ${token}`;
```

I modify the current request's headers.

For example, if the token is:

``` text
abc123
```

the header becomes:

``` text
Authorization: Bearer abc123
```

### Return the config

``` js
return config;
```

This is very important.

I return the modified request configuration back to Axios.

Axios then continues processing that request.

### What would I say to the interviewer?

> "The request interceptor runs before the request is sent. I get the
> access token from storage, add it to the Authorization header of the
> current request, and return the modified config so Axios can continue
> with the request."

------------------------------------------------------------------------

# 5. Understand the Request Interceptor Flow

Once the interceptor is added:

``` js
axiosInstance.get("/users");
```

the flow becomes:

``` text
Component
   ↓
axiosInstance.get("/users")
   ↓
Request interceptor
   ↓
Get token from sessionStorage
   ↓
Add Authorization header
   ↓
return config
   ↓
Axios sends request
   ↓
Backend
```

### Why is this better?

Without an interceptor, I might have to do this everywhere:

``` js
axiosInstance.get("/users", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

Then again:

``` js
axiosInstance.get("/products", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

The interceptor removes this repetition.

------------------------------------------------------------------------

# 6. Add a Response Interceptor

### What are we doing?

The request interceptor works **before** the request reaches the server.

Now I want to handle the response **after** the server responds.

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

### What did we just do?

`interceptors.response` means:

> "I want to intercept the response coming back from the server."

The `use()` method receives two callbacks.

### First callback

``` js
(response) => {
  return response;
}
```

This runs when the request succeeds.

For example:

``` text
200 OK
201 Created
204 No Content
```

The response object is passed into the callback.

I return it so the normal Axios promise chain can continue.

### Second callback

``` js
(error) => {
  return Promise.reject(error);
}
```

This runs when Axios considers the request unsuccessful.

For example:

``` text
401 Unauthorized
403 Forbidden
404 Not Found
500 Internal Server Error
```

I reject the error so the calling code can still handle it.

### What would I say?

> "The response interceptor gives me one central place to handle
> successful responses and common API errors."

------------------------------------------------------------------------

# 7. Check for a 401 Unauthorized

### What are we doing?

A `401` commonly means the access token is missing, invalid, or expired.

I can detect that in the response interceptor.

``` js
axiosInstance.interceptors.response.use(
  (response) => {
    return response;
  },
  (error) => {
    if (error.response?.status === 401) {
      console.log("Access token expired or invalid");
    }

    return Promise.reject(error);
  }
);
```

### What did we just do?

``` js
error.response
```

contains information about the server's error response when Axios
received one.

``` js
error.response.status
```

gives us the HTTP status code.

So:

``` js
error.response?.status === 401
```

means:

> "If the response exists and its status is 401..."

The `?.` is optional chaining. It prevents an error if `error.response`
is undefined.

### Runtime

``` text
API request
   ↓
Server
   ↓
401 Unauthorized
   ↓
Response interceptor
   ↓
error.response.status === 401
   ↓
Handle authentication problem
```

------------------------------------------------------------------------

# 8. Why Do We Need a Refresh Token?

### What are we trying to solve?

Access tokens are usually short-lived.

For example:

``` text
Access token
   ↓
Valid
   ↓
Expires
```

If the access token expires, the API may return:

``` text
401 Unauthorized
```

Instead of forcing the user to log in again immediately, the application
can use a refresh token to request a new access token.

The flow becomes:

``` text
Request
   ↓
Access token expired
   ↓
401
   ↓
Refresh token
   ↓
Get new access token
   ↓
Retry original request
   ↓
Success
```

------------------------------------------------------------------------

# 9. Create a Separate Refresh Client

### What are we doing?

For the refresh request, I create a separate Axios client.

``` js
import axios from "axios";

const refreshClient = axios.create({
  baseURL: "https://api.example.com",
});

export default refreshClient;
```

### What did we just do?

I created another Axios instance specifically for the refresh request.

The reason is important.

My main `axiosInstance` has the response interceptor that handles `401`.

If I use the same instance for the refresh request and the refresh
request itself returns `401`, the interceptor could try to refresh
again.

That can create recursive behavior.

So I keep:

``` text
axiosInstance
    ↓
normal API requests
    ↓
interceptors
```

and:

``` text
refreshClient
    ↓
refresh endpoint
    ↓
no authentication-refresh interceptor
```

### What would I say?

> "I use a separate Axios client for the refresh endpoint so the refresh
> request does not go through the same 401-refresh interceptor and
> accidentally trigger another refresh cycle."

------------------------------------------------------------------------

# 10. Store the New Access Token

Assume the backend returns:

``` js
{
  accessToken: "new-access-token"
}
```

The refresh function can be:

``` js
const refreshAccessToken = async () => {
  const refreshToken = sessionStorage.getItem("refreshToken");

  const response = await refreshClient.post("/auth/refresh", {
    refreshToken,
  });

  const newAccessToken = response.data.accessToken;

  sessionStorage.setItem("token", newAccessToken);

  return newAccessToken;
};
```

### What did we just do?

### 1. Get refresh token

``` js
const refreshToken = sessionStorage.getItem("refreshToken");
```

I retrieve the refresh token from storage.

### 2. Call refresh endpoint

``` js
refreshClient.post("/auth/refresh", {
  refreshToken,
});
```

I send the refresh token to the backend.

### 3. Read the new access token

``` js
const newAccessToken = response.data.accessToken;
```

The server returns a new access token.

### 4. Update storage

``` js
sessionStorage.setItem("token", newAccessToken);
```

Now future requests can use the new token.

### 5. Return the token

``` js
return newAccessToken;
```

I return it because the interceptor also needs the new token immediately
for the request that originally failed.

### Important point

Updating storage does **not** automatically update the request that
already failed.

That request has its own `config` object.

We need to update that original request manually.

------------------------------------------------------------------------

# 11. Understand `error.config`

### What are we doing?

Inside the response interceptor, the error contains information about
the request that failed.

``` js
(error) => {
  const originalRequest = error.config;
}
```

### What did we just do?

``` js
error.config
```

is the Axios configuration object of the request that failed.

For example, suppose we originally called:

``` js
axiosInstance.get("/profile");
```

and the access token was expired.

Axios creates a request configuration internally.

Conceptually:

``` js
{
  url: "/profile",
  method: "get",
  headers: {
    Authorization: "Bearer old-token"
  }
}
```

If that request receives `401`, Axios exposes its configuration through:

``` js
error.config
```

So:

``` js
const originalRequest = error.config;
```

means:

> "Give me the configuration of the request that just failed."

### Why do we need this?

Because we don't want to make a completely new `/profile` request
manually.

We want to take the **same failed request**, replace its expired token,
and retry it.

------------------------------------------------------------------------

# 12. Retry the Original Request

### What are we doing?

Now we combine token refresh and `error.config`.

``` js
axiosInstance.interceptors.response.use(
  (response) => response,

  async (error) => {
    if (error.response?.status === 401) {
      const originalRequest = error.config;

      const newAccessToken = await refreshAccessToken();

      originalRequest.headers.Authorization =
        `Bearer ${newAccessToken}`;

      return axiosInstance(originalRequest);
    }

    return Promise.reject(error);
  }
);
```

### What did we just do?

This is the core refresh-token interceptor.

### Step 1 --- Detect 401

``` js
if (error.response?.status === 401)
```

We only start the refresh flow when the server returns 401.

### Step 2 --- Get the failed request

``` js
const originalRequest = error.config;
```

We save the request that failed.

### Step 3 --- Refresh the access token

``` js
const newAccessToken = await refreshAccessToken();
```

We wait for the backend to give us a new access token.

### Step 4 --- Update the original request

``` js
originalRequest.headers.Authorization =
  `Bearer ${newAccessToken}`;
```

This is extremely important.

Changing:

``` js
sessionStorage
```

only changes future requests.

It does not magically change the already-created `originalRequest`.

Therefore, I explicitly replace the old Authorization header inside the
original request.

### Step 5 --- Retry

``` js
return axiosInstance(originalRequest);
```

Axios instances can be called with a request configuration object.

Here I am saying:

> "Take this original request configuration, now containing the new
> token, and send it again."

### Runtime

``` text
GET /profile
   ↓
old access token
   ↓
401
   ↓
error.config
   ↓
originalRequest
   ↓
refreshAccessToken()
   ↓
new access token
   ↓
update originalRequest.headers
   ↓
axiosInstance(originalRequest)
   ↓
GET /profile again
   ↓
200 OK
```

### What would I say to the interviewer?

> "When a request gets a 401, I take the failed request from
> `error.config`, refresh the access token, update the Authorization
> header on that original request, and retry the same request using the
> Axios instance."

------------------------------------------------------------------------

# 13. Prevent an Infinite Refresh Loop

### What problem are we solving?

Imagine the refresh succeeds, but the retried request still returns
`401`.

The response interceptor sees another 401.

It might try to refresh again.

That could continue forever.

So I add a custom flag.

``` js
if (error.response?.status === 401 && !error.config._retry) {
  error.config._retry = true;

  const newAccessToken = await refreshAccessToken();

  error.config.headers.Authorization =
    `Bearer ${newAccessToken}`;

  return axiosInstance(error.config);
}

return Promise.reject(error);
```

### What did we just do?

``` js
!error.config._retry
```

checks whether this request has already gone through our retry logic.

Then:

``` js
error.config._retry = true;
```

marks the request.

`_retry` is **not an Axios keyword**.

It is a custom property that we add to the request configuration for our
own logic.

### Why is this necessary?

Without it:

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
401
 ↓
...
```

With `_retry`:

``` text
401
 ↓
_retry is false
 ↓
refresh
 ↓
retry
 ↓
401
 ↓
_retry is true
 ↓
do not refresh again
 ↓
reject error
```

------------------------------------------------------------------------

# 14. Complete Response Interceptor

Now we can combine the pieces.

``` js
axiosInstance.interceptors.response.use(
  (response) => {
    return response;
  },

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status === 401 &&
      !originalRequest?._retry
    ) {
      originalRequest._retry = true;

      try {
        const newAccessToken = await refreshAccessToken();

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
```

### What did we just build?

We now have the complete basic flow:

``` text
Normal request
     ↓
Request interceptor
     ↓
Attach access token
     ↓
Server
     ↓
Success?
 ┌───┴────┐
Yes       No
 ↓         ↓
Return    401?
response   ↓
         refresh
           ↓
       new token
           ↓
     update original
           ↓
        retry
```

### What happens if refresh fails?

``` js
sessionStorage.removeItem("token");
sessionStorage.removeItem("refreshToken");
```

I clear the authentication data.

Then:

``` js
return Promise.reject(refreshError);
```

I allow the application to handle the failed authentication, usually by
logging the user out or redirecting to the login page.

------------------------------------------------------------------------

# 15. Logout

### What are we doing?

When the user explicitly logs out, I remove the authentication
information.

``` js
const logout = () => {
  sessionStorage.removeItem("token");
  sessionStorage.removeItem("refreshToken");
};
```

### What did we just do?

The access token and refresh token are removed.

Future authenticated requests will no longer have the token available to
the request interceptor.

### In a real application

Logout may also involve:

``` text
Clear client-side auth state
        ↓
Remove tokens/cookies
        ↓
Optionally notify backend
        ↓
Navigate to login
```

------------------------------------------------------------------------

# 16. Connect This With React Context

### What are we doing?

Axios handles API communication.

Context handles application-wide authentication state.

For example:

``` js
import { createContext, useState } from "react";

export const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [isAuthenticated, setIsAuthenticated] = useState(
    !!sessionStorage.getItem("token")
  );

  const login = (token, refreshToken) => {
    sessionStorage.setItem("token", token);
    sessionStorage.setItem("refreshToken", refreshToken);

    setIsAuthenticated(true);
  };

  const logout = () => {
    sessionStorage.removeItem("token");
    sessionStorage.removeItem("refreshToken");

    setIsAuthenticated(false);
  };

  return (
    <AuthContext.Provider
      value={{ isAuthenticated, login, logout }}
    >
      {children}
    </AuthContext.Provider>
  );
};
```

### What did we just do?

The Context stores authentication state that can be accessed by multiple
components.

The important separation is:

``` text
Context
→ application authentication state

Axios interceptor
→ HTTP authentication behavior
```

The interceptor does not replace Context.

They solve different problems.

------------------------------------------------------------------------

# 17. Request Interceptor + Response Interceptor Together

The final architecture looks like this:

``` text
                 React Component
                       |
                       v
               axiosInstance.get()
                       |
                       v
              REQUEST INTERCEPTOR
                       |
                 Get access token
                       |
                 Add Bearer token
                       |
                       v
                    SERVER
                       |
              +--------+--------+
              |                 |
            Success            401
              |                 |
              v                 v
         RESPONSE          refresh token
         INTERCEPTOR             |
              |             new access token
              |                 |
              |           update original
              |                 |
              |              retry
              |                 |
              +--------<--------+
                       |
                       v
                 Component gets
                    response
```

------------------------------------------------------------------------

# 18. Recommended Folder Structure

A simple project structure can be:

``` text
src/
│
├── api/
│   ├── axiosInstance.js
│   ├── refreshClient.js
│   └── authApi.js
│
├── context/
│   └── AuthContext.jsx
│
├── components/
│   ├── Login.jsx
│   └── Dashboard.jsx
│
├── routes/
│   └── ProtectedRoute.jsx
│
└── App.jsx
```

### What are we doing?

I separate responsibilities.

``` text
api/
→ API and Axios configuration

context/
→ authentication state

routes/
→ route protection

components/
→ UI
```

This keeps the application easier to maintain.

------------------------------------------------------------------------

# 19. Protected Route Is Different From an Interceptor

A common interview question is:

> "Is an Axios interceptor the same as a protected route?"

No.

A protected route controls **whether the user can access a frontend
route**.

An Axios interceptor controls **how HTTP requests and responses are
processed**.

Example:

``` jsx
const ProtectedRoute = ({ children }) => {
  const token = sessionStorage.getItem("token");

  if (!token) {
    return <Navigate to="/login" />;
  }

  return children;
};
```

### What did we just do?

We check whether an authentication token exists before rendering the
protected page.

The interceptor is still needed because the token can expire while the
user is already inside the application.

So:

``` text
Protected Route
→ Can the user enter this page?

Interceptor
→ What should happen when an API request is made?
```

------------------------------------------------------------------------

# 20. Debugging the Interceptor

### What are we doing?

When learning interceptors, I can log the request configuration.

``` js
axiosInstance.interceptors.request.use((config) => {
  console.log("Request URL:", config.url);
  console.log("Request Method:", config.method);
  console.log("Request Headers:", config.headers);

  return config;
});
```

### What did we just do?

I am inspecting the exact request configuration before Axios sends it.

This helps me verify whether the Authorization header was added.

For example:

``` text
Request URL: /users
Request Method: get
Request Headers:
Authorization: Bearer eyJ...
```

### Browser Network tab

I can also open:

``` text
Developer Tools
→ Network
→ API request
→ Headers
```

and check:

``` text
Authorization: Bearer <token>
```

This is one of the best ways to debug authentication requests.

------------------------------------------------------------------------

# 21. Common Interview Mistake --- Forgetting `return config`

Incorrect:

``` js
axiosInstance.interceptors.request.use((config) => {
  config.headers.Authorization = `Bearer ${token}`;
});
```

### What is wrong?

I modified the configuration but did not return it.

The interceptor should pass the request configuration forward.

Correct:

``` js
axiosInstance.interceptors.request.use((config) => {
  config.headers.Authorization = `Bearer ${token}`;

  return config;
});
```

### What would I say?

> "The interceptor callback needs to return the request configuration so
> Axios can continue the request chain with the modified configuration."

------------------------------------------------------------------------

# 22. Common Interview Mistake --- Updating Only Storage

Incorrect idea:

``` js
sessionStorage.setItem("token", newAccessToken);

return axiosInstance(error.config);
```

### Why is this incomplete?

The request in:

``` js
error.config
```

was already created with the old Authorization header.

Updating storage does not automatically mutate that existing request.

So I should do:

``` js
sessionStorage.setItem("token", newAccessToken);

error.config.headers.Authorization =
  `Bearer ${newAccessToken}`;

return axiosInstance(error.config);
```

### The key distinction

``` text
sessionStorage
→ token for future requests

error.config
→ request that already exists
```

Both need to be handled.

------------------------------------------------------------------------

# 23. Complete Practical Example

## `axiosInstance.js`

``` js
import axios from "axios";
import refreshClient from "./refreshClient";

const axiosInstance = axios.create({
  baseURL: "https://api.example.com",
});

axiosInstance.interceptors.request.use(
  (config) => {
    const token = sessionStorage.getItem("token");

    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
  }
);

const refreshAccessToken = async () => {
  const refreshToken = sessionStorage.getItem("refreshToken");

  const response = await refreshClient.post("/auth/refresh", {
    refreshToken,
  });

  const newAccessToken = response.data.accessToken;

  sessionStorage.setItem("token", newAccessToken);

  return newAccessToken;
};

axiosInstance.interceptors.response.use(
  (response) => {
    return response;
  },

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status === 401 &&
      !originalRequest?._retry
    ) {
      originalRequest._retry = true;

      try {
        const newAccessToken = await refreshAccessToken();

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

### What did we just build?

This one file now handles the complete Axios authentication flow.

### Request side

``` text
API call
 ↓
request interceptor
 ↓
read access token
 ↓
add Authorization header
 ↓
send request
```

### Response side

``` text
Response
 ↓
success?
 ├── yes → return response
 │
 └── 401
       ↓
    get original request
       ↓
    check _retry
       ↓
    refresh token
       ↓
    save new access token
       ↓
    update original request header
       ↓
    retry original request
```

------------------------------------------------------------------------

# 24. `refreshClient.js`

``` js
import axios from "axios";

const refreshClient = axios.create({
  baseURL: "https://api.example.com",
});

export default refreshClient;
```

### What did we just do?

This is a separate Axios instance used only for the refresh API.

It intentionally does not use the main response interceptor.

That keeps the refresh operation separate from the normal authenticated
request flow.

------------------------------------------------------------------------

# 25. API Function Using the Instance

``` js
import axiosInstance from "./axiosInstance";

export const getProfile = async () => {
  const response = await axiosInstance.get("/profile");

  return response.data;
};
```

### What did we just do?

The API function does not need to know about:

-   access-token retrieval
-   Authorization headers
-   token expiration
-   refresh logic
-   retry logic

All of that is centralized inside the Axios configuration.

So the API function remains simple.

------------------------------------------------------------------------

# 26. What I Would Explain to the Interviewer

If the interviewer asks:

> "Explain your Axios interceptor implementation."

I would say:

> "I first create a custom Axios instance using `axios.create()` so that
> I can centralize my API configuration. Then I add a request
> interceptor that runs before every request. It retrieves the access
> token from storage, adds it as a Bearer token in the Authorization
> header, and returns the modified config.
>
> On the response side, I add a response interceptor with success and
> error handlers. If the API returns a 401, I take the failed request
> from `error.config`. I use the refresh token to request a new access
> token, store the new token, update the Authorization header of the
> original request, and retry that same request using the Axios
> instance.
>
> I also use a custom `_retry` flag so that the same request does not
> continuously trigger the refresh logic if the retry also fails. For
> the refresh API, I use a separate Axios instance so that the refresh
> request does not recursively enter the same 401 interceptor."

------------------------------------------------------------------------

# 27. One-Minute Version

> "In my React application, I use Axios interceptors to centralize
> authentication logic. I create a custom Axios instance using
> `axios.create()` with a common base URL. A request interceptor runs
> before every request, gets the access token from storage, and attaches
> it as a Bearer token to the Authorization header.
>
> I also use a response interceptor to handle API errors centrally. When
> a request returns 401, I get the original request using
> `error.config`, call the refresh endpoint using a separate Axios
> client, store the new access token, update the Authorization header of
> the original request, and retry it.
>
> I use a custom `_retry` flag to prevent an infinite refresh loop. This
> keeps authentication logic centralized instead of repeating it in
> every API call."

------------------------------------------------------------------------

# 28. Key Things to Remember While Coding in an Interview

``` text
axios.create()
      ↓
Create reusable Axios instance

interceptors.request.use()
      ↓
Before request

config
      ↓
Current request configuration

return config
      ↓
Continue request

interceptors.response.use()
      ↓
After response

response
      ↓
Successful response

error
      ↓
Failed response

error.response.status
      ↓
HTTP status

error.config
      ↓
Failed request configuration

_retry
      ↓
Prevent repeated retry

refreshAccessToken()
      ↓
Get new access token

originalRequest.headers.Authorization
      ↓
Put new token on failed request

axiosInstance(originalRequest)
      ↓
Retry failed request
```

------------------------------------------------------------------------

# 29. Final Interview Mental Model

Do not try to memorize the entire implementation.

Remember the sequence:

``` text
1. Create Axios instance
        ↓
2. Request interceptor
        ↓
3. Get token
        ↓
4. Add token
        ↓
5. Send request
        ↓
6. Response interceptor
        ↓
7. If 401
        ↓
8. Get error.config
        ↓
9. Refresh token
        ↓
10. Save new token
        ↓
11. Update original request
        ↓
12. Retry original request
        ↓
13. _retry prevents loop
```

The most important interview concept is:

> **The request interceptor prepares the request before it is sent. The
> response interceptor handles what comes back. When a 401 occurs,
> `error.config` gives me the failed request so I can refresh the token,
> update that request, and retry it.**
