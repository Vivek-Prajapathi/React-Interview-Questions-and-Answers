# React Router – Complete Version-Wise Guide (v1 to v7)

A detailed guide explaining the evolution of React Router, its architecture, APIs, hooks, routing patterns, major changes, migration strategies, and modern features.

---

## Table of Contents

1. [Introduction to React Router](#1-introduction-to-react-router)
2. [React Router Version History](#2-react-router-version-history)
3. [React Router v1, v2 and v3](#3-react-router-v1-v2-and-v3)
4. [React Router v4](#4-react-router-v4)
5. [React Router v5](#5-react-router-v5)
6. [React Router v6](#6-react-router-v6)
7. [React Router v7](#7-react-router-v7)
8. [Version-Wise Comparison](#8-version-wise-comparison)
9. [Important Hooks Across Versions](#9-important-hooks-across-versions)
10. [Important Components Across Versions](#10-important-components-across-versions)
11. [Routing Architecture: Declarative, Data and Framework Modes](#11-routing-architecture-declarative-data-and-framework-modes)
12. [Migration from v5 to v6](#12-migration-from-v5-to-v6)
13. [Migration from v6 to v7](#13-migration-from-v6-to-v7)
14. [Choosing the Right Router](#14-choosing-the-right-router)
15. [Best Practices](#15-best-practices)
16. [Official Documentation](#16-official-documentation)

---

# 1. Introduction to React Router

React Router is a routing library for React applications. It allows developers to build Single Page Applications (SPAs) with multiple views and URLs without performing a full browser-page reload for every navigation.

### Why do we need React Router?

React itself is a UI library. It does not provide a complete built-in routing system.

For example, consider an application with these pages:

- `/` – Home
- `/about` – About
- `/courses` – Courses
- `/login` – Login
- `/dashboard` – Dashboard
- `/profile` – Profile

Without a routing library, we would have to manually check the browser URL and decide which component to display.

React Router handles this URL-to-component relationship for us.

### How does routing work?

1. The user visits a URL.
2. React Router reads the current location.
3. It compares the URL with the configured routes.
4. It identifies the matching route.
5. It renders the corresponding React component.
6. Navigation can update the URL without a full page reload.

### Traditional navigation vs client-side navigation

**Traditional navigation:**

- Browser requests a new HTML document.
- The server responds with a new page.
- The browser reloads the document.

**Client-side navigation:**

- React Router intercepts navigation.
- The URL is updated using browser history.
- React renders the matching route.
- The entire document does not need to reload.

---

# 2. React Router Version History

React Router has evolved significantly over time.

| Version | Major development |
|---|---|
| v1 | Early routing architecture |
| v2 | Improved route configuration and nested routes |
| v3 | Mature route configuration and history integration |
| v4 | Complete redesign using declarative React components |
| v5 | Improved hooks, route matching and compatibility |
| v6 | Simplified routing, nested layouts, `Routes`, `useNavigate`, data APIs |
| v7 | Unified modern APIs, framework capabilities, type safety and improved data routing |

The most important versions for modern React development are v5, v6 and v7.

---

# 3. React Router v1, v2 and v3

These are legacy versions. They are important for understanding the history of React Router, but they are not recommended for new applications.

## 3.1 React Router v1

React Router initially provided routing through route configuration.

The application defined paths and associated components.

Conceptually, the structure looked like this:

```jsx
<Router>
  <Route path="/" component={Home} />
  <Route path="/about" component={About} />
</Router>
```

The library evolved through several early releases, so exact APIs and syntax varied between minor versions.

### Main ideas

- URL-based component rendering.
- Route configuration.
- Browser history integration.
- Navigation between views.

## 3.2 React Router v2

React Router v2 improved route configuration and introduced more mature nested routing patterns.

### Important concepts

**Nested routes**

A parent route could contain child routes.

For example:

```text
/dashboard
    /profile
    /settings
```

The dashboard acted as the parent route, while profile and settings were child routes.

**Route parameters**

Dynamic values could be captured from URLs.

Example:

```text
/users/:userId
```

A URL such as `/users/101` could identify user ID `101`.

**History management**

Applications could use browser history or hash-based navigation.

## 3.3 React Router v3

Version 3 continued the route-configuration approach and provided a more mature API for nested routes and application navigation.

### Important features

- Nested route configuration.
- Dynamic route parameters.
- Route transitions.
- Browser history integration.
- Redirects.
- Route-based component rendering.

### Legacy syntax example

Older React Router versions used APIs such as:

```jsx
<Router history={browserHistory}>
  <Route path="/" component={App}>
    <Route path="about" component={About} />
  </Route>
</Router>
```

This is historical syntax. Do not copy it into a modern v6 or v7 application.

### Why were these versions redesigned?

As React evolved, the React Router team moved toward a more declarative design.

Instead of configuring routing separately from the component tree, version 4 made routing itself a composition of React components.

---

# 4. React Router v4

React Router v4 introduced a major architectural redesign.

It was released around the time React applications increasingly adopted component-based composition.

## 4.1 Declarative routing

In v4, routes became React components.

Instead of describing the complete route tree in one central configuration, developers could place route definitions inside components.

Example:

```jsx
import {
  BrowserRouter,
  Route,
  Switch
} from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Switch>
        <Route exact path="/" component={Home} />
        <Route path="/about" component={About} />
      </Switch>
    </BrowserRouter>
  );
}
```

### Explanation

- `BrowserRouter`: Uses the browser History API.
- `Switch`: Renders the first matching route.
- `Route`: Connects a URL pattern to a component.
- `exact`: Requires an exact path match.

## 4.2 BrowserRouter

`BrowserRouter` provides routing functionality using the browser's History API.

It allows navigation using normal-looking URLs.

```jsx
<BrowserRouter>
  <App />
</BrowserRouter>
```

## 4.3 HashRouter

`HashRouter` stores the route in the URL hash.

Example:

```text
https://example.com/#/about
```

Usage:

```jsx
import { HashRouter } from "react-router-dom";

function App() {
  return (
    <HashRouter>
      <Routes />
    </HashRouter>
  );
}
```

The example demonstrates the router concept; `Routes` here represents your own component, not the modern `Routes` component.

## 4.4 Switch

`Switch` checks its child routes and renders the first matching route.

```jsx
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/contact" component={Contact} />
</Switch>
```

**Important:** `Switch` belongs to the v4/v5 API. In v6 and v7, use `Routes`.

## 4.5 Route

A `Route` connects a path to a component.

```jsx
<Route path="/profile" component={Profile} />
```

In v4/v5, the `component` prop was commonly used.

## 4.6 Route parameters

```jsx
<Route path="/users/:id" component={UserDetails} />
```

The matched parameter could be accessed through the route props, such as `match.params.id`.

## 4.7 Navigation with Link

```jsx
import { Link } from "react-router-dom";

<Link to="/about">About</Link>
```

`Link` enables client-side navigation without a normal full-page anchor navigation.

## 4.8 Route props

In v4 and v5, matched route components commonly received route props:

- `match`
- `location`
- `history`

Example:

```jsx
function UserDetails(props) {
  const userId = props.match.params.id;

  return <h1>User ID: {userId}</h1>;
}
```

These route props were important in older versions.

In v6 and v7, hooks are generally used instead.

---

# 5. React Router v5

React Router v5 improved the v4 API and became widely used in production applications.

It retained the declarative routing architecture.

## 5.1 Installation

```bash
npm install react-router-dom@5
```

## 5.2 Routes using Switch

```jsx
import {
  BrowserRouter,
  Switch,
  Route
} from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Switch>
        <Route exact path="/" component={Home} />
        <Route path="/about" component={About} />
        <Route path="/contact" component={Contact} />
      </Switch>
    </BrowserRouter>
  );
}
```

### How Switch works

Suppose the URL is:

```text
/about
```

React Router checks routes in order and renders the first matching route.

`Switch` renders only one matching route from its children.

## 5.3 Three ways to render a component through Route

React Router v5 supported three common rendering approaches.

### 1. component

```jsx
<Route path="/about" component={About} />
```

React Router creates the component when the route matches.

### 2. render

```jsx
<Route
  path="/about"
  render={(props) => <About {...props} />}
/>
```

Use this when you need to pass additional props or control rendering.

### 3. children

```jsx
<Route
  path="/about"
  children={(props) => (
    <div>
      {props.match && <About />}
    </div>
  )}
/>
```

Unlike `component` and `render`, the `children` callback can be rendered even when the route does not match. The `match` value indicates whether a match exists.

## 5.4 useHistory()

`useHistory()` is a v5 hook used for programmatic navigation.

```jsx
import { useHistory } from "react-router-dom";

function Login() {
  const history = useHistory();

  function handleLogin() {
    history.push("/dashboard");
  }

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

### Important history methods

| Method | Purpose |
|---|---|
| `push()` | Adds a new history entry |
| `replace()` | Replaces the current history entry |
| `goBack()` | Moves backward |
| `goForward()` | Moves forward |
| `go()` | Moves a specified number of entries |

## 5.5 useLocation()

`useLocation()` returns information about the current URL.

```jsx
import { useLocation } from "react-router-dom";

function CurrentPage() {
  const location = useLocation();

  return <p>{location.pathname}</p>;
}
```

The location object includes:

- `pathname`
- `search`
- `hash`
- `state`
- `key`

Example URL:

```text
/courses?category=react#lessons
```

Possible values:

```text
pathname = /courses
search   = ?category=react
hash     = #lessons
```

## 5.6 useParams()

`useParams()` returns dynamic URL parameters.

```jsx
import { useParams } from "react-router-dom";

function CourseDetails() {
  const { courseId } = useParams();

  return <h1>Course ID: {courseId}</h1>;
}
```

Route:

```jsx
<Route path="/courses/:courseId" component={CourseDetails} />
```

For `/courses/101`, `courseId` is `"101"`.

## 5.7 useRouteMatch()

`useRouteMatch()` provides information about the current route match.

```jsx
import { useRouteMatch } from "react-router-dom";

function Courses() {
  const match = useRouteMatch();

  return <p>{match.path}</p>;
}
```

It was also useful for building nested routes and links relative to a matched route.

## 5.8 useHistory vs useLocation vs useParams vs useRouteMatch

| Hook | Purpose |
|---|---|
| `useHistory()` | Programmatic navigation |
| `useLocation()` | Current URL information |
| `useParams()` | Dynamic URL parameters |
| `useRouteMatch()` | Current route matching information |

## 5.9 Redirect

v5 provided the `Redirect` component.

```jsx
import { Redirect } from "react-router-dom";

function PrivatePage({ isAuthenticated }) {
  if (!isAuthenticated) {
    return <Redirect to="/login" />;
  }

  return <h1>Private Dashboard</h1>;
}
```

## 5.10 Nested routes

```jsx
<Switch>
  <Route exact path="/dashboard" component={Dashboard} />
  <Route path="/dashboard/profile" component={Profile} />
  <Route path="/dashboard/settings" component={Settings} />
</Switch>
```

This is one way to configure related pages in v5.

---

# 6. React Router v6

React Router v6 introduced major API improvements and a simpler routing model.

It replaced several older APIs and improved nested layouts.

## 6.1 Installation

```bash
npm install react-router-dom@6
```

## 6.2 Switch was replaced by Routes

In v5:

```jsx
<Switch>
  <Route exact path="/" component={Home} />
</Switch>
```

In v6:

```jsx
<Routes>
  <Route path="/" element={<Home />} />
</Routes>
```

### Important changes

- `Switch` was replaced by `Routes`.
- `component` was replaced by `element`.
- `render` and `children` route rendering patterns were removed from the standard v6 Route API.
- `exact` is no longer required.
- Route matching became more predictable and ranking-based.

## 6.3 Basic routing

```jsx
import {
  BrowserRouter,
  Routes,
  Route
} from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </BrowserRouter>
  );
}
```

## 6.4 useNavigate()

`useNavigate()` replaced `useHistory()`.

```jsx
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();

  function handleLogin() {
    navigate("/dashboard");
  }

  return (
    <button onClick={handleLogin}>
      Login
    </button>
  );
}
```

### Replace navigation history

```jsx
navigate("/dashboard", { replace: true });
```

### Go backward

```jsx
navigate(-1);
```

### Go forward

```jsx
navigate(1);
```

## 6.5 useParams()

The purpose remains similar.

```jsx
const { id } = useParams();
```

## 6.6 useLocation()

```jsx
const location = useLocation();

console.log(location.pathname);
console.log(location.search);
console.log(location.state);
```

## 6.7 Nested routes

One of the most important improvements in v6 is the nested route layout system.

```jsx
<Routes>
  <Route path="/dashboard" element={<DashboardLayout />}>
    <Route path="profile" element={<Profile />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>
```

The parent layout must render an `Outlet`.

```jsx
import { Outlet } from "react-router-dom";

function DashboardLayout() {
  return (
    <div>
      <h1>Dashboard</h1>

      <Outlet />
    </div>
  );
}
```

### What is Outlet?

`Outlet` is a placeholder where the matching child route is rendered.

For `/dashboard/profile`:

1. React Router matches `/dashboard`.
2. It renders `DashboardLayout`.
3. It matches the child route `profile`.
4. It renders `Profile` inside `Outlet`.

## 6.8 Index routes

An index route is the default child route of a parent route.

```jsx
<Routes>
  <Route path="/dashboard" element={<DashboardLayout />}>
    <Route index element={<DashboardHome />} />
    <Route path="profile" element={<Profile />} />
  </Route>
</Routes>
```

Visiting `/dashboard` renders `DashboardHome` inside the layout.

## 6.9 Navigate component

`Navigate` is used for declarative redirects.

```jsx
import { Navigate } from "react-router-dom";

function ProtectedRoute({ isAuthenticated, children }) {
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  return children;
}
```

## 6.10 useSearchParams()

This hook reads and updates URL query parameters.

```jsx
import { useSearchParams } from "react-router-dom";

function Courses() {
  const [searchParams, setSearchParams] = useSearchParams();

  const category = searchParams.get("category");

  function changeCategory() {
    setSearchParams({ category: "react" });
  }

  return (
    <div>
      <p>Category: {category}</p>

      <button onClick={changeCategory}>
        React Courses
      </button>
    </div>
  );
}
```

Example URL:

```text
/courses?category=react
```

## 6.11 useOutlet()

`useOutlet()` returns the element rendered by the matching child route.

```jsx
import { useOutlet } from "react-router-dom";

function DashboardLayout() {
  const outlet = useOutlet();

  return (
    <div>
      <h1>Dashboard</h1>
      {outlet}
    </div>
  );
}
```

`Outlet` is generally the simpler choice for ordinary layouts.

## 6.12 useNavigationType()

This hook identifies how the current location was reached.

```jsx
import { useNavigationType } from "react-router-dom";

function Page() {
  const navigationType = useNavigationType();

  return <p>{navigationType}</p>;
}
```

It can return:

- `POP`
- `PUSH`
- `REPLACE`

## 6.13 useResolvedPath()

Resolves a relative destination into a path object.

```jsx
import { useResolvedPath } from "react-router-dom";

function Example() {
  const path = useResolvedPath("../profile");

  return <pre>{JSON.stringify(path, null, 2)}</pre>;
}
```

## 6.14 useMatch()

Checks whether the current URL matches a specified pattern.

```jsx
import { useMatch } from "react-router-dom";

function Navigation() {
  const match = useMatch("/courses");

  return (
    <p>
      {match ? "Courses page" : "Another page"}
    </p>
  );
}
```

## 6.15 Relative routing

Nested routes can use relative paths.

```jsx
<Link to="profile">Profile</Link>
```

This is useful inside a dashboard layout because the link is resolved relative to the current route context.

## 6.16 Data Router APIs

Later v6 releases introduced the Data Router APIs.

They allow developers to define route-level data loading and mutations.

Important APIs include:

- `createBrowserRouter`
- `createHashRouter`
- `RouterProvider`
- `loader`
- `action`
- `useLoaderData`
- `useActionData`
- `useNavigation`
- `useFetcher`
- `useRouteError`
- `errorElement`
- `defer` in earlier data-router workflows

Example:

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
```

### What is a loader?

A loader fetches data before the route component is rendered.

```jsx
async function userLoader({ params }) {
  const response = await fetch(`/api/users/${params.id}`);

  if (!response.ok) {
    throw new Response("User not found", {
      status: response.status
    });
  }

  return response.json();
}
```

Route configuration:

```jsx
{
  path: "/users/:id",
  loader: userLoader,
  element: <UserDetails />
}
```

Access the loaded data:

```jsx
import { useLoaderData } from "react-router-dom";

function UserDetails() {
  const user = useLoaderData();

  return <h1>{user.name}</h1>;
}
```

### What is an action?

An action handles route-level mutations, usually associated with form submissions.

```jsx
async function contactAction({ request }) {
  const formData = await request.formData();

  const name = formData.get("name");

  return { message: `Hello ${name}` };
}
```

Route:

```jsx
{
  path: "/contact",
  element: <Contact />,
  action: contactAction
}
```

### What is useNavigation?

`useNavigation()` provides information about the current data-router navigation state.

```jsx
import { useNavigation } from "react-router-dom";

function RootLayout() {
  const navigation = useNavigation();

  return (
    <>
      {navigation.state === "loading" && <p>Loading...</p>}
      <Outlet />
    </>
  );
}
```

Possible states include:

- `idle`
- `loading`
- `submitting`

### What is useFetcher?

`useFetcher()` allows a component to interact with route loaders and actions without navigating to another page.

It is useful for operations such as:

- Updating a favorite.
- Submitting an inline form.
- Updating a record.
- Loading data in the background.

---

# 7. React Router v7

React Router v7 is the modern continuation of the v6 Data Router architecture.

It provides declarative routing, data routing and framework capabilities.

It is designed to make migration from v6 easier while providing a more integrated development experience.

## 7.1 Installation

For a standard declarative React application:

```bash
npm install react-router
```

For a project using the v7 Framework Mode, follow the official framework setup instructions. Framework Mode requires additional project configuration.

**Important:** React Router v7 uses the `react-router` package as its primary package. Existing v6 applications commonly use `react-router-dom`; the v7 upgrade path provides compatibility guidance for moving to the unified package.

## 7.2 Three modes of React Router v7

React Router v7 supports three major modes.

### 1. Declarative Mode

This is the simplest routing approach.

It uses components such as:

- `BrowserRouter`
- `Routes`
- `Route`
- `Link`
- `NavLink`
- `Navigate`

Example:

```jsx
import {
  BrowserRouter,
  Routes,
  Route
} from "react-router";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/courses" element={<Courses />} />
      </Routes>
    </BrowserRouter>
  );
}
```

Use this when your application needs ordinary client-side routing without route-level loaders and actions.

### 2. Data Mode

Data Mode uses route objects and a router provider.

Important APIs:

- `createBrowserRouter`
- `createHashRouter`
- `RouterProvider`
- `loader`
- `action`
- `useLoaderData`
- `useActionData`
- `useNavigation`
- `useFetcher`
- `useRouteError`

Example:

```jsx
import {
  createBrowserRouter,
  RouterProvider
} from "react-router";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />
  },
  {
    path: "/courses",
    element: <Courses />
  }
]);

function App() {
  return <RouterProvider router={router} />;
}
```

Data Mode provides route-level data loading, mutations, navigation state and error handling.

### 3. Framework Mode

Framework Mode provides a more complete application architecture.

It builds on React Router's routing and data capabilities.

It supports features such as:

- File-based route configuration.
- Route loaders.
- Route actions.
- Server rendering.
- Client-side navigation.
- Route-level error boundaries.
- Automatic route code splitting.
- Type generation.
- Server and client data handling.

It is useful when building a complete production application with integrated routing and server capabilities.

## 7.3 Route configuration in Framework Mode

Framework Mode uses route configuration and route modules.

A route module can define:

- `loader`
- `action`
- `headers`
- `meta`
- `links`
- `clientLoader`
- `clientAction`
- `HydrateFallback`
- `ErrorBoundary`
- A default React component export

The exact exports used depend on the route's requirements.

Example concept:

```jsx
export async function loader() {
  return {
    title: "React Courses"
  };
}

export default function Courses() {
  return <h1>Courses</h1>;
}
```

The framework associates route modules with route definitions.

## 7.4 Type safety

React Router v7 provides improved type generation in Framework Mode.

This helps developers work with:

- Route parameters.
- Loader return values.
- Action data.
- Route-specific component data.

Type generation reduces the need to manually maintain duplicated route data types.

## 7.5 Middleware

Modern React Router v7 framework capabilities include route middleware.

Middleware can be used for shared request-processing logic, such as:

- Authentication checks.
- Logging.
- Request context.
- Permission checks.

Middleware is a Framework Mode capability and should not be confused with a basic `BrowserRouter` feature.

## 7.6 Server-side rendering

React Router v7 Framework Mode supports server rendering.

The server can render the initial HTML response, while React hydrates the page in the browser.

This can improve initial rendering and support SEO-friendly server-rendered pages.

## 7.7 React Router v7 hooks

Important hooks include:

| Hook | Purpose |
|---|---|
| `useNavigate` | Programmatic navigation |
| `useParams` | Read URL parameters |
| `useLocation` | Read current location |
| `useSearchParams` | Read and update query parameters |
| `useMatch` | Match the current URL |
| `useMatches` | Access the active route matches |
| `useOutlet` | Access the child route element |
| `useNavigation` | Read navigation state |
| `useNavigationType` | Identify navigation type |
| `useLoaderData` | Read loader data |
| `useActionData` | Read action results |
| `useFetcher` | Interact with loaders and actions without navigation |
| `useRouteError` | Read a route error |
| `useRevalidator` | Revalidate route data |
| `useRouteLoaderData` | Read data from a specific route |
| `useSubmit` | Submit data programmatically |
| `useFormAction` | Resolve a form action URL |
| `useFormStatus` | Read form submission status in supported router workflows |

Not every hook is required in every routing mode. Some are primarily useful with Data Mode or Framework Mode.

## 7.8 Important v7 distinction

React Router v7 does not mean every application must use Framework Mode.

A normal React + Vite application can continue using declarative routing or Data Mode.

Choose the mode according to the application's requirements.

---

# 8. Version-Wise Comparison

## 8.1 Major API changes

| Feature | v4 | v5 | v6 | v7 |
|---|---|---|---|---|
| Router package | react-router-dom | react-router-dom | react-router-dom | react-router |
| Route grouping | Switch | Switch | Routes | Routes |
| Route rendering | component/render | component/render | element | element |
| Programmatic navigation | history | useHistory | useNavigate | useNavigate |
| Redirect component | Redirect | Redirect | Navigate | Navigate |
| Nested layouts | Route composition | Route composition | Outlet | Outlet |
| Route parameters | match.params / hooks | useParams | useParams | useParams |
| Query parameters | location.search | location.search | useSearchParams | useSearchParams |
| Data loaders | Not built in | Not built in | Data Router | Data Router |
| Route actions | Not built in | Not built in | Data Router | Data Router |
| Framework Mode | No | No | Available in later v6 evolution | Full modern framework direction |

The package column describes the commonly used package for each generation. v7 migration and compatibility paths should be followed from the official upgrade guide.

## 8.2 Navigation comparison

**v5:**

```jsx
const history = useHistory();

history.push("/dashboard");
```

**v6 and v7:**

```jsx
const navigate = useNavigate();

navigate("/dashboard");
```

## 8.3 Route declaration comparison

**v5:**

```jsx
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
</Switch>
```

**v6 and v7:**

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
</Routes>
```

## 8.4 Redirect comparison

**v5:**

```jsx
<Redirect to="/login" />
```

**v6 and v7:**

```jsx
<Navigate to="/login" replace />
```

---

# 9. Important Hooks Across Versions

This section explains the major routing hooks and their evolution.

## 9.1 useHistory – v5

Used for navigation.

```jsx
const history = useHistory();

history.push("/profile");
```

Replaced by `useNavigate` in v6.

## 9.2 useNavigate – v6 and v7

Used for programmatic navigation.

```jsx
const navigate = useNavigate();

navigate("/profile");
navigate(-1);
```

## 9.3 useParams – v5, v6 and v7

Reads dynamic route parameters.

```jsx
const { id } = useParams();
```

## 9.4 useLocation – v5, v6 and v7

Reads the current URL and location state.

```jsx
const location = useLocation();
```

## 9.5 useSearchParams – v6 and v7

Reads and updates query parameters.

```jsx
const [params, setParams] = useSearchParams();

const search = params.get("search");
```

## 9.6 useOutlet – v6 and v7

Accesses the child route element.

```jsx
const outlet = useOutlet();
```

## 9.7 useNavigation – Data Router APIs

Reads the state of an active navigation or submission.

```jsx
const navigation = useNavigation();
```

## 9.8 useLoaderData – Data Router APIs

Reads the data returned by the matched route's loader.

```jsx
const data = useLoaderData();
```

## 9.9 useActionData – Data Router APIs

Reads data returned by a route action after a submission.

```jsx
const result = useActionData();
```

## 9.10 useFetcher – Data Router APIs

Performs route data operations without navigating.

```jsx
const fetcher = useFetcher();

fetcher.Form;
fetcher.state;
fetcher.data;
```

A fetcher can submit data or load route data independently of the current page navigation.

---

# 10. Important Components Across Versions

| Component | Purpose |
|---|---|
| BrowserRouter | Uses browser history |
| HashRouter | Uses URL hash for routing |
| MemoryRouter | Stores navigation history in memory |
| Routes | Selects the matching route in v6/v7 |
| Route | Defines a route |
| Link | Navigates to a URL |
| NavLink | Navigation link with active-state support |
| Navigate | Performs a declarative redirect |
| Outlet | Renders a matching child route |
| RouterProvider | Provides a configured Data Router |
| Form | Router-aware form for route actions |
| ScrollRestoration | Restores scroll positions in supported data/framework workflows |

### BrowserRouter vs HashRouter vs MemoryRouter

**BrowserRouter**

URL:

```text
/about
```

Best for most normal web applications. Server configuration must support fallback to the application's entry HTML for client-side routes.

**HashRouter**

URL:

```text
/#/about
```

Useful when server-side URL fallback configuration is unavailable.

**MemoryRouter**

The URL is not managed through the browser address bar in the normal way.

Useful for tests and environments without normal browser navigation.

---

# 11. Routing Architecture: Declarative, Data and Framework Modes

React Router's architecture can be understood through three approaches.

## 11.1 Declarative routing

Routes are declared using React components.

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
  </Routes>
</BrowserRouter>
```

Suitable for:

- Small and medium React applications.
- Basic page navigation.
- Applications that fetch data inside components.
- Applications using their own API layer.

## 11.2 Data routing

Routes are configured as objects.

```jsx
const router = createBrowserRouter([
  {
    path: "/courses",
    loader: coursesLoader,
    element: <Courses />
  }
]);
```

Suitable for:

- Route-level data loading.
- Form mutations.
- Pending navigation states.
- Route-level error handling.

## 11.3 Framework routing

The router becomes part of a complete application framework.

Suitable for:

- Server-side rendering.
- File-based route modules.
- Server data loading.
- Route-level code splitting.
- Type generation.
- Larger production applications.

---

# 12. Migration from v5 to v6

The following are the most important migration changes.

## 12.1 Switch to Routes

Before:

```jsx
<Switch>
  <Route exact path="/" component={Home} />
</Switch>
```

After:

```jsx
<Routes>
  <Route path="/" element={<Home />} />
</Routes>
```

## 12.2 component to element

Before:

```jsx
<Route path="/about" component={About} />
```

After:

```jsx
<Route path="/about" element={<About />} />
```

## 12.3 useHistory to useNavigate

Before:

```jsx
const history = useHistory();

history.push("/dashboard");
```

After:

```jsx
const navigate = useNavigate();

navigate("/dashboard");
```

## 12.4 Redirect to Navigate

Before:

```jsx
<Redirect to="/login" />
```

After:

```jsx
<Navigate to="/login" replace />
```

## 12.5 Nested route layouts

Before, nested routes were commonly composed using route components.

In v6, use a parent route and `Outlet`.

```jsx
<Route path="/dashboard" element={<DashboardLayout />}>
  <Route path="profile" element={<Profile />} />
</Route>
```

## 12.6 exact is no longer required

Before:

```jsx
<Route exact path="/" component={Home} />
```

After:

```jsx
<Route path="/" element={<Home />} />
```

v6 uses ranked route matching rather than relying on the old `exact` prop.

## 12.7 useRouteMatch changes

The old `useRouteMatch` API is no longer used in v6.

Depending on the requirement, use:

- `useMatch`
- `useResolvedPath`
- `useLocation`
- `useMatches`

---

# 13. Migration from v6 to v7

React Router v7 was designed to provide a smoother upgrade path from v6.

Before upgrading:

1. Update to the latest compatible v6 release.
2. Review the official v7 upgrade guide.
3. Check your React and Node.js versions against the current requirements.
4. Review package imports.
5. Run the application tests.
6. Check loaders, actions, forms and error handling.
7. Test direct URL navigation and refresh behavior.

### Package migration

Older v6 applications commonly import from:

```jsx
import {
  BrowserRouter,
  Routes,
  Route
} from "react-router-dom";
```

The v7 unified package is:

```jsx
import {
  BrowserRouter,
  Routes,
  Route
} from "react-router";
```

Follow the official upgrade guide for the exact dependency and compatibility changes applicable to your project.

### Important migration advice

Do not rewrite an entire application simply because a newer major version is available.

Upgrade in a controlled way:

- Keep existing route paths.
- Preserve authentication behavior.
- Preserve API integrations.
- Preserve nested layouts.
- Test protected routes.
- Test browser refresh.
- Test redirects.
- Test all user roles.

---

# 14. Choosing the Right Router

| Requirement | Recommended approach |
|---|---|
| Basic React application | Declarative Mode |
| Simple page navigation | BrowserRouter + Routes |
| Nested dashboards | Nested Routes + Outlet |
| Route-level API loading | Data Mode |
| Route-level form mutations | Data Mode |
| Background route operations | useFetcher |
| Server-side rendering | Framework Mode |
| File-based routes | Framework Mode |
| Type-generated route data | Framework Mode |

A project does not automatically need Framework Mode just because it uses React Router v7.

---

# 15. Best Practices

1. Use `BrowserRouter` for standard browser-based applications.
2. Keep route definitions organised.
3. Use nested routes for shared layouts.
4. Use `Outlet` to render nested pages.
5. Use `NavLink` for active navigation states.
6. Use `useNavigate` for programmatic navigation.
7. Use `useParams` for dynamic URL values.
8. Use `useSearchParams` for query strings.
9. Use protected routes for authentication and authorization.
10. Validate permissions on the backend as well as the frontend.
11. Use loaders when route-level data loading is appropriate.
12. Handle route errors properly.
13. Test direct URL access and browser refresh.
14. Avoid mixing APIs from v5 and v6/v7.
15. Follow the official migration guide before upgrading.

### Common mistakes

**Mistake 1: Using Switch in v6/v7**

Use `Routes`.

**Mistake 2: Using component in a v6/v7 Route**

Use `element`.

**Mistake 3: Using useHistory in v6/v7**

Use `useNavigate`.

**Mistake 4: Forgetting Outlet**

A nested child route will not appear in the intended parent layout if the parent does not render an `Outlet`.

**Mistake 5: Treating authentication as authorization**

Authentication confirms who the user is. Authorization determines what the user can access. Both frontend and backend protections matter.

**Mistake 6: Assuming all v7 APIs work in every mode**

Some features belong specifically to Data Mode or Framework Mode.

---

# 16. Official Documentation

Always use official documentation for current APIs, compatibility requirements and migration details.

- React Router documentation: https://reactrouter.com/
- React Router v7 upgrade guide: https://reactrouter.com/upgrading/v6
- React Router concepts: https://reactrouter.com/start/declarative/routing
- Data loading: https://reactrouter.com/start/data/data-loading
- Actions: https://reactrouter.com/start/data/actions
- Framework Mode: https://reactrouter.com/start/framework/installation

---

## Final Learning Summary

The most important evolution to remember is:

- **v1–v3:** Configuration-based routing.
- **v4:** Declarative component-based routing.
- **v5:** Improved declarative routing and hooks such as `useHistory`.
- **v6:** `Routes`, `element`, `useNavigate`, nested layouts with `Outlet`, and Data Router APIs.
- **v7:** Unified modern routing APIs, Data Mode and Framework Mode capabilities.

For modern React development, concentrate deeply on v6 and v7 while understanding v5 migration because many existing projects still use v5.

**Remember:** Learn the older versions to understand how routing evolved, but write new application code using the APIs supported by the version installed in your project.