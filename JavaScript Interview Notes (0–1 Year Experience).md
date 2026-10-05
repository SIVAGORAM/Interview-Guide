# JavaScript Interview Notes (0–1 Year Experience)

Simple notes for a Full Stack Developer interview. Each topic has: **What it is**, **Why we use it**, a **small example**, and **What to say in the interview**.

---

# TIER 1 — MUST MASTER

## 1. var / let / const

**What:** Three ways to declare variables.

|  | var | let | const |
| --- | --- | --- | --- |
| Scope | Function | Block `{}` | Block `{}` |
| Re-declare | Yes | No | No |
| Re-assign | Yes | Yes | No |
| Hoisted | Yes (value `undefined`) | Yes (but in TDZ) | Yes (but in TDZ) |

```js
var a = 1;   // old way, avoid
let b = 2;   // value can change
const c = 3; // value cannot be re-assigned
```

**Important:** `const` does not make an object frozen. You cannot re-assign it, but you can change what is inside.

```js
const user = { name: "Siva" };
user.name = "Ravi"; // allowed
user = {};          // error
```

**Say in interview:** "I use `const` by default, `let` only when the value must change, and I avoid `var` because it is function-scoped and causes bugs."

---

## 2. Data Types

**Primitive (stored by value):** `string`, `number`, `boolean`, `undefined`, `null`, `symbol`, `bigint`.

**Non-primitive (stored by reference):** `object` (this includes arrays, functions, dates).

```js
typeof "hi"      // "string"
typeof 10        // "number"
typeof undefined // "undefined"
typeof null      // "object"  <- famous JS bug
typeof []        // "object"
Array.isArray([]) // true
```

- `undefined` = variable declared but no value given.
- `null` = we intentionally set "no value".
- Primitives are copied by value. Objects are copied by reference.

```js
let a = 5; let b = a; b = 10; // a is still 5
let x = {n:1}; let y = x; y.n = 2; // x.n is also 2
```

**Say in interview:** "Primitives are immutable and copied by value; objects are copied by reference, so two variables can point to the same object."

---

## 3. == vs ===

- `==` (loose) compares value **after type conversion**.
- `===` (strict) compares value **and type**. No conversion.

```js
5 == "5"   // true
5 === "5"  // false
0 == false // true
0 === false // false
null == undefined  // true
null === undefined // false
NaN === NaN // false (use Number.isNaN())
```

**Say in interview:** "I always use `===` to avoid unexpected type conversion bugs."

---

## 4. Functions

**What:** A reusable block of code.

```js
// Declaration (hoisted)
function add(a, b) { return a + b; }

// Expression (not hoisted)
const add2 = function (a, b) { return a + b; };

// Arrow function
const add3 = (a, b) => a + b;
```

**Key points:**

- Functions are **first-class**: you can store them in variables, pass them as arguments, and return them.
- **Callback** = a function passed into another function.
- **Higher-order function** = a function that takes or returns another function (`map`, `filter`).
- **Arrow function differences:** no own `this`, no `arguments` object, cannot be used with `new`.
- **Default parameters:** `function greet(name = "Guest") {}`
- **Rest parameters:** `function sum(...nums) {}` collects all arguments in an array.

**Say in interview:** "Functions are first-class in JavaScript, which is why callbacks and higher-order functions work."

---

## 5. Scope

**What:** Where a variable can be accessed.

- **Global scope:** accessible everywhere.
- **Function scope:** accessible only inside the function.
- **Block scope:** (`let`/`const`) accessible only inside `{ }`.
- **Lexical scope:** an inner function can access variables of its outer function, based on where it is *written*.
- **Scope chain:** JS looks for a variable in the current scope, then the parent, then up to global.

```js
const a = "global";
function outer() {
  const b = "outer";
  function inner() {
    const c = "inner";
    console.log(a, b, c); // can access all three
  }
  inner();
}
```

**Say in interview:** "JavaScript uses lexical scoping; inner functions can read outer variables through the scope chain, but not the other way around."

---

## 6. Hoisting

**What:** Before running code, JS moves **declarations** to the top of their scope. Only the declaration moves, not the value.

```js
console.log(x); // undefined (not an error)
var x = 5;

sayHi(); // works
function sayHi() { console.log("hi"); }

sayBye(); // error: not a function (var) / not initialized (let/const)
var sayBye = function () {};
```

| Declaration | Hoisted? | Usable before line? |
| --- | --- | --- |
| `var` | Yes | Yes, value is `undefined` |
| `let` / `const` | Yes | No (TDZ error) |
| Function declaration | Yes (fully) | Yes |
| Function expression / arrow | Only the variable | No |

**Say in interview:** "Hoisting means declarations are processed first during the creation phase of the execution context."

---

## 7. TDZ (Temporal Dead Zone)

**What:** The time between the start of a scope and the line where a `let`/`const` is declared. Accessing the variable in this zone throws a `ReferenceError`.

```js
console.log(a); // ReferenceError (TDZ)
let a = 10;
console.log(a); // 10
```

**Why it exists:** It catches bugs by forcing you to declare a variable before using it.

**Say in interview:** "`let` and `const` are hoisted but not initialized, so they sit in the TDZ until the declaration line runs."

---

## 8. Closures

**What:** A function that **remembers the variables of its outer function**, even after the outer function has finished running.

```js
function counter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}
const c = counter();
c(); // 1
c(); // 2
```

`count` is private; nobody outside can change it directly.

**Why we use it:**

- Data privacy (private variables)
- Function factories
- Keeping state (counters, `once`, memoization)
- Debounce / throttle use closures internally

**Classic trap (var in loop):**

```js
for (var i = 0; i < 3; i++) setTimeout(() => console.log(i)); // 3 3 3
for (let i = 0; i < 3; i++) setTimeout(() => console.log(i)); // 0 1 2
```

Reason: `var` has one shared `i`; `let` creates a new `i` for each loop round.

**Say in interview:** "A closure is a function bundled with its lexical environment. I use it for private state, like a counter or inside a debounce function."

---

## 9. `this`

**What:** A keyword that refers to **who is calling the function**. Its value depends on *how* the function is called.

| Situation | `this` is |
| --- | --- |
| Global / normal function | `window` (or `undefined` in strict mode) |
| Method of an object | The object before the dot |
| Arrow function | Taken from the surrounding (outer) scope |
| `new` constructor | The new object being created |
| `call/apply/bind` | Whatever you pass |

```js
const user = {
  name: "Siva",
  regular() { console.log(this.name); }, // "Siva"
  arrow: () => console.log(this.name),   // undefined (outer this)
};
```

**Common problem:** losing `this` when passing a method as a callback.

```js
const fn = user.regular;
fn(); // this is lost
const fixed = user.regular.bind(user); // fix
```

**Say in interview:** "`this` is decided at call time, except in arrow functions, where it is decided by where they are written."

---

## 10. call / apply / bind

**What:** Three methods to **manually set `this`** for a function.

```js
function intro(city, role) {
  console.log(this.name + " - " + city + " - " + role);
}
const p = { name: "Siva" };

intro.call(p, "Hyderabad", "Dev");    // runs now, args one by one
intro.apply(p, ["Hyderabad", "Dev"]); // runs now, args in an array
const f = intro.bind(p, "Hyderabad"); // returns a NEW function, runs later
f("Dev");
```

**Memory trick:** **C**all = **C**omma separated, **A**pply = **A**rray, **B**ind = **B**orrow and run **B**ack later.

**Why we use it:** borrow methods, fix `this` in callbacks (React class components, event handlers).

---

## 11. Arrays & Objects

**Arrays (ordered list):**

```js
const arr = [1, 2, 3];
arr.push(4);      // add at end
arr.pop();        // remove from end
arr.unshift(0);   // add at start
arr.shift();      // remove from start
arr.slice(1, 3);  // copy part (does NOT change original)
arr.splice(1, 1); // remove/insert (CHANGES original)
arr.includes(2);  // true
arr.indexOf(2);   // position
arr.find(x => x > 1);  // first match
arr.some(x => x > 2);  // any match? true/false
arr.every(x => x > 0); // all match?
arr.sort((a, b) => a - b); // number sort
arr.concat([5]); arr.join("-"); arr.reverse();
```

**Objects (key-value pairs):**

```js
const obj = { name: "Siva", age: 24 };
obj.name; obj["age"];        // access
obj.city = "HYD";            // add
delete obj.age;              // remove
"name" in obj;               // check key
Object.keys(obj); Object.values(obj); Object.entries(obj);
Object.assign({}, obj);      // copy
Object.freeze(obj);          // make read-only
```

**Say in interview:** "`slice` is safe because it returns a new array, while `splice` and `sort` mutate the original."

---

## 12. map / filter / forEach / reduce

| Method | Returns | Use when |
| --- | --- | --- |
| `forEach` | nothing | You just want to do something for each item |
| `map` | new array (same length) | Transform each item |
| `filter` | new array (fewer items) | Keep items that pass a test |
| `reduce` | a single value | Combine all items into one |

```js
const nums = [1, 2, 3, 4];
nums.forEach(n => console.log(n));
nums.map(n => n * 2);              // [2, 4, 6, 8]
nums.filter(n => n % 2 === 0);     // [2, 4]
nums.reduce((sum, n) => sum + n, 0); // 10
```

- None of `map/filter/reduce` change the original array.
- `forEach` cannot be stopped with `break`, and it returns `undefined`.
- Always give `reduce` an initial value (the `0` above).
- They can be **chained**: `users.filter(u => u.active).map(u => u.name)`.

**Say in interview:** "In React I use `map` to render lists and `filter` to remove items without mutating state."

---

## 13. Destructuring & Spread

**Destructuring** = pull values out of arrays/objects into variables.

```js
const [a, b] = [1, 2];
const { name, age = 18 } = { name: "Siva" }; // age gets default 18
const { name: userName } = user;              // rename
function show({ name, city }) {}              // in parameters
```

**Spread (`...`)** = expand an array/object.

```js
const arr2 = [...arr1, 4];            // copy + add
const obj2 = { ...obj1, age: 25 };    // copy + override
Math.max(...[1, 5, 3]);               // 5
```

**Rest (`...`)** = collect the remaining items (same symbol, opposite job).

```js
const [first, ...others] = [1, 2, 3]; // others = [2, 3]
```

**Why:** cleaner code, and spread is how we update state immutably in React.

---

## 14. Promises

**What:** An object that represents a value that will be available **in the future** (async result).

**3 states:** `pending` → `fulfilled` (resolved) or `rejected`. Once settled, it cannot change.

```js
const p = new Promise((resolve, reject) => {
  setTimeout(() => resolve("done"), 1000);
});

p.then(result => console.log(result))
 .catch(err => console.log(err))
 .finally(() => console.log("always runs"));
```

**Why:** solves **callback hell** (nested callbacks) and gives cleaner error handling.

**Common methods:**

- `Promise.all([p1, p2])`: waits for all; fails if any one fails.
- `Promise.allSettled([...])`: waits for all; never fails, gives each result.
- `Promise.race([...])`: first one to finish (success or fail).
- `Promise.any([...])`: first one to succeed.

---

## 15. async / await

**What:** Cleaner syntax on top of Promises. Makes async code look like normal step-by-step code.

```js
async function getUser() {
  try {
    const res = await fetch("/api/user");
    if (!res.ok) throw new Error("Request failed");
    return await res.json();
  } catch (err) {
    console.log(err.message);
  } finally {
    console.log("done");
  }
}
```

**Key points:**

- An `async` function **always returns a Promise**.
- `await` pauses only that function, **not the whole program**.
- `fetch` does **not** throw on 404/500, so check `res.ok`.

**Run in parallel (important):**

```js
const a = await getA();
const b = await getB();            // slow: one after another
const [a, b] = await Promise.all([getA(), getB()]); // fast: together
```

**Say in interview:** "If calls are independent, I use `Promise.all` so they run in parallel instead of waiting one by one."

---

## 16. Event Loop

**What:** JavaScript is **single-threaded** (one thing at a time). The event loop is what lets it handle async tasks without blocking.

**Parts:**

1. **Call Stack:** where the code runs, one function at a time.
2. **Web APIs / Node APIs:** handle `setTimeout`, `fetch`, DOM events in the background.
3. **Callback (Macrotask) Queue:** finished `setTimeout`/event callbacks wait here.
4. **Microtask Queue:** finished Promise callbacks (`.then`, `await`) wait here.
5. **Event Loop:** keeps checking: *"Is the call stack empty? If yes, push the next task."*

```js
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
// Output: 1, 4, 3, 2
```

**Why this order?** Sync code first (1, 4). Then microtasks (3). Then macrotasks (2).

**Say in interview:** "Even `setTimeout(fn, 0)` does not run immediately; it waits for the stack to be empty and all microtasks to finish."

---

## 17. Microtask vs Macrotask

|  | Microtask | Macrotask |
| --- | --- | --- |
| Examples | `Promise.then/catch`, `await`, `queueMicrotask` | `setTimeout`, `setInterval`, DOM events, I/O |
| Priority | **Higher** | Lower |

**Rule:** After each macrotask, the engine runs **all** microtasks until the queue is empty, and only then takes the next macrotask.

**Side effect:** an endless chain of microtasks can block the page (starve the macrotasks).

---

## 18. Error Handling

```js
try {
  JSON.parse("bad json");
} catch (err) {
  console.log(err.name, err.message);
} finally {
  console.log("cleanup"); // always runs
}

throw new Error("Something went wrong"); // create your own error
```

- **Promises:** use `.catch()`.
- **async/await:** use `try/catch`.
- `try/catch` does **not** catch errors inside a `setTimeout` callback or an un-awaited Promise.
- Common error types: `ReferenceError`, `TypeError`, `SyntaxError`.
- Custom error: `class AppError extends Error {}`.
- Never swallow errors silently; log them and show a friendly message to the user.

---

## 19. Shallow vs Deep Copy

**Shallow copy:** copies only the first level. Nested objects are still shared (same reference).

```js
const a = { name: "Siva", address: { city: "HYD" } };
const shallow = { ...a };          // also Object.assign, arr.slice()
shallow.address.city = "BLR";
console.log(a.address.city);       // "BLR" (original changed!)
```

**Deep copy:** copies everything, no shared references.

```js
const deep = structuredClone(a);          // modern, best choice
const deep2 = JSON.parse(JSON.stringify(a)); // old way
```

**`JSON` method problem:** loses functions, `undefined`, `Date` (becomes string), `Map/Set`, and fails on circular references. `structuredClone` handles these better (but not functions).

**Say in interview:** "Spread is shallow. For nested data I use `structuredClone`."

---

## 20. Debouncing & Throttling

Both **limit how often a function runs**. Both use closures.

**Debounce:** Run the function only **after the user stops** doing the action for X ms. *Use case:* search box (call API only after typing stops), window resize.

```js
function debounce(fn, delay) {
  let timer;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => fn.apply(this, args), delay);
  };
}
```

**Throttle:** Run the function **at most once every X ms**, even if the event keeps firing. *Use case:* scroll event, button spam, mouse move.

```js
function throttle(fn, limit) {
  let wait = false;
  return function (...args) {
    if (wait) return;
    fn.apply(this, args);
    wait = true;
    setTimeout(() => (wait = false), limit);
  };
}
```

**Easy line:** Debounce = *"wait until they stop."* Throttle = *"once per interval."*

---

# TIER 2 — STRONGLY RECOMMENDED

## 21. Prototypes

**What:** Every JavaScript object has a hidden link to another object called its **prototype**. If a property is not found on the object, JS looks in its prototype.

**Why:** It lets objects **share methods** without copying them. This saves memory.

```js
function Person(name) { this.name = name; }
Person.prototype.sayHi = function () { console.log("Hi " + this.name); };

const p1 = new Person("Siva");
const p2 = new Person("Ravi");
p1.sayHi();                       // works
p1.sayHi === p2.sayHi;            // true (one shared function)
```

- `obj.__proto__` (or `Object.getPrototypeOf(obj)`) points to the prototype.
- `Person.prototype` is the object that **instances will inherit from**.
- JavaScript uses **prototype-based inheritance**, not class-based. (`class` is just cleaner syntax on top.)

---

## 22. Prototype Chain

**What:** When you access `obj.something`, JS searches: the object itself → its prototype → prototype's prototype → … until it reaches `null`. This path is the **prototype chain**.

```js
const arr = [1, 2];
arr.push(3);
// arr -> Array.prototype (has push, map...) -> Object.prototype (has toString...) -> null
```

- If not found anywhere, result is `undefined`.
- `obj.hasOwnProperty("x")` or `Object.hasOwn(obj, "x")` checks only the object's own properties.
- This is how arrays, functions and dates all get their built-in methods.

**Say in interview:** "The prototype chain is how JS looks up properties and implements inheritance. It stops at `Object.prototype`, whose prototype is `null`."

---

## 23. Classes / OOP

**What:** `class` is a cleaner way to write constructor functions and prototypes (syntactic sugar).

```js
class Animal {
  constructor(name) { this.name = name; }
  speak() { console.log(this.name + " makes a sound"); }
  static info() { return "I am a static method"; }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);          // must call before using this
    this.breed = breed;
  }
  speak() { console.log(this.name + " barks"); } // overriding
}

new Dog("Tommy", "Lab").speak(); // Tommy barks
```

**Private fields:** `#balance = 0;` can only be used inside the class. **Getters/setters:** `get fullName() {}` and `set fullName(v) {}`.

**4 OOP pillars (simple words):**

1. **Encapsulation:** keep data and methods together, hide private details.
2. **Abstraction:** show only what is needed, hide complexity.
3. **Inheritance:** a child class reuses a parent class (`extends`).
4. **Polymorphism:** same method name, different behavior (method overriding).

**Say in interview:** "Classes in JS are syntactic sugar over prototypes. Under the hood it is still prototype-based."

---

## 24. DOM (Document Object Model)

**What:** The browser converts the HTML page into a **tree of objects**. JavaScript can read and change this tree to update the page.

```js
// Select
document.getElementById("id");
document.querySelector(".btn");        // first match
document.querySelectorAll("li");       // all matches

// Change
el.textContent = "Hello";              // safe, text only
el.innerHTML = "<b>Hi</b>";            // parses HTML (XSS risk)
el.style.color = "red";
el.classList.add("active");  // also remove, toggle
el.setAttribute("href", "#");

// Create & add
const li = document.createElement("li");
ul.appendChild(li);
li.remove();

// Events
btn.addEventListener("click", () => console.log("clicked"));
```

- `textContent` vs `innerHTML`: use `textContent` for user input to avoid **XSS attacks**.
- Changing the DOM many times is slow. React uses a **Virtual DOM** to reduce this.
- Scripts should run after the DOM loads (`DOMContentLoaded` event, or put `<script>` at the end / use `defer`).

---

## 25. Event Bubbling & Capturing

When you click an element, the event travels in **3 phases**:

1. **Capturing:** from `window` **down** to the target.
2. **Target:** reaches the clicked element.
3. **Bubbling:** goes **back up** from the target to `window`.

By default, listeners run in the **bubbling** phase.

```js
parent.addEventListener("click", () => console.log("parent"));
child.addEventListener("click", () => console.log("child"));
// Click child -> "child" then "parent" (bubbling)

parent.addEventListener("click", fn, true); // true = capture phase
```

**Controls:**

- `event.stopPropagation()`: stops the event from moving further up/down.
- `event.preventDefault()`: stops the browser default action (form submit, link open).
- `event.target` = the element actually clicked. `event.currentTarget` = the element the listener is attached to.

---

## 26. Event Delegation

**What:** Instead of adding a listener to **every child**, add **one listener on the parent** and use bubbling to find which child was clicked.

```js
ul.addEventListener("click", (e) => {
  if (e.target.tagName === "LI") {
    console.log("Clicked:", e.target.textContent);
  }
});
```

**Why we use it:**

- Better performance (1 listener instead of 100).
- Works for **elements added later** (dynamic lists) with no extra code.
- Less memory use, easier cleanup.

**Say in interview:** "Event delegation uses bubbling; one parent listener handles all children, and `event.target` tells me which child was clicked."

---

## 27. LocalStorage / SessionStorage / Cookies

|  | localStorage | sessionStorage | Cookies |
| --- | --- | --- | --- |
| Size | \~5–10 MB | \~5 MB | \~4 KB |
| Lifetime | Until manually cleared | Until the **tab** closes | Until expiry date set |
| Sent to server automatically? | No | No | **Yes**, with every request |
| Accessible by JS | Yes | Yes | Yes (unless `HttpOnly`) |

```js
localStorage.setItem("user", JSON.stringify({ name: "Siva" }));
const user = JSON.parse(localStorage.getItem("user"));
localStorage.removeItem("user");
localStorage.clear();
```

- Storage only saves **strings**, so use `JSON.stringify` / `JSON.parse` for objects.
- **Never store sensitive data** (passwords, tokens if avoidable) in localStorage; any JS (including XSS attacks) can read it.
- **Cookie security flags:** `HttpOnly` (JS cannot read), `Secure` (HTTPS only), `SameSite` (helps against CSRF).
- **Use cases:** localStorage = theme, preferences. sessionStorage = temporary form data. Cookies = login session / auth.

---

## 28. Modules

**What:** Split code into separate files, each with its own scope. You export what you want to share and import where needed.

```js
// math.js
export const add = (a, b) => a + b;     // named export
export default function multiply(a, b) { return a * b; } // default export

// app.js
import multiply, { add } from "./math.js";
import * as math from "./math.js";      // import everything
```

- **Named export:** many per file; import with the same name in `{ }`.
- **Default export:** one per file; import with any name, no `{ }`.
- **ES Modules (ESM):** `import / export`. Used in browsers and modern Node.
- **CommonJS (CJS):** `require() / module.exports`. Older Node style.
- Modules run **once** and are **cached**.
- **Why:** organized code, no global pollution, reusability, easier testing. Bundlers (Vite, Webpack) use this to remove unused code (tree shaking).
- `import()` (dynamic import) loads a module on demand → **lazy loading**.

---

## 29. Map / Set

**Map:** key-value pairs where **keys can be any type** (objects, functions too). Keeps insertion order.

```js
const m = new Map();
m.set("a", 1); m.set(2, "two");
m.get("a"); m.has(2); m.delete(2); m.size;
for (const [k, v] of m) console.log(k, v);
```

**Set:** collection of **unique values only**.

```js
const s = new Set([1, 2, 2, 3]); // {1, 2, 3}
s.add(4); s.has(2); s.delete(1); s.size;
const unique = [...new Set(arr)]; // remove duplicates from array
```

| Object vs Map |
| --- |
| Object keys are only strings/symbols; Map keys can be anything |
| Map has `.size`; object needs `Object.keys(obj).length` |
| Map is better for frequent add/remove |

**Say in interview:** "I use `Set` to remove duplicates and for fast `has()` lookups, and `Map` when keys are not simple strings or when I add and delete often."

---

## 30. Memory Leaks

**What:** Memory that is no longer needed but is **not released**, because something still holds a reference to it. The app slowly becomes slow or crashes.

**Common causes and fixes:**

1. **Forgotten timers:** `setInterval` never cleared → use `clearInterval`.
2. **Event listeners never removed** → use `removeEventListener` (in React, clean up in `useEffect` return).
3. **Accidental global variables** (forgetting `let/const`) → use strict mode.
4. **Closures holding big data** longer than needed.
5. **Detached DOM nodes:** removed from page but still referenced in a variable.
6. **Ever-growing caches/arrays** with no limit → use size limits or `WeakMap`.

```js
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id); // cleanup prevents leak
}, []);
```

**Garbage collection:** JS automatically frees memory of objects that are **unreachable** (uses "mark and sweep").

**How to find leaks:** Chrome DevTools → Memory tab → take heap snapshots and compare.

---

# TIER 3 — IF YOU HAVE EXTRA TIME

## 31. WeakMap / WeakSet

- Like `Map`/`Set`, but keys must be **objects** and they are held **weakly**: if nothing else references the object, it gets garbage collected automatically.
- Not iterable, no `.size`.
- **Use case:** attach private/extra data to an object (like DOM nodes) without causing memory leaks.

```js
const cache = new WeakMap();
let user = { id: 1 };
cache.set(user, "data");
user = null; // entry can now be cleaned up automatically
```

---

## 32. IIFE (Immediately Invoked Function Expression)

A function that **runs immediately** after it is defined.

```js
(function () {
  const secret = "private";
  console.log("runs now");
})();

(() => console.log("arrow IIFE"))();
```

**Why:** creates a private scope (avoids polluting global scope), used before modules existed, and used to run `async` code at top level: `(async () => { await x(); })();`

---

## 33. Recursion

A function that **calls itself** until a stop condition (base case) is reached.

```js
function factorial(n) {
  if (n <= 1) return 1;          // base case (must have!)
  return n * factorial(n - 1);   // recursive case
}
factorial(5); // 120
```

- No base case → **stack overflow** ("Maximum call stack size exceeded").
- Good for: trees, nested objects, folder structures, deep-flatten.
- Slow repeated work (like Fibonacci) can be fixed with **memoization** (cache results, uses closure).

---

## 34. Generators / Iterators

**Iterator:** an object with a `next()` method that returns `{ value, done }`. Arrays, strings, Maps, Sets are **iterable** (work with `for...of`).

**Generator:** a special function (`function*`) that can **pause and resume** using `yield`.

```js
function* count() {
  yield 1;
  yield 2;
  yield 3;
}
const g = count();
g.next(); // { value: 1, done: false }
g.next(); // { value: 2, done: false }
```

**Why:** lazy values (generate only when needed), custom iteration, infinite sequences. `async/await` is built on this idea.

---

## 35. Symbols

A **unique and immutable** primitive value, often used as an object key to avoid name clashes.

```js
const id1 = Symbol("id");
const id2 = Symbol("id");
id1 === id2; // false (always unique)
const obj = { [id1]: 123 };
```

Symbol keys do not show in `for...in` or `Object.keys`. Rare in daily work; just know the definition.

---

## 36. BigInt

For integers **larger than** `Number.MAX_SAFE_INTEGER` (2^53 − 1).

```js
const big = 9007199254740993n;   // add n at the end
BigInt("123456789012345678901234567890");
5n + 3n;      // 8n
5n + 3;       // TypeError (cannot mix with Number)
```

Also know: `0.1 + 0.2 !== 0.3` (floating-point issue; compare with a small tolerance or use integers like paise/cents).

---

## 37. Advanced Promise Patterns

- **Promise chaining:** return a value/Promise from `.then` to pass it to the next `.then`.
- **Sequential vs parallel:** `await` in a `for...of` loop = one by one; `Promise.all(arr.map(...))` = parallel.
- **Timeout pattern:** `Promise.race([fetchData(), timeout(5000)])`.
- **Retry:** wrap the call in a loop with `try/catch` and retry a few times (add a delay).
- **`Promise.allSettled`** when you want results even if some fail.
- **Promisify:** wrap a callback-style function in `new Promise`.
- **Unhandled rejection:** a rejected Promise with no `.catch` → always handle errors.
- `forEach` does **not** wait for `await` inside it; use `for...of` or `Promise.all(map)`.

---

## 38. Advanced Memory / Engine Concepts

- **JS engine:** V8 (Chrome, Node.js) reads code, compiles it (JIT, Just-In-Time) and runs it.
- **Execution context:** the environment where code runs. Has 2 phases: **creation** (hoisting, memory set up) and **execution** (code runs line by line). Each function call makes a new one.
- **Call stack:** stack of execution contexts. Too deep → stack overflow.
- **Memory heap:** where objects and functions are stored. **Stack** stores primitives and references.
- **Garbage collection:** mark-and-sweep removes unreachable objects automatically.
- **Single-threaded + non-blocking** because of the event loop. For heavy work use **Web Workers**.

---

# LAST-MINUTE CHEAT SHEET

## Questions that always come up (answer in 1–2 lines)

| Question | Short answer |
| --- | --- |
| `var` vs `let` vs `const`? | `var` function-scoped, `let/const` block-scoped; `const` cannot be re-assigned |
| What is a closure? | Inner function that remembers outer variables even after the outer function ends |
| What is hoisting? | Declarations are moved to the top of scope before execution |
| `==` vs `===`? | `==` converts types, `===` checks value and type |
| What is `this`? | The object that calls the function; arrow functions use outer `this` |
| `map` vs `forEach`? | `map` returns a new array, `forEach` returns nothing |
| What is a Promise? | An object for a future value with states pending, fulfilled, rejected |
| Explain the event loop | Stack runs sync code; async callbacks wait in queues; microtasks run before macrotasks |
| Shallow vs deep copy? | Shallow copies one level, nested objects are shared; deep copies all levels |
| Debounce vs throttle? | Debounce waits until activity stops; throttle runs once per interval |
| Why is JS single-threaded yet async? | Event loop + browser/Node APIs handle background work |
| `null` vs `undefined`? | `undefined` = not assigned; `null` = intentionally empty |

## Output questions to practice

```js
console.log(typeof null);            // "object"
console.log([] + []);                // "" (empty string)
console.log(0.1 + 0.2 === 0.3);      // false
console.log("5" + 3, "5" - 3);       // "53", 2
console.log([1, 2, 3] == "1,2,3");   // true
console.log(NaN === NaN);            // false

setTimeout(() => console.log("A"), 0);
Promise.resolve().then(() => console.log("B"));
console.log("C");
// C, B, A
```

## How to impress the interviewer

1. **Start with the definition** in one line, then a tiny example, then say where **you used it in a real project**.
2. Connect concepts to real work: "I used `debounce` on the search input to reduce API calls", "I used `Promise.all` to load dashboard data in parallel", "I used `useEffect` cleanup to avoid memory leaks."
3. Mention **trade-offs or gotchas** (e.g. `const` objects are still mutable; `fetch` does not reject on 404). This shows real experience.
4. If you do not know something, say: "I haven't used it in depth, but my understanding is…" and explain what you do know. Never bluff.
5. Think aloud when solving a coding or output question; interviewers value your reasoning.

## 2-day study plan

- **Day 1:** Tier 1 topics 1–10 (morning), 11–20 (afternoon). In the evening, write closure, debounce, `Promise.all` and event-loop output questions **by hand** without looking.
- **Day 2:** Tier 2 (morning). Revise Tier 1 cheat sheet and say each answer out loud (afternoon). Skim Tier 3 definitions only (evening). Sleep well.
