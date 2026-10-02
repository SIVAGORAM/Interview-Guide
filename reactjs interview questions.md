# React JS Front-End Interview Preparation Guide
### For 6 months – 1 year experience | Questions + Simple Answers + Short Examples

> **How to use this document (2-day plan)**
> - **Day 1:** Parts 1 to 4 (Intro, JavaScript, HTML/CSS, React Core). Say the answers out loud.
> - **Day 2:** Parts 5 to 10 (Hooks, Performance, Routing, Redux, Coding Tasks, HR). Then read the Cheat Sheet at the end.
> - Always answer in this format: **Definition → Why we use it → Small example → Where you used it in your project.**
> - If you don't know something, say: *"I haven't worked on this in depth, but my understanding is..."* Honesty plus a good guess is better than silence.

---

## Table of Contents
1. [Introduction and Project Questions](#part-1--introduction-and-project-questions)
2. [JavaScript Essentials](#part-2--javascript-essentials)
3. [HTML and CSS Essentials](#part-3--html-and-css-essentials)
4. [React Core Concepts](#part-4--react-core-concepts)
5. [React Hooks](#part-5--react-hooks)
6. [Performance Optimization](#part-6--performance-optimization)
7. [Routing, Forms and API Calls](#part-7--routing-forms-and-api-calls)
8. [State Management (Context and Redux)](#part-8--state-management)
9. [Advanced and Misc React Topics](#part-9--advanced-and-misc-topics)
10. [Coding Tasks They Often Ask](#part-10--coding-tasks)
11. [HR / Behavioural Questions](#part-11--hr--behavioural-questions)
12. [Last-Minute Cheat Sheet](#part-12--last-minute-cheat-sheet)

---

# Part 1 — Introduction and Project Questions

### Q1. Tell me about yourself.
**Answer (template, adjust with your details):**
"Hello, my name is ___. I have around one year of experience as a front-end developer. My main skills are HTML, CSS, JavaScript and React JS. I have worked with React hooks, React Router, API integration using fetch/axios, and basic state management with Context API/Redux. In my current project, I built ___ (for example, a dashboard / e-commerce UI / admin panel). I enjoy building clean, reusable components and I am keen to learn more. That is why I am excited about this opportunity."

**Tips:** Keep it 40–60 seconds. Don't list everything. End with why you want this job.

### Q2. Explain your current project.
**Answer template:**
1. **What** the project is (one line): "It is a ___ application for ___."
2. **Tech stack:** "React, React Router, Axios, CSS/Tailwind/Material UI, Redux/Context."
3. **Your role:** "I worked on ___ pages/components, API integration, form validation, bug fixing."
4. **One challenge + how you solved it:** "The list page was slow with many records, so I used pagination and `useMemo`."

> Prepare this carefully. Almost every interviewer starts here, and the next questions come from what you say.

### Q3. What are your roles and responsibilities?
"Developing UI components from design (Figma), integrating REST APIs, handling state, making the pages responsive, fixing bugs, writing reusable components, and doing code reviews/Git pull requests with the team."

### Q4. What challenges did you face and how did you solve them?
Pick **one real example.** Good examples:
- *Unnecessary re-renders* → fixed using `React.memo`, `useCallback`, `useMemo`.
- *API data not showing (undefined error)* → used optional chaining `data?.name` and loading state.
- *Infinite loop in useEffect* → fixed the dependency array.

### Q5. What is the difference between a library and a framework? Is React a library or a framework?
React is a **library** for building user interfaces. A library gives you tools and **you** decide the structure. A framework (like Angular) gives a full structure and **it** controls the flow. React only handles the "View" layer; for routing and state you add other libraries.

---

# Part 2 — JavaScript Essentials

> React is JavaScript. Interviewers always check JS basics for freshers/juniors.

### Q6. What is the difference between `var`, `let` and `const`?
| | `var` | `let` | `const` |
|---|---|---|---|
| Scope | Function | Block | Block |
| Re-declare | Yes | No | No |
| Re-assign | Yes | Yes | No |
| Hoisting | Hoisted, value `undefined` | Hoisted, but in *Temporal Dead Zone* | Same as `let` |

```js
const user = { name: "Ravi" };
user.name = "Kiran";   // allowed (object content can change)
// user = {};          // error (can't re-assign the variable)
```
**Point to remember:** `const` stops re-assignment, not changing the inside of an object/array.

### Q7. What is hoisting?
JavaScript moves **declarations** to the top of their scope before running the code. `var` is hoisted with value `undefined`. Function declarations are fully hoisted. `let` and `const` are hoisted but cannot be used before declaration (Temporal Dead Zone → ReferenceError).
```js
console.log(a); // undefined
var a = 5;
console.log(b); // ReferenceError
let b = 5;
```

### Q8. What is a closure?
A closure is a function that **remembers the variables from its outer function**, even after the outer function has finished.
```js
function counter() {
  let count = 0;
  return function () { count++; return count; };
}
const inc = counter();
inc(); // 1
inc(); // 2
```
**Use cases:** data privacy, counters, `useState` internals, debounce/throttle, event handlers.

### Q9. What is the difference between `==` and `===`?
`==` compares value after **type conversion** (`"5" == 5` → true). `===` compares value **and type** (`"5" === 5` → false). Always prefer `===`.

### Q10. What are arrow functions? How are they different from normal functions?
Short syntax: `const add = (a, b) => a + b;`
Differences:
- Arrow functions **don't have their own `this`**; they take `this` from the surrounding scope.
- No `arguments` object.
- Can't be used as constructors (`new`).

### Q11. What is `this` in JavaScript?
`this` refers to the object that is **calling** the function.
- In an object method → that object.
- In a normal function (non-strict) → `window`; (strict mode) → `undefined`.
- In an arrow function → `this` of the outer scope.
- You can set it manually with `call`, `apply`, `bind`.

### Q12. Difference between `call`, `apply` and `bind`?
All three set `this` manually.
- `call(obj, a, b)` → calls the function immediately, arguments one by one.
- `apply(obj, [a, b])` → calls immediately, arguments as an array.
- `bind(obj, a, b)` → **returns a new function**, to be called later.

### Q13. What is destructuring?
Taking values out of arrays/objects into variables.
```js
const { name, age } = { name: "Anu", age: 22 };
const [first, second] = [10, 20];
```
In React we use it for props: `function Card({ title, price }) {...}`

### Q14. What are the spread (`...`) and rest (`...`) operators?
- **Spread** *expands* an array/object: `const copy = [...arr, 4]; const newObj = { ...obj, age: 25 };`
- **Rest** *collects* the remaining items: `function sum(...nums) {}`

Spread is used heavily in React to update state **without mutating** it.

### Q15. Explain `map`, `filter`, `reduce`, `find`, `forEach`.
```js
const nums = [1, 2, 3, 4];
nums.map(n => n * 2);          // [2,4,6,8]  → transforms each item, returns new array
nums.filter(n => n % 2 === 0); // [2,4]      → keeps items that pass the test
nums.find(n => n > 2);         // 3          → first matching item
nums.reduce((acc, n) => acc + n, 0); // 10   → reduces to a single value
nums.forEach(n => console.log(n));   // no return value
```
`map` is used in React to render lists.

### Q16. What is the difference between `map` and `forEach`?
`map` **returns a new array**; `forEach` returns `undefined`. Use `map` when you need the result (like rendering JSX).

### Q17. What is a Promise? What are its states?
A Promise represents a value that will be available **in the future** (like an API response). States: **pending → fulfilled (resolved) or rejected.**
```js
fetch(url).then(res => res.json()).then(data => console.log(data)).catch(err => console.log(err));
```

### Q18. What is `async/await`?
A cleaner way to write Promise code. `async` makes a function return a promise; `await` pauses until the promise is done. Use `try/catch` for errors.
```js
async function getUsers() {
  try {
    const res = await fetch("https://jsonplaceholder.typicode.com/users");
    const data = await res.json();
    return data;
  } catch (error) {
    console.log("Error:", error);
  }
}
```

### Q19. What is the event loop? (simple answer)
JavaScript is **single-threaded** (one task at a time). Async tasks (setTimeout, fetch) go to the browser's Web APIs. When they finish, their callbacks wait in a queue. The **event loop** pushes them to the call stack only when the stack is empty. **Promises (microtask queue) run before `setTimeout` (macrotask queue).**
```js
console.log(1);
setTimeout(() => console.log(2), 0);
Promise.resolve().then(() => console.log(3));
console.log(4);
// Output: 1 4 3 2
```

### Q20. What is the difference between shallow copy and deep copy?
- **Shallow copy:** copies only the first level; nested objects still share the same reference (`{...obj}`, `Object.assign`).
- **Deep copy:** copies everything independently (`structuredClone(obj)` or `JSON.parse(JSON.stringify(obj))`).

### Q21. What is the difference between `null` and `undefined`?
`undefined` → variable declared but no value assigned. `null` → value intentionally set to "nothing".

### Q22. What are truthy and falsy values?
Falsy: `false, 0, "", null, undefined, NaN`. Everything else is truthy (including `[]` and `{}`).

### Q23. What is optional chaining (`?.`) and nullish coalescing (`??`)?
```js
const city = user?.address?.city;   // no error if address is undefined
const name = input ?? "Guest";      // default only if null/undefined (not for 0 or "")
```

### Q24. What is event bubbling, capturing and delegation?
- **Bubbling:** event goes from the clicked element **up** to its parents (default).
- **Capturing:** event goes from the top **down** to the target.
- **Delegation:** put one listener on the parent to handle many children using `event.target`. Good for performance.
- `event.stopPropagation()` stops bubbling; `event.preventDefault()` stops default behavior (like form submit).

### Q25. Difference between `localStorage`, `sessionStorage` and cookies?
| | localStorage | sessionStorage | Cookies |
|---|---|---|---|
| Lifetime | Until manually cleared | Until the tab closes | Until expiry date |
| Size | ~5–10 MB | ~5 MB | ~4 KB |
| Sent to server | No | No | Yes, with each request |

### Q26. What is debouncing and throttling?
- **Debounce:** run the function only **after the user stops** doing the action for X ms (search box).
- **Throttle:** run the function **at most once in X ms** (scroll, resize).
```js
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
```

### Q27. What are ES6 modules? (`import`/`export`)
Split code into files. `export default Component` (one per file) and `export const x` (named, many). Import: `import Component from "./Component"` and `import { x } from "./file"`.

### Q28. What is the difference between `slice` and `splice`?
`slice(start, end)` → returns a **new** array, original unchanged. `splice(start, count, ...items)` → **changes the original** array. In React state, use `slice`/`filter`, never `splice` directly.

### Q29. What is a higher-order function?
A function that **takes a function as an argument or returns a function.** Examples: `map`, `filter`, `reduce`, `setTimeout`.

### Q30. What is a pure function?
Same input → always same output, and **no side effects** (doesn't change outside data). React components should be pure.

---

# Part 3 — HTML and CSS Essentials

### Q31. What is semantic HTML? Why use it?
Using tags that **describe meaning**: `<header>, <nav>, <main>, <section>, <article>, <footer>` instead of only `<div>`. Benefits: SEO, accessibility (screen readers), readability.

### Q32. Difference between `<div>` and `<span>`? Block vs inline?
`div` is **block** (takes full width, starts a new line). `span` is **inline** (takes only needed width, no new line). `inline-block` is inline but allows width/height.

### Q33. Explain the CSS box model.
Every element is a box: **Content → Padding → Border → Margin** (inside to outside). With `box-sizing: border-box`, width includes padding and border (recommended).

### Q34. What is the difference between `display: none` and `visibility: hidden`?
`display: none` removes the element from the layout (no space). `visibility: hidden` hides it but **space is kept.**

### Q35. Explain CSS `position` values.
- `static` – default.
- `relative` – moves relative to its normal place.
- `absolute` – relative to the nearest **positioned** parent.
- `fixed` – relative to the viewport (stays on scroll).
- `sticky` – normal until a scroll point, then sticks.

### Q36. What is Flexbox? Important properties?
A one-dimensional layout system (row or column).
```css
.container {
  display: flex;
  justify-content: space-between; /* main axis */
  align-items: center;            /* cross axis */
  flex-direction: row;            /* or column */
  flex-wrap: wrap;
  gap: 10px;
}
```
**Center a div:** `display:flex; justify-content:center; align-items:center;`

### Q37. What is CSS Grid? Flexbox vs Grid?
Grid is **two-dimensional** (rows and columns together). Flexbox is **one-dimensional.** Use Flexbox for components (navbar, cards row), Grid for page layouts.
```css
.grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
```

### Q38. What is CSS specificity?
The rule that decides which style wins: **inline style > ID > class/attribute/pseudo-class > element.** `!important` overrides all (avoid it).

### Q39. What are `px`, `em`, `rem`, `%`, `vh/vw`?
`px` fixed; `em` relative to the parent font size; `rem` relative to the **root** font size; `%` relative to the parent; `vh/vw` percent of viewport height/width.

### Q40. How do you make a website responsive?
Use relative units, Flexbox/Grid, **media queries**, flexible images (`max-width: 100%`), and the viewport meta tag. Approach: mobile-first.
```css
@media (max-width: 768px) { .card { width: 100%; } }
```

### Q41. What is the difference between `id` and `class`?
`id` is **unique** per page; `class` can be reused on many elements. In React, `class` is written as `className`.

### Q42. What are pseudo-classes and pseudo-elements?
Pseudo-class: a state, e.g. `:hover`, `:focus`, `:nth-child(2)`. Pseudo-element: part of an element, e.g. `::before`, `::after`.

### Q43. What is `z-index`?
Controls stacking order of **positioned** elements. Higher value is on top.

### Q44. Which CSS approaches have you used in React?
Plain CSS, CSS Modules (scoped styles), Styled-components, Tailwind CSS, Material UI/Bootstrap. **Mention what you actually used.**

---

# Part 4 — React Core Concepts

### Q45. What is React?
React is an open-source **JavaScript library** (made by Facebook/Meta) for building user interfaces using **reusable components.** It is mainly used for Single Page Applications (SPA).

### Q46. Why use React? What are its features?
- **Component-based** → reusable code.
- **Virtual DOM** → fast updates.
- **One-way data flow** → easy to debug.
- **JSX** → HTML-like syntax in JavaScript.
- **Hooks** → state and lifecycle in functional components.
- Big community and ecosystem.

### Q47. What is JSX?
JSX = JavaScript XML. It lets us write HTML-like code inside JavaScript. Browsers don't understand it, so **Babel** converts it into `React.createElement()` calls.
```jsx
const element = <h1 className="title">Hello {name}</h1>;
```
**JSX rules:** return **one parent element** (or a Fragment), use `className` not `class`, close all tags (`<img />`), write JS inside `{}`, use camelCase (`onClick`, `tabIndex`).

### Q48. What is the Virtual DOM? How does it work?
The Virtual DOM is a **lightweight JavaScript copy** of the real DOM. Steps:
1. State changes → React creates a **new Virtual DOM.**
2. React **compares** it with the previous one (**diffing**).
3. React finds the minimum changes and updates **only those parts** of the real DOM (**reconciliation**).

Updating the real DOM is slow; this makes React fast.

### Q49. What is the difference between the real DOM and the virtual DOM?
| Real DOM | Virtual DOM |
|---|---|
| Updates are slow | Updates are fast |
| Re-paints more of the page | Updates only changed parts |
| Direct manipulation | In-memory JS object |

### Q50. What is reconciliation / the diffing algorithm?
It's how React decides what to update. Two simple rules: (1) elements of **different types** → React rebuilds the whole subtree; (2) for **lists**, `key` identifies which item changed.

### Q51. What is a component? Types of components?
A component is an independent, reusable piece of UI. Two types:
- **Functional components** (modern, use hooks) ✅
- **Class components** (older, use `this.state` and lifecycle methods)
```jsx
function Welcome({ name }) {
  return <h1>Hello, {name}</h1>;
}
```
Component names **must start with a capital letter.**

### Q52. What are props?
Props (properties) are **inputs passed from a parent to a child component.** They are **read-only** (immutable); the child must not change them.
```jsx
<Card title="Phone" price={999} />
function Card({ title, price }) { return <p>{title}: ₹{price}</p>; }
```

### Q53. What is state?
State is **data that belongs to a component and can change over time.** When state changes, the component **re-renders.**
```jsx
const [count, setCount] = useState(0);
```

### Q54. Props vs State — the most asked question!
| Props | State |
|---|---|
| Passed from parent | Owned by the component itself |
| **Read-only** | **Can be changed** (using setter) |
| For configuration | For dynamic data |
| Change causes re-render of child | Change causes re-render of that component |

### Q55. Why shouldn't we update state directly?
Directly changing (`state.count = 5`) **does not trigger a re-render** and breaks React's change detection. Always use the setter and create a **new** object/array.
```jsx
setUser({ ...user, age: 25 });       // object
setItems([...items, newItem]);       // add to array
setItems(items.filter(i => i.id !== id)); // remove from array
```

### Q56. Is `setState` synchronous or asynchronous?
**Asynchronous (batched).** React groups updates for performance, so the new value is not available on the next line. If the new state depends on the old one, use the **functional update:**
```jsx
setCount(prev => prev + 1);
```

### Q57. What are keys in React? Why are they important?
A `key` is a **unique, stable identifier** for list items. It helps React know which item changed, was added, or was removed, which makes updates efficient and correct.
```jsx
{users.map(user => <li key={user.id}>{user.name}</li>)}
```
**Avoid using index as a key** when the list can be reordered, added to, or deleted from. It can cause wrong UI/state bugs.

### Q58. How do you do conditional rendering?
```jsx
{isLoggedIn ? <Dashboard /> : <Login />}   // ternary
{isLoading && <Spinner />}                  // && operator
if (!data) return null;                     // early return
```
Careful: `{count && <p>..</p>}` shows `0` when count is 0. Use `count > 0 &&`.

### Q59. How do you handle events in React?
Use camelCase handlers and pass a **function** (not a function call).
```jsx
<button onClick={handleClick}>Click</button>      // ✅
<button onClick={() => handleDelete(id)}>Del</button> // ✅ with argument
<button onClick={handleClick()}>Click</button>    // ❌ runs immediately
```
React uses **SyntheticEvent**, a wrapper that works the same across all browsers.

### Q60. What are controlled and uncontrolled components?
- **Controlled:** form input value is controlled by **React state.** (Recommended)
- **Uncontrolled:** the **DOM** keeps the value; you read it with `useRef`.
```jsx
const [name, setName] = useState("");
<input value={name} onChange={e => setName(e.target.value)} />
```

### Q61. What is "lifting state up"?
When two sibling components need the same data, move the state to their **closest common parent** and pass it down via props (and pass setter functions for updates).

### Q62. What is prop drilling? How to avoid it?
Passing props through many levels of components that don't need them, just to reach a deep child. Avoid it with **Context API**, Redux, or component composition.

### Q63. What is the `children` prop?
Anything written between a component's opening and closing tags.
```jsx
function Card({ children }) { return <div className="card">{children}</div>; }
<Card><h2>Title</h2></Card>
```

### Q64. What is a Fragment?
It lets you return multiple elements without adding an extra DOM node: `<> ... </>` or `<React.Fragment>`.

### Q65. What is PropTypes? Default props?
`PropTypes` checks the type of props during development. In modern code, default values are given using default parameters: `function Btn({ text = "Submit" })`. (TypeScript replaces PropTypes in many projects.)

### Q66. What are class component lifecycle methods?
Three phases:
- **Mounting:** `constructor` → `render` → `componentDidMount` (API calls here)
- **Updating:** `render` → `componentDidUpdate`
- **Unmounting:** `componentWillUnmount` (cleanup here)

**Hooks equivalent:** `useEffect(() => {}, [])` = didMount; `useEffect(() => {}, [dep])` = didUpdate; the **cleanup return function** = willUnmount.

### Q67. Functional vs Class components?
| Functional | Class |
|---|---|
| Simple function, less code | Uses ES6 class, `render()` |
| Hooks for state/lifecycle | `this.state`, lifecycle methods |
| No `this` confusion | `this` binding needed |
| Modern, recommended | Older, still supported |

### Q68. What is the difference between an element and a component?
An **element** is a plain object describing what to show on screen (`<div />`). A **component** is a function/class that **returns** elements.

### Q69. What is `React.StrictMode`?
A development-only wrapper that highlights potential problems. In React 18 dev mode it **runs effects and renders twice** to detect bugs. It does not affect production.

### Q70. What is a SPA (Single Page Application)?
An app that loads **one HTML page** and updates content dynamically without full page reloads. Navigation is handled in the browser (React Router). It feels fast like a mobile app.

---

# Part 5 — React Hooks

### Q71. What are hooks? Why were they introduced?
Hooks are special functions that let **functional components use state and lifecycle features** (without classes). They make code shorter, reusable (custom hooks), and avoid `this` confusion.

### Q72. What are the rules of hooks?
1. Call hooks **only at the top level** (not inside loops, conditions, or nested functions).
2. Call hooks **only inside React function components or custom hooks.**
3. Custom hook names must start with `use`.

*Why?* React identifies hooks by their **call order** on every render.

### Q73. Explain `useState`.
Adds state to a functional component. Returns `[value, setter]`.
```jsx
function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Clicked {count}</button>;
}
```
Lazy initial state: `useState(() => expensiveCalc())` runs only once.

### Q74. Explain `useEffect`. (very important)
It runs **side effects** after render: API calls, subscriptions, timers, DOM updates, event listeners.
```jsx
useEffect(() => {
  // effect code
  return () => { /* cleanup */ };
}, [dependencies]);
```
**Dependency array behavior:**
| Code | When it runs |
|---|---|
| `useEffect(fn)` | After **every** render |
| `useEffect(fn, [])` | **Only once** after first render (mount) |
| `useEffect(fn, [a, b])` | On mount and whenever **a or b changes** |

**Cleanup function** runs before the next effect and on unmount. Use it to clear timers, remove listeners, cancel subscriptions.

### Q75. Example: fetching data using `useEffect`.
```jsx
function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const getUsers = async () => {
      try {
        const res = await fetch("https://jsonplaceholder.typicode.com/users");
        if (!res.ok) throw new Error("Failed to fetch");
        setUsers(await res.json());
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    getUsers();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```
**Note:** the `useEffect` callback itself cannot be `async`; create an async function inside it and call it.

### Q76. What causes an infinite loop in `useEffect`? How to fix?
Updating state inside an effect **without a dependency array** (or with a dependency that changes every render, like a new object/array) triggers re-render → effect → re-render again.
**Fix:** add the correct dependency array, or use primitive values/`useMemo`/`useCallback` for dependencies.

### Q77. Explain `useRef`.
Returns a mutable object `{ current: value }` that **persists across renders and does NOT cause re-render when changed.** Uses: (1) access DOM elements, (2) store previous values/timer IDs.
```jsx
const inputRef = useRef(null);
<input ref={inputRef} />
<button onClick={() => inputRef.current.focus()}>Focus</button>
```

### Q78. State vs ref — what's the difference?
Changing **state** re-renders the component. Changing **ref.current** does **not**.

### Q79. Explain `useContext`.
Reads data from a Context **without prop drilling.**
```jsx
const ThemeContext = createContext("light");

// Provide
<ThemeContext.Provider value="dark"><App /></ThemeContext.Provider>

// Consume (any depth)
const theme = useContext(ThemeContext);
```

### Q80. Explain `useReducer`. When to use it instead of `useState`?
For **complex state logic** (multiple related values or many update types). It uses a reducer function `(state, action) => newState`.
```jsx
const reducer = (state, action) => {
  switch (action.type) {
    case "increment": return { count: state.count + 1 };
    case "decrement": return { count: state.count - 1 };
    default: return state;
  }
};
const [state, dispatch] = useReducer(reducer, { count: 0 });
<button onClick={() => dispatch({ type: "increment" })}>+</button>
```

### Q81. Explain `useMemo`.
**Memoizes (caches) a calculated value** and recalculates only when dependencies change. Use for **expensive calculations.**
```jsx
const filtered = useMemo(() => items.filter(i => i.price > 100), [items]);
```

### Q82. Explain `useCallback`.
**Memoizes a function** so the same function reference is kept between renders unless dependencies change. Useful when passing functions to memoized children (`React.memo`).
```jsx
const handleClick = useCallback(() => setCount(c => c + 1), []);
```

### Q83. `useMemo` vs `useCallback`?
`useMemo` caches the **result (value)** of a function. `useCallback` caches the **function itself.**
`useCallback(fn, deps)` is the same as `useMemo(() => fn, deps)`.
Don't overuse them. They have a cost too. Use only when there's a real performance problem.

### Q84. What is `useLayoutEffect`? How is it different from `useEffect`?
`useEffect` runs **after the browser paints** (async). `useLayoutEffect` runs **after DOM changes but before paint** (sync), used for measuring layout (size/position) to avoid flicker. Rarely needed.

### Q85. What is a custom hook? Example?
A function starting with `use` that **reuses stateful logic** between components.
```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then(res => res.json())
      .then(json => { if (!ignore) setData(json); })
      .catch(err => { if (!ignore) setError(err); })
      .finally(() => { if (!ignore) setLoading(false); });
    return () => { ignore = true; };
  }, [url]);

  return { data, loading, error };
}
// Usage
const { data, loading, error } = useFetch("/api/products");
```

### Q86. What is `useId`, `useTransition`, `useDeferredValue`? (React 18, just awareness)
- `useId` → generates unique IDs (for form labels, accessibility).
- `useTransition` → marks a state update as **low priority** so the UI stays responsive.
- `useDeferredValue` → delays updating a value (like a search results list) until urgent updates are done.

### Q87. Can we use hooks inside class components?
No. Hooks work only in function components.

### Q88. How do you run code only on the first render?
`useEffect(() => { ... }, [])`

### Q89. How do you run code when a specific value changes?
Put that value in the dependency array: `useEffect(() => { ... }, [value])`.

### Q90. How do you get the previous value of a state?
Store it in a `useRef` inside an effect.
```jsx
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => { ref.current = value; });
  return ref.current;
}
```

---

# Part 6 — Performance Optimization

### Q91. Why does a component re-render?
1. Its **state** changes.
2. Its **props** change.
3. Its **parent re-renders** (even if its props did not change).
4. A **context** value it uses changes.

### Q92. How do you optimize a React application?
- `React.memo` to avoid re-rendering unchanged components
- `useMemo` / `useCallback` for expensive values/functions
- **Code splitting** with `React.lazy` + `Suspense`
- **Pagination / virtualization** (`react-window`) for long lists
- Proper **keys** in lists
- **Debounce** search inputs
- Optimize images (lazy loading, WebP)
- Avoid unnecessary state; keep state close to where it's used
- Use production build; analyze bundle size

### Q93. What is `React.memo`?
A higher-order component that **skips re-rendering** a functional component if its **props haven't changed** (shallow comparison).
```jsx
const Child = React.memo(function Child({ name }) {
  console.log("rendered");
  return <p>{name}</p>;
});
```
It works well with `useCallback` for function props.

### Q94. What is code splitting and lazy loading?
Splitting the bundle into smaller chunks that load **only when needed**, to make the initial load faster.
```jsx
const Dashboard = React.lazy(() => import("./Dashboard"));

<Suspense fallback={<p>Loading...</p>}>
  <Dashboard />
</Suspense>
```
Most commonly done **per route.**

### Q95. What is list virtualization (windowing)?
Rendering **only the visible items** in a long list instead of thousands of DOM nodes (libraries: `react-window`, `react-virtualized`).

### Q96. Why is using an inline function/object in props sometimes bad?
A new function/object is created **every render**, so a `React.memo` child sees a "new" prop and re-renders. Fix with `useCallback` / `useMemo`. (It's not a problem for normal simple components.)

### Q97. How do you measure performance?
React **DevTools Profiler**, Chrome DevTools **Performance/Lighthouse** tab, and `console.log` render checks.

---

# Part 7 — Routing, Forms and API Calls

### Q98. What is React Router? Key components (v6)?
A library for **client-side routing** in a SPA (no page reload).
- `BrowserRouter` – wraps the app
- `Routes` and `Route` – define paths
- `Link` / `NavLink` – navigation (no reload; `NavLink` adds an active class)
- `useNavigate` – navigate by code
- `useParams` – read URL params
- `Outlet` – render nested child routes
- `Navigate` – redirect
```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/products/:id" element={<ProductDetail />} />
    <Route path="*" element={<NotFound />} />
  </Routes>
</BrowserRouter>
```

### Q99. How do you get URL params and navigate programmatically?
```jsx
const { id } = useParams();          // /products/5 → id = "5"
const navigate = useNavigate();
navigate("/dashboard");              // go to a page
navigate(-1);                        // go back
const [searchParams] = useSearchParams(); // ?name=abc
```

### Q100. What is a protected (private) route?
A route that only logged-in users can open; otherwise redirect to login.
```jsx
function ProtectedRoute({ children }) {
  const isLoggedIn = localStorage.getItem("token");
  return isLoggedIn ? children : <Navigate to="/login" replace />;
}
<Route path="/dashboard" element={<ProtectedRoute><Dashboard /></ProtectedRoute>} />
```

### Q101. `Link` vs `<a>` tag?
`<a href>` reloads the whole page. `Link` changes the URL and renders the new component **without reload.**

### Q102. How do you call an API in React?
Use `fetch` or `axios` inside `useEffect` (on load) or inside an event handler (on click). Always handle **loading, success and error** states. (See Q75.)

### Q103. `fetch` vs `axios`?
| fetch | axios |
|---|---|
| Built-in browser API | External library |
| Need `res.json()` manually | Auto JSON parsing |
| Doesn't throw on HTTP 404/500 (check `res.ok`) | Throws error for non-2xx |
| No interceptors | Has interceptors, easy headers, timeout |

### Q104. How do you send a POST request?
```jsx
await axios.post("/api/users", { name, email });
// or
await fetch("/api/users", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name, email }),
});
```

### Q105. How do you handle forms in React?
Use controlled inputs with state, handle `onSubmit`, call `e.preventDefault()`, validate, then submit.
```jsx
function LoginForm() {
  const [form, setForm] = useState({ email: "", password: "" });
  const [errors, setErrors] = useState({});

  const handleChange = (e) =>
    setForm({ ...form, [e.target.name]: e.target.value });

  const handleSubmit = (e) => {
    e.preventDefault();
    const newErrors = {};
    if (!form.email.includes("@")) newErrors.email = "Invalid email";
    if (form.password.length < 6) newErrors.password = "Min 6 characters";
    setErrors(newErrors);
    if (Object.keys(newErrors).length === 0) console.log("Submit", form);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" value={form.email} onChange={handleChange} />
      {errors.email && <span>{errors.email}</span>}
      <input name="password" type="password" value={form.password} onChange={handleChange} />
      {errors.password && <span>{errors.password}</span>}
      <button type="submit">Login</button>
    </form>
  );
}
```
Libraries to mention: **React Hook Form, Formik, Yup** (validation).

### Q106. How do you store the login token? What are the security concerns?
Common: `localStorage` (simple, but vulnerable to XSS) or **httpOnly cookies** (safer, set by the server). Never store passwords. Send the token in the `Authorization: Bearer <token>` header.

### Q107. What is CORS?
Cross-Origin Resource Sharing: a browser security rule that blocks requests from one origin (domain/port) to another unless the **server allows it** through headers. It's fixed on the **backend** (or with a dev proxy).

### Q108. What are the common HTTP methods and status codes?
- Methods: **GET** (read), **POST** (create), **PUT/PATCH** (update), **DELETE** (remove).
- Codes: **200** OK, **201** Created, **400** Bad Request, **401** Unauthorized, **403** Forbidden, **404** Not Found, **500** Server Error.

### Q109. What are environment variables in React?
Used to keep config like API URLs outside code. In Vite: `VITE_API_URL` in `.env`, read with `import.meta.env.VITE_API_URL`. In CRA: `REACT_APP_API_URL` → `process.env.REACT_APP_API_URL`. **Never put secrets** in front-end env variables (they are visible in the browser).

---

# Part 8 — State Management

### Q110. What is the Context API? When to use it?
A built-in way to **share data globally** (theme, logged-in user, language) without prop drilling.
```jsx
const AuthContext = createContext();

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  return (
    <AuthContext.Provider value={{ user, setUser }}>
      {children}
    </AuthContext.Provider>
  );
}
export const useAuth = () => useContext(AuthContext);
```
**Limitation:** every consumer re-renders when the context value changes, so it's not ideal for fast-changing large state.

### Q111. What is Redux? Why use it?
Redux is a **predictable global state container.** Use it when many components share complex state across a large app.

**Three principles:** single source of truth (one **store**), state is **read-only** (change only via **actions**), changes are made by pure **reducers.**

### Q112. Explain the Redux flow.
**UI → dispatch(action) → reducer → new state in store → UI re-renders.**
- **Store:** holds the whole state
- **Action:** plain object `{ type, payload }` describing what happened
- **Reducer:** pure function `(state, action) => newState`
- **Dispatch:** sends the action to the store
- **Selector (`useSelector`):** reads data from the store

### Q113. What is Redux Toolkit (RTK)?
The **official, recommended** way to write Redux. Less boilerplate: `configureStore`, `createSlice` (reducer + actions together), `createAsyncThunk` (API calls). It uses **Immer**, so you can "mutate" state inside reducers safely.
```jsx
const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1; },
    decrement: (state) => { state.value -= 1; },
  },
});
export const { increment, decrement } = counterSlice.actions;
export default counterSlice.reducer;

// store.js
const store = configureStore({ reducer: { counter: counterSlice.reducer } });

// Component
const count = useSelector(state => state.counter.value);
const dispatch = useDispatch();
<button onClick={() => dispatch(increment())}>+</button>
```
Wrap the app: `<Provider store={store}><App /></Provider>`.

### Q114. Context API vs Redux?
| Context | Redux |
|---|---|
| Built into React | External library |
| Good for small/medium, low-frequency data (theme, auth) | Good for large apps, complex, frequently-updated state |
| No middleware/devtools by default | Middleware, DevTools, time-travel debugging |

### Q115. What is middleware / thunk in Redux?
Middleware sits between **dispatch and reducer.** **Redux Thunk** lets action creators handle **async logic** (API calls). RTK includes it with `createAsyncThunk`.

### Q116. When do you use local state vs global state?
Use **local state** (`useState`) if only one component (or its children) needs it. Use **global state** (Context/Redux) when many distant components need it. Don't put everything in Redux.

### Q117. Other tools you may have heard of?
**Zustand, Recoil, Jotai** (simple state libraries); **React Query / TanStack Query** (server-state: caching, refetching API data).

---

# Part 9 — Advanced and Misc Topics

### Q118. What is a Higher-Order Component (HOC)?
A function that **takes a component and returns a new enhanced component.** Used to reuse logic (e.g., `withAuth`). Today, custom hooks are preferred.
```jsx
const withLogger = (Component) => (props) => {
  console.log("Rendering");
  return <Component {...props} />;
};
```

### Q119. What is the render props pattern?
Passing a **function as a prop** that tells the component what to render. Mostly replaced by hooks.

### Q120. What are Error Boundaries?
A **class component** that catches JavaScript errors in its child tree during rendering and shows a **fallback UI** instead of crashing the whole app. It uses `componentDidCatch` / `getDerivedStateFromError`. (It does **not** catch errors in event handlers or async code.)

### Q121. What are Portals?
`ReactDOM.createPortal(child, domNode)` renders a child **outside the parent DOM hierarchy.** Used for modals, tooltips, and dropdowns.

### Q122. What is `forwardRef`?
Lets a parent pass a `ref` **through** a component to a DOM element inside the child.

### Q123. Difference between `useEffect` and event handlers for API calls?
Use `useEffect` when data should load **because the component appeared / a value changed.** Use an **event handler** when it should happen **because the user did something** (click, submit).

### Q124. What is `create-react-app` vs Vite?
CRA is the older tool (webpack, slow, now deprecated). **Vite** is the modern tool: very fast dev server, fast build, uses ES modules. Create a project: `npm create vite@latest my-app -- --template react`.

### Q125. What is Webpack/Babel?
**Babel** converts modern JS/JSX to code older browsers understand. **Webpack** bundles all files (JS, CSS, images) into optimized bundles.

### Q126. What is `package.json`? What is `node_modules`? `package-lock.json`?
`package.json` lists the project's dependencies and scripts. `node_modules` holds installed packages (never commit it to Git). `package-lock.json` locks exact versions for consistent installs.

### Q127. `dependencies` vs `devDependencies`?
`dependencies` are needed to **run** the app (react, axios). `devDependencies` are only for **development** (eslint, vite, testing tools).

### Q128. What is SSR vs CSR? What is Next.js?
- **CSR (Client-Side Rendering):** browser downloads JS and builds the page (React default). Slower first load, weaker SEO.
- **SSR (Server-Side Rendering):** server sends ready HTML. Faster first paint, better SEO.
- **Next.js** is a React framework offering SSR, SSG (static generation), file-based routing, and API routes.

### Q129. What is a Single Responsibility Principle in React components?
Each component should do **one thing.** Split big components into smaller reusable ones (e.g., `ProductList` → `ProductCard`, `Filter`, `Pagination`).

### Q130. What is a folder structure you follow?
```
src/
 ├─ components/   (reusable UI: Button, Navbar, Card)
 ├─ pages/        (route-level screens: Home, Login)
 ├─ hooks/        (custom hooks)
 ├─ context/ or store/
 ├─ services/     (API calls)
 ├─ utils/        (helper functions)
 ├─ assets/       (images, styles)
 └─ App.jsx, main.jsx
```

### Q131. How do you debug a React application?
React **DevTools** (inspect props/state), browser **console and Network tab** (API responses), `console.log`, breakpoints in the **Sources** tab, and reading the error message/stack trace carefully.

### Q132. How do you test React components?
**Jest** (test runner) + **React Testing Library** (render component, simulate user actions, check output). Test what the user sees, not internal implementation. If you haven't used it, say: *"I have basic knowledge; I know the idea of rendering and asserting with RTL."*

### Q133. What is accessibility (a11y) in React?
Making the app usable by everyone: semantic HTML, `alt` on images, `label` for inputs, keyboard navigation, ARIA attributes (`aria-label`), enough colour contrast.

### Q134. What are the new features in React 18?
- **Automatic batching** (multiple state updates grouped, even inside promises/timeouts)
- **Concurrent rendering** features: `useTransition`, `useDeferredValue`
- `createRoot` replaces `ReactDOM.render`
- Improved **Suspense** and streaming SSR

### Q135. What is the difference between `ReactDOM.render` and `createRoot`?
`createRoot` (React 18+) enables the new concurrent features:
```jsx
ReactDOM.createRoot(document.getElementById("root")).render(<App />);
```

### Q136. What is Git? Commands you use daily?
Version control system. `git clone`, `git status`, `git add .`, `git commit -m "msg"`, `git pull`, `git push`, `git branch`, `git checkout -b feature`, `git merge`. **Pull request** = ask the team to review and merge your branch.

### Q137. How do you handle merge conflicts?
Open the conflicted file, choose/merge the correct code between the `<<<<<<<` and `>>>>>>>` markers, remove the markers, `git add`, then `git commit`.

---

# Part 10 — Coding Tasks

> Interviewers love small live-coding tasks. **Practice typing these without looking.**

### Task 1. Counter with increment, decrement, reset
```jsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <h2>{count}</h2>
      <button onClick={() => setCount(c => c + 1)}>+</button>
      <button onClick={() => setCount(c => c - 1)}>-</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

### Task 2. Toggle show/hide
```jsx
export default function Toggle() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(!show)}>{show ? "Hide" : "Show"}</button>
      {show && <p>Hello, I am visible!</p>}
    </>
  );
}
```

### Task 3. Todo list (add, delete, toggle complete)
```jsx
export default function Todo() {
  const [text, setText] = useState("");
  const [todos, setTodos] = useState([]);

  const addTodo = () => {
    if (!text.trim()) return;
    setTodos([...todos, { id: Date.now(), text, done: false }]);
    setText("");
  };
  const deleteTodo = (id) => setTodos(todos.filter(t => t.id !== id));
  const toggleTodo = (id) =>
    setTodos(todos.map(t => (t.id === id ? { ...t, done: !t.done } : t)));

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={addTodo}>Add</button>
      <ul>
        {todos.map(t => (
          <li key={t.id}>
            <span
              onClick={() => toggleTodo(t.id)}
              style={{ textDecoration: t.done ? "line-through" : "none", cursor: "pointer" }}
            >
              {t.text}
            </span>
            <button onClick={() => deleteTodo(t.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Task 4. Search / filter a list
```jsx
export default function Search() {
  const [query, setQuery] = useState("");
  const names = ["Anil", "Priya", "Ravi", "Sneha", "Arjun"];
  const filtered = names.filter(n => n.toLowerCase().includes(query.toLowerCase()));

  return (
    <>
      <input placeholder="Search..." value={query} onChange={e => setQuery(e.target.value)} />
      <ul>{filtered.map(n => <li key={n}>{n}</li>)}</ul>
      {filtered.length === 0 && <p>No results</p>}
    </>
  );
}
```

### Task 5. Fetch and display API data (with loading and error)
Use the code in **Q75**.

### Task 6. Debounced search input
```jsx
function useDebounce(value, delay = 500) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  return debounced;
}
// In component:
const debouncedQuery = useDebounce(query, 500);
useEffect(() => { /* call API with debouncedQuery */ }, [debouncedQuery]);
```

### Task 7. Timer / Stopwatch (shows cleanup)
```jsx
export default function Timer() {
  const [seconds, setSeconds] = useState(0);
  useEffect(() => {
    const id = setInterval(() => setSeconds(s => s + 1), 1000);
    return () => clearInterval(id);   // cleanup!
  }, []);
  return <h2>{seconds}s</h2>;
}
```

### Task 8. Reusable Button component with props
```jsx
function Button({ text, onClick, disabled = false, variant = "primary" }) {
  return (
    <button className={`btn btn-${variant}`} onClick={onClick} disabled={disabled}>
      {text}
    </button>
  );
}
```

### Task 9. Simple Modal using conditional rendering
```jsx
function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;
  return (
    <div className="overlay">
      <div className="modal">
        <button onClick={onClose}>X</button>
        {children}
      </div>
    </div>
  );
}
```

### Task 10. Accordion / Tabs (only one open at a time)
```jsx
function Accordion({ items }) {
  const [openIndex, setOpenIndex] = useState(null);
  return items.map((item, i) => (
    <div key={i}>
      <h4 onClick={() => setOpenIndex(openIndex === i ? null : i)}>{item.title}</h4>
      {openIndex === i && <p>{item.content}</p>}
    </div>
  ));
}
```

### Task 11. Pagination (client-side)
```jsx
const [page, setPage] = useState(1);
const perPage = 5;
const start = (page - 1) * perPage;
const currentItems = items.slice(start, start + perPage);
const totalPages = Math.ceil(items.length / perPage);
// buttons: setPage(p => p - 1) disabled when page===1, setPage(p => p + 1) disabled when page===totalPages
```

### Plain JavaScript coding questions

**Reverse a string**
```js
const reverse = (str) => str.split("").reverse().join("");
```
**Palindrome check**
```js
const isPalindrome = (s) => s === s.split("").reverse().join("");
```
**Remove duplicates from an array**
```js
const unique = [...new Set([1, 2, 2, 3, 3])]; // [1,2,3]
```
**Find the largest number**
```js
Math.max(...[3, 9, 2]); // 9
```
**Count character occurrences**
```js
const count = {};
for (const ch of "hello") count[ch] = (count[ch] || 0) + 1; // {h:1,e:1,l:2,o:1}
```
**Flatten a nested array**
```js
[1, [2, [3, 4]]].flat(Infinity); // [1,2,3,4]
```
**Sum of array**
```js
[1, 2, 3].reduce((a, b) => a + b, 0); // 6
```
**FizzBuzz**
```js
for (let i = 1; i <= 15; i++) {
  console.log(i % 15 === 0 ? "FizzBuzz" : i % 3 === 0 ? "Fizz" : i % 5 === 0 ? "Buzz" : i);
}
```
**Factorial**
```js
const fact = (n) => (n <= 1 ? 1 : n * fact(n - 1));
```
**Sort array of objects by a key**
```js
users.sort((a, b) => a.age - b.age);
```
**Group by**
```js
const grouped = items.reduce((acc, item) => {
  (acc[item.category] ||= []).push(item);
  return acc;
}, {});
```
**Capitalize first letter of each word**
```js
const cap = (s) => s.split(" ").map(w => w[0].toUpperCase() + w.slice(1)).join(" ");
```

### Output-based questions (very common)
```js
console.log(typeof null);        // "object" (a famous JS bug)
console.log(typeof undefined);   // "undefined"
console.log([] + []);            // "" (empty string)
console.log(0.1 + 0.2 === 0.3);  // false (floating point)
console.log("5" + 3);            // "53"
console.log("5" - 3);            // 2
console.log(NaN === NaN);        // false
console.log([1,2,3] == "1,2,3"); // true
```

---

# Part 11 — HR / Behavioural Questions

### Q138. Why do you want to leave your current company / why are you looking for a change?
"I have learned a lot in my current role, but I want to work on bigger challenges and learn more advanced React concepts and best practices. This role offers that growth." *(Never speak badly about your current company.)*

### Q139. Why should we hire you?
"I have hands-on experience with React, hooks, API integration and responsive UI, and I am a quick learner who takes feedback well. I write clean, reusable components and I'm ready to contribute from day one while continuing to grow with the team."

### Q140. What are your strengths and weaknesses?
- **Strengths:** quick learner, good problem-solving, team player, attention to UI details.
- **Weakness (safe and real):** "Sometimes I spend too much time perfecting the UI. I'm learning to prioritise and deliver on time." Or: "I'm still improving in testing, and I'm learning React Testing Library."

### Q141. Where do you see yourself in 3–5 years?
"I want to grow into a strong senior front-end developer, with deep knowledge of React and system design, and possibly mentor juniors."

### Q142. What is your expected salary / notice period?
Know your current CTC and notice period. Say: "I'm looking for a hike in line with my skills and market standards, and I'm flexible for the right opportunity." Check the company's range if they ask first.

### Q143. How do you handle a tight deadline or a bug you can't solve?
"I first break the problem into small parts, check the console and network tab, search documentation/Stack Overflow, and if I'm still stuck after reasonable time, I ask a senior teammate with the details of what I've already tried."

### Q144. How do you keep yourself updated?
"I read React docs and follow blogs/YouTube channels, build small practice projects, and read other developers' code on GitHub."

### Q145. Do you have any questions for us? (ALWAYS ask 1–2)
- "What does the tech stack and the team structure look like?"
- "What would be my expectations in the first 3 months?"
- "How does the team do code reviews and learning?"

---

# Part 12 — Last-Minute Cheat Sheet

### One-line answers (memorize these)
| Topic | One-line answer |
|---|---|
| React | JS library for building UIs using reusable components |
| JSX | HTML-like syntax in JS, converted by Babel |
| Virtual DOM | Lightweight JS copy of the DOM; React diffs it and updates only changes |
| Props | Read-only data passed parent → child |
| State | Changeable data owned by a component; change = re-render |
| Key | Unique ID for list items so React can track them |
| `useState` | Adds state to a function component |
| `useEffect` | Runs side effects (API, timers); dependency array controls when |
| `useRef` | Persistent value/DOM access without re-render |
| `useMemo` | Caches a computed **value** |
| `useCallback` | Caches a **function** |
| `useContext` | Reads context to avoid prop drilling |
| `useReducer` | Complex state logic with reducer + dispatch |
| `React.memo` | Skips re-render if props unchanged |
| Controlled component | Input value controlled by React state |
| Lifting state up | Move shared state to the common parent |
| Prop drilling | Passing props through many levels; fix with Context/Redux |
| Context vs Redux | Context for simple global data; Redux for complex large-scale state |
| Lazy / Suspense | Load components only when needed |
| React Router | Client-side routing in SPA |
| HOC | Function that takes a component and returns an enhanced one |
| Error boundary | Catches render errors in children and shows fallback UI |
| Portal | Render children outside the parent DOM (modals) |

### Top 15 questions that are asked in almost every interview
1. Tell me about yourself and your project
2. What is React? Virtual DOM?
3. Props vs State
4. Why `key` in lists?
5. `useState` and `useEffect` (with dependency array and cleanup)
6. How do you call an API in React?
7. Controlled vs uncontrolled components
8. `useMemo` vs `useCallback` vs `React.memo`
9. Context API vs Redux
10. Prop drilling and how to avoid it
11. `var/let/const`, hoisting, closures
12. `==` vs `===`, `map/filter/reduce`
13. Promises, async/await, event loop
14. Flexbox/Grid and responsive design
15. Small coding task (counter / todo / fetch + display)

### Interview day tips
- **Think aloud** when solving coding tasks. Interviewers value your thinking process.
- **Listen carefully**, take a moment before answering, and don't rush.
- Give **short, structured answers**, then offer: *"Would you like me to explain with an example?"*
- If you don't know: *"I haven't used this in practice, but I understand it as ... and I'm happy to learn it."*
- Be honest about your project. Don't claim skills you cannot explain.
- Check your **laptop, internet, mic, and charger** before the interview. Keep VS Code and a React project ready for live coding.
- Sleep well. **You are prepared. Stay calm and confident.** 💪

---
**Best of luck with your interview! 🚀**
