# React Performance — Interview Questions and Answers

## Table of Contents
1. [How do you approach React performance optimization?](#q1-how-do-you-approach-react-performance-optimization)
2. [What is React.memo?](#q2-what-is-reactmemo)
3. [What is the difference between useMemo and useCallback?](#q3-what-is-the-difference-between-usememo-and-usecallback)
4. [Why might React.memo not prevent a render?](#q4-why-might-reactmemo-not-prevent-a-render)
5. [How does Context affect performance?](#q5-how-does-context-affect-performance)
6. [What is list virtualization?](#q6-what-is-list-virtualization)
7. [What is the purpose of React Profiler?](#q7-what-is-the-purpose-of-react-profiler)
8. [What does useTransition do?](#q8-what-does-usetransition-do)
9. [Does memoization improve every component?](#q9-does-memoization-improve-every-component)
10. [How does code splitting help performance?](#q10-how-does-code-splitting-help-performance)

## Q1. How do you approach React performance optimization?
**Interview answer:** I first reproduce and measure the problem using React DevTools Profiler and browser performance tools. Then I identify whether the bottleneck is rendering, expensive computation, DOM size, network, or bundle size. I apply the smallest targeted optimization and measure again.

## Q2. What is React.memo?
**Interview answer:** `memo` is a higher-order component that can skip rendering a component when its props have not changed according to its comparison. It is useful for expensive components receiving stable props, but it is an optimization rather than a correctness guarantee.

## Q3. What is the difference between useMemo and useCallback?
**Interview answer:** `useMemo` memoizes a computed value. `useCallback` memoizes a function reference. I use them when they prevent expensive recalculation or help a memoized child receive stable props.

## Q4. Why might React.memo not prevent a render?
**Interview answer:** By default, React compares props shallowly. If the parent creates a new object, array, or function on every render, the prop reference changes and the child may render again. The component's own state or consumed Context can also cause it to render.

## Q5. How does Context affect performance?
**Interview answer:** When a provider value changes, components consuming that Context are notified. If the provider creates a new object on every render, consumers may update even when the meaningful data is unchanged. I can stabilize values when appropriate or split Contexts by responsibility.

## Q6. What is list virtualization?
**Interview answer:** Virtualization renders only the rows that are visible, plus a small buffer, instead of mounting every row in a large collection. It reduces DOM size and improves scrolling performance for very large lists.

## Q7. What is the purpose of React Profiler?
**Interview answer:** React Profiler helps me inspect commits, component render durations, and which components rendered. I use it to find expensive parts of the React tree rather than guessing.

## Q8. What does useTransition do?
**Interview answer:** `useTransition` marks certain state updates as non-urgent. React can keep urgent interactions responsive while it works on the transition update. It does not make the calculation itself faster.

## Q9. Does memoization improve every component?
**Interview answer:** No. Memoization has comparison and maintenance costs. For a cheap component or props that change every time, it may not help. I profile first and memoize only when it addresses a measured bottleneck.

## Q10. How does code splitting help performance?
**Interview answer:** Code splitting divides the JavaScript bundle into smaller chunks that can be loaded when needed. With `React.lazy` and `Suspense`, I can defer route or feature code and reduce the initial amount of JavaScript the user must download and parse.
