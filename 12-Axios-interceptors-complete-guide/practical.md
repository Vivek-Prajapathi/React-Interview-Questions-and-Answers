# Axios Interceptors --- Practical Implementation, Axios Instances & Token Authentication

> **This chapter is the practical continuation of the theoretical Axios
> Interceptors chapter.**
>
> The goal is not to memorize interceptor syntax. The goal is to be able
> to **build the system yourself**, understand what each object
> represents, follow a request through the application, understand token
> refresh and retry behavior, debug failures, and explain the
> architecture confidently in an interview.

------------------------------------------------------------------------

# How to Use This Chapter

The previous chapter already established the concepts of:

-   interceptors
-   `use()`
-   callbacks
-   request `config`
-   response `response`
-   `error.response`
-   `config.headers`
-   `response.data`
-   `response.status`
-   request/response lifecycle

We will **not restart from those definitions**.

Instead, every section here follows this pattern:

1.  **What are we building?**
2.  **Code**
3.  **What did we just do?**
4.  **Why did we do it?**
5.  **What happens at runtime?**
6.  **What happens if we remove it?**
7.  **How does it connect to the next step?**

The code is therefore the anchor of the chapter.

------------------------------------------------------------------------

# PART 1 --- FROM THEORY TO A REAL PROJECT

## 1. Why Do We Need an Axios Instance?

Before creating an instance, imagine that the application uses Axios
directly everywhere.

### Code

``` js
import axios from "axios";

axios.get("https://api.example.com/users");

axios.post("https://api.example.com/login");

axios.get("https://api.example.com/products");
```

## What did we just do?

We imported the global Axios object and called its HTTP methods
directly.

Each call contains the complete URL.

For example:

``` js
axios.get("https://api.example.com/users");
```

means:

> Make a GET request to `https://api.example.com/users`.

The next call repeats the same server address:

``` js
axios.post("https://api.example.com/login");
```

And again:

``` js
axios.get("https://api.example.com/products");
```

This works.

There is nothing inherently wrong with making a request this way.

The problem appears when the application becomes larger.

------------------------------------------------------------------------

## Problems With the Direct Approach

### Problem 1 --- Repeating the base URL

We keep writing:

``` text
https://api.example.com
```

in many files.

If the backend URL changes, we may have to update many places.

------------------------------------------------------------------------

### Problem 2 --- Repeating configuration

Suppose every API request needs:

``` js
headers: {
  "Content-Type": "application/json"
}
```

We may start repeating configuration.

------------------------------------------------------------------------

### Problem 3 --- Authentication becomes repetitive

Suppose every protected request requires:

``` js
Authorization: `Bearer ${token}`
```

We don't want every component to manually add that header.

------------------------------------------------------------------------

### Problem 4 --- Centralized error handling becomes difficult

Suppose every API call can return:

``` text
401
403
500
```

We don't want every component to contain its own authentication/error
logic.

------------------------------------------------------------------------

### Problem 5 --- Refresh-token logic becomes difficult

Suppose the access token expires.

We need a central process:

``` text
401
 ↓
refresh token
 ↓
new access token
 ↓
retry original request
```

Putting this into every API function would create duplication.

------------------------------------------------------------------------

# 2. Create an Axios Instance

Now solve the first problem.

### Code

``` js
import axios from "axios";

const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});
```

## What did we just do?

We called:

``` js
axios.create(...)
```

and stored the returned value in:

``` js
axiosInstance
```

This is the important transition:

``` text
Before:

axios
 ↓
every request contains its own configuration


After:

axios
 ↓
axiosInstance
 ↓
shared configuration
 ↓
API requests
```

------------------------------------------------------------------------

## What Is `axios.create()` Actually Doing?

`axios.create()` creates a **new Axios client/instance with its own
configuration context**.

A useful mental model is:

``` text
Global Axios
      ↓
   create()
      ↓
Configured Axios instance
```

The returned instance provides the familiar HTTP methods:

``` js
axiosInstance.get(...)
axiosInstance.post(...)
axiosInstance.put(...)
axiosInstance.patch(...)
axiosInstance.delete(...)
```

It also has its own interceptor manager and defaults.

So we can configure this client specifically for our application API.

------------------------------------------------------------------------

## Why Call It an "Instance"?

Because `axiosInstance` is a separate configured Axios client created
from Axios's factory function.

Do not think:

> "It is just another variable containing Axios."

Instead think:

> "It is a configured HTTP client created from Axios."

That distinction becomes important when we have multiple clients.

For example:

``` text
apiClient
    ↓
application APIs

authClient
    ↓
login / refresh

uploadClient
    ↓
file uploads
```

Each can have different configuration.

------------------------------------------------------------------------

# 3. What Does `baseURL` Do?

We wrote:

``` js
const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});
```

Now:

``` js
axiosInstance.get("/users");
```

can resolve to:

``` text
https://api.example.com/users
```

Conceptually:

``` text
baseURL
https://api.example.com
       +
request URL
/users
       ↓
https://api.example.com/users
```

### Important

The exact URL-resolution behavior follows Axios's URL handling rules.
The useful application-level mental model is:

> `baseURL` provides the common server base for relative request URLs.

------------------------------------------------------------------------

# 4. What Else Can Belong to the Instance?

An instance can hold shared configuration such as:

``` js
const axiosInstance = axios.create({
  baseURL: "https://api.example.com",
  timeout: 10000,
  headers: {
    "Content-Type": "application/json"
  }
});
```

## What did we just do?

We created defaults for requests made through this instance.

The instance now has a common starting configuration.

Individual requests can still provide their own configuration when
necessary.

This gives us:

``` text
Instance defaults
      +
Request-specific configuration
      ↓
Actual request configuration
```

The exact merging behavior is handled by Axios.

------------------------------------------------------------------------

# 5. Why Is an Instance Useful in a Large Application?

Imagine:

``` text
100 API calls
20 React components
10 API modules
```

Without an instance, common configuration gets repeated.

With an instance:

``` text
             axiosInstance
             /     |      \
            /      |       \
       users     orders    products
```

All of those requests can share:

-   base URL
-   common defaults
-   request interceptor
-   response interceptor
-   authentication behavior
-   centralized error handling

That is the real reason instances are useful.

------------------------------------------------------------------------

# PART 2 --- `axios` VS `axiosInstance`

## 6. Compare the Two

### Global Axios

``` js
axios.get("/users");
```

### Instance

``` js
axiosInstance.get("/users");
```

Both provide:

``` js
.get()
.post()
.put()
.patch()
.delete()
```

Why?

Because both are Axios clients capable of making HTTP requests.

The important difference is their configuration context.

------------------------------------------------------------------------

## 7. Where Does `.get()` Come From?

When we write:

``` js
axiosInstance.get("/users");
```

we are calling the GET-request method provided by the Axios instance.

Likewise:

``` js
axios.get("/users");
```

uses the GET-request capability of the global Axios client.

So the difference is not:

``` text
axios = has get()
axiosInstance = somehow invented get()
```

Instead:

``` text
Axios client
    ↓
HTTP methods
    ↓
get / post / put / patch / delete
```

An instance is another configured Axios client.

------------------------------------------------------------------------

# 8. Global Interceptor vs Instance Interceptor

### Global Axios interceptor

``` js
axios.interceptors.request.use((config) => {
  console.log("Global Axios request");
  return config;
});
```

### Instance interceptor

``` js
axiosInstance.interceptors.request.use((config) => {
  console.log("Instance request");
  return config;
});
```

## What did we just do?

We registered the first interceptor on the global Axios client.

The second interceptor belongs to `axiosInstance`.

This distinction matters.

If a request is made through:

``` js
axiosInstance.get("/users");
```

the instance's interceptor participates.

If a request is made through:

``` js
axios.get("/users");
```

that request is not automatically the same as a request through
`axiosInstance`.

------------------------------------------------------------------------

## Practical Rule

If your application standardizes on:

``` js
axiosInstance
```

then application API calls should consistently use:

``` js
axiosInstance.get(...)
axiosInstance.post(...)
```

rather than randomly mixing:

``` js
axios.get(...)
axiosInstance.get(...)
```

unless there is a deliberate reason.

------------------------------------------------------------------------

# PART 3 --- REALISTIC PROJECT STRUCTURE

A practical React project can look like this:

``` text
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

## What did we just decide?

We separated responsibilities.

### `api/axiosInstance.js`

Responsible for:

``` text
HTTP client configuration
interceptors
authentication request behavior
refresh handling
```

### `api/authApi.js`

Responsible for operations such as:

``` text
login
register
refresh
```

depending on the backend architecture.

### `api/userApi.js`

Responsible for:

``` text
get users
get user by ID
delete user
update user
```

### `AuthContext.jsx`

Responsible for application authentication state:

``` text
user
isAuthenticated
login state
logout
```

### `ProtectedRoute.jsx`

Responsible for frontend route access.

This is important:

> Protected routes and Axios interceptors solve different problems.

------------------------------------------------------------------------

# PART 4 --- BUILD THE AXIOS INSTANCE FROM ZERO

## 9. Create `axiosInstance.js`

Create:

``` text
src/api/axiosInstance.js
```

### Code

``` js
import axios from "axios";

const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});

export default axiosInstance;
```

## What did we just do?

### Line 1

``` js
import axios from "axios";
```

We imported Axios into this module.

Now this file can use Axios APIs.

------------------------------------------------------------------------

### Line 2--4

``` js
const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});
```

We created a configured Axios instance.

The result of:

``` js
axios.create(...)
```

is assigned to:

``` js
axiosInstance
```

------------------------------------------------------------------------

### Final line

``` js
export default axiosInstance;
```

We make this configured client available to other modules.

Another file can now write:

``` js
import axiosInstance from "./axiosInstance";
```

and receive the exported Axios client.

------------------------------------------------------------------------

## What Does the Other File Receive?

Suppose:

``` js
import axiosInstance from "./api/axiosInstance";
```

The imported value is the object returned by:

``` js
axios.create(...)
```

So the other file can use:

``` js
axiosInstance.get(...)
```

and:

``` js
axiosInstance.post(...)
```

It can also access:

``` js
axiosInstance.interceptors
```

because the imported value is the configured Axios client.

------------------------------------------------------------------------

# PART 5 --- USING THE INSTANCE

## 10. Create an API Function

Create:

``` text
src/api/userApi.js
```

### Code

``` js
import axiosInstance from "./axiosInstance";

export const getUsers = () => {
  return axiosInstance.get("/users");
};
```

## What did we just do?

We created a function called:

``` js
getUsers
```

Its responsibility is very simple:

> Call the users endpoint.

The function does not know about:

-   React rendering
-   token refresh
-   protected routes
-   UI state

It simply describes the API operation.

------------------------------------------------------------------------

## What Happens When `getUsers()` Runs?

When another file calls:

``` js
getUsers();
```

the function executes:

``` js
axiosInstance.get("/users");
```

The instance already knows:

``` text
baseURL = https://api.example.com
```

so the request is conceptually:

``` text
GET https://api.example.com/users
```

------------------------------------------------------------------------

# 11. Call the API From a Component

### Code

``` js
const response = await getUsers();

console.log(response.data);
```

## What did we just do?

We called our API function.

``` js
getUsers()
```

returns the Promise from:

``` js
axiosInstance.get("/users")
```

Because the HTTP operation is asynchronous, we use:

``` js
await
```

to wait for the Promise to settle successfully.

Then:

``` js
response
```

contains Axios's successful response object.

And:

``` js
response.data
```

contains the response body returned by the server.

------------------------------------------------------------------------

# 12. Complete Flow So Far

``` text
Component
    ↓
getUsers()
    ↓
axiosInstance.get("/users")
    ↓
Axios instance
    ↓
baseURL + request URL
    ↓
HTTP request
    ↓
Server
    ↓
response
    ↓
Component
```

The Axios instance participates between the API function and the actual
network request.

Soon we will place an interceptor inside that path.

------------------------------------------------------------------------

# PART 6 --- ADDING A REQUEST INTERCEPTOR

## 13. First Request Interceptor

Add this to `axiosInstance.js`.

### Code

``` js
axiosInstance.interceptors.request.use(
  (config) => {
    console.log("Request interceptor");
    return config;
  }
);
```

## What did we just do?

We attached a request interceptor to our configured Axios client.

Notice the object:

``` js
axiosInstance
```

We are not attaching it to an unrelated Axios client.

We are saying:

> Whenever a request is made through this instance, run this registered
> request interceptor as part of the request processing chain.

------------------------------------------------------------------------

## Break the Syntax Apart

``` js
axiosInstance
```

This is our configured Axios client.

Then:

``` js
.interceptors
```

accesses its interceptor system.

Then:

``` js
.request
```

selects the request interceptor manager.

Then:

``` js
.use(...)
```

registers interceptor callbacks.

Then:

``` js
(config) => { ... }
```

is the callback Axios will invoke during request processing.

------------------------------------------------------------------------

## Why Don't We Call the Callback Ourselves?

We do not write:

``` js
axiosInstance.interceptors.request.use(
  (config)
);
```

because we need Axios to call the callback at the correct point in its
request processing.

We register the function:

``` js
(config) => {
  ...
}
```

and Axios invokes it later.

This connects directly to the callback concept from the theoretical
chapter.

------------------------------------------------------------------------

# 14. Log the Request

Let's make the interceptor useful for debugging.

### Code

``` js
axiosInstance.interceptors.request.use((config) => {
  console.log("Method:", config.method);
  console.log("URL:", config.url);
  console.log("Headers:", config.headers);

  return config;
});
```

## What did we just do?

We are inspecting the actual request configuration Axios is processing.

### `config.method`

Tells us the HTTP method.

For example:

``` text
get
post
put
delete
```

### `config.url`

Contains the request URL information associated with this request.

For:

``` js
axiosInstance.get("/users");
```

we may see:

``` text
/users
```

depending on the configuration being inspected.

### `config.headers`

Contains the headers Axios will use for the request after configuration
processing.

------------------------------------------------------------------------

## Runtime

When this executes:

``` js
axiosInstance.get("/users");
```

the request interceptor gets an opportunity to inspect the request
configuration before the request is sent.

Conceptually:

``` text
axiosInstance.get("/users")
       ↓
Axios prepares request
       ↓
request interceptor
       ↓
config
       ↓
console.log(...)
       ↓
return config
       ↓
request continues
```

------------------------------------------------------------------------

# PART 7 --- ADDING THE ACCESS TOKEN

Now we move from debugging to authentication.

## 15. Read the Token

### Code

``` js
const token = sessionStorage.getItem("token");
```

## What did we just do?

We asked browser session storage for the value stored under:

``` text
token
```

Suppose login previously stored:

``` js
sessionStorage.setItem("token", accessToken);
```

Then later:

``` js
sessionStorage.getItem("token");
```

retrieves that value.

------------------------------------------------------------------------

## Why Do We Read It in the Interceptor?

Because the request interceptor is a centralized place through which our
API requests pass.

Instead of doing this in every API function:

``` js
const token = sessionStorage.getItem("token");
```

we do it once in the request processing layer.

That gives us:

``` text
API request
    ↓
request interceptor
    ↓
read current token
    ↓
attach token
    ↓
send request
```

------------------------------------------------------------------------

# 16. Attach the Authorization Header

### Code

``` js
if (token) {
  config.headers.Authorization = `Bearer ${token}`;
}
```

## What did we just do?

We checked whether a token exists.

If it does, we modify the request configuration.

------------------------------------------------------------------------

## Understand `config`

``` js
config
```

is the request configuration Axios is currently processing.

We previously inspected:

``` js
config.method
config.url
config.headers
```

Now we modify:

``` js
config.headers
```

------------------------------------------------------------------------

## Understand `config.headers`

``` js
config.headers
```

represents the headers associated with the request configuration.

We add:

``` js
config.headers.Authorization
```

------------------------------------------------------------------------

## Understand `Authorization`

``` js
config.headers.Authorization
```

means:

> Set the HTTP Authorization header for this request.

The value becomes:

``` text
Bearer ACCESS_TOKEN
```

because of:

``` js
`Bearer ${token}`
```

------------------------------------------------------------------------

## What Does "Bearer" Mean?

At a practical level:

``` text
Authorization: Bearer <token>
```

communicates to the server:

> This request is presenting a bearer access token for authentication.

The server can validate the token and determine whether the request is
authorized.

------------------------------------------------------------------------

## Resulting HTTP Header

If:

``` js
token = "eyJ..."
```

the outgoing request conceptually contains:

``` text
Authorization: Bearer eyJ...
```

------------------------------------------------------------------------

# 17. Complete Request Interceptor

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

## What did we just build?

We created the first real authentication layer.

The sequence is:

``` text
Request starts
    ↓
Interceptor receives config
    ↓
Read token
    ↓
Token exists?
   ↙       ↘
 yes        no
  ↓          ↓
Add header  Continue
   ↘        ↙
    return config
         ↓
Send request
```

------------------------------------------------------------------------

## Why Is `return config` Important?

We changed the configuration.

But Axios still needs that processed configuration to continue through
the request chain.

So:

``` js
return config;
```

passes the request configuration forward.

Think:

``` text
config in
   ↓
modify config
   ↓
config out
```

------------------------------------------------------------------------

# PART 8 --- WHY INTERCEPTOR IS BETTER THAN MANUAL TOKEN ATTACHMENT

## 18. Without an Interceptor

You could write:

### Code

``` js
const token = sessionStorage.getItem("token");

axios.get("/users", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

And again:

``` js
const token = sessionStorage.getItem("token");

axios.get("/products", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

And again:

``` js
const token = sessionStorage.getItem("token");

axios.get("/orders", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

## What problem did we create?

Authentication logic is now duplicated.

If token handling changes, many files may need changes.

------------------------------------------------------------------------

# 19. With the Interceptor

Now the API calls can remain simple.

### Code

``` js
axiosInstance.get("/users");

axiosInstance.get("/products");

axiosInstance.get("/orders");
```

## What did we just achieve?

The individual API calls don't need to know how the access token is
attached.

The request interceptor handles it.

So:

``` text
API function
   ↓
axiosInstance
   ↓
request interceptor
   ↓
Authorization header
   ↓
server
```

This is a cross-cutting concern.

Authentication headers affect many API requests, so centralizing them
makes the application easier to maintain.

------------------------------------------------------------------------

# PART 9 --- RESPONSE INTERCEPTOR IN THE REAL PROJECT

## 20. Add a Response Interceptor

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

We registered two response callbacks.

### First callback

``` js
(response) => {
  return response;
}
```

handles the successful response path.

### Second callback

``` js
(error) => {
  return Promise.reject(error);
}
```

handles the rejected/error path.

This gives us a centralized location for response processing.

------------------------------------------------------------------------

## Why Return `response`?

Suppose an API function does:

``` js
const response = await getUsers();
```

If our interceptor simply returns the response:

``` js
return response;
```

the response continues to the code that called the API.

Conceptually:

``` text
server response
    ↓
response interceptor
    ↓
return response
    ↓
getUsers()
    ↓
component
```

------------------------------------------------------------------------

## Why Return `Promise.reject(error)`?

Suppose an error reaches the response interceptor.

If we want the caller's:

``` js
try/catch
```

to receive the error, we need the Promise chain to remain rejected.

So:

``` js
return Promise.reject(error);
```

passes the rejection onward.

Then:

``` js
try {
  await getUsers();
} catch (error) {
  console.log(error);
}
```

can handle it.

------------------------------------------------------------------------

# PART 10 --- CENTRALIZED 401 HANDLING

## 21. Detect Unauthorized Responses

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // authentication recovery
    }

    return Promise.reject(error);
  }
);
```

## What did we just do?

We introduced a condition for authentication recovery.

``` js
error.response?.status === 401
```

means:

1.  Look at the Axios error.
2.  Check whether it has a response.
3.  If it does, read the HTTP status.
4.  Check whether that status is `401`.

------------------------------------------------------------------------

## Why Use `?.`?

We should not assume every Axios error contains a server response.

For example, a network failure may happen before a response exists.

So:

``` js
error.response?.status
```

is safer than blindly doing:

``` js
error.response.status
```

in code where `response` might be absent.

------------------------------------------------------------------------

## What Does 401 Mean?

HTTP `401 Unauthorized` indicates that the request lacks valid
authentication credentials for the resource.

In a token-based API, one possible reason is:

``` text
access token expired
```

But don't treat:

``` text
401 = definitely token expired
```

as a universal law.

The backend's API contract determines what its 401 responses mean.

Our application can choose to use 401 as the trigger for token recovery
when that matches the backend design.

------------------------------------------------------------------------

# PART 11 --- ACCESS TOKEN VS REFRESH TOKEN

## 22. Access Token

An access token is the credential used when calling protected APIs.

Conceptually:

``` text
Client
  ↓
Authorization: Bearer ACCESS_TOKEN
  ↓
Protected API
```

Access tokens are commonly short-lived.

Why?

Because if an access token is exposed, limiting its lifetime limits how
long that credential remains valid.

The exact expiration policy belongs to the backend authentication
system.

------------------------------------------------------------------------

# 23. Refresh Token

A refresh token is used to obtain a new access token when the current
access token is no longer valid, assuming the refresh token itself is
still valid and the backend accepts it.

Conceptually:

``` text
Access token expires
        ↓
Refresh token
        ↓
Refresh endpoint
        ↓
New access token
```

The refresh token is not normally attached to every protected API
request.

Instead:

``` text
Access token
→ normal protected APIs

Refresh token
→ token-refresh operation
```

------------------------------------------------------------------------

# PART 12 --- COMPLETE AUTHENTICATION FLOW

## 24. Login

Conceptually:

``` text
User enters credentials
        ↓
POST /login
        ↓
Server validates credentials
        ↓
Server returns authentication information
        ↓
Access token
Refresh token
```

The client then stores the credentials according to the application's
security architecture.

For a learning implementation, we may use:

``` js
sessionStorage.setItem("token", accessToken);
sessionStorage.setItem("refreshToken", refreshToken);
```

Production applications need a deliberate storage/security design,
discussed later.

------------------------------------------------------------------------

## 25. Normal Protected Request

``` text
Component
   ↓
API function
   ↓
axiosInstance.get(...)
   ↓
Request interceptor
   ↓
Read access token
   ↓
Authorization: Bearer <access-token>
   ↓
Server
   ↓
Success
```

------------------------------------------------------------------------

## 26. Expired Access Token

``` text
API request
   ↓
Request interceptor
   ↓
Old access token
   ↓
Server
   ↓
401
   ↓
Response interceptor
   ↓
Refresh token
   ↓
Refresh endpoint
   ↓
New access token
   ↓
Update token storage
   ↓
Update original request
   ↓
Retry original request
   ↓
Server
   ↓
Success
```

This is the central implementation we are about to build.

------------------------------------------------------------------------

# PART 13 --- REFRESH TOKEN IMPLEMENTATION

## 27. Start With the Naive Implementation

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => response,

  async (error) => {
    if (error.response?.status === 401) {

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

      return axiosInstance(error.config);
    }

    return Promise.reject(error);
  }
);
```

## What did we just build?

We created the basic idea:

``` text
401
 ↓
get refresh token
 ↓
call refresh endpoint
 ↓
receive new access token
 ↓
store new access token
 ↓
retry failed request
```

But this implementation is incomplete.

The most important missing detail is:

> We have not updated the Authorization header of the failed request.

And we have not protected against an infinite refresh loop.

Let's understand both carefully.

------------------------------------------------------------------------

# PART 14 --- UNDERSTANDING `error.config`

## 28. What Is `error.config`?

Suppose the application makes:

``` js
axiosInstance.get("/users");
```

Axios internally works with a request configuration representing that
request.

Conceptually:

``` js
{
  method: "get",
  url: "/users",
  headers: {
    Authorization: "Bearer OLD_TOKEN"
  }
}
```

If that request fails and Axios gives us:

``` js
error
```

the error can contain:

``` js
error.config
```

which refers to the configuration associated with the failed request.

So:

``` js
const originalRequest = error.config;
```

is a useful naming step.

------------------------------------------------------------------------

## 29. Store `error.config` in `originalRequest`

### Code

``` js
const originalRequest = error.config;
```

## What did we just do?

We created a local variable:

``` js
originalRequest
```

that points to the failed request's configuration.

We use the name `originalRequest` because that is exactly what we are
trying to recover:

> the request that originally failed.

Conceptually:

``` text
error
 ↓
config
 ↓
failed request configuration
 ↓
originalRequest
```

------------------------------------------------------------------------

# PART 15 --- HOW DOES `axiosInstance(error.config)` WORK?

## 30. Axios Can Receive a Configuration Object

We are familiar with:

``` js
axiosInstance.get("/users");
```

But Axios can also receive a request configuration directly.

### Code

``` js
axiosInstance({
  method: "get",
  url: "/users",
  headers: {
    Authorization: "Bearer NEW_TOKEN"
  }
});
```

## What did we just do?

Instead of using the convenience method:

``` js
.get(...)
```

we gave Axios the request configuration directly.

Conceptually:

``` text
Axios client
     +
request config
     ↓
HTTP request
```

------------------------------------------------------------------------

## 31. Therefore `axiosInstance(error.config)` Makes Sense

If:

``` js
error.config
```

contains the configuration of the failed request, then:

``` js
axiosInstance(error.config);
```

asks our Axios client to make a request using that configuration.

So:

``` text
Original request
     ↓
request config
     ↓
error.config
     ↓
refresh token
     ↓
modify config
     ↓
axiosInstance(error.config)
     ↓
retry
```

This is why `error.config` is so important.

------------------------------------------------------------------------

# PART 16 --- IMPORTANT PROBLEM: UPDATING STORAGE IS NOT ENOUGH

## 32. Store the New Access Token

### Code

``` js
const newAccessToken =
  response.data.accessToken;

sessionStorage.setItem(
  "token",
  newAccessToken
);
```

## What did we just do?

We obtained a new access token and replaced the stored access token.

Before:

``` text
sessionStorage
token → OLD_TOKEN
```

After:

``` text
sessionStorage
token → NEW_TOKEN
```

This is important for **future requests**.

But what about the request that already failed?

------------------------------------------------------------------------

# 33. The Existing Request Still Has Its Own Configuration

Suppose:

``` js
originalRequest.headers.Authorization
```

was:

``` text
Bearer OLD_TOKEN
```

Then:

``` js
sessionStorage.setItem("token", "NEW_TOKEN");
```

does not automatically mutate that existing request configuration.

Think of these as two different places:

``` text
Storage
   ↓
token = NEW_TOKEN


Existing request
   ↓
Authorization = OLD_TOKEN
```

Changing the first does not automatically rewrite the second.

This is one of the most important practical details in refresh-token
implementation.

------------------------------------------------------------------------

# 34. Update the Original Request

### Code

``` js
originalRequest.headers.Authorization =
  `Bearer ${newAccessToken}`;
```

## What did we just do?

We explicitly changed the Authorization header of the request that
failed.

Before:

``` text
Authorization: Bearer OLD_TOKEN
```

After:

``` text
Authorization: Bearer NEW_TOKEN
```

Now the original request is ready to be retried.

------------------------------------------------------------------------

# PART 17 --- RETRY THE ORIGINAL REQUEST

## 35. Retry

### Code

``` js
return axiosInstance(originalRequest);
```

## What did we just do?

We asked the Axios instance to execute the request configuration again.

The configuration contains information such as:

``` text
method
url
headers
data
params
other request settings
```

So we don't need to reconstruct the request manually.

------------------------------------------------------------------------

## 36. Why Not Write `axiosInstance.get("/users")`?

Because the failed request might not have been:

``` text
GET /users
```

It could have been:

``` text
POST /orders
PUT /profile
DELETE /user/10
GET /products?page=2
```

The interceptor should not need to know which endpoint failed.

`error.config` gives us the exact failed request configuration.

Therefore:

``` js
axiosInstance(originalRequest)
```

is generic.

------------------------------------------------------------------------

# PART 18 --- COMPLETE BASIC REFRESH FLOW

## 37. Build It Carefully

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => response,

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status === 401 &&
      !originalRequest._retry
    ) {
      originalRequest._retry = true;

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
    }

    return Promise.reject(error);
  }
);
```

## What did we just build?

Let's walk through it in the exact runtime order.

### Step 1

``` js
const originalRequest = error.config;
```

We capture the failed request.

### Step 2

``` js
error.response?.status === 401
```

We check whether the server returned the authentication-related status
we are handling.

### Step 3

``` js
!originalRequest._retry
```

We check whether our custom retry marker is not already set.

### Step 4

``` js
originalRequest._retry = true;
```

We mark this request as having entered the refresh/retry path.

### Step 5

``` js
sessionStorage.getItem("refreshToken");
```

We retrieve the refresh credential.

### Step 6

``` js
axios.post("/refresh", ...)
```

We ask the backend for a new access token.

### Step 7

``` js
response.data.accessToken
```

We extract the new access token from the refresh response.

### Step 8

``` js
sessionStorage.setItem(...)
```

We update the stored token.

### Step 9

``` js
originalRequest.headers.Authorization = ...
```

We update the failed request's header.

### Step 10

``` js
return axiosInstance(originalRequest);
```

We retry the original request.

------------------------------------------------------------------------

# PART 19 --- WHY `_retry` IS NECESSARY

## 38. The Infinite Loop

Imagine:

``` text
GET /users
   ↓
401
   ↓
refresh
   ↓
retry /users
   ↓
401 again
```

If the response interceptor blindly says:

``` text
401 → refresh
```

then:

``` text
401
 ↓
refresh
 ↓
401
 ↓
refresh
 ↓
401
 ↓
refresh
 ↓
...
```

The application can enter an endless loop.

------------------------------------------------------------------------

# 39. Add `_retry`

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

We created our own marker:

``` js
_retry
```

Important:

> `_retry` is not a special Axios keyword.

It is a custom property we attach to the request configuration for our
application's control flow.

------------------------------------------------------------------------

## First 401

``` text
originalRequest._retry
        ↓
undefined
        ↓
!undefined = true
        ↓
enter refresh logic
        ↓
_retry = true
```

------------------------------------------------------------------------

## Second 401

If the same retry comes back with another 401:

``` text
originalRequest._retry
        ↓
true
        ↓
!true = false
        ↓
do not enter refresh logic
```

The interceptor can then reject the error or trigger the application's
logout behavior.

------------------------------------------------------------------------

# PART 20 --- SEPARATE REFRESH CLIENT

## 40. Why Can the Refresh Request Be Dangerous?

Suppose we do:

``` js
axiosInstance.post("/refresh");
```

and `axiosInstance` has our 401 response interceptor.

If `/refresh` itself returns 401, the same interceptor can see:

``` text
401
 ↓
refresh
 ↓
refresh endpoint returns 401
 ↓
interceptor sees 401
 ↓
refresh again
```

The exact behavior depends on the retry guard and backend contract, but
conceptually the refresh operation should be isolated from the ordinary
protected-request recovery path.

------------------------------------------------------------------------

# 41. Create a Separate Refresh Client

### Code

``` js
const refreshClient = axios.create({
  baseURL: "https://api.example.com"
});
```

## What did we just do?

We created another Axios client specifically for operations such as
token refresh.

Our architecture becomes:

``` text
axiosInstance
    ↓
normal protected API requests
    ↓
request/response interceptors


refreshClient
    ↓
refresh endpoint
    ↓
no ordinary protected-request refresh interceptor
```

This makes the refresh operation easier to reason about.

------------------------------------------------------------------------

## Alternative: Use the Global Axios Client

You may also see:

``` js
axios.post(
  "https://api.example.com/refresh",
  { refreshToken }
);
```

The idea is the same: use a client that does not accidentally enter the
same refresh-recovery interceptor chain.

Which approach you choose depends on the application's architecture.

A separate client is often clearer when the application has multiple
authentication-related endpoints.

------------------------------------------------------------------------

# PART 21 --- COMPLETE CLEAN BASIC IMPLEMENTATION

## 42. Build the Axios File

### Code

``` js
import axios from "axios";

const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});

const refreshClient = axios.create({
  baseURL: "https://api.example.com"
});

axiosInstance.interceptors.request.use((config) => {
  const token = sessionStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});

axiosInstance.interceptors.response.use(
  (response) => response,

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status === 401 &&
      originalRequest &&
      !originalRequest._retry
    ) {
      originalRequest._retry = true;

      try {
        const refreshToken =
          sessionStorage.getItem("refreshToken");

        const response = await refreshClient.post(
          "/refresh",
          { refreshToken }
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

## What did we just build?

This file now has three major responsibilities.

### Responsibility 1 --- Create the application HTTP client

``` js
const axiosInstance = axios.create(...)
```

### Responsibility 2 --- Add the current access token

``` js
axiosInstance.interceptors.request.use(...)
```

### Responsibility 3 --- Recover from a 401

``` js
axiosInstance.interceptors.response.use(...)
```

The refresh client is kept separate:

``` js
const refreshClient = axios.create(...)
```

so the refresh operation doesn't accidentally depend on the protected
API's refresh interceptor.

------------------------------------------------------------------------

# PART 22 --- REAL-WORLD CONCURRENCY: MULTIPLE 401s

## 43. The Next Problem

Imagine the dashboard loads several APIs at the same time:

``` text
GET /profile
GET /users
GET /notifications
GET /orders
```

Suppose the access token has expired.

All four can return:

``` text
401
```

A naive interceptor may do:

``` text
/profile → 401 → refresh
/users → 401 → refresh
/notifications → 401 → refresh
/orders → 401 → refresh
```

Now four refresh requests may be running.

That is usually undesirable.

------------------------------------------------------------------------

# 44. What We Want Instead

``` text
/profile       → 401 ┐
/users         → 401 ├──→ ONE refresh
/notifications → 401 │
/orders        → 401 ┘

                    ↓

               NEW TOKEN

                    ↓

       retry all waiting requests
```

This is where coordination becomes necessary.

------------------------------------------------------------------------

# PART 23 --- `isRefreshing`

## 45. Create Refresh State

### Code

``` js
let isRefreshing = false;
```

## What did we just do?

We created application-level state outside the interceptor.

It represents:

``` text
false
→ no refresh currently running

true
→ refresh currently running
```

This is not built into Axios.

It is our own coordination mechanism.

------------------------------------------------------------------------

# 46. Create a Queue

### Code

``` js
let failedQueue = [];
```

## What did we just do?

We created an array to hold requests that encounter 401 while another
request is already refreshing the token.

Conceptually:

``` text
Refresh in progress
        ↓
Request B → wait
Request C → wait
Request D → wait
```

The queue represents those waiting operations.

------------------------------------------------------------------------

# 47. How the Queue Works Conceptually

Suppose:

``` text
Request A → 401
```

A starts refresh:

``` text
isRefreshing = true
```

Then:

``` text
Request B → 401
```

Instead of starting another refresh:

``` text
isRefreshing === true
```

so B waits.

Then:

``` text
Request C → 401
```

C also waits.

When A's refresh succeeds:

``` text
NEW_TOKEN
```

the waiting requests can continue.

------------------------------------------------------------------------

# PART 24 --- QUEUE HELPER

## 48. Process Waiting Requests

### Code

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

We created a function that tells all queued operations what happened.

If refresh failed:

``` js
processQueue(error);
```

then every waiting Promise is rejected.

If refresh succeeded:

``` js
processQueue(null, newAccessToken);
```

then every waiting Promise is resolved with the new token.

------------------------------------------------------------------------

## Why Is `failedQueue = []` Important?

Once the waiting requests have been processed, they are no longer
waiting.

So we clear the array.

Otherwise old entries could remain and be processed again.

------------------------------------------------------------------------

# PART 25 --- COMPLETE CONCURRENT REFRESH IMPLEMENTATION

## 49. Build the Advanced Response Interceptor

### Code

``` js
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

axiosInstance.interceptors.response.use(
  (response) => response,

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status !== 401 ||
      !originalRequest ||
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

      const response = await refreshClient.post(
        "/refresh",
        { refreshToken }
      );

      const newAccessToken =
        response.data.accessToken;

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

      return Promise.reject(refreshError);
    } finally {
      isRefreshing = false;
    }
  }
);
```

## What did we just build?

This is the coordinated refresh pattern.

Let's understand the two paths.

------------------------------------------------------------------------

## Path A --- First Request Gets 401

Suppose:

``` text
Request A → 401
```

The interceptor checks:

``` js
isRefreshing
```

It is:

``` js
false
```

Therefore A becomes the request responsible for refreshing.

We then do:

``` js
originalRequest._retry = true;
isRefreshing = true;
```

Now:

``` text
refresh is running
```

------------------------------------------------------------------------

## Path B --- Another Request Gets 401

Suppose while A is refreshing:

``` text
Request B → 401
```

Now:

``` js
isRefreshing === true
```

So B does not start another refresh.

Instead:

``` js
failedQueue.push({
  resolve,
  reject
});
```

B waits.

The same thing can happen for C, D, and so on.

------------------------------------------------------------------------

## Refresh Succeeds

A receives:

``` js
newAccessToken
```

Then:

``` js
sessionStorage.setItem("token", newAccessToken);
```

updates the stored access token.

Then:

``` js
processQueue(null, newAccessToken);
```

releases the waiting requests.

Each queued Promise receives the new token.

Then each waiting request updates its Authorization header and retries.

------------------------------------------------------------------------

## Refresh Fails

If the refresh request throws:

``` js
catch (refreshError)
```

we call:

``` js
processQueue(refreshError, null);
```

so the waiting requests are rejected.

We also clear authentication state in this learning implementation.

------------------------------------------------------------------------

## Why `finally`?

``` js
finally {
  isRefreshing = false;
}
```

Regardless of success or failure, the refresh operation is finished.

So the state must return to:

``` text
false
```

------------------------------------------------------------------------

# PART 26 --- REFRESH QUEUE: PROMISES EXPLAINED

The queue implementation can look confusing.

The important line is:

``` js
return new Promise((resolve, reject) => {
  failedQueue.push({
    resolve,
    reject
  });
});
```

## What Are We Doing?

We are creating a Promise that will remain pending until the refresh
operation finishes.

The Promise gives us two functions:

``` js
resolve
reject
```

We put those functions into the queue.

Conceptually:

``` text
Request B
   ↓
create Promise
   ↓
store resolve/reject
   ↓
wait
```

Later:

``` text
refresh succeeds
   ↓
processQueue(...)
   ↓
resolve(token)
   ↓
Promise continues
```

Or:

``` text
refresh fails
   ↓
reject(error)
   ↓
Promise rejects
```

This is why the waiting request can pause without starting another
refresh.

------------------------------------------------------------------------

# PART 27 --- SECURITY: TOKEN STORAGE

## 50. `sessionStorage`

For a learning/demo application, we may write:

``` js
sessionStorage.setItem("token", accessToken);
sessionStorage.setItem("refreshToken", refreshToken);
```

### What did we just do?

We stored token strings in browser session storage.

The data is associated with the current browser tab/session behavior of
`sessionStorage`.

However:

> This is not a universal recommendation for production authentication.

------------------------------------------------------------------------

# 51. `localStorage`

You may also see:

``` js
localStorage.setItem("token", accessToken);
```

`localStorage` persists beyond a page session until it is cleared.

But browser storage is accessible to JavaScript running in the origin.

Therefore, an XSS vulnerability can create serious token-exposure risk
if sensitive tokens are stored there.

------------------------------------------------------------------------

# 52. HttpOnly Cookies

Another architecture uses cookies marked:

``` text
HttpOnly
Secure
SameSite=...
```

An HttpOnly cookie cannot be read directly by normal JavaScript through:

``` js
document.cookie
```

This can reduce certain token-theft risks from XSS compared with
JavaScript-readable storage, although it does not magically eliminate
all web security problems.

Cookie-based authentication also requires careful CSRF and SameSite/CORS
design.

------------------------------------------------------------------------

# 53. Production Security Principle

Do not memorize:

> "Always use sessionStorage."

or:

> "Always use localStorage."

or:

> "Always use cookies."

The correct choice depends on:

-   backend architecture
-   browser/client type
-   threat model
-   XSS protections
-   CSRF strategy
-   cookie configuration
-   token lifetime
-   application requirements

For learning the Axios interceptor mechanics, browser storage is
convenient.

For production authentication, storage must be designed deliberately.

------------------------------------------------------------------------

# PART 28 --- LOGOUT FLOW

## 54. Basic Logout

### Code

``` js
const logout = () => {
  sessionStorage.removeItem("token");
  sessionStorage.removeItem("refreshToken");
};
```

## What did we just do?

We removed the two stored credentials.

Future request interceptors will no longer find the access token.

Therefore:

``` js
const token = sessionStorage.getItem("token");
```

will return:

``` text
null
```

and the request interceptor won't attach the bearer token.

------------------------------------------------------------------------

## What Should Happen When Refresh Fails?

A common application behavior is:

``` text
refresh fails
   ↓
authentication is no longer recoverable
   ↓
clear authentication state
   ↓
logout
   ↓
redirect to login
```

The exact navigation/state implementation belongs to the React
authentication architecture.

------------------------------------------------------------------------

# PART 29 --- AUTH CONTEXT + AXIOS

## 55. Separate Responsibilities

A clean architecture can look like:

``` text
AuthContext
    ↓
application authentication state

Axios instance
    ↓
HTTP client

Request interceptor
    ↓
attach access token

Response interceptor
    ↓
401 / refresh / retry

ProtectedRoute
    ↓
frontend route access
```

These are related, but they are not the same responsibility.

------------------------------------------------------------------------

# 56. AuthContext Responsibility

AuthContext might expose:

``` text
user
isAuthenticated
login()
logout()
```

It answers questions such as:

> Is the application currently authenticated?

It can also synchronize authentication state with the UI.

------------------------------------------------------------------------

# 57. Axios Interceptor Responsibility

The Axios layer answers:

> How should an HTTP request be authenticated and how should an
> authentication-related HTTP error be recovered?

It handles:

``` text
Authorization header
401
refresh
retry
```

------------------------------------------------------------------------

# PART 30 --- PROTECTED ROUTES VS AXIOS INTERCEPTORS

## 58. Protected Route

A protected route might conceptually do:

``` text
User visits /dashboard
        ↓
Is authenticated?
      ↙   ↘
    yes    no
     ↓      ↓
dashboard  login
```

This is a frontend navigation decision.

------------------------------------------------------------------------

## 59. Axios Interceptor

The interceptor works at the HTTP layer:

``` text
API request
   ↓
attach token
   ↓
server
   ↓
401?
   ↓
refresh/retry
```

Therefore:

``` text
ProtectedRoute
→ controls frontend route access

Axios interceptor
→ controls HTTP request/response behavior
```

Do not mix these responsibilities.

------------------------------------------------------------------------

# PART 31 --- API MODULE DESIGN

## 60. User API Module

### Code

``` js
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

## What did we just do?

We created a module whose job is to describe user-related API
operations.

### `getUsers`

``` js
axiosInstance.get("/users");
```

### `getUserById`

``` js
axiosInstance.get(`/users/${id}`);
```

### `deleteUser`

``` js
axiosInstance.delete(`/users/${id}`);
```

Notice what is missing.

We don't manually write:

``` js
Authorization: ...
```

because the request interceptor handles authentication.

------------------------------------------------------------------------

# 61. Component Usage

### Code

``` js
const response = await getUsers();
```

## What did we just do?

The component says:

> I need users.

It doesn't need to know:

``` text
How is the token attached?
How does refresh work?
How does 401 recovery work?
What is the base URL?
```

Those concerns are handled below it.

The flow becomes:

``` text
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

This separation is the main architectural benefit.

------------------------------------------------------------------------

# PART 32 --- WHAT BELONGS WHERE?

## 62. API Function

An API function should answer:

``` text
WHAT endpoint?
WHAT HTTP method?
WHAT request data?
WHAT path parameters?
```

Example:

``` js
export const getUserById = (id) => {
  return axiosInstance.get(`/users/${id}`);
};
```

------------------------------------------------------------------------

## 63. Axios Interceptor

An interceptor is suitable for cross-cutting HTTP concerns:

``` text
authentication headers
logging
common request behavior
centralized response handling
401 recovery
token refresh
```

This gives:

``` text
API function
→ endpoint-specific behavior

Interceptor
→ cross-cutting behavior
```

------------------------------------------------------------------------

# PART 33 --- MULTIPLE AXIOS INSTANCES

## 64. Why Multiple Instances?

A larger application may use:

``` js
const apiClient = axios.create({
  baseURL: "https://api.example.com"
});

const authClient = axios.create({
  baseURL: "https://api.example.com"
});
```

The conceptual design could be:

``` text
apiClient
 ↓
protected application APIs

authClient
 ↓
login
refresh
registration
```

This gives each client its own interceptor/configuration context.

------------------------------------------------------------------------

# 65. Why Is This Useful?

Suppose the protected API client has:

``` text
401 → refresh
```

You don't necessarily want the refresh endpoint itself to be processed
by that same recovery mechanism.

Using a separate authentication client makes the boundaries clearer.

------------------------------------------------------------------------

# PART 34 --- INTERCEPTOR EJECTION

## 66. Register an Interceptor and Save Its ID

### Code

``` js
const interceptorId =
  axiosInstance.interceptors.request.use((config) => {
    console.log("Request");
    return config;
  });
```

## What did we just do?

When an interceptor is registered, Axios provides an identifier.

We stored it:

``` js
interceptorId
```

We can later use that identifier to eject the interceptor.

------------------------------------------------------------------------

## 67. Eject the Interceptor

### Code

``` js
axiosInstance.interceptors.request.eject(
  interceptorId
);
```

## What did we just do?

We asked Axios to remove the registered interceptor associated with that
ID.

This is useful when interceptor registration is dynamic and tied to a
lifecycle.

------------------------------------------------------------------------

# PART 35 --- DO NOT REGISTER INTERCEPTORS DURING EVERY RENDER

## 68. Problematic React Code

### Code

``` jsx
function App() {
  axiosInstance.interceptors.request.use((config) => {
    console.log("Request");
    return config;
  });

  return <div>App</div>;
}
```

## What did we just do?

Every time React renders `App`, the registration statement runs again.

If the component renders multiple times, we can accumulate multiple
interceptors.

Conceptually:

``` text
Render 1
 ↓
Interceptor A registered

Render 2
 ↓
Interceptor B registered

Render 3
 ↓
Interceptor C registered
```

Now one request may pass through multiple registered callbacks.

That can create duplicated logs, repeated modifications, and confusing
behavior.

------------------------------------------------------------------------

# 69. Better Static Approach

For a static application-wide interceptor, putting the registration in:

``` text
src/api/axiosInstance.js
```

is often simpler.

The module is initialized as part of the application's module loading,
and the interceptor is registered there rather than during every React
render.

------------------------------------------------------------------------

# 70. Dynamic Interceptors and `useEffect`

Sometimes an interceptor genuinely depends on React state or lifecycle.

In those cases, registration can be performed in an effect and the
interceptor can be ejected during cleanup.

### Code

``` jsx
useEffect(() => {
  const id = axiosInstance.interceptors.request.use(
    (config) => {
      return config;
    }
  );

  return () => {
    axiosInstance.interceptors.request.eject(id);
  };
}, []);
```

## What did we just do?

### On effect setup

``` js
axiosInstance.interceptors.request.use(...)
```

registers the interceptor.

### Axios gives us an ID

``` js
const id = ...
```

We save it.

### On cleanup

``` js
axiosInstance.interceptors.request.eject(id);
```

we remove that specific interceptor.

This prevents a lifecycle-based interceptor from accumulating.

------------------------------------------------------------------------

# PART 36 --- EDGE CASES

## 71. 401

A 401 may trigger authentication recovery if that matches the backend's
contract.

``` js
if (error.response?.status === 401) {
  // recovery
}
```

------------------------------------------------------------------------

## 72. 403

A `403 Forbidden` generally means the server understood the request but
refuses authorization.

Do not automatically treat 403 as an access-token refresh trigger unless
the API contract explicitly requires it.

A common distinction is:

``` text
401
→ authentication credentials missing/invalid

403
→ authenticated/request understood, but access is forbidden
```

Exact semantics depend on the backend.

------------------------------------------------------------------------

## 73. Network Error

Sometimes:

``` js
error.response
```

does not exist.

For example:

``` text
browser
  ↓
network failure
  ↓
no HTTP response received
```

Therefore this is safer:

``` js
error.response?.status
```

than assuming:

``` js
error.response.status
```

always exists.

------------------------------------------------------------------------

## 74. Refresh Token Expired

Suppose:

``` text
Access token expired
Refresh token also expired
```

The refresh endpoint may reject the refresh operation.

At that point:

``` text
cannot obtain new access token
        ↓
clear authentication
        ↓
logout
        ↓
login again
```

------------------------------------------------------------------------

## 75. Retry Itself Fails

Suppose:

``` text
401
 ↓
refresh succeeds
 ↓
retry
 ↓
retry returns 500
```

The interceptor should not assume that the retry must succeed.

The retry is simply another HTTP request.

Its result can be:

``` text
200
400
403
404
500
network failure
```

and should flow through the application's normal error handling.

------------------------------------------------------------------------

# PART 37 --- DEBUGGING INTERCEPTORS

## 76. Add Request Logs

### Code

``` js
axiosInstance.interceptors.request.use((config) => {
  console.log("REQUEST INTERCEPTOR");
  console.log("Method:", config.method);
  console.log("URL:", config.url);
  console.log("Headers:", config.headers);

  return config;
});
```

## What did we just do?

We created a debugging checkpoint before the request is sent.

We can inspect:

``` text
method
URL
headers
```

This is useful when authentication appears broken.

------------------------------------------------------------------------

# 77. Add Response Logs

### Code

``` js
axiosInstance.interceptors.response.use(
  (response) => {
    console.log("SUCCESS RESPONSE");
    console.log(response);

    return response;
  },

  (error) => {
    console.log("ERROR RESPONSE");
    console.log(error);

    return Promise.reject(error);
  }
);
```

## What did we just do?

We created a debugging checkpoint after Axios receives the server
result.

Now we can distinguish:

``` text
successful response
```

from:

``` text
error/rejected response
```

------------------------------------------------------------------------

# 78. Inspect Important Values

During debugging, inspect:

``` js
console.log(config.url);
console.log(config.method);
console.log(config.headers);

console.log(response.status);
console.log(response.data);

console.log(error.response?.status);
console.log(error.config);
```

These values tell us where the request is in its lifecycle.

------------------------------------------------------------------------

# 79. Browser Network Tab

Open:

``` text
DevTools
→ Network
```

Then inspect the API request.

Look at:

``` text
Request URL
Request Method
Status Code
Request Headers
Request Payload
Response Headers
Response Body
```

For authentication, especially inspect:

``` text
Authorization
```

------------------------------------------------------------------------

# 80. Trace the Complete Request

When debugging, follow this exact chain:

``` text
Component
   ↓
API function
   ↓
axiosInstance
   ↓
request interceptor
   ↓
Network request
   ↓
Server
   ↓
response/error
   ↓
response interceptor
   ↓
component
```

If refresh happens:

``` text
401
 ↓
response interceptor
 ↓
refresh endpoint
 ↓
new token
 ↓
original request configuration
 ↓
retry
 ↓
server
```

This mental model is much more useful than randomly adding logs.

------------------------------------------------------------------------

# PART 38 --- COMPLETE END-TO-END PROJECT

Below is a compact but realistic project architecture.

``` text
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

------------------------------------------------------------------------

# 81. `axiosInstance.js`

### Code

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

axiosInstance.interceptors.request.use((config) => {
  const token = sessionStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});

axiosInstance.interceptors.response.use(
  (response) => response,

  async (error) => {
    const originalRequest = error.config;

    if (
      error.response?.status !== 401 ||
      !originalRequest ||
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

      return Promise.reject(refreshError);
    } finally {
      isRefreshing = false;
    }
  }
);

export default axiosInstance;
```

## What did we just build?

This file is now the HTTP authentication engine.

### Request side

``` text
Every request
    ↓
Read current access token
    ↓
Attach Authorization
```

### Response side

``` text
Success
    ↓
return response
```

or:

``` text
401
    ↓
check retry
    ↓
if refresh already running → wait
    ↓
otherwise → refresh
    ↓
store new token
    ↓
release queued requests
    ↓
update original request
    ↓
retry
```

This is the central piece of the architecture.

------------------------------------------------------------------------

# 82. `authApi.js`

### Code

``` js
import axiosInstance from "./axiosInstance";

export const login = (credentials) => {
  return axiosInstance.post("/login", credentials);
};

export const register = (userData) => {
  return axiosInstance.post("/register", userData);
};
```

## What did we just do?

We created API functions for authentication-related operations.

The login function describes:

``` text
POST /login
```

The register function describes:

``` text
POST /register
```

The exact endpoints depend on the backend.

The API module doesn't need to implement token refresh itself because
that behavior belongs to the HTTP layer.

------------------------------------------------------------------------

# 83. `userApi.js`

### Code

``` js
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

## What did we just do?

We separated user-specific endpoint definitions from authentication
infrastructure.

This makes the API layer easier to maintain.

------------------------------------------------------------------------

# 84. Login Flow

### Code

``` js
const handleLogin = async () => {
  const response = await login({
    email,
    password
  });

  const {
    accessToken,
    refreshToken
  } = response.data;

  sessionStorage.setItem(
    "token",
    accessToken
  );

  sessionStorage.setItem(
    "refreshToken",
    refreshToken
  );
};
```

## What did we just do?

We:

1.  Sent credentials to the login endpoint.
2.  Received authentication tokens.
3.  Extracted the access token.
4.  Extracted the refresh token.
5.  Stored them for later use.

After login:

``` text
sessionStorage
    ↓
token → access token
refreshToken → refresh credential
```

The next protected API call can now be processed by the request
interceptor.

------------------------------------------------------------------------

# 85. Dashboard API Call

### Code

``` js
const loadUsers = async () => {
  const response = await getUsers();

  console.log(response.data);
};
```

## What did we just do?

The dashboard does not manually attach the access token.

It calls:

``` js
getUsers()
```

which calls:

``` js
axiosInstance.get("/users")
```

The interceptor handles authentication.

This is the separation we wanted.

------------------------------------------------------------------------

# 86. `ProtectedRoute.jsx`

A simplified conceptual version:

### Code

``` jsx
import { Navigate, Outlet } from "react-router-dom";
import { useAuth } from "../context/AuthContext";

function ProtectedRoute() {
  const { isAuthenticated } = useAuth();

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}

export default ProtectedRoute;
```

## What did we just do?

This component controls **frontend route access**.

It does not refresh access tokens.

It does not attach Authorization headers.

It simply asks:

``` text
Is the application authenticated?
```

If no:

``` text
→ login
```

If yes:

``` text
→ protected route
```

This is different from the Axios interceptor.

------------------------------------------------------------------------

# PART 39 --- COMPLETE END-TO-END FLOW

## 87. Login

``` text
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
AUTHENTICATED STATE
  ↓
DASHBOARD
```

------------------------------------------------------------------------

## 88. Normal Protected API

``` text
DASHBOARD
  ↓
getUsers()
  ↓
axiosInstance.get("/users")
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

------------------------------------------------------------------------

## 89. Expired Access Token

``` text
DASHBOARD
  ↓
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
POST /refresh
  ↓
NEW ACCESS TOKEN
  ↓
UPDATE STORAGE
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

------------------------------------------------------------------------

## 90. Refresh Failure

``` text
API REQUEST
   ↓
401
   ↓
REFRESH
   ↓
REFRESH FAILS
   ↓
CLEAR AUTHENTICATION
   ↓
LOGOUT
   ↓
LOGIN PAGE
```

------------------------------------------------------------------------

# PART 40 --- INTERVIEW QUESTIONS

# Axios

## 91. What is Axios?

### 30-second answer

> Axios is a Promise-based HTTP client commonly used in JavaScript
> applications to communicate with APIs. It provides methods such as GET
> and POST and also supports features such as interceptors, request
> configuration, response handling, and centralized HTTP behavior.

### 1-minute answer

> Axios is a Promise-based HTTP client that simplifies communication
> between a frontend application and a backend API. Instead of manually
> constructing requests, we can use methods such as `get()`, `post()`,
> `put()`, and `delete()`. Axios also provides request and response
> interceptors, which are particularly useful for centralized concerns
> such as authentication headers, logging, error handling, and token
> refresh.

### 2--3-minute answer

> Axios is a Promise-based HTTP client used by frontend applications to
> communicate with backend APIs. It provides a convenient API for common
> HTTP operations such as GET, POST, PUT, PATCH, and DELETE. In a React
> application, I can create an Axios instance with shared configuration
> such as a base URL and then use that instance throughout the
> application.
>
> One important advantage is that Axios provides interceptors. A request
> interceptor can modify the request configuration before it is sent,
> which makes it useful for adding an Authorization header. A response
> interceptor can process successful responses or centralized errors.
> For token-based authentication, the response interceptor can detect a
> 401, perform a refresh-token operation, update the access token, and
> retry the original request.
>
> So in a real project, I would typically use Axios not just as a way to
> send HTTP requests, but as a centralized HTTP layer for API
> communication and cross-cutting concerns such as authentication.

------------------------------------------------------------------------

# Axios Instances

## 92. What Is an Axios Instance?

### 30-second answer

> An Axios instance is a configured Axios client created using
> `axios.create()`. It can have its own base URL, defaults,
> interceptors, and other request configuration, allowing an application
> to centralize API behavior.

### 1-minute answer

> I create an Axios instance when I want a dedicated HTTP client for a
> particular API. For example,
> `axios.create({ baseURL: "https://api.example.com" })` returns an
> Axios client that I can use with methods such as `get()` and `post()`.
> I can also attach request and response interceptors to that instance,
> so authentication and error handling are centralized.

### 2--3-minute answer

> An Axios instance is essentially a separately configured Axios client
> created through `axios.create()`. The important point is that it gives
> the application its own configuration context. For example, I can
> configure a base URL, common defaults, and interceptors on the
> instance.
>
> Instead of writing the full URL in every API call, I can write
> `axiosInstance.get("/users")`. The instance supplies the common base
> URL. More importantly, I can attach an authentication request
> interceptor to that instance. Every API call made through that
> instance can then pass through the same authentication logic.
>
> This is useful for separation as well. A large application could have
> one client for protected application APIs and another client for
> authentication or file uploads. Therefore, an Axios instance gives me
> a reusable and centrally configured HTTP client rather than scattering
> HTTP configuration throughout components.

------------------------------------------------------------------------

# Interceptors

## 93. What Is an Axios Interceptor?

### 30-second answer

> An Axios interceptor is a registered callback that allows us to
> process a request before it is sent or process a response after it is
> received. Request interceptors are commonly used for authentication
> headers, while response interceptors are useful for centralized error
> handling and token refresh.

### 1-minute answer

> In a real application, I use request interceptors for cross-cutting
> request behavior. For example, the request interceptor receives the
> request configuration, reads the current access token, adds
> `Authorization: Bearer <token>`, and returns the configuration. The
> response interceptor can process successful responses or inspect
> errors. If a protected API returns 401, the response interceptor can
> perform token refresh and retry the failed request.

### 2--3-minute answer

> An Axios interceptor sits in the Axios request or response processing
> chain. A request interceptor runs while Axios is preparing a request,
> so it can inspect or modify the request configuration before the
> network request is sent. In authentication, I use this to retrieve the
> current access token and add it to the Authorization header.
>
> A response interceptor runs when Axios receives the result of the
> request. It has a success path and an error path. The success path can
> return the response normally. The error path can inspect information
> such as `error.response.status`. If the backend uses 401 to indicate
> an expired access token, the interceptor can access `error.config`,
> which represents the failed request's configuration. It can then use
> the refresh token to obtain a new access token, update the failed
> request's Authorization header, and retry it.
>
> The important architectural benefit is that components do not have to
> repeat authentication and error-recovery logic. The interceptor
> becomes the centralized HTTP layer.

------------------------------------------------------------------------

# 94. Why Use an Axios Instance Instead of Global Axios?

### Answer

> I use an Axios instance when I want application-specific configuration
> and behavior. With an instance, I can centralize the API base URL,
> defaults, interceptors, and authentication behavior. It also lets me
> create different clients for different backend concerns. Global Axios
> is still perfectly valid for simple requests or deliberately
> independent clients, but a dedicated instance is usually easier to
> maintain in a larger application.

------------------------------------------------------------------------

# 95. How Does a Request Interceptor Work?

### Answer

> The request interceptor is registered using
> `axiosInstance.interceptors.request.use(...)`. Axios invokes the
> registered callback when a request made through that instance enters
> the request processing chain. The callback receives the request
> `config`. I can read or modify properties such as the URL and headers.
> For authentication, I retrieve the access token and set
> `config.headers.Authorization`. Finally, I return the config so Axios
> can continue processing the request.

------------------------------------------------------------------------

# 96. Why Do We Return `config`?

### Answer

> Because the interceptor receives the request configuration,
> potentially modifies it, and then needs to pass that processed
> configuration forward. The mental model is
> `config in → modify → config out`. Returning it allows Axios to
> continue with the request.

------------------------------------------------------------------------

# 97. Why Do We Return `Promise.reject(error)`?

### Answer

> When the interceptor receives an error and we are not recovering from
> it, we want the Promise chain to remain rejected so the API caller can
> handle the error with `catch` or `try/catch`. `Promise.reject(error)`
> preserves that rejected state.

------------------------------------------------------------------------

# 98. What Is `error.config`?

### Answer

> `error.config` is the request configuration associated with the failed
> Axios operation. It is particularly useful for token refresh because
> after receiving a 401, I need to know which request failed. I can
> store `error.config` as `originalRequest`, update its Authorization
> header with the new access token, and retry it using
> `axiosInstance(originalRequest)`.

------------------------------------------------------------------------

# 99. How Do You Retry a Failed Request?

### Answer

> First, I capture `error.config` as the original request. Then I
> refresh the access token. I update both the stored access token and
> the Authorization header on the original request. Finally, I call
> `axiosInstance(originalRequest)`. Axios accepts a configuration object
> directly, so it can reconstruct the HTTP request using the original
> method, URL, headers, body, and other settings.

------------------------------------------------------------------------

# 100. Why Update Both Storage and `originalRequest`?

### Answer

> Storage and the existing request are two different pieces of state.
> Updating `sessionStorage` changes what future requests will read, but
> it does not automatically rewrite the configuration object of the
> request that already failed. Therefore, after receiving the new token,
> I update storage for future requests and update
> `originalRequest.headers.Authorization` for the immediate retry.

------------------------------------------------------------------------

# 101. What Is `_retry`?

### Answer

> `_retry` is a custom property that developers commonly add to the
> failed request configuration. It is not a special Axios keyword. It is
> used as a marker to indicate that the request has already entered the
> refresh/retry path. This prevents a repeated 401 from continuously
> triggering refresh operations.

------------------------------------------------------------------------

# 102. How Can Refresh Logic Cause an Infinite Loop?

### Answer

> If every 401 automatically triggers a refresh and the refresh or
> retried request also produces a 401, the interceptor can repeatedly
> call the refresh operation. The flow becomes 401 → refresh → 401 →
> refresh indefinitely. A retry marker such as `_retry`, combined with
> careful separation of the refresh client, prevents this behavior.

------------------------------------------------------------------------

# 103. Why Use a Separate Refresh Client?

### Answer

> A separate refresh client isolates the refresh operation from the
> protected API's authentication-recovery interceptor. If the refresh
> endpoint itself fails with 401 and the same interceptor processes it,
> the application can accidentally re-enter refresh logic. A separate
> Axios client makes the refresh path easier to reason about and
> prevents accidental recursive handling.

------------------------------------------------------------------------

# 104. How Do You Handle Concurrent 401s?

### Answer

> I use a shared refresh state such as `isRefreshing` and a queue for
> requests that receive 401 while the refresh is already running. The
> first request starts the refresh. Later 401 requests wait instead of
> starting additional refresh operations. Once the refresh succeeds, the
> new access token is given to the queued requests and they retry. If
> refresh fails, the queued requests are rejected.

------------------------------------------------------------------------

# 105. What Is a Refresh Queue?

### Answer

> A refresh queue is a coordination mechanism for requests that receive
> 401 while another request is already refreshing the access token.
> Instead of performing multiple refresh operations, those requests wait
> on a Promise. When the refresh succeeds, the Promise is resolved with
> the new token and the waiting requests retry. If refresh fails, the
> Promise is rejected.

------------------------------------------------------------------------

# 106. What Happens If the Refresh Token Expires?

### Answer

> If the refresh token itself is no longer valid, the refresh endpoint
> will reject the refresh attempt. At that point the application cannot
> obtain a new access token through the refresh flow. A common
> application behavior is to clear authentication state, log the user
> out, and redirect them to the login page.

------------------------------------------------------------------------

# 107. Where Should Interceptors Be Registered in React?

### Answer

> For a static application-wide Axios configuration, registering
> interceptors in the Axios module itself is often the simplest
> approach. This prevents registration from happening during every
> component render. If an interceptor genuinely depends on React
> lifecycle or dynamic state, it can be registered in `useEffect` and
> ejected during cleanup.

------------------------------------------------------------------------

# 108. Why Shouldn't We Register an Interceptor in Component Render?

### Answer

> React components can render multiple times. If interceptor
> registration happens directly in the render body, each render can add
> another interceptor. That can cause duplicated execution and confusing
> behavior. For static interceptors, registering them in the Axios
> module is cleaner. For lifecycle-dependent interceptors, `useEffect`
> with `eject()` cleanup can be used.

------------------------------------------------------------------------

# 109. Difference Between Protected Routes and Axios Interceptors?

### Answer

> Protected routes control frontend navigation. They decide whether a
> user should be allowed to enter a route such as `/dashboard`. Axios
> interceptors operate at the HTTP layer. They add authentication
> headers, process API responses, handle 401 responses, refresh tokens,
> and retry requests. A protected route does not replace an API
> interceptor, and an interceptor does not replace frontend route
> protection.

------------------------------------------------------------------------

# PART 41 --- WHY DO WE WRITE IT THIS WAY?

## 110. Why `axios.create()`?

``` js
const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});
```

Because we want a dedicated configured Axios client.

It solves:

``` text
repeated base URL
repeated configuration
scattered interceptors
```

and gives us a central HTTP layer.

------------------------------------------------------------------------

## 111. Why `axiosInstance.interceptors.request.use()`?

Because:

``` text
axiosInstance
    ↓
interceptors
    ↓
request
    ↓
use
```

means:

> Register a callback that participates in request processing for this
> Axios client.

------------------------------------------------------------------------

## 112. Why `(config) => {}`?

Because Axios needs to give our interceptor access to the request being
processed.

That request is represented by the configuration object.

So:

``` js
(config) => {}
```

means:

> When Axios reaches this interceptor, give me the request
> configuration.

------------------------------------------------------------------------

## 113. Why `return config`?

Because the interceptor is part of a processing chain.

We modify:

``` text
config
```

and pass it forward:

``` text
config → interceptor → config → next stage
```

------------------------------------------------------------------------

## 114. Why `config.headers.Authorization`?

Because the Authorization header belongs to the HTTP request headers.

We are effectively producing:

``` text
Authorization: Bearer <access-token>
```

------------------------------------------------------------------------

## 115. Why `error.response?.status`?

Because the status is inside the response associated with the Axios
error:

``` text
error
 ↓
response
 ↓
status
```

The optional chaining:

``` js
?.
```

protects the expression when no response exists.

------------------------------------------------------------------------

## 116. Why `error.config`?

Because after a request fails, we need the failed request's
configuration to retry the same operation.

``` text
error
 ↓
config
 ↓
failed request
```

------------------------------------------------------------------------

## 117. Why `_retry`?

Because we need our own marker to distinguish:

``` text
first authentication failure
```

from:

``` text
request already attempted recovery
```

It prevents repeated refresh attempts.

------------------------------------------------------------------------

## 118. Why `axiosInstance(error.config)`?

Because Axios can accept a configuration object directly.

The configuration contains the details required to make the request
again.

It is more generic than writing:

``` js
axiosInstance.get("/users");
```

because we may not know which endpoint failed.

------------------------------------------------------------------------

## 119. Why `Promise.reject(error)`?

Because if we are not recovering from an error, we need to preserve the
rejected Promise so the caller can handle it.

------------------------------------------------------------------------

## 120. Why Update Storage?

Because future requests will read the token from storage.

``` text
future request
   ↓
request interceptor
   ↓
getItem("token")
   ↓
NEW_TOKEN
```

------------------------------------------------------------------------

## 121. Why Update the Original Request?

Because the current failed request already has its own request
configuration.

Updating storage alone does not mutate that existing configuration.

So:

``` js
originalRequest.headers.Authorization =
  `Bearer ${newAccessToken}`;
```

updates the immediate retry.

------------------------------------------------------------------------

# PART 42 --- VISUAL MENTAL MODELS

## 122. Axios Instance

``` text
axios
  ↓
create()
  ↓
axiosInstance
  ├── baseURL
  ├── defaults
  ├── interceptors
  └── HTTP methods
```

------------------------------------------------------------------------

## 123. Request

``` text
Component
   ↓
API function
   ↓
Axios instance
   ↓
Request interceptor
   ↓
Request config
   ↓
Authorization header
   ↓
Server
```

------------------------------------------------------------------------

## 124. Response

``` text
Server
   ↓
Axios response/error
   ↓
Response interceptor
   ↓
Success OR error handling
   ↓
API caller
```

------------------------------------------------------------------------

## 125. Refresh

``` text
401
 ↓
Response interceptor
 ↓
error.config
 ↓
_refresh check
 ↓
Refresh token
 ↓
New access token
 ↓
Update storage
 ↓
Update original request
 ↓
Retry
```

------------------------------------------------------------------------

## 126. Concurrent Requests

``` text
Request A ──401──┐
Request B ──401──┤
Request C ──401──┤
                 ↓
             ONE REFRESH
                 ↓
             NEW TOKEN
                 ↓
          A / B / C RETRY
```

------------------------------------------------------------------------

# PART 43 --- FINAL COMPLETE MENTAL MODEL

## Axios Authentication --- Complete Mental Model

``` text
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
Update Storage
  ↓
Update Original Request
  ↓
Retry Original Request
  ↓
Application
```

Now translate the diagram into natural language:

1.  The application creates an Axios instance.
2.  API modules use that instance.
3.  A request enters Axios.
4.  The request interceptor receives the request configuration.
5.  The interceptor retrieves the current access token.
6.  It adds the Authorization header.
7.  The configuration is returned.
8.  Axios sends the request.
9.  The server responds.
10. The response enters the response interceptor.
11. A successful response continues normally.
12. If the server returns a 401 and the backend's contract says this
    represents an expired access credential, the interceptor can begin
    recovery.
13. The failed request's configuration is obtained through
    `error.config`.
14. A retry marker prevents endless recovery.
15. The refresh token is sent to the refresh endpoint.
16. A new access token is received.
17. Storage is updated for future requests.
18. The original request's Authorization header is updated for the
    immediate retry.
19. The original request is retried using its existing configuration.
20. If multiple requests fail at the same time, refresh coordination
    prevents every request from independently starting a refresh.
21. If refresh cannot recover authentication, the application clears
    authentication and requires login again.

------------------------------------------------------------------------

# PART 44 --- RECONSTRUCT THE IMPLEMENTATION WITHOUT MEMORIZING IT

If you forget the exact syntax, reconstruct the system using questions.

## Question 1

Where should common API configuration live?

``` text
Axios instance
```

So:

``` js
axios.create(...)
```

------------------------------------------------------------------------

## Question 2

Where should every outgoing protected request get its token?

``` text
Request interceptor
```

So:

``` js
axiosInstance.interceptors.request.use(...)
```

------------------------------------------------------------------------

## Question 3

What object does the interceptor need to modify?

``` text
config
```

So:

``` js
(config) => {
  ...
}
```

------------------------------------------------------------------------

## Question 4

Where is the access token added?

``` text
config.headers.Authorization
```

------------------------------------------------------------------------

## Question 5

Where should centralized 401 handling happen?

``` text
Response interceptor
```

------------------------------------------------------------------------

## Question 6

How do we know which request failed?

``` text
error.config
```

------------------------------------------------------------------------

## Question 7

How do we stop the same request from repeatedly refreshing?

``` text
originalRequest._retry
```

------------------------------------------------------------------------

## Question 8

How do we get a new access token?

``` text
refresh token
 ↓
refresh endpoint
```

------------------------------------------------------------------------

## Question 9

What must be updated after refresh?

Two things:

``` text
1. stored access token
2. original request's Authorization header
```

------------------------------------------------------------------------

## Question 10

How do we retry?

``` js
axiosInstance(originalRequest);
```

------------------------------------------------------------------------

## Question 11

What if multiple requests receive 401 together?

``` text
isRefreshing
+
failedQueue
```

------------------------------------------------------------------------

## Question 12

What if refresh fails?

``` text
clear authentication
→ reject
→ logout/login flow
```

That is the implementation logic.

------------------------------------------------------------------------

# PART 45 --- QUICK REFERENCE

## Axios Instance

``` js
const axiosInstance = axios.create({
  baseURL: "https://api.example.com"
});
```

## Request Interceptor

``` js
axiosInstance.interceptors.request.use((config) => {
  const token = sessionStorage.getItem("token");

  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }

  return config;
});
```

## Response Interceptor

``` js
axiosInstance.interceptors.response.use(
  (response) => response,
  (error) => Promise.reject(error)
);
```

## Detect 401

``` js
if (error.response?.status === 401) {
  // recovery
}
```

## Capture Failed Request

``` js
const originalRequest = error.config;
```

## Retry Protection

``` js
if (!originalRequest._retry) {
  originalRequest._retry = true;
}
```

## Update Token

``` js
sessionStorage.setItem("token", newAccessToken);
```

## Update Original Request

``` js
originalRequest.headers.Authorization =
  `Bearer ${newAccessToken}`;
```

## Retry

``` js
return axiosInstance(originalRequest);
```

## Reject

``` js
return Promise.reject(error);
```

------------------------------------------------------------------------

# Final Takeaway

The important thing is not remembering a large interceptor file.

The important thing is understanding the relationships:

``` text
Axios instance
    ↓
owns centralized API configuration

Request interceptor
    ↓
prepares outgoing requests

config
    ↓
represents the request configuration being processed

Access token
    ↓
authenticates normal protected API requests

Response interceptor
    ↓
processes server responses/errors

401
    ↓
possible authentication recovery

error.config
    ↓
identifies the request that failed

Refresh token
    ↓
obtains a new access token

originalRequest
    ↓
the failed request we want to retry

_new access token
    ↓
update storage + original request header

axiosInstance(originalRequest)
    ↓
retry the exact failed operation

_retry
    ↓
prevents endless recovery

isRefreshing + failedQueue
    ↓
coordinates simultaneous 401 responses
```

If you understand that chain, you can reconstruct the implementation
even if you forget the exact syntax.

The final goal is:

> **Do not memorize Axios authentication code. Understand why each
> object exists, what changes at every stage, and how the request moves
> from the React component to the server and back.**
