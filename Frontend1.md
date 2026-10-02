# Master Frontend Interview Guide: React.js, JavaScript, HTML, CSS

**For:** Goram Siva Prasad | **Role:** Full Stack Engineer (6 months to 1 year experience) | **Round:** Technical (theory + live coding)

> Read this document in order. Every question has a **simple answer** and, where useful, a **"Say it like this"** line you can speak directly in the interview. Practise the code sections by typing them yourself, not by reading.

---

## Table of Contents

1. [How to answer in the interview](#1-how-to-answer-in-the-interview)
2. [Introduction and resume based questions](#2-introduction-and-resume-based-questions)
3. [JavaScript theory questions](#3-javascript-theory-questions)
4. [JavaScript output based questions](#4-javascript-output-based-questions)
5. [React theory questions](#5-react-theory-questions)
6. [Hooks in depth](#6-hooks-in-depth)
7. [React advanced topics: performance, state management, routing, Next.js](#7-react-advanced-topics)
8. [HTML questions](#8-html-questions)
9. [CSS questions](#9-css-questions)
10. [Scenario based questions](#10-scenario-based-questions)
11. [Live coding: React components](#11-live-coding-react-components)
12. [Live coding: custom hooks](#12-live-coding-custom-hooks)
13. [Live coding: JavaScript functions and polyfills](#13-live-coding-javascript-functions-and-polyfills)
14. [Pseudo code guide](#14-pseudo-code-guide)
15. [Interview day strategy](#15-interview-day-strategy)
16. [Last day revision checklist](#16-last-day-revision-checklist)

---

## 1. How to answer in the interview

Use this simple pattern for theory questions. It makes your answer sound clear and confident.

1. **Definition** in one line (what is it).
2. **Why we use it** (the problem it solves).
3. **Small example** (one line of code or a real use from your project).
4. **Pitfall or comparison** (shows depth).

**Example, "What is useEffect?"**
"useEffect lets a component run side effects, like API calls or subscriptions, after it renders. We use it because render must stay pure. For example, I fetch the doctor list in useEffect with an empty dependency array so it runs once. One thing to remember is to return a cleanup function for timers and sockets, otherwise we get memory leaks."

**Rules for live coding rounds**
- Repeat the problem in your own words and ask 1 or 2 clarifying questions (inputs, edge cases).
- Say your approach before you type: "I will keep the list in state and filter it on every change."
- Write the simplest working version first. Improve after it works.
- Handle loading, error and empty states. Interviewers notice this.
- Think aloud. If you get stuck, say what you are trying, and write pseudo code.
- If you do not know something, say: "I have not used that in depth, but my understanding is... and I would check the docs for..." Never make something up.

**Be honest about your resume.** Anything written on your resume can be asked in depth. Explain only what you actually did.

---

## 2. Introduction and resume based questions

### Q1. Tell me about yourself.

**Say it like this (about 60 seconds, change to your own words):**
"I am Siva, a full stack engineer from Hyderabad with a little over a year of experience. I have worked on a healthcare platform with the MERN stack, an AI document extraction system with FastAPI and a React interface, and an Agri-Tech ERP with React, Node.js and PostgreSQL. My strongest area is frontend with React, and I also handle backend APIs and basic deployment on AWS with Docker and Terraform. I am currently building a secure team chat application as a personal project. I am looking for a role where I can build full stack features end to end and keep growing."

### Q2. Why do you want to join us?
Be specific and positive: you want to work on production full stack products, you like that the stack matches your skills (React, Node, FastAPI, PostgreSQL, AWS), and you want to grow with senior developers.

### Q3. Explain your healthcare project (eAsha). Frontend side.
Prepare this answer using the structure **Problem, What I built, Tools, Result**.
- **Problem:** patients, doctors and admins needed one platform for appointments, payments and consultations.
- **What I built (frontend):** three role-based interfaces (User, Doctor, Admin), appointment booking by department, Razorpay payment flow, real-time appointment status through WebSockets, video consultation with Jitsi Meet, doctor onboarding with admin approval.
- **Tools:** React.js, Node.js, MongoDB, Socket.io.
- **Result:** API response improved around 40% through query optimization and indexing, deployed on AWS EC2.

**Likely follow-up questions (prepare each one):**
- How did you handle different screens for User, Doctor and Admin? (Role stored after login, protected routes, conditional rendering by role, separate route groups.)
- How did you manage state? (Local state for forms, Context or Redux for user and auth data. Say what you really used.)
- How did the Razorpay flow work on the frontend? (Backend creates an order, frontend opens the Razorpay checkout with the order id, on success the frontend sends the payment details to the backend, and the backend verifies the signature. The frontend never trusts the payment status by itself.)
- How did real-time updates work? (Socket.io client connects in useEffect, listens for events, updates state, and disconnects in the cleanup function.)
- How did you protect routes? (A ProtectedRoute component checks the token and role, and redirects to login if not allowed.)

### Q4. Explain the AI document extraction project. Where did React come in?
- **Problem:** manual data entry from invoices and PDFs was slow.
- **Solution:** FastAPI backend with OCR and LLMs converting documents to structured JSON, stored in PostgreSQL, with a React interface.
- **Frontend answer:** "The React interface let users upload documents, see the extraction status, and review the extracted data in a form."
- Be ready to explain: file upload with `FormData`, showing progress and loading, handling failed extraction, and editing extracted fields before saving.

### Q5. Explain the Agri-Tech ERP project.
- Farms, blocks, rows and beds are a hierarchy, shown on a map using GIS layers (Esri, OpenStreetMap).
- Be ready for: how you displayed hierarchical data (tree or nested lists), how you handled large data (pagination, lazy loading, memoization), and how you kept the map and the data in sync.

### Q6. Explain your chat application project.
- Real-time messaging with Socket.io, channels, roles, AES-256 encryption, invite links.
- Frontend questions: how the message list updates in real time, how to auto-scroll to the latest message, how to avoid duplicate listeners, how to store the encryption keys safely in the browser.

### Q7. What was the hardest frontend problem you solved?
Pick one real example. Good topics: a performance problem (slow list, too many re-renders), a state sharing problem, a payment or real-time bug. Explain what you saw, how you debugged (React DevTools, console, network tab) and what fixed it.

### Q8. Which frontend skills are you strongest in?
"React hooks and component design, API integration, forms and validation, and role-based routing. I am also comfortable with TypeScript, Tailwind CSS and React Query."

---

## 3. JavaScript theory questions

### Q1. Difference between var, let and const?
| | var | let | const |
|---|---|---|---|
| Scope | function | block | block |
| Re-assign | yes | yes | no |
| Re-declare | yes | no | no |
| Hoisting | hoisted, value `undefined` | hoisted, but in temporal dead zone | same as let |

**Say it like this:** "var is function scoped and old. let and const are block scoped. const cannot be re-assigned, but if it holds an object, the object's contents can still change. I use const by default and let only when needed."

### Q2. What is hoisting?
JavaScript moves declarations to the top of their scope before running the code. Function declarations are fully hoisted. `var` is hoisted with value `undefined`. `let` and `const` are hoisted but cannot be used before the line where they are declared.

### Q3. What is the temporal dead zone (TDZ)?
The time between the start of the scope and the line where a `let` or `const` is declared. Accessing the variable in that time throws a `ReferenceError`.

### Q4. What is scope and the scope chain?
Scope decides where a variable can be accessed: global, function and block scope. When JavaScript looks for a variable, it checks the current scope, then the outer scope, and so on up to global. That path is the scope chain.

### Q5. What is a closure? (Very common)
A closure is a function that remembers the variables of the place where it was created, even after that outer function has finished.

```js
function counter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}
const inc = counter();
inc(); // 1
inc(); // 2
```

**Uses:** private data, counters, debounce and throttle, memoization, event handlers, and React hooks (a stale closure is a common React bug).

### Q6. How does the `this` keyword work?
`this` depends on how a function is called.
- In an object method, `this` is the object.
- In a normal function call, `this` is `undefined` in strict mode (or `window` otherwise).
- With `call`, `apply` or `bind`, `this` is what you pass.
- **Arrow functions** do not have their own `this`. They use `this` from the outer scope.

### Q7. call, apply and bind?
All three set `this` for a function.
- `call(thisArg, a, b)` calls the function now, arguments one by one.
- `apply(thisArg, [a, b])` calls now, arguments as an array.
- `bind(thisArg)` returns a new function to call later.

### Q8. Arrow function vs normal function?
Arrow functions have shorter syntax, no own `this`, no `arguments` object, and cannot be used as constructors. Normal functions have their own `this`.

### Q9. `==` vs `===`?
`==` converts types before comparing (`"5" == 5` is true). `===` compares value and type (`"5" === 5` is false). Use `===` always.

### Q10. null vs undefined?
`undefined` means a variable was declared but has no value. `null` is an intentional empty value that you assign. `typeof null` is `"object"` (an old bug in JavaScript).

### Q11. What are truthy and falsy values?
Falsy: `false`, `0`, `""`, `null`, `undefined`, `NaN`. Everything else is truthy, including `[]` and `{}`.

### Q12. Primitive vs reference types?
Primitives (string, number, boolean, null, undefined, symbol, bigint) are copied by value. Objects, arrays and functions are copied by reference, so two variables can point to the same object. This is why we never mutate state directly in React.

### Q13. Shallow copy vs deep copy?
A shallow copy copies only the first level. Nested objects are still shared.
```js
const a = { name: "A", address: { city: "X" } };
const shallow = { ...a };            // address is shared
const deep = structuredClone(a);     // fully independent copy
// JSON.parse(JSON.stringify(a)) also works but loses functions, dates and undefined
```

### Q14. Spread and rest operators?
Both use `...`. **Spread** expands items: `[...arr, 4]`, `{...obj, age: 20}`. **Rest** collects items: `function sum(...nums) {}` or `const [first, ...others] = arr`.

### Q15. What is destructuring?
Extracting values from arrays or objects into variables.
```js
const { name, age = 18 } = user;
const [a, b] = [1, 2];
```

### Q16. map vs filter vs reduce vs forEach?
- `map` returns a new array of the same length with each item transformed.
- `filter` returns a new array with only the items that pass a condition.
- `reduce` combines all items into a single value (sum, object, etc.).
- `forEach` just loops and returns nothing.
```js
[1,2,3].map(n => n * 2);                  // [2,4,6]
[1,2,3,4].filter(n => n % 2 === 0);       // [2,4]
[1,2,3].reduce((sum, n) => sum + n, 0);   // 6
```

### Q17. slice vs splice?
`slice(start, end)` returns a copy of a part and does not change the original. `splice(start, count, ...items)` changes the original array (removes or inserts).

### Q18. What is a prototype and prototypal inheritance?
Every object has a hidden link to another object called its prototype. If a property is not found on the object, JavaScript looks in the prototype, then its prototype, and so on (the prototype chain). Classes in JavaScript are just a cleaner syntax on top of this.

### Q19. What is the event loop? (Very common)
JavaScript has one thread and one call stack. Async work (timers, fetch, events) is handled by the browser. When it is done, the callback goes to a queue. The event loop checks: if the call stack is empty, it moves tasks from the queue to the stack. **Microtasks (promises, `queueMicrotask`) run before macrotasks (`setTimeout`, events).**

**Say it like this:** "JavaScript is single threaded. The call stack runs code. Web APIs handle async work and push callbacks to queues. The event loop pushes them to the stack when it is empty, and promise callbacks run before setTimeout callbacks."

### Q20. Callbacks and callback hell?
A callback is a function passed to another function to run later. Many nested callbacks make code hard to read (callback hell). Promises and async/await solve this.

### Q21. What is a Promise? States?
An object that represents a value that will come in the future. States: **pending**, **fulfilled**, **rejected**. We use `.then()`, `.catch()`, `.finally()`.

### Q22. async/await?
Syntax on top of promises that makes async code look like normal code. `await` pauses inside the async function until the promise settles. Use `try/catch` for errors.
```js
async function getUsers() {
  try {
    const res = await fetch("/api/users");
    if (!res.ok) throw new Error("Failed");
    return await res.json();
  } catch (err) {
    console.error(err.message);
  }
}
```

### Q23. Promise.all vs allSettled vs race vs any?
- `Promise.all`: waits for all. Fails fast if one fails.
- `Promise.allSettled`: waits for all and gives each result (success or failure).
- `Promise.race`: result of the first one that settles.
- `Promise.any`: first one that succeeds.

### Q24. Debounce vs throttle?
- **Debounce:** run the function only after the user stops triggering it for some time. Use: search box, window resize end.
- **Throttle:** run the function at most once in a time interval. Use: scroll events, button spam.

### Q25. Event bubbling, capturing and delegation?
Events go down from the root to the target (capturing) and then bubble up to the root. **Event delegation** means adding one listener on a parent and using `event.target` to handle clicks of many children. This saves memory and works for items added later.

### Q26. preventDefault vs stopPropagation?
`preventDefault()` stops the browser's default action (form submit, link navigation). `stopPropagation()` stops the event from bubbling to parents.

### Q27. localStorage vs sessionStorage vs cookies?
| | localStorage | sessionStorage | cookies |
|---|---|---|---|
| Lifetime | until cleared | until tab closes | set by expiry |
| Size | about 5MB | about 5MB | about 4KB |
| Sent to server | no | no | yes, with every request |
| JS access | yes | yes | yes unless HttpOnly |

**Security point:** tokens in localStorage can be stolen by XSS. An HttpOnly cookie is safer because JavaScript cannot read it.

### Q27b. Where would you store the JWT?
Safest: HttpOnly, Secure, SameSite cookie set by the server. Simple apps often use localStorage, but then you must protect against XSS. Say you understand the trade-off.

### Q28. What are higher-order functions?
Functions that take a function as an argument or return a function. `map`, `filter` and `reduce` are examples.

### Q29. What is currying?
Turning a function with many arguments into a chain of functions with one argument each: `add(1)(2)(3)`.

### Q30. What is a pure function?
Same input always gives same output, and no side effects (no changing outside data, no API calls). React components should be pure during render.

### Q31. Optional chaining and nullish coalescing?
`user?.address?.city` returns `undefined` instead of an error if something is missing. `value ?? "default"` uses the default only for `null` or `undefined` (unlike `||`, which also replaces `0` and `""`).

### Q32. for...in vs for...of?
`for...in` loops over keys (property names) of an object. `for...of` loops over values of iterables (arrays, strings, Sets, Maps).

### Q33. Set, Map, WeakMap?
`Set` stores unique values. `Map` stores key-value pairs where keys can be any type. `WeakMap` keys are objects that can be garbage collected, useful to avoid memory leaks.

### Q34. JSON.stringify and JSON.parse?
`JSON.stringify` converts an object to a string. `JSON.parse` converts the string back. Functions and `undefined` are dropped.

### Q35. What is memoization?
Caching the result of a function for the same inputs, to avoid repeating expensive work. React has `useMemo`, `useCallback` and `React.memo` for this idea.

### Q36. What are generators?
Functions that can pause with `yield` and resume later. Used for lazy sequences. (Just know the idea.)

### Q37. typeof vs instanceof?
`typeof` gives the type as a string (`"number"`, `"string"`, `"object"`). `instanceof` checks whether an object was created by a constructor or class.

### Q38. fetch vs axios?
fetch is built in, returns a promise, but does not reject on HTTP errors like 404 or 500 (you must check `res.ok`) and you must call `res.json()`. Axios is a library that gives automatic JSON, rejects on errors, supports interceptors (useful for attaching tokens) and request cancellation.

### Q39. What is CORS?
Browsers block requests from one origin (domain, protocol, port) to another unless the server allows it with CORS headers. It is a browser security feature. The fix is on the server (`Access-Control-Allow-Origin`), not in the frontend.

### Q40. What are ES modules?
`import` and `export` syntax to split code into files. Named exports (`export const a`) vs default export (`export default`). CommonJS uses `require` and `module.exports` (Node style).

### Q41. What causes memory leaks in frontend code?
Event listeners, timers, intervals and sockets that are never removed; large objects held in closures; subscriptions not cleaned up. In React, always clean up in the `useEffect` return function.

### Q42. How does the browser render a page?
Parse HTML into the DOM, parse CSS into the CSSOM, combine them into the render tree, then layout (sizes and positions), paint, and composite. Changing layout properties (width, margin) causes **reflow** (expensive). Changing only colors causes **repaint**. Transform and opacity are cheapest.

### Q43. What is TypeScript and why use it?
A typed superset of JavaScript. It catches errors at compile time, improves autocomplete and makes large code safer. In React we type props, state and API responses.
```tsx
interface UserProps { name: string; age?: number }
const User = ({ name, age }: UserProps) => <p>{name}</p>;
```
`interface` vs `type`: both describe shapes. Interfaces can be extended and merged; types can express unions (`"a" | "b"`).

### Q44. `setTimeout` vs `setInterval`?
`setTimeout` runs once after a delay. `setInterval` runs repeatedly until cleared with `clearInterval`.

### Q45. What is strict mode?
`"use strict"` makes JavaScript stricter: no undeclared variables, safer `this`, and some silent errors become real errors. ES modules and classes are strict by default.

---

## 4. JavaScript output based questions

Interviewers love these. Try to answer before reading the answer.

**Q1.**
```js
console.log(a);
var a = 5;
console.log(b);
let b = 10;
```
**Answer:** first prints `undefined` (var is hoisted), second throws `ReferenceError` (TDZ).

**Q2. Loop with var and setTimeout**
```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0);
}
```
**Answer:** `3 3 3`. `var` has one shared `i`, and the loop ends before the callbacks run. With `let i`, the output is `0 1 2` because each loop gets a new `i`.

**Q3. Event loop order**
```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
```
**Answer:** `1 4 3 2`. Sync code first, then microtasks (promise), then macrotasks (timeout).

**Q4. `this` in an object**
```js
const user = {
  name: "Siva",
  normal() { return this.name; },
  arrow: () => this.name,
};
user.normal(); // "Siva"
user.arrow();  // undefined (arrow has no own this)
```

**Q5. Type conversion**
```js
console.log(0.1 + 0.2 === 0.3); // false (floating point)
console.log(typeof null);       // "object"
console.log(NaN === NaN);       // false (use Number.isNaN)
console.log([] + []);           // "" (empty string)
console.log("5" + 3);           // "53"
console.log("5" - 3);           // 2
console.log([1,2] == "1,2");    // true
```

**Q6. Reference equality**
```js
console.log({} === {});      // false (different references)
const a = [1]; const b = a;
b.push(2);
console.log(a);              // [1, 2] (same reference)
```

**Q7. Closure counter**
```js
function make() {
  let c = 0;
  return () => ++c;
}
const x = make(), y = make();
x(); x();
console.log(x(), y()); // 3 1  (each closure has its own c)
```

**Q8. async ordering**
```js
async function f() {
  console.log("a");
  await null;
  console.log("b");
}
f();
console.log("c");
```
**Answer:** `a c b`. Code after `await` runs later as a microtask.

**Q9. Array methods**
```js
[1, 2, 3].map(parseInt);   // [1, NaN, NaN]
```
`map` passes (value, index), so `parseInt("2", 1)` gives NaN. Fix: `.map(n => parseInt(n, 10))`.

**Q10. Default parameters and falsy**
```js
function show(n = 10) { return n; }
show(undefined); // 10
show(null);      // null
show(0);         // 0
```

---
## 5. React theory questions

### Q1. What is React? Why use it?
React is a JavaScript library for building user interfaces using reusable components. We use it because it makes UI predictable (UI is a function of state), reusable (components), and fast (virtual DOM updates only what changed).

### Q2. Library or framework?
React is a library focused on the view layer. Routing, state management and data fetching come from other libraries (React Router, Redux Toolkit, React Query). Next.js is a framework built on React.

### Q3. What is the Virtual DOM?
A lightweight JavaScript copy of the real DOM. When state changes, React builds a new virtual DOM, compares it with the old one (diffing), and updates only the changed parts of the real DOM. This is faster than rebuilding the whole page.

### Q4. What is reconciliation and how does diffing work?
Reconciliation is the process of comparing the old and new virtual DOM trees. React assumes: elements of different types produce different trees (it rebuilds them), and **keys** identify which list items stayed the same.

### Q5. Why do we need `key` in lists? Why not use index?
Keys let React identify which item changed, was added or was removed, so it updates only that item. Using the array index as key causes bugs when the list is reordered, filtered or has items inserted (wrong input values, wrong state, extra re-renders). Use a stable unique id.

### Q6. What is JSX?
A syntax that looks like HTML inside JavaScript. It is compiled to `React.createElement` calls. Rules: one parent element (or fragment), `className` instead of `class`, `htmlFor` instead of `for`, camelCase attributes, close all tags, and JavaScript goes inside `{}`.

### Q7. Functional vs class components?
Functional components are plain functions that use hooks. Class components use `this`, `state` and lifecycle methods. Modern React uses functional components. Error boundaries are the one thing that still needs a class.

### Q8. Props vs state?
- **Props:** data passed from parent to child. Read only.
- **State:** data owned by the component that can change over time. Changing state re-renders the component.

### Q9. What is props drilling and how do you avoid it?
Passing props through many levels just to reach a deep child. Avoid with Context API, a state library (Redux Toolkit, Zustand), component composition (passing children), or React Query for server data.

### Q10. What is "lifting state up"?
When two components need the same data, move the state to their closest common parent and pass it down as props.

### Q11. Controlled vs uncontrolled components?
Controlled: React state controls the input value (`value` and `onChange`). Uncontrolled: the DOM keeps the value and you read it with a `ref`. Controlled is preferred for validation and dynamic UI. Uncontrolled is fine for simple forms and file inputs.

### Q12. Why can't we change state directly?
React compares references to detect changes. If you mutate the same object, the reference does not change, so React may not re-render. Always create a new copy.
```js
setUser({ ...user, age: 21 });          // object
setItems([...items, newItem]);          // add
setItems(items.filter(i => i.id !== id)); // remove
setItems(items.map(i => i.id === id ? { ...i, done: true } : i)); // update
```

### Q13. Is `setState` synchronous? What is batching?
No. State updates are asynchronous and batched. React groups multiple updates in the same event and renders once. The state variable does not change until the next render. When the new value depends on the old value, use the functional form: `setCount(c => c + 1)`. React 18 batches automatically everywhere (including timeouts and promises).

### Q14. What is the component lifecycle? How do hooks map to it?
Mounting (created), updating (state or props change), unmounting (removed).
- `useEffect(() => {...}, [])` runs after the first render (mount).
- `useEffect(() => {...}, [dep])` runs when `dep` changes (update).
- The function returned from `useEffect` is the cleanup, which runs on unmount and before the effect re-runs.

### Q15. What are the common reasons a component re-renders?
Its state changes, its props change, its parent re-renders, or a context it uses changes. A parent re-render re-renders all children unless they are wrapped in `React.memo`.

### Q16. What are fragments?
`<>...</>` lets you return multiple elements without adding an extra DOM node.

### Q17. What are synthetic events?
React wraps native browser events in a cross-browser object called SyntheticEvent. It has the same interface as native events (`preventDefault`, `target`).

### Q18. How do you do conditional rendering?
Ternary `cond ? <A /> : <B />`, logical AND `cond && <A />` (careful with `0`), early return (`if (!data) return null`), or a lookup object.

### Q19. What are refs and when to use them?
`useRef` gives a mutable object `{ current }` that does not trigger a re-render. Use for DOM access (focus an input), storing timers or previous values, and for values that must survive renders without causing them.

### Q20. What is the Context API?
A way to share data (theme, user, language) with many components without passing props through every level. Create with `createContext`, provide with `<Context.Provider value={...}>`, read with `useContext`. **Caution:** every consumer re-renders when the value changes, so do not put fast-changing data in one big context.

### Q21. HOC (Higher Order Component) and render props?
A HOC is a function that takes a component and returns a new component with extra behaviour (`withAuth(Component)`). Render props pass a function as a prop to share logic. Today, custom hooks replace most uses of both.

### Q22. What are portals?
`ReactDOM.createPortal(child, domNode)` renders a child into a different DOM node outside the parent hierarchy. Used for modals, tooltips and dropdowns so that CSS overflow or z-index of the parent does not clip them.

### Q23. What are error boundaries?
Class components that catch errors in their child tree during rendering and show a fallback UI instead of crashing the whole app. They do not catch errors in event handlers or async code.

### Q24. What is `React.StrictMode`?
A development tool that runs extra checks. In React 18 it runs effects twice on mount in development to help you find missing cleanups. It does nothing in production.

### Q25. What is code splitting and lazy loading?
Splitting the bundle into smaller chunks that load only when needed.
```jsx
const Dashboard = React.lazy(() => import("./Dashboard"));

<Suspense fallback={<p>Loading...</p>}>
  <Dashboard />
</Suspense>
```

### Q26. What is `children` prop?
The content placed between the opening and closing tags of a component. It makes components flexible (`<Card><p>Hello</p></Card>`).

### Q27. What is the difference between an element and a component?
An element is a plain object describing what to show (`<div />`). A component is a function (or class) that returns elements.

### Q28. What is hydration?
When server-rendered HTML arrives in the browser, React attaches event handlers and makes it interactive. This is the "hydrate" step in SSR frameworks.

### Q29. What are the new features in React 18 and later (know the names)?
- **Automatic batching** of state updates.
- **Concurrent rendering** with `useTransition` and `useDeferredValue` (keep the UI responsive during heavy updates).
- **Suspense** improvements and **Server Components** (used by Next.js App Router).
- **React 19:** Actions, `useActionState`, `useOptimistic`, the `use` hook, and ref as a normal prop. Say: "I know the main ideas, and I mostly use hooks like useState, useEffect and React Query in my projects."

### Q30. How do you pass data from child to parent?
The parent passes a callback function as a prop. The child calls it with the data.
```jsx
function Parent() {
  const [msg, setMsg] = useState("");
  return <Child onSend={setMsg} />;
}
function Child({ onSend }) {
  return <button onClick={() => onSend("Hello")}>Send</button>;
}
```

### Q31. How do you handle forms and validation in React?
Controlled inputs with state, validate on change or submit, show error messages, disable the submit button while loading. For bigger forms use **React Hook Form** (fewer re-renders) with **Zod** or **Yup** for schema validation.

### Q32. How do you call APIs in React?
Inside `useEffect` with fetch or axios, handling loading, error and data states, and cancelling with `AbortController` on cleanup. In real projects I prefer **React Query** because it gives caching, retries, refetching and loading and error states out of the box.

### Q33. How do you handle authentication on the frontend?
Login API returns a token. Store it (HttpOnly cookie is safest, or memory/localStorage). Attach it to requests (axios interceptor). Keep user info in Context or Redux. Protect routes with a `ProtectedRoute` component. Handle token expiry (401) by refreshing the token or redirecting to login.

### Q34. How do you test React apps?
Jest as the test runner and React Testing Library to render components and test what the user sees and does (not implementation details). Mock API calls with MSW or jest mocks. Be honest if you have limited experience; mention you know the basic idea.

### Q35. How do you make a React app accessible?
Semantic HTML, `alt` text, labels for inputs, keyboard navigation, focus management in modals, ARIA attributes only when needed, enough color contrast.

### Q36. How do you prevent XSS in React?
React escapes values in JSX by default. Danger comes from `dangerouslySetInnerHTML` (sanitize with DOMPurify first), putting user input in `href="javascript:..."`, and storing tokens in localStorage.

### Q37. Folder structure you follow?
Feature-based: `src/features/appointments/{components, hooks, api, types}`, plus shared `components/`, `hooks/`, `utils/`, `services/`. Keep components small and reusable.

---

## 6. Hooks in depth

### Rules of Hooks
1. Call hooks only at the top level (not inside loops, conditions or nested functions).
2. Call hooks only from React function components or custom hooks.
(React depends on the call order of hooks to match state to each hook.)

### useState
```jsx
const [count, setCount] = useState(0);
setCount(count + 1);       // uses the value from this render
setCount(c => c + 1);      // functional update, always correct
const [data, setData] = useState(() => expensiveInit()); // lazy initial state, runs once
```

### useEffect, the dependency array
| Code | When the effect runs |
|---|---|
| `useEffect(fn)` | after every render |
| `useEffect(fn, [])` | once, after the first render |
| `useEffect(fn, [a, b])` | after first render and when `a` or `b` changes |

```jsx
useEffect(() => {
  const id = setInterval(() => console.log("tick"), 1000);
  return () => clearInterval(id);   // cleanup
}, []);
```

**Why do we get an infinite loop?** Setting state inside an effect that depends on that same state or on a new object or array created every render. Fix by correcting the dependencies, or by using `useMemo` or `useCallback` for those values.

**What is a stale closure?** A function that captured an old value of state. Example: `setInterval(() => setCount(count + 1), 1000)` with `[]` always sees `count = 0`. Fix: `setCount(c => c + 1)`.

**Race condition in fetching:** a slow old request finishing after a new one. Fix with `AbortController` or an `ignore` flag in cleanup.
```jsx
useEffect(() => {
  const controller = new AbortController();
  fetch(`/api/search?q=${q}`, { signal: controller.signal })
    .then(r => r.json())
    .then(setResults)
    .catch(e => { if (e.name !== "AbortError") setError(e.message); });
  return () => controller.abort();
}, [q]);
```

### useRef
```jsx
const inputRef = useRef(null);
useEffect(() => inputRef.current.focus(), []);
return <input ref={inputRef} />;
```
Also used to keep a previous value or a timer id without re-rendering.

### useMemo vs useCallback vs React.memo
- `useMemo(() => compute(a), [a])` caches a **value** (expensive calculation).
- `useCallback(fn, [deps])` caches a **function** so its reference stays the same.
- `React.memo(Component)` skips re-rendering a component if its props did not change.

**Important:** useCallback is useful mainly when you pass the function to a memoized child. Do not wrap everything, because memoization has its own cost. Measure first.

### useReducer
Like useState but for complex state logic. `const [state, dispatch] = useReducer(reducer, initial)`.
```jsx
function reducer(state, action) {
  switch (action.type) {
    case "add": return { ...state, items: [...state.items, action.item] };
    case "remove": return { ...state, items: state.items.filter(i => i.id !== action.id) };
    default: return state;
  }
}
```
Use when state has many related fields or the next state depends on actions (like a cart).

### useContext
```jsx
const ThemeContext = createContext("light");
const theme = useContext(ThemeContext);
```

### useLayoutEffect vs useEffect
`useLayoutEffect` runs synchronously after DOM changes but before the browser paints. Use for measuring the DOM (size, position) to avoid flicker. `useEffect` runs after paint and is the default choice.

### Other hooks to know by name
`useId` (unique ids for forms), `useTransition` and `useDeferredValue` (keep UI smooth), `useImperativeHandle` with `forwardRef` (expose methods from a child), `useSyncExternalStore` (subscribe to external stores).

### Custom hooks
A function starting with `use` that uses other hooks to share logic between components. See section 12 for code (`useFetch`, `useDebounce`, `useLocalStorage`).

---

## 7. React advanced topics

### Performance optimization checklist (very common question)
1. Find the problem first with **React DevTools Profiler**.
2. Use stable keys and avoid index as key.
3. `React.memo`, `useMemo`, `useCallback` where re-renders are actually costly.
4. Keep state close to where it is used (do not put everything at the top).
5. **Virtualize long lists** (react-window or react-virtualized) so only visible rows are rendered.
6. Code splitting with `React.lazy` and route-based splitting.
7. Debounce search inputs, paginate API data.
8. Optimize images (correct size, lazy loading, modern formats).
9. Avoid creating new objects and functions in props for memoized children.
10. Use React Query for caching to avoid repeated API calls.

**Say it like this:** "I do not optimize blindly. I first use the Profiler to see which component re-renders too often, then fix the cause, for example by moving state down, memoizing the component, or virtualizing the list."

### State management: which one when?
| Need | Choose |
|---|---|
| Local UI state (input, toggle) | `useState` |
| Complex local state | `useReducer` |
| Share a little data (theme, user) | Context API |
| Large app state with many updates | Redux Toolkit or Zustand |
| Server data (APIs) | React Query (TanStack Query) |

### Redux Toolkit flow
Store holds the state. A **slice** has the initial state, reducers and actions (`createSlice`). A component **dispatches** an action, the reducer updates the state (Immer lets you write "mutating" code safely), and components that **select** that state re-render. Async API work uses `createAsyncThunk` or RTK Query.
```js
const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: { increment: (state) => { state.value += 1; } },
});
```

### Why React Query?
It handles caching, background refetching, loading and error states, retries, pagination, and invalidation after mutations. It removes a lot of `useEffect` + `useState` code for fetching.
```jsx
const { data, isLoading, error } = useQuery({
  queryKey: ["doctors"],
  queryFn: () => fetch("/api/doctors").then(r => r.json()),
});
const mutation = useMutation({ mutationFn: createAppointment,
  onSuccess: () => queryClient.invalidateQueries({ queryKey: ["appointments"] }) });
```

### React Router (v6)
```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/doctors/:id" element={<Doctor />} />
    <Route element={<ProtectedRoute />}>
      <Route path="/dashboard" element={<Dashboard />} />
    </Route>
    <Route path="*" element={<NotFound />} />
  </Routes>
</BrowserRouter>
```
Hooks: `useNavigate`, `useParams`, `useLocation`, `useSearchParams`. Know nested routes, `<Outlet />`, `<Link>` vs `<a>` (Link does not reload the page).

**Protected route:**
```jsx
function ProtectedRoute({ allowedRoles }) {
  const { user } = useAuth();
  if (!user) return <Navigate to="/login" replace />;
  if (allowedRoles && !allowedRoles.includes(user.role)) return <Navigate to="/unauthorized" replace />;
  return <Outlet />;
}
```

### CSR vs SSR vs SSG vs ISR
- **CSR:** browser builds the page with JavaScript. Fast after first load, weak first paint and SEO.
- **SSR:** server renders HTML on each request. Good SEO and first paint, more server work.
- **SSG:** HTML generated at build time. Very fast, for content that rarely changes.
- **ISR:** static pages that regenerate in the background after a time.

### Next.js basics (know the main points)
File-based routing, layouts, server and client components (`"use client"`), data fetching on the server, API routes or route handlers, image optimization (`next/image`), SEO metadata. Use Next.js when you need SEO, fast first load, or one codebase for frontend and light backend.

### Real-time in React (relevant to your projects)
Connect the socket once in `useEffect`, listen for events, update state, and clean up.
```jsx
useEffect(() => {
  const socket = io(URL);
  socket.on("message", (m) => setMessages(prev => [...prev, m]));
  return () => socket.disconnect();
}, []);
```
Common bug: adding the listener multiple times because cleanup was missed.

### File upload with progress
Use `FormData`, `axios.post(url, formData, { onUploadProgress })`, and show a progress bar and an error state.

---

## 8. HTML questions

**Q1. What is semantic HTML? Why important?**
Using tags that describe meaning: `header`, `nav`, `main`, `section`, `article`, `aside`, `footer`. It helps SEO, accessibility (screen readers) and code readability.

**Q2. Block vs inline vs inline-block elements?**
Block takes a full line and respects width and height (`div`, `p`, `h1`). Inline flows in a line and ignores width and height (`span`, `a`). Inline-block flows in a line but accepts width and height.

**Q3. `div` vs `span`?** `div` is a block container, `span` is an inline container. Both have no meaning on their own.

**Q4. What is the DOCTYPE?** `<!DOCTYPE html>` tells the browser to use standards mode (HTML5).

**Q5. Why is the viewport meta tag needed?** `<meta name="viewport" content="width=device-width, initial-scale=1">` makes the page scale properly on mobile screens.

**Q6. `async` vs `defer` on script tags?** Normal scripts block HTML parsing. `async` downloads in parallel and runs as soon as ready (order not guaranteed). `defer` downloads in parallel and runs after HTML is parsed, in order. Use `defer` for most scripts.

**Q7. Why `alt` on images?** For screen readers, SEO, and when the image fails to load.

**Q8. `id` vs `class`?** `id` is unique in a page. `class` can be reused on many elements.

**Q9. What are data attributes?** `data-*` attributes store custom data on elements, read in JS with `element.dataset`.

**Q10. `localStorage` vs cookies?** See JavaScript Q27.

**Q11. What is the difference between `<section>` and `<div>`?** `section` is semantic (a themed group of content, normally with a heading). `div` has no meaning.

**Q12. How do forms work? Important input attributes?** `<form>`, `<label for>`, `<input type name required pattern placeholder>`, `<select>`, `<textarea>`, `<button type="submit">`. HTML5 gives built-in validation (`required`, `minlength`, `type="email"`).

**Q13. What is ARIA?** Attributes (`aria-label`, `role`, `aria-expanded`) that add accessibility information when HTML alone is not enough. First rule: use native semantic elements before ARIA.

**Q14. Reflow vs repaint?** Reflow recalculates layout (expensive), repaint redraws pixels without changing layout. Batch DOM changes and prefer `transform` for animations.

**Q15. What are `iframe`s and are they safe?** They embed another page. Use `sandbox` and be careful with untrusted content (we use an iframe for Jitsi Meet style embeds).

---

## 9. CSS questions

**Q1. Box model?** Every element is a box: content, padding, border, margin. `box-sizing: border-box` makes `width` include padding and border, which is easier to work with. Many projects set `* { box-sizing: border-box; }`.

**Q2. CSS specificity?** Decides which rule wins: inline style > id > class, attribute and pseudo-class > element. `!important` overrides all (avoid it). If equal, the later rule wins.

**Q3. `position` values?**
- `static` default.
- `relative` moves relative to its normal place and is the anchor for absolute children.
- `absolute` positioned against the nearest positioned ancestor.
- `fixed` positioned against the viewport (stays on scroll).
- `sticky` behaves like relative until a scroll point, then sticks.

**Q4. `display: none` vs `visibility: hidden` vs `opacity: 0`?** `none` removes it from layout. `visibility: hidden` hides it but keeps its space. `opacity: 0` is invisible but still takes space and still receives clicks.

**Q5. Flexbox vs Grid?** Flexbox is one-dimensional (row or column). Grid is two-dimensional (rows and columns together). Use flex for components and alignment, grid for page layouts.
```css
.row { display: flex; justify-content: space-between; align-items: center; gap: 12px; }
.cards { display: grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap: 16px; }
```

**Q6. How do you center a div?**
```css
.parent { display: flex; justify-content: center; align-items: center; }
/* or */
.parent { display: grid; place-items: center; }
/* or horizontally only */
.child { margin: 0 auto; width: 300px; }
```

**Q7. `px` vs `em` vs `rem` vs `%` vs `vh/vw`?** `px` is fixed. `em` is relative to the parent font size. `rem` is relative to the root font size (predictable, good for accessibility). `%` is relative to the parent. `vh` and `vw` are relative to the viewport.

**Q8. How do you make a site responsive?** Mobile-first design, flexible units, flexbox and grid, media queries, responsive images, and the viewport meta tag.
```css
@media (min-width: 768px) { .container { max-width: 720px; } }
```

**Q9. Pseudo-classes vs pseudo-elements?** Pseudo-classes target a state (`:hover`, `:focus`, `:nth-child`). Pseudo-elements target a part of an element (`::before`, `::after`, `::placeholder`).

**Q10. How does `z-index` work?** It controls stacking order but only works on positioned elements (or flex and grid items). Stacking contexts can trap a high z-index inside a parent.

**Q11. `overflow` values?** `visible`, `hidden`, `scroll`, `auto`. Use `overflow-x: auto` for wide tables.

**Q12. CSS variables?** `:root { --primary: #1f3a5f; }` and `color: var(--primary);`. Great for themes and dark mode.

**Q13. Transitions vs animations?** A transition animates between two states (hover). Keyframe animations (`@keyframes`) can run multi-step sequences automatically. Animate `transform` and `opacity` for good performance.

**Q14. What is BEM?** A naming convention: `block__element--modifier` (`card__title--large`). Helps avoid style clashes in plain CSS.

**Q15. Ways to style React apps?** Plain CSS, CSS Modules (scoped class names), styled-components (CSS in JS), Tailwind CSS (utility classes), UI libraries (shadcn/ui, MUI). **Tailwind pros:** fast development, consistent design, small final CSS. **Cons:** long class lists, learning curve.

**Q16. How do you implement dark mode?** CSS variables for colors, a class or `data-theme` on `html`, store the choice in localStorage, and respect `prefers-color-scheme`.

**Q17. Inline vs internal vs external CSS?** Inline is in the tag, internal is in a `<style>` tag, external is a `.css` file (best for caching and reuse).

**Q18. How do you hide an element but keep it for screen readers?** Use a visually-hidden class (clip and absolute position) rather than `display: none`.

---
## 10. Scenario based questions

For each scenario: **identify the problem, give the fix, mention one trade-off.** Keep answers short and structured.

**S1. The page is slow. How do you find and fix the problem?**
Measure first: Chrome DevTools Performance and Network tabs, Lighthouse, React Profiler. Then fix by cause: big bundle (code split, remove unused libraries), slow API (cache, paginate), too many re-renders (memo, move state down), large images (resize, lazy load), huge lists (virtualize).

**S2. You must show 10,000 items in a list.**
Do not render all of them. Use virtualization (react-window) or pagination or infinite scroll. Also use stable keys and memoized row components.

**S3. A search box calls the API on every key press. How do you improve it?**
Debounce the input (300 to 500 ms), cancel the old request with `AbortController`, show a loading state, and ignore empty queries. React Query also helps by caching previous results.

**S4. Two distant components need the same data.**
Lift state up if they are close. Otherwise use Context, Redux Toolkit or Zustand. If the data comes from an API, use React Query so both components read from the same cache.

**S5. A component re-renders too often.**
Open the Profiler to see why. Possible fixes: wrap in `React.memo`, stabilize props with `useMemo` and `useCallback`, split the component so only the changing part re-renders, move state down, split context.

**S6. The JWT token expires while the user is working.**
Use an axios interceptor: on 401, call the refresh token endpoint, retry the failed request, and if refresh fails, clear the session and redirect to login. Queue requests that fail at the same time so refresh is called only once.

**S7. Old API response overwrites new data (race condition).**
Happens when a slow earlier request finishes after a later one. Cancel the old request with `AbortController` in the effect cleanup, or use React Query, which handles it.

**S8. How do you build role-based UI (User, Doctor, Admin)?**
Store the role after login. Use a `ProtectedRoute` with `allowedRoles`. Hide or show menu items by role. **Important:** frontend checks are only for user experience. The real permission checks must be on the backend. (This matches your eAsha project.)

**S9. A form has 20 fields and is slow or hard to manage.**
Use React Hook Form (uncontrolled inputs, fewer re-renders), schema validation with Zod, split the form into sections or steps, and keep field state local.

**S10. How do you show real-time updates, like a new appointment status?**
WebSockets (Socket.io): connect in `useEffect`, listen for events, update state, clean up on unmount. Fallback options are polling or server-sent events. With React Query, you can invalidate the query when a socket event arrives.

**S11. How do you handle errors in the UI?**
Local error states for API calls with friendly messages, error boundaries for rendering crashes, a global axios interceptor for common cases (401, 500), toast notifications, and logging to a service like Sentry.

**S12. How do you make a React SPA SEO friendly?**
Plain CSR has weak SEO. Use SSR or SSG with Next.js, set proper title and meta tags, semantic HTML, sitemap and good performance. For internal dashboards SEO does not matter.

**S13. How do you upload a large file and show progress?**
`FormData` with axios `onUploadProgress` for a progress bar, validate size and type before upload, allow cancel with `AbortController`, and for very large files use chunked or presigned S3 uploads.

**S14. How do you design a reusable component (Button, Modal, Input)?**
Keep it generic with props (`variant`, `size`, `disabled`, `onClick`), use `children` for content, forward refs and extra props (`...rest`), keep styling consistent with a design system, add accessibility (labels, keyboard), and write types with TypeScript.

**S15. A bug happens only in production. How do you debug?**
Reproduce with the same data and build, check browser console and network, use source maps and error monitoring (Sentry), check environment variables and API URLs, compare with a local production build.

**S16. How would you structure state for a shopping cart?**
`useReducer` or Redux slice with actions `add`, `remove`, `updateQty`, `clear`. Derive totals with a selector or `useMemo` instead of storing them. Persist to localStorage.

**S17. How do you handle a payment flow securely on the frontend? (your Razorpay experience)**
The frontend never decides if payment succeeded. Backend creates the order, frontend opens checkout, then sends the response to the backend, and the backend verifies the signature and updates the status. Frontend shows the status from the backend and handles failure and retry.

---

## 11. Live coding: React components

**How to practise:** open an empty Vite project (`npm create vite@latest`), type each component from memory, then compare. Aim to write each in 10 to 15 minutes.

### 11.1 Counter (warm-up)
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

### 11.2 Todo list (add, toggle, delete, filter) (very common)
```jsx
import { useState } from "react";

export default function TodoApp() {
  const [text, setText] = useState("");
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState("all");

  const addTodo = (e) => {
    e.preventDefault();
    const title = text.trim();
    if (!title) return;
    setTodos(prev => [...prev, { id: Date.now(), title, done: false }]);
    setText("");
  };

  const toggle = (id) =>
    setTodos(prev => prev.map(t => (t.id === id ? { ...t, done: !t.done } : t)));

  const remove = (id) => setTodos(prev => prev.filter(t => t.id !== id));

  const visible = todos.filter(t =>
    filter === "all" ? true : filter === "done" ? t.done : !t.done
  );

  return (
    <div>
      <form onSubmit={addTodo}>
        <input value={text} onChange={e => setText(e.target.value)} placeholder="Add task" />
        <button type="submit">Add</button>
      </form>

      <div>
        {["all", "active", "done"].map(f => (
          <button key={f} onClick={() => setFilter(f)} disabled={filter === f}>{f}</button>
        ))}
      </div>

      {visible.length === 0 && <p>No tasks</p>}
      <ul>
        {visible.map(t => (
          <li key={t.id}>
            <input type="checkbox" checked={t.done} onChange={() => toggle(t.id)} />
            <span style={{ textDecoration: t.done ? "line-through" : "none" }}>{t.title}</span>
            <button onClick={() => remove(t.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```
**Explain while coding:** immutable updates, functional updates, key from id, derived `visible` list (not stored in state).

### 11.3 Fetch and display data (loading, error, empty)
```jsx
import { useState, useEffect } from "react";

export default function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    const controller = new AbortController();
    fetch("https://jsonplaceholder.typicode.com/users", { signal: controller.signal })
      .then(res => {
        if (!res.ok) throw new Error("Failed to load users");
        return res.json();
      })
      .then(setUsers)
      .catch(err => { if (err.name !== "AbortError") setError(err.message); })
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p style={{ color: "red" }}>{error}</p>;
  if (users.length === 0) return <p>No users found</p>;

  return (
    <ul>
      {users.map(u => <li key={u.id}>{u.name} ({u.email})</li>)}
    </ul>
  );
}
```

### 11.4 Search with debounce
Uses the `useDebounce` hook from section 12.
```jsx
import { useState, useEffect } from "react";
import useDebounce from "./useDebounce";

export default function Search() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);
  const debounced = useDebounce(query, 400);

  useEffect(() => {
    if (!debounced.trim()) { setResults([]); return; }
    const controller = new AbortController();
    setLoading(true);
    fetch(`https://dummyjson.com/products/search?q=${encodeURIComponent(debounced)}`, { signal: controller.signal })
      .then(r => r.json())
      .then(d => setResults(d.products))
      .catch(e => { if (e.name !== "AbortError") console.error(e); })
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, [debounced]);

  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} placeholder="Search products" />
      {loading && <p>Searching...</p>}
      <ul>{results.map(p => <li key={p.id}>{p.title}</li>)}</ul>
    </div>
  );
}
```

### 11.5 Form with validation (login or register)
```jsx
import { useState } from "react";

export default function LoginForm() {
  const [values, setValues] = useState({ email: "", password: "" });
  const [errors, setErrors] = useState({});
  const [submitting, setSubmitting] = useState(false);

  const validate = () => {
    const e = {};
    if (!values.email) e.email = "Email is required";
    else if (!/^\S+@\S+\.\S+$/.test(values.email)) e.email = "Enter a valid email";
    if (!values.password) e.password = "Password is required";
    else if (values.password.length < 6) e.password = "Minimum 6 characters";
    return e;
  };

  const handleChange = (e) =>
    setValues(prev => ({ ...prev, [e.target.name]: e.target.value }));

  const handleSubmit = async (e) => {
    e.preventDefault();
    const found = validate();
    setErrors(found);
    if (Object.keys(found).length) return;
    setSubmitting(true);
    try {
      // await api.post("/login", values);
      console.log("Submitted", values);
    } finally {
      setSubmitting(false);
    }
  };

  return (
    <form onSubmit={handleSubmit} noValidate>
      <label htmlFor="email">Email</label>
      <input id="email" name="email" value={values.email} onChange={handleChange} />
      {errors.email && <span>{errors.email}</span>}

      <label htmlFor="password">Password</label>
      <input id="password" name="password" type="password" value={values.password} onChange={handleChange} />
      {errors.password && <span>{errors.password}</span>}

      <button type="submit" disabled={submitting}>{submitting ? "Logging in..." : "Login"}</button>
    </form>
  );
}
```
**Explain:** one `handleChange` using `name`, validation function, disabled button while submitting, labels linked with `htmlFor`.

### 11.6 Accordion
```jsx
import { useState } from "react";

const items = [
  { id: 1, title: "What is React?", body: "A library for building UIs." },
  { id: 2, title: "What is JSX?", body: "HTML-like syntax in JavaScript." },
];

export default function Accordion() {
  const [openId, setOpenId] = useState(null);
  return (
    <div>
      {items.map(item => (
        <div key={item.id}>
          <button onClick={() => setOpenId(openId === item.id ? null : item.id)}
                  aria-expanded={openId === item.id}>
            {item.title}
          </button>
          {openId === item.id && <p>{item.body}</p>}
        </div>
      ))}
    </div>
  );
}
```

### 11.7 Tabs
```jsx
import { useState } from "react";

const tabs = [
  { id: "a", label: "Profile", content: "Profile content" },
  { id: "b", label: "Settings", content: "Settings content" },
];

export default function Tabs() {
  const [active, setActive] = useState(tabs[0].id);
  const current = tabs.find(t => t.id === active);
  return (
    <div>
      <div role="tablist">
        {tabs.map(t => (
          <button key={t.id} role="tab" aria-selected={t.id === active} onClick={() => setActive(t.id)}>
            {t.label}
          </button>
        ))}
      </div>
      <div role="tabpanel">{current.content}</div>
    </div>
  );
}
```

### 11.8 Modal (portal, close on Escape and outside click)
```jsx
import { useEffect } from "react";
import { createPortal } from "react-dom";

export default function Modal({ open, onClose, children }) {
  useEffect(() => {
    if (!open) return;
    const onKey = (e) => e.key === "Escape" && onClose();
    document.addEventListener("keydown", onKey);
    return () => document.removeEventListener("keydown", onKey);
  }, [open, onClose]);

  if (!open) return null;
  return createPortal(
    <div onClick={onClose} style={{ position: "fixed", inset: 0, background: "rgba(0,0,0,.5)",
                                    display: "grid", placeItems: "center" }}>
      <div onClick={(e) => e.stopPropagation()} style={{ background: "#fff", padding: 24 }}>
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </div>,
    document.body
  );
}
```

### 11.9 Pagination (client side)
```jsx
import { useState } from "react";

export default function PaginatedList({ items, pageSize = 5 }) {
  const [page, setPage] = useState(1);
  const totalPages = Math.ceil(items.length / pageSize);
  const start = (page - 1) * pageSize;
  const current = items.slice(start, start + pageSize);

  return (
    <div>
      <ul>{current.map(i => <li key={i.id}>{i.name}</li>)}</ul>
      <button onClick={() => setPage(p => p - 1)} disabled={page === 1}>Prev</button>
      <span> Page {page} of {totalPages} </span>
      <button onClick={() => setPage(p => p + 1)} disabled={page === totalPages}>Next</button>
    </div>
  );
}
```
**Formula to remember:** `start = (page - 1) * pageSize`, `slice(start, start + pageSize)`, `totalPages = Math.ceil(total / pageSize)`.

### 11.10 Star rating
```jsx
import { useState } from "react";

export default function StarRating({ total = 5 }) {
  const [rating, setRating] = useState(0);
  const [hover, setHover] = useState(0);
  return (
    <div>
      {Array.from({ length: total }, (_, i) => i + 1).map(star => (
        <span key={star}
              style={{ cursor: "pointer", fontSize: 28, color: star <= (hover || rating) ? "gold" : "#ccc" }}
              onClick={() => setRating(star)}
              onMouseEnter={() => setHover(star)}
              onMouseLeave={() => setHover(0)}>
          ★
        </span>
      ))}
      <p>Rating: {rating}</p>
    </div>
  );
}
```

### 11.11 Stopwatch / timer (cleanup practice)
```jsx
import { useState, useRef, useEffect } from "react";

export default function Stopwatch() {
  const [seconds, setSeconds] = useState(0);
  const [running, setRunning] = useState(false);
  const intervalRef = useRef(null);

  useEffect(() => {
    if (running) {
      intervalRef.current = setInterval(() => setSeconds(s => s + 1), 1000);
    }
    return () => clearInterval(intervalRef.current);
  }, [running]);

  const reset = () => { setRunning(false); setSeconds(0); };

  return (
    <div>
      <h2>{seconds}s</h2>
      <button onClick={() => setRunning(r => !r)}>{running ? "Pause" : "Start"}</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

### 11.12 Infinite scroll with IntersectionObserver
```jsx
import { useState, useEffect, useRef, useCallback } from "react";

export default function InfiniteList() {
  const [items, setItems] = useState([]);
  const [page, setPage] = useState(1);
  const [hasMore, setHasMore] = useState(true);
  const [loading, setLoading] = useState(false);
  const observer = useRef();

  useEffect(() => {
    setLoading(true);
    fetch(`https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=10`)
      .then(r => r.json())
      .then(data => {
        setItems(prev => [...prev, ...data]);
        setHasMore(data.length > 0);
      })
      .finally(() => setLoading(false));
  }, [page]);

  const lastRef = useCallback((node) => {
    if (loading) return;
    if (observer.current) observer.current.disconnect();
    observer.current = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting && hasMore) setPage(p => p + 1);
    });
    if (node) observer.current.observe(node);
  }, [loading, hasMore]);

  return (
    <div>
      {items.map((item, i) => (
        <div key={item.id} ref={i === items.length - 1 ? lastRef : null}>{item.title}</div>
      ))}
      {loading && <p>Loading...</p>}
    </div>
  );
}
```

### 11.13 Theme toggle with Context
```jsx
import { createContext, useContext, useState } from "react";

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");
  const toggle = () => setTheme(t => (t === "light" ? "dark" : "light"));
  return <ThemeContext.Provider value={{ theme, toggle }}>{children}</ThemeContext.Provider>;
}

export const useTheme = () => useContext(ThemeContext);

function Header() {
  const { theme, toggle } = useTheme();
  return <button onClick={toggle}>Current: {theme}</button>;
}

export default function App() {
  return <ThemeProvider><Header /></ThemeProvider>;
}
```

### 11.14 Shopping cart with useReducer
```jsx
import { useReducer } from "react";

function cartReducer(state, action) {
  switch (action.type) {
    case "add": {
      const exists = state.find(i => i.id === action.item.id);
      return exists
        ? state.map(i => i.id === action.item.id ? { ...i, qty: i.qty + 1 } : i)
        : [...state, { ...action.item, qty: 1 }];
    }
    case "remove": return state.filter(i => i.id !== action.id);
    case "clear": return [];
    default: return state;
  }
}

export default function Cart() {
  const [cart, dispatch] = useReducer(cartReducer, []);
  const total = cart.reduce((sum, i) => sum + i.price * i.qty, 0);
  const products = [{ id: 1, name: "Pen", price: 10 }, { id: 2, name: "Book", price: 100 }];

  return (
    <div>
      {products.map(p => (
        <button key={p.id} onClick={() => dispatch({ type: "add", item: p })}>Add {p.name}</button>
      ))}
      <ul>
        {cart.map(i => (
          <li key={i.id}>{i.name} x {i.qty}
            <button onClick={() => dispatch({ type: "remove", id: i.id })}>Remove</button>
          </li>
        ))}
      </ul>
      <p>Total: {total}</p>
    </div>
  );
}
```

### 11.15 Select all checkboxes
```jsx
import { useState } from "react";

export default function SelectAll() {
  const items = ["Apple", "Mango", "Banana"];
  const [selected, setSelected] = useState([]);
  const allChecked = selected.length === items.length;

  const toggleAll = () => setSelected(allChecked ? [] : items);
  const toggleOne = (item) =>
    setSelected(prev => prev.includes(item) ? prev.filter(i => i !== item) : [...prev, item]);

  return (
    <div>
      <label><input type="checkbox" checked={allChecked} onChange={toggleAll} /> Select all</label>
      {items.map(i => (
        <label key={i}><input type="checkbox" checked={selected.includes(i)} onChange={() => toggleOne(i)} /> {i}</label>
      ))}
    </div>
  );
}
```

### 11.16 OTP input (focus handling)
```jsx
import { useRef, useState } from "react";

export default function Otp({ length = 4 }) {
  const [otp, setOtp] = useState(Array(length).fill(""));
  const refs = useRef([]);

  const handleChange = (value, i) => {
    if (!/^\d?$/.test(value)) return;
    const next = [...otp];
    next[i] = value;
    setOtp(next);
    if (value && i < length - 1) refs.current[i + 1].focus();
  };

  const handleKeyDown = (e, i) => {
    if (e.key === "Backspace" && !otp[i] && i > 0) refs.current[i - 1].focus();
  };

  return (
    <div>
      {otp.map((digit, i) => (
        <input key={i} ref={el => (refs.current[i] = el)} value={digit} maxLength={1}
               onChange={e => handleChange(e.target.value, i)}
               onKeyDown={e => handleKeyDown(e, i)} style={{ width: 32, textAlign: "center" }} />
      ))}
    </div>
  );
}
```

### 11.17 Protected route with auth context
See section 7 for `ProtectedRoute`. Be ready to also write a small `AuthProvider` with `login`, `logout` and `user` state.

### Other component problems to practise (build each once)
Progress bar, image carousel / slider, nested comments, file explorer (recursive component), autocomplete dropdown, drag and drop list, sortable table with search, multi-step form, toast notifications, tic-tac-toe, quiz app with score, weather app using an API, chat message list with auto-scroll to bottom.

---

## 12. Live coding: custom hooks

### useDebounce
```jsx
import { useState, useEffect } from "react";

export default function useDebounce(value, delay = 300) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const id = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);
  return debounced;
}
```

### useFetch
```jsx
import { useState, useEffect } from "react";

export default function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);
    setError(null);
    fetch(url, { signal: controller.signal })
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(setData)
      .catch(err => { if (err.name !== "AbortError") setError(err.message); })
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}
```

### useLocalStorage
```jsx
import { useState, useEffect } from "react";

export default function useLocalStorage(key, initial) {
  const [value, setValue] = useState(() => {
    try {
      const saved = localStorage.getItem(key);
      return saved ? JSON.parse(saved) : initial;
    } catch {
      return initial;
    }
  });
  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);
  return [value, setValue];
}
```

### usePrevious
```jsx
import { useRef, useEffect } from "react";

export default function usePrevious(value) {
  const ref = useRef();
  useEffect(() => { ref.current = value; }, [value]);
  return ref.current;
}
```

### useToggle
```jsx
import { useState, useCallback } from "react";

export default function useToggle(initial = false) {
  const [on, setOn] = useState(initial);
  const toggle = useCallback(() => setOn(v => !v), []);
  return [on, toggle];
}
```

### useOnClickOutside
```jsx
import { useEffect } from "react";

export default function useOnClickOutside(ref, handler) {
  useEffect(() => {
    const listener = (e) => {
      if (!ref.current || ref.current.contains(e.target)) return;
      handler(e);
    };
    document.addEventListener("mousedown", listener);
    return () => document.removeEventListener("mousedown", listener);
  }, [ref, handler]);
}
```

---
## 13. Live coding: JavaScript functions and polyfills

### debounce
```js
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

### throttle
```js
function throttle(fn, limit) {
  let last = 0;
  return function (...args) {
    const now = Date.now();
    if (now - last >= limit) {
      last = now;
      fn.apply(this, args);
    }
  };
}
```

### Array.prototype.map, filter, reduce polyfills
```js
Array.prototype.myMap = function (cb, thisArg) {
  const out = [];
  for (let i = 0; i < this.length; i++) {
    if (i in this) out[i] = cb.call(thisArg, this[i], i, this);
  }
  return out;
};

Array.prototype.myFilter = function (cb, thisArg) {
  const out = [];
  for (let i = 0; i < this.length; i++) {
    if (i in this && cb.call(thisArg, this[i], i, this)) out.push(this[i]);
  }
  return out;
};

Array.prototype.myReduce = function (cb, initial) {
  let i = 0;
  let acc = initial;
  if (arguments.length < 2) {
    if (this.length === 0) throw new TypeError("Reduce of empty array with no initial value");
    acc = this[0];
    i = 1;
  }
  for (; i < this.length; i++) acc = cb(acc, this[i], i, this);
  return acc;
};
```

### bind polyfill
```js
Function.prototype.myBind = function (context, ...boundArgs) {
  const fn = this;
  return function (...args) {
    return fn.apply(context, [...boundArgs, ...args]);
  };
};
```

### Promise.all polyfill
```js
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    const results = [];
    let completed = 0;
    if (promises.length === 0) return resolve([]);
    promises.forEach((p, i) => {
      Promise.resolve(p)
        .then(value => {
          results[i] = value;
          if (++completed === promises.length) resolve(results);
        })
        .catch(reject);
    });
  });
}
```

### deep clone
```js
function deepClone(value) {
  if (value === null || typeof value !== "object") return value;
  if (value instanceof Date) return new Date(value);
  if (Array.isArray(value)) return value.map(deepClone);
  const copy = {};
  for (const key of Object.keys(value)) copy[key] = deepClone(value[key]);
  return copy;
}
```

### flatten an array
```js
function flatten(arr, depth = Infinity) {
  return arr.reduce(
    (acc, item) =>
      Array.isArray(item) && depth > 0 ? acc.concat(flatten(item, depth - 1)) : acc.concat(item),
    []
  );
}
flatten([1, [2, [3, [4]]]]); // [1,2,3,4]
```

### once, memoize, curry, compose
```js
function once(fn) {
  let called = false, result;
  return function (...args) {
    if (!called) { called = true; result = fn.apply(this, args); }
    return result;
  };
}

function memoize(fn) {
  const cache = new Map();
  return function (...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

function curry(fn) {
  return function curried(...args) {
    return args.length >= fn.length
      ? fn.apply(this, args)
      : (...more) => curried.apply(this, [...args, ...more]);
  };
}
// curry((a, b, c) => a + b + c)(1)(2)(3) === 6

const compose = (...fns) => (x) => fns.reduceRight((acc, fn) => fn(acc), x);
```

### Simple event emitter
```js
class EventEmitter {
  constructor() { this.events = {}; }
  on(name, cb) { (this.events[name] ||= []).push(cb); }
  off(name, cb) { this.events[name] = (this.events[name] || []).filter(f => f !== cb); }
  emit(name, ...args) { (this.events[name] || []).forEach(cb => cb(...args)); }
}
```

### Common problems (write these in under 5 minutes each)
```js
// Reverse a string
const reverse = (s) => s.split("").reverse().join("");

// Palindrome
const isPalindrome = (s) => {
  const t = s.toLowerCase().replace(/[^a-z0-9]/g, "");
  return t === t.split("").reverse().join("");
};

// Remove duplicates
const unique = (arr) => [...new Set(arr)];

// Count frequency
const frequency = (arr) =>
  arr.reduce((acc, x) => { acc[x] = (acc[x] || 0) + 1; return acc; }, {});

// Group by key
const groupBy = (arr, key) =>
  arr.reduce((acc, item) => { (acc[item[key]] ||= []).push(item); return acc; }, {});

// Chunk an array
const chunk = (arr, size) => {
  const out = [];
  for (let i = 0; i < arr.length; i += size) out.push(arr.slice(i, i + size));
  return out;
};

// Two Sum (O(n) with a map)
function twoSum(nums, target) {
  const seen = new Map();
  for (let i = 0; i < nums.length; i++) {
    const need = target - nums[i];
    if (seen.has(need)) return [seen.get(need), i];
    seen.set(nums[i], i);
  }
  return [];
}

// Valid parentheses (stack)
function isValid(s) {
  const pairs = { ")": "(", "]": "[", "}": "{" };
  const stack = [];
  for (const ch of s) {
    if (ch in pairs) { if (stack.pop() !== pairs[ch]) return false; }
    else stack.push(ch);
  }
  return stack.length === 0;
}

// Anagram
const isAnagram = (a, b) =>
  a.length === b.length && [...a].sort().join("") === [...b].sort().join("");

// First non-repeating character
function firstUnique(s) {
  const count = {};
  for (const c of s) count[c] = (count[c] || 0) + 1;
  for (const c of s) if (count[c] === 1) return c;
  return null;
}

// FizzBuzz
for (let i = 1; i <= 15; i++) {
  console.log(i % 15 === 0 ? "FizzBuzz" : i % 3 === 0 ? "Fizz" : i % 5 === 0 ? "Buzz" : i);
}

// Largest number and second largest
function topTwo(arr) {
  let first = -Infinity, second = -Infinity;
  for (const n of arr) {
    if (n > first) { second = first; first = n; }
    else if (n > second && n !== first) second = n;
  }
  return [first, second];
}

// Sort array of objects by a key
users.sort((a, b) => a.age - b.age);                  // numbers
users.sort((a, b) => a.name.localeCompare(b.name));   // strings
```

### Async practice
```js
// Sleep helper
const sleep = (ms) => new Promise(res => setTimeout(res, ms));

// Retry an async function
async function retry(fn, times = 3) {
  for (let i = 0; i < times; i++) {
    try { return await fn(); }
    catch (err) { if (i === times - 1) throw err; await sleep(500); }
  }
}

// Run promises one after another vs in parallel
for (const url of urls) await fetch(url);          // sequential
await Promise.all(urls.map(u => fetch(u)));        // parallel
```

---

## 14. Pseudo code guide

The interviewer wants to see your **thinking**, not exact syntax. Pseudo code is plain English steps that look like code. No semicolons, no exact function names needed.

**Format to follow**
```
FUNCTION name(inputs)
    IF condition THEN
        do something
    ELSE
        do something else
    END IF

    FOR each item IN list
        do something
    END FOR

    RETURN result
END FUNCTION
```

**Example 1: Search filter in a list (React)**
```
STATE: allUsers (from API), query (text typed)
ON input change:
    SET query = typed text
FILTERED = allUsers WHERE name (lowercase) CONTAINS query (lowercase)
RENDER FILTERED list
IF FILTERED is empty THEN show "No results"
```

**Example 2: Login flow**
```
ON form submit:
    VALIDATE email and password
    IF invalid THEN show errors AND STOP
    SET loading = true
    CALL login API with email and password
    IF success THEN
        SAVE token
        SAVE user in context
        NAVIGATE to dashboard (by role)
    ELSE
        SHOW error message
    SET loading = false
```

**Example 3: Pagination**
```
INPUT: items, pageSize, currentPage
totalPages = CEIL(items.length / pageSize)
start = (currentPage - 1) * pageSize
visible = items FROM start TO start + pageSize
Previous button disabled IF currentPage == 1
Next button disabled IF currentPage == totalPages
```

**Example 4: Find duplicates in an array**
```
CREATE empty set seen, empty list duplicates
FOR each number IN array
    IF number IS IN seen THEN add to duplicates
    ELSE add number to seen
RETURN duplicates       (time O(n), space O(n))
```

**After the pseudo code, always say:**
1. The time and space complexity.
2. One edge case (empty input, duplicates, null).
3. How you would test it with a small example.

---

## 15. Interview day strategy

### Before the round
- Set up a Vite React project and an empty JS file so you can start coding fast.
- Have your resume open. Be able to explain every line.
- Test your internet, camera and screen share if it is online.

### During theory questions
- Start with a simple one-line definition, then give a small example. Do not give a long lecture.
- If you are not sure, say what you know and be honest about the rest.
- Connect answers to your projects ("In my healthcare project I used...").
- It is fine to ask the interviewer to repeat or clarify.

### During live coding
1. Repeat the problem and clarify (data shape, edge cases).
2. Explain your plan in two or three sentences.
3. Write the simplest working version first.
4. Name variables clearly and keep functions small.
5. Handle loading, error and empty states.
6. Test with an example out loud.
7. Mention improvements: accessibility, performance, TypeScript types, tests.

### Common mistakes to avoid
- Starting to code without a plan.
- Mutating state directly.
- Forgetting `key` in lists, or using index as key.
- Missing the dependency array or cleanup in `useEffect`.
- Not handling errors and loading.
- Staying silent while thinking. Think aloud.
- Saying "I know everything." Say what you know and your learning approach.

### Questions you can ask the interviewer
- What does the tech stack and architecture of the project look like?
- How is the team structured and how are code reviews done?
- What would the first three months look like for this role?
- What kind of frontend work will I do (new features, performance, design system)?

### Rapid answers for tricky "why" questions
- **Why React and not Angular or Vue?** Large ecosystem, component model, huge community and jobs, and flexibility. Angular is a full framework with more rules, Vue is simpler but has a smaller market.
- **Why TypeScript?** Catches bugs early, better editor support, safer refactoring.
- **Why Tailwind?** Speed, consistency, small CSS output.
- **Why React Query?** Server state, caching and fewer bugs than manual fetching in effects.

---

## 16. Last day revision checklist

**Be able to write from memory (without looking):**
- [ ] Counter, Todo list, Fetch list with loading and error
- [ ] Controlled form with validation
- [ ] Search with debounce (`useDebounce`)
- [ ] Accordion, Tabs, Modal
- [ ] Pagination formula
- [ ] `useFetch`, `useLocalStorage`
- [ ] ProtectedRoute with roles
- [ ] debounce, throttle, map polyfill, Promise.all
- [ ] Two Sum, valid parentheses, group by, flatten

**Be able to explain in 30 seconds each:**
- [ ] Closure, event loop, hoisting, `this`, promises vs async/await
- [ ] Virtual DOM, reconciliation, keys
- [ ] Props vs state, controlled vs uncontrolled
- [ ] useEffect (deps, cleanup), useMemo vs useCallback vs React.memo
- [ ] Context vs Redux vs React Query
- [ ] CSR vs SSR vs SSG
- [ ] Box model, flex vs grid, specificity, position
- [ ] Where to store JWT and why
- [ ] 5 ways to improve React performance

**Be ready to talk about your projects:**
- [ ] eAsha healthcare: roles, Razorpay, WebSockets, Jitsi, performance
- [ ] AI document extraction: upload flow, review screen, status handling
- [ ] Agri-Tech ERP: hierarchy data, GIS map, large data
- [ ] Secure chat app: real-time, roles, encryption basics

---

**Final advice:** The interviewers hiring for 6 months to 1 year experience want clear fundamentals, clean working code, and honest communication. You do not need to know everything. Show that you understand how things work, you can write code calmly, and you learn quickly. Good luck!
