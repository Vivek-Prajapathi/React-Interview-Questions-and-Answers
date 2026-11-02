# Complete React Router Guide — Concepts, Hooks, APIs & Version-Wise Changes

A detailed reference covering React Router from beginner to advanced level, including version-wise changes, routing concepts, hooks, Data Router APIs, practical implementations, and interview explanations.

---

# Table of Contents

1. What is Routing?
2. Why Do We Need React Router?
3. Client-Side Routing vs Server-Side Routing
4. React Router Installation
5. React Router Version History
6. React Router v5 — Concepts and APIs
7. React Router v6 — Major Changes
8. React Router v6.4+ — Data Router
9. React Router v7 — Latest Architecture
10. Core Routing Components
11. Route Configuration
12. Nested Routing
13. Index Routes
14. Dynamic Routing
15. Route Parameters
16. Query Parameters
17. Navigation
18. Layout Routes and Outlet
19. Protected Routes
20. Role-Based Authorization
21. Loaders
22. Actions
23. Forms
24. Fetchers
25. Navigation States
26. Error Handling
27. Lazy Loading
28. Navigation Blocking
29. Scroll Restoration
30. View Transitions
31. Complete React Router Hooks Reference
32. React Router Project Architecture
33. Common Errors and Solutions
34. Best Practices
35. Version Comparison
36. Interview Questions and Answers
37. Complete Learning Roadmap

---

# 1. What is Routing?

Routing is the process of displaying different components or pages based on the URL entered or selected by the user.

For example:

| URL | Component |
|---|---|
| `/` | Home |
| `/about` | About |
| `/courses` | Courses |
| `/courses/10` | CourseDetails |
| `/login` | Login |
| `/dashboard` | Dashboard |

In a React application, we generally build a Single Page Application (SPA). React Router allows us to navigate between different views without reloading the entire HTML document for every navigation.

**Interview answer:**

"Routing in React is the process of mapping URLs to specific components. React Router is a library that allows us to implement client-side navigation, dynamic routes, nested routes, protected routes, and route-based data handling in React applications."

# 2. Why Do We Need React Router?

Imagine an e-commerce application containing:

- Home page
- Products page
- Product details page
- Login page
- Register page
- User dashboard
- Admin dashboard

Without a routing library, we would need to manually manage URL changes and decide which component should be rendered.

React Router simplifies this by providing:

1. URL-to-component mapping.
2. Navigation between pages.
3. Dynamic URL parameters.
4. Nested routes.
5. Shared layouts.
6. Query parameter handling.
7. Protected routes.
8. Role-based route handling.
9. Route-level data loading.
10. Route-level error handling.
11. Lazy loading and code splitting.

# 3. Client-Side Routing vs Server-Side Routing

## Client-Side Routing

Client-side routing handles navigation in the browser using JavaScript.

Example:

1. User opens `/courses`.
2. React Router matches the URL.
3. The Courses component renders.
4. The browser URL changes without requesting an entirely new HTML document.

## Server-Side Routing

In server-side routing, the browser sends a request to the server whenever a URL is requested. The server determines what response or HTML document should be returned.

| Client-side routing | Server-side routing |
|---|---|
| Navigation is handled in the browser | Navigation is handled by the server |
| Usually avoids a full document reload between SPA routes | Often returns a new document |
| React renders the matching component | Server returns the requested response |
| Requires SPA fallback configuration for direct nested URLs | Server handles the requested URL directly |

## Browser History API

React Router uses browser history mechanisms to manage navigation.

Important methods:

```javascript
history.pushState()
history.replaceState()
history.back()
history.forward()
```

- `pushState()` adds a new history entry.
- `replaceState()` replaces the current history entry.
- `back()` moves to the previous history entry.
- `forward()` moves to the next history entry.

React Router manages this integration, so we normally use its navigation APIs rather than manipulating browser history directly.

# 4. React Router Installation

For a typical React application:

```bash
npm install react-router-dom
```

Check the installed version:

```bash
npm list react-router-dom
```

You can also check `package.json`.

```json
{
  "dependencies": {
    "react-router-dom": "^6.XX.X"
  }
}
```

The exact version matters because some APIs and import paths differ between major versions and router modes.

# 5. React Router Version History

| Version | Important features |
|---|---|
| v4 | Component-based routing |
| v5 | Switch, Redirect, useHistory |
| v6 | Routes, Navigate, useNavigate, Outlet |
| v6.4+ | Data Router, loaders, actions, fetchers |
| v7 | Declarative, Data, and Framework modes |

## Main version changes

**v5:** Traditional component-based routing.

**v6:** Simplified route matching, nested routing, and new navigation APIs.

**v6.4+:** Introduced Data Router APIs for loading and submitting route data.

**v7:** Continues the Data Router architecture and introduces a more integrated Framework mode.

The exact latest patch release can change. Always check the installed package and official documentation.

# 6. React Router v5 — Concepts and APIs

React Router v5 commonly uses:

- BrowserRouter
- Switch
- Route
- Link
- NavLink
- Redirect
- useHistory
- useParams
- useLocation
- useRouteMatch

## 6.1 BrowserRouter

BrowserRouter uses the browser History API to keep the UI synchronized with the URL.

```jsx
import { BrowserRouter } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      {/* Routes */}
    </BrowserRouter>
  );
}
```

## 6.2 Switch

Switch renders the first matching Route or Redirect.

```jsx
import { Switch, Route } from "react-router-dom";

function App() {
  return (
    <Switch>
      <Route exact path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/contact" component={Contact} />
    </Switch>
  );
}
```

The `exact` property was commonly used in v5 to prevent `/` from matching every URL that begins with `/`.

## 6.3 Route

Route connects a URL pattern with a component.

```jsx
<Route exact path="/about" component={About} />
```

Another v5 syntax:

```jsx
<Route path="/about" render={() => <About />} />
```

## 6.4 Link

Link navigates between routes without a normal full-page reload.

```jsx
import { Link } from "react-router-dom";

<Link to="/about">About</Link>
```

## 6.5 NavLink

NavLink is similar to Link, but it can apply active styling.

```jsx
<NavLink
  to="/dashboard"
  activeClassName="active"
>
  Dashboard
</NavLink>
```

## 6.6 Redirect

Redirect sends the user to another route.

```jsx
import { Redirect } from "react-router-dom";

<Redirect to="/login" />
```

## 6.7 useHistory

useHistory provides access to the history object.

```jsx
import { useHistory } from "react-router-dom";

function Login() {
  const history = useHistory();

  function handleLogin() {
    history.push("/dashboard");
  }

  return <button onClick={handleLogin}>Login</button>;
}
```

Important methods:

```javascript
history.push("/dashboard");
history.replace("/login");
history.goBack();
history.goForward();
history.go(-2);
```

## 6.8 useParams

Reads dynamic URL parameters.

```jsx
<Route path="/users/:userId" component={UserDetails} />
```

```jsx
const { userId } = useParams();
```

## 6.9 useLocation

Returns information about the current location.

```jsx
const location = useLocation();

console.log(location.pathname);
console.log(location.search);
console.log(location.hash);
console.log(location.state);
```

## 6.10 useRouteMatch

Checks whether the current URL matches a specified route pattern.

```jsx
const match = useRouteMatch("/users/:userId");
```

# 7. React Router v6 — Major Changes

React Router v6 introduced a simpler API and improved nested routing.

| React Router v5 | React Router v6 |
|---|---|
| Switch | Routes |
| Redirect | Navigate |
| useHistory | useNavigate |
| component prop | element prop |
| render prop | element prop |
| activeClassName | className callback |
| useRouteMatch | useMatch |
| exact | Generally unnecessary |

## 7.1 Routes replaces Switch

v5:

```jsx
<Switch>
  <Route exact path="/" component={Home} />
</Switch>
```

v6:

```jsx
<Routes>
  <Route path="/" element={<Home />} />
</Routes>
```

Routes ranks matching routes and renders the best matching route branch.

## 7.2 element replaces component

v5:

```jsx
<Route path="/about" component={About} />
```

v6:

```jsx
<Route path="/about" element={<About />} />
```

The element prop receives a React element.

## 7.3 Navigate replaces Redirect

```jsx
import { Navigate } from "react-router-dom";

<Route
  path="/"
  element={<Navigate to="/home" replace />}
/>
```

The `replace` option replaces the current history entry.

## 7.4 useNavigate replaces useHistory

```jsx
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();

  function handleLogin() {
    navigate("/dashboard");
  }

  return <button onClick={handleLogin}>Login</button>;
}
```

Examples:

```javascript
navigate("/dashboard");
navigate("/login", { replace: true });
navigate(-1);
navigate(1);
```

## 7.5 NavLink active styling

```jsx
<NavLink
  to="/courses"
  className={({ isActive }) =>
    isActive ? "active-link" : "normal-link"
  }
>
  Courses
</NavLink>
```

## 7.6 useMatch

```jsx
const match = useMatch("/courses/:courseId");
```

Checks whether the current location matches a route pattern.

# 8. React Router v6.4+ — Data Router

React Router v6.4 introduced Data Router APIs.

These APIs allow us to connect data loading and data mutations directly to routes.

Important APIs:

- createBrowserRouter
- RouterProvider
- loader
- action
- useLoaderData
- useActionData
- useNavigation
- useFetcher
- useRouteError
- useRevalidator

## 8.1 createBrowserRouter

Creates a router from route objects.

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

## 8.2 RouterProvider

RouterProvider makes the configured router available to the application.

```jsx
<RouterProvider router={router} />
```

Create the router outside the React component so it is not recreated on every render.

## 8.3 Loader

A loader fetches data required by a route before that route renders.

```jsx
async function courseLoader({ params }) {
  const response = await fetch(
    `/api/courses/${params.courseId}`
  );

  if (!response.ok) {
    throw new Response("Course not found", {
      status: 404
    });
  }

  return response.json();
}
```

Attach it to a route:

```jsx
{
  path: "/courses/:courseId",
  element: <CourseDetails />,
  loader: courseLoader
}
```

Read the data:

```jsx
import { useLoaderData } from "react-router-dom";

function CourseDetails() {
  const course = useLoaderData();

  return <h1>{course.title}</h1>;
}
```

### Why use loaders?

1. Fetches data at route level.
2. Connects data requirements to route configuration.
3. Allows route navigation to coordinate data loading.
4. Makes loader data available through useLoaderData.
5. Supports route-level error handling.

## 8.4 Loader arguments

A loader receives an object that can contain:

- `params`: Dynamic URL parameters.
- `request`: The navigation request.
- `context`: Router-provided context where supported.

Example:

```jsx
async function loader({ params, request }) {
  const url = new URL(request.url);

  return {
    courseId: params.courseId,
    search: url.searchParams.get("q")
  };
}
```

## 8.5 Action

An action handles data mutations such as creating, updating, or deleting records.

```jsx
async function createCourseAction({ request }) {
  const formData = await request.formData();

  const course = {
    title: formData.get("title")
  };

  const response = await fetch("/api/courses", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(course)
  });

  if (!response.ok) {
    throw new Response("Unable to create course", {
      status: response.status
    });
  }

  return response.json();
}
```

Attach it:

```jsx
{
  path: "/courses/new",
  element: <CreateCourse />,
  action: createCourseAction
}
```

## 8.6 Form

React Router's Form submits data to a route action.

```jsx
import { Form } from "react-router-dom";

function CreateCourse() {
  return (
    <Form method="post">
      <input name="title" placeholder="Course title" />
      <button type="submit">Create</button>
    </Form>
  );
}
```

## 8.7 useActionData

Reads data returned by the most recent action submission.

```jsx
const result = useActionData();
```

Example:

```jsx
function LoginForm() {
  const result = useActionData();

  return (
    <>
      {result?.error && <p>{result.error}</p>}

      <Form method="post">
        <input name="email" type="email" />
        <input name="password" type="password" />
        <button type="submit">Login</button>
      </Form>
    </>
  );
}
```

## 8.8 Loader vs Action

| Loader | Action |
|---|---|
| Reads data | Mutates or submits data |
| Used for route data loading | Used for form submissions |
| Read using useLoaderData | Returned data read using useActionData |
| Commonly associated with GET | Commonly associated with POST, PUT, PATCH, DELETE |

# 9. React Router v7 — Modern Architecture

React Router v7 continues the v6.4+ Data Router architecture.

It provides three broad modes.

## 9.1 Declarative Mode

Used when we primarily need URL-to-component mapping and navigation.

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

The exact import source depends on the v7 setup and package entry points.

## 9.2 Data Mode

Used when we need:

- Loaders
- Actions
- Forms
- Fetchers
- Pending navigation states
- Route error boundaries

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
    element: <Courses />,
    loader: coursesLoader
  }
]);

function App() {
  return <RouterProvider router={router} />;
}
```

## 9.3 Framework Mode

Framework mode provides a more integrated application structure.

It can include:

- Route modules.
- Route-level loaders and actions.
- Automatic route code splitting.
- Server rendering.
- Client hydration.
- Build and development integration.
- Route-aware type generation in supported setups.

Framework mode is optional. A normal React SPA can continue using Declarative or Data mode.

## 9.4 v6 to v7 migration

When migrating:

1. Check the official migration guide.
2. Check package and import changes.
3. Review future flags as instructed.
4. Test nested routes.
5. Test navigation and redirects.
6. Test loaders and actions.
7. Test protected routes.
8. Test direct URL refreshes.
9. Verify the production build.

Do not assume every v6 tutorial has the correct imports for every v7 setup.

# 10. Core Routing Components

## BrowserRouter

Provides routing context and connects the application to browser history.

```jsx
<BrowserRouter>
  <App />
</BrowserRouter>
```

## Routes

Contains Route definitions and renders the best matching route branch.

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
</Routes>
```

## Route

Connects a URL pattern with a component or nested route configuration.

```jsx
<Route path="/profile" element={<Profile />} />
```

## Link

Navigates to another route.

```jsx
<Link to="/courses">Courses</Link>
```

## NavLink

Provides navigation with active-state information.

```jsx
<NavLink
  to="/dashboard"
  className={({ isActive }) =>
    isActive ? "active" : ""
  }
>
  Dashboard
</NavLink>
```

## Navigate

Performs a declarative redirect.

```jsx
<Navigate to="/login" replace />
```

## Outlet

Displays the matching child route inside a parent layout.

```jsx
function MainLayout() {
  return (
    <>
      <Navbar />
      <Outlet />
      <Footer />
    </>
  );
}
```

## RouterProvider

Renders the configured Data Router.

## Form

Submits data to a route action.

## ScrollRestoration

Restores scroll positions in supported Data and Framework router setups.

# 11. Route Configuration

A basic v6 route configuration:

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
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

## Catch-all route

```jsx
<Route path="*" element={<NotFound />} />
```

This is commonly used to render a 404 page.

# 12. Nested Routing

Nested routing allows multiple pages to share a common parent layout.

```jsx
<Routes>
  <Route path="/admin" element={<AdminLayout />}>
    <Route index element={<AdminOverview />} />
    <Route path="users" element={<AdminUsers />} />
    <Route path="courses" element={<AdminCourses />} />
    <Route path="orders" element={<AdminOrders />} />
  </Route>
</Routes>
```

Resulting URLs:

```text
/admin
/admin/users
/admin/courses
/admin/orders
```

The parent layout stays visible while the child component changes.

# 13. Index Routes

An index route is the default child page displayed when the parent URL matches exactly.

```jsx
<Route path="/dashboard" element={<DashboardLayout />}>
  <Route index element={<DashboardHome />} />
  <Route path="profile" element={<Profile />} />
  <Route path="settings" element={<Settings />} />
</Route>
```

- `/dashboard` renders DashboardHome.
- `/dashboard/profile` renders Profile.
- `/dashboard/settings` renders Settings.

An index route does not have its own path.

# 14. Dynamic Routing

Dynamic routing allows route values to change based on URL parameters.

```jsx
<Route
  path="/products/:productId"
  element={<ProductDetails />}
/>
```

For this URL:

```text
/products/25
```

The `productId` value is `25`.

```jsx
const { productId } = useParams();
```

Multiple parameters:

```jsx
<Route
  path="/courses/:courseId/lessons/:lessonId"
  element={<LessonDetails />}
/>
```

```jsx
const { courseId, lessonId } = useParams();
```

Route parameters are strings. Convert them when numeric operations are required.

```jsx
const numericId = Number(productId);
```

# 15. Query Parameters

Query parameters appear after `?` in the URL.

Example:

```text
/courses?category=react&page=2
```

Use `useSearchParams`:

```jsx
import { useSearchParams } from "react-router-dom";

function Courses() {
  const [searchParams, setSearchParams] = useSearchParams();

  const category = searchParams.get("category");
  const page = searchParams.get("page");

  return (
    <>
      <p>{category}</p>
      <p>{page}</p>

      <button
        onClick={() =>
          setSearchParams({
            category: "javascript",
            page: "1"
          })
        }
      >
        JavaScript courses
      </button>
    </>
  );
}
```

### Route parameters vs query parameters

| Route parameter | Query parameter |
|---|---|
| `/courses/10` | `/courses?page=2` |
| Part of URL path | Part after `?` |
| Read using useParams | Read using useSearchParams |
| Commonly identifies a resource | Commonly handles filters and pagination |

# 16. Navigation

## Link navigation

```jsx
<Link to="/courses">Courses</Link>
```

## Programmatic navigation

```jsx
const navigate = useNavigate();

navigate("/dashboard");
```

Useful after a successful login or API operation.

## Replace navigation

```jsx
navigate("/dashboard", { replace: true });
```

## Browser back and forward

```jsx
navigate(-1);
navigate(1);
```

## Navigation with state

```jsx
navigate("/profile", {
  state: {
    message: "Profile updated"
  }
});
```

Read it:

```jsx
const location = useLocation();

const message = location.state?.message;
```

Location state is temporary navigation state. It is not a replacement for database storage or persistent API data.

# 17. Layout Routes and Outlet

A layout route provides shared UI across multiple pages.

Example:

```jsx
import { Outlet } from "react-router-dom";

function PublicLayout() {
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

Admin layout:

```jsx
function AdminLayout() {
  return (
    <div className="admin-layout">
      <AdminSidebar />

      <main>
        <AdminHeader />
        <Outlet />
      </main>
    </div>
  );
}
```

### Why Outlet?

Without Outlet, the parent layout has no location where the matching nested child route can render.

**Interview answer:**

"Outlet is a placeholder used inside a parent route component. When a nested child route matches, React Router renders that child component at the Outlet position. It helps us share layouts such as navigation bars, sidebars, and footers without repeating them on every page."

# 18. Protected Routes

React Router does not automatically authenticate users. Authentication is handled by application logic, commonly through Context API or another state-management solution.

A protected route checks whether a user is authenticated before rendering a page.

```jsx
import {
  Navigate,
  Outlet,
  useLocation
} from "react-router-dom";

import { useAuth } from "../context/AuthContext";

function ProtectedRoute() {
  const { isAuthenticated, loading } = useAuth();
  const location = useLocation();

  if (loading) {
    return <p>Checking authentication...</p>;
  }

  if (!isAuthenticated) {
    return (
      <Navigate
        to="/login"
        replace
        state={{ from: location }}
      />
    );
  }

  return <Outlet />;
}

export default ProtectedRoute;
```

Use it:

```jsx
<Route element={<ProtectedRoute />}>
  <Route path="/dashboard" element={<Dashboard />} />
  <Route path="/profile" element={<Profile />} />
</Route>
```

# 19. Role-Based Authorization

Authentication verifies who the user is.

Authorization checks what the user is allowed to access.

Example roles:

- Admin
- Student
- Instructor

```jsx
function RoleRoute({ allowedRoles }) {
  const { isAuthenticated, user, loading } = useAuth();

  if (loading) return <p>Loading...</p>;

  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }

  if (!allowedRoles.includes(user?.role)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return <Outlet />;
}
```

Use it:

```jsx
<Route element={<RoleRoute allowedRoles={["admin"]} />}>
  <Route path="/admin" element={<AdminLayout />}>
    <Route index element={<AdminOverview />} />
    <Route path="users" element={<AdminUsers />} />
  </Route>
</Route>
```

**Important security point:** Frontend route protection is not sufficient security. Backend APIs must independently validate tokens and user permissions.

# 20. Loaders

A loader fetches data required by a route.

```jsx
async function userLoader({ params }) {
  const response = await fetch(
    `/api/users/${params.userId}`
  );

  if (!response.ok) {
    throw new Response("User not found", {
      status: 404
    });
  }

  return response.json();
}
```

Route:

```jsx
{
  path: "/users/:userId",
  element: <UserDetails />,
  loader: userLoader
}
```

Component:

```jsx
function UserDetails() {
  const user = useLoaderData();

  return <h1>{user.name}</h1>;
}
```

A loader can also access query parameters through `request.url`.

# 21. Actions

An action processes route submissions and data mutations.

```jsx
async function createUserAction({ request }) {
  const formData = await request.formData();

  const user = {
    name: formData.get("name"),
    email: formData.get("email")
  };

  const response = await fetch("/api/users", {
    method: "POST",
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify(user)
  });

  if (!response.ok) {
    return { error: "Unable to create user" };
  }

  return { success: true };
}
```

Actions are useful for creating, updating, and deleting data.

# 22. Forms

React Router Form integrates form submission with route actions.

```jsx
import { Form, useNavigation } from "react-router-dom";

function CreateUser() {
  const navigation = useNavigation();

  const submitting = navigation.state === "submitting";

  return (
    <Form method="post">
      <input name="name" required />
      <input name="email" type="email" required />

      <button disabled={submitting}>
        {submitting ? "Saving..." : "Create User"}
      </button>
    </Form>
  );
}
```

# 23. Fetchers

`useFetcher` loads or submits data without triggering a normal page navigation.

```jsx
import { useFetcher } from "react-router-dom";

function LikeButton({ postId }) {
  const fetcher = useFetcher();

  return (
    <fetcher.Form
      method="post"
      action={`/posts/${postId}/like`}
    >
      <button disabled={fetcher.state !== "idle"}>
        Like
      </button>
    </fetcher.Form>
  );
}
```

Useful for:

- Like buttons.
- Inline updates.
- Toggle controls.
- Small forms.
- Background data loading.

## useFetchers

Returns active fetchers in the router.

```jsx
const fetchers = useFetchers();
```

## useSubmit

Programmatically submits data to an action.

```jsx
const submit = useSubmit();

submit(
  { title: "React" },
  {
    method: "post",
    action: "/courses/new"
  }
);
```

# 24. Navigation States

## useNavigation

Returns the current navigation or submission state.

```jsx
const navigation = useNavigation();

const isBusy = navigation.state !== "idle";
```

Common states:

| State | Meaning |
|---|---|
| idle | No pending navigation or submission |
| loading | Route data is loading |
| submitting | Form submission is in progress |

Example:

```jsx
function LoadingIndicator() {
  const navigation = useNavigation();

  if (navigation.state === "idle") {
    return null;
  }

  return <p>Loading...</p>;
}
```

## useRevalidator

Manually triggers route data revalidation.

```jsx
const revalidator = useRevalidator();

revalidator.revalidate();
```

## useNavigationType

Returns how the current location was reached:

- POP
- PUSH
- REPLACE

```jsx
const type = useNavigationType();
```

# 25. Error Handling

Data Router supports route-level error boundaries.

```jsx
{
  path: "/courses/:courseId",
  element: <CourseDetails />,
  loader: courseLoader,
  errorElement: <CourseError />
}
```

Use `useRouteError`:

```jsx
import {
  useRouteError,
  isRouteErrorResponse
} from "react-router-dom";

function CourseError() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    return (
      <>
        <h1>{error.status}</h1>
        <p>{error.statusText}</p>
      </>
    );
  }

  return <h1>Something went wrong.</h1>;
}
```

### React Error Boundary vs React Router Error Boundary

- React Error Boundary handles errors in its descendant component tree.
- React Router route error boundaries handle supported route errors, including loader and action errors.

# 26. Lazy Loading

Lazy loading loads a component only when it is required.

```jsx
import { lazy, Suspense } from "react";

const AdminDashboard = lazy(
  () => import("./pages/AdminDashboard")
);

function App() {
  return (
    <Suspense fallback={<p>Loading page...</p>}>
      <AdminDashboard />
    </Suspense>
  );
}
```

Benefits:

1. Reduces initial JavaScript bundle size.
2. Improves initial loading performance.
3. Loads large pages only when needed.
4. Useful for admin sections and large applications.

Data Router also supports lazy route modules. The exact route-module export format depends on the router mode.

# 27. Navigation Blocking

Navigation blocking is useful when a user has unsaved changes.

`useBlocker` can block client-side navigation in supported Data/Framework router setups.

```jsx
const blocker = useBlocker(isDirty);
```

When blocked, the application can let the user proceed or stay.

```jsx
if (blocker.state === "blocked") {
  return (
    <>
      <p>You have unsaved changes.</p>

      <button onClick={() => blocker.proceed()}>
        Leave page
      </button>

      <button onClick={() => blocker.reset()}>
        Stay here
      </button>
    </>
  );
}
```

`useBeforeUnload` can handle browser document unload events. Browser warning behavior is controlled by the browser.

# 28. Scroll Restoration

ScrollRestoration restores scroll positions during navigation in supported Data and Framework router setups.

```jsx
import { ScrollRestoration } from "react-router-dom";

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

# 29. View Transitions

Modern React Router versions can integrate with the browser View Transitions API.

Example:

```jsx
<Link to="/courses" viewTransition>
  Courses
</Link>
```

Programmatic navigation where supported:

```jsx
navigate("/courses", {
  viewTransition: true
});
```

View transitions are a progressive enhancement. Browser support and API availability depend on the environment and installed version.

# 30. Complete React Router Hooks Reference

Hooks must be called at the top level of React components or custom hooks.

## A. Core routing hooks

### 1. useNavigate

**Purpose:** Programmatic navigation.

```jsx
const navigate = useNavigate();

navigate("/dashboard");
navigate(-1);
```

**Use case:** Redirect after login, logout, or successful form submission.

### 2. useParams

**Purpose:** Reads dynamic route parameters.

```jsx
const { courseId } = useParams();
```

**Use case:** `/courses/:courseId`.

### 3. useLocation

**Purpose:** Returns the current location object.

```jsx
const location = useLocation();

location.pathname;
location.search;
location.hash;
location.state;
```

**Use case:** Reading navigation state and responding to URL changes.

### 4. useSearchParams

**Purpose:** Reads and updates query parameters.

```jsx
const [searchParams, setSearchParams] = useSearchParams();

const page = searchParams.get("page");
```

**Use case:** Filters, search, sorting, pagination.

### 5. useMatch

**Purpose:** Checks whether a URL matches a pattern.

```jsx
const match = useMatch("/courses/:id");
```

### 6. useHref

**Purpose:** Resolves a destination into an href string.

```jsx
const href = useHref("/courses");
```

### 7. useResolvedPath

**Purpose:** Resolves a relative destination against the current route.

```jsx
const path = useResolvedPath("settings");
```

### 8. useNavigationType

**Purpose:** Reads the navigation type.

```jsx
const type = useNavigationType();
```

### 9. useLinkClickHandler

**Purpose:** Provides the click behavior used by router links.

```jsx
const handleClick = useLinkClickHandler("/courses");
```

Useful when building custom link components.

### 10. useInRouterContext

**Purpose:** Checks whether a component is inside a router.

```jsx
const insideRouter = useInRouterContext();
```

### 11. useOutlet

**Purpose:** Returns the matching child route element.

```jsx
const outlet = useOutlet();

return <main>{outlet}</main>;
```

### 12. useOutletContext

**Purpose:** Reads data provided by a parent through Outlet context.

Parent:

```jsx
<Outlet context={{ user, theme }} />
```

Child:

```jsx
const { user, theme } = useOutletContext();
```

## B. Data Router hooks — v6.4+ and v7

### 13. useLoaderData

Reads data returned by the current route loader.

```jsx
const courses = useLoaderData();
```

### 14. useRouteLoaderData

Reads loader data from another route using its route ID.

```jsx
const rootData = useRouteLoaderData("root");
```

### 15. useActionData

Reads the result returned by the route action.

```jsx
const result = useActionData();
```

### 16. useNavigation

Reads pending navigation and submission state.

```jsx
const navigation = useNavigation();
```

### 17. useFetcher

Loads or submits data without normal navigation.

```jsx
const fetcher = useFetcher();
```

### 18. useFetchers

Reads all active fetchers.

```jsx
const fetchers = useFetchers();
```

### 19. useSubmit

Programmatically submits form data.

```jsx
const submit = useSubmit();
```

### 20. useRevalidator

Manually revalidates route loader data.

```jsx
const revalidator = useRevalidator();

revalidator.revalidate();
```

### 21. useRouteError

Reads the error caught by the current route error boundary.

```jsx
const error = useRouteError();
```

### 22. useAsyncValue

Reads a resolved value from an Await boundary.

```jsx
const data = useAsyncValue();
```

### 23. useAsyncError

Reads a rejected deferred value's error inside an Await error element.

```jsx
const error = useAsyncError();
```

### 24. useBlocker

Blocks a client-side navigation when a condition is true.

```jsx
const blocker = useBlocker(isDirty);
```

### 25. useBeforeUnload

Registers a callback for the browser's beforeunload event.

```jsx
useBeforeUnload((event) => {
  event.preventDefault();
  event.returnValue = "";
});
```

### 26. useViewTransitionState

Checks whether a destination is participating in an active view transition.

```jsx
const transitioning = useViewTransitionState("/courses");
```

### 27. useFormAction

Resolves the action URL for a route form. Confirm its availability and signature in the installed version.

```jsx
const action = useFormAction();
```

## C. React Router v5 hooks

### 28. useHistory

Accesses the history object.

```jsx
const history = useHistory();

history.push("/dashboard");
history.replace("/login");
history.goBack();
```

**Replaced by:** useNavigate in v6.

### 29. useRouteMatch

Reads route match information.

```jsx
const match = useRouteMatch("/users/:id");
```

**Replaced by:** useMatch in v6.

### 30. useParams

Reads dynamic route parameters.

### 31. useLocation

Reads the current location.

## Hooks Summary

| Hook | Purpose | Version |
|---|---|---|
| useNavigate | Programmatic navigation | v6+ |
| useParams | Route parameters | v5+ |
| useLocation | Current location | v5+ |
| useSearchParams | Query parameters | v6+ |
| useMatch | Match a route pattern | v6+ |
| useHref | Resolve href | v6+ |
| useResolvedPath | Resolve relative path | v6+ |
| useNavigationType | Navigation type | v6+ |
| useLinkClickHandler | Custom link behavior | v6+ |
| useInRouterContext | Detect router context | v6+ |
| useOutlet | Child route element | v6+ |
| useOutletContext | Parent outlet context | v6+ |
| useLoaderData | Loader result | v6.4+ |
| useRouteLoaderData | Another route's loader data | v6.4+ |
| useActionData | Action result | v6.4+ |
| useNavigation | Pending navigation state | v6.4+ |
| useFetcher | Non-navigation submission/loading | v6.4+ |
| useFetchers | Active fetchers | v6.4+ |
| useSubmit | Programmatic submission | v6.4+ |
| useRevalidator | Revalidate loader data | v6.4+ |
| useRouteError | Route error | v6.4+ |
| useAsyncValue | Resolved Await value | v6.4+ |
| useAsyncError | Await error | v6.4+ |
| useBlocker | Block navigation | v6.4+ |
| useBeforeUnload | Browser unload event | v6+ |
| useViewTransitionState | View transition state | Newer releases; verify version |
| useFormAction | Resolve form action | Verify version |
| useHistory | History object | v5 |
| useRouteMatch | Route matching | v5 |

**Important:** Not every hook is available in every router mode or minor version. Check the documentation for your installed package.

# 31. React Router Project Architecture

A clean application can organize routes, pages, layouts, and components separately.

```text
src/
├── api/
│   └── axiosInstance.js
├── components/
│   ├── Navbar.jsx
│   ├── Footer.jsx
│   └── ProtectedRoute.jsx
├── context/
│   └── AuthContext.jsx
├── layouts/
│   ├── PublicLayout.jsx
│   ├── UserLayout.jsx
│   └── AdminLayout.jsx
├── pages/
│   ├── Home.jsx
│   ├── Courses.jsx
│   ├── CourseDetails.jsx
│   ├── Login.jsx
│   ├── Register.jsx
│   ├── Dashboard.jsx
│   └── NotFound.jsx
├── routes/
│   └── AppRoutes.jsx
└── main.jsx
```

## AppRoutes.jsx example

```jsx
import {
  BrowserRouter,
  Routes,
  Route
} from "react-router-dom";

import PublicLayout from "../layouts/PublicLayout";
import UserLayout from "../layouts/UserLayout";
import AdminLayout from "../layouts/AdminLayout";

import ProtectedRoute from "../components/ProtectedRoute";

import Home from "../pages/Home";
import Courses from "../pages/Courses";
import CourseDetails from "../pages/CourseDetails";
import Login from "../pages/Login";
import Dashboard from "../pages/Dashboard";
import AdminOverview from "../pages/AdminOverview";
import NotFound from "../pages/NotFound";

function AppRoutes() {
  return (
    <BrowserRouter>
      <Routes>

        <Route element={<PublicLayout />}>
          <Route path="/" element={<Home />} />
          <Route path="/courses" element={<Courses />} />

          <Route
            path="/courses/:courseId"
            element={<CourseDetails />}
          />

          <Route path="/login" element={<Login />} />
        </Route>

        <Route element={<ProtectedRoute />}>
          <Route element={<UserLayout />}>
            <Route
              path="/dashboard"
              element={<Dashboard />}
            />
          </Route>
        </Route>

        <Route
          element={
            <ProtectedRoute allowedRoles={["admin"]} />
          }
        >
          <Route path="/admin" element={<AdminLayout />}>
            <Route index element={<AdminOverview />} />
          </Route>
        </Route>

        <Route path="*" element={<NotFound />} />

      </Routes>
    </BrowserRouter>
  );
}

export default AppRoutes;
```

This is an illustrative setup. Adapt it to the actual AuthContext and route structure of your project.

# 32. Common Errors and Solutions

## Error 1: Switch is not exported

**Reason:** Switch belongs to older React Router versions.

**Solution:** Use Routes in v6+.

## Error 2: useHistory is not exported

**Reason:** useHistory was replaced in v6.

**Solution:** Use useNavigate.

## Error 3: Child route is not displaying

**Reason:** The parent layout may be missing Outlet.

**Solution:**

```jsx
function Layout() {
  return (
    <>
      <Navbar />
      <Outlet />
    </>
  );
}
```

## Error 4: Nested route URL is incorrect

Inside `/admin`, use a relative child path:

```jsx
<Route path="/admin" element={<AdminLayout />}>
  <Route path="users" element={<Users />} />
</Route>
```

This creates `/admin/users`.

## Error 5: Refreshing a nested URL gives 404

The hosting server must be configured to serve the SPA entry HTML for valid client-side routes.

## Error 6: Protected page appears before auth is loaded

Show a loading state while the authentication provider initializes.

## Error 7: API is accessible even though route is protected

Frontend protection does not secure the API. Add backend token and permission checks.

## Error 8: Query parameter is undefined

Use `useSearchParams` for query parameters and `useParams` for path parameters.

## Error 9: Router context error

Check that the component is rendered inside the correct top-level router and that you have not accidentally created multiple router providers.

## Error 10: Data Router hooks do not work

Hooks such as `useLoaderData` require the appropriate Data/Framework router context and configured route loaders.

# 33. Best Practices

1. Keep route definitions in a separate file for larger projects.
2. Use nested routes for shared layouts.
3. Use Outlet for child route rendering.
4. Use Link for normal navigation.
5. Use NavLink for active navigation menus.
6. Use useNavigate for programmatic navigation.
7. Use useParams for dynamic path values.
8. Use useSearchParams for filters and pagination.
9. Keep authentication state centralized.
10. Display loading UI while authentication initializes.
11. Use role-based routes where needed.
12. Enforce permissions on the backend.
13. Add a catch-all 404 route.
14. Add route-level error boundaries when using Data Router.
15. Use loaders for route data when appropriate.
16. Use actions for route-based mutations when appropriate.
17. Use fetchers for independent interactions.
18. Lazy-load large route modules.
19. Test direct navigation and refreshes.
20. Follow documentation for the exact installed version.

# 34. Version Comparison

| Feature | v5 | v6 | v6.4+ | v7 |
|---|---|---|---|---|
| Route container | Switch | Routes | Routes / Data Router | Declarative / Data / Framework |
| Component prop | component / render | element | element / route objects | Mode-dependent |
| Redirect | Redirect | Navigate | Navigate / data redirects | Modern redirect APIs |
| Navigation hook | useHistory | useNavigate | useNavigate | useNavigate |
| Nested layouts | Supported | Outlet | Outlet | Outlet / route modules |
| Dynamic params | useParams | useParams | useParams | useParams |
| Query params | Location APIs | useSearchParams | useSearchParams | useSearchParams |
| Loaders | No modern Data Router | No in early v6 | Yes | Yes |
| Actions | No modern Data Router | No in early v6 | Yes | Yes |
| Fetchers | No | No | Yes | Yes |
| Route error handling | Different patterns | Basic route patterns | Data route boundaries | Modern route boundaries |

# 35. Interview Questions and Answers

## Q1. What is React Router?

**Answer:**

"React Router is a routing library for React applications. It helps us map URLs to components and implement navigation, nested routes, dynamic routes, protected routes, and route-based data handling."

## Q2. What is the difference between BrowserRouter and Routes?

**Answer:**

"BrowserRouter provides the routing context and integrates the application with browser history. Routes contains Route definitions and renders the best matching route branch."

## Q3. What is the difference between Link and NavLink?

**Answer:**

"Both are used for client-side navigation. NavLink additionally provides active-state information, so it is useful for highlighting the current page in navigation menus."

## Q4. What is the difference between Link and useNavigate?

**Answer:**

"Link is used for normal navigation through a clickable link. useNavigate is used when navigation needs to happen programmatically, such as after a successful login or API operation."

## Q5. What replaced Switch in v6?

**Answer:**

"Routes replaced Switch. Routes ranks matching routes and renders the best matching route branch, whereas Switch in v5 rendered the first matching route in its child order."

## Q6. What replaced useHistory?

**Answer:**

"useNavigate replaced useHistory in v6. It allows us to navigate to a path or move through browser history."

## Q7. What is Outlet?

**Answer:**

"Outlet is a placeholder in a parent route layout. When a nested child route matches, React Router renders that child component at the Outlet position. This helps us share layouts across multiple pages."

## Q8. What are nested routes?

**Answer:**

"Nested routes are routes defined inside a parent route. They allow related pages to share a common layout, such as an admin sidebar and header."

## Q9. What is the difference between route parameters and query parameters?

**Answer:**

"Route parameters are part of the URL path, such as `/courses/10`, and are accessed using useParams. Query parameters appear after the question mark, such as `/courses?page=2`, and are handled using useSearchParams."

## Q10. What is useLocation?

**Answer:**

"useLocation returns information about the current URL, including pathname, search, hash, and navigation state. It is useful when we need to inspect the current location or read state passed during navigation."

## Q11. What is a protected route?

**Answer:**

"A protected route checks whether the user is authenticated before rendering its content. If the user is not authenticated, it redirects them to the login page. Backend authorization is still required to secure API operations."

## Q12. What is the difference between authentication and authorization?

**Answer:**

"Authentication verifies who the user is. Authorization determines what the authenticated user is allowed to access or perform."

## Q13. What is createBrowserRouter?

**Answer:**

"createBrowserRouter creates a router from route objects. It is commonly used with RouterProvider and supports Data Router features such as loaders, actions, fetchers, and route error handling."

## Q14. What is RouterProvider?

**Answer:**

"RouterProvider connects a configured router to the React application and renders the route tree."

## Q15. What is a loader?

**Answer:**

"A loader is a route-level function that fetches the data required by a route. The component can access its returned data using useLoaderData."

## Q16. What is an action?

**Answer:**

"An action handles route submissions and data mutations, such as creating or updating a record. It commonly works with React Router Form and submission APIs."

## Q17. What is the difference between loader and action?

**Answer:**

"A loader is mainly used to read data required by a route. An action processes a mutation or form submission. Loader data is accessed using useLoaderData, while action data can be accessed using useActionData."

## Q18. What is useFetcher?

**Answer:**

"useFetcher allows a component to load or submit route data without causing normal navigation. It is useful for interactions such as liking a post or updating an item inline."

## Q19. What is useNavigation?

**Answer:**

"useNavigation provides information about the current router navigation or submission state. We can use it to show loading indicators and disable buttons while an operation is in progress."

## Q20. What is useRouteError?

**Answer:**

"useRouteError reads the error caught by the current route error boundary. It allows us to display a route-specific error interface."

## Q21. What is lazy loading?

**Answer:**

"Lazy loading means loading a component or route module only when it is needed. It reduces the initial JavaScript bundle size and can improve the initial loading performance."

## Q22. Does React Router secure backend APIs?

**Answer:**

"No. React Router can protect frontend pages, but the backend must independently validate authentication tokens and user permissions for every protected endpoint."

## Q23. What happens when a user refreshes a nested route?

**Answer:**

"The browser sends a request for that URL to the server. The server must be configured to return the SPA entry HTML. React Router then reads the URL and renders the matching route."

## Q24. What is the difference between Declarative routing and Data Router?

**Answer:**

"Declarative routing defines routes using components such as BrowserRouter, Routes, and Route. Data Router uses a configured router with RouterProvider and adds loaders, actions, fetchers, pending states, and route error handling."

## Q25. What are the three modes in React Router v7?

**Answer:**

"React Router v7 supports Declarative mode for basic component-based routing, Data mode for loaders and actions, and Framework mode for a more integrated application framework with route modules and server/client rendering capabilities."

## Q26. What is useBlocker?

**Answer:**

"useBlocker allows a supported Data or Framework router application to block client-side navigation when a condition is true. It is useful when a user tries to leave a form containing unsaved changes."

## Q27. What is ScrollRestoration?

**Answer:**

"ScrollRestoration helps restore the scroll position during navigation in supported Data and Framework router setups."

## Q28. What is the difference between Navigate and useNavigate?

**Answer:**

"Navigate is a component used for declarative redirects. useNavigate is a hook that provides a function for programmatic navigation."

# 36. Complete Learning Roadmap

Follow this learning order.

## Level 1 — Routing Fundamentals

- What is routing?
- SPA.
- Client-side routing.
- Server-side routing.
- BrowserRouter.
- Routes.
- Route.
- Link.
- NavLink.
- 404 route.

## Level 2 — Navigation and URL Handling

- useNavigate.
- Navigate.
- useLocation.
- useParams.
- Dynamic routes.
- useSearchParams.
- Query parameters.
- Route parameters.

## Level 3 — Nested Routing

- Nested routes.
- Outlet.
- Index routes.
- Layout routes.
- Relative paths.
- Public layout.
- User layout.
- Admin layout.

## Level 4 — Authentication and Authorization

- ProtectedRoute.
- Authentication vs authorization.
- Redirect after login.
- Role-based routing.
- Backend authorization.

## Level 5 — Data Router

- createBrowserRouter.
- RouterProvider.
- Route objects.
- Loaders.
- useLoaderData.
- Actions.
- Form.
- useActionData.
- Route error boundaries.

## Level 6 — Advanced Data APIs

- useNavigation.
- useFetcher.
- useFetchers.
- useSubmit.
- useRevalidator.
- useRouteLoaderData.
- Await and deferred data concepts.

## Level 7 — Advanced Routing

- Lazy loading.
- Route-level code splitting.
- useBlocker.
- useBeforeUnload.
- ScrollRestoration.
- View transitions.
- Framework mode.
- Server rendering and hydration concepts.

## Practical Projects

1. Create Home, About, and Contact pages.
2. Create a course listing and dynamic course details page.
3. Create a dashboard with nested Profile and Settings routes.
4. Implement login and protected routes.
5. Implement admin-only routes.
6. Add filters and pagination using query parameters.
7. Create a Data Router example with a loader and action.
8. Add route error handling and lazy loading.

# 37. Official References

Use official documentation as the final authority for APIs supported by the version installed in your project.

- React Router: https://reactrouter.com/
- React Router v6: https://reactrouter.com/en/6.30.1
- React documentation: https://react.dev/

---

# Final Revision

- **v5:** Switch, Redirect, useHistory.
- **v6:** Routes, Navigate, useNavigate, Outlet.
- **v6.4+:** createBrowserRouter, RouterProvider, loaders, actions, fetchers.
- **v7:** Declarative, Data, and Framework modes.
- `useParams` reads dynamic path parameters.
- `useSearchParams` reads query parameters.
- `Link` is for normal navigation.
- `useNavigate` is for programmatic navigation.
- `Outlet` renders nested child routes.
- Protected routes do not replace backend authorization.
- Loaders read route data.
- Actions process route mutations.
- Fetchers handle data interactions without normal navigation.