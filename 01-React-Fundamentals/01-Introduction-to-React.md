# Chapter 1: React Fundamentals
## Complete React Interview Questions and Answers (Beginner to Intermediate)

**Topics Covered:** Introduction to React, JSX, Components, Props, State, Virtual DOM, Rendering, Reconciliation, Events, Lists, Conditional Rendering and more.

---

## Table of Contents

1. What is React?
2. Why was React introduced?
3. What are the main features of React?
4. Is React a library or a framework?
5. What are the advantages of React?
6. What are the disadvantages of React?
7. What is the difference between React and JavaScript?
8. What is the difference between React and Angular?
9. What is a Single Page Application (SPA)?
10. What is a Multi Page Application (MPA)?
11. What is declarative programming?
12. What is component-based architecture?
13. What are components in React?
14. What are the types of components?
15. What is a functional component?
16. What is a class component?
17. Functional Components vs Class Components.
18. What is JSX?
19. Why do we use JSX?
20. Can we use React without JSX?
21. What are the rules of JSX?
22. What is the difference between JSX and HTML?
23. What is a React Element?
24. What is the difference between a React Element and a Component?
25. What are Props in React?
26. What are the characteristics of Props?
27. Can we modify Props?
28. What is State in React?
29. What are the characteristics of State?
30. Difference between Props and State.
31. What happens when State changes?
32. What is Virtual DOM?
33. How does Virtual DOM work?
34. What is the difference between Real DOM and Virtual DOM?
35. What is Reconciliation in React?
36. What is the Diffing Algorithm?
37. What is React Fiber?
38. What is Rendering in React?
39. What is Re-rendering?
40. What causes a component to re-render?
41. Does React re-render the entire application when State changes?
42. What is the purpose of Keys in React?
43. Why should we not use array indexes as Keys?
44. What is a Fragment in React?
45. What is Conditional Rendering?
46. How do you render lists in React?
47. What are Synthetic Events?
48. How does Event Handling work in React?
49. What is One-Way Data Binding?
50. What is Unidirectional Data Flow?
51. What is Lifting State Up?
52. What is Props Drilling?
53. What is Component Composition?
54. What is the Children Prop?
55. What is the difference between `null`, `undefined`, and `false` in JSX?
56. What is Strict Mode in React?
57. What is the purpose of `createRoot()`?
58. What is the purpose of the `public` and `src` folders?
59. What is the difference between `export default` and named exports?
60. What are the Rules of Hooks?
61. Why should State not be modified directly?
62. What is the difference between Controlled and Uncontrolled Components?
63. What is the difference between `useState` and normal variables?
64. What is the difference between `className` and `class` in JSX?
65. What is the difference between `onClick={handleClick}` and `onClick={handleClick()}`?

---

# Section 1: Introduction to React

### Q1. What is React?

**Interview Answer:**

React is an open-source JavaScript library developed by Meta (formerly Facebook) for building interactive and dynamic user interfaces, especially for web applications.

It follows a component-based architecture, which allows developers to divide complex user interfaces into smaller, reusable components.

React uses a declarative programming approach and efficiently updates the user interface when application data changes.

**Key Points:**
- Developed by Facebook (now Meta).
- JavaScript library for building user interfaces.
- Uses component-based architecture.
- Supports reusable UI components.
- Uses JSX to describe UI.
- Supports one-way data flow.
- Uses reconciliation to update the UI.

**Example:**

```jsx
function App() {
  return (
    <h1>Hello, React!</h1>
  );
}

export default App;
```

**Real-world example:**

Consider an e-commerce application. Instead of creating the entire page in one file, we can divide it into components:

- Navbar
- ProductList
- ProductCard
- Cart
- Footer

Each component can be developed and reused independently.

**Follow-up Questions:**
1. Is React a framework or a library?
2. Who developed React?
3. Can React be used for mobile applications?
4. What makes React different from JavaScript?

---

### Q2. Why was React introduced?

**Interview Answer:**

React was introduced by Facebook to solve the challenges of building and maintaining complex, interactive user interfaces.

In traditional JavaScript applications, developers often had to manually manipulate the DOM whenever application data changed. This could become difficult to manage as applications grew.

React introduced a component-based architecture and declarative UI development, making interfaces easier to build, maintain, and reuse.

**Main Reasons:**

1. To manage complex user interfaces.
2. To make UI components reusable.
3. To simplify DOM updates.
4. To improve code maintainability.
5. To make application development more predictable.
6. To manage frequently changing application data.

---

### Q3. What are the main features of React?

**Interview Answer:**

React provides several features that make it useful for building modern user interfaces.

**Important Features:**

1. **Component-Based Architecture:** Applications are divided into small, reusable components.
2. **JSX:** Allows developers to write HTML-like syntax inside JavaScript.
3. **Virtual DOM:** React maintains an in-memory representation of the UI and uses reconciliation to determine necessary updates.
4. **Declarative UI:** Developers describe what the UI should look like for a given state.
5. **One-Way Data Flow:** Data generally flows from parent components to child components through props.
6. **Hooks:** Allow functional components to use state and other React features.
7. **Reconciliation:** React compares UI representations and determines the required changes.
8. **Efficient Rendering:** React can update only the necessary parts of the DOM.
9. **Developer Ecosystem:** Supports routing, state management, testing and many other integrations through its ecosystem.

---

### Q4. Is React a library or a framework?

**Interview Answer:**

React is a JavaScript library, not a complete framework.

It primarily focuses on building user interfaces. For additional functionality such as routing, data fetching, and global state management, developers can integrate external libraries.

**Example:**

- React – UI development.
- React Router – Client-side routing.
- Redux – State management.
- Axios – HTTP requests.

**Difference:**

| React Library | Framework |
|---|---|
| Focuses primarily on UI development | Provides a broader application structure |
| Developers choose additional tools | Often provides built-in solutions |
| More flexibility in choosing libraries | More predefined conventions |
| Example: React | Example: Angular |

**Important Interview Point:**

React gives developers flexibility in choosing the tools and architecture required for their applications.

---

### Q5. What are the advantages of React?

**Interview Answer:**

React provides several advantages that make application development easier and more maintainable.

1. **Reusability:** Components can be reused throughout an application.
2. **Maintainability:** Applications can be divided into smaller components.
3. **Declarative Approach:** Developers describe the desired UI instead of manually updating the DOM.
4. **Efficient Updates:** React uses reconciliation to determine necessary DOM updates.
5. **Large Ecosystem:** Provides access to libraries, tools and community support.
6. **Easy Integration:** React can be integrated into existing applications.
7. **Developer Tools:** React Developer Tools help inspect components and profile performance.
8. **Mobile Development:** React Native allows developers to build native mobile applications using React concepts.

---

### Q6. What are the disadvantages of React?

**Interview Answer:**

Although React has many advantages, it also has some limitations.

1. **Requires Additional Libraries:** Routing and advanced state management often require additional libraries.
2. **Frequent Ecosystem Changes:** Developers need to keep learning new tools and updates.
3. **SEO Considerations:** Client-side rendered applications may need additional rendering strategies for SEO.
4. **Learning Curve:** Concepts such as Hooks, reconciliation and state management require practice.
5. **JavaScript Knowledge:** Developers need a good understanding of JavaScript, especially ES6 features.
6. **Architecture Decisions:** React gives flexibility, but developers must make decisions about project structure and additional libraries.

**Follow-up:** Is React suitable for large-scale applications?

**Answer:** Yes. React is suitable for large applications when supported by proper architecture, component design, routing, state management and performance optimization.

---

### Q7. What is the difference between React and JavaScript?

**Interview Answer:**

JavaScript is a programming language, whereas React is a JavaScript library used to build user interfaces.

JavaScript provides the programming capabilities, while React provides tools and concepts for building component-based interfaces.

| JavaScript | React |
|---|---|
| Programming language | JavaScript library |
| Used for general programming | Mainly used for building user interfaces |
| Can manipulate DOM directly | Uses React's rendering system |
| Does not require a component architecture | Encourages component-based architecture |
| Supports imperative and functional programming | Encourages declarative UI development |

**Example using JavaScript:**

```javascript
const heading = document.createElement("h1");

heading.textContent = "Hello World";

document.body.appendChild(heading);
```

**Example using React:**

```jsx
function App() {
  return <h1>Hello World</h1>;
}
```

---

### Q8. What is the difference between React and Angular?

**Interview Answer:**

React and Angular are technologies used for building user interfaces and web applications.

React is a JavaScript library primarily focused on UI development, whereas Angular is a full-fledged application framework.

| React | Angular |
|---|---|
| JavaScript library | Full application framework |
| Developed by Meta | Developed by Google |
| Uses JSX | Primarily uses HTML templates |
| Uses JavaScript or TypeScript | Uses TypeScript |
| Flexible architecture | More predefined architecture |
| Uses a Virtual DOM-based reconciliation system | Uses its own rendering and change-detection architecture |
| Routing generally requires additional libraries | Provides Angular Router |

**Important:** React and Angular are both used in large-scale applications. The choice depends on project requirements and team preferences.

---

### Q9. What is a Single Page Application (SPA)?

**Interview Answer:**

A Single Page Application is a web application that loads a single HTML document initially and dynamically updates its content as users navigate or interact with the application.

Instead of requesting a completely new HTML page for every navigation, an SPA can update the required UI using JavaScript.

React is commonly used to build SPAs.

**Example:**

Consider an online shopping application.

When navigating between:
- Home
- Products
- Cart
- Profile

The application can update the displayed content without performing a full-page reload.

**Advantages:**
1. Smooth navigation.
2. Better user experience.
3. Reduced full-page reloads.
4. Efficient client-side interactions.

**Disadvantages:**
1. Initial JavaScript loading can be large.
2. SEO may require additional consideration.
3. Client-side routing and state management need to be handled.

**Follow-up:** Does every React application have to be an SPA?

**Answer:** No. React can also be used in multi-page applications, server-rendered applications and static websites.

---

### Q10. What is a Multi Page Application (MPA)?

**Interview Answer:**

A Multi Page Application is a web application where navigation generally requests a new HTML document from the server for different pages.

Each page may have its own document and server-rendered content.

**Examples:**
- Traditional banking websites.
- Traditional server-rendered e-commerce websites.
- Older PHP-based applications.

**SPA vs MPA**

| SPA | MPA |
|---|---|
| Usually loads one HTML document initially | Navigation commonly loads new HTML documents |
| Client-side navigation is common | Server-side navigation is common |
| Often provides smooth transitions | May involve full-page reloads |
| Can require more initial JavaScript | Can load page-specific resources |
| Example: React SPA | Example: Traditional server-rendered website |

---

### Q11. What is Declarative Programming in React?

**Interview Answer:**

Declarative programming is a programming approach where developers describe what the user interface should look like based on the current application data, rather than manually specifying every DOM operation.

React follows a declarative approach.

**Example:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

Here, we describe how the UI should look when `count` changes.

React handles the necessary rendering and DOM updates.

**Declarative vs Imperative**

| Declarative | Imperative |
|---|---|
| Describes what should happen | Describes how to perform each step |
| React commonly uses this approach | Direct DOM manipulation is an example |
| Easier to reason about UI based on state | Requires explicit DOM operations |

---

# Section 2: Components in React

### Q12. What is Component-Based Architecture?

**Interview Answer:**

Component-based architecture is a software design approach in which an application is divided into smaller, independent and reusable pieces called components.

Each component is responsible for a specific part of the user interface.

React follows this architecture.

**Example:**

An e-commerce application can be divided into:

```text
App
│
├── Navbar
├── Home
│   ├── Banner
│   └── ProductList
│       └── ProductCard
├── Cart
└── Footer
```

**Advantages:**
1. Code reusability.
2. Easy maintenance.
3. Better organization.
4. Easier testing.
5. Separation of concerns.
6. Better collaboration between developers.

---

### Q13. What are Components in React?

**Interview Answer:**

Components are independent and reusable building blocks of a React application.

A component represents a part of the user interface and can contain its own logic, JSX, state and event handlers.

Components allow developers to divide complex applications into smaller manageable pieces.

**Example:**

```jsx
function Welcome() {
  return <h1>Welcome to React</h1>;
}

function App() {
  return (
    <div>
      <Welcome />
      <Welcome />
    </div>
  );
}

export default App;
```

Here, `Welcome` is a reusable component used twice inside `App`.

**Important Rules:**
- Component names must start with an uppercase letter.
- Components should return valid React nodes.
- Components should be pure with respect to their props and state.
- Components can receive data through props.

---

### Q14. What are the types of Components in React?

**Interview Answer:**

React traditionally supports two main ways of defining components:

1. Functional Components.
2. Class Components.

Today, functional components with Hooks are the recommended approach for writing new React components.

**Functional Component:**

```jsx
function Welcome() {
  return <h1>Hello React</h1>;
}
```

**Class Component:**

```jsx
import { Component } from "react";

class Welcome extends Component {
  render() {
    return <h1>Hello React</h1>;
  }
}
```

**Note:** Presentational and container components are architectural patterns, not separate official React component types.

---

### Q15. What is a Functional Component?

**Interview Answer:**

A functional component is a JavaScript function that returns React elements or other valid React nodes to describe the user interface.

Functional components can use Hooks such as `useState`, `useEffect` and `useContext` to manage state and use other React features.

**Example:**

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>{count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

**Advantages:**
1. Simple syntax.
2. Easy to read and maintain.
3. Supports Hooks.
4. Encourages reusable logic through custom Hooks.
5. Recommended for modern React development.

---

### Q16. What is a Class Component?

**Interview Answer:**

A class component is a React component defined using an ES6 JavaScript class that extends `Component` from React.

It must implement a `render()` method that returns the UI.

Class components can manage state using `this.state` and update it using `this.setState()`.

**Example:**

```jsx
import { Component } from "react";

class Counter extends Component {
  constructor(props) {
    super(props);

    this.state = {
      count: 0
    };
  }

  increment = () => {
    this.setState({
      count: this.state.count + 1
    });
  };

  render() {
    return (
      <div>
        <h2>{this.state.count}</h2>

        <button onClick={this.increment}>
          Increment
        </button>
      </div>
    );
  }
}

export default Counter;
```

**Important Points:**
- Uses ES6 classes.
- Extends `React.Component` or imported `Component`.
- Uses `this.state` for state.
- Uses `this.setState()` to update state.
- Uses lifecycle methods such as `componentDidMount()`.

Class components are still supported, but functional components are generally preferred for new development.

---

### Q17. What is the difference between Functional and Class Components?

**Interview Answer:**

Functional and class components are two ways of defining React components. Modern React development generally prefers functional components because Hooks provide a simpler way to manage state and component logic.

| Functional Components | Class Components |
|---|---|
| Defined using JavaScript functions | Defined using ES6 classes |
| Use Hooks for state and effects | Use `this.state` and lifecycle methods |
| Do not use `this` for component state | Use `this` to access class properties |
| Usually less boilerplate | Usually more boilerplate |
| Can use custom Hooks | Cannot call Hooks directly |
| Recommended for new components | Supported for existing applications |

---

# Section 3: JSX and React Elements

### Q18. What is JSX?

**Interview Answer:**

JSX stands for JavaScript XML. It is a syntax extension for JavaScript that allows developers to write HTML-like markup inside JavaScript code.

React uses JSX to describe what the user interface should look like.

JSX is transformed into JavaScript by build tools.

**Example:**

```jsx
function App() {
  const name = "Nikitha";

  return (
    <div>
      <h1>Hello, {name}</h1>
    </div>
  );
}
```

Here, `{name}` is a JavaScript expression embedded inside JSX.

**Advantages:**
1. Makes UI code readable.
2. Allows JavaScript expressions inside markup.
3. Makes component structure easier to understand.
4. Helps organize UI and component logic together.

---

### Q19. Why do we use JSX in React?

**Interview Answer:**

JSX makes it easier to describe and maintain user interfaces by allowing developers to write HTML-like markup inside JavaScript.

Without JSX, developers would need to use functions such as `createElement()` to describe the UI.

**With JSX:**

```jsx
const element = <h1>Hello World</h1>;
```

**Without JSX:**

```jsx
import { createElement } from "react";

const element = createElement(
  "h1",
  null,
  "Hello World"
);
```

JSX improves readability and makes complex UI structures easier to write.

---

### Q20. Can we use React without JSX?

**Interview Answer:**

Yes. JSX is optional in React.

We can create React elements using the `createElement()` API directly.

**Example:**

```jsx
import { createElement } from "react";

function App() {
  return createElement(
    "h1",
    null,
    "Hello React"
  );
}
```

**Important:** JSX is a developer-friendly syntax. React itself does not require JSX.

---

### Q21. What are the rules of JSX?

**Interview Answer:**

JSX follows several important rules.

**1. Return a Single Root Element**

Multiple sibling elements must be wrapped in a parent element or Fragment.

```jsx
function App() {
  return (
    <>
      <h1>Hello</h1>
      <p>Welcome</p>
    </>
  );
}
```

**2. Close All Tags**

```jsx
<img src="image.jpg" alt="Example" />
```

**3. Use `className` Instead of `class`**

```jsx
<h1 className="title">Hello</h1>
```

**4. Use Curly Braces for JavaScript Expressions**

```jsx
const name = "React";

function App() {
  return <h1>Hello {name}</h1>;
}
```

**5. Component Names Must Start with Uppercase**

```jsx
function Header() {
  return <h1>Header</h1>;
}
```

**6. Event Handlers Use CamelCase**

```jsx
<button onClick={handleClick}>Click</button>
```

**7. JavaScript Expressions Can Be Embedded, but Statements Cannot Be Used Directly**

```jsx
const age = 22;

function App() {
  return (
    <h1>{age >= 18 ? "Adult" : "Minor"}</h1>
  );
}
```

---

### Q22. What is the difference between JSX and HTML?

**Interview Answer:**

JSX looks similar to HTML, but it is a JavaScript syntax extension with its own rules.

| JSX | HTML |
|---|---|
| JavaScript syntax extension | Markup language |
| Uses `className` | Uses `class` |
| Uses `htmlFor` | Uses `for` |
| Event handlers use camelCase | HTML event attributes are commonly lowercase |
| Supports JavaScript expressions using `{}` | Does not directly support JavaScript expressions |
| All tags must be properly closed | Some HTML tags can be written without closing tags |
| Components can be used as elements | HTML has predefined elements |

**Example:**

```jsx
function App() {
  const name = "React";

  return (
    <div className="container">
      <label htmlFor="username">Username</label>

      <input id="username" />

      <h1>Hello {name}</h1>
    </div>
  );
}
```

---

### Q23. What is a React Element?

**Interview Answer:**

A React Element is an immutable description of what should appear on the screen.

It contains information such as the element type, props and children.

React uses these element descriptions to determine what UI should be rendered.

**Example:**

```jsx
const element = <h1>Hello React</h1>;
```

Here, `element` is a React Element.

**Important:** A React Element is not the actual DOM node. It is an object describing the UI.

---

### Q24. What is the difference between a React Element and a Component?

**Interview Answer:**

A component is a reusable function or class that describes UI, whereas a React Element is the description of a particular UI element or component instance.

| Component | React Element |
|---|---|
| Defines UI behavior and structure | Describes what should appear |
| Can be a function or class | Is an immutable React object |
| Can receive props | Contains props |
| Can be reused | Represents a particular element description |
| Example: `Welcome` | Example: `<Welcome />` |

**Example:**

```jsx
function Welcome() {
  return <h1>Hello</h1>;
}

const element = <Welcome />;
```

Here:
- `Welcome` is the component.
- `<Welcome />` creates a React Element.

---

# Section 4: Props and State

### Q25. What are Props in React?

**Interview Answer:**

Props stands for properties. Props are used to pass data from a parent component to a child component.

They allow components to be reusable and dynamic.

Props are read-only from the receiving component's perspective.

**Example:**

```jsx
function Student(props) {
  return <h2>Student Name: {props.name}</h2>;
}

function App() {
  return (
    <Student name="Nikitha" />
  );
}
```

**Using Destructuring:**

```jsx
function Student({ name, age }) {
  return (
    <h2>{name} is {age} years old</h2>
  );
}

function App() {
  return (
    <Student name="Nikitha" age={22} />
  );
}
```

**Real-world example:**

In an e-commerce application, a ProductCard component can receive product name, price and image through props.

---

### Q26. What are the characteristics of Props?

**Interview Answer:**

The important characteristics of props are:

1. Props are used to pass data from parent to child.
2. Props are read-only.
3. Props make components reusable.
4. Props can contain strings, numbers, arrays, objects, functions and React elements.
5. Props can be passed through multiple component levels.
6. Props can be used to configure the behavior of child components.

---

### Q27. Can we modify Props in React?

**Interview Answer:**

No. Props should not be modified directly by the receiving component.

React follows a principle of treating props as read-only inputs.

If a child component needs to change data, it should request the parent to update that data through a callback function.

**Incorrect Example:**

```jsx
function Student(props) {
  props.name = "Rahul";

  return <h1>{props.name}</h1>;
}
```

**Correct Example:**

```jsx
function Student({ name, onChangeName }) {
  return (
    <button onClick={onChangeName}>
      Change Name
    </button>
  );
}

function App() {
  const [name, setName] = useState("Nikitha");

  return (
    <Student
      name={name}
      onChangeName={() => setName("Rahul")}
    />
  );
}
```

The parent owns the state and updates it.

---

### Q28. What is State in React?

**Interview Answer:**

State is data managed by a component that can change over time.

When state changes through React's state update mechanism, React schedules a re-render so the UI can reflect the updated data.

In functional components, state is commonly managed using the `useState` Hook.

**Example:**

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Count: {count}</h2>

      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}

export default Counter;
```

**Explanation:**

- `count` – Current state value.
- `setCount` – Function used to update state.
- `useState(0)` – Initializes state with 0.

**Real-world examples of State:**
- Login status.
- Shopping cart items.
- Form input values.
- Search filters.
- Modal visibility.
- Selected products.

---

### Q29. What are the characteristics of State?

**Interview Answer:**

1. State is managed by a component.
2. State can change over time.
3. State updates can trigger re-rendering.
4. State should not be modified directly.
5. State updates are scheduled by React.
6. State is preserved between renders while the component maintains its identity.
7. State is private to the component that owns it unless shared through other React patterns.

---

### Q30. What is the difference between Props and State?

**Interview Answer:**

Props and State are both used to manage data in React, but they have different purposes.

Props are used to pass data from parent to child, whereas state is used to manage data that changes over time within a component.

| Props | State |
|---|---|
| Passed from parent to child | Managed by a component |
| Read-only to the receiving component | Updated using state setters |
| Used for communication between components | Used for dynamic component data |
| Changes when parent provides different values | Changes through state update functions |
| Helps with component reusability | Helps with interactive UI |
| Example: Product name | Example: Cart count |

**Example:**

```jsx
import { useState } from "react";

function Child({ name }) {
  return <h2>{name}</h2>;
}

function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <Child name="Nikitha" />

      <button onClick={() => setCount(count + 1)}>
        {count}
      </button>
    </div>
  );
}
```

Here:
- `name` is a prop.
- `count` is state.

---

### Q31. What happens when State changes?

**Interview Answer:**

When a state setter is called, React schedules an update for the component.

React then renders the component again using the updated state and reconciles the resulting UI with the previous one. It commits the necessary DOM changes.

**Flow:**

```text
User Interaction
       |
       v
State Setter Called
       |
       v
React Schedules Update
       |
       v
Component Renders Again
       |
       v
Reconciliation
       |
       v
Necessary DOM Updates
```

**Important:** Calling a state setter does not immediately change the state variable in the currently executing function.

---

# Section 5: Virtual DOM and Reconciliation

### Q32. What is Virtual DOM?

**Interview Answer:**

Virtual DOM is a commonly used term for the in-memory representation of the UI that React works with during rendering.

React creates React Elements that describe the UI and maintains internal render structures to manage updates.

When state or props change, React renders the component again and uses reconciliation to determine what needs to change in the actual DOM.

**Important Points:**
1. It is not the actual browser DOM.
2. It is not a separate browser feature.
3. React uses element descriptions and internal structures during rendering.
4. Reconciliation determines the necessary UI updates.
5. React commits required changes to the DOM.

---

### Q33. How does Virtual DOM work in React?

**Interview Answer:**

React uses a rendering and reconciliation process to update the UI.

The general process is:

**Step 1: Initial Render**

React renders components and creates the initial UI.

**Step 2: State or Props Change**

When data changes, React schedules another render.

**Step 3: New UI Description**

React calls the relevant component functions again to calculate the new UI.

**Step 4: Reconciliation**

React compares the new result with the previous result to determine what changed.

**Step 5: Commit Phase**

React applies the necessary changes to the actual DOM.

**Example:**

Suppose a counter displays 10.

When the user clicks Increment:
- The state changes from 10 to 11.
- React renders the component again.
- React determines the required UI changes.
- The updated count is committed to the DOM.

---

### Q34. What is the difference between Real DOM and Virtual DOM?

**Interview Answer:**

The Real DOM represents the actual document structure managed by the browser, whereas the term Virtual DOM refers to React's in-memory representation and internal rendering structures used to describe and reconcile the UI.

| Real DOM | Virtual DOM |
|---|---|
| Actual browser DOM | In-memory UI representation |
| Managed by the browser | Managed by React |
| Represents actual HTML elements | Represents UI descriptions |
| Direct DOM manipulation is possible | Updated through React's rendering process |
| Changes can involve browser layout and painting | Helps React determine required DOM changes |

**Important Interview Point:**

Do not claim that every Virtual DOM operation is automatically faster than direct DOM manipulation. Performance depends on the application, workload and implementation.

---

### Q35. What is Reconciliation in React?

**Interview Answer:**

Reconciliation is the process React uses to compare the previous rendered output with the new rendered output and determine what changes are necessary.

When state or props change, React renders the component again and reconciles the result.

React then commits the necessary updates to the DOM.

**Example:**

Before update:

```jsx
<h1>Hello</h1>
<p>Welcome</p>
```

After update:

```jsx
<h1>Hello React</h1>
<p>Welcome</p>
```

React can identify that the heading's content has changed and update the necessary DOM content.

---

### Q36. What is the Diffing Algorithm in React?

**Interview Answer:**

The Diffing Algorithm is the set of comparison strategies React uses during reconciliation to determine how the previous and new UI trees differ.

React uses heuristics rather than comparing every possible pair of nodes.

**Important Rules:**

1. Elements of different types generally produce different trees, so React replaces the previous subtree.
2. Elements of the same type can be updated while preserving the existing DOM node and component state where appropriate.
3. Keys help React match items in lists across renders.
4. Stable keys help React identify which list items were inserted, deleted or moved.

**Example:**

```jsx
const items = ["Apple", "Banana", "Orange"];

function App() {
  return (
    <ul>
      {items.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
}
```

---

### Q37. What is React Fiber?

**Interview Answer:**

React Fiber is the internal reconciliation architecture introduced in React 16.

It allows React to organize rendering work into smaller units and provides the foundation for prioritizing, pausing and resuming rendering work where appropriate.

It also supports modern React capabilities such as concurrent rendering.

**Key Points:**
1. Introduced in React 16.
2. Replaced the older reconciliation architecture.
3. Supports scheduling and prioritization of rendering work.
4. Helps React handle complex UI updates.
5. Provides the foundation for concurrent features.

**Follow-up:** Is React Fiber the same as Virtual DOM?

**Answer:** No. Fiber is React's internal reconciliation architecture, while Virtual DOM is a general term used for React's in-memory UI representation.

---

# Section 6: Rendering and Re-rendering

### Q38. What is Rendering in React?

**Interview Answer:**

Rendering in React means calling a component to determine what UI it should display based on its current props, state and context.

React then reconciles the returned UI description and commits the required changes when necessary.

**Example:**

```jsx
function App() {
  return <h1>Hello React</h1>;
}
```

When React renders `App`, it evaluates the component and determines that it should display an `h1` element.

---

### Q39. What is Re-rendering?

**Interview Answer:**

Re-rendering means React calls a component again to calculate its updated UI.

A re-render does not necessarily mean that the browser DOM will change.

React compares the new output with the previous output and commits only the necessary updates.

**Example:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

When the button is clicked, the component re-renders using the new count value.

---

### Q40. What causes a component to re-render?

**Interview Answer:**

A React component can re-render for several reasons:

1. Its own state is updated.
2. Its parent component re-renders, subject to React's rendering and optimization behavior.
3. A context value it consumes changes.
4. An external store subscription it uses reports an update.

**Important:** Re-rendering does not always mean the DOM is updated.

---

### Q41. Does React re-render the entire application when State changes?

**Interview Answer:**

No. React does not automatically recreate the entire application whenever a component's state changes.

React schedules an update for the component whose state changed. Its descendants may also be rendered as part of the update, depending on the component tree and optimization behavior.

React reconciles the resulting UI and commits only the necessary DOM updates.

**Follow-up:** How can unnecessary re-renders be reduced?

**Answer:**

- Use `React.memo` where appropriate.
- Use `useMemo` for expensive calculations when useful.
- Use `useCallback` when stable function identity is beneficial.
- Keep state close to the components that need it.
- Split large components into smaller components.

---

# Section 7: Lists, Keys and Fragments

### Q42. What is the purpose of Keys in React?

**Interview Answer:**

Keys are special attributes used when rendering lists of elements.

They help React identify which items have changed, been added or removed between renders.

Keys should be unique among siblings.

**Example:**

```jsx
const users = [
  { id: 1, name: "Nikitha" },
  { id: 2, name: "Rahul" },
  { id: 3, name: "Priya" }
];

function UserList() {
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>
          {user.name}
        </li>
      ))}
    </ul>
  );
}
```

Here, `user.id` is used as the key.

**Benefits:**
1. Helps React match list items.
2. Supports correct reconciliation.
3. Helps preserve component identity.
4. Avoids unnecessary replacement of list items.

---

### Q43. Why should we not use Array Indexes as Keys?

**Interview Answer:**

Using array indexes as keys can create problems when a list is reordered, items are inserted or deleted.

React may associate an existing component's state with the wrong list item because the index represents the position rather than the item's identity.

**Example:**

```jsx
{users.map((user, index) => (
  <User key={index} user={user} />
))}
```

This can cause unexpected behavior in dynamic lists.

**Better Approach:**

```jsx
{users.map((user) => (
  <User key={user.id} user={user} />
))}
```

**When is an index acceptable?**

An index can be acceptable when the list is static, never reordered, and items are never inserted or deleted in a way that changes their positions.

---

### Q44. What is a Fragment in React?

**Interview Answer:**

A Fragment allows developers to group multiple React elements without adding an extra DOM element.

It helps keep the DOM structure clean.

**Example:**

```jsx
function App() {
  return (
    <>
      <h1>Hello</h1>
      <p>Welcome to React</p>
    </>
  );
}
```

**Using React.Fragment:**

```jsx
import { Fragment } from "react";

function App() {
  return (
    <Fragment>
      <h1>Hello</h1>
      <p>Welcome</p>
    </Fragment>
  );
}
```

**Important:** The short syntax `<>...</>` cannot accept a key. Use `<Fragment key={...}>` when a keyed Fragment is needed, such as when mapping a list.

---

### Q45. What is Conditional Rendering in React?

**Interview Answer:**

Conditional rendering means displaying different UI elements based on a condition.

React supports JavaScript conditional expressions such as:
- `if/else`
- Ternary operator
- Logical AND (`&&`)
- Switch statements outside JSX.

**Example using Ternary Operator:**

```jsx
function App() {
  const isLoggedIn = true;

  return (
    <div>
      {isLoggedIn ? (
        <h1>Welcome Back</h1>
      ) : (
        <h1>Please Login</h1>
      )}
    </div>
  );
}
```

**Example using Logical AND:**

```jsx
function App() {
  const isAdmin = true;

  return (
    <div>
      {isAdmin && <h1>Admin Dashboard</h1>}
    </div>
  );
}
```

**Example using if/else:**

```jsx
function App() {
  const isLoggedIn = true;

  if (isLoggedIn) {
    return <h1>Welcome</h1>;
  }

  return <h1>Please Login</h1>;
}
```

---

### Q46. How do you render Lists in React?

**Interview Answer:**

We can render lists in React using JavaScript array methods such as `map()`.

The `map()` method iterates through an array and returns a new array of React elements.

**Example:**

```jsx
function App() {
  const fruits = ["Apple", "Banana", "Mango"];

  return (
    <ul>
      {fruits.map((fruit, index) => (
        <li key={fruit}>
          {fruit}
        </li>
      ))}
    </ul>
  );
}
```

**Important:** Each element in a list should have a stable and unique key among its siblings.

---

# Section 8: Events and Data Flow

### Q47. What are Synthetic Events in React?

**Interview Answer:**

Synthetic Events are React's cross-browser event objects that provide a consistent interface for handling events.

They follow the DOM event API and wrap the underlying native browser event.

**Example:**

```jsx
function App() {
  const handleClick = (event) => {
    console.log(event);
  };

  return (
    <button onClick={handleClick}>
      Click Me
    </button>
  );
}
```

Here, `event` is a React SyntheticEvent.

**Important Points:**
1. Provides a consistent event interface.
2. Works with React event handlers.
3. Exposes the native browser event through `event.nativeEvent`.
4. Supports methods such as `preventDefault()` and `stopPropagation()`.

**Interview Note:** React 17 removed event pooling from the web implementation, so SyntheticEvent objects no longer need to be persisted using `event.persist()`.

---

### Q48. How does Event Handling work in React?

**Interview Answer:**

React handles user interactions using event handlers such as `onClick`, `onChange`, `onSubmit` and `onMouseEnter`.

Event handler names use camelCase, and we pass functions as their values.

**Example:**

```jsx
function App() {
  const handleClick = () => {
    alert("Button clicked!");
  };

  return (
    <button onClick={handleClick}>
      Click Me
    </button>
  );
}
```

**Important:**

Correct:

```jsx
<button onClick={handleClick}>
  Click
</button>
```

Incorrect for a normal click handler:

```jsx
<button onClick={handleClick()}>
  Click
</button>
```

The second version calls the function during rendering rather than waiting for the click.

---

### Q49. What is One-Way Data Binding?

**Interview Answer:**

One-way data binding means data flows in one direction, generally from a parent component to a child component through props.

The child component receives the data and displays it.

If the child needs to request a change, the parent can pass a callback function.

**Example:**

```jsx
function Child({ name }) {
  return <h1>Hello {name}</h1>;
}

function Parent() {
  const name = "Nikitha";

  return <Child name={name} />;
}
```

Here, data flows from Parent to Child.

---

### Q50. What is Unidirectional Data Flow?

**Interview Answer:**

Unidirectional data flow means data follows a predictable direction through the component tree.

In React, parent components generally pass data to child components through props.

When a child needs to update parent-owned data, the parent can pass a callback function to the child.

**Flow:**

```text
Parent Component
       |
       | Props
       v
Child Component
       |
       | Callback
       v
Parent Updates State
       |
       v
New Props to Child
```

**Benefits:**
1. Predictable data flow.
2. Easier debugging.
3. Better state management.
4. Easier maintenance.

---

### Q51. What is Lifting State Up?

**Interview Answer:**

Lifting State Up is a React technique in which state is moved from multiple child components to their closest common parent component.

This allows the child components to share and synchronize data.

**Example:**

```jsx
import { useState } from "react";

function Input({ value, onChange }) {
  return (
    <input
      value={value}
      onChange={(e) => onChange(e.target.value)}
    />
  );
}

function Display({ value }) {
  return <h2>{value}</h2>;
}

function App() {
  const [name, setName] = useState("");

  return (
    <div>
      <Input value={name} onChange={setName} />
      <Display value={name} />
    </div>
  );
}
```

Here:
- Parent manages the state.
- Input updates the parent through a callback.
- Display receives the updated value through props.

---

### Q52. What is Props Drilling?

**Interview Answer:**

Props Drilling occurs when data is passed through multiple intermediate components that do not directly need that data, simply to reach a deeply nested child component.

**Example:**

```text
App
 |
 | Props
 v
Dashboard
 |
 | Props
 v
Profile
 |
 | Props
 v
UserDetails
```

If only `UserDetails` needs the user information, passing it through every intermediate component can make the application difficult to maintain.

**How can we avoid Props Drilling?**

1. Context API.
2. Redux or other state management libraries.
3. Component composition.
4. Custom Hooks built around shared state.

---

### Q53. What is Component Composition?

**Interview Answer:**

Component Composition is a technique in React where smaller components are combined to build larger and more complex user interfaces.

Instead of creating one large component, we create smaller reusable components and combine them.

**Example:**

```jsx
function Header() {
  return <h1>My Website</h1>;
}

function Content() {
  return <p>Welcome to my website.</p>;
}

function Footer() {
  return <p>Copyright 2026</p>;
}

function App() {
  return (
    <>
      <Header />
      <Content />
      <Footer />
    </>
  );
}
```

**Advantages:**
1. Reusability.
2. Better code organization.
3. Easier maintenance.
4. Flexible UI design.

---

### Q54. What is the Children Prop?

**Interview Answer:**

The `children` prop is a special prop that React provides to components. It represents the content placed between the opening and closing tags of a component.

**Example:**

```jsx
function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

function App() {
  return (
    <Card>
      <h2>React Interview</h2>
      <p>Prepare for your interview.</p>
    </Card>
  );
}
```

Here, the heading and paragraph are passed to the Card component through `children`.

**Real-world examples:**
- Modal components.
- Card components.
- Layout components.
- Reusable containers.

---

# Section 9: Important React Concepts

### Q55. What is the difference between null, undefined and false in JSX?

**Interview Answer:**

In JSX, `null`, `undefined` and `false` are treated as empty nodes and do not render visible content.

However, they represent different JavaScript values.

| Value | Meaning | JSX Output |
|---|---|---|
| `null` | Intentional absence of value | Nothing |
| `undefined` | Value not assigned or missing | Nothing |
| `false` | Boolean false | Nothing |
| `true` | Boolean true | Nothing |

**Example:**

```jsx
function App() {
  return (
    <div>
      {null}
      {undefined}
      {false}
      {true}
      <h1>Hello</h1>
    </div>
  );
}
```

Only `Hello` will be visibly rendered.

**Tricky Point:**

Be careful with logical AND:

```jsx
function App() {
  const count = 0;

  return (
    <div>
      {count && <h1>Available</h1>}
    </div>
  );
}
```

This renders `0`, because JavaScript evaluates `0 && expression` to `0`.

A safer approach is:

```jsx
{count > 0 && <h1>Available</h1>}
```

---

### Q56. What is Strict Mode in React?

**Interview Answer:**

Strict Mode is a development-only tool in React that helps identify potential problems in an application.

It does not render a visible UI element.

It can help detect:
1. Components that are not pure.
2. Deprecated APIs.
3. Missing Effect cleanup.
4. Other potential problems in development.

**Example:**

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

**Important Interview Point:**

In development, Strict Mode may call component functions more than once and run an additional Effect setup-and-cleanup cycle to expose bugs.

This behavior does not mean production components always render twice.

---

### Q57. What is the purpose of createRoot()?

**Interview Answer:**

`createRoot()` is a React DOM API used to create a React root for displaying a React application inside a browser DOM element.

It is the modern way to initialize a client-rendered React application.

**Example:**

```jsx
import { createRoot } from "react-dom/client";

import App from "./App";

const root = createRoot(
  document.getElementById("root")
);

root.render(<App />);
```

**Explanation:**
- `createRoot()` creates the React root.
- `document.getElementById("root")` selects the HTML container.
- `root.render()` displays the React application inside that container.

**Important:** `createRoot()` is used for client-side rendering. For hydrating HTML that was already rendered on the server, React provides `hydrateRoot()`.

---

### Q58. What is the purpose of the public and src folders?

**Interview Answer:**

In a typical React project created using Vite, the `public` and `src` folders serve different purposes.

**Public Folder:**

Contains static assets that are served directly, such as:
- Favicon.
- Static images.
- Robots.txt.
- Other publicly accessible files.

**Src Folder:**

Contains the main application source code, such as:
- Components.
- Pages.
- CSS files.
- Hooks.
- Context.
- API services.
- Application logic.

**Example Project Structure:**

```text
my-react-app/
│
├── public/
│   └── favicon.ico
│
├── src/
│   ├── components/
│   ├── pages/
│   ├── hooks/
│   ├── context/
│   ├── services/
│   ├── App.jsx
│   ├── main.jsx
│   └── index.css
│
├── index.html
├── package.json
└── vite.config.js
```

**Important:** This is a common Vite project structure, not a mandatory structure imposed by React.

---

### Q59. What is the difference between export default and named exports?

**Interview Answer:**

JavaScript modules support default exports and named exports to share functions, variables and components between files.

**Default Export:**

A module can have one default export.

```jsx
function Navbar() {
  return <h1>Navbar</h1>;
}

export default Navbar;
```

Import:

```jsx
import Navbar from "./Navbar";
```

**Named Export:**

A module can have multiple named exports.

```jsx
export function Header() {
  return <h1>Header</h1>;
}

export function Footer() {
  return <h1>Footer</h1>;
}
```

Import:

```jsx
import { Header, Footer } from "./Components";
```

**Difference:**

| Default Export | Named Export |
|---|---|
| One default export per module | Multiple named exports |
| Imported without curly braces | Imported using curly braces |
| Import name can be chosen by the importer | Import name must match unless aliased |
| `export default Navbar` | `export function Navbar()` |

---

### Q60. What are the Rules of Hooks?

**Interview Answer:**

Hooks are special React functions that allow functional components to use React features such as state, context and effects.

React Hooks must follow specific rules.

**Rule 1: Only Call Hooks at the Top Level**

Hooks should not be called inside:
- Loops.
- Conditions.
- Nested functions.
- Event handlers.

**Incorrect:**

```jsx
if (isLoggedIn) {
  const [user, setUser] = useState(null);
}
```

**Correct:**

```jsx
const [user, setUser] = useState(null);
```

**Rule 2: Only Call Hooks from React Functions**

Hooks should be called from:
- React function components.
- Custom Hooks.

They should not be called from ordinary JavaScript functions.

**Rule 3: Maintain Hook Call Order**

Hooks must be called in the same order on every render.

**Example:**

```jsx
function App() {
  const [count, setCount] = useState(0);

  const [name, setName] = useState("");

  return <h1>{count} {name}</h1>;
}
```

---

### Q61. Why should State not be modified directly?

**Interview Answer:**

React state should not be mutated directly because React relies on state update mechanisms to schedule rendering and determine changes.

Directly changing an object or array stored in state does not tell React that an update is needed.

Instead, we should create a new value and pass it to the state setter.

**Incorrect:**

```jsx
const [user, setUser] = useState({
  name: "Nikitha"
});

user.name = "Rahul";
```

**Correct:**

```jsx
setUser({
  ...user,
  name: "Rahul"
});
```

**For Arrays:**

Incorrect:

```jsx
items.push("React");

setItems(items);
```

Correct:

```jsx
setItems([...items, "React"]);
```

**Important:** Treat objects and arrays in React state as immutable.

---

### Q62. What is the difference between Controlled and Uncontrolled Components?

**Interview Answer:**

Controlled and uncontrolled components are two approaches to managing form input values in React.

In a controlled component, React state controls the input value.

In an uncontrolled component, the DOM manages the input value, and React can access it using a ref.

**Controlled Component:**

```jsx
import { useState } from "react";

function App() {
  const [name, setName] = useState("");

  return (
    <input
      value={name}
      onChange={(e) => setName(e.target.value)}
    />
  );
}
```

Here, React state controls the input value.

**Uncontrolled Component:**

```jsx
import { useRef } from "react";

function App() {
  const inputRef = useRef(null);

  const handleSubmit = () => {
    alert(inputRef.current.value);
  };

  return (
    <div>
      <input ref={inputRef} />

      <button onClick={handleSubmit}>
        Submit
      </button>
    </div>
  );
}
```

Here, the DOM maintains the input value, and the ref provides access to it.

**Difference:**

| Controlled | Uncontrolled |
|---|---|
| Value managed by React state | Value managed by DOM |
| Uses `value` or `checked` | Commonly uses `defaultValue` or `defaultChecked` |
| Uses `onChange` to update state | Uses refs to read the value |
| Provides more control over form data | Can require less state management |
| Useful for dynamic validation | Useful for simpler form scenarios |

---

### Q63. What is the difference between useState and normal variables?

**Interview Answer:**

A normal JavaScript variable does not trigger a React re-render when its value changes.

However, updating React state using its setter schedules a re-render.

**Normal Variable:**

```jsx
function App() {
  let count = 0;

  const increment = () => {
    count++;
    console.log(count);
  };

  return (
    <button onClick={increment}>
      Click
    </button>
  );
}
```

The variable changes, but React does not automatically re-render because of that change.

**Using useState:**

```jsx
import { useState } from "react";

function App() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```

Here, the state setter schedules an update and the displayed value can change.

---

### Q64. What is the difference between className and class in JSX?

**Interview Answer:**

In JSX, we use `className` instead of `class` to assign CSS classes to elements.

**HTML:**

```html
<h1 class="title">Hello</h1>
```

**JSX:**

```jsx
<h1 className="title">Hello</h1>
```

`className` is the React JSX property used for the DOM element's CSS class attribute.

---

### Q65. What is the difference between onClick={handleClick} and onClick={handleClick()}?

**Interview Answer:**

`onClick={handleClick}` passes a function reference to React. The function is called when the user clicks the element.

`onClick={handleClick()}` calls the function immediately during rendering and passes its return value as the handler.

**Correct:**

```jsx
function App() {
  const handleClick = () => {
    alert("Clicked!");
  };

  return (
    <button onClick={handleClick}>
      Click Me
    </button>
  );
}
```

**Incorrect for a normal click handler:**

```jsx
<button onClick={handleClick()}>
  Click Me
</button>
```

**What if we need to pass arguments?**

Use an arrow function:

```jsx
function App() {
  const handleClick = (name) => {
    alert(name);
  };

  return (
    <button onClick={() => handleClick("Nikitha")}>
      Click Me
    </button>
  );
}
```

---

# Section 10: Interview Practice – Tricky Questions

### Q66. Does changing a normal variable cause a React component to re-render?

**Answer:** No. Changing a normal variable does not automatically schedule a React render. A state update, parent render or another React update mechanism is needed.

### Q67. Does every re-render update the actual DOM?

**Answer:** No. React can re-render a component without making any DOM changes if the resulting UI is unchanged.

### Q68. Can a component return multiple elements?

**Answer:** Yes. It can return multiple sibling elements wrapped in a parent element or Fragment.

### Q69. Can Props be functions?

**Answer:** Yes. Functions can be passed as props, commonly to allow child components to communicate with parent components.

### Q70. Can State store objects and arrays?

**Answer:** Yes. React state can store strings, numbers, booleans, objects, arrays and other JavaScript values. Objects and arrays should be updated immutably.

### Q71. Is JSX mandatory in React?

**Answer:** No. JSX is optional. React elements can also be created using `createElement()`.

### Q72. Can we use JavaScript inside JSX?

**Answer:** Yes. JavaScript expressions can be included inside curly braces `{}`.

### Q73. Can we use Hooks inside class components?

**Answer:** No. Hooks cannot be called directly inside class components. Class components use state and lifecycle methods.

### Q74. Can a React component return null?

**Answer:** Yes. Returning `null` means the component renders no visible UI.

```jsx
function App() {
  return null;
}
```

### Q75. What happens if we update State with the same value?

**Answer:** React can skip rendering work when the new state is identical to the current state according to `Object.is`. However, code should not depend on an exact number of component function calls.

### Q76. Are Props and State synchronous?

**Answer:** State setters schedule updates rather than immediately changing the state variable in the current render. Props are values supplied by the parent during rendering.

### Q77. Why should components start with an uppercase letter?

**Answer:** React uses capitalization in JSX to distinguish custom React components from built-in HTML elements.

```jsx
<Header />
```

is treated as a React component, whereas:

```jsx
<header />
```

is treated as a built-in DOM element.

### Q78. What happens if we forget the key in a list?

**Answer:** React may show a development warning. Without stable keys, React has less reliable information for matching list items between renders.

### Q79. Can we use an index as a key?

**Answer:** Yes, but only when the list is static and its order and membership do not change in a way that affects item identity. Stable unique IDs are preferred.

### Q80. Is React responsible for routing?

**Answer:** React itself does not provide a built-in general-purpose routing system. Libraries such as React Router are commonly used for routing.

---

# Quick Revision: Important Differences

| Concepts | Main Difference |
|---|---|
| React vs JavaScript | UI library vs programming language |
| React vs Angular | UI library vs full framework |
| SPA vs MPA | Single document navigation vs multiple document navigation |
| JSX vs HTML | JavaScript syntax extension vs markup language |
| Props vs State | Parent-provided inputs vs component-managed data |
| Functional vs Class Components | Functions vs ES6 classes |
| Real DOM vs Virtual DOM | Browser DOM vs in-memory UI representation |
| Rendering vs Re-rendering | Calculating UI vs calculating UI again |
| Component vs Element | UI definition vs UI description |
| Controlled vs Uncontrolled | React-managed input vs DOM-managed input |
| `null` vs `undefined` | Intentional absence vs missing/unassigned value |
| Default vs Named Export | One default export vs multiple named exports |

---

# Interview Preparation Checklist

Use this checklist to track your preparation.

- [ ] Explain React in your own words.
- [ ] Explain why React is used.
- [ ] Explain React features and advantages.
- [ ] Understand SPA and MPA.
- [ ] Explain component-based architecture.
- [ ] Write functional components independently.
- [ ] Understand class components.
- [ ] Explain JSX and its rules.
- [ ] Differentiate React Elements and Components.
- [ ] Explain Props and State with examples.
- [ ] Understand Virtual DOM and Reconciliation.
- [ ] Explain React Fiber.
- [ ] Understand Rendering and Re-rendering.
- [ ] Explain Keys and Fragments.
- [ ] Practice Conditional Rendering.
- [ ] Understand Event Handling.
- [ ] Explain Props Drilling and Lifting State Up.
- [ ] Understand Component Composition.
- [ ] Explain Strict Mode and createRoot.
- [ ] Understand Controlled and Uncontrolled Components.
- [ ] Practice all 80 questions without looking at the answers.

---

## End of Chapter 1

**Next Chapter:** Chapter 2 – React Hooks (Complete Interview Questions and Answers)

Topics include `useState`, `useEffect`, `useContext`, `useRef`, `useMemo`, `useCallback`, `useReducer`, `useLayoutEffect`, custom Hooks, and tricky Hooks interview questions.

**Preparation Tip:** Do not memorize definitions word for word. Understand each concept, explain it in your own words, and write the code examples without referring to the answers.