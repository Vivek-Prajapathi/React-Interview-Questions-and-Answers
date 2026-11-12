
---

# 1. BASIC AXIOS INTERCEPTOR INTERCEPTOR QUESTIONS

---

## Q1. What is an Axios interceptor?

### Answer

"An interceptor is a function that runs automatically between Axios and the server. It allows us to perform common logic before a request is sent or before a response reaches our application."

There are two main types of interceptors:

1. **Request interceptor**
2. **Response interceptor**

A request interceptor runs during request processing, before the request continues to the server.

A response interceptor runs during response processing, before the response continues to the application.

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

Here, the request interceptor receives the request configuration, modifies it by adding the token, and returns it so Axios can continue processing the request.

The main purpose of interceptors is to centralize common logic instead of repeating the same logic in every API call.

---

# 2. What problem do Axios interceptors solve?

### Answer

The main problem Axios interceptors solve is **repeated common logic**.

For example, suppose every API request in an application needs an authentication token.

Without an interceptor, we might repeatedly write:

```js
axios.get("/users", {
    headers: {
        Authorization: `Bearer ${token}`
    }
});
```

We would have to repeat similar logic for every API request.

Instead, we can use a request interceptor:

```js
axiosInstance.interceptors.request.use((config) => {
    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});
```

Now the authentication logic is centralized.

So interceptors are useful for common cross-cutting logic such as:

- authentication
- common headers
- request logging
- request modification
- response processing
- centralized error handling

---

# 3. What does "intercept" mean in Axios?

### Answer

Intercept does **not** mean that the request is wrong.

It means that Axios provides a point in its processing flow where we can execute some logic before allowing the request or response to continue.

Normally, the flow is:

```text
Application
     ↓
Axios
     ↓
Server
```

With a request interceptor:

```text
Application
     ↓
Axios
     ↓
Request Interceptor
     ↓
Server
```

For the response:

```text
Server
     ↓
Axios
     ↓
Response Interceptor
     ↓
Application
```

So when we say "intercept", we mean:

> Axios gives us an opportunity to inspect or modify something during the request/response flow before the processing continues.

---

# 4. Why do we need interceptors if the request is already correct?

### Answer

The request does not need to be wrong for us to use an interceptor.

The interceptor is simply a place where we can perform common logic.

For example, suppose the request itself is perfectly valid, but every request needs an authentication token.

Instead of manually adding the token to every request, we can allow the request interceptor to add it automatically.

So the purpose is not:

```text
Request is wrong
↓
Stop it
```

The purpose is:

```text
Request is being processed
↓
Perform common logic
↓
Continue processing
```

---

# 5. What are the two types of Axios interceptors?

### Answer

There are two main types:

### 1. Request interceptor

```js
axiosInstance.interceptors.request.use(...)
```

It is used during request processing.

Conceptually:

```text
Application
    ↓
Axios
    ↓
Request Interceptor
    ↓
Server
```

The request interceptor receives the request configuration, usually represented by `config`.

### 2. Response interceptor

```js
axiosInstance.interceptors.response.use(...)
```

It is used during response processing.

Conceptually:

```text
Server
    ↓
Axios
    ↓
Response Interceptor
    ↓
Application
```

The response interceptor receives the response on the successful path and error information on the rejected path.

---

# 6. What is the difference between request and response interceptors?

### Answer

The main difference is the stage at which they participate in the Axios lifecycle and the information they receive.

A request interceptor works with the request configuration:

```js
axiosInstance.interceptors.request.use((config) => {
    return config;
});
```

The flow is:

```text
Application
    ↓
Axios
    ↓
Request interceptor
    ↓
Server
```

A response interceptor works with the response:

```js
axiosInstance.interceptors.response.use((response) => {
    return response;
});
```

The flow is:

```text
Server
    ↓
Axios
    ↓
Response interceptor
    ↓
Application
```

Request interceptors are commonly used for authentication tokens, headers, logging, and request modification.

Response interceptors are commonly used for response processing, logging, and centralized error handling.

---

# 7. Where are Axios interceptors used in a React application?

### Answer

They are commonly configured in a centralized Axios instance.

For example:

```js
import axios from "axios";

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

export default axiosInstance;
```

Then components use:

```js
axiosInstance.get("/users");
```

Instead of configuring authentication logic separately inside every component.

This keeps the API-related logic centralized and makes components cleaner.

---

# 8. Explain `axios.interceptors.request.use()`.

### Answer

I would understand this syntax step by step rather than memorizing it.

Start with:

```js
axios
```

Then:

```js
axios.interceptors
```

We access Axios's interceptor functionality.

Then:

```js
axios.interceptors.request
```

We select the request interceptor section.

Then:

```js
axios.interceptors.request.use
```

We access the `use` method.

Finally:

```js
axios.interceptors.request.use(...)
```

We call the `use` method and pass a callback function.

Conceptually:

```text
axios
   ↓
interceptors
   ↓
request
   ↓
use()
   ↓
register callback
```

---

# 9. Explain `axios.interceptors.response.use()`.

### Answer

The same JavaScript property-access principle applies.

```js
axios
```

gives us the Axios API.

```js
axios.interceptors
```

accesses the interceptor functionality.

```js
axios.interceptors.response
```

selects the response interceptor section.

```js
axios.interceptors.response.use
```

accesses the `use` method.

And:

```js
axios.interceptors.response.use(...)
```

calls the method and passes callback functions.

Conceptually:

```text
axios
   ↓
interceptors
   ↓
response
   ↓
use()
   ↓
register callback(s)
```

---

# 10. What does the dot `.` mean in `axios.interceptors.response.use()`?

### Answer

The dot is normal JavaScript property access.

For example:

```js
const person = {
    name: "Nikitha"
};

person.name;
```

Here:

```js
person.name
```

means:

> Access the `name` property from `person`.

With nested objects:

```js
const person = {
    address: {
        city: "Bangalore"
    }
};
```

we can write:

```js
person.address.city;
```

This means:

```text
person
   ↓
address
   ↓
city
```

The same idea is used conceptually in:

```js
axios.interceptors.response.use()
```

We move through the Axios API structure:

```text
axios
   ↓
interceptors
   ↓
response
   ↓
use
```

The dots themselves are not special Axios syntax. They are JavaScript property access.

---

# 11. What is the difference between `use` and `use()`?

### Answer

This is a basic JavaScript function concept.

Suppose we have:

```js
function greet() {
    console.log("Hello");
}
```

Then:

```js
greet
```

refers to the function.

But:

```js
greet()
```

calls the function.

Similarly:

```js
use
```

refers to the function/method.

While:

```js
use()
```

means we are invoking it.

Therefore:

```js
axios.interceptors.response.use(...)
```

means that we are calling the `use` method and providing the required callback functions.

---

# 12. Why is `use()` called a method?

### Answer

In JavaScript, when a function is stored as a property of an object and is invoked through that object, we commonly call it a method.

For example:

```js
const person = {
    greet() {
        console.log("Hello");
    }
};

person.greet();
```

Here:

```js
person.greet
```

accesses the function.

And:

```js
person.greet()
```

calls it.

Similarly:

```js
axios.interceptors.response.use(...)
```

accesses the `use` function through the interceptor API and invokes it.

---

# 13. What does `use()` do in Axios interceptors?

### Answer

`use()` is used to **register interceptor callback functions**.

For example:

```js
axios.interceptors.request.use((config) => {
    return config;
});
```

We give Axios a callback function.

Axios then remembers that callback and invokes it when the relevant request processing occurs.

Conceptually:

```text
Register callback
      ↓
Axios remembers callback
      ↓
Request occurs
      ↓
Axios calls callback
      ↓
Callback receives config
      ↓
Callback returns config
      ↓
Axios continues
```

The exact internal implementation is more complex, but this is the correct mental model for understanding registration.

---

# 14. What does "register an interceptor" mean?

### Answer

Registering means:

> We give Axios a callback function now so that Axios can call that function later when the appropriate request or response passes through the interceptor processing.

For example:

```js
axios.interceptors.request.use(myRequestHandler);
```

We are not manually executing:

```js
myRequestHandler();
```

We are telling Axios:

> "Use this function during request processing."

Later, when a request is processed, Axios invokes the callback.

---

# 15. Why are callbacks used in interceptors?

### Answer

Because Axios needs to decide **when** the function should execute.

When we write:

```js
axios.interceptors.request.use((config) => {
    console.log(config);
    return config;
});
```

we are giving Axios a function.

We are not asking our code to execute it immediately.

Axios determines when the request reaches that stage and then invokes the callback.

The pattern is:

```text
Give function now
      ↓
Axios remembers it
      ↓
Request/response processing occurs
      ↓
Axios calls function later
```

That is why the function is called a callback.

---

# 16. Why do we pass `callback` instead of `callback()`?

### Answer

Because:

```js
callback
```

means:

> Pass the function itself.

While:

```js
callback()
```

means:

> Execute the function immediately.

For example:

```js
function greet() {
    console.log("Hello");
}
```

If we write:

```js
someFunction(greet);
```

we are giving `someFunction` the function.

But:

```js
someFunction(greet());
```

first executes `greet()` and then passes its return value.

For interceptors, we want Axios to decide when to execute the callback.

Therefore we pass:

```js
callback
```

not:

```js
callback()
```

---

# 17. How is registering an interceptor similar to `addEventListener()`?

### Answer

Both follow the callback pattern.

For example:

```js
button.addEventListener("click", handleClick);
```

We give the browser a function.

The browser remembers the function and calls it when the click happens.

Similarly:

```js
axios.interceptors.response.use(handleResponse);
```

we give Axios a function.

Axios remembers the function and calls it during the appropriate response processing.

The common pattern is:

```text
Give function now
      ↓
System remembers function
      ↓
Something happens later
      ↓
System calls function
```

---

# 18. Why does response `use()` commonly receive two functions?

### Answer

A response interceptor can handle both the successful and rejected paths.

For example:

```js
axios.interceptors.response.use(
    (response) => {
        return response;
    },

    (error) => {
        return Promise.reject(error);
    }
);
```

The first callback is for the successful response path.

The second callback is for the error/rejected path.

Conceptually:

```text
Response processing
       ↓
     Success?
      /     \
    Yes      No
     ↓        ↓
response    error
callback    callback
```

This allows us to centralize response processing and error handling.

---

# 19. Are both functions passed to `use()` callbacks?

### Answer

Yes.

For example:

```js
axios.interceptors.response.use(
    (response) => {
        return response;
    },

    (error) => {
        return Promise.reject(error);
    }
);
```

Both arrow functions are callback functions.

We pass them to Axios.

Axios decides when the appropriate callback should be invoked based on the result of the response processing.

---

# 20. Why does the request interceptor usually have one callback?

### Answer

The request interceptor's main callback handles the request configuration before the request continues.

For example:

```js
axios.interceptors.request.use((config) => {
    return config;
});
```

The callback receives the request configuration.

There can also be an error handler for request-interceptor processing, but the most common example focuses on the callback that receives and returns `config`.

The important concept is that the request side works with request configuration, while the response side commonly demonstrates both success and error paths.

---

# 21. What is the `config` parameter in a request interceptor?

### Answer

`config` represents the request configuration being processed by Axios.

For example:

```js
axios.interceptors.request.use((config) => {
    console.log(config);
    return config;
});
```
when we write :-
```js
axios.get("/users");
```
Axios internally creates a request configuration object.

Conceptually, the configuration can contain information such as:

```js
{
    method: "GET",
    url: "/users",
    baseURL: "https://example.com",
    headers: {},
    timeout: 5000
}
```

So when we write:
```js 
config.headers.Authorization = `Bearer ${token}`;
```

we are basically saying:
```js
"Take this request's configuration and add an Authorization header."
```
Then:
```js
return config;
```

means:
"Okay Axios, I've finished modifying the request. Now continue sending it."

So:
Request created
      ↓
Interceptor receives config
      ↓
We modify config
      ↓
return config
      ↓
Axios sends request
      ↓
Server

The exact structure can vary, but the important concept is:

> `config` contains the information Axios uses to process the request.

The interceptor can inspect or modify that configuration.

---

# 22. Why does a request interceptor receive `config`?

### Answer

Because the request interceptor runs during request processing, before the server response exists.

The flow is:

```text
Request configuration
       ↓
Request interceptor
       ↓
HTTP request
       ↓
Server
       ↓
Response
```

At the request-interceptor stage, we have request information, not the response.

Therefore Axios gives the callback the request configuration.

---

# 23. What can we access from `config`?

### Answer

We can inspect properties related to the request.

For example:

```js
config.method
config.url
config.baseURL
config.headers
config.timeout
```

For example:

```js
console.log(config.method);
console.log(config.url);
```

We can also modify the request configuration.

For example:

```js
config.headers["X-Test"] = "Hello";
```

or:

```js
config.headers.Authorization = `Bearer ${token}`;
```

Then we return the configuration.

---

# 24. Explain `config.headers.Authorization`.

### Answer

This is nested object property access.

Conceptually:

```js
const config = {
    headers: {
        Authorization: "Bearer abc123"
    }
};
```

The structure is:

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

1. Start with `config`
2. Access `headers`
3. From `headers`, access `Authorization`

The dots are simply JavaScript property access.

---

# 25. Why do we return `config`?

### Answer

We return `config` so that the resulting request configuration can continue through Axios's processing chain.

For example:

```js
axiosInstance.interceptors.request.use((config) => {

    config.headers.Authorization = `Bearer ${token}`;

    return config;
});
```

The flow is:

```text
Request configuration
       ↓
Request interceptor
       ↓
Modify config
       ↓
return config
       ↓
Axios continues processing
       ↓
Request is sent
```

Returning `config` does not itself send the request.

It passes the resulting configuration onward.

---

# 26. What happens if we don't return `config`?

### Answer

The interceptor chain expects the callback to provide the value that should continue through the request processing chain.

For a normal request interceptor, we therefore write:

```js
return config;
```

If the callback does not return the expected configuration, the next stage may not receive the request configuration it needs, which can cause the request to fail or behave unexpectedly.

So the mental model is:

```text
Receive config
     ↓
Inspect / modify
     ↓
Return config
     ↓
Continue processing
```

---

# 27. How do you add an authentication token using an interceptor?

### Answer

We can use a request interceptor:

```js
axiosInstance.interceptors.request.use((config) => {

    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});
```

The steps are:

1. Retrieve the token.
2. Check whether the token exists.
3. Access the request headers.
4. Add the `Authorization` header.
5. Return the modified configuration.
6. Axios continues processing the request.

This avoids repeating authentication logic in every API call.

---

# 28. Why is an interceptor useful for authentication?

### Answer

Authentication is commonly required across many API requests.

Without an interceptor, every request may need to manually include:

```js
headers: {
    Authorization: `Bearer ${token}`
}
```

That creates duplication.

With a request interceptor, we define the logic once:

```js
axiosInstance.interceptors.request.use((config) => {
    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});
```

Then normal API calls can remain simple:

```js
axiosInstance.get("/users");
axiosInstance.get("/products");
axiosInstance.get("/orders");
```

The interceptor handles the common authentication logic.

---

# 29. What is the response object?

### Answer

The response object represents the Axios response being processed.

Conceptually, it can contain:

```js
{
    data: {...},
    status: 200,
    statusText: "OK",
    headers: {...},
    config: {...}
}
```

Important properties include:

- `data` — the response payload
- `status` — HTTP status code
- `statusText` — status description when available
- `headers` — response headers
- `config` — request configuration associated with the response

The exact object can contain additional properties, but these are the important concepts for understanding response interceptors.

---

# 30. What is the difference between `response` and `response.data`?

### Answer

`response` is the larger Axios response object.

Conceptually:

```text
response
├── data
├── status
├── headers
├── config
└── ...
```

Therefore:

```js
response.data
```

means:

> Access the `data` property inside the response object.

For example:

```js
response.data
```

might contain:

```js
{
    id: 1,
    name: "Nikitha"
}
```

So:

```js
response
```

and:

```js
response.data
```

are different things.

---

# 31. Why do we return `response` from a response interceptor?

### Answer

We return the response so that it can continue through the Axios Promise chain.

For example:

```js
axiosInstance.interceptors.response.use((response) => {

    console.log(response);

    return response;
});
```

The flow is:

```text
Server
   ↓
Response
   ↓
Axios
   ↓
Response interceptor
   ↓
Inspect / modify
   ↓
return response
   ↓
Application
```

Returning the response passes the resulting value onward.

---

# 32. What is the `error` parameter in a response interceptor?

### Answer

`error` represents the error/rejection information associated with a failed Axios operation.

A conceptual structure may look like:

```js
{
    message: "...",
    response: {
        status: 401,
        data: {...},
        headers: {...}
    }
}
```

When a server response exists, information about that response can often be found inside:

```js
error.response
```

The important point is that the error object contains information about why the Axios operation was rejected.

---

# 33. Explain `error.response.status`.

### Answer

This is normal nested JavaScript property access.

Suppose:

```js
const error = {
    response: {
        status: 401
    }
};
```

Then:

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

First:

```js
error.response
```

accesses the response object inside the error.

Then:

```js
error.response.status
```

accesses the status property inside that response.

So the multiple dots simply represent nested object access.

---

# 34. Why do we use `error.response?.status`?

### Answer

Because `error.response` may not always exist.

If we write:

```js
error.response.status
```

and `error.response` is `undefined`, JavaScript would fail when trying to access `.status`.

Optional chaining:

```js
error.response?.status
```

safely checks whether `response` exists.

Conceptually:

```text
error
  ↓
Does response exist?
  ↓
Yes → access status
No  → return undefined
```

This is useful when handling Axios errors because not every failure necessarily contains an HTTP response.

---

# 35. How would you handle a 401 response globally?

### Answer

We can use a response interceptor:

```js
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
```

The idea is:

```text
API response
     ↓
Response interceptor
     ↓
Is status 401?
     ↓
Yes
     ↓
Remove token
     ↓
Redirect to login
```

This centralizes authentication failure handling instead of repeating it inside every component.

---

# 36. Why do we return `Promise.reject(error)`?

### Answer

If the response interceptor handles an error but we do not want to consider the original API operation successful, we can keep the Promise in the rejected state by returning:

```js
Promise.reject(error);
```

For example:

```js
(error) => {

    if (error.response?.status === 401) {
        sessionStorage.removeItem("token");
    }

    return Promise.reject(error);
}
```

This allows the error to continue through the Promise chain and be handled by the caller if needed.

The important mental model is:

```text
Error occurs
    ↓
Response error interceptor
    ↓
Perform centralized logic
    ↓
Reject error again
    ↓
Caller can still handle the error
```

---

# 37. What is the complete request interceptor flow?

### Answer

The complete conceptual flow is:

```text
React component
      ↓
axiosInstance.get("/users")
      ↓
Axios
      ↓
Request interceptor
      ↓
config received
      ↓
Inspect / modify config
      ↓
return config
      ↓
Axios continues
      ↓
HTTP request
      ↓
Server
```

For authentication:

```text
Component
    ↓
API call
    ↓
Request interceptor
    ↓
Get token
    ↓
Add Authorization header
    ↓
return config
    ↓
Server
```

---

# 38. What is the complete response interceptor flow?

### Answer

The conceptual flow is:

```text
Server
    ↓
HTTP response
    ↓
Axios
    ↓
Response interceptor
    ↓
Success or error path
    ↓
Process response/error
    ↓
Return response OR reject error
    ↓
Application
```

For a successful response:

```text
Server
   ↓
response
   ↓
response interceptor
   ↓
return response
   ↓
component receives result
```

For an error:

```text
Server / request failure
   ↓
error
   ↓
response error interceptor
   ↓
centralized error logic
   ↓
Promise.reject(error)
   ↓
caller can handle error
```

---

# 39. What is the difference between interceptor logic and component logic?

### Answer

Interceptor logic is generally used for **common behavior across many API calls**.

For example:

```text
Authentication token
Common headers
Centralized error handling
Logging
```

Component logic is usually specific to a particular UI or feature.

For example:

```text
Show user profile
Display products
Open modal
Update UI
```

If the same logic needs to happen for many API requests, an interceptor is often a good place for it.

---

# 40. Why should we use an Axios instance with interceptors?

### Answer

An Axios instance allows us to create a configured Axios client for our application.

For example:

```js
const axiosInstance = axios.create({
    baseURL: "https://example.com"
});
```

Then we can configure interceptors specifically on that instance:

```js
axiosInstance.interceptors.request.use(...);

axiosInstance.interceptors.response.use(...);
```

Then API calls use:

```js
axiosInstance.get("/users");
```

This gives us a centralized place for:

- base URL
- request interceptors
- response interceptors
- authentication logic
- common configuration

It also keeps the API configuration separate from UI components.

---

# 41. What is the difference between `axios` and `axiosInstance`?

### Answer

`axios` is the Axios library/API.

An Axios instance is a configured instance created using:

```js
axios.create(...)
```

For example:

```js
const axiosInstance = axios.create({
    baseURL: "https://example.com"
});
```

We can then configure:

```js
axiosInstance.interceptors.request.use(...);
axiosInstance.interceptors.response.use(...);
```

and use:

```js
axiosInstance.get("/users");
```

The advantage is that the instance can have application-specific configuration without repeating it for every request.

---

# 42. Can we have multiple Axios instances?

### Answer

Yes.

We can create different Axios instances with different configurations.

For example, conceptually:

```js
const apiInstance = axios.create({
    baseURL: "https://api.example.com"
});

const authInstance = axios.create({
    baseURL: "https://auth.example.com"
});
```

Each instance can have its own configuration and interceptors.

This is useful when different APIs require different behavior.

---

# 43. Does an interceptor modify the server directly?

### Answer

No.

A request interceptor modifies the request configuration before Axios sends the request.

For example:

```js
config.headers.Authorization = `Bearer ${token}`;
```

This modifies the request configuration being processed by Axios.

Then Axios sends the resulting HTTP request to the server.

The flow is:

```text
Application
    ↓
Axios
    ↓
Interceptor modifies configuration
    ↓
Axios sends HTTP request
    ↓
Server
```

The interceptor itself does not directly modify the server.

---

# 44. Does a response interceptor run before the server sends the response?

### Answer

No.

The server must first produce a response.

The conceptual flow is:

```text
Application
    ↓
Request
    ↓
Server
    ↓
Response
    ↓
Axios
    ↓
Response interceptor
    ↓
Application
```

Therefore, the response interceptor works with the response after Axios receives it and before it continues through the application's Promise chain.

---

# 45. Can a request interceptor modify the request?

### Answer

Yes.

That is one of its main purposes.

For example:

```js
axiosInstance.interceptors.request.use((config) => {

    config.headers["X-Test"] = "Hello";

    return config;
});
```

Or:

```js
config.headers.Authorization = `Bearer ${token}`;
```

The interceptor modifies the request configuration and returns it.

---

# 46. Can a response interceptor modify the response?

### Answer

Yes.

A response interceptor can inspect or transform the response before it continues through the Promise chain.

For example:

```js
axiosInstance.interceptors.response.use((response) => {

    console.log(response);

    return response;
});
```

The important idea is:

```text
Response
   ↓
Interceptor
   ↓
Inspect / modify
   ↓
return response
   ↓
Continue
```

---

# 47. What happens when an API request is made after an interceptor is registered?

### Answer

Suppose we register:

```js
axiosInstance.interceptors.request.use((config) => {
    console.log("Request interceptor");
    return config;
});
```

Then we make:

```js
axiosInstance.get("/users");
```

The conceptual flow is:

```text
axiosInstance.get("/users")
        ↓
Axios begins request processing
        ↓
Registered request interceptor runs
        ↓
Callback receives config
        ↓
Callback returns config
        ↓
Axios continues processing
        ↓
HTTP request is sent
```

When the response arrives:

```text
Server
   ↓
Response
   ↓
Axios
   ↓
Registered response interceptor
   ↓
Application
```

---

# 48. What happens if there are multiple interceptors?

### Answer

Multiple interceptors can participate in the processing chain.

Conceptually:

```text
Request
   ↓
Interceptor 1
   ↓
Interceptor 2
   ↓
Interceptor 3
   ↓
Server
```

Each interceptor can inspect or modify the value and pass it onward.

Similarly, response processing can pass through registered response handlers before reaching the application.

The important mental model is that interceptors form part of Axios's processing chain.

---

# 49. Are interceptors synchronous or asynchronous?

### Answer

Interceptor callbacks can participate in asynchronous processing.

For example, an interceptor can be written as:

```js
axiosInstance.interceptors.request.use(async (config) => {

    const token = await getToken();

    config.headers.Authorization = `Bearer ${token}`;

    return config;
});
```

The important concept is that Axios's request/response processing is Promise-based, so interceptor handlers can participate in that asynchronous flow.

The exact behavior depends on how the interceptor is written.

---

# 50. Can we perform asynchronous work inside an interceptor?

### Answer

Yes.

For example:

```js
axiosInstance.interceptors.request.use(async (config) => {

    const token = await getToken();

    config.headers.Authorization = `Bearer ${token}`;

    return config;
});
```

The interceptor waits for the asynchronous operation before returning the resulting configuration.

The important idea is that the interceptor participates in Axios's asynchronous processing chain.

---

# 51. What is the difference between an interceptor and middleware?

### Answer

They have a similar high-level idea: both allow common logic to participate in a processing flow.

An interceptor specifically refers to Axios's request/response processing mechanism.

For example:

```js
axiosInstance.interceptors.request.use(...)
```

and:

```js
axiosInstance.interceptors.response.use(...)
```

Middleware is a broader concept used in many systems.

So I would say:

> An Axios interceptor is Axios's mechanism for inserting custom logic into its request and response processing flow.

---

# LEVEL 8 — PRACTICAL SCENARIO QUESTIONS

---

# 52. You have 20 API calls and every API needs a token. How would you implement it?

### Answer

I would create a centralized Axios instance and use a request interceptor.

```js
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
```

Then all API calls can simply use:

```js
axiosInstance.get("/users");
axiosInstance.get("/products");
axiosInstance.get("/orders");
```

The token logic is centralized instead of being repeated in every request.

---

# 53. Your application gets a 401 response from many APIs. How would you handle it?

### Answer

I would use a response interceptor.

```js
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
```

The benefit is centralized authentication failure handling.

Instead of checking for `401` in every component, the interceptor handles the common case in one place.

---

# 54. You want to log every API request. Where would you do it?

### Answer

I would use a request interceptor.

```js
axiosInstance.interceptors.request.use((config) => {

    console.log("Method:", config.method);
    console.log("URL:", config.url);

    return config;
});
```

The request interceptor receives `config`, so we can inspect the request information before it continues.

---

# 55. You want to log every API response. Where would you do it?

### Answer

I would use a response interceptor.

```js
axiosInstance.interceptors.response.use((response) => {

    console.log("Status:", response.status);
    console.log("Data:", response.data);

    return response;
});
```

The response interceptor receives the response object, so we can inspect its properties before returning it.

---

# 56. You want to add a common header to every request. What would you use?

### Answer

I would use a request interceptor.

```js
axiosInstance.interceptors.request.use((config) => {

    config.headers["X-App-Version"] = "1.0";

    return config;
});
```

The reason is that headers are part of the request configuration, which is available in the request interceptor.

---

# 57. You want centralized error handling. What would you use?

### Answer

I would use a response interceptor's error callback.

```js
axiosInstance.interceptors.response.use(
    (response) => response,

    (error) => {
        console.log("API error:", error);

        return Promise.reject(error);
    }
);
```

This gives us a centralized place to inspect errors before they continue to the caller.

---

# 58. A developer writes this:

```js
axios.interceptors.request.use((config) => {
    config.headers.Authorization = `Bearer ${token}`;
});
```

What is missing?

### Answer

The interceptor should normally return the configuration:

```js
axios.interceptors.request.use((config) => {

    config.headers.Authorization = `Bearer ${token}`;

    return config;
});
```

The reason is that the resulting configuration needs to continue through Axios's request processing chain.

The mental model is:

```text
Receive config
     ↓
Modify config
     ↓
return config
     ↓
Continue processing
```

---

# 59. A developer writes:

```js
axios.interceptors.request.use((config) => {
    return config;
});
```

Is the interceptor doing anything useful?

### Answer

It is technically registering a request interceptor, but it is not modifying or performing any additional logic.

It simply receives the configuration and returns it unchanged.

For example:

```js
axios.interceptors.request.use((config) => {

    console.log(config);

    return config;
});
```

would at least allow us to inspect the request.

Or:

```js
config.headers.Authorization = `Bearer ${token}`;
return config;
```

would modify it.

So an interceptor does not have to modify the request, but its usefulness comes from the logic we perform during the processing stage.

---

# 60. What happens if the interceptor changes the request URL?

### Answer

A request interceptor can modify request configuration before the request continues.

For example:

```js
axiosInstance.interceptors.request.use((config) => {

    config.url = "/new-url";

    return config;
});
```

The resulting configuration continues through Axios's request processing.

The important concept is:

```text
Original config
     ↓
Interceptor
     ↓
Modify config
     ↓
return config
     ↓
Axios continues
```

---

# LEVEL 9 — TRICKY CONCEPTUAL QUESTIONS

---

# 61. Does an interceptor stop the request permanently?

### Answer

No.

The word "intercept" can be misleading.

Normally, the interceptor temporarily participates in the processing flow.

For example:

```text
Application
   ↓
Axios
   ↓
Interceptor
   ↓
Server
```

The interceptor performs its logic and then returns the appropriate value.

So interception normally means:

> Participate in the flow before allowing processing to continue.

It does not automatically mean permanently stopping the request.

---

# 62. Does an interceptor mean the request failed?

### Answer

No.

An interceptor is not an indication of failure.

A request interceptor can run for a completely successful request.

For example:

```js
axiosInstance.interceptors.request.use((config) => {

    config.headers.Authorization = `Bearer ${token}`;

    return config;
});
```

The request can then successfully reach the server.

The word "intercept" refers to the processing point, not an error condition.

---

# 63. Does the request interceptor receive the server response?

### Answer

No.

The request interceptor receives request configuration.

```js
axios.interceptors.request.use((config) => {
    // config
});
```

The response has not been received yet.

The response interceptor handles the response:

```js
axios.interceptors.response.use((response) => {
    // response
});
```

Therefore:

```text
Request interceptor → request/config
Response interceptor → response
```

---

# 64. Does the response interceptor send the request?

### Answer

No.

The request has already been sent and the response is being processed.

The response flow is:

```text
Server
   ↓
Response
   ↓
Axios
   ↓
Response interceptor
   ↓
Application
```

The response interceptor processes the response; it does not initiate the original request.

---

# 65. Is `config` the same as `response`?

### Answer

No.

They represent different stages.

`config` represents request configuration.

```js
(config) => {}
```

It is used in request processing.

`response` represents the response received from the server.

```js
(response) => {}
```

It is used in response processing.

Conceptually:

```text
config
  ↓
request
  ↓
server
  ↓
response
```

---

# 66. Why can't we use `response.data` inside a request interceptor?

### Answer

Because the response does not exist yet when the request interceptor is running.

The lifecycle is:

```text
Request configuration
       ↓
Request interceptor
       ↓
Request
       ↓
Server
       ↓
Response
```

The request interceptor happens before the server response.

Therefore it receives `config`, not `response`.

---

# 67. Why can't we use `config` as the response object?

### Answer

Because `config` and `response` represent different objects in different stages of processing.

The request interceptor works with:

```js
config
```

The response interceptor works with:

```js
response
```

Conceptually:

```text
config
   ↓
request
   ↓
server
   ↓
response
```

So the data available at each stage is different.

---

# 68. Is `axios.interceptors.response.use()` a special JavaScript syntax?

### Answer

No.

It is built using normal JavaScript concepts:

- objects/API
- property access using `.`
- functions
- method calls
- callbacks
- function arguments

The syntax can be understood as:

```text
axios
   ↓
interceptors
   ↓
response
   ↓
use()
```

So understanding JavaScript property access and functions makes the Axios syntax much easier to understand.

---

# 69. Why does Axios call our callback instead of us calling it?

### Answer

Because Axios knows **when** the relevant request or response reaches that stage.

We register:

```js
axios.interceptors.request.use(myFunction);
```

We do not write:

```js
myFunction();
```

Axios later calls the function when request processing reaches the interceptor.

This is the callback pattern:

```text
Our code
   ↓
Give Axios function
   ↓
Axios remembers function
   ↓
Relevant event/process occurs
   ↓
Axios invokes function
```

---

# 70. What is the mental model you use to understand interceptors?

### Answer

I think of an interceptor as a checkpoint in Axios's processing flow.

For requests:

```text
Application
     ↓
Axios
     ↓
[ Request Interceptor ]
     ↓
Server
```

For responses:

```text
Server
     ↓
Axios
     ↓
[ Response Interceptor ]
     ↓
Application
```

At the checkpoint, Axios gives our callback the relevant information.

For a request:

```js
config
```

For a successful response:

```js
response
```

For a rejected response:

```js
error
```

We can inspect or modify the value and return the appropriate result so processing can continue.

---

# LEVEL 10 — INTERVIEWER FOLLOW-UP QUESTIONS

---

# 71. If an interviewer asks "What exactly happens internally when you register an interceptor?", what would you say?

### Answer

At a conceptual level, when we call:

```js
axios.interceptors.request.use(callback);
```

we are giving Axios a callback function and registering it with its interceptor processing mechanism.

We can imagine a simplified implementation like:

```js
const callbacks = [];

function use(callback) {
    callbacks.push(callback);
}
```

Then later, when a request is processed, Axios invokes the registered callback with the request configuration.

Conceptually:

```text
use(callback)
     ↓
callback registered
     ↓
request occurs
     ↓
Axios invokes callback(config)
     ↓
callback returns config
     ↓
processing continues
```

This is a conceptual model for understanding registration, not Axios's literal source-code implementation.

---

# 72. What does "callback is registered" mean?

### Answer

It means that the function is provided to another system so that the system can invoke it later.

For example:

```js
axios.interceptors.request.use(myCallback);
```

means Axios receives `myCallback`.

We are not executing it immediately.

Axios can later do the equivalent conceptual operation:

```js
myCallback(config);
```

when request processing reaches that point.

This is why the callback is called a callback: we give it now, and another system calls it later.

---

# 73. Can you explain the complete lifecycle from API call to response?

### Answer

Yes.

Suppose the component calls:

```js
axiosInstance.get("/users");
```

The flow is:

```text
1. Component calls axiosInstance.get()
              ↓
2. Axios creates/processes request configuration
              ↓
3. Request interceptors run
              ↓
4. Request interceptor receives config
              ↓
5. We can inspect or modify config
              ↓
6. We return config
              ↓
7. Axios sends HTTP request
              ↓
8. Server processes request
              ↓
9. Server returns response
              ↓
10. Axios receives response
              ↓
11. Response interceptors run
              ↓
12. Success or error callback is used
              ↓
13. Response/error continues through Promise chain
              ↓
14. Application receives result
```

This is the complete mental model I use for Axios interceptors.

---

# 74. How would you explain interceptors to a beginner?

### Answer

I would first explain normal Axios flow:

```text
Application
    ↓
Axios
    ↓
Server
```

Then I would say that sometimes we need to perform common logic for many requests.

For example, every request may need a token.

Instead of modifying every request individually, Axios provides a request interceptor:

```text
Application
    ↓
Axios
    ↓
Request Interceptor
    ↓
Server
```

The interceptor receives the request configuration:

```js
config
```

We can modify it:

```js
config.headers.Authorization = `Bearer ${token}`;
```

and return it:

```js
return config;
```

Similarly, a response interceptor lets us perform common logic when the response comes back.

So an interceptor is basically a controlled point in Axios's request/response processing flow where we can execute common logic.

---

# 75. What are the main benefits of Axios interceptors?

### Answer

The main benefits are:

### 1. Centralization

Common logic can be placed in one location.

### 2. Less duplication

We don't need to repeat authentication or error-handling code in every API call.

### 3. Consistency

All requests using the configured Axios instance can follow the same behavior.

### 4. Cleaner components

Components can focus more on UI and feature-specific logic.

### 5. Centralized error handling

Common errors such as authentication failures can be handled in one place.

### 6. Request/response modification

We can inspect or modify request configurations and responses during Axios processing.

---

# 76. What are some common use cases of request interceptors?

### Answer

Common use cases include:

- adding authentication tokens
- adding common headers
- logging request information
- modifying request configuration
- attaching application-specific information
- performing common request-related logic

For example:

```js
axiosInstance.interceptors.request.use((config) => {

    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    console.log(config.method);
    console.log(config.url);

    return config;
});
```

---

# 77. What are some common use cases of response interceptors?

### Answer

Common use cases include:

- centralized response processing
- logging responses
- inspecting status codes
- centralized error handling
- handling authentication failures
- transforming response information when appropriate

For example:

```js
axiosInstance.interceptors.response.use(
    (response) => {
        console.log(response);
        return response;
    },

    (error) => {
        console.log(error);
        return Promise.reject(error);
    }
);
```

---

# 78. What is the difference between interceptor registration and interceptor execution?

### Answer

These are two different moments.

### Registration

This happens when we write:

```js
axios.interceptors.request.use(callback);
```

We are telling Axios:

> Remember this callback and use it during request processing.

### Execution

Execution happens later when an actual request reaches that interceptor.

Conceptually:

```text
Registration
    ↓
Callback is remembered
    ↓
Time passes
    ↓
API request occurs
    ↓
Callback executes
```

This distinction is important because calling `use()` does not mean the callback is immediately executing for an API request.

---

# 79. Does calling `use()` immediately call our callback?

### Answer

No.

For example:

```js
axios.interceptors.request.use((config) => {
    console.log("Interceptor");
    return config;
});
```

Calling `use()` registers the callback.

The callback executes later when Axios processes a request.

So:

```text
use()
 ↓
register callback
```

not:

```text
use()
 ↓
execute callback immediately
```

---

# 80. What happens if the token does not exist?

### Answer

We can conditionally add the Authorization header.

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

If there is no token:

```text
token = null
```

the `if` condition is false, so the Authorization header is not added.

The configuration is still returned:

```js
return config;
```

and Axios continues processing according to the resulting configuration.

---

# 81. What happens when the server returns a successful response?

### Answer

The successful response goes through the response interceptor's success callback.

For example:

```js
axiosInstance.interceptors.response.use(
    (response) => {
        console.log("Success:", response);
        return response;
    }
);
```

The conceptual flow is:

```text
Server
   ↓
Successful response
   ↓
Axios
   ↓
Response success callback
   ↓
return response
   ↓
Application
```

---

# 82. What happens when the API request fails?

### Answer

The rejected/error path can go through the response interceptor's error callback.

For example:

```js
axiosInstance.interceptors.response.use(
    (response) => response,

    (error) => {
        console.log("Error:", error);

        return Promise.reject(error);
    }
);
```

The flow is:

```text
Request/response failure
       ↓
Error
       ↓
Response error interceptor
       ↓
Centralized error logic
       ↓
Promise.reject(error)
       ↓
Caller can handle it
```

---

# LEVEL 11 — ADVANCED INTERVIEW QUESTIONS

---

# 83. Why are Axios interceptors considered useful for cross-cutting concerns?

### Answer

A cross-cutting concern is logic that applies across many different parts of an application rather than belonging to only one feature.

Authentication is a good example.

Many API requests may need:

```text
Authorization header
```

Instead of putting authentication code into every component, we can put it into one request interceptor.

Similarly, centralized handling of authentication failures can be placed into a response interceptor.

Therefore:

```text
Common concern
      ↓
Interceptor
      ↓
Many API calls
```

This keeps common behavior centralized.

---

# 84. How would you explain interceptors without using the word "middleware"?

### Answer

I would say:

> An Axios interceptor is a callback that Axios executes during its request or response processing flow. It gives us a centralized place to inspect or modify a request before it continues, or inspect/process a response before it reaches the application.

Then I would demonstrate:

```text
Request:

Application
    ↓
Axios
    ↓
Interceptor
    ↓
Server
```

and:

```text
Response:

Server
    ↓
Axios
    ↓
Interceptor
    ↓
Application
```

This directly explains what an interceptor does without relying on another concept.

---

# 85. How would you explain Axios interceptors in one minute?

### Answer

An Axios interceptor is a mechanism that allows us to execute common logic during Axios's request and response processing lifecycle.

There are two main types: request interceptors and response interceptors.

A request interceptor runs during request processing and receives the request configuration, usually represented by `config`. We can use it to add authentication tokens, common headers, logging, or modify the request before it continues.

A response interceptor runs during response processing. It receives the response on the success path and error information on the rejected path. It can be used for centralized response processing and error handling.

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

The main benefit is that common logic is centralized instead of repeated in every API request.

---

# 86. Give a 2–3 minute interview answer for Axios interceptors.

### Answer

An Axios interceptor is a mechanism that allows us to execute common logic during Axios's request and response processing lifecycle.

There are two main types of interceptors: request interceptors and response interceptors.

A request interceptor runs during request processing, before the request continues to the server.

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

Here, `config` represents the request configuration. We can inspect or modify it and then return it so Axios can continue processing the request.

The flow is:

```text
Application
    ↓
Axios
    ↓
Request interceptor
    ↓
Server
```

A common use case is authentication. Instead of adding the Bearer token manually to every API request, we can add it centrally in the request interceptor.

Similarly, Axios provides response interceptors:

```js
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
```

The response interceptor runs when Axios processes the response.

The flow is:

```text
Server
    ↓
Axios
    ↓
Response interceptor
    ↓
Application
```

The first callback handles the successful response path and receives `response`. The second callback handles the error/rejected path and receives `error`.

One important concept is that `use()` registers callback functions. We give Axios the functions, and Axios invokes them later when the appropriate request or response reaches that stage.

So overall, interceptors provide a centralized way to inspect, modify, and handle requests and responses without repeating the same logic throughout the application.

---

# 87. If the interviewer asks "Explain the syntax from scratch", how would you answer?

### Answer

I would break the syntax down into normal JavaScript concepts.

Take:

```js
axios.interceptors.response.use(...)
```

First:

```js
axios
```

represents the Axios API.

Then:

```js
axios.interceptors
```

accesses the interceptor functionality.

Then:

```js
axios.interceptors.response
```

selects the response interceptor.

Then:

```js
axios.interceptors.response.use
```

accesses the `use` method.

Finally:

```js
axios.interceptors.response.use(...)
```

calls the `use` method and passes callback functions.

So conceptually:

```text
axios
   ↓
interceptors
   ↓
response
   ↓
use()
   ↓
register callback(s)
```

The dots are normal JavaScript property access, and the parentheses invoke the method.

---

# 88. If the interviewer asks "How does registration happen?", how would you answer?

### Answer

When we write:

```js
axios.interceptors.request.use(myCallback);
```

we pass the callback function to Axios.

We are not executing it immediately.

Conceptually, we can imagine something like:

```js
const callbacks = [];

function use(callback) {
    callbacks.push(callback);
}
```

So calling:

```js
use(myCallback);
```

can be understood as registering the callback so Axios can remember it.

Later, when a request is processed:

```text
Request occurs
    ↓
Axios finds registered callback
    ↓
Axios invokes callback(config)
    ↓
Callback returns config
    ↓
Axios continues
```

This is a conceptual model rather than Axios's literal internal source code.

---

# 89. If the interviewer asks "Why are the arguments to `use()` callbacks?", what would you say?

### Answer

Because Axios needs to control when those functions execute.

For example:

```js
axios.interceptors.response.use(
    (response) => {
        return response;
    },

    (error) => {
        return Promise.reject(error);
    }
);
```

We give Axios these functions.

Axios decides whether the success or error path is appropriate and invokes the corresponding callback during response processing.

This is the same general callback pattern as:

```js
button.addEventListener("click", handleClick);
```

We provide the function now and another system calls it later.

---

# 90. What are the five most important things you should remember about Axios interceptors?

### Answer

If I had to reduce the entire topic to five concepts, they would be:

### 1. Interceptor = processing point

It gives us a place to execute common logic during Axios request/response processing.

### 2. Request interceptor = `config`

```js
axios.interceptors.request.use((config) => {
    return config;
});
```

It works with request configuration.

### 3. Response interceptor = `response` / `error`

```js
axios.interceptors.response.use(
    (response) => response,
    (error) => Promise.reject(error)
);
```

It handles successful and rejected response processing.

### 4. `use()` = registration

```js
use(callback)
```

means we give Axios a callback to use later.

### 5. Return = continue the chain

```js
return config;
```

or:

```js
return response;
```

passes the resulting value onward through the processing chain.

---

# FINAL INTERVIEW CHEAT SHEET

## Basic Definition

> An Axios interceptor is a mechanism that allows us to execute common logic during Axios's request and response processing lifecycle.

---

## Request Interceptor

```js
axiosInstance.interceptors.request.use((config) => {

    // inspect or modify request

    return config;
});
```

### Remember:

```text
Request interceptor
       ↓
config
       ↓
inspect / modify
       ↓
return config
       ↓
continue
```

---

## Response Interceptor

```js
axiosInstance.interceptors.response.use(
    (response) => {

        // success logic

        return response;
    },

    (error) => {

        // error logic

        return Promise.reject(error);
    }
);
```

### Remember:

```text
Response
    ↓
Success → response callback
    OR
Failure → error callback
```

---

## Syntax Breakdown

```js
axios.interceptors.response.use(...)
```

```text
axios
  ↓
interceptors
  ↓
response
  ↓
use()
  ↓
register callback
```

---

## Callback Concept

```js
use(callback)
```

means:

```text
Give function now
      ↓
Axios remembers it
      ↓
Relevant processing happens later
      ↓
Axios calls function
```

Not:

```js
use(callback());
```

because that would execute the function immediately.

---

## Dot Notation

```js
config.headers.Authorization
```

means:

```text
config
  ↓
headers
  ↓
Authorization
```

And:

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

---

## Request Flow

```text
React Component
      ↓
Axios
      ↓
Request Interceptor
      ↓
config
      ↓
Modify / inspect
      ↓
return config
      ↓
Server
```

---

## Response Flow

```text
Server
      ↓
Response
      ↓
Axios
      ↓
Response Interceptor
      ↓
Success / Error
      ↓
Application
```

---

## Authentication Example

```js
axiosInstance.interceptors.request.use((config) => {

    const token = sessionStorage.getItem("token");

    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }

    return config;
});
```

---

## Centralized 401 Handling

```js
axiosInstance.interceptors.response.use(
    (response) => response,

    (error) => {

        if (error.response?.status === 401) {
            sessionStorage.removeItem("token");
            window.location.href = "/login";
        }

        return Promise.reject(error);
    }
);
```

---

# FINAL MENTAL MODEL

Do not memorize the entire Axios interceptor API as isolated syntax.

Understand the flow:

```text
                 REQUEST
                    ↓
Application → Axios → Request Interceptor → Server
                         ↓
                       config
                         ↓
                  inspect / modify
                         ↓
                    return config


                 RESPONSE
                    ↓
Server → Axios → Response Interceptor → Application
                         ↓
                  response / error
                         ↓
                  inspect / modify
                         ↓
             return / reject
```

And understand the JavaScript behind the syntax:

```text
.       → property access

()      → function invocation

use()   → method call

callback
        → function passed as a value

register
        → give Axios the function so it can call it later

config
        → request configuration

response
        → response object

error
        → rejected/error information

return
        → pass the resulting value to the next stage
```

If these concepts are clear, Axios interceptor syntax becomes much easier to understand instead of something that has to be memorized.