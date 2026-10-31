# Chapter 3: React Routing – Complete Interview Questions and Answers

> A comprehensive interview-preparation guide covering React Router from beginner to advanced level, including practical implementations, React Router v6/v7, authentication, protected routes, nested routing, data APIs, and real-world scenarios.

---

# Table of Contents

## Section 1: Routing Fundamentals

1. [What is React Router?](#q1-what-is-react-router)
2. [Why do we need routing in React?](#q2-why-do-we-need-routing-in-react)
3. [What is Single Page Application (SPA) routing?](#q3-what-is-single-page-application-spa-routing)
4. [How does routing work in a React application?](#q4-how-does-routing-work-in-a-react-application)
5. [What is the difference between traditional routing and client-side routing?](#q5-traditional-routing-vs-client-side-routing)
6. [What is React Router DOM?](#q6-what-is-react-router-dom)
7. [How do you install React Router?](#q7-how-do-you-install-react-router)
8. [What is BrowserRouter?](#q8-what-is-browserrouter)
9. [What are Routes and Route?](#q9-what-are-routes-and-route)
10. [What is the difference between a URL and a route?](#q10-what-is-the-difference-between-a-url-and-a-route)

## Section 2: Navigation Components

11. [What is Link?](#q11-what-is-link)
12. [What is NavLink?](#q12-what-is-navlink)
13. [Difference between Link and NavLink](#q13-what-is-the-difference-between-link-and-navlink)
14. [What is useNavigate?](#q14-what-is-usenavigate)
15. [Difference between Link and useNavigate](#q15-what-is-the-difference-between-link-and-usenavigate)
16. [What is the replace option in navigation?](#q16-what-is-the-replace-option-in-navigation)
17. [How do you navigate programmatically?](#q17-how-do-you-navigate-programmatically)
18. [How do you navigate backward and forward?](#q18-how-do-you-navigate-backward-and-forward)

## Section 3: Route Parameters and URL Handling

19. [What are dynamic routes?](#q19-what-are-dynamic-routes)
20. [What is useParams?](#q20-what-is-useparams)
21. [What is the difference between static and dynamic routes?](#q21-what-is-the-difference-between-static-and-dynamic-routes)
22. [What are URL search parameters?](#q22-what-are-url-search-parameters)
23. [What is useSearchParams?](#q23-what-is-usesearchparams)
24. [Difference between useParams and useSearchParams](#q24-what-is-the-difference-between-useparams-and-usesearchparams)
25. [How do you pass data between routes?](#q25-how-do-you-pass-data-between-routes)
26. [What is useLocation?](#q26-what-is-uselocation)
27. [What is location state?](#q27-what-is-location-state)
28. [What is useLocation().pathname?](#q28-what-is-uselocationpathname)

## Section 4: Nested Routing and Layouts

29. [What is nested routing?](#q29-what-is-nested-routing)
30. [What is Outlet?](#q30-what-is-outlet)
31. [Why do we use Outlet?](#q31-why-do-we-use-outlet)
32. [What is the difference between nested routes and normal routes?](#q32-what-is-the-difference-between-nested-routes-and-normal-routes)
33. [What is an index route?](#q33-what-is-an-index-route)
34. [What is a layout route?](#q34-what-is-a-layout-route)
35. [How do you create multiple layouts in React?](#q35-how-do-you-create-multiple-layouts-in-react)
36. [What are absolute and relative paths?](#q36-what-is-the-difference-between-absolute-and-relative-paths)

## Section 5: Protected Routes and Authentication

37. [What are protected routes?](#q37-what-are-protected-routes)
38. [Why do we need protected routes?](#q38-why-do-we-need-protected-routes)
39. [How do you implement protected routes?](#q39-how-do-you-implement-protected-routes)
40. [How do you redirect unauthenticated users to the login page?](#q40-how-do-you-redirect-unauthenticated-users-to-the-login-page)
41. [What is Navigate?](#q41-what-is-navigate)
42. [Difference between Navigate and useNavigate](#q42-what-is-the-difference-between-navigate-and-usenavigate)
43. [How do you implement role-based routing?](#q43-how-do-you-implement-role-based-routing)
44. [How do you prevent users from accessing admin routes?](#q44-how-do-you-prevent-users-from-accessing-admin-routes)
45. [How do you handle logout and redirect?](#q45-how-do-you-handle-logout-and-redirect)
46. [What is the difference between authentication and authorization in routing?](#q46-what-is-the-difference-between-authentication-and-authorization-in-routing)
47. [Are protected routes enough to secure an application?](#q47-are-protected-routes-enough-to-secure-an-application)

## Section 6: Advanced React Router Concepts

48. [What is createBrowserRouter?](#q48-what-is-createbrowserrouter)
49. [What is createRoutesFromElements?](#q49-what-is-createroutesfromelements)
50. [Difference between BrowserRouter and createBrowserRouter](#q50-what-is-the-difference-between-browserrouter-and-createbrowserrouter)
51. [What are route objects?](#q51-what-are-route-objects)
52. [What are loaders?](#q52-what-are-loaders)
53. [What is useLoaderData?](#q53-what-is-useloaderdata)
54. [What are actions in React Router?](#q54-what-are-actions-in-react-router)
55. [What is Form in React Router?](#q55-what-is-form-in-react-router)
56. [What is useActionData?](#q56-what-is-useactiondata)
57. [What is useNavigation?](#q57-what-is-usenavigation)
58. [What is useSubmit?](#q58-what-is-usesubmit)
59. [What is useFetcher?](#q59-what-is-usefetcher)
60. [What is errorElement?](#q60-what-is-errorelement)
61. [What is useRouteError?](#q61-what-is-userouteerror)
62. [What is lazy loading in routing?](#q62-what-is-lazy-loading-in-routing)
63. [What is route-based code splitting?](#q63-what-is-route-based-code-splitting)
64. [What is revalidation in React Router?](#q64-what-is-revalidation-in-react-router)
65. [What is useRevalidator?](#q65-what-is-userevalidator)

## Section 7: Advanced Navigation and Routing

66. [What is a wildcard route?](#q66-what-is-a-wildcard-route)
67. [How do you implement a 404 Not Found page?](#q67-how-do-you-implement-a-404-not-found-page)
68. [What is basename in React Router?](#q68-what-is-basename-in-react-router)
69. [What is HashRouter?](#q69-what-is-hashrouter)
70. [Difference between BrowserRouter and HashRouter](#q70-what-is-the-difference-between-browserrouter-and-hashrouter)
71. [What is MemoryRouter?](#q71-what-is-memoryrouter)
72. [What is ScrollRestoration?](#q72-what-is-scrollrestoration)
73. [What is useBlocker?](#q73-what-is-useblocker)
74. [What is useNavigationType?](#q74-what-is-usenavigationtype)
75. [How do you handle query strings and filters?](#q75-how-do-you-handle-query-strings-and-filters)

## Section 8: React Router v6 vs v7

76. [What are the major differences between React Router v6 and v7?](#q76-what-are-the-major-differences-between-react-router-v6-and-v7)
77. [What happened to Switch in React Router v6?](#q77-what-happened-to-switch-in-react-router-v6)
78. [Why is exact no longer required in React Router v6?](#q78-why-is-exact-no-longer-required-in-react-router-v6)
79. [What is the difference between element and Component in route configuration?](#q79-what-is-the-difference-between-element-and-component-in-route-configuration)
80. [What is the difference between declarative, data and framework routing?](#q80-what-is-the-difference-between-declarative-data-and-framework-routing)
81. [Can React Router be used without BrowserRouter?](#q81-can-react-router-be-used-without-browserrouter)

## Section 9: Practical and Scenario-Based Interview Questions

82. [How would you create a navbar that highlights the active page?](#q82-how-would-you-create-a-navbar-that-highlights-the-active-page)
83. [How would you redirect users after successful login?](#q83-how-would-you-redirect-users-after-successful-login)
84. [How would you redirect users to the page they originally wanted to visit?](#q84-how-would-you-redirect-users-to-the-page-they-originally-wanted-to-visit)
85. [How would you implement an admin dashboard with nested pages?](#q85-how-would-you-implement-an-admin-dashboard-with-nested-pages)
86. [How would you prevent a logged-in user from opening the login page?](#q86-how-would-you-prevent-an-authenticated-user-from-opening-the-login-page)
87. [How would you implement pagination using URL parameters?](#q87-how-would-you-implement-pagination-using-url-parameters)
88. [How would you handle invalid route parameters?](#q88-how-would-you-handle-invalid-route-parameters)
89. [How would you implement breadcrumbs?](#q89-how-would-you-implement-breadcrumbs)
90. [How would you troubleshoot a blank page after refreshing a route?](#q90-how-would-you-troubleshoot-a-blank-page-after-refreshing-a-route)
91. [How would you troubleshoot navigation not working?](#q91-how-would-you-troubleshoot-navigation-not-working)
92. [What are the common mistakes developers make while implementing routing?](#q92-what-are-the-common-mistakes-developers-make-while-implementing-routing)

---

# Section 1: Routing Fundamentals

### Q1. What is React Router?

**Interview Answer:**

React Router is a routing library used in React applications to manage navigation between different pages or views without reloading the entire browser page.

In a React application, we generally build a Single Page Application, where different components represent different pages.

React Router allows us to associate URL paths with specific components.

For example, if a user visits `/home`, we can display the Home component. If the user visits `/about`, we can display the About component.

It also provides features like dynamic routing, nested routing, protected routes, navigation, URL parameters and data loading.

**Example:**

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

function Home() {
  return <h1>Home Page</h1>;
}

function About() {
  return <h1>About Page</h1>;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

**Explanation:**

- `BrowserRouter` provides routing functionality.
- `Routes` contains all the route definitions.
- `Route` defines the URL path and the component to render.
- `element` specifies the React element to display when the route matches.

**Interview follow-up:** Does React Router reload the entire page?

No. Normal client-side navigation through React Router changes the URL and renders the matching route without performing a full document reload.

---

### Q2. Why do we need routing in React?

**Interview Answer:**

We need routing because real-world React applications contain multiple pages or views, such as Home, About, Products, Login, Register and Dashboard.

Without a routing library, we would have to manually manage which component should be displayed based on the current URL.

React Router simplifies this process by providing a structured way to manage navigation.

The main advantages are:

1. **Navigation:** Allows users to move between different pages.
2. **No full-page reload:** Makes navigation faster and provides a smoother user experience.
3. **Dynamic routing:** Allows us to display different data based on URL parameters.
4. **Nested routing:** Helps us build layouts containing multiple child pages.
5. **Protected routing:** Helps control which routes should be displayed based on authentication and authorization state.
6. **URL management:** Allows us to maintain page state through URL parameters and query strings.
7. **Browser history:** Supports back and forward navigation.

**Real-world example:**

In an e-commerce application:

- `/` displays the homepage.
- `/products` displays all products.
- `/products/101` displays product details.
- `/cart` displays the shopping cart.
- `/login` displays the login page.

React Router manages these navigation paths.

---

### Q3. What is Single Page Application (SPA) routing?

**Interview Answer:**

SPA stands for Single Page Application.

It is an application where the browser initially loads a single HTML document, and JavaScript dynamically updates the content when users navigate between different views.

In traditional multi-page applications, navigating to a different page generally sends a request to the server for another HTML document.

In an SPA, client-side routing handles navigation by updating the URL and rendering the appropriate React components without reloading the entire document.

**Example:**

Suppose we have three routes:

```text
/home
/about
/contact
```

When the user navigates from `/home` to `/about`, React Router renders the About component while keeping the application running.

**Advantages:**

- Faster navigation after the initial load.
- Better interactive experience.
- Reusable components.
- Reduced full-document requests.

**Disadvantages:**

- Initial JavaScript loading can be expensive.
- SEO requires appropriate rendering and metadata strategies.
- Routing configuration and server fallback handling are necessary.

---

### Q4. How does routing work in a React application?

**Interview Answer:**

Routing works by matching the current URL against the route definitions configured in the application.

When a user navigates to a URL, React Router identifies the matching route and renders the corresponding React component.

The basic flow is:

1. The user opens a URL.
2. BrowserRouter provides access to browser-based navigation.
3. React Router checks the configured routes.
4. It identifies the matching route.
5. The corresponding component is rendered.
6. When the user navigates to another route, React Router updates the URL and renders the new matching component.

**Flow:**

```text
User visits /about
       |
       v
BrowserRouter
       |
       v
Routes
       |
       v
Match /about
       |
       v
Render About Component
```

**Important:** React Router handles client-side navigation. The web server must also be configured to serve the application's entry HTML for valid client-side routes when users directly open or refresh those URLs.

---

### Q5. Traditional Routing vs Client-Side Routing

**Interview Answer:**

Traditional routing is generally handled by the server, whereas client-side routing is handled by JavaScript in the browser.

| Traditional routing                  | Client-side routing                       |
| ------------------------------------ | ----------------------------------------- |
| Server handles navigation requests   | Browser handles navigation                |
| Usually requests a new HTML document | Usually updates the existing document     |
| Full-page navigation                 | No full-page reload for normal navigation |
| More document requests               | Fewer full-document requests              |
| Common in multi-page applications    | Common in SPAs                            |

**Example:**

Traditional routing:

```text
Click About
    |
    v
Browser sends request to server
    |
    v
Server returns new HTML
    |
    v
Browser loads new document
```

Client-side routing:

```text
Click About
    |
    v
React Router handles navigation
    |
    v
URL changes
    |
    v
About component renders
```

---

### Q6. What is React Router DOM?

**Interview Answer:**

React Router DOM is the React Router package traditionally used for web applications.

It provides browser-specific routing components and APIs, such as:

- BrowserRouter
- HashRouter
- Routes
- Route
- Link
- NavLink
- Navigate
- useNavigate
- useParams
- useLocation

In React Router v6, these APIs are commonly imported from `react-router-dom`.

In React Router v7, the package structure was simplified and web APIs can also be imported from `react-router`. The `react-router-dom` package remains available as a compatibility re-export.

---

### Q7. How do you install React Router?

**Interview Answer:**

For a React web application, we can install React Router using npm.

```bash
npm install react-router-dom
```

After installation, we can import the required routing components.

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link
} from "react-router-dom";
```

To verify the installed version:

```bash
npm list react-router-dom
```

**Note:** React Router v7 also supports imports from `react-router`. Follow the import conventions of the installed version and project configuration.

---

### Q8. What is BrowserRouter?

**Interview Answer:**

BrowserRouter is a router component that uses the browser's History API to manage navigation in a React application.

It allows us to create clean URLs without hash symbols.

For example:

```text
https://example.com/home
https://example.com/about
```

It provides routing context to all its descendant components.

We generally wrap the application with BrowserRouter so that components inside it can access routing features.

**Example:**

```jsx
import { BrowserRouter } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <h1>My React Application</h1>
    </BrowserRouter>
  );
}

export default App;
```

**Important points:**

1. It uses the browser History API.
2. It supports clean URLs.
3. It enables client-side navigation.
4. It provides routing context.
5. The server must support fallback to the application entry point for direct URL requests.

---

### Q9. What are Routes and Route?

**Interview Answer:**

`Routes` and `Route` are components used to define and match routes in React Router.

`Routes` acts as a container for route definitions and renders the best matching route branch.

`Route` defines a specific URL pattern and the React element that should be rendered when that route matches.

**Example:**

```jsx
import {
  Routes,
  Route
} from "react-router-dom";

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="/contact" element={<Contact />} />
    </Routes>
  );
}
```

**Difference:**

| Routes                          | Route                        |
| ------------------------------- | ---------------------------- |
| Container for route definitions | Defines an individual route  |
| Performs route matching         | Specifies path and element   |
| Contains Route components       | Contains route configuration |

**Important:** A Route must be rendered inside Routes or supplied through a supported route configuration API.

---

### Q10. What is the difference between a URL and a route?

**Interview Answer:**

A URL is the actual web address entered into the browser, whereas a route is the application's configuration that determines which component should be rendered for a matching URL.

For example:

```text
URL: https://example.com/products/101
Route: /products/:productId
```

Here, `/products/101` is the actual URL, and `/products/:productId` is the route pattern.

---

# Section 2: Navigation Components

### Q11. What is Link?

**Interview Answer:**

Link is a React Router component used to navigate between different routes without reloading the entire browser page.

It works similarly to an HTML anchor tag, but instead of performing normal document navigation, it handles client-side navigation.

**Example:**

```jsx
import { Link } from "react-router-dom";

function Navbar() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/contact">Contact</Link>
    </nav>
  );
}
```

When the user clicks About, React Router navigates to `/about` and renders the matching component.

**Why not use a normal anchor tag?**

A normal `<a>` tag generally performs a document navigation, whereas React Router's Link performs client-side navigation for internal application routes.

---

### Q12. What is NavLink?

**Interview Answer:**

NavLink is a special type of Link component that provides information about whether the current route is active.

It is mainly used for navigation menus where we want to highlight the currently selected page.

**Example:**

```jsx
import { NavLink } from "react-router-dom";

function Navbar() {
  return (
    <nav>
      <NavLink
        to="/"
        className={({ isActive }) =>
          isActive ? "active" : ""
        }
      >
        Home
      </NavLink>

      <NavLink
        to="/about"
        className={({ isActive }) =>
          isActive ? "active" : ""
        }
      >
        About
      </NavLink>
    </nav>
  );
}
```

**CSS:**

```css
.active {
  color: blue;
  font-weight: bold;
}
```

NavLink also supports properties such as `end` and `style`.

---

### Q13. What is the difference between Link and NavLink?

**Interview Answer:**

Both Link and NavLink are used for client-side navigation.

The main difference is that NavLink provides active-state information, which allows us to highlight the currently active navigation item.

| Link                                      | NavLink                           |
| ----------------------------------------- | --------------------------------- |
| Used for navigation                       | Used for navigation               |
| Does not provide active-state styling API | Provides active-state information |
| Suitable for buttons and ordinary links   | Suitable for navigation menus     |
| No `isActive` callback                    | Supports `isActive` callback      |

**When would you use them?**

I would use Link for normal navigation, such as navigating from a product card to its details page.

I would use NavLink for a navbar or sidebar where the active page should be highlighted.

---

### Q14. What is useNavigate?

**Interview Answer:**

useNavigate is a React Router hook that allows us to navigate programmatically from within a React component.

It is useful when navigation should happen after an action, such as successful login, logout, form submission or button click.

**Example:**

```jsx
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();

  const handleLogin = () => {
    // Login logic
    navigate("/dashboard");
  };

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

Here, after successful login, the user is navigated to the dashboard.

**Important:** useNavigate must be called inside a component that is rendered within a router context.

---

### Q15. What is the difference between Link and useNavigate?

**Interview Answer:**

Link is a component used for navigation through user interaction, whereas useNavigate is a hook used when we need to navigate programmatically.

| Link                         | useNavigate                         |
| ---------------------------- | ----------------------------------- |
| Component                    | Hook                                |
| Used directly in JSX         | Called inside a component           |
| Suitable for clickable links | Suitable for conditional navigation |
| Declarative navigation       | Programmatic navigation             |

**Example of Link:**

```jsx
<Link to="/dashboard">
  Dashboard
</Link>
```

**Example of useNavigate:**

```jsx
const navigate = useNavigate();

const handleSubmit = () => {
  navigate("/dashboard");
};
```

---

### Q16. What is the replace option in navigation?

**Interview Answer:**

The replace option tells React Router to replace the current browser history entry instead of adding a new entry.

Normally, navigation adds a new history entry.

However, when we use `replace: true`, the current history entry is replaced.

**Example:**

```jsx
navigate("/dashboard", {
  replace: true
});
```

**Real-world example:**

After successful login, we may not want the user to return to the login page by pressing the browser's Back button.

In such cases, we can use replace navigation.

```jsx
navigate("/dashboard", {
  replace: true
});
```

---

### Q17. How do you navigate programmatically?

**Interview Answer:**

We can use the useNavigate hook to navigate programmatically.

For example, after submitting a login form, we can check whether authentication was successful and then navigate to the dashboard.

```jsx
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();

  const handleLogin = async () => {
    const success = true;

    if (success) {
      navigate("/dashboard");
    }
  };

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

We can also navigate with options:

```jsx
navigate("/dashboard", {
  replace: true
});
```

Or navigate backward:

```jsx
navigate(-1);
```

---

### Q18. How do you navigate backward and forward?

**Interview Answer:**

We can use the useNavigate hook with numeric values to move through browser history.

```jsx
const navigate = useNavigate();
```

**Backward:**

```jsx
navigate(-1);
```

**Forward:**

```jsx
navigate(1);
```

**Move backward by two entries:**

```jsx
navigate(-2);
```

These operations depend on the available browser history entries.

---

# Section 3: Route Parameters and URL Handling

### Q19. What are dynamic routes?

**Interview Answer:**

Dynamic routes are routes where a part of the URL can change depending on the data being displayed.

They are useful when we want to display different content using the same component.

For example, in an e-commerce application, every product has a different ID.

Instead of creating separate routes for every product, we can create one dynamic route.

```jsx
<Route
  path="/products/:productId"
  element={<ProductDetails />}
/>
```

Examples:

```text
/products/101
/products/102
/products/103
```

All these URLs match the same route, but the product ID changes.

---

### Q20. What is useParams?

**Interview Answer:**

useParams is a React Router hook used to access dynamic parameters from the current URL.

When we define a dynamic route using a colon, React Router extracts that value and makes it available through useParams.

**Example:**

```jsx
<Route
  path="/products/:productId"
  element={<ProductDetails />}
/>
```

**ProductDetails.jsx:**

```jsx
import { useParams } from "react-router-dom";

function ProductDetails() {
  const { productId } = useParams();

  return (
    <h1>Product ID: {productId}</h1>
  );
}

export default ProductDetails;
```

If the user visits:

```text
/products/101
```

The output will be:

```text
Product ID: 101
```

**Important points:**

1. It is a hook.
2. It returns an object containing matched route parameters.
3. Parameter values are strings.
4. It is useful for product details, user profiles and blog pages.

---

### Q21. What is the difference between static and dynamic routes?

**Interview Answer:**

A static route has a fixed URL path, whereas a dynamic route contains parameters that can change depending on the requested data.

| Static Route            | Dynamic Route                |
| ----------------------- | ---------------------------- |
| Fixed path              | Contains variable parameters |
| Example `/about`        | Example `/products/:id`      |
| Same path for all users | Parameter changes            |
| Used for fixed pages    | Used for data-driven pages   |

**Example:**

```jsx
<Route path="/about" element={<About />} />

<Route
  path="/products/:id"
  element={<ProductDetails />}
/>
```

---

### Q22. What are URL search parameters?

**Interview Answer:**

URL search parameters are key-value pairs included in the query string of a URL.

They begin after the question mark `?`.

They are commonly used for searching, filtering, sorting and pagination.

**Example:**

```text
/products?category=electronics&page=2
```

Here:

- `category=electronics` is a search parameter.
- `page=2` is another search parameter.

We can access these values using React Router's useSearchParams hook.

---

### Q23. What is useSearchParams?

**Interview Answer:**

useSearchParams is a React Router hook used to read and update query parameters in the URL.

It returns two values:

1. `searchParams` – used to read query parameters.
2. `setSearchParams` – used to update query parameters.

**Example:**

```jsx
import { useSearchParams } from "react-router-dom";

function Products() {
  const [searchParams, setSearchParams] =
    useSearchParams();

  const page = searchParams.get("page");

  return (
    <div>
      <h1>Products Page</h1>
      <p>Current Page: {page || "1"}</p>

      <button
        onClick={() =>
          setSearchParams({ page: "2" })
        }
      >
        Go to Page 2
      </button>
    </div>
  );
}
```

If the URL is:

```text
/products?page=2
```

The output is:

```text
Current Page: 2
```

**Important:** Updating search parameters causes navigation and updates the URL. When preserving existing parameters matters, use a functional update or construct the new parameter set carefully.

---

### Q24. What is the difference between useParams and useSearchParams?

**Interview Answer:**

Both hooks are used to extract information from the URL, but they handle different types of URL values.

| useParams                             | useSearchParams                 |
| ------------------------------------- | ------------------------------- |
| Reads route parameters                | Reads query parameters          |
| Parameters are part of the route path | Parameters appear after `?`     |
| Defined using `:`                     | Defined using key-value pairs   |
| Returns an object                     | Returns searchParams and setter |
| Example `/users/101`                  | Example `/users?page=2`         |

**Example:**

```text
/users/101
```

Here, useParams accesses `101`.

```text
/users?page=2
```

Here, useSearchParams accesses `2`.

---

### Q25. How do you pass data between routes?

**Interview Answer:**

In React Router, we can pass data between routes using several approaches depending on the type of data and whether it should be shareable through the URL.

The common approaches are:

1. URL parameters.
2. Query parameters.
3. Navigation state.
4. Context API or global state management.

**Method 1: URL Parameters**

```jsx
<Link to="/users/101">
  View User
</Link>
```

Receiving component:

```jsx
const { id } = useParams();
```

**Method 2: Query Parameters**

```jsx
<Link to="/products?category=mobile">
  Mobiles
</Link>
```

Receiving component:

```jsx
const [searchParams] = useSearchParams();

const category = searchParams.get("category");
```

**Method 3: Navigation State**

```jsx
navigate("/profile", {
  state: {
    username: "Nikitha"
  }
});
```

Receiving component:

```jsx
const location = useLocation();

const username = location.state?.username;
```

**Method 4: Context API**

If multiple components need the same information, we can store it in Context API and access it across the application.

**Interview tip:** I would use URL parameters for resource identifiers, query parameters for filters and pagination, navigation state for temporary navigation-specific data, and Context API or a global store for shared application state.

---

### Q26. What is useLocation?

**Interview Answer:**

useLocation is a React Router hook that provides information about the current location.

It returns a location object containing properties such as:

- `pathname`
- `search`
- `hash`
- `state`
- `key`

**Example:**

```jsx
import { useLocation } from "react-router-dom";

function CurrentPage() {
  const location = useLocation();

  return (
    <div>
      <p>Path: {location.pathname}</p>
      <p>Query: {location.search}</p>
      <p>Hash: {location.hash}</p>
    </div>
  );
}
```

If the current URL is:

```text
/products?page=2#reviews
```

The output will be:

```text
Path: /products
Query: ?page=2
Hash: #reviews
```

---

### Q27. What is location state?

**Interview Answer:**

Location state is a way to pass temporary data during navigation without adding that data to the URL.

We can pass it using the `state` option of Link or navigate.

**Example:**

```jsx
navigate("/profile", {
  state: {
    userId: 101,
    username: "Nikitha"
  }
});
```

To access the data:

```jsx
const location = useLocation();

const user = location.state;

console.log(user?.username);
```

**Use cases:**

- Passing information between pages.
- Returning to a previous page.
- Displaying temporary success messages.
- Passing selected item information.

**Important:** Location state is not a replacement for persistent storage or secure authentication. It may be unavailable when a page is opened directly, and sensitive information should not be placed there.

---

### Q28. What is useLocation().pathname?

**Interview Answer:**

`pathname` is a property of the location object that represents the current URL path without the query string or hash fragment.

Example:

```text
/products/101?category=mobile#details
```

The pathname is:

```text
/products/101
```

We can access it using:

```jsx
const location = useLocation();

console.log(location.pathname);
```

It is useful for conditional rendering, active navigation logic and tracking page changes.

---

# Section 4: Nested Routing and Layouts

### Q29. What is nested routing?

**Interview Answer:**

Nested routing means defining child routes inside a parent route.

It allows us to create a common layout that remains visible while different child components are rendered inside it.

For example, an admin dashboard may contain:

- Dashboard
- Users
- Products
- Settings

All these pages can share the same sidebar and navbar.

Instead of repeating the sidebar and navbar in every component, we create a parent layout and render the child routes inside it using Outlet.

**Example:**

```jsx
<Routes>
  <Route path="/admin" element={<AdminLayout />}>
    <Route
      path="dashboard"
      element={<Dashboard />}
    />

    <Route
      path="users"
      element={<Users />}
    />

    <Route
      path="settings"
      element={<Settings />}
    />
  </Route>
</Routes>
```

---

### Q30. What is Outlet?

**Interview Answer:**

Outlet is a component provided by React Router that acts as a placeholder for rendering matched child routes inside a parent route.

When we use nested routing, the parent component provides the common layout, and Outlet determines where the active child component should appear.

**Example:**

```jsx
import { Outlet } from "react-router-dom";

function AdminLayout() {
  return (
    <div>
      <h1>Admin Dashboard</h1>

      <aside>
        Sidebar
      </aside>

      <main>
        <Outlet />
      </main>
    </div>
  );
}
```

Routes:

```jsx
<Routes>
  <Route
    path="/admin"
    element={<AdminLayout />}
  >
    <Route
      path="users"
      element={<Users />}
    />

    <Route
      path="products"
      element={<Products />}
    />
  </Route>
</Routes>
```

When the user visits `/admin/users`, the AdminLayout renders and the Users component appears inside Outlet.

---

### Q31. Why do we use Outlet?

**Interview Answer:**

We use Outlet to avoid repeating common layout components across multiple pages.

Suppose we have an admin dashboard containing a navbar, sidebar and main content area.

If we create separate components for every admin page, we might end up repeating the same navbar and sidebar.

Using nested routing with Outlet allows us to define the common structure once and render different child components inside it.

**Advantages:**

1. Code reusability.
2. Cleaner component structure.
3. Easier maintenance.
4. Consistent layout.
5. Better route organisation.

---

### Q32. What is the difference between nested routes and normal routes?

**Interview Answer:**

Normal routes are independent route definitions, whereas nested routes establish a parent-child relationship between routes.

| Normal Routes                  | Nested Routes                     |
| ------------------------------ | --------------------------------- |
| Independent routes             | Parent-child relationship         |
| Separate page rendering        | Child routes render within parent |
| No shared layout automatically | Can share common layout           |
| No Outlet required             | Outlet commonly used              |

---

### Q33. What is an index route?

**Interview Answer:**

An index route is the default child route rendered when the parent route matches exactly and none of its more specific child paths are selected.

It does not have its own path.

**Example:**

```jsx
<Routes>
  <Route path="/admin" element={<AdminLayout />}>

    <Route
      index
      element={<Dashboard />}
    />

    <Route
      path="users"
      element={<Users />}
    />

    <Route
      path="settings"
      element={<Settings />}
    />

  </Route>
</Routes>
```

When the user visits:

```text
/admin
```

The Dashboard component is rendered inside Outlet.

When the user visits:

```text
/admin/users
```

The Users component is rendered instead.

**Important:** An index route cannot have children or a path of its own.

---

### Q34. What is a layout route?

**Interview Answer:**

A layout route is a route that provides a common structure for multiple child routes.

For example, we can create:

- PublicLayout for public pages.
- AuthLayout for login and registration.
- AdminLayout for administrative pages.
- UserLayout for user dashboards.

A layout route can be pathless or have a URL path.

**Example:**

```jsx
function MainLayout() {
  return (
    <>
      <Navbar />

      <main>
        <Outlet />
      </main>

      <Footer />
    </>
  );
}
```

Routes:

```jsx
<Routes>
  <Route element={<MainLayout />}>
    <Route path="/" element={<Home />} />
    <Route path="/about" element={<About />} />
    <Route path="/contact" element={<Contact />} />
  </Route>
</Routes>
```

Here, the navbar and footer are shared across the child routes.

---

### Q35. How do you create multiple layouts in React?

**Interview Answer:**

We can create multiple layouts by defining separate parent routes for different sections of the application.

For example, a real-world application may have public pages, authentication pages and admin pages.

Each section can have its own layout.

**Example:**

```jsx
<Routes>

  {/* Public Layout */}
  <Route element={<PublicLayout />}>
    <Route path="/" element={<Home />} />
    <Route path="/about" element={<About />} />
  </Route>

  {/* Authentication Layout */}
  <Route element={<AuthLayout />}>
    <Route path="/login" element={<Login />} />
    <Route path="/register" element={<Register />} />
  </Route>

  {/* Admin Layout */}
  <Route path="/admin" element={<AdminLayout />}>
    <Route index element={<Dashboard />} />
    <Route path="users" element={<Users />} />
    <Route path="settings" element={<Settings />} />
  </Route>

</Routes>
```

Each layout contains its own Outlet.

This approach keeps the application modular and maintainable.

---

### Q36. What is the difference between absolute and relative paths?

**Interview Answer:**

An absolute path starts from the root of the URL, whereas a relative path is resolved in relation to the current route context.

**Absolute path:**

```jsx
<Route path="/admin/users" element={<Users />} />
```

**Relative child path:**

```jsx
<Route path="/admin" element={<AdminLayout />}>
  <Route path="users" element={<Users />} />
</Route>
```

The resulting URL is:

```text
/admin/users
```

Relative paths are especially useful in nested routing because they help us organise routes without repeating the entire parent path.

---

# Section 5: Protected Routes and Authentication

### Q37. What are protected routes?

**Interview Answer:**

Protected routes are routes that should only be accessible to users who satisfy certain authentication or authorization conditions.

For example, in an application, the dashboard should only be accessible to authenticated users.

Similarly, an admin dashboard should only be accessible to users who have the admin role.

We can implement protected routes using authentication state, role checks and React Router navigation.

---

### Q38. Why do we need protected routes?

**Interview Answer:**

We need protected routes to control access to specific parts of an application.

For example:

- Unauthenticated users should not access the dashboard.
- Normal users should not access admin pages.
- Users should be redirected to the login page if they are not authenticated.

Protected routes help improve the user experience by preventing unauthorized navigation through the frontend.

However, frontend route protection is not sufficient security by itself. The backend must independently verify authentication and permissions for protected API operations.

---

### Q39. How do you implement protected routes?

**Interview Answer:**

I implement protected routes by creating a reusable component that checks whether the user is authenticated.

If the user is authenticated, I render the child route using Outlet.

Otherwise, I redirect the user to the login page using Navigate.

**Step 1: Create ProtectedRoute.jsx**

```jsx
import { Navigate, Outlet } from "react-router-dom";

function ProtectedRoute() {
  const token = sessionStorage.getItem("token");

  if (!token) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}

export default ProtectedRoute;
```

**Step 2: Configure the routes**

```jsx
<Routes>

  <Route path="/login" element={<Login />} />

  <Route element={<ProtectedRoute />}>
    <Route
      path="/dashboard"
      element={<Dashboard />}
    />

    <Route
      path="/profile"
      element={<Profile />}
    />

  </Route>

</Routes>
```

**Execution flow:**

```text
User visits /dashboard
         |
         v
ProtectedRoute executes
         |
         v
Is user authenticated?
      /       \
    Yes        No
     |          |
     v          v
  Outlet    Navigate
     |       /login
     v
 Dashboard
```

**Important interview point:**

Checking whether a token exists in sessionStorage is only a simplified frontend example. In a production application, authentication state should be managed carefully, and the backend must validate tokens and permissions on every protected request.

---

### Q40. How do you redirect unauthenticated users to the login page?

**Interview Answer:**

We can use the Navigate component to redirect unauthenticated users to the login page.

**Example:**

```jsx
import { Navigate, Outlet } from "react-router-dom";

function ProtectedRoute() {
  const isAuthenticated = false;

  return isAuthenticated
    ? <Outlet />
    : <Navigate to="/login" replace />;
}
```

The `replace` option replaces the current history entry, preventing the user from returning to the unauthorized route using the Back button.

---

### Q41. What is Navigate?

**Interview Answer:**

Navigate is a React Router component used to perform declarative navigation.

It redirects the user to another route when the component is rendered.

It is useful for conditional redirects, such as redirecting unauthenticated users to login.

**Example:**

```jsx
import { Navigate } from "react-router-dom";

function ProtectedPage() {
  const isAuthenticated = false;

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return <h1>Protected Page</h1>;
}
```

---

### Q42. What is the difference between Navigate and useNavigate?

**Interview Answer:**

Navigate is a component used for declarative navigation, whereas useNavigate is a hook used for programmatic navigation.

| Navigate                         | useNavigate                               |
| -------------------------------- | ----------------------------------------- |
| Component                        | Hook                                      |
| Used inside JSX                  | Called inside a component                 |
| Navigation occurs when rendered  | Navigation occurs when function is called |
| Useful for conditional redirects | Useful for event-based navigation         |

**Navigate:**

```jsx
<Navigate to="/login" replace />
```

**useNavigate:**

```jsx
const navigate = useNavigate();

navigate("/dashboard");
```

---

### Q43. How do you implement role-based routing?

**Interview Answer:**

Role-based routing means restricting access to specific routes based on the user's role.

For example, an application may have two roles:

- Admin
- User

An admin can access administrative pages, while a normal user can access only user-specific pages.

I implement this by checking both authentication and the user's role.

**Example:**

```jsx
import { Navigate, Outlet } from "react-router-dom";

function RoleProtectedRoute({ allowedRoles }) {
  const role = sessionStorage.getItem("role");

  if (!role) {
    return <Navigate to="/login" replace />;
  }

  if (!allowedRoles.includes(role)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return <Outlet />;
}
```

**Route configuration:**

```jsx
<Routes>

  <Route
    element={
      <RoleProtectedRoute allowedRoles={["admin"]} />
    }
  >
    <Route
      path="/admin"
      element={<AdminDashboard />}
    />
  </Route>

  <Route
    element={
      <RoleProtectedRoute allowedRoles={["user", "admin"]} />
    }
  >
    <Route
      path="/dashboard"
      element={<UserDashboard />}
    />
  </Route>

</Routes>
```

**Important:** The example demonstrates frontend route organisation. The backend must verify the actual user's role and permissions. Never trust a role value stored in browser storage as proof of authorization.

---

### Q44. How do you prevent users from accessing admin routes?

**Interview Answer:**

I would implement role-based authorization.

First, I would authenticate the user and obtain their trusted role information from the server.

Then I would create a reusable route guard that checks whether the user has the admin role.

If the user is an admin, I would render the requested route. Otherwise, I would redirect them to an unauthorized page.

I would also implement authorization checks on the backend so that users cannot access admin APIs simply by bypassing frontend routing.

---

### Q45. How do you handle logout and redirect?

**Interview Answer:**

During logout, I clear the authentication state, remove the locally stored session information and redirect the user to the login page.

**Example:**

```jsx
import { useNavigate } from "react-router-dom";

function Navbar() {
  const navigate = useNavigate();

  const handleLogout = () => {
    sessionStorage.removeItem("token");
    sessionStorage.removeItem("role");

    navigate("/login", {
      replace: true
    });
  };

  return (
    <button onClick={handleLogout}>
      Logout
    </button>
  );
}
```

In a production application, logout should also invalidate the server-side session or refresh token where applicable.

---

### Q46. What is the difference between authentication and authorization in routing?

**Interview Answer:**

Authentication verifies who the user is, whereas authorization determines what the authenticated user is allowed to access.

For example:

- Authentication checks whether the user has successfully logged in.
- Authorization checks whether that user has permission to access the admin dashboard.

| Authentication         | Authorization                |
| ---------------------- | ---------------------------- |
| Verifies user identity | Verifies user permissions    |
| Answers who are you?   | Answers what can you access? |
| Usually happens first  | Follows authentication       |
| Example: Login         | Example: Admin role check    |

---

### Q47. Are protected routes enough to secure an application?

**Interview Answer:**

No. Protected routes only control navigation and rendering on the frontend.

A user can potentially bypass frontend restrictions by directly calling backend APIs.

Therefore, real security must be implemented on the backend.

For example, if only admins can delete users, the backend must verify the authenticated user's permissions before processing the delete request.

Frontend route protection improves user experience, but backend authorization provides the actual security enforcement.

---

# Section 6: Advanced React Router Concepts

### Q48. What is createBrowserRouter?

**Interview Answer:**

createBrowserRouter is a function used to create a browser-based router using route object configuration.

It is commonly used with React Router's Data Router APIs, such as loaders, actions, error handling and route-level data management.

Instead of declaring routes only through BrowserRouter and Routes, we define route objects and pass them to createBrowserRouter.

**Example:**

```jsx
import {
  createBrowserRouter,
  RouterProvider
} from "react-router-dom";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />
  },
  {
    path: "/about",
    element: <About />
  }
]);

function App() {
  return <RouterProvider router={router} />;
}

export default App;
```

**Important points:**

1. Uses route object configuration.
2. Supports nested routes.
3. Supports loaders and actions.
4. Supports route-level error handling.
5. Used with RouterProvider.

---

### Q49. What is createRoutesFromElements?

**Interview Answer:**

createRoutesFromElements is a helper function that converts JSX route definitions into route objects.

It allows us to write routes using the familiar Route component syntax while using the Data Router APIs.

**Example:**

```jsx
import {
  createBrowserRouter,
  createRoutesFromElements,
  Route,
  RouterProvider
} from "react-router-dom";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path="/" element={<Home />}>
      <Route
        path="about"
        element={<About />}
      />
    </Route>
  )
);

function App() {
  return <RouterProvider router={router} />;
}
```

It is useful when we prefer JSX route definitions instead of manually creating route objects.

---

### Q50. What is the difference between BrowserRouter and createBrowserRouter?

**Interview Answer:**

BrowserRouter is a router component that provides browser-based routing using declarative route configuration.

createBrowserRouter is a function that creates a router instance from route objects and supports Data Router features.

| BrowserRouter                       | createBrowserRouter                |
| ----------------------------------- | ---------------------------------- |
| Router component                    | Router creation function           |
| Commonly used with Routes and Route | Used with route objects            |
| Declarative routing                 | Data Router configuration          |
| Suitable for basic routing          | Suitable for advanced data routing |
| Used with BrowserRouter context     | Rendered through RouterProvider    |

**Important:** Both can support nested routes. The main distinction is their routing configuration model and the availability of Data Router APIs.

---

### Q51. What are route objects?

**Interview Answer:**

Route objects are JavaScript objects used to define the configuration of routes in React Router.

They contain properties such as:

- path
- element
- children
- loader
- action
- errorElement
- Component

**Example:**

```jsx
const routes = [
  {
    path: "/",
    element: <Home />
  },
  {
    path: "/products",
    element: <Products />,
    children: [
      {
        path: ":id",
        element: <ProductDetails />
      }
    ]
  }
];
```

Route objects are commonly used with createBrowserRouter.

---

### Q52. What are loaders?

**Interview Answer:**

Loaders are functions used to fetch or prepare route data before the route component is rendered.

They are part of React Router's Data Router APIs.

Instead of fetching data inside useEffect, we can define a loader at the route level and access its result through useLoaderData.

**Example:**

```jsx
async function productsLoader() {
  const response = await fetch(
    "https://fakestoreapi.com/products"
  );

  if (!response.ok) {
    throw new Response("Failed to fetch products", {
      status: response.status
    });
  }

  return response.json();
}
```

Route configuration:

```jsx
const router = createBrowserRouter([
  {
    path: "/products",
    element: <Products />,
    loader: productsLoader
  }
]);
```

Component:

```jsx
import { useLoaderData } from "react-router-dom";

function Products() {
  const products = useLoaderData();

  return (
    <div>
      {products.map(product => (
        <h3 key={product.id}>
          {product.title}
        </h3>
      ))}
    </div>
  );
}
```

**Advantages:**

1. Centralised data loading.
2. Route-level data management.
3. Better integration with navigation.
4. Error handling through route error boundaries.
5. Supports parallel loading for matched routes where appropriate.

---

### Q53. What is useLoaderData?

**Interview Answer:**

useLoaderData is a React Router hook used to access the data returned by the loader function of the current route.

It allows components to receive route data without manually fetching it through useEffect.

**Example:**

```jsx
function ProductDetails() {
  const product = useLoaderData();

  return (
    <div>
      <h1>{product.title}</h1>
      <p>{product.price}</p>
    </div>
  );
}
```

It must be used within a route rendered by a compatible Data Router.

---

### Q54. What are actions in React Router?

**Interview Answer:**

Actions are functions used to handle data mutations associated with a route.

They are commonly used for operations such as:

- Creating a user.
- Updating a profile.
- Deleting a product.
- Submitting a login form.

While loaders are mainly responsible for reading data, actions are mainly responsible for modifying data.

**Example:**

```jsx
async function contactAction({ request }) {
  const formData = await request.formData();

  const name = formData.get("name");
  const email = formData.get("email");

  const response = await fetch("/api/contact", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ name, email })
  });

  if (!response.ok) {
    throw new Response("Submission failed", {
      status: response.status
    });
  }

  return { success: true };
}
```

Route configuration:

```jsx
{
  path: "/contact",
  element: <Contact />,
  action: contactAction
}
```

---

### Q55. What is Form in React Router?

**Interview Answer:**

Form is a component provided by React Router that enhances normal HTML form submission by integrating it with route actions.

Instead of manually handling every form submission using an onSubmit handler, we can use Form to submit data to the action associated with the current route.

**Example:**

```jsx
import { Form } from "react-router-dom";

function Contact() {
  return (
    <Form method="post">
      <input
        type="text"
        name="name"
        placeholder="Enter name"
      />

      <input
        type="email"
        name="email"
        placeholder="Enter email"
      />

      <button type="submit">
        Submit
      </button>
    </Form>
  );
}
```

When the form is submitted, React Router invokes the matching route action.

---

### Q56. What is useActionData?

**Interview Answer:**

useActionData is a hook used to access the data returned by the most recently completed action for the current route.

It is useful for displaying form submission results, validation errors and success messages.

**Example:**

```jsx
import {
  Form,
  useActionData
} from "react-router-dom";

function Register() {
  const result = useActionData();

  return (
    <div>
      <Form method="post">
        <input name="email" type="email" />

        <button type="submit">
          Register
        </button>
      </Form>

      {result?.error && (
        <p>{result.error}</p>
      )}
    </div>
  );
}
```

The action function can return validation errors that the component displays.

---

### Q57. What is useNavigation?

**Interview Answer:**

useNavigation is a React Router hook used to access the current navigation state.

It helps us understand whether the application is idle, loading or submitting.

It is particularly useful for displaying loading indicators and disabling buttons during form submissions.

**Example:**

```jsx
import {
  Form,
  useNavigation
} from "react-router-dom";

function Login() {
  const navigation = useNavigation();

  const isSubmitting =
    navigation.state === "submitting";

  return (
    <Form method="post">
      <input name="email" />

      <button disabled={isSubmitting}>
        {isSubmitting ? "Logging in..." : "Login"}
      </button>
    </Form>
  );
}
```

**Navigation states:**

| State      | Meaning                                    |
| ---------- | ------------------------------------------ |
| idle       | No navigation or submission is in progress |
| loading    | Route navigation/data loading is happening |
| submitting | A form or mutation is being submitted      |

---

### Q58. What is useSubmit?

**Interview Answer:**

useSubmit is a hook that provides a programmatic way to submit forms or form data to a React Router action.

It is useful when we want to submit data without relying on a normal form submit button.

**Example:**

```jsx
import { useSubmit } from "react-router-dom";

function Search() {
  const submit = useSubmit();

  const handleSearch = (event) => {
    submit(event.currentTarget, {
      method: "get"
    });
  };

  return (
    <form onSubmit={handleSearch}>
      <input name="search" />

      <button type="submit">
        Search
      </button>
    </form>
  );
}
```

---

### Q59. What is useFetcher?

**Interview Answer:**

useFetcher is a React Router hook used to interact with loaders and actions without performing a normal navigation to another route.

It is useful when we want to load or submit data independently while keeping the user on the current page.

For example, we may want to update a product's favorite status without navigating away from the products page.

**Example:**

```jsx
import { useFetcher } from "react-router-dom";

function FavoriteButton({ productId }) {
  const fetcher = useFetcher();

  return (
    <fetcher.Form method="post" action="/favorite">
      <input
        type="hidden"
        name="productId"
        value={productId}
      />

      <button type="submit">
        {fetcher.state === "submitting"
          ? "Updating..."
          : "Add to Favorites"}
      </button>
    </fetcher.Form>
  );
}
```

**Difference between useSubmit and useFetcher:**

useSubmit submits data through the router's navigation mechanism, whereas useFetcher provides an independent interaction that generally does not navigate the user to a new page.

---

### Q60. What is errorElement?

**Interview Answer:**

errorElement is a route configuration property used to display an error UI when an error occurs during route rendering, data loading or action execution.

It allows us to handle errors at the route level rather than allowing the entire application to fail.

**Example:**

```jsx
const router = createBrowserRouter([
  {
    path: "/products",
    element: <Products />,
    loader: productsLoader,
    errorElement: <ErrorPage />
  }
]);
```

If the loader throws an error, React Router renders the ErrorPage for that route branch.

---

### Q61. What is useRouteError?

**Interview Answer:**

useRouteError is a React Router hook used inside a route error element to access the error that was thrown during route loading, action execution or rendering.

**Example:**

```jsx
import { useRouteError } from "react-router-dom";

function ErrorPage() {
  const error = useRouteError();

  return (
    <div>
      <h1>Something went wrong</h1>

      <p>
        {error.statusText || error.message}
      </p>
    </div>
  );
}
```

It helps us display meaningful error information to users.

---

### Q62. What is lazy loading in routing?

**Interview Answer:**

Lazy loading means loading a component or its associated code only when it is needed instead of loading every component during the initial application load.

In React, we can use React.lazy and Suspense for component-level lazy loading.

**Example:**

```jsx
import { lazy, Suspense } from "react";
import {
  BrowserRouter,
  Routes,
  Route
} from "react-router-dom";

const Dashboard = lazy(
  () => import("./Dashboard")
);

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<p>Loading...</p>}>
        <Routes>
          <Route
            path="/dashboard"
            element={<Dashboard />}
          />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

The Dashboard component is loaded when it is needed.

**Benefits:**

- Reduces initial JavaScript bundle size.
- Improves initial loading performance.
- Loads code on demand.
- Useful for large applications.

---

### Q63. What is route-based code splitting?

**Interview Answer:**

Route-based code splitting means dividing the application's JavaScript bundle based on routes.

Instead of downloading all page components when the application starts, we split the code so that each route's component can be loaded when required.

For example, the admin dashboard code does not need to be downloaded immediately when the user visits the public homepage.

We can implement it using React.lazy with Suspense or React Router's route lazy-loading APIs.

**Example using route lazy:**

```jsx
const router = createBrowserRouter([
  {
    path: "/dashboard",
    lazy: async () => {
      const module = await import("./Dashboard");

      return {
        Component: module.default
      };
    }
  }
]);
```

This allows the route module to be loaded when needed.

---

### Q64. What is revalidation in React Router?

**Interview Answer:**

Revalidation means checking whether route data should be loaded again after an operation or navigation.

For example, when a user adds a new product, the product list may need to be refreshed to show the updated data.

React Router can automatically revalidate relevant loaders after actions and certain navigations.

We can also control this behaviour using route configuration such as shouldRevalidate.

**Important:** Revalidation helps keep route data synchronised with the latest application state.

---

### Q65. What is useRevalidator?

**Interview Answer:**

useRevalidator is a React Router hook that allows us to manually trigger route data revalidation.

It is useful when the application needs to refresh loader data without performing normal navigation.

**Example:**

```jsx
import { useRevalidator } from "react-router-dom";

function RefreshButton() {
  const revalidator = useRevalidator();

  return (
    <button
      onClick={() => revalidator.revalidate()}
      disabled={revalidator.state === "loading"}
    >
      Refresh Data
    </button>
  );
}
```

---

# Section 7: Advanced Navigation and Routing

### Q66. What is a wildcard route?

**Interview Answer:**

A wildcard route is a route that uses the `*` character to match remaining URL segments.

It is commonly used for handling unknown routes or creating nested catch-all routes.

**Example:**

```jsx
<Route path="*" element={<NotFound />} />
```

When a user visits a URL that does not match any defined route, the NotFound component is displayed.

---

### Q67. How do you implement a 404 Not Found page?

**Interview Answer:**

We can implement a 404 page by defining a wildcard route using `*`.

It matches URLs that do not match any other configured route.

**Example:**

```jsx
import {
  Routes,
  Route
} from "react-router-dom";

function App() {
  return (
    <Routes>
      <Route path="/" element={<Home />} />

      <Route
        path="/about"
        element={<About />}
      />

      <Route
        path="*"
        element={<NotFound />}
      />
    </Routes>
  );
}
```

NotFound.jsx:

```jsx
import { Link } from "react-router-dom";

function NotFound() {
  return (
    <div>
      <h1>404 - Page Not Found</h1>

      <p>The page you are looking for does not exist.</p>

      <Link to="/">
        Go to Home
      </Link>
    </div>
  );
}

export default NotFound;
```

**Important:** In a Data Router application, we can also handle unmatched routes using an appropriate catch-all route or route-level error handling.

---

### Q68. What is basename in React Router?

**Interview Answer:**

basename is a configuration used to specify the base URL path from which an application is served.

It is useful when deploying a React application inside a subdirectory instead of the domain root.

**Example:**

```jsx
<BrowserRouter basename="/myapp">
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/about" element={<About />} />
  </Routes>
</BrowserRouter>
```

The corresponding URLs become:

```text
/myapp/
/myapp/about
```

It is also supported as a configuration option in createBrowserRouter.

---

### Q69. What is HashRouter?

**Interview Answer:**

HashRouter is a router that uses the URL hash portion to manage navigation.

Instead of using normal pathname-based URLs, it places the route after the `#` symbol.

**Example:**

```text
https://example.com/#/home
https://example.com/#/about
```

It is useful when the hosting environment cannot be configured to support client-side route fallback.

**Example:**

```jsx
import {
  HashRouter,
  Routes,
  Route
} from "react-router-dom";

function App() {
  return (
    <HashRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </HashRouter>
  );
}
```

---

### Q70. What is the difference between BrowserRouter and HashRouter?

**Interview Answer:**

Both BrowserRouter and HashRouter are used for client-side routing, but they manage the URL differently.

| BrowserRouter                          | HashRouter                                        |
| -------------------------------------- | ------------------------------------------------- |
| Uses browser History API               | Uses URL hash                                     |
| Clean URLs                             | URLs contain `#`                                  |
| Requires server fallback configuration | Usually avoids server route fallback requirements |
| Example `/about`                       | Example `/#/about`                                |
| Common in modern web applications      | Useful for limited hosting environments           |

---

### Q71. What is MemoryRouter?

**Interview Answer:**

MemoryRouter is a router that stores navigation history in memory instead of using the browser's URL.

It is useful for environments where browser URL navigation is not required, such as certain tests and non-browser environments.

**Example:**

```jsx
import {
  MemoryRouter,
  Routes,
  Route
} from "react-router-dom";

function App() {
  return (
    <MemoryRouter initialEntries={["/home"]}>
      <Routes>
        <Route path="/home" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </MemoryRouter>
  );
}
```

The current route is maintained internally rather than being reflected in the browser address bar.

---

### Q72. What is ScrollRestoration?

**Interview Answer:**

ScrollRestoration is a React Router component used to manage and restore the scroll position when users navigate between routes.

For example, if a user scrolls down a product listing page, opens a product and then returns, scroll restoration can help restore the previous scroll position.

It is commonly used with Data Router applications.

**Example:**

```jsx
import {
  Outlet,
  ScrollRestoration
} from "react-router-dom";

function RootLayout() {
  return (
    <>
      <Navbar />

      <Outlet />

      <ScrollRestoration />
    </>
  );
}
```

It uses location information to manage scroll positions across navigation.

---

### Q73. What is useBlocker?

**Interview Answer:**

useBlocker is a React Router hook used to prevent or delay certain client-side navigations based on a condition.

It is useful when a user has unsaved form changes and attempts to leave the current page.

We can show a confirmation message and allow the user to decide whether to stay or continue.

**Conceptual example:**

```jsx
import {
  useBlocker
} from "react-router-dom";

function EditProfile() {
  const blocker = useBlocker(
    ({ currentLocation, nextLocation }) =>
      currentLocation.pathname !== nextLocation.pathname
  );

  return (
    <div>
      <h1>Edit Profile</h1>

      {blocker.state === "blocked" && (
        <div>
          <p>You have unsaved changes.</p>

          <button onClick={() => blocker.proceed()}>
            Leave Page
          </button>

          <button onClick={() => blocker.reset()}>
            Stay
          </button>
        </div>
      )}
    </div>
  );
}
```

**Important:** This example blocks navigation whenever the pathname changes. In a real application, the condition should check whether the form actually contains unsaved changes.

useBlocker handles client-side navigation blocking. Browser unload events require separate handling.

---

### Q74. What is useNavigationType?

**Interview Answer:**

useNavigationType is a hook that returns the type of navigation that resulted in the current location.

It can return:

- `POP`
- `PUSH`
- `REPLACE`

**Explanation:**

| Type    | Meaning                                                        |
| ------- | -------------------------------------------------------------- |
| POP     | Navigation through browser history, including back and forward |
| PUSH    | A new history entry was added                                  |
| REPLACE | The current history entry was replaced                         |

**Example:**

```jsx
import { useNavigationType } from "react-router-dom";

function Page() {
  const navigationType = useNavigationType();

  return (
    <p>Navigation: {navigationType}</p>
  );
}
```

It is useful for understanding navigation behaviour and implementing certain analytics or navigation-related features.

---

### Q75. How do you handle query strings and filters?

**Interview Answer:**

I use useSearchParams to store filter and search information in the URL.

For example, in a product listing page, I may have category, price and page filters.

Instead of storing everything only in component state, I can maintain these values in the URL.

This provides shareable URLs and allows users to refresh the page without losing their selected filters.

**Example:**

```jsx
import { useSearchParams } from "react-router-dom";

function ProductFilter() {
  const [searchParams, setSearchParams] =
    useSearchParams();

  const category =
    searchParams.get("category") || "";

  const handleCategoryChange = (event) => {
    const nextParams = new URLSearchParams(searchParams);

    nextParams.set("category", event.target.value);

    setSearchParams(nextParams);
  };

  return (
    <select
      value={category}
      onChange={handleCategoryChange}
    >
      <option value="">All</option>
      <option value="electronics">Electronics</option>
      <option value="clothing">Clothing</option>
    </select>
  );
}
```

---

# Section 8: React Router v6 vs v7

### Q76. What are the major differences between React Router v6 and v7?

**Interview Answer:**

React Router v7 is the next major version after v6. It introduces a simplified package structure and continues improving its data and framework capabilities.

The main differences include:

| React Router v6                         | React Router v7                                   |
| --------------------------------------- | ------------------------------------------------- |
| Commonly imported from react-router-dom | Web APIs can be imported from react-router        |
| Supports declarative routing            | Supports declarative, data and framework modes    |
| Uses Routes and Route                   | Continues supporting Routes and Route             |
| Supports createBrowserRouter            | Continues supporting createBrowserRouter          |
| Earlier v6 APIs and conventions         | Additional modern APIs and migration improvements |

React Router v7 also requires React 18 or later.

**Important:** React Router v7 is designed as an upgrade from v6. Many familiar APIs, including useNavigate, useParams, Link, Outlet and Routes, remain available.

---

### Q77. What happened to Switch in React Router v6?

**Interview Answer:**

In React Router v5, we used the Switch component to render the first matching route.

In React Router v6, Switch was replaced by Routes.

Routes uses route ranking to select the best matching route instead of relying on the previous first-match behaviour.

**React Router v5:**

```jsx
<Switch>
  <Route path="/about" component={About} />
</Switch>
```

**React Router v6/v7:**

```jsx
<Routes>
  <Route
    path="/about"
    element={<About />}
  />
</Routes>
```

---

### Q78. Why is exact no longer required in React Router v6?

**Interview Answer:**

In React Router v5, we sometimes used the exact property to ensure that a route matched only the exact URL.

In React Router v6, route matching was redesigned to use ranking and more predictable matching rules.

Therefore, exact is no longer required.

**React Router v5:**

```jsx
<Route
  exact
  path="/"
  component={Home}
/>
```

**React Router v6/v7:**

```jsx
<Route
  path="/"
  element={<Home />}
/>
```

---

### Q79. What is the difference between element and Component in route configuration?

**Interview Answer:**

In declarative routing, we commonly use the `element` property to provide a React element.

In route object configuration, React Router also supports the `Component` property to specify a component type.

**Using element:**

```jsx
{
  path: "/home",
  element: <Home />
}
```

**Using Component:**

```jsx
{
  path: "/home",
  Component: Home
}
```

The main distinction is that element receives an already-created React element, while Component receives a component type.

In Data Router configuration, both are supported, but we should avoid defining both for the same route.

---

### Q80. What is the difference between declarative, data and framework routing?

**Interview Answer:**

React Router supports different ways of building applications.

**1. Declarative Routing**

We define routes using BrowserRouter, Routes and Route.

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
  </Routes>
</BrowserRouter>
```

It is suitable for applications where we mainly need navigation and route rendering.

**2. Data Routing**

We use createBrowserRouter and RouterProvider with loaders, actions and route-level error handling.

```jsx
const router = createBrowserRouter([
  {
    path: "/products",
    element: <Products />,
    loader: productsLoader
  }
]);
```

It is suitable for applications that need integrated route-level data loading and mutations.

**3. Framework Routing**

React Router's framework mode provides a more integrated application architecture with route modules, server rendering capabilities, data loading, actions and code splitting.

It is suitable for larger applications that need full application-level routing and rendering infrastructure.

---

### Q81. Can React Router be used without BrowserRouter?

**Interview Answer:**

Yes. React Router provides different router implementations depending on the application requirements.

For example:

- HashRouter uses URL hashes.
- MemoryRouter stores navigation history in memory.
- createBrowserRouter uses browser-based route configuration with RouterProvider.

We do not always need BrowserRouter specifically, but components using React Router hooks must be rendered within an appropriate router context.

---

# Section 9: Practical and Scenario-Based Interview Questions

### Q82. How would you create a navbar that highlights the active page?

**Interview Answer:**

I would use NavLink because it provides an `isActive` property that tells us whether the current route matches the navigation link.

**Example:**

```jsx
import { NavLink } from "react-router-dom";

function Navbar() {
  return (
    <nav>
      <NavLink
        to="/"
        end
        className={({ isActive }) =>
          isActive ? "active" : ""
        }
      >
        Home
      </NavLink>

      <NavLink
        to="/courses"
        className={({ isActive }) =>
          isActive ? "active" : ""
        }
      >
        Courses
      </NavLink>

      <NavLink
        to="/about"
        className={({ isActive }) =>
          isActive ? "active" : ""
        }
      >
        About
      </NavLink>
    </nav>
  );
}
```

**CSS:**

```css
nav {
  display: flex;
  gap: 20px;
}

.active {
  color: blue;
  border-bottom: 2px solid blue;
}
```

The `end` property ensures that the Home link is active only when the URL matches `/` exactly.

---

### Q83. How would you redirect users after successful login?

**Interview Answer:**

I would use the useNavigate hook.

After the login API returns a successful response, I would update the authentication state and navigate the user to the dashboard.

**Example:**

```jsx
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();

  const handleLogin = async () => {
    try {
      // Example login API call
      const response = await loginUser();

      if (response.success) {
        navigate("/dashboard", {
          replace: true
        });
      }
    } catch (error) {
      console.error(error);
    }
  };

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

In a real application, the authentication state should also be updated through Context API or another appropriate state-management solution.

---

### Q84. How would you redirect users to the page they originally wanted to visit?

**Interview Answer:**

I would store the originally requested location when redirecting an unauthenticated user to the login page.

After successful login, I would navigate the user back to that location.

**ProtectedRoute.jsx:**

```jsx
import {
  Navigate,
  Outlet,
  useLocation
} from "react-router-dom";

function ProtectedRoute() {
  const isAuthenticated = false;
  const location = useLocation();

  if (!isAuthenticated) {
    return (
      <Navigate
        to="/login"
        state={{ from: location }}
        replace
      />
    );
  }

  return <Outlet />;
}
```

**Login.jsx:**

```jsx
import {
  useLocation,
  useNavigate
} from "react-router-dom";

function Login() {
  const navigate = useNavigate();
  const location = useLocation();

  const from = location.state?.from;

  const handleLogin = async () => {
    const success = true;

    if (success) {
      const destination =
        from?.pathname
          ? `${from.pathname}${from.search || ""}${from.hash || ""}`
          : "/dashboard";

      navigate(destination, {
        replace: true
      });
    }
  };

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

**Important:** In production, validate any redirect destination and restrict redirects to trusted internal paths to prevent open-redirect vulnerabilities.

---

### Q85. How would you implement an admin dashboard with nested pages?

**Interview Answer:**

I would create an AdminLayout component containing the sidebar, navbar and Outlet.

Then I would define child routes for Dashboard, Users, Products and Settings.

This avoids repeating the common layout for every admin page.

**AdminLayout.jsx:**

```jsx
import { Outlet, NavLink } from "react-router-dom";

function AdminLayout() {
  return (
    <div className="admin-layout">

      <aside>
        <NavLink to="/admin">
          Dashboard
        </NavLink>

        <NavLink to="/admin/users">
          Users
        </NavLink>

        <NavLink to="/admin/products">
          Products
        </NavLink>

        <NavLink to="/admin/settings">
          Settings
        </NavLink>
      </aside>

      <main>
        <Outlet />
      </main>

    </div>
  );
}
```

**App.jsx:**

```jsx
<Routes>
  <Route
    path="/admin"
    element={<AdminLayout />}
  >
    <Route
      index
      element={<Dashboard />}
    />

    <Route
      path="users"
      element={<Users />}
    />

    <Route
      path="products"
      element={<Products />}
    />

    <Route
      path="settings"
      element={<Settings />}
    />
  </Route>
</Routes>
```

The admin layout remains visible while the child route changes.

---

### Q86. How would you prevent an authenticated user from opening the login page?

**Interview Answer:**

I would create a public-only route component that checks the authentication state.

If the user is already authenticated, I would redirect them to the dashboard.

Otherwise, I would allow them to access the login page.

**Example:**

```jsx
import {
  Navigate,
  Outlet
} from "react-router-dom";

function GuestRoute() {
  const isAuthenticated = false;

  return isAuthenticated
    ? <Navigate to="/dashboard" replace />
    : <Outlet />;
}
```

**Route configuration:**

```jsx
<Routes>
  <Route element={<GuestRoute />}>
    <Route
      path="/login"
      element={<Login />}
    />

    <Route
      path="/register"
      element={<Register />}
    />
  </Route>
</Routes>
```

---

### Q87. How would you implement pagination using URL parameters?

**Interview Answer:**

I would use useSearchParams to maintain the current page number in the URL.

This makes pagination shareable and allows users to refresh the page without losing their current page.

**Example:**

```jsx
import { useSearchParams } from "react-router-dom";

function Pagination() {
  const [searchParams, setSearchParams] =
    useSearchParams();

  const currentPage = Number(
    searchParams.get("page") || 1
  );

  const handleNext = () => {
    setSearchParams({
      page: String(currentPage + 1)
    });
  };

  const handlePrevious = () => {
    if (currentPage > 1) {
      setSearchParams({
        page: String(currentPage - 1)
      });
    }
  };

  return (
    <div>
      <button
        onClick={handlePrevious}
        disabled={currentPage <= 1}
      >
        Previous
      </button>

      <span>{currentPage}</span>

      <button onClick={handleNext}>
        Next
      </button>
    </div>
  );
}
```

In a complete implementation, I would also validate page numbers and request the corresponding data from the API.

---

### Q88. How would you handle invalid route parameters?

**Interview Answer:**

I would validate the route parameter before making an API request.

For example, if the route is `/products/:id`, I would verify that the ID has the expected format.

If the parameter is invalid or the requested resource does not exist, I would display an appropriate error or 404 page.

**Example:**

```jsx
import { useParams } from "react-router-dom";

function ProductDetails() {
  const { id } = useParams();

  if (!/^\d+$/.test(id || "")) {
    return <h1>Invalid Product ID</h1>;
  }

  return <h1>Product ID: {id}</h1>;
}
```

For production applications, validation should also happen on the backend.

---

### Q89. How would you implement breadcrumbs?

**Interview Answer:**

Breadcrumbs help users understand their current location within an application hierarchy.

I would use the current route information and route metadata to generate navigation links.

React Router supports route handles and useMatches in Data Router applications, which can be used to implement route-aware breadcrumbs.

**Example:**

```jsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      {
        path: "products",
        element: <Products />,
        handle: {
          breadcrumb: "Products"
        }
      },
      {
        path: "products/:id",
        element: <ProductDetails />,
        handle: {
          breadcrumb: "Product Details"
        }
      }
    ]
  }
]);
```

The layout can use `useMatches()` to read matched route metadata and render the breadcrumb trail.

---

### Q90. How would you troubleshoot a blank page after refreshing a route?

**Interview Answer:**

First, I would check the browser console and network tab to identify whether the issue is related to routing, JavaScript errors or server configuration.

If the application works when navigating internally but fails after refreshing a nested route, I would check the server configuration.

With BrowserRouter, the server must return the React application's entry HTML for valid client-side routes.

For example, when a user refreshes `/dashboard`, the server should serve the application entry point instead of returning a server-side 404.

I would also check:

1. Whether the route is configured correctly.
2. Whether the component is imported correctly.
3. Whether BrowserRouter is configured.
4. Whether the deployment platform supports SPA fallback.
5. Whether the browser console shows runtime errors.

---

### Q91. How would you troubleshoot navigation not working?

**Interview Answer:**

I would debug the issue step by step.

First, I would verify that the route is correctly defined.

Then I would check whether the navigation path matches the configured route.

I would also verify that the component is rendered within the router context.

Other things I would check include:

1. Incorrect import statements.
2. Missing BrowserRouter or RouterProvider.
3. Incorrect use of Link or NavLink.
4. Incorrect nested route configuration.
5. Missing Outlet in the parent layout.
6. Incorrect dynamic parameter names.
7. JavaScript errors preventing rendering.
8. Incorrect route paths or navigation destinations.

---

### Q92. What are the common mistakes developers make while implementing routing?

**Interview Answer:**

Some common mistakes are:

1. Using normal anchor tags for internal navigation when client-side navigation is intended.
2. Forgetting to wrap the application in a router.
3. Forgetting to use Outlet in a nested layout.
4. Defining incorrect route paths.
5. Calling routing hooks outside router context.
6. Forgetting to configure server fallback for BrowserRouter.
7. Not handling unknown routes.
8. Storing sensitive authentication information in insecure browser storage.
9. Relying only on frontend protected routes for security.
10. Not handling loading and error states.
11. Not validating dynamic route parameters.
12. Not considering refresh behaviour and browser history.

---

# Important Comparison Tables – Quick Revision

## 1. Link vs NavLink vs useNavigate

| Feature                 | Link         | NavLink          | useNavigate            |
| ----------------------- | ------------ | ---------------- | ---------------------- |
| Type                    | Component    | Component        | Hook                   |
| Navigation              | Yes          | Yes              | Yes                    |
| Active state            | No           | Yes              | No                     |
| Programmatic navigation | No           | No               | Yes                    |
| Main use                | Normal links | Navigation menus | Conditional navigation |

## 2. useParams vs useSearchParams vs useLocation

| Hook            | Purpose                             |
| --------------- | ----------------------------------- |
| useParams       | Access dynamic route parameters     |
| useSearchParams | Read and update query parameters    |
| useLocation     | Access current location information |

## 3. BrowserRouter vs HashRouter vs MemoryRouter

| Router        | URL handling      | Common use                             |
| ------------- | ----------------- | -------------------------------------- |
| BrowserRouter | History API       | Standard web applications              |
| HashRouter    | Hash-based URLs   | Hosting without route fallback support |
| MemoryRouter  | In-memory history | Testing and non-browser environments   |

## 4. Navigate vs useNavigate

| Navigate                       | useNavigate                      |
| ------------------------------ | -------------------------------- |
| Component                      | Hook                             |
| Declarative                    | Programmatic                     |
| Used for conditional redirects | Used for event-driven navigation |

## 5. Loader vs Action

| Loader                             | Action                                        |
| ---------------------------------- | --------------------------------------------- |
| Reads or prepares route data       | Handles route mutations                       |
| Runs during relevant route loading | Runs on supported submissions                 |
| Used for fetching data             | Used for creating, updating and deleting data |
| Accessed using useLoaderData       | Results can be accessed using useActionData   |

## 6. BrowserRouter vs createBrowserRouter

| BrowserRouter           | createBrowserRouter |
| ----------------------- | ------------------- |
| Component               | Function            |
| Declarative route setup | Route object setup  |
| Routes and Route        | RouterProvider      |
| Basic routing           | Data Router APIs    |

---

# Important Interview Traps

### 1. Does Link reload the entire page?

No. Link performs client-side navigation for internal routes.

### 2. Does useNavigate work outside a Router?

No. It requires an appropriate router context.

### 3. Can we use useParams without dynamic routes?

It can be called within router context, but it only provides parameters matched from the current route branch.

### 4. Is Outlet mandatory in nested routing?

If we want child route elements to appear inside a parent layout component, we need to render Outlet.

### 5. Does Navigate perform programmatic navigation?

Navigate performs declarative navigation when rendered. useNavigate is used for programmatic navigation.

### 6. Does BrowserRouter automatically configure the server?

No. Server fallback configuration must be handled separately.

### 7. Are protected routes a security mechanism?

They provide frontend access control and navigation behaviour, but backend authorization is essential.

### 8. Does useSearchParams return a normal JavaScript object?

No. It returns a URLSearchParams instance and a setter function.

### 9. Can loaders and actions be used with ordinary BrowserRouter and Routes?

The route-level Data Router APIs require a Data Router setup, such as createBrowserRouter with RouterProvider.

### 10. Does React Router automatically fetch API data?

No. Loaders provide a mechanism for defining data-loading functions, but we must implement the actual data-fetching logic.

---

# Practical Coding Questions to Practice

These are important implementation tasks that interviewers may ask you to build.

1. Create a React application with Home, About and Contact pages.
2. Create a responsive navbar using NavLink.
3. Implement dynamic product details routing.
4. Pass product IDs through URL parameters.
5. Implement search and filter functionality using query parameters.
6. Create nested routing for an admin dashboard.
7. Implement multiple layouts using Outlet.
8. Create an authentication-based protected route.
9. Implement admin and user role-based routing.
10. Redirect users after successful login.
11. Redirect users back to their originally requested page after login.
12. Prevent authenticated users from accessing login and registration pages.
13. Implement a 404 Not Found page.
14. Implement route-based lazy loading.
15. Fetch data using React Router loaders.
16. Submit forms using React Router actions.
17. Display loading states using useNavigation.
18. Implement a route-level error page.
19. Implement pagination using useSearchParams.
20. Implement a confirmation dialog for unsaved form changes using useBlocker.

---

# Final Interview Preparation Checklist

Use this checklist to evaluate your preparation.

## Fundamentals

- [ ] React Router
- [ ] SPA routing
- [ ] BrowserRouter
- [ ] Routes and Route
- [ ] Client-side navigation

## Navigation

- [ ] Link
- [ ] NavLink
- [ ] useNavigate
- [ ] Navigate
- [ ] Browser history
- [ ] replace option

## URL Handling

- [ ] Dynamic routes
- [ ] useParams
- [ ] useSearchParams
- [ ] useLocation
- [ ] Location state
- [ ] Query strings

## Nested Routing

- [ ] Nested routes
- [ ] Outlet
- [ ] Index routes
- [ ] Layout routes
- [ ] Multiple layouts
- [ ] Absolute and relative paths

## Authentication

- [ ] Protected routes
- [ ] Authentication
- [ ] Authorization
- [ ] Role-based routing
- [ ] Login redirection
- [ ] Logout
- [ ] Backend authorization

## Advanced Concepts

- [ ] createBrowserRouter
- [ ] RouterProvider
- [ ] Loaders
- [ ] Actions
- [ ] useLoaderData
- [ ] useActionData
- [ ] useNavigation
- [ ] useSubmit
- [ ] useFetcher
- [ ] errorElement
- [ ] Lazy loading
- [ ] Revalidation
- [ ] ScrollRestoration
- [ ] useBlocker

## Version Differences

- [ ] React Router v5 vs v6
- [ ] React Router v6 vs v7
- [ ] Switch vs Routes
- [ ] BrowserRouter vs createBrowserRouter
- [ ] Declarative vs Data vs Framework mode

---

# React Router – Complete Learning Flow

Follow this order while learning React Router. Complete the concepts and coding practice first. Attempt the interview questions only after finishing the learning flow.

## Phase 1: Understand and Set Up React Router

### 1. React Router Fundamentals
- What is routing?
- What is React Router?
- Why do we need routing in React?
- What is a Single Page Application (SPA)?
- SPA vs MPA
- Traditional routing vs client-side routing
- How client-side routing works
- What happens when the URL changes?

### 2. Installation and Project Setup
- Install React Router
- Understand the `react-router` and `react-router-dom` package names for the version being used
- Set up a basic React application
- Understand where routing is configured (`main.jsx` or `App.jsx`)
- Importing the required router components

### 3. Router Components and Router Setup
- What is a router?
- What is `BrowserRouter`?
- Wrap the application with `BrowserRouter`
- What is `RouterProvider`?
- What is `createBrowserRouter()`?
- `BrowserRouter` vs `RouterProvider` (two different setup approaches)
- Understand the basic router component hierarchy

### 4. Creating Basic Routes
- What is a route?
- What is `Routes`?
- What is `Route`?
- `path` and `element` props
- Create Home, About and Contact routes
- Route matching basics
- Default/home route
- Index route (introductory understanding)
- Wildcard route (`*`)
- Create a 404 Not Found page

## Phase 2: Navigate Between Pages

### 5. Link and NavLink
- What is client-side navigation?
- What is the `Link` component?
- `Link` vs HTML anchor (`<a>`) tag
- `to` prop
- Absolute vs relative navigation paths
- What is the `NavLink` component?
- `NavLink` vs `Link`
- Active link styling
- `isActive`, `isPending` and `end`

### 6. Build Navigation UI
- Create a Navbar using `Link` and `NavLink`
- Highlight the active page
- Create a Sidebar
- Navigate between pages without a full-page reload
- Organize navigation links in reusable components

### 7. Programmatic Navigation with `useNavigate`
- What is programmatic navigation?
- What is `useNavigate()`?
- Navigate after a button click
- Navigate after form submission
- Conditional navigation
- Navigate backward and forward (`navigate(-1)`, `navigate(1)`)
- `replace` vs normal navigation
- What is the `Navigate` component?
- `Navigate` vs `useNavigate()`

## Phase 3: Understand URL Data and Router Hooks

### 8. Dynamic Routing and Route Parameters
- What is dynamic routing?
- Static routes vs dynamic routes
- Dynamic route segments (for example, `/products/:productId`)
- What are route parameters?
- What is `useParams()`?
- Read parameters inside a component
- Multiple route parameters
- Splat/wildcard route parameters
- Optional segments (check support in the installed version)
- Build a product listing and product details page

### 9. Location and Navigation Hooks
- What are React Router hooks?
- What is `useLocation()`?
- Understand `pathname`, `search`, `hash`, `state` and `key`
- What is `useNavigationType()`?
- What is `useMatch()`?
- When and why to use each hook

### 10. Passing Data Between Routes
- Pass state using `Link`
- Pass state using `useNavigate()`
- Read route state using `useLocation()`
- URL parameters vs location state
- When to use URL data and when to use navigation state

### 11. Query Parameters and Search Params
- What is a query string?
- Path parameters vs query parameters
- Understand `URLSearchParams`
- What is `useSearchParams()`?
- Read query parameters
- Update and delete query parameters
- Keep search/filter state in the URL
- Implement search, filtering, sorting and pagination

## Phase 4: Structure Routes and Build Layouts

### 12. Nested Routing
- What is nested routing?
- Parent routes and child routes
- What is `Outlet`?
- Render child routes using `Outlet`
- Index routes
- Pathless layout routes
- Relative child paths
- `useOutlet()` and `useOutletContext()`

### 13. Layout Routing
- What is a layout component?
- Shared layouts
- Layout routes with `Outlet`
- Public layout
- Dashboard layout
- Admin layout
- Nested Navbar and Sidebar layouts
- Build a dashboard with multiple child pages

## Phase 5: Authentication and Route Protection

### 14. Authentication and Protected Routes
- Authentication vs authorization
- Public routes vs private routes
- What is a protected route?
- Create a `ProtectedRoute` component
- Redirect unauthenticated users using `Navigate`
- Preserve the page the user originally wanted to visit
- Redirect after successful login
- Prevent authenticated users from opening login/register pages
- Logout and redirect flow
- Role-based routing
- Admin-only routes
- Handle expired sessions/tokens
- Why frontend route protection is not a replacement for backend authorization

## Phase 6: Data Routers and Advanced Features

### 15. Data Router Setup
- What is data router routing?
- `createBrowserRouter()`
- `RouterProvider`
- Route objects
- `createRoutesFromElements()`
- Declarative routing vs data routing

### 16. Loaders and Loading States
- What is a loader?
- Define a loader on a route
- Load data before rendering a route
- What is `useLoaderData()`?
- Handle loader errors
- What is `useNavigation()`?
- Show pending/loading UI during navigation

### 17. Actions and Form Submissions
- What are route actions?
- What is React Router's `Form` component?
- Submit form data to an action
- What is `useActionData()`?
- What is `useSubmit()`?
- What is `useFetcher()`?
- `Form` vs `useFetcher()`
- Data revalidation after mutations
- What is `useRevalidator()`?

### 18. Route Error Handling
- What is `errorElement` / route error boundary?
- What is `useRouteError()`?
- What is `isRouteErrorResponse()`?
- Handle loader and action errors
- Handle route-not-found errors
- Build a route-level error page

### 19. Lazy Loading and Performance
- What is lazy loading?
- `React.lazy()` and `Suspense`
- Route-based code splitting
- Lazy route modules
- Reduce the initial JavaScript bundle
- Prefetching concepts (where supported)

## Phase 7: Advanced Navigation, Deployment and Testing

### 20. Browser History and Advanced Navigation
- Understand the browser history stack
- `pushState()` and `replaceState()` concepts
- Push vs replace navigation
- Back and forward navigation
- What is `useBlocker()`?
- What is `useBeforeUnload()`?
- Prevent accidental navigation away from unsaved forms
- What is `ScrollRestoration`?

### 21. Router Types and Configuration
- `BrowserRouter`
- `HashRouter`
- `MemoryRouter`
- `StaticRouter` (server-rendering context)
- `basename`
- `BrowserRouter` vs `HashRouter`
- When to use each router type

### 22. Deployment and Production Issues
- Deploy an SPA using BrowserRouter
- Configure server-side fallback/rewrites
- Handle refresh on nested URLs
- Fix 404 errors after refreshing a route
- Deployment considerations for Vercel and Netlify
- Common production routing problems

### 23. Testing React Router
- Why router testing is needed
- Test with `MemoryRouter`
- Test with `createMemoryRouter()`
- Test navigation and active links
- Test dynamic routes and URL parameters
- Test protected routes
- React Testing Library integration

### 24. React Router Version Differences
- React Router v5 vs v6
- React Router v6 vs v7
- `Switch` vs `Routes`
- `useHistory()` vs `useNavigate()`
- `Redirect` vs `Navigate`
- `component` / `render` vs `element` / `Component`
- `BrowserRouter` vs `createBrowserRouter()`
- Declarative vs Data vs Framework mode
- Common migration mistakes

## Phase 8: Practical Coding Sequence

Build these projects in order. Do not move to the next project until you can build the current one without copying the entire solution.

1. **Basic Multi-Page App** – `BrowserRouter`, `Routes`, `Route`, `Link` and `NavLink`.
2. **Navigation Bar** – active links, reusable Navbar and Sidebar.
3. **Product App** – dynamic product routes, `useParams()` and product details.
4. **Searchable Product App** – `useSearchParams()`, search, filters and pagination.
5. **Dashboard App** – nested routes, `Outlet`, index routes and shared layouts.
6. **Authentication App** – login, logout, protected routes and redirects.
7. **Role-Based Admin App** – user/admin route access and nested admin pages.
8. **Data Router App** – loaders, actions, forms, pending UI and error boundaries.
9. **Production-Ready App** – lazy loading, deployment fallback and route testing.

## How to Study Each Topic

For every topic, follow this sequence:

1. Understand what the concept is and why it is needed.
2. Learn its syntax, props, hooks and working.
3. Trace what happens step by step when it runs.
4. Study a small practical example.
5. Write the code yourself without copying.
6. Test edge cases and common mistakes.
7. Make short revision notes.

## When to Start Interview Questions

Start the interview questions in the Table of Contents only after completing the learning phases and practical projects. Then practise in this order:

1. Basic concept questions.
2. Syntax and difference-based questions.
3. Hooks and implementation questions.
4. Advanced and version-specific questions.
5. Scenario-based and debugging questions.

**Important:** Learn `BrowserRouter` with the declarative `Routes`/`Route` approach first. Then learn `createBrowserRouter()` with `RouterProvider` as the data-router approach. They are alternative setup patterns; you do not need to wrap one inside the other.

---

**End of Chapter 3 – React Routing**

**Next Chapter: Chapter 4 – React State Management (useState, Lifting State Up, Prop Drilling, Context API, Redux Toolkit, Zustand and Advanced State Management).**
