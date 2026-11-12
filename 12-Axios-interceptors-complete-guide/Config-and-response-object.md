# Axios `config` Object and `response` Object — Complete Explanation

When working with Axios interceptors, two objects are especially important:

- `config` → represents the **request configuration**
- `response` → represents the **server's response**

Understanding these two objects is extremely important because request interceptors mainly work with `config`, while response interceptors mainly work with `response`.

---

# 1. The Big Picture

Consider this request:

```js
axiosInstance.get("/users");
```

Before Axios sends the request, Axios internally has to know things such as:

- What URL should be called?
- What HTTP method should be used?
- What headers should be sent?
- Is there any request body?
- Is there a timeout?
- Are there query parameters?
- What should happen with credentials?
- What response format is expected?

Axios keeps this request-related information inside a **configuration object**.

That object is commonly called:

```js
config
```

So conceptually:

```text
axiosInstance.get("/users")
          ↓
Axios creates request configuration
          ↓
config object
          ↓
Request interceptor receives config
          ↓
Axios sends request
```

After the server responds, Axios creates a **response object** containing information about the server's response.

Conceptually:

```text
Server
   ↓
Response
   ↓
Axios creates response object
   ↓
Response interceptor receives response
   ↓
Application receives response
```

---

# 2. What Is the `config` Object?

The `config` object contains the information Axios uses to make the HTTP request.

For example:

```js
axiosInstance.get("/users");
```

can conceptually produce a configuration object similar to:

```js
{
    method: "get",
    url: "/users",
    baseURL: "https://api.example.com",
    headers: {},
    params: undefined,
    data: undefined,
    timeout: 0
}
```

The exact object can contain many more properties.

A simplified mental model is:

```text
config
│
├── url
├── method
├── baseURL
├── headers
├── params
├── data
├── timeout
├── responseType
├── withCredentials
├── auth
├── signal
├── validateStatus
├── transformRequest
├── transformResponse
└── other Axios configuration options
```

The exact properties present can vary depending on:

- Axios version
- browser vs Node.js
- how the request was created
- Axios defaults
- Axios instance configuration
- custom configuration supplied by the developer

So the object should not be thought of as having one fixed set of properties in every situation.

---

# 3. `config.url`

Example:

```js
config.url
```

This contains the URL/path supplied for the request.

For example:

```js
axiosInstance.get("/users");
```

may result in:

```js
config.url
```

being:

```text
/users
```

If the request was:

```js
axiosInstance.get("/products/10");
```

then:

```js
config.url
```

would typically be:

```text
/products/10
```

Important:

If an Axios instance has:

```js
const axiosInstance = axios.create({
    baseURL: "https://api.example.com"
});
```

and we call:

```js
axiosInstance.get("/users");
```

then conceptually:

```text
baseURL
   ↓
https://api.example.com

url
   ↓
/users
```

Together they form the final request URL:

```text
https://api.example.com/users
```

---

# 4. `config.method`

This contains the HTTP method used for the request.

Examples:

```js
config.method
```

could represent:

```text
get
post
put
patch
delete
```

For:

```js
axiosInstance.get("/users");
```

we may see:

```js
config.method
```

as:

```text
"get"
```

For:

```js
axiosInstance.post("/users");
```

we may see:

```text
"post"
```

This allows us to inspect the request method inside an interceptor.

Example:

```js
axiosInstance.interceptors.request.use((config) => {
    console.log("Method:", config.method);

    return config;
});
```

---

# 5. `config.baseURL`

`baseURL` contains the base URL configured for an Axios instance or request.

Example:

```js
const axiosInstance = axios.create({
    baseURL: "https://api.example.com"
});
```

Then:

```js
config.baseURL
```

may contain:

```text
https://api.example.com
```

If we call:

```js
axiosInstance.get("/users");
```

then Axios combines the base URL and request URL.

Conceptually:

```text
baseURL
   +
url
   ↓
https://api.example.com/users
```

---

# 6. `config.headers`

This contains the HTTP headers that Axios will send with the request.

Example:

```js
config.headers
```

may contain information such as:

```js
{
    Authorization: "Bearer abc123",
    "Content-Type": "application/json"
}
```

The exact structure and headers depend on the request and Axios version.

A very common use of a request interceptor is modifying headers.

For example:

```js
axiosInstance.interceptors.request.use((config) => {

    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});
```

The important path is:

```text
config
   ↓
headers
   ↓
Authorization
```

Therefore:

```js
config.headers.Authorization
```

means:

> Go inside the `config` object, access its `headers` property, and then access the `Authorization` property.

---

# 7. `config.params`

`params` contains query parameters that should be added to the URL.

Example:

```js
axiosInstance.get("/users", {
    params: {
        page: 2,
        limit: 10
    }
});
```

Conceptually:

```js
config.params
```

contains:

```js
{
    page: 2,
    limit: 10
}
```

Axios can use these to create a URL similar to:

```text
/users?page=2&limit=10
```

So:

```js
config.params.page
```

means:

```text
config
   ↓
params
   ↓
page
```

---

# 8. `config.data`

`data` contains the request body.

It is especially important for methods such as:

```text
POST
PUT
PATCH
```

Example:

```js
axiosInstance.post("/users", {
    name: "Nikitha",
    age: 22
});
```

Conceptually:

```js
config.data
```

contains the data being sent in the request body.

For example:

```js
{
    name: "Nikitha",
    age: 22
}
```

So:

```js
config.data.name
```

means:

```text
config
   ↓
data
   ↓
name
```

For a typical `GET` request, there may not be a request body, so `config.data` may be undefined.

---

# 9. `config.timeout`

This specifies how long Axios should wait before timing out the request.

Example:

```js
const axiosInstance = axios.create({
    timeout: 5000
});
```

This means Axios is configured with a timeout of approximately 5000 milliseconds.

The value can be inspected using:

```js
config.timeout
```

For example:

```text
5000
```

---

# 10. `config.responseType`

This tells Axios what type of response is expected.

Examples include:

```text
json
text
blob
arraybuffer
document
stream
```

For example:

```js
axios.get("/users", {
    responseType: "json"
});
```

Then:

```js
config.responseType
```

can contain:

```text
"json"
```

The available behavior depends on the environment and Axios version.

---

# 11. `config.withCredentials`

This controls whether credentials such as cookies should be included in cross-site requests where supported.

Example:

```js
axios.get("/users", {
    withCredentials: true
});
```

Then:

```js
config.withCredentials
```

can be:

```js
true
```

This is commonly relevant when working with authentication using cookies.

---

# 12. `config.auth`

Axios can also be configured with HTTP Basic Authentication.

Conceptually:

```js
{
    auth: {
        username: "user",
        password: "password"
    }
}
```

Then:

```js
config.auth
```

contains the authentication configuration.

---

# 13. `config.signal`

Axios supports request cancellation through `AbortController`.

Example:

```js
const controller = new AbortController();

axios.get("/users", {
    signal: controller.signal
});
```

The request configuration can contain:

```js
config.signal
```

which is associated with the abort signal.

This allows the request to be cancelled.

---

# 14. `config.validateStatus`

Axios uses `validateStatus` to determine which HTTP status codes should be considered successful for the promise chain.

Conceptually:

```js
config.validateStatus
```

is a function.

For example, a custom configuration could define:

```js
validateStatus: (status) => status < 500
```

The exact default behavior is part of Axios configuration.

This is important because it affects whether a response goes through the fulfilled or rejected response-interceptor path.

---

# 15. `config.transformRequest`

Axios supports transformation of request data before it is sent.

Conceptually:

```js
config.transformRequest
```

contains transformation logic.

This can be used to transform request data or headers before the actual network request.

---

# 16. `config.transformResponse`

Similarly, Axios supports transformation of response data before it is given to the application.

Conceptually:

```js
config.transformResponse
```

contains response transformation logic.

This is one reason why the data available to the application can already be transformed by the time the response interceptor receives it.

---

# 17. Important: `config` Is Not Only These Properties

A very important point:

Do NOT memorize the list as:

```text
config = only url, method, headers...
```

Axios configuration is much larger.

A better mental model is:

```text
config = instructions/options Axios uses to perform the request
```

Depending on the request and environment, additional Axios configuration properties can exist.

The most important properties to understand for interceptors are:

```text
config.url
config.method
config.baseURL
config.headers
config.params
config.data
config.timeout
config.responseType
config.withCredentials
config.signal
config.validateStatus
```

---

# 18. What Happens to `config` in a Request Interceptor?

Consider:

```js
axiosInstance.interceptors.request.use((config) => {

    console.log(config);

    return config;
});
```

Here:

```js
(config) => {}
```

is the callback function we registered.

Axios later calls this function when a request is being processed.

When Axios calls it, Axios provides the request configuration as the argument.

Conceptually:

```js
ourCallback(config);
```

So:

```js
(config) => {
    console.log(config);
}
```

means:

> When Axios calls this callback, give me the request configuration object.

That is why we can access:

```js
config.url
config.method
config.headers
config.params
config.data
```

---

# 19. Why Do We `return config`?

This is extremely important.

Consider:

```js
axiosInstance.interceptors.request.use((config) => {

    config.headers.Authorization = `Bearer ${token}`;

    return config;
});
```

The interceptor receives the request configuration.

We modify it.

Then we return it.

Conceptually:

```text
Axios
  ↓
creates config
  ↓
request interceptor
  ↓
config modified
  ↓
return config
  ↓
Axios continues with modified config
  ↓
server
```

Therefore:

```js
return config;
```

means:

> I have finished processing this request configuration. Continue the Axios request using this configuration.

---

# 20. Now What Is the `response` Object?

After the server processes the request, the server sends a response.

Axios wraps the information about that response into a response object.

A simplified response object looks like:

```js
{
    data: {...},
    status: 200,
    statusText: "OK",
    headers: {...},
    config: {...},
    request: ...
}
```

The most important properties are:

```text
response
│
├── data
├── status
├── statusText
├── headers
├── config
└── request
```

Again, the exact contents can vary depending on the environment and Axios version.

---

# 21. `response.data`

This contains the actual response data returned by the server, after Axios's response transformations have been applied.

Suppose the server returns:

```json
{
    "id": 1,
    "name": "Nikitha"
}
```

Then:

```js
response.data
```

may be:

```js
{
    id: 1,
    name: "Nikitha"
}
```

Therefore:

```js
response.data.name
```

means:

```text
response
   ↓
data
   ↓
name
```

and gives:

```text
"Nikitha"
```

This is usually the most important property when we actually want the API's returned data.

---

# 22. `response.status`

This contains the HTTP status code returned by the server.

Examples:

```text
200 → successful request
201 → resource created
204 → successful request with no content
400 → bad request
401 → unauthorized
403 → forbidden
404 → not found
500 → server error
```

Example:

```js
response.status
```

may return:

```text
200
```

We can use it inside a response interceptor:

```js
axiosInstance.interceptors.response.use((response) => {

    console.log("Status:", response.status);

    return response;
});
```

---

# 23. `response.statusText`

This contains the HTTP status text associated with the response when available.

For example:

```js
response.statusText
```

may be:

```text
"OK"
```

for a `200` response.

Important:

Do not depend heavily on `statusText` in applications because its availability and value can vary depending on the environment/server.

The numeric:

```js
response.status
```

is generally more important.

---

# 24. `response.headers`

This contains the response headers sent by the server.

For example, the server may return headers related to:

```text
Content-Type
Cache-Control
Set-Cookie
Content-Length
```

The exact headers depend on the server and browser/environment.

Conceptually:

```js
response.headers
```

could look like:

```js
{
    "content-type": "application/json"
}
```

So:

```js
response.headers["content-type"]
```

means:

```text
response
   ↓
headers
   ↓
content-type
```

---

# 25. `response.config`

This is one of the MOST IMPORTANT things to understand.

The response object contains:

```js
response.config
```

This refers to the Axios request configuration associated with that response.

In other words:

```text
response
   ↓
config
   ↓
request configuration
```

So inside a response interceptor:

```js
response.config
```

allows us to access the configuration of the request that produced this response.

For example:

```js
response.config.url
```

can tell us which URL the request was made for.

And:

```js
response.config.method
```

can tell us which HTTP method was used.

---

# 26. Why Does `response` Have `config`?

This may initially look confusing:

```js
config
```

already existed before the request.

Why is it also inside:

```js
response.config
```

?

Because the response is associated with the request that produced it.

Conceptually:

```text
REQUEST

config
│
├── url: "/users"
├── method: "get"
└── headers: {...}

        ↓
      SERVER
        ↓

RESPONSE

response
│
├── data
├── status
├── headers
├── config  ← request configuration
└── request
```

This allows Axios to preserve information about the original request alongside the response.

---

# 27. `response.request`

This refers to the underlying request object used by Axios's adapter/environment.

In a browser, this is commonly associated with the browser's XMLHttpRequest mechanism.

In Node.js, the underlying request object is different.

Therefore, its exact structure is environment-dependent.

For most normal React application code, you usually do not need to directly manipulate:

```js
response.request
```

Understanding that it represents the underlying network request is enough.

---

# 28. Complete Response Object Mental Model

A useful simplified model is:

```js
response = {
    data,
    status,
    statusText,
    headers,
    config,
    request
}
```

Think of it as:

```text
response
│
├── data
│     └── What server returned
│
├── status
│     └── HTTP status code
│
├── statusText
│     └── HTTP status description
│
├── headers
│     └── Headers sent by server
│
├── config
│     └── Configuration of the request
│
└── request
      └── Underlying network request
```

---

# 29. Complete `config` Mental Model

A useful simplified model is:

```js
config = {
    url,
    method,
    baseURL,
    headers,
    params,
    data,
    timeout,
    responseType,
    withCredentials,
    auth,
    signal,
    validateStatus,
    transformRequest,
    transformResponse
}
```

Think of it as:

```text
config
│
├── url
│     └── Which endpoint?
│
├── method
│     └── GET / POST / PUT / PATCH / DELETE
│
├── baseURL
│     └── Base address
│
├── headers
│     └── Extra HTTP information
│
├── params
│     └── Query parameters
│
├── data
│     └── Request body
│
├── timeout
│     └── Maximum waiting time
│
├── responseType
│     └── Expected response type
│
├── withCredentials
│     └── Credential/cookie behavior
│
├── auth
│     └── Authentication configuration
│
├── signal
│     └── Request cancellation
│
├── validateStatus
│     └── Determines fulfilled/rejected handling
│
├── transformRequest
│     └── Request transformation
│
└── transformResponse
      └── Response transformation
```

---

# 30. `config` vs `response`

This distinction is extremely important.

| `config` | `response` |
|---|---|
| Represents the request configuration | Represents the server response |
| Mainly used in request interceptors | Mainly used in response interceptors |
| Exists to tell Axios how to make the request | Contains information about what came back |
| Contains URL | Contains returned data |
| Contains HTTP method | Contains HTTP status |
| Contains request headers | Contains response headers |
| Contains request body | Contains response data |
| Contains query parameters | Contains original request config |
| Can be modified before request is sent | Can be inspected/transformed before app receives it |

---

# 31. Request Interceptor Using `config`

Example:

```js
axiosInstance.interceptors.request.use((config) => {

    console.log("URL:", config.url);
    console.log("Method:", config.method);
    console.log("Headers:", config.headers);

    return config;
});
```

Flow:

```text
axiosInstance.get("/users")
        ↓
Axios creates config
        ↓
Request interceptor receives config
        ↓
config.url
config.method
config.headers
        ↓
return config
        ↓
Axios sends request
```

---

# 32. Response Interceptor Using `response`

Example:

```js
axiosInstance.interceptors.response.use((response) => {

    console.log("Data:", response.data);
    console.log("Status:", response.status);
    console.log("Headers:", response.headers);
    console.log("Request URL:", response.config.url);

    return response;
});
```

Flow:

```text
Server
   ↓
Axios receives response
   ↓
Response object created
   ↓
Response interceptor
   ↓
response.data
response.status
response.headers
response.config
   ↓
return response
   ↓
Application
```

---

# 33. Very Important: `error` Is Different From `response`

In a response interceptor, we commonly have:

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

The second callback receives an:

```js
error
```

object.

The error can contain a `response` property when the server actually responded with an HTTP error status.

For example:

```js
error.response.status
```

means:

```text
error
  ↓
response
  ↓
status
```

So if the server returns:

```text
401 Unauthorized
```

we may access:

```js
error.response.status
```

and get:

```text
401
```

---

# 34. `error.response` Can Be Different From `response`

Successful response:

```js
(response) => {
    console.log(response.status);
}
```

Error response:

```js
(error) => {
    console.log(error.response?.status);
}
```

Why?

Because the second callback receives an **error object**, not directly the response object.

Conceptually:

```text
SUCCESS

response
│
├── data
├── status
├── headers
├── config
└── request
```

But for an HTTP error:

```text
error
│
├── message
├── config
├── response
│    ├── data
│    ├── status
│    ├── headers
│    ├── config
│    └── request
└── ...
```

The exact Axios error structure can vary, but this is the important mental model.

---

# 35. Why Do We Write `error.response.status`?

Suppose:

```js
error = {
    response: {
        status: 401
    }
}
```

Then:

```js
error.response.status
```

means:

```text
Start with error
      ↓
go to response
      ↓
go to status
      ↓
401
```

This is just nested object property access.

It is NOT special Axios syntax.

It is normal JavaScript dot notation.

---

# 36. Why Do We Sometimes Write `error.response?.status`?

The problem is that not every error necessarily has:

```js
error.response
```

For example, a network failure may occur before a response is received.

In that situation:

```js
error.response
```

may be undefined.

If we directly write:

```js
error.response.status
```

JavaScript could throw another error because we are trying to access:

```text
status
```

from:

```text
undefined
```

Optional chaining:

```js
error.response?.status
```

means:

> If `response` exists, access `status`; otherwise safely return `undefined`.

This is why you often see:

```js
if (error.response?.status === 401) {
    ...
}
```

---

# 37. Complete Axios Interceptor Example

```js
import axios from "axios";

const axiosInstance = axios.create({
    baseURL: "https://api.example.com"
});

axiosInstance.interceptors.request.use(
    (config) => {

        console.log("REQUEST INTERCEPTOR");

        console.log("URL:", config.url);
        console.log("Method:", config.method);
        console.log("Headers:", config.headers);

        const token = sessionStorage.getItem("token");

        if (token) {
            config.headers.Authorization = `Bearer ${token}`;
        }

        return config;
    }
);

axiosInstance.interceptors.response.use(
    (response) => {

        console.log("RESPONSE INTERCEPTOR");

        console.log("Data:", response.data);
        console.log("Status:", response.status);
        console.log("Headers:", response.headers);
        console.log("Request URL:", response.config.url);

        return response;
    },

    (error) => {

        console.log("RESPONSE ERROR INTERCEPTOR");

        console.log("Error:", error);
        console.log("Status:", error.response?.status);

        return Promise.reject(error);
    }
);

export default axiosInstance;
```

---

# 38. Complete Runtime Flow

Suppose the application executes:

```js
axiosInstance.get("/users");
```

The complete conceptual flow is:

```text
                 REQUEST SIDE

React Component
      ↓
axiosInstance.get("/users")
      ↓
Axios creates config
      ↓
┌─────────────────────────────┐
│ Request Interceptor         │
│                             │
│ receives config             │
│                             │
│ config.url                  │
│ config.method               │
│ config.headers              │
│ config.params               │
│ config.data                 │
│                             │
│ modify config if required   │
│                             │
│ return config               │
└─────────────────────────────┘
      ↓
Axios sends HTTP request
      ↓
      SERVER
      ↓
Server processes request
      ↓
                 RESPONSE SIDE
      ↓
Axios receives server response
      ↓
Axios creates response object
      ↓
┌─────────────────────────────┐
│ Response Interceptor        │
│                             │
│ receives response           │
│                             │
│ response.data               │
│ response.status             │
│ response.headers            │
│ response.config             │
│ response.request            │
└─────────────────────────────┘
      ↓
return response
      ↓
React/application receives it
```

---

# 39. The Most Important Relationship

Remember this relationship:

```text
REQUEST
   ↓
config
   ↓
Request Interceptor
```

and:

```text
SERVER RESPONSE
   ↓
response
   ↓
Response Interceptor
```

And:

```text
response.config
```

connects the response back to the request configuration.

---

# 40. Easy Real-Life Mental Model

Think of sending a parcel.

Before sending:

```text
Package
+
Address
+
Sender details
+
Delivery instructions
```

These are like:

```text
config
```

The courier checks/modifies the information before sending the parcel.

That is similar to:

```text
Request Interceptor
```

Then the parcel reaches the destination and a response comes back.

The response contains information such as:

```text
What was returned?
Was it successful?
What status did we receive?
What response headers were sent?
```

That is similar to:

```text
response
```

The response can also contain information about the original request:

```text
response.config
```

---

# 41. Interview-Level Explanation

If an interviewer asks:

### "What is the difference between Axios config and response objects?"

A strong answer is:

> The Axios `config` object represents the configuration of an HTTP request. It contains information such as the URL, HTTP method, base URL, headers, query parameters, request body, timeout, response type, credentials configuration, cancellation signal, and other Axios options. Request interceptors receive this config object, allowing us to inspect or modify the request before Axios sends it.
>
> The Axios `response` object represents the response received from the server. Its important properties include `data`, which contains the returned response data, `status`, which contains the HTTP status code, `statusText`, `headers`, `config`, and `request`. Response interceptors receive this response object after Axios receives the server response but before the response reaches the application.
>
> One important relationship is that `response.config` refers to the configuration of the request that produced that response. So `config` is mainly about how we make the request, while `response` is mainly about what we received back from the server.

---

# 42. Quick Cheat Sheet

## Request `config`

```text
config
├── url              → endpoint/path
├── method           → GET/POST/etc.
├── baseURL          → base address
├── headers          → request headers
├── params           → query parameters
├── data             → request body
├── timeout          → timeout configuration
├── responseType     → expected response type
├── withCredentials  → credential/cookie behavior
├── auth             → authentication configuration
├── signal           → cancellation
├── validateStatus   → status handling
├── transformRequest → request transformation
└── transformResponse→ response transformation
```

## Response

```text
response
├── data             → returned server data
├── status           → HTTP status code
├── statusText       → status description
├── headers          → response headers
├── config           → original request configuration
└── request          → underlying request object
```

## Error

```text
error
├── message
├── config
├── response
│   ├── data
│   ├── status
│   ├── headers
│   ├── config
│   └── request
└── ...
```

---

# 43. Final Mental Model

Do not memorize every property individually.

Understand the three main objects:

```text
             BEFORE REQUEST

                  config
                    ↓
          "How should Axios
           make this request?"
                    ↓
             Request Interceptor
                    ↓
                 SERVER
                    ↓
             Response Interceptor
                    ↑
                 response
                    ↑
          "What did the server
             send back?"
```

And remember:

```text
config
→ describes the request

response
→ describes the server's response

response.config
→ connects the response to the request that produced it

error.response
→ contains the server response when an error response exists
```

This understanding is more important than memorizing the property list.