# Chapter 6 – React API Integration and Data Fetching: Interview Questions and Answers

> Comprehensive beginner-to-advanced interview preparation. Answers are phrased as responses you can give directly to an interviewer. Adapt examples to the backend contract and libraries used in the project.

## Table of Contents

1. [What is API integration in React?](#q1-what-is-api-integration-in-react)
2. [What is an API?](#q2-what-is-an-api)
3. [What is an endpoint?](#q3-what-is-an-endpoint)
4. [Explain the request-response cycle.](#q4-explain-the-request-response-cycle)
5. [What is HTTP?](#q5-what-is-http)
6. [What is the difference between HTTP and HTTPS?](#q6-what-is-the-difference-between-http-and-https)
7. [What are HTTP methods?](#q7-what-are-http-methods)
8. [GET vs POST?](#q8-get-vs-post)
9. [PUT vs PATCH?](#q9-put-vs-patch)
10. [What is the difference between PUT and POST?](#q10-what-is-the-difference-between-put-and-post)
11. [What are common HTTP status codes?](#q11-what-are-common-http-status-codes)
12. [What is the difference between 401 and 403?](#q12-what-is-the-difference-between-401-and-403)
13. [What are request headers?](#q13-what-are-request-headers)
14. [What is a request body?](#q14-what-is-a-request-body)
15. [What is JSON?](#q15-what-is-json)
16. [What is the Fetch API?](#q16-what-is-the-fetch-api)
17. [Does Fetch reject for 404 or 500 responses?](#q17-does-fetch-reject-for-404-or-500-responses)
18. [What does response.ok mean?](#q18-what-does-responseok-mean)
19. [What is the difference between response.json() and JSON.stringify()?](#q19-what-is-the-difference-between-responsejson-and-jsonstringify)
20. [What is a Promise?](#q20-what-is-a-promise)
21. [What is async/await?](#q21-what-is-asyncawait)
22. [Why do we use try/catch with API calls?](#q22-why-do-we-use-trycatch-with-api-calls)
23. [How do you make a GET request in React?](#q23-how-do-you-make-a-get-request-in-react)
24. [How do you make a POST request?](#q24-how-do-you-make-a-post-request)
25. [How do you make a DELETE request?](#q25-how-do-you-make-a-delete-request)
26. [How do you handle loading, error and success states?](#q26-how-do-you-handle-loading-error-and-success-states)
27. [Why is API fetching commonly done inside useEffect?](#q27-why-is-api-fetching-commonly-done-inside-useeffect)
28. [Can we make useEffect async?](#q28-can-we-make-useeffect-async)
29. [What is a race condition in data fetching?](#q29-what-is-a-race-condition-in-data-fetching)
30. [How do you cancel a Fetch request?](#q30-how-do-you-cancel-a-fetch-request)
31. [What is Axios?](#q31-what-is-axios)
32. [Why do developers use Axios?](#q32-why-do-developers-use-axios)
33. [Fetch vs Axios?](#q33-fetch-vs-axios)
34. [How do you install Axios?](#q34-how-do-you-install-axios)
35. [What is an Axios instance?](#q35-what-is-an-axios-instance)
36. [What is baseURL?](#q36-what-is-baseurl)
37. [What is an interceptor?](#q37-what-is-an-interceptor)
38. [What is a request interceptor?](#q38-what-is-a-request-interceptor)
39. [What is a response interceptor?](#q39-what-is-a-response-interceptor)
40. [How do you attach a token using an interceptor?](#q40-how-do-you-attach-a-token-using-an-interceptor)
41. [What is a Bearer token?](#q41-what-is-a-bearer-token)
42. [Where should authentication tokens be stored?](#q42-where-should-authentication-tokens-be-stored)
43. [What happens when an access token expires?](#q43-what-happens-when-an-access-token-expires)
44. [What is a refresh token?](#q44-what-is-a-refresh-token)
45. [What are environment variables in React/Vite?](#q45-what-are-environment-variables-in-reactvite)
46. [Why should API secrets not be stored in frontend environment variables?](#q46-why-should-api-secrets-not-be-stored-in-frontend-environment-variables)
47. [What is CORS?](#q47-what-is-cors)
48. [What is a preflight request?](#q48-what-is-a-preflight-request)
49. [Can React fix a CORS error?](#q49-can-react-fix-a-cors-error)
50. [What are query parameters?](#q50-what-are-query-parameters)
51. [Path parameters vs query parameters?](#q51-path-parameters-vs-query-parameters)
52. [How do you implement pagination?](#q52-how-do-you-implement-pagination)
53. [What is debouncing?](#q53-what-is-debouncing)
54. [Debouncing vs throttling?](#q54-debouncing-vs-throttling)
55. [How do you make parallel API requests?](#q55-how-do-you-make-parallel-api-requests)
56. [Promise.all vs Promise.allSettled?](#q56-promiseall-vs-promiseallsettled)
57. [What is a dependent API request?](#q57-what-is-a-dependent-api-request)
58. [How do you upload a file through an API?](#q58-how-do-you-upload-a-file-through-an-api)
59. [What is FormData?](#q59-what-is-formdata)
60. [How do you handle API errors in Axios?](#q60-how-do-you-handle-api-errors-in-axios)
61. [What is a reusable API service layer?](#q61-what-is-a-reusable-api-service-layer)
62. [What is a custom data-fetching hook?](#q62-what-is-a-custom-data-fetching-hook)
63. [Does a custom hook automatically cache API data?](#q63-does-a-custom-hook-automatically-cache-api-data)
64. [What is server state?](#q64-what-is-server-state)
65. [What is TanStack Query?](#q65-what-is-tanstack-query)
66. [What is SWR?](#q66-what-is-swr)
67. [What is cache invalidation?](#q67-what-is-cache-invalidation)
68. [What is an optimistic update?](#q68-what-is-an-optimistic-update)
69. [How do React 19 Actions help with API mutations?](#q69-how-do-react-19-actions-help-with-api-mutations)
70. [How do you prevent duplicate form submissions?](#q70-how-do-you-prevent-duplicate-form-submissions)
71. [How do you debug a failed API request?](#q71-how-do-you-debug-a-failed-api-request)
72. [What are the most common API integration mistakes?](#q72-what-are-the-most-common-api-integration-mistakes)
73. [How do you secure API integration in a React app?](#q73-how-do-you-secure-api-integration-in-a-react-app)
74. [How do you test API integration?](#q74-how-do-you-test-api-integration)
75. [How would you design API integration for a production React app?](#q75-how-would-you-design-api-integration-for-a-production-react-app)
76. [Scenario: API returns 200 but the UI is empty. What would you check?](#q76-scenario-api-returns-200-but-the-ui-is-empty-what-would-you-check)
77. [Scenario: A request works in Postman but fails in the browser. Why?](#q77-scenario-a-request-works-in-postman-but-fails-in-the-browser-why)
78. [Scenario: Search results appear in the wrong order. How would you fix it?](#q78-scenario-search-results-appear-in-the-wrong-order-how-would-you-fix-it)
79. [Scenario: The API returns 204 and JSON parsing fails. Why?](#q79-scenario-the-api-returns-204-and-json-parsing-fails-why)
80. [Scenario: Multiple components request the same data. How would you avoid duplicate calls?](#q80-scenario-multiple-components-request-the-same-data-how-would-you-avoid-duplicate-calls)
81. [Scenario: A token expires while several requests are running. What should happen?](#q81-scenario-a-token-expires-while-several-requests-are-running-what-should-happen)
82. [Scenario: A user clicks Save repeatedly. How would you handle it?](#q82-scenario-a-user-clicks-save-repeatedly-how-would-you-handle-it)
83. [Scenario: A file upload fails. What would you inspect?](#q83-scenario-a-file-upload-fails-what-would-you-inspect)
84. [Scenario: The server returns 500. What should the UI do?](#q84-scenario-the-server-returns-500-what-should-the-ui-do)
85. [Scenario: How would you implement retry behavior?](#q85-scenario-how-would-you-implement-retry-behavior)
86. [Scenario: How would you handle a very large API response?](#q86-scenario-how-would-you-handle-a-very-large-api-response)
87. [Scenario: How would you keep a list updated after creating an item?](#q87-scenario-how-would-you-keep-a-list-updated-after-creating-an-item)
88. [Scenario: How would you handle offline or slow network conditions?](#q88-scenario-how-would-you-handle-offline-or-slow-network-conditions)
89. [What is the difference between client state and server state?](#q89-what-is-the-difference-between-client-state-and-server-state)
90. [What is the difference between authentication and authorization in API requests?](#q90-what-is-the-difference-between-authentication-and-authorization-in-api-requests)
91. [Why should frontend validation not replace backend validation?](#q91-why-should-frontend-validation-not-replace-backend-validation)
92. [What is idempotency and why does it matter for API requests?](#q92-what-is-idempotency-and-why-does-it-matter-for-api-requests)
93. [How do you avoid memory leaks or unnecessary work during fetching?](#q93-how-do-you-avoid-memory-leaks-or-unnecessary-work-during-fetching)
94. [What is the difference between polling and refetching on focus?](#q94-what-is-the-difference-between-polling-and-refetching-on-focus)
95. [How do you handle partial failure when loading a dashboard?](#q95-how-do-you-handle-partial-failure-when-loading-a-dashboard)
96. [How would you explain your API integration approach in a project?](#q96-how-would-you-explain-your-api-integration-approach-in-a-project)

---

## Q1. What is API integration in React?

**Answer:** “API integration in React is the process of connecting the frontend to a backend service through HTTP requests. I use Fetch or Axios to send requests, receive data, handle loading and error states, and update React state so the UI displays the latest server data.”

## Q2. What is an API?

**Answer:** “An API, or Application Programming Interface, is a defined way for two software systems to communicate. In a React application, the frontend usually communicates with backend APIs to retrieve or modify application data.”

## Q3. What is an endpoint?

**Answer:** “An endpoint is a specific URL exposed by an API for a particular resource or operation. For example, `/api/users` may return a list of users, while `/api/users/10` may return the user whose ID is 10.”

## Q4. Explain the request-response cycle.

**Answer:** “The React application sends an HTTP request to a backend endpoint. The server receives it, validates the request, performs the required operation and sends an HTTP response. The frontend checks the response, handles errors if necessary, updates state and React re-renders the UI.”

## Q5. What is HTTP?

**Answer:** “HTTP stands for Hypertext Transfer Protocol. It is a request-response protocol used for communication between clients and servers. A request contains details such as the method, URL, headers and optional body, while the response contains a status code, headers and optional data.”

## Q6. What is the difference between HTTP and HTTPS?

**Answer:** “HTTPS is HTTP secured using TLS encryption. It protects data while it travels between the client and server, helps verify the server's identity and protects against network tampering. Production applications should use HTTPS, especially for authentication and personal data.”

## Q7. What are HTTP methods?

**Answer:** “HTTP methods describe the operation the client wants to perform. GET retrieves data, POST submits data or creates a resource, PUT replaces a resource, PATCH partially updates it, and DELETE removes a resource.”

## Q8. GET vs POST?

**Answer:** “GET is used to retrieve data and its parameters are commonly placed in the URL. POST sends data in the request body and is commonly used to create resources or trigger operations. GET is intended to be safe and read-only, while POST can change server state.”

## Q9. PUT vs PATCH?

**Answer:** “PUT is generally used to replace the complete representation of a resource, whereas PATCH is used to apply a partial update. For example, if I only want to change a user's role, PATCH is usually more suitable. The exact behavior still depends on the API contract.”

## Q10. What is the difference between PUT and POST?

**Answer:** “POST is commonly used to submit data or create a resource where the server determines the new resource identifier. PUT generally targets a known resource URI and replaces its representation. PUT is defined as idempotent, while POST is not necessarily idempotent.”

## Q11. What are common HTTP status codes?

**Answer:** “200 means the request succeeded, 201 means a resource was created, and 204 means success with no response body. 400 indicates a bad request, 401 means authentication is missing or invalid, 403 means access is forbidden, 404 means the resource was not found, and 500 indicates an internal server error.”

## Q12. What is the difference between 401 and 403?

**Answer:** “401 means the request does not have valid authentication credentials. 403 means the server understood the identity or request but refuses to authorize the operation. In simple terms, 401 is an authentication problem and 403 is an access-permission problem.”

## Q13. What are request headers?

**Answer:** “Request headers are metadata sent with an HTTP request. They can specify the content type, accepted response format, authorization credentials, caching preferences and other request information. For example, `Content-Type: application/json` tells the server that the request body is JSON.”

## Q14. What is a request body?

**Answer:** “The request body contains the data sent to the server, commonly with POST, PUT or PATCH. For example, when registering a user, the body may contain the name, email and password. The backend must validate that data before processing it.”

## Q15. What is JSON?

**Answer:** “JSON stands for JavaScript Object Notation. It is a lightweight text format used to exchange structured data. With Fetch, I commonly use `JSON.stringify()` to send an object and `response.json()` to parse a JSON response.”

## Q16. What is the Fetch API?

**Answer:** “Fetch is a browser-provided API for making HTTP requests. It returns a Promise that resolves to a Response object. I can use methods such as GET and POST, inspect the response status, and parse the response body.”

## Q17. Does Fetch reject for 404 or 500 responses?

**Answer:** “No. Fetch normally resolves even when the server returns an HTTP error such as 404 or 500. It rejects for network-level failures and aborts. Therefore, I check `response.ok` or the status code and throw an error when the response is not successful.”

## Q18. What does response.ok mean?

**Answer:** “`response.ok` is a boolean property that is true when the HTTP status is in the successful 200–299 range. It is a convenient way to check whether a Fetch request received a successful HTTP response.”

## Q19. What is the difference between response.json() and JSON.stringify()?

**Answer:** “`JSON.stringify()` converts a JavaScript value into a JSON string, which is useful when sending data in a Fetch request body. `response.json()` reads the response body and parses JSON text into a JavaScript value.”

## Q20. What is a Promise?

**Answer:** “A Promise represents the eventual completion or failure of an asynchronous operation. It has three states: pending, fulfilled and rejected. API requests return Promises because the response may arrive later.”

## Q21. What is async/await?

**Answer:** “Async/await is syntax for writing Promise-based code in a more readable way. An async function always returns a Promise, and await pauses execution inside that function until the awaited Promise settles. It does not block the browser's main thread.”

## Q22. Why do we use try/catch with API calls?

**Answer:** “I use try/catch to handle rejected Promises and unexpected errors during a request. It lets me show a useful error message, log diagnostic information and keep the application from failing silently. I also make sure errors are rethrown or returned when the calling code needs to know the operation failed.”

## Q23. How do you make a GET request in React?

**Answer:** “I can use Fetch or Axios. For data needed when a component appears, I commonly trigger the request from an effect or use a data-fetching library. I store the response in state and render loading, error, empty and success states.”

```jsx
useEffect(() => {
  async function load() {
    const response = await fetch("/api/users");
    if (!response.ok) throw new Error("Failed to load");
    setUsers(await response.json());
  }
  load().catch((error) => setError(error.message));
}, []);
```

## Q24. How do you make a POST request?

**Answer:** “I send the data in the request body and specify the content type when using Fetch. With Axios, I can pass a JavaScript object directly as the POST data. I handle the response and display success or validation errors to the user.”

```js
await axios.post("/api/users", { name: "Asha" });
```

## Q25. How do you make a DELETE request?

**Answer:** “I call the resource endpoint with the DELETE method, usually including the resource ID in the URL. If the request succeeds, I remove that item from the UI or refresh the relevant data. I also handle failures so the UI does not incorrectly show that deletion succeeded.”

## Q26. How do you handle loading, error and success states?

**Answer:** “I maintain state for the request lifecycle. Before starting, I set loading to true and clear any old error. On success, I store the returned data. In catch, I set a useful error message, and in finally I stop the loading indicator. I also handle empty results separately from errors.”

## Q27. Why is API fetching commonly done inside useEffect?

**Answer:** “When data should be loaded because a component is displayed or a dependency changes, useEffect lets me synchronize the component with the external API. It runs after rendering and supports cleanup. However, I can also fetch in event handlers, route loaders or a query library when those better match the use case.”

## Q28. Can we make useEffect async?

**Answer:** “The effect callback itself should not be async because React expects it to return either nothing or a cleanup function, not a Promise. I define an async function inside the effect and call it. That function can perform the request and handle errors.”

## Q29. What is a race condition in data fetching?

**Answer:** “A race condition can happen when multiple requests are running and they finish in a different order from the order in which they started. An older response might overwrite newer data. I prevent this by aborting obsolete requests or ensuring only the latest request can update state.”

## Q30. How do you cancel a Fetch request?

**Answer:** “I create an AbortController, pass its signal to Fetch, and call `abort()` when the request is no longer needed, such as during effect cleanup. I check for an AbortError so cancellation is not displayed as an ordinary failure.”

## Q31. What is Axios?

**Answer:** “Axios is a Promise-based HTTP client used to make requests from browser or Node.js applications. It provides convenient methods for HTTP operations, automatic JSON handling in common cases, interceptors, instances, timeout configuration and cancellation support.”

## Q32. Why do developers use Axios?

**Answer:** “Developers use Axios because it makes request configuration convenient and provides features such as instances, base URLs, interceptors, timeout options and consistent error handling for non-success HTTP responses. These features are useful when an application has many API endpoints.”

## Q33. Fetch vs Axios?

**Answer:** “Fetch is built into modern browsers, so it does not require an additional package. However, I must check `response.ok` and parse the body manually. Axios is an external library that provides instances and interceptors and rejects non-2xx responses by default. I choose based on the application's needs.”

## Q34. How do you install Axios?

**Answer:** “I install Axios using `npm install axios`, then import it in the file where I need to make requests. In a larger application, I usually create a shared Axios instance rather than configuring every request separately.”

## Q35. What is an Axios instance?

**Answer:** “An Axios instance is a separately configured Axios client created using `axios.create()`. I use it to define common settings such as base URL, timeout and headers, and to attach interceptors. This avoids repeating configuration in every API call.”

## Q36. What is baseURL?

**Answer:** “`baseURL` is the common starting URL for requests made through an Axios instance. If the base URL is `https://api.example.com` and I request `/users`, Axios combines them to form the full endpoint URL.”

## Q37. What is an interceptor?

**Answer:** “An interceptor is a function that runs before a request is sent or after a response is received. Axios provides request and response interceptors. They help centralize cross-cutting logic such as attaching authentication headers or normalizing errors.”

## Q38. What is a request interceptor?

**Answer:** “A request interceptor runs before the HTTP request is sent. A common use case is reading an access token and adding it to the Authorization header. It can also add common headers or request metadata.”

## Q39. What is a response interceptor?

**Answer:** “A response interceptor runs when a response arrives or when the request fails. I can use it to centralize response handling, normalize errors or coordinate authentication-expiry behavior. I make sure rejected errors are propagated so the component can handle them.”

## Q40. How do you attach a token using an interceptor?

**Answer:** “I register a request interceptor on the shared Axios instance. Inside it, I read the token using the storage or authentication mechanism selected by the application, add `Authorization: Bearer <token>` to the request headers, and return the updated config.”

```js
axiosInstance.interceptors.request.use((config) => {
  const token = sessionStorage.getItem("accessToken");
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

## Q41. What is a Bearer token?

**Answer:** “A Bearer token is an access credential. The client sends it in the Authorization header using the format `Bearer token-value`. The backend validates it and uses its claims or associated session to determine the request's identity and permissions.”

## Q42. Where should authentication tokens be stored?

**Answer:** “It depends on the security architecture. Local storage and session storage are accessible to JavaScript, so an XSS vulnerability can expose tokens stored there. Secure HttpOnly cookies prevent JavaScript access but require appropriate CSRF protections. I follow the backend's authentication design and avoid claiming that one storage choice is universally secure.”

## Q43. What happens when an access token expires?

**Answer:** “The backend may reject a request with 401. If the application supports token refresh, the frontend can use the refresh mechanism to obtain a new access token and retry the original request once. If refresh fails, it should clear the session state and ask the user to sign in again.”

## Q44. What is a refresh token?

**Answer:** “A refresh token is a credential used to obtain a new access token after the current access token expires. It is generally longer-lived and must be protected carefully. Its storage, rotation and revocation should be designed by the backend authentication system.”

## Q45. What are environment variables in React/Vite?

**Answer:** “Environment variables let us configure values such as an API base URL without hardcoding them in multiple files. In Vite, client-side variables are exposed through `import.meta.env` when their names use the `VITE_` prefix. I restart the development server after changing them.”

## Q46. Why should API secrets not be stored in frontend environment variables?

**Answer:** “Frontend environment variables are bundled into client-side code and can be inspected by users. Therefore, they are not secret. Private API keys, database credentials and server secrets must remain on the backend.”

## Q47. What is CORS?

**Answer:** “CORS stands for Cross-Origin Resource Sharing. It is a browser security mechanism that controls whether a web page from one origin can access resources from another origin. The backend must return the appropriate CORS headers to allow the frontend origin.”

## Q48. What is a preflight request?

**Answer:** “A preflight request is an OPTIONS request sent by the browser before certain cross-origin requests. It asks the server whether the actual method and headers are allowed. The server must respond with the appropriate CORS permissions before the browser sends the actual request.”

## Q49. Can React fix a CORS error?

**Answer:** “No. CORS permission is controlled by the server's response headers. I can verify the frontend request and origin, but the backend or proxy configuration must allow the required origin, methods and headers. Adding random headers in React does not solve a server-side CORS configuration problem.”

## Q50. What are query parameters?

**Answer:** “Query parameters are key-value pairs appended to a URL after a question mark. They are commonly used for search, filtering, sorting and pagination. For example, `/products?page=2&limit=10` requests a particular page of products.”

## Q51. Path parameters vs query parameters?

**Answer:** “Path parameters identify a particular resource, such as `/users/25`. Query parameters modify or describe the requested result, such as `/users?role=admin&page=2`. The backend API design defines how each parameter is interpreted.”

## Q52. How do you implement pagination?

**Answer:** “I keep track of the current page and page size, send them as query parameters, and render the records returned by the server. I also use pagination metadata such as total records or next-page information when the API provides it. For large or frequently changing datasets, cursor pagination may be more suitable than page numbers.”

## Q53. What is debouncing?

**Answer:** “Debouncing delays a function until a specified period has passed without another call. In search, I use it to wait until the user stops typing before sending an API request. This reduces unnecessary network calls.”

## Q54. Debouncing vs throttling?

**Answer:** “Debouncing waits until calls stop for a specified period before running once. Throttling limits execution to at most once during a specified interval. I commonly use debounce for search input and throttle for frequent events such as scrolling.”

## Q55. How do you make parallel API requests?

**Answer:** “When requests are independent, I use `Promise.all()` to start them together and wait for all of them. This can reduce total waiting time compared with making each request sequentially.”

```js
const [users, courses] = await Promise.all([
  axios.get("/api/users"),
  axios.get("/api/courses"),
]);
```

## Q56. Promise.all vs Promise.allSettled?

**Answer:** “Promise.all fulfills only when every input Promise fulfills and rejects as soon as one rejects. Promise.allSettled waits for every Promise to settle and gives me each result's status. I use allSettled when partial results are useful even if one request fails.”

## Q57. What is a dependent API request?

**Answer:** “A dependent request needs information returned by an earlier request. For example, I may first fetch a user's profile and then use the user ID from that response to fetch their orders. I await the first request before starting the second.”

## Q58. How do you upload a file through an API?

**Answer:** “I create a FormData object, append the selected file and send it to the upload endpoint. I validate file size and type on the frontend for user experience, but the backend must validate the file independently for security.”

## Q59. What is FormData?

**Answer:** “FormData is a browser API that builds key-value form data, commonly used for multipart file uploads. I can append files and text fields and send the FormData object in a Fetch or Axios request.”

## Q60. How do you handle API errors in Axios?

**Answer:** “I inspect the Axios error. If `error.response` exists, the server returned a response and I can inspect its status and data. If `error.request` exists but there is no response, the request was sent but no response arrived. Otherwise, the problem may have occurred while configuring the request. I map these cases to safe, user-friendly messages.”

## Q61. What is a reusable API service layer?

**Answer:** “A service layer keeps endpoint functions and HTTP logic separate from UI components. For example, a `userApi` module can expose `getUsers`, `createUser`, `updateUser` and `deleteUser`. Components call these functions without repeating URLs and request configuration.”

## Q62. What is a custom data-fetching hook?

**Answer:** “A custom data-fetching hook is a reusable function, usually beginning with `use`, that combines request logic with React state and effects. It can expose data, loading and error values to components. It helps avoid repeating the same fetching logic in multiple components.”

## Q63. Does a custom hook automatically cache API data?

**Answer:** “No. A custom hook reuses logic, but each component calling it can still create its own state and request. Caching and request deduplication require additional shared state or a data-fetching library such as TanStack Query or SWR.”

## Q64. What is server state?

**Answer:** “Server state is data owned by a backend, such as users, products or orders. The frontend fetches a copy and must deal with loading, caching, synchronization and possible changes on the server. It differs from local UI state, such as whether a modal is open.”

## Q65. What is TanStack Query?

**Answer:** “TanStack Query is a library for managing asynchronous server state. It provides caching, request deduplication, background refetching, mutations and cache invalidation. It reduces the amount of manual fetching and synchronization logic we need to write.”

## Q66. What is SWR?

**Answer:** “SWR is a React data-fetching library built around the stale-while-revalidate strategy. It can return cached data quickly and revalidate it in the background. It also provides features such as caching, focus revalidation and request deduplication.”

## Q67. What is cache invalidation?

**Answer:** “Cache invalidation marks cached data as stale or removes it when it may no longer be correct. For example, after creating a user, I invalidate the users query so the list can fetch or display updated data.”

## Q68. What is an optimistic update?

**Answer:** “An optimistic update updates the UI before the server confirms the operation. It makes the interface feel faster. I preserve the previous data so I can roll back if the request fails, and then reconcile the cache with the server response.”

## Q69. How do React 19 Actions help with API mutations?

**Answer:** “React 19 Actions provide a way to handle form submissions and mutations. With `useActionState`, I can manage an action's result and pending state, while `useOptimistic` can show an expected result before the server confirms it. They improve the UI workflow but do not replace backend validation or authorization.”

## Q70. How do you prevent duplicate form submissions?

**Answer:** “I track the pending state and disable the submit button while the request is running. I can also prevent repeated submissions in the handler. For important operations, the backend should support idempotency or duplicate protection because frontend disabling alone is not a reliable guarantee.”

## Q71. How do you debug a failed API request?

**Answer:** “I open the browser's Network tab and inspect the request URL, method, headers, payload, status and response body. I compare them with the API documentation, check the Console for frontend errors, and inspect backend logs if the request reached the server. I also verify the token, base URL and CORS configuration.”

## Q72. What are the most common API integration mistakes?

**Answer:** “Common mistakes include not checking Fetch's response status, forgetting to parse or stringify JSON, missing effect dependencies, not handling loading and error states, allowing stale requests to update state, repeating API configuration in components, exposing secrets in frontend code and relying only on frontend authorization.”

## Q73. How do you secure API integration in a React app?

**Answer:** “I use HTTPS, avoid exposing secrets in the frontend, protect authentication credentials, validate input on the backend and enforce authorization on every protected endpoint. I also handle XSS and CSRF risks according to the authentication design, validate uploads on the server and avoid logging sensitive information.”

## Q74. How do you test API integration?

**Answer:** “I test service functions with mocked responses and test components for loading, success, empty and error states. I include cases such as 401, 500 and network failures. Tools such as Mock Service Worker let me mock requests at the network level, and end-to-end tests cover important user journeys.”

## Q75. How would you design API integration for a production React app?

**Answer:** “I would create a shared HTTP client with a configured base URL and timeout, separate endpoint functions into API service modules, and use custom hooks or a server-state library for data. I would centralize authentication and error handling carefully, support cancellation and pagination, and ensure every screen has loading, empty and error states. I would also test critical flows and keep authorization and validation on the backend.”

## Q76. Scenario: API returns 200 but the UI is empty. What would you check?

**Answer:** “First, I would inspect the response body in the Network tab to confirm its structure. Then I would verify that the code is reading the correct property, that state is being updated, and that the component maps the correct array. I would also check filters, conditional rendering and the browser Console.”

## Q77. Scenario: A request works in Postman but fails in the browser. Why?

**Answer:** “I would first check CORS because Postman is not restricted by browser CORS enforcement in the same way. I would then compare the URL, headers, authorization token, cookies, method and payload. Browser cookie policies, preflight requests and environment-specific URLs can also cause differences.”

## Q78. Scenario: Search results appear in the wrong order. How would you fix it?

**Answer:** “I would check whether older requests are completing after newer ones and overwriting the latest results. I would cancel obsolete requests with AbortController or track the latest request so only its result updates state. I would also verify that the backend sorting parameters are correct.”

## Q79. Scenario: The API returns 204 and JSON parsing fails. Why?

**Answer:** “A 204 response means the request succeeded but the server returned no content. Calling `response.json()` on an empty body can fail. I would check the status first and skip JSON parsing for 204 responses.”

## Q80. Scenario: Multiple components request the same data. How would you avoid duplicate calls?

**Answer:** “I would consider moving the request into a shared data layer or using a server-state library such as TanStack Query or SWR. These libraries can cache and deduplicate requests using query keys. A custom hook alone does not automatically provide shared caching.”

## Q81. Scenario: A token expires while several requests are running. What should happen?

**Answer:** “If the backend supports refresh tokens, I would coordinate a single refresh operation and let the failed requests wait for it. After a successful refresh, I would retry eligible requests once. If refresh fails, I would clear the authentication session and require login. I would also prevent infinite retry loops.”

## Q82. Scenario: A user clicks Save repeatedly. How would you handle it?

**Answer:** “I would set a pending state immediately and disable the Save button while the request is in progress. I would also protect the handler against duplicate submissions. For operations where duplicates would cause problems, I would use backend idempotency or duplicate detection.”

## Q83. Scenario: A file upload fails. What would you inspect?

**Answer:** “I would inspect the request payload, FormData field name, file size and type, endpoint, authorization and server response. I would verify that the client is not incorrectly setting the multipart boundary and check server upload limits. I would also make sure the backend validates the file securely.”

## Q84. Scenario: The server returns 500. What should the UI do?

**Answer:** “I would show a safe message such as ‘Something went wrong. Please try again later,’ preserve user input where possible, and log diagnostic information without exposing sensitive details. I would not automatically retry every operation, especially mutations that might have partially completed.”

## Q85. Scenario: How would you implement retry behavior?

**Answer:** “I would retry only appropriate failures, such as selected network errors or temporary server responses, with a limited retry count and backoff. I would respect `Retry-After` when provided. I would avoid blindly retrying non-idempotent operations such as a payment or create request unless the backend supports idempotency.”

## Q86. Scenario: How would you handle a very large API response?

**Answer:** “I would first see whether the backend can provide pagination, filtering or smaller fields. I would avoid rendering thousands of records at once and consider virtualization for long lists. I would also use caching and request only the data required for the current screen.”

## Q87. Scenario: How would you keep a list updated after creating an item?

**Answer:** “After the create request succeeds, I can append the returned resource to local state if the response contains the created record. If I use a query library, I can update the cache or invalidate the list query. I would use the server response as the source of truth rather than assuming the submitted payload exactly matches the stored record.”

## Q88. Scenario: How would you handle offline or slow network conditions?

**Answer:** “I would show loading and connection-related feedback, set sensible timeouts, cancel obsolete requests and avoid duplicate submissions. If offline support is required, I would design an explicit queue or cached-read strategy and reconcile changes when connectivity returns. I would not assume a failed request means the server did not process it.”

## Q89. What is the difference between client state and server state?

**Answer:** “Client state is owned by the frontend, such as whether a dropdown is open or which tab is selected. Server state is owned by the backend, such as a user's profile or product list. Server state needs synchronization, caching and refetching because it can change outside the current component.”

## Q90. What is the difference between authentication and authorization in API requests?

**Answer:** “Authentication verifies who the user is. Authorization determines what that authenticated user is allowed to do. A valid token may authenticate a user, but the backend must still check whether that user has permission to access a particular resource or perform an operation.”

## Q91. Why should frontend validation not replace backend validation?

**Answer:** “Frontend validation improves user experience by catching mistakes early, but users can bypass or modify frontend code and send requests directly. The backend must independently validate all input and enforce business rules and permissions before changing data.”

## Q92. What is idempotency and why does it matter for API requests?

**Answer:** “An operation is idempotent if repeating the same request has the same intended effect as making it once. GET, PUT and DELETE are defined as idempotent in HTTP semantics, while POST is not necessarily idempotent. Idempotency matters when handling retries because repeating a create or payment request could otherwise create duplicates or charge twice.”

## Q93. How do you avoid memory leaks or unnecessary work during fetching?

**Answer:** “I clean up effects by aborting requests that are no longer needed or by ignoring their results. I clear debounce timers and avoid allowing obsolete responses to update state. With a data-fetching library, I use its cancellation and lifecycle features where appropriate.”

## Q94. What is the difference between polling and refetching on focus?

**Answer:** “Polling means requesting updated data at a regular interval. Refetching on focus means requesting data when the user returns to or focuses the application window. Polling provides periodic updates, while focus refetching updates data based on user activity and can reduce unnecessary requests.”

## Q95. How do you handle partial failure when loading a dashboard?

**Answer:** “If dashboard sections are independent, I can load them in parallel with `Promise.allSettled()` so one failed endpoint does not prevent the others from displaying. I would show an error state for the failed section and keep successful sections available. If the sections depend on each other, I would handle them in the required sequence.”

## Q96. How would you explain your API integration approach in a project?

**Answer:** “I separate API logic from components by creating a shared Axios instance and service modules for each resource. I use React state or a data-fetching library to manage the request lifecycle, including loading, success and error states. For authenticated endpoints, I attach credentials according to the application's authentication design. I also handle cancellation, validation errors and backend failures, and I verify requests using the browser Network tab and API documentation.”

---

## Final interview revision checklist

- [ ] Explain the request-response lifecycle.
- [ ] Compare HTTP methods and status codes.
- [ ] Explain Fetch's HTTP-error behavior.
- [ ] Write GET, POST, PATCH and DELETE requests.
- [ ] Explain Promises, async/await and try/catch.
- [ ] Handle loading, success, empty and error states.
- [ ] Explain useEffect cleanup and race conditions.
- [ ] Compare Fetch and Axios.
- [ ] Configure Axios instances and interceptors.
- [ ] Explain bearer tokens, refresh and storage trade-offs.
- [ ] Explain CORS and preflight requests.
- [ ] Implement pagination, search and debouncing.
- [ ] Explain Promise.all and Promise.allSettled.
- [ ] Upload files using FormData.
- [ ] Explain service layers and custom hooks.
- [ ] Compare TanStack Query and SWR.
- [ ] Explain caching, invalidation and optimistic updates.
- [ ] Discuss API security, testing and debugging.
- [ ] Answer production and scenario-based questions clearly.

---

**End of Chapter 6 – React API Integration and Data Fetching**
