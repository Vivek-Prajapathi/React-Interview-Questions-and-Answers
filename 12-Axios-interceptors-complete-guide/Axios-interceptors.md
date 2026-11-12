# Axios Interceptors --- Complete Concept-First Study Guide

> **Goal:** Understand Axios interceptors deeply enough to derive the
> syntax logically instead of memorizing it.

This guide follows the requested learning path: first understand a
normal Axios request, then understand the problem interceptors solve,
then derive the syntax from JavaScript objects and functions, and
finally connect everything to a realistic React application.

------------------------------------------------------------------------

# Part 1 --- Before Interceptors

## 1. What happens when we write `axios.get("/users")`?

Before understanding an interceptor, first understand the normal path:

``` text
React / Application
        ↓
axios.get("/users")
        ↓
      Axios
        ↓
   HTTP Request
        ↓
      Server
        ↓
   HTTP Response
        ↓
      Axios
        ↓
   Application
```

The important idea is that Axios is a library sitting between our
JavaScript application and the HTTP communication layer.

When we write:

``` js
axios.get("/users");
```

we are asking Axios to create and send a GET request.

## 2. Why does Axios need configuration?

A request is not just a URL. A real HTTP request may need information
such as:

-   HTTP method
-   URL
-   headers
-   request body
-   timeout
-   base URL
-   query parameters
-   authentication information

Conceptually, we can imagine the request configuration as:

``` js
{
    method: "GET",
    url: "/users",
    headers: {},
    baseURL: "https://example.com",
    timeout: 5000
}
```

This is a **learning model**, not a claim that Axios literally stores
its internal request in exactly this object shape at every stage.

The important idea is that Axios works with request configuration.

### Why use an object?

Instead of passing many unrelated arguments:

``` js
makeRequest("GET", "/users", {}, 5000, "...");
```

an object groups related information:

``` js
const config = {
    method: "GET",
    url: "/users",
    headers: {},
    timeout: 5000
};
```

Now code can inspect or modify one property without losing the rest.

For example:

``` js
config.method
config.url
config.headers
```

This becomes very important when we introduce request interceptors.

------------------------------------------------------------------------

# Part 2 --- The Problem Before Interceptors

Imagine an application has 20 API requests.

Suppose every request needs:

``` text
Authorization token
Common headers
Logging
Request modification
Centralized error handling
```

Without a centralized mechanism, we might repeatedly write:

``` js
axios.get("/users", {
    headers: {
        Authorization: `Bearer ${token}`
    }
});
```

and then repeat similar logic for:

``` js
axios.get("/products", ...)
axios.get("/orders", ...)
axios.post("/payments", ...)
axios.put("/profile", ...)
```

This creates:

-   duplication
-   repeated authentication logic
-   maintenance problems
-   inconsistent behavior
-   more places for bugs

We want one place where common logic can be applied.

That is where interceptors become useful.

------------------------------------------------------------------------

# Part 3 --- What Does "Intercept" Mean?

The word **intercept** does not mean that something is wrong.

It means:

> A point in a flow where we get an opportunity to perform logic before
> allowing the operation to continue.

Without a request interceptor:

``` text
Application
     ↓
   Axios
     ↓
   Server
```

With a request interceptor:

``` text
Application
     ↓
   Axios
     ↓
Request Interceptor
     ↓
   Server
```

For a response:

``` text
Server
  ↓
Axios
  ↓
Response Interceptor
  ↓
Application
```

The interceptor is therefore a controlled point in the request/response
pipeline.

------------------------------------------------------------------------

# Part 4 --- What Is an Interceptor?

A useful beginner definition is:

> **An interceptor is a function that runs automatically during Axios
> request/response processing, allowing us to perform common logic
> before a request is sent or before a response reaches the
> application.**

Now break the definition apart.

## "Function"

An interceptor is implemented using a JavaScript function.

For example:

``` js
(config) => {
    return config;
}
```

## "Automatically"

We do not manually call that callback every time an API request happens.

Instead, we register it with Axios.

Axios later invokes it at the appropriate point.

## "During Axios processing"

The interceptor is part of Axios's request/response processing pipeline.

## "Common logic"

Examples include:

-   attaching authentication tokens
-   logging requests
-   modifying headers
-   centralized response processing
-   handling authentication failures
-   logging errors

------------------------------------------------------------------------

# Part 5 --- Understanding Axios as an Object/API

This is one of the most important foundations.

When we write:

``` js
axios
```

we are working with the Axios API interface.

To understand syntax such as:

``` js
axios.interceptors.response.use()
```

we first need ordinary JavaScript property access.

## 1. The dot operator

Consider:

``` js
const person = {
    name: "Nikitha",
    age: 22
};

person.name;
```

The `.` means:

> Access the property named `name` from the object referenced by
> `person`.

So:

``` js
person.name
```

means:

``` text
person
  ↓
name
```

The result is:

``` js
"Nikitha"
```

## 2. Nested objects

Now:

``` js
const person = {
    address: {
        city: "Bangalore"
    }
};
```

Then:

``` js
person.address.city
```

means:

``` text
person
  ↓
address
  ↓
city
```

First:

``` js
person.address
```

gives the nested object:

``` js
{
    city: "Bangalore"
}
```

Then:

``` js
person.address.city
```

accesses `city` from that nested object.

### Critical insight

``` js
a.b.c
```

is not special Axios syntax.

It is normal JavaScript property access.

The **object structure determines the dots**.

------------------------------------------------------------------------

# Part 6 --- Deriving `axios.interceptors.response.use()`

Now we can understand the famous syntax.

``` js
axios.interceptors.response.use()
```

Conceptually:

``` text
axios
  ↓
interceptors
  ↓
response
  ↓
use()
```

A conceptual API tree is:

``` text
axios
└── interceptors
    ├── request
    │   └── use()
    │
    └── response
        └── use()
```

> This is a conceptual representation for learning the API. It is not
> intended to reproduce Axios's literal source-code implementation.

## `axios`

This is the Axios API/interface we are using.

## `.interceptors`

This accesses Axios's interceptor functionality.

## `.response`

This selects the response-interceptor side.

## `.use`

This accesses the registration function.

## `()`

Parentheses mean the function is being called.

So:

``` js
axios.interceptors.response.use()
```

can be read as:

> Go to Axios → access interceptors → access response interceptors →
> call the `use` function.

------------------------------------------------------------------------

# Part 7 --- `use` vs `use()`

This distinction is essential.

## `use`

This refers to the function itself.

Conceptually:

``` js
const use = someFunction;
```

We have a function reference.

## `use()`

This means:

> Call the function.

For example:

``` js
function greet() {
    console.log("Hello");
}
```

Then:

``` js
greet;
```

means the function itself.

But:

``` js
greet();
```

means execute it now.

The same JavaScript principle applies to:

``` js
use
```

versus:

``` js
use()
```

Therefore:

``` js
axios.interceptors.response.use(...)
```

means that we are **calling** the `use` method and giving it information
about the interceptor we want to register.

------------------------------------------------------------------------

# Part 8 --- Request `use()` vs Response `use()`

Both are derived from the same conceptual structure:

``` text
axios
└── interceptors
    ├── request
    │   └── use()
    └── response
        └── use()
```

Therefore:

``` js
axios.interceptors.request.use(...)
```

means:

> Register something for request processing.

And:

``` js
axios.interceptors.response.use(...)
```

means:

> Register something for response processing.

The difference is **where in the lifecycle the callback participates**.

------------------------------------------------------------------------

# Part 9 --- What Does `use()` Actually Do?

People often say:

> "`use()` registers an interceptor."

Let's unpack "register".

Suppose we have:

``` js
function greet() {
    console.log("Hello");
}
```

and:

``` js
someFunction(greet);
```

We are passing the function itself to another function.

We are **not** doing:

``` js
someFunction(greet());
```

The receiving system can store the function and use it later.

A simplified conceptual implementation could be:

``` js
const callbacks = [];

function use(callback) {
    callbacks.push(callback);
}
```

Then:

``` js
use(greet);
```

conceptually means:

``` text
greet function
     ↓
passed to use()
     ↓
stored / remembered
     ↓
some later event occurs
     ↓
system calls greet()
```

This is what we mean by **registration**.

> The code above is only a teaching model. It is not Axios's actual
> implementation.

------------------------------------------------------------------------

# Part 10 --- Why Are Callbacks Used?

A callback is a function that we give to another piece of code so that
the other code can decide when to call it.

Consider:

``` js
function greet() {
    console.log("Hello");
}

someFunction(greet);
```

We pass:

``` js
greet
```

not:

``` js
greet()
```

because we do not want `greet` to execute immediately.

## Compare the two

### Passing a function

``` js
use(myFunction);
```

means:

> Here is the function. You can call it later.

### Calling the function immediately

``` js
use(myFunction());
```

means:

> Run `myFunction` right now and pass its return value to `use`.

Those are completely different operations.

------------------------------------------------------------------------

# Part 11 --- Why Can't We Execute the Interceptor Immediately?

Imagine:

``` js
axios.interceptors.response.use(handleResponse);
```

At the moment this line runs, the API response may not even exist yet.

The request could be:

``` text
0 ms    request starts
50 ms   request travels
150 ms  server processes request
300 ms  response arrives
```

So Axios cannot use the response callback immediately.

Instead:

``` js
use(handleResponse);
```

means:

> Axios, remember this function. When an appropriate response is
> processed later, call it.

This is the core callback idea.

------------------------------------------------------------------------

# Part 12 --- Event Listener Connection

You have probably seen:

``` js
button.addEventListener("click", handleClick);
```

We do not write:

``` js
button.addEventListener("click", handleClick());
```

Why?

Because we want the browser to call `handleClick` **when the click
happens**.

The pattern is:

``` text
Give function now
       ↓
System remembers function
       ↓
Event happens later
       ↓
System calls function
```

Axios interceptors follow the same broad JavaScript callback pattern:

``` js
axios.interceptors.response.use(handleResponse);
```

means:

``` text
Give Axios the callback
       ↓
Axios registers it
       ↓
Response processing happens later
       ↓
Axios invokes callback
```

------------------------------------------------------------------------

# Part 13 --- Why Does Response `use()` Accept Two Functions?

A common form is:

``` js
axios.interceptors.response.use(
    (response) => {
        return response;
    },

    (error) => {
        return Promise.reject(error);
    }
);
```

Conceptually:

``` js
use(successCallback, errorCallback);
```

There are two possible paths in response processing:

``` text
             Response processing
                    ↓
                 Success?
                /       \
              Yes        No
               ↓          ↓
          success      error
          callback     callback
```

## First callback

``` js
(response) => {
    return response;
}
```

This is used for the successful response path.

Axios provides the response value to the callback.

## Second callback

``` js
(error) => {
    return Promise.reject(error);
}
```

This handles the rejection/error path.

The parameter is called `error` because it represents an error
condition, not a successful response object.

### Important nuance

This is a useful learning model of Axios's interceptor API. Axios is
built on Promise-based processing, so interceptor callbacks participate
in a Promise chain. The exact conditions under which a callback runs
depend on the request/response state and Axios's Promise behavior.

------------------------------------------------------------------------

# Part 14 --- Request Interceptors

A basic request interceptor:

``` js
axios.interceptors.request.use(
    (config) => {
        return config;
    }
);
```

Let's derive every part.

## Why `request`?

Because this interceptor participates in request processing.

## Why `.use()`?

Because we are registering a callback with Axios.

## Why `(config)`?

Because Axios gives the request interceptor the request configuration it
is currently processing.

Conceptually:

``` js
{
    method: "GET",
    url: "/users",
    baseURL: "https://example.com",
    headers: {},
    timeout: 5000
}
```

Again, this is a conceptual representation for learning.

------------------------------------------------------------------------

# Part 15 --- Understanding the Request Config

Consider:

``` js
(config) => {
    console.log(config);
    return config;
}
```

The callback receives a value.

We name that value:

``` js
config
```

The name is not magic.

This:

``` js
(config) => {}
```

means:

> When Axios calls this function, give me the value as the local
> variable named `config`.

Conceptually:

``` text
Axios
  ↓
creates/processes request configuration
  ↓
calls callback(config)
  ↓
our parameter receives that value
```

We can then access properties.

For example:

``` js
config.method
config.url
config.headers
```

------------------------------------------------------------------------

# Part 16 --- Deriving `config.headers.Authorization`

Suppose the conceptual object is:

``` js
const config = {
    method: "GET",
    url: "/users",
    headers: {
        Authorization: "Bearer abc123"
    }
};
```

Then:

``` js
config.headers.Authorization
```

is derived directly from the object structure:

``` text
config
└── headers
    └── Authorization
```

First:

``` js
config.headers
```

gives:

``` js
{
    Authorization: "Bearer abc123"
}
```

Then:

``` js
config.headers.Authorization
```

gets:

``` js
"Bearer abc123"
```

The multiple dots are therefore not something to memorize.

They reflect multiple levels of nesting.

------------------------------------------------------------------------

# Part 17 --- Why Return `config`?

Consider:

``` js
axios.interceptors.request.use((config) => {
    return config;
});
```

The request-processing flow is conceptually:

``` text
Request configuration
       ↓
Request interceptor
       ↓
Modify / inspect config
       ↓
return config
       ↓
Axios continues interceptor/request processing
       ↓
HTTP request is sent
```

The important idea is:

> Returning `config` passes the resulting request configuration onward
> through Axios's interceptor chain.

It does **not** mean:

> `return config` itself sends the HTTP request.

Axios is responsible for continuing request processing.

## Why modify and then return?

For example:

``` js
axios.interceptors.request.use((config) => {
    config.headers["X-Test"] = "Hello";
    return config;
});
```

The interceptor changes the configuration.

Then it returns the updated configuration so the chain can continue with
it.

------------------------------------------------------------------------

# Part 18 --- Practical Request Interceptor

A realistic authentication example:

``` js
axiosInstance.interceptors.request.use((config) => {
    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});
```

Let's go line by line.

## Line 1

``` js
axiosInstance.interceptors.request.use((config) => {
```

-   `axiosInstance` --- the Axios instance we configured.
-   `.interceptors` --- access interceptor functionality.
-   `.request` --- select request processing.
-   `.use()` --- register a callback.
-   `(config)` --- callback parameter receiving request configuration.

## Line 2

``` js
const token = sessionStorage.getItem("token");
```

We retrieve the stored authentication token.

## Line 3

``` js
if (token) {
```

Only modify the request if a token exists.

## Line 4

``` js
config.headers.Authorization = `Bearer ${token}`;
```

The structure is:

``` text
config
└── headers
    └── Authorization
```

So the syntax follows the object structure.

## Final line

``` js
return config;
```

The modified configuration is passed onward.

------------------------------------------------------------------------

# Part 19 --- Why Centralized Authentication Is Useful

Without an interceptor:

``` js
axios.get("/users", {
    headers: {
        Authorization: `Bearer ${token}`
    }
});
```

Every request may need similar code.

With an interceptor:

``` js
axiosInstance.get("/users");
axiosInstance.get("/products");
axiosInstance.get("/orders");
```

The request interceptor can automatically add the token.

The application code focuses on **what API to call**, while the
interceptor handles a cross-cutting concern:

``` text
Application request
      ↓
Authentication logic
      ↓
Server
```

------------------------------------------------------------------------

# Part 20 --- Understanding the Response Object

Before understanding response interceptors, understand what a response
represents.

Conceptually:

``` js
{
    data: {...},
    status: 200,
    statusText: "OK",
    headers: {...},
    config: {...}
}
```

The exact details can vary by adapter and Axios version, but these are
useful common response properties.

## `data`

Usually the response payload.

For example:

``` js
response.data
```

might be:

``` js
{
    id: 1,
    name: "Nikitha"
}
```

## `status`

The HTTP status code.

Example:

``` js
200
```

## `statusText`

A textual status description when provided.

Example:

``` js
"OK"
```

## `headers`

Response headers.

## `config`

The configuration associated with the request that produced the
response.

------------------------------------------------------------------------

# Part 21 --- Deriving `response.data`

Imagine:

``` js
const response = {
    data: {
        user: {
            name: "Nikitha"
        }
    },
    status: 200
};
```

Then:

``` js
response.data
```

means:

``` text
response
  ↓
data
```

and:

``` js
response.data.user.name
```

means:

``` text
response
  ↓
data
  ↓
user
  ↓
name
```

Again, the dots come from nested JavaScript object access.

------------------------------------------------------------------------

# Part 22 --- Response Interceptors

A simple response interceptor:

``` js
axios.interceptors.response.use(
    (response) => {
        return response;
    }
);
```

The conceptual flow is:

``` text
Server
   ↓
HTTP response
   ↓
Axios receives response
   ↓
Response interceptor
   ↓
Callback receives response
   ↓
Callback may inspect/modify it
   ↓
return response
   ↓
Promise chain continues
   ↓
Application receives result
```

The key difference from a request interceptor is **what value is being
processed**.

Request interceptor:

``` js
(config) => {}
```

Response interceptor:

``` js
(response) => {}
```

------------------------------------------------------------------------

# Part 23 --- `response` vs `response.data`

These are not the same thing.

``` js
response
```

means the complete Axios response representation.

``` js
response.data
```

means the data property inside that response.

Conceptually:

``` text
response
├── data
├── status
├── headers
└── config
```

Therefore:

``` js
response.data
```

means:

> Go inside the response and access its `data` property.

------------------------------------------------------------------------

# Part 24 --- Understanding Errors

A useful conceptual error structure is:

``` js
{
    message: "...",
    response: {
        status: 401,
        data: {...},
        headers: {...}
    }
}
```

Then:

``` js
error.response.status
```

is simply nested property access.

Break it apart:

``` js
error.response
```

means:

``` text
error
  ↓
response
```

Then:

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

This is not a special "Axios syntax".

It is normal JavaScript.

------------------------------------------------------------------------

# Part 25 --- Optional Chaining

Consider:

``` js
error.response?.status
```

The `?.` is optional chaining.

It is useful when `response` might not exist.

Without optional chaining:

``` js
error.response.status
```

If:

``` js
error.response
```

is `undefined`, attempting:

``` js
undefined.status
```

causes an error.

With:

``` js
error.response?.status
```

JavaScript safely evaluates the access.

Conceptually:

``` text
Does error.response exist?
       ↓
     yes → get status
       ↓
      no → result becomes undefined
```

This matters because not every Axios error necessarily represents a
server response.

For example, a network-level failure may occur before a server response
is available.

------------------------------------------------------------------------

# Part 26 --- Response Error Interceptor

A common pattern:

``` js
axios.interceptors.response.use(
    (response) => {
        return response;
    },

    (error) => {
        if (error.response?.status === 401) {
            sessionStorage.removeItem("token");
        }

        return Promise.reject(error);
    }
);
```

## Success callback

``` js
(response) => {
    return response;
}
```

The successful response continues through the Promise chain.

## Error callback

``` js
(error) => {
```

Axios provides the rejection/error value.

## Status check

``` js
error.response?.status === 401
```

Read it from left to right:

``` text
error
 ↓
response
 ↓
status
 ↓
compare with 401
```

`401` commonly represents an authentication-related HTTP failure.

## Remove token

``` js
sessionStorage.removeItem("token");
```

The stale/invalid authentication token is removed.

------------------------------------------------------------------------

# Part 27 --- Why `Promise.reject(error)`?

This is important.

Suppose an interceptor detects an error:

``` js
(error) => {
    if (error.response?.status === 401) {
        sessionStorage.removeItem("token");
    }

    return Promise.reject(error);
}
```

We handled some centralized logic, but we usually do not want to pretend
the API request succeeded.

Returning:

``` js
Promise.reject(error)
```

keeps the Promise chain in the rejected/error state.

Conceptually:

``` text
Server error
     ↓
Error interceptor
     ↓
Perform centralized handling
     ↓
Reject again
     ↓
Calling code can still handle the error
```

### What if we swallowed the error?

If code instead returned a successful value:

``` js
return {};
```

the downstream Promise chain could treat the interceptor result as
successful.

That can hide the original failure and create confusing behavior.

Therefore, when we have handled an error but still want the caller to
know that the operation failed, we propagate the rejection.

------------------------------------------------------------------------

# Part 28 --- Before and After Interceptors

## Without interceptor

``` js
const token = sessionStorage.getItem("token");

axios.get("/users", {
    headers: {
        Authorization: `Bearer ${token}`
    }
});
```

Then similar logic may be repeated for every request.

Problems:

-   duplication
-   maintenance burden
-   inconsistent behavior
-   repeated error handling

## With interceptor

``` text
Every API request
       ↓
Request interceptor
       ↓
Add common authentication/header logic
       ↓
Server
```

Response:

``` text
Server
  ↓
Response
  ↓
Response interceptor
  ↓
Centralized processing
  ↓
Application
```

The fundamental change is **where common logic lives**.

------------------------------------------------------------------------

# Part 29 --- Axios Instances

Applications often create an Axios instance:

``` js
const axiosInstance = axios.create({
    baseURL: "https://example.com"
});
```

Why?

Because an application may want a customized Axios client.

For example:

``` text
axios
 ├── default/global configuration
 └── generic Axios functionality

axiosInstance
 ├── baseURL
 ├── custom defaults
 └── interceptors
```

Then:

``` js
axiosInstance.get("/users");
```

uses the configured instance.

We can attach interceptors to that instance:

``` js
axiosInstance.interceptors.request.use(...);

axiosInstance.interceptors.response.use(...);
```

This is useful because API calls made through that instance can share
the configured request/response behavior.

------------------------------------------------------------------------

# Part 30 --- Complete Practical Example

## Step 1 --- Create the project structure

``` text
src/
├── api/
│   └── axiosInstance.js
├── App.jsx
└── main.jsx
```

## Step 2 --- Create the Axios instance

``` js
// src/api/axiosInstance.js

import axios from "axios";

const axiosInstance = axios.create({
    baseURL: "https://jsonplaceholder.typicode.com"
});

export default axiosInstance;
```

At this stage, there is no interceptor.

------------------------------------------------------------------------

## Step 3 --- Make a request

``` jsx
import axiosInstance from "./api/axiosInstance";

function App() {
    const getUsers = async () => {
        const response = await axiosInstance.get("/users");

        console.log(response);
    };

    return (
        <button onClick={getUsers}>
            Get Users
        </button>
    );
}

export default App;
```

Flow:

``` text
Button click
    ↓
getUsers()
    ↓
axiosInstance.get("/users")
    ↓
Axios
    ↓
JSONPlaceholder
    ↓
Response
    ↓
response variable
```

------------------------------------------------------------------------

# Part 31 --- Add a Request Interceptor

In `axiosInstance.js`:

``` js
axiosInstance.interceptors.request.use((config) => {
    console.log("REQUEST INTERCEPTOR");
    return config;
});
```

Now the flow becomes:

``` text
Button
  ↓
getUsers()
  ↓
axiosInstance.get()
  ↓
Request interceptor
  ↓
HTTP request
  ↓
Server
```

The callback does not execute merely because the registration line is
read.

Registration happens first.

Later, when Axios processes a request, Axios invokes the registered
callback.

------------------------------------------------------------------------

# Part 32 --- Inspect the Request Config

Change it to:

``` js
axiosInstance.interceptors.request.use((config) => {
    console.log("REQUEST CONFIG:", config);
    return config;
});
```

Open DevTools and inspect the object.

You can explore properties such as:

``` js
config.method
config.url
config.baseURL
config.headers
```

For example:

``` js
axiosInstance.interceptors.request.use((config) => {
    console.log("Method:", config.method);
    console.log("URL:", config.url);

    return config;
});
```

The syntax is derived from the object:

``` text
config
├── method
├── url
├── headers
└── ...
```

------------------------------------------------------------------------

# Part 33 --- Modify a Harmless Header

For learning, you can add:

``` js
axiosInstance.interceptors.request.use((config) => {
    config.headers["X-Test"] = "Hello";

    return config;
});
```

The flow is:

``` text
Original config
      ↓
Interceptor receives config
      ↓
Add X-Test header
      ↓
return config
      ↓
Axios continues
      ↓
HTTP request
```

This demonstrates the real purpose of a request interceptor: **inspect
or modify request processing before it continues**.

------------------------------------------------------------------------

# Part 34 --- Add a Response Interceptor

``` js
axiosInstance.interceptors.response.use(
    (response) => {
        console.log("RESPONSE INTERCEPTOR");
        console.log(response);

        return response;
    }
);
```

Now:

``` text
Request
  ↓
Request interceptor
  ↓
Server
  ↓
Response
  ↓
Response interceptor
  ↓
getUsers()
  ↓
response
```

Use console logs to prove the order.

------------------------------------------------------------------------

# Part 35 --- Demonstrate Error Handling

A complete response interceptor can be:

``` js
axiosInstance.interceptors.response.use(
    (response) => {
        console.log("SUCCESS RESPONSE");
        return response;
    },

    (error) => {
        console.log("ERROR RESPONSE");
        console.log(error);

        return Promise.reject(error);
    }
);
```

The first callback is for the fulfilled path.

The second callback participates in the rejected path.

------------------------------------------------------------------------

# Part 36 --- Authentication Example

A realistic setup:

``` js
const axiosInstance = axios.create({
    baseURL: "https://example.com"
});

axiosInstance.interceptors.request.use((config) => {
    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});

axiosInstance.interceptors.response.use(
    (response) => {
        return response;
    },

    (error) => {
        if (error.response?.status === 401) {
            sessionStorage.removeItem("token");
            window.location.href = "/login";
        }

        return Promise.reject(error);
    }
);

export default axiosInstance;
```

------------------------------------------------------------------------

# Part 37 --- Authentication Runtime Flow

Suppose the application calls:

``` js
axiosInstance.get("/profile");
```

The runtime flow is:

``` text
1. Component calls axiosInstance.get("/profile")
                    ↓
2. Axios creates/processes request configuration
                    ↓
3. Request interceptor runs
                    ↓
4. sessionStorage token is read
                    ↓
5. Authorization header is added
                    ↓
6. config is returned
                    ↓
7. Axios continues request processing
                    ↓
8. HTTP request reaches server
                    ↓
9. Server returns response
                    ↓
10. Axios processes response
                    ↓
11. Response interceptor runs
                    ↓
12. Success → response callback
       OR
       Error → error callback
                    ↓
13. Response/error continues through Promise chain
                    ↓
14. Calling code receives final result
```

------------------------------------------------------------------------

# Part 38 --- Complete Internal Mental Model

Think of the Axios instance as a configured processing system:

``` text
                         AXIOS INSTANCE
                              │
                  ┌───────────┴───────────┐
                  │                       │
               REQUEST                 RESPONSE
                  │                       │
                use()                   use()
                  │                       │
             callbacks              callbacks
                  │                       │
                  ↓                       ↓
           Request config            Response object
                  │                       │
                  ↓                       ↓
            HTTP request            HTTP response
                  │                       │
                  ↓                       ↓
                SERVER              Application
```

The key is that request and response interceptors operate on **different
stages and different values**.

------------------------------------------------------------------------

# Part 39 --- JavaScript Foundations Behind Interceptors

## 1. Objects

An object groups related data:

``` js
const user = {
    name: "Nikitha",
    age: 22
};
```

Axios uses objects extensively for configuration and response
information.

## 2. Nested objects

``` js
const config = {
    headers: {
        Authorization: "Bearer abc"
    }
};
```

This enables:

``` js
config.headers.Authorization
```

## 3. Property access

``` js
object.property
```

means access a property of an object.

## 4. Functions as values

JavaScript allows:

``` js
const fn = function () {};
```

A function can therefore be stored in a variable and passed around.

## 5. Passing functions as arguments

``` js
someFunction(callback);
```

A function can be passed to another function.

## 6. Callbacks

A callback is a function given to another system so it can be called
later.

## 7. Function invocation

``` js
fn();
```

means call the function.

## 8. Returning values

``` js
return config;
```

passes a value back to the caller/processing chain.

## 9. Promise basics

Axios requests return Promises.

Conceptually:

``` text
pending
  ↓
fulfilled
or
rejected
```

Interceptors participate in this asynchronous Promise-based processing.

## 10. Error handling

A rejected request can travel through the error/rejection side of the
interceptor chain.

## 11. Optional chaining

``` js
error.response?.status
```

safely accesses `status` when `response` may not exist.

## 12. Method calls

``` js
object.method()
```

means:

1.  access `method`
2.  invoke that function

That is exactly the JavaScript principle behind:

``` js
axios.interceptors.response.use(...)
```

------------------------------------------------------------------------

# Part 40 --- The Most Important Syntax Derivation

Instead of memorizing:

``` js
axiosInstance.interceptors.request.use(...)
```

derive it.

### Step 1

We have an Axios instance:

``` js
axiosInstance
```

### Step 2

We want interceptor functionality:

``` js
axiosInstance.interceptors
```

### Step 3

We are dealing with a request:

``` js
axiosInstance.interceptors.request
```

### Step 4

We need to register a callback:

``` js
axiosInstance.interceptors.request.use
```

### Step 5

We need to call the registration method:

``` js
axiosInstance.interceptors.request.use(...)
```

The syntax follows the API structure.

Likewise:

``` js
axiosInstance.interceptors.response.use(...)
```

means:

``` text
Axios instance
    ↓
interceptors
    ↓
response side
    ↓
registration method
    ↓
register callback(s)
```

------------------------------------------------------------------------

# Part 41 --- Common Misconceptions

## Misconception 1: "Interceptor means something is wrong."

Incorrect.

Interception simply means there is a point where we can perform logic
before the flow continues.

------------------------------------------------------------------------

## Misconception 2: "The interceptor sends the request."

Not exactly.

The interceptor can modify the request configuration. Axios continues
the request process afterward.

------------------------------------------------------------------------

## Misconception 3: "`use()` immediately executes the callback."

Incorrect.

This:

``` js
use(callback);
```

registers the function.

The callback is invoked later by Axios.

------------------------------------------------------------------------

## Misconception 4: "`response` is the JSON data."

Not necessarily.

Think:

``` text
response
├── data
├── status
├── headers
└── config
```

So:

``` js
response.data
```

accesses the payload/data property.

------------------------------------------------------------------------

## Misconception 5: "`error.response.status` is special Axios syntax."

No.

It is normal nested property access:

``` text
error
 ↓
response
 ↓
status
```

------------------------------------------------------------------------

## Misconception 6: "I need to memorize `axios.interceptors.response.use()`."

You can derive it:

``` text
Axios
 → interceptors
 → response
 → use()
```

------------------------------------------------------------------------

## Misconception 7: "Returning `config` sends the HTTP request."

No.

Returning `config` gives Axios the resulting request configuration so
its processing chain can continue.

------------------------------------------------------------------------

## Misconception 8: "Every Axios error has `error.response`."

No.

Some failures may occur without receiving an HTTP response, such as
certain network failures.

Therefore:

``` js
error.response?.status
```

can be safer than:

``` js
error.response.status
```

------------------------------------------------------------------------

# Part 42 --- Debugging and Visualization

When learning interceptors, do not guess what is happening.

Log the objects:

``` js
console.log(config);
```

``` js
console.log(response);
```

``` js
console.log(error);
```

Then inspect their properties.

A useful trace is:

``` text
Request
   ↓
REQUEST INTERCEPTOR
   ↓
Server
   ↓
Response
   ↓
RESPONSE INTERCEPTOR
   ↓
Component
```

Add logs:

``` js
console.log("1. before request");

axiosInstance.interceptors.request.use((config) => {
    console.log("2. request interceptor");
    return config;
});

axiosInstance.interceptors.response.use(
    (response) => {
        console.log("4. response interceptor");
        return response;
    },
    (error) => {
        console.log("4. response error interceptor");
        return Promise.reject(error);
    }
);

console.log("3. request sent");
```

The exact placement of logs relative to asynchronous execution should be
understood through the Promise/request lifecycle, but the principle is
to use logs to observe the sequence rather than memorize it.

------------------------------------------------------------------------

# Part 43 --- 30-Second Interview Answer

> Axios interceptors allow us to run common logic during the request and
> response lifecycle. A request interceptor runs before the request is
> sent, so it is commonly used to add authentication tokens, headers, or
> logging. A response interceptor runs while Axios is processing the
> response, so it can centralize response transformation and error
> handling. We register interceptor callbacks using
> `interceptors.request.use()` or `interceptors.response.use()`. The
> callbacks are passed as functions, so Axios can invoke them later at
> the appropriate point in the request/response Promise chain.

------------------------------------------------------------------------

# Part 44 --- 1-Minute Interview Answer

> An Axios interceptor is a callback registered with Axios that allows
> us to execute common logic during request or response processing.
> Instead of repeating logic such as adding an Authorization token to
> every API call, we can use a request interceptor. Axios gives the
> interceptor the request configuration object, which we can inspect or
> modify, and then we return the configuration so processing can
> continue.
>
> Similarly, a response interceptor receives the response object and can
> inspect or modify it. It can also have an error callback for
> centralized error handling, such as handling a 401 authentication
> failure. The important thing is that `use()` registers the callback;
> it does not execute the callback immediately. Axios invokes the
> callback later when the relevant request or response passes through
> the interceptor chain.

------------------------------------------------------------------------

# Part 45 --- 2--3 Minute Interview Answer

> Axios interceptors are a mechanism for running common logic during
> Axios's request and response lifecycle. The easiest way to understand
> them is to first look at a normal request. When we call
> `axios.get("/users")`, our application gives the request to Axios,
> Axios prepares the request, sends it to the server, receives the HTTP
> response, and then returns the result to the application.
>
> The problem is that many requests often need common behavior. For
> example, every authenticated API request may need an Authorization
> token, and every response may need centralized error handling. Instead
> of duplicating that logic in every API call, Axios provides
> interceptors.
>
> A request interceptor is registered using
> `axios.interceptors.request.use(...)`. The syntax can be derived from
> normal JavaScript property access: we start with the Axios instance,
> access `interceptors`, select `request`, and then call its `use`
> method to register a callback. Axios later calls that callback during
> request processing and provides the request configuration as the
> `config` parameter. We can inspect or modify properties such as
> `config.headers`, and then return `config` so the Axios processing
> chain can continue.
>
> A response interceptor follows the same idea:
> `axios.interceptors.response.use(...)`. Its success callback receives
> a response object containing properties such as `data`, `status`,
> headers, and request configuration. It can inspect or modify that
> response and return it so processing continues. The response
> interceptor can also receive an error callback. For example, we can
> check `error.response?.status` for a 401 and centrally remove an
> invalid token. We normally return `Promise.reject(error)` afterward so
> the failure is still propagated to the calling code.
>
> The key conceptual point is that `use()` registers callbacks; it does
> not immediately execute them. We pass the function itself because
> Axios does not know when the request or response will occur. Axios
> remembers the callback and invokes it later at the appropriate point
> in its Promise-based processing chain.

------------------------------------------------------------------------

# Part 46 --- Practice Questions

## Level 1 --- Understanding

1.  What does the dot mean in `axios.interceptors`?
2.  What does `use()` do?
3.  What does registration mean?
4.  Why is a callback passed to `use()`?
5.  What is the difference between `use` and `use()`?
6.  Why do we use `use(callback)` instead of `use(callback())`?

## Level 2 --- Object Understanding

Given:

``` js
const response = {
    data: {
        user: {
            name: "Nikitha"
        }
    },
    status: 200
};
```

Derive:

``` js
response.data.user.name
```

Explain every dot.

Expected reasoning:

``` text
response
  ↓
data
  ↓
user
  ↓
name
```

## Level 3 --- Request Interceptor

Write an interceptor that logs:

``` text
Method: GET
URL: /users
```

Hint:

``` js
config.method
config.url
```

## Level 4 --- Authentication

Write a request interceptor that:

1.  reads `token` from `sessionStorage`
2.  adds it to `config.headers.Authorization`
3.  returns `config`

## Level 5 --- Error Handling

Write a response interceptor that:

1.  checks for status `401`
2.  removes the token
3.  rejects the error again

## Level 6 --- Debugging

What is wrong here?

``` js
axios.interceptors.request.use((config) => {
    console.log(config);
});
```

Think about what the interceptor chain expects from a request
interceptor.

Then compare it with:

``` js
axios.interceptors.request.use((config) => {
    console.log(config);
    return config;
});
```

------------------------------------------------------------------------

# Part 47 --- Final Cheat Sheet

  -----------------------------------------------------------------------
  Concept                             Meaning
  ----------------------------------- -----------------------------------
  Interceptor                         A callback-based point in Axios
                                      request/response processing

  Request interceptor                 Runs during request processing
                                      before the request continues to the
                                      server

  Response interceptor                Runs during response processing
                                      before the result continues to
                                      application code

  `config`                            Request configuration being
                                      processed

  `response`                          Axios response representation

  `error`                             Error/rejection information

  `error.response`                    Server-response information when a
                                      response exists

  `error.response.status`             HTTP status accessed through nested
                                      properties

  `use()`                             Registration method for interceptor
                                      callbacks

  Callback                            Function given to Axios so Axios
                                      can call it later

  Registration                        Giving Axios a function to
                                      remember/use later

  `return config`                     Pass resulting request
                                      configuration onward

  `return response`                   Pass resulting response onward

  `Promise.reject(error)`             Propagate the failure through the
                                      Promise chain
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# Final Mental Model

If you remember only one logical flow, use this:

``` text
                 APPLICATION
                      │
                      │ API call
                      ↓
              AXIOS INSTANCE
                      │
                      ↓
              REQUEST INTERCEPTOR
                      │
                receives config
                      │
              inspect / modify
                      │
                return config
                      ↓
                 HTTP REQUEST
                      │
                      ↓
                    SERVER
                      │
                      ↓
                HTTP RESPONSE
                      │
                      ↓
             RESPONSE INTERCEPTOR
                 /           \
              success        error
                ↓              ↓
          receives response   receives error
                ↓              ↓
          return response   handle + reject
                 \             /
                  ↓           ↓
                    APPLICATION
```

And when you see:

``` js
axiosInstance.interceptors.request.use((config) => {
    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});

axiosInstance.interceptors.response.use(
    (response) => {
        return response;
    },
    (error) => {
        if (error.response?.status === 401) {
            sessionStorage.removeItem("token");
        }

        return Promise.reject(error);
    }
);
```

derive it rather than memorize it:

``` text
axiosInstance
    ↓
interceptors
    ↓
request / response
    ↓
use()
    ↓
register callback(s)
    ↓
Axios calls them later
    ↓
request callback receives config
    ↓
response callback receives response
    ↓
error callback receives error
    ↓
return values continue the Promise/interceptor chain
```

That is the core mental model behind Axios interceptors.
