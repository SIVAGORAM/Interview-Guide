# Node.js Interview Preparation Guide (End to End)
### Fundamentals → Core Modules → Async → Express → REST APIs → Database → Auth → Security → Performance → Advanced | Questions + Simple Answers + Short Examples

> **How to use this document**
> - **Part 1–4:** Core Node.js (must know, asked in every interview)
> - **Part 5–8:** Express.js, REST APIs, Database, Authentication (asked in almost every interview)
> - **Part 9–14:** Security, Performance, Testing, Deployment, Advanced topics (asked for deeper rounds)
> - **Part 15–17:** Coding tasks, scenario questions, HR, and cheat sheet (read the day before)
> - Answer format that impresses: **Definition → Why/When we use it → Small example → "In my project I used..."**
> - Where you have not used something, say: *"I haven't used it in depth, but my understanding is..."* Honesty plus a clear idea is respected.

---

## Table of Contents
1. [Node.js Fundamentals](#part-1--nodejs-fundamentals)
2. [Modules, npm and Project Setup](#part-2--modules-npm-and-project-setup)
3. [Core Modules (fs, path, events, streams, buffer, http...)](#part-3--core-modules)
4. [Asynchronous Programming and Event Loop](#part-4--asynchronous-programming-and-event-loop)
5. [Express.js](#part-5--expressjs)
6. [REST API Design](#part-6--rest-api-design)
7. [Databases (MongoDB/Mongoose and SQL)](#part-7--databases)
8. [Authentication and Authorization](#part-8--authentication-and-authorization)
9. [Security](#part-9--security)
10. [Error Handling, Logging and Validation](#part-10--error-handling-logging-and-validation)
11. [Performance, Scaling and Clustering](#part-11--performance-scaling-and-clustering)
12. [Testing and Debugging](#part-12--testing-and-debugging)
13. [Deployment and DevOps Basics](#part-13--deployment-and-devops-basics)
14. [Advanced Topics (WebSockets, GraphQL, Microservices, etc.)](#part-14--advanced-topics)
15. [Coding Tasks](#part-15--coding-tasks)
16. [Scenario-Based and HR Questions](#part-16--scenario-based-and-hr-questions)
17. [Last-Minute Cheat Sheet](#part-17--last-minute-cheat-sheet)

---

# Part 1 — Node.js Fundamentals

### Q1. What is Node.js?
Node.js is an **open-source, cross-platform JavaScript runtime** that lets us run JavaScript **outside the browser** (on a server). It is built on Chrome's **V8 JavaScript engine** and is used to build fast, scalable network applications like APIs, real-time chat apps and microservices.

### Q2. Is Node.js a programming language, framework or library?
None of these. It is a **runtime environment.** The language is JavaScript; Node.js gives it the ability to run on the server with extra APIs (file system, network, etc.).

### Q3. Why use Node.js? What are its advantages?
- **Fast:** V8 engine compiles JS to machine code.
- **Non-blocking, event-driven I/O:** handles many requests at the same time.
- **Single language** (JavaScript) for frontend and backend.
- **npm:** the largest package ecosystem.
- **Good for real-time apps** (chat, notifications, streaming) and **REST APIs.**
- Easy to scale, large community.

### Q4. What are the disadvantages / when should we NOT use Node.js?
Not good for **CPU-heavy tasks** (video encoding, heavy calculations, image processing) because they block the single main thread. For such work, use **worker threads**, child processes, or another language/service.

### Q5. What is the architecture of Node.js?
Node.js uses a **single-threaded, event-driven, non-blocking I/O model.**
Main parts:
- **V8 engine:** runs JavaScript code.
- **libuv:** C library that provides the **event loop**, **thread pool**, and handles async I/O (files, network, DNS).
- **Node APIs (core modules):** fs, http, path, etc.
- **Bindings (C++):** connect JavaScript with C/C++ libraries.

### Q6. What is the V8 engine?
Google's open-source JavaScript engine written in C++. It **compiles JavaScript directly into machine code** (JIT compilation) so it runs fast. Chrome also uses it.

### Q7. What is libuv?
A C library used by Node.js that provides the **event loop**, a **thread pool** (default 4 threads), and cross-platform async I/O. It is the reason Node can do non-blocking operations.

### Q8. How is Node.js different from a browser's JavaScript?
| Browser JS | Node.js |
|---|---|
| Has `window`, `document`, DOM | No DOM; has `global`, `process` |
| Used for UI | Used for servers/tools |
| Can't access file system directly | Can read/write files (`fs`) |
| ES modules | CommonJS + ES modules |

### Q9. What is the difference between Node.js and traditional servers like PHP/Apache?
Traditional servers create **a new thread per request** (uses more memory, blocking). Node.js uses **one thread with an event loop** and handles many requests asynchronously, so it uses fewer resources for I/O-heavy workloads.

### Q10. What is blocking vs non-blocking code?
- **Blocking:** the next line waits until the current operation finishes (`fs.readFileSync`).
- **Non-blocking:** the operation runs in the background; the program continues, and a callback/promise handles the result later (`fs.readFile`).
```js
const fs = require("fs");
// Blocking
const data = fs.readFileSync("a.txt", "utf8");
console.log(data);
// Non-blocking
fs.readFile("a.txt", "utf8", (err, data) => console.log(data));
console.log("This prints first");
```

### Q11. What does "single-threaded" mean in Node.js? Then how does it handle many requests?
Your JavaScript code runs on **one main thread.** But for I/O work (file, network, DB), Node hands the task to the **OS/libuv thread pool** and continues. When done, the callback is queued and the **event loop** runs it. So it handles thousands of concurrent connections without creating a thread for each.

### Q12. What is the `process` object?
A **global object** giving information about and control over the current Node.js process.
```js
process.argv        // command-line arguments
process.env         // environment variables
process.cwd()       // current working directory
process.pid         // process id
process.exit(0)     // exit the program
process.on("uncaughtException", fn)
process.memoryUsage()
process.nextTick(fn)
```

### Q13. What are global objects in Node.js?
Available everywhere without importing: `global`, `process`, `console`, `Buffer`, `setTimeout`, `setInterval`, `setImmediate`, `clearTimeout`, `__dirname`, `__filename` (CommonJS only), `require`, `module`, `exports` (CommonJS). In newer Node versions, `fetch`, `URL`, `AbortController`, `structuredClone` are also global.

### Q14. What are `__dirname` and `__filename`?
`__dirname` = the **folder path** of the current file. `__filename` = the **full path** of the current file. (Not available in ES modules. Use `import.meta.url` with `fileURLToPath`, or `import.meta.dirname` in recent Node versions.)

### Q15. What is the REPL?
**Read–Eval–Print Loop.** An interactive shell started by typing `node` in the terminal to test JS code quickly.

### Q16. What is LTS?
**Long-Term Support.** Even-numbered Node.js versions (18, 20, 22) get LTS (stable, security fixes for a long time). Use LTS in production.

### Q17. How do you check the Node.js and npm version?
`node -v` and `npm -v`.

### Q18. How do you run a Node.js file?
`node app.js`. For development with auto-restart: `nodemon app.js` or `node --watch app.js` (built-in in newer versions).

### Q19. What is the difference between Node.js and Express.js?
Node.js is the **runtime.** Express is a **web framework built on top of Node** that makes routing, middleware and API creation much easier and shorter than using the raw `http` module.

### Q20. What are some popular Node.js frameworks?
Express (most popular, minimal), **NestJS** (structured, TypeScript), Fastify (very fast), Koa, Hapi.

### Q21. Create a basic server using Node.js (without Express).
```js
const http = require("http");

const server = http.createServer((req, res) => {
  if (req.url === "/" && req.method === "GET") {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello from Node.js");
  } else {
    res.writeHead(404);
    res.end("Not Found");
  }
});

server.listen(3000, () => console.log("Server running on port 3000"));
```

---

# Part 2 — Modules, npm and Project Setup

### Q22. What is a module in Node.js?
A module is a **separate file (or package) that contains code you can reuse** in other files. It helps keep code organized and avoids one giant file.

### Q23. Types of modules?
1. **Core (built-in) modules:** fs, http, path, os, events... (no install needed)
2. **Local (custom) modules:** your own files (`./utils.js`)
3. **Third-party modules:** installed from npm (express, mongoose)

### Q24. What is CommonJS vs ES Modules?
| CommonJS (CJS) | ES Modules (ESM) |
|---|---|
| `require()` / `module.exports` | `import` / `export` |
| Default in Node (`.js` without type) | Use `"type": "module"` in package.json or `.mjs` |
| Loads **synchronously** | Loads **asynchronously**, supports top-level `await` |
| `__dirname` available | Not available |
```js
// CommonJS
const express = require("express");
module.exports = { add };

// ES Modules
import express from "express";
export const add = (a, b) => a + b;
export default app;
```

### Q25. What is the difference between `module.exports` and `exports`?
`exports` is just a **reference** to `module.exports`. If you re-assign `exports = ...`, the link breaks and nothing is exported. So to export a single function/class/object, use **`module.exports = ...`**. To add properties, both work.

### Q26. How does `require()` work? What is module caching?
When you `require` a file: Node **resolves** the path, **loads** the file, **wraps** it in a function, **executes** it, and **caches** the result. The next `require` of the same file returns the **cached** exports (the file does not run again).

### Q27. What is the module wrapper function?
Node wraps every module in a function so variables stay private to that file:
`(function (exports, require, module, __filename, __dirname) { /* your code */ })`

### Q28. What is `npm`? What is `npx`?
**npm** = Node Package Manager: installs and manages packages (and is the registry of packages). **npx** runs a package command **without installing it globally** (`npx create-react-app my-app`).

### Q29. What is `package.json`? Important fields?
The **manifest file** of the project: `name`, `version`, `main` (entry file), `scripts` (commands like `start`, `dev`, `test`), `dependencies`, `devDependencies`, `engines`, `type`.
Create with `npm init -y`.

### Q30. What is `package-lock.json`?
Locks the **exact versions** of all installed packages (including nested dependencies), so every developer and server installs the same versions. **Commit it to Git.**

### Q31. dependencies vs devDependencies vs peerDependencies?
- `dependencies`: needed to **run** the app in production (express, mongoose)
- `devDependencies`: only for **development/testing** (nodemon, jest, eslint)
- `peerDependencies`: the package expects the **host project** to provide it (common in plugins/libraries)
Install: `npm i express` / `npm i -D nodemon`.

### Q32. What is semantic versioning (semver)? What do `^` and `~` mean?
Version = **MAJOR.MINOR.PATCH** (e.g., 4.18.2). MAJOR = breaking change, MINOR = new feature (backward compatible), PATCH = bug fix.
- `^4.18.2` → allows updates to **minor and patch** (up to <5.0.0)
- `~4.18.2` → allows only **patch** updates (up to <4.19.0)

### Q33. `npm install` vs `npm ci`?
`npm install` can update the lock file. `npm ci` installs **exactly** from `package-lock.json`, deletes `node_modules` first, and is faster and more reliable for CI/CD and production builds.

### Q34. What are npm scripts?
Custom commands in `package.json`:
```json
"scripts": { "start": "node server.js", "dev": "nodemon server.js", "test": "jest" }
```
Run: `npm start`, `npm run dev`, `npm test`.

### Q35. What does `node_modules` contain? Should we commit it?
It contains installed packages. **Never commit it.** Add it to `.gitignore`; anyone can recreate it with `npm install`.

### Q36. How do you update or remove a package?
`npm update <pkg>`, `npm uninstall <pkg>`, `npm outdated` (check old packages), `npm audit` / `npm audit fix` (security vulnerabilities).

### Q37. What is the difference between local and global install?
Local (`npm i pkg`) → installed in the project's `node_modules`. Global (`npm i -g pkg`) → installed system-wide for CLI tools (nodemon, pm2).

### Q38. What is `.env` and `dotenv`?
`.env` stores **environment variables** (DB URL, secrets, port) outside the code. `dotenv` loads them into `process.env`. Newer Node versions can also use `node --env-file=.env app.js`. **Never commit `.env`.**
```js
require("dotenv").config();
const port = process.env.PORT || 3000;
```

### Q39. What is a recommended folder structure for a Node/Express project?
```
project/
 ├─ src/
 │   ├─ config/        (db connection, env config)
 │   ├─ controllers/   (request handling logic)
 │   ├─ routes/        (route definitions)
 │   ├─ models/        (database schemas)
 │   ├─ middlewares/   (auth, error handler, validation)
 │   ├─ services/      (business logic)
 │   ├─ utils/         (helpers)
 │   └─ app.js, server.js
 ├─ tests/
 ├─ .env, .gitignore, package.json
```

### Q40. What is the difference between `app.js` and `server.js` separation?
`app.js` creates and configures the Express app (middleware, routes). `server.js` starts listening (`app.listen`). This separation makes **testing easier** (import `app` without starting a server).

---

# Part 3 — Core Modules

### Q41. Name the important core modules.
`fs`, `path`, `http`/`https`, `events`, `os`, `url`, `util`, `stream`, `buffer`, `crypto`, `zlib`, `child_process`, `cluster`, `worker_threads`, `readline`, `assert`, `dns`, `net`, `timers`.

### Q42. `fs` module — how to read, write, append, delete files?
```js
const fs = require("fs");
const fsp = require("fs/promises");

// Callback style
fs.readFile("a.txt", "utf8", (err, data) => {});
fs.writeFile("a.txt", "Hello", (err) => {});
fs.appendFile("a.txt", "\nMore", (err) => {});
fs.unlink("a.txt", (err) => {});         // delete
fs.mkdir("folder", { recursive: true }, cb);
fs.readdir(".", cb);                      // list folder
fs.existsSync("a.txt");

// Promise style (preferred)
const data = await fsp.readFile("a.txt", "utf8");
await fsp.writeFile("a.txt", "Hello");
```

### Q43. Sync vs async `fs` methods — which to use?
Use **async/promise** methods in servers (they don't block the event loop). Sync methods (`readFileSync`) are fine only at **startup** (reading config) or in scripts.

### Q44. What is the `path` module? Why use it?
Handles file paths **safely across operating systems** (Windows `\` vs Linux `/`).
```js
const path = require("path");
path.join(__dirname, "uploads", "a.png");  // joins parts
path.resolve("a.txt");                      // absolute path
path.basename("/x/y/a.txt");                // "a.txt"
path.extname("a.txt");                      // ".txt"
path.dirname("/x/y/a.txt");                 // "/x/y"
```

### Q45. What is the `os` module?
Gives info about the operating system: `os.platform()`, `os.cpus()`, `os.totalmem()`, `os.freemem()`, `os.hostname()`, `os.homedir()`.

### Q46. What is the `events` module / EventEmitter?
Node is event-driven. **EventEmitter** lets you create and listen to custom events (observer pattern).
```js
const EventEmitter = require("events");
const emitter = new EventEmitter();

emitter.on("greet", (name) => console.log("Hello " + name));
emitter.once("start", () => console.log("only once"));
emitter.emit("greet", "Ravi");   // Hello Ravi
```
Key methods: `on`, `once`, `emit`, `off`/`removeListener`, `removeAllListeners`. Many Node objects (streams, http server) are EventEmitters.

### Q47. What happens if an `error` event is emitted and there is no listener?
Node **throws the error and crashes** the process. Always add `emitter.on("error", handler)`.

### Q48. What are streams? Why use them?
Streams process data **in small chunks** instead of loading everything in memory. Good for large files, video, network data. They are memory-efficient and faster to start.

### Q49. Types of streams?
1. **Readable** (read data: `fs.createReadStream`, `req`)
2. **Writable** (write data: `fs.createWriteStream`, `res`)
3. **Duplex** (both: TCP socket)
4. **Transform** (modify while passing: `zlib.createGzip()`)

### Q50. How do you copy a large file using streams?
```js
const fs = require("fs");
const { pipeline } = require("stream");

pipeline(
  fs.createReadStream("big.mp4"),
  fs.createWriteStream("copy.mp4"),
  (err) => { if (err) console.error(err); else console.log("Done"); }
);
```
`pipeline` also handles errors and cleanup properly (better than plain `.pipe()`).

### Q51. What is backpressure in streams?
When the writable side is **slower** than the readable side, data piles up in memory. Backpressure is the mechanism to **pause the reader until the writer catches up.** `pipe()` / `pipeline()` handle it automatically.

### Q52. What is a Buffer?
A Buffer is a **fixed-size chunk of memory for handling raw binary data** (files, images, TCP data). Streams use buffers internally.
```js
const buf = Buffer.from("Hello");
console.log(buf);              // <Buffer 48 65 6c 6c 6f>
console.log(buf.toString());   // "Hello"
Buffer.alloc(10);              // 10 zero-filled bytes
```

### Q53. What is the `util` module? What is `util.promisify`?
`util.promisify` converts a **callback-style function into a promise-returning function.**
```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);
const data = await readFile("a.txt", "utf8");
```

### Q54. What is the `crypto` module?
Provides cryptography: hashing, random bytes, encryption.
```js
const crypto = require("crypto");
crypto.createHash("sha256").update("text").digest("hex");
crypto.randomBytes(16).toString("hex");
crypto.randomUUID();
```
(For passwords, use **bcrypt**, not plain SHA.)

### Q55. What is the `url` module / `URL` class?
```js
const u = new URL("https://site.com/products?id=5&sort=asc");
u.pathname;                    // "/products"
u.searchParams.get("id");      // "5"
```

### Q56. What is the `zlib` module?
Compresses/decompresses data (gzip, deflate), often used with streams: `fs.createReadStream("a.txt").pipe(zlib.createGzip()).pipe(fs.createWriteStream("a.txt.gz"))`.

### Q57. What is the `readline` module?
Reads input from the terminal line by line (CLI programs) or reads a large file line by line.

### Q58. What is `child_process`? Methods?
Lets Node run **other programs/scripts as separate processes.**
- `exec` – runs a shell command, buffers output
- `spawn` – streams output (good for large output)
- `execFile` – runs a file directly without a shell
- `fork` – spawns a **new Node.js process** with a built-in message channel (IPC)

### Q59. `child_process` vs `worker_threads` vs `cluster`?
| | What it does | Use for |
|---|---|---|
| **child_process** | Separate OS process (any program) | Running external commands/scripts |
| **worker_threads** | Extra **threads** in the same process, can share memory | **CPU-heavy** JS tasks |
| **cluster** | Multiple copies of the same Node app sharing a port | Using **all CPU cores** for a web server |

### Q60. What is the `http` module? What are `req` and `res`?
`http.createServer((req, res) => {})`.
- `req` (IncomingMessage): `req.url`, `req.method`, `req.headers`, body stream
- `res` (ServerResponse): `res.writeHead()`, `res.write()`, `res.end()`, `res.setHeader()`

### Q61. `http` vs `https`?
`https` serves over **TLS/SSL** (encrypted) and needs a certificate. In production, usually **Nginx or a load balancer handles HTTPS** and forwards to Node.

### Q62. What is `fetch` in Node?
Newer Node versions (18+) include the global `fetch()`, same as in the browser. Older versions used `node-fetch` or `axios`.

### Q63. What is the `assert` / built-in test runner?
`assert` checks conditions in tests. Node 18+ also has a built-in test runner: `node:test` with `node --test`.

---

# Part 4 — Asynchronous Programming and Event Loop

### Q64. What are the ways to handle async code in Node.js?
1. **Callbacks** (oldest)
2. **Promises** (`.then/.catch`)
3. **async/await** (cleanest, built on promises)
4. Events/Streams

### Q65. What is a callback? What is callback hell?
A callback is a function passed to another function to be **called after a task finishes.** **Callback hell** is deeply nested callbacks that make code hard to read and maintain (the "pyramid of doom"). Solved using promises and async/await.
```js
getUser(1, (err, user) => {
  getOrders(user.id, (err, orders) => {
    getItems(orders[0].id, (err, items) => { /* ...nested... */ });
  });
});
```

### Q66. What is the error-first callback pattern?
In Node, callbacks have **error as the first argument**: `(err, data) => {}`. Always check `if (err)` first.

### Q67. What is a Promise? States? Methods?
An object representing the **future result** of an async operation. States: **pending → fulfilled / rejected.**
Methods: `.then()`, `.catch()`, `.finally()`.
Static methods:
- `Promise.all([...])` → waits for **all**; fails if **any** fails
- `Promise.allSettled([...])` → waits for all; gives each result (success or failure)
- `Promise.race([...])` → first one to **settle** (resolve or reject)
- `Promise.any([...])` → first **successful** one

### Q68. What is async/await? Error handling with it?
`async` function always returns a promise; `await` waits for a promise. Use **try/catch.**
```js
const getUser = async (id) => {
  try {
    const user = await User.findById(id);
    return user;
  } catch (err) {
    throw err;
  }
};
```

### Q69. Sequential vs parallel execution with async/await?
```js
// Sequential (slow): one after another
const a = await getA();
const b = await getB();

// Parallel (fast): both start together
const [a, b] = await Promise.all([getA(), getB()]);
```
Use `Promise.all` when tasks are **independent.**

### Q70. Does `await` inside `forEach` work?
No. `forEach` doesn't wait for async callbacks. Use a **`for...of`** loop (sequential) or **`Promise.all(arr.map(async ...))`** (parallel).

### Q71. Explain the Node.js event loop. (VERY IMPORTANT)
The event loop is what lets Node do non-blocking work on a single thread. It **continuously checks if the call stack is empty** and then moves queued callbacks to the stack.

**Phases (in order, repeated):**
1. **Timers** – runs `setTimeout` / `setInterval` callbacks that are due
2. **Pending callbacks** – some system-level callbacks
3. **Idle, prepare** – internal use
4. **Poll** – retrieves new I/O events and runs I/O callbacks (files, network)
5. **Check** – runs `setImmediate` callbacks
6. **Close callbacks** – e.g., `socket.on("close")`

**Between each phase**, Node runs the **microtask queues**: first `process.nextTick` queue, then **Promise** microtasks.

### Q72. What is the order: `process.nextTick`, Promise, `setTimeout`, `setImmediate`?
```js
console.log("1 start");
setTimeout(() => console.log("5 timeout"), 0);
setImmediate(() => console.log("6 immediate"));
Promise.resolve().then(() => console.log("4 promise"));
process.nextTick(() => console.log("3 nextTick"));
console.log("2 end");
// Order: 1, 2, 3 (nextTick), 4 (promise), 5/6 (timeout/immediate)
```
Order: **sync code → nextTick → promises → timers → check (setImmediate)**. (The relative order of `setTimeout 0` and `setImmediate` from the main module can vary; inside an I/O callback `setImmediate` is always first.)

### Q73. `setTimeout` vs `setImmediate` vs `process.nextTick`?
- `process.nextTick` – runs **right after the current operation**, before the event loop continues (highest priority; overusing it can starve I/O).
- `setImmediate` – runs in the **check phase**, after the poll phase.
- `setTimeout(fn, 0)` – runs in the **timers phase** after at least the minimum delay.

### Q74. What is the call stack, callback queue (task queue) and microtask queue?
- **Call stack:** where functions are executed (LIFO).
- **Callback/macrotask queue:** timers, I/O callbacks waiting to run.
- **Microtask queue:** promise callbacks and `nextTick`; they run **before** the next macrotask.

### Q75. What is the thread pool in Node.js? What uses it?
libuv has a pool of **4 threads by default** (`UV_THREADPOOL_SIZE`, up to 1024). It handles **file system operations, DNS lookup (`dns.lookup`), crypto (pbkdf2, bcrypt, scrypt), and zlib compression.** Network I/O uses the OS async mechanism (epoll/kqueue), not the pool.

### Q76. How do you handle CPU-intensive tasks in Node.js?
Don't run them on the main thread. Options: **Worker Threads**, **child processes**, **cluster**, **offload to a queue/background job** (BullMQ, RabbitMQ) or a separate microservice.

### Q77. What are Worker Threads? Example?
Run JavaScript on **separate threads** within the same process, good for heavy computation.
```js
const { Worker, isMainThread, parentPort, workerData } = require("worker_threads");

if (isMainThread) {
  const worker = new Worker(__filename, { workerData: 40 });
  worker.on("message", (result) => console.log("Result:", result));
} else {
  const fib = (n) => (n < 2 ? n : fib(n - 1) + fib(n - 2));
  parentPort.postMessage(fib(workerData));
}
```

### Q78. What is the difference between concurrency and parallelism? Which does Node use?
**Concurrency** = managing many tasks at once (switching between them). **Parallelism** = running tasks at the exact same time on multiple cores. Node.js JS code is **concurrent** (event loop); parallelism comes from the thread pool, worker threads, or cluster.

### Q79. What is an unhandled promise rejection?
A promise is rejected and **no `.catch`** handles it. In modern Node (15+), it **crashes the process** by default. Always handle errors; log with `process.on("unhandledRejection", ...)`.

### Q80. What is a memory leak? Common causes in Node?
Memory that is no longer needed but is never released, so memory usage keeps growing. Causes: **global variables, forgotten timers/intervals, unremoved event listeners, unbounded caches, closures holding big objects, unclosed DB connections/streams.**
Find with: `process.memoryUsage()`, Chrome DevTools heap snapshots (`node --inspect`), `clinic.js`.

---

# Part 5 — Express.js

### Q81. What is Express.js? Why use it?
A **minimal and flexible web framework** for Node.js. It simplifies routing, middleware, request/response handling, and building REST APIs, so you write much less code than with the raw `http` module.

### Q82. Basic Express server?
```js
const express = require("express");
const app = express();

app.use(express.json());   // parse JSON body

app.get("/", (req, res) => res.send("Hello Express"));

app.listen(3000, () => console.log("Server on 3000"));
```

### Q83. What is middleware? (VERY IMPORTANT)
Middleware is a **function that runs between the request and the response.** It has access to `req`, `res`, and `next`. It can modify them, end the request, or call `next()` to pass control to the next middleware.
```js
const logger = (req, res, next) => {
  console.log(req.method, req.url);
  next();   // go to the next middleware/route
};
app.use(logger);
```
**If you don't call `next()` or send a response, the request hangs.**

### Q84. Types of middleware?
1. **Application-level:** `app.use(fn)`
2. **Router-level:** `router.use(fn)`
3. **Built-in:** `express.json()`, `express.urlencoded()`, `express.static()`
4. **Third-party:** `cors`, `helmet`, `morgan`, `cookie-parser`, `multer`
5. **Error-handling:** has **4 arguments** `(err, req, res, next)`

### Q85. What is the order of middleware? Why does it matter?
Middleware runs **in the order it is written.** Put body parsers, CORS and logging **before** routes, and the **error handler at the end.**

### Q86. What is routing in Express?
Matching an HTTP method + URL path to a handler function.
```js
app.get("/users", getUsers);
app.post("/users", createUser);
app.put("/users/:id", updateUser);
app.delete("/users/:id", deleteUser);
app.all("/secret", handler);   // all methods
```

### Q87. `req.params` vs `req.query` vs `req.body`?
```js
// URL: /users/5?role=admin   with body { "name": "Ravi" }
req.params.id    // "5"        → route parameter (/users/:id)
req.query.role   // "admin"    → query string (?role=admin)
req.body.name    // "Ravi"     → request body (needs express.json())
```
Also: `req.headers`, `req.cookies` (with cookie-parser), `req.method`, `req.path`, `req.ip`.

### Q88. Common `res` methods?
```js
res.send("text or object");
res.json({ message: "ok" });
res.status(201).json({ id: 1 });
res.sendStatus(204);
res.redirect("/login");
res.cookie("token", value, { httpOnly: true });
res.sendFile(path.join(__dirname, "index.html"));
res.download("file.pdf");
res.set("X-Custom", "value");
```

### Q89. `res.send()` vs `res.json()` vs `res.end()`?
`res.json()` sends JSON with the correct content-type. `res.send()` sends text/HTML/Buffer/object (auto-detects). `res.end()` ends the response without data (Node's low-level method).

### Q90. What is `express.Router()`? Why use it?
Creates **modular, mountable route handlers** so routes live in separate files.
```js
// routes/user.routes.js
const router = require("express").Router();
router.get("/", getUsers);
router.post("/", createUser);
module.exports = router;

// app.js
app.use("/api/users", require("./routes/user.routes"));
```

### Q91. What is the MVC pattern in Express?
**Model** (database schema), **View** (UI/response), **Controller** (handles request logic). In APIs: **Routes → Controllers → Services → Models.**

### Q92. How do you serve static files?
`app.use(express.static("public"))`. Files in `public/` are served directly (e.g., `/logo.png`).

### Q93. How do you handle errors in Express?
Use an **error-handling middleware** with 4 params, placed **last.**
```js
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.statusCode || 500).json({ success: false, message: err.message || "Server Error" });
});
```
For async routes in Express 4, errors must be passed to `next(err)` (or use a wrapper). **Express 5 handles rejected promises automatically.**

### Q94. How to avoid try/catch in every controller (async wrapper)?
```js
const asyncHandler = (fn) => (req, res, next) =>
  Promise.resolve(fn(req, res, next)).catch(next);

app.get("/users", asyncHandler(async (req, res) => {
  const users = await User.find();
  res.json(users);
}));
```

### Q95. How do you handle 404 (route not found)?
Add a catch-all middleware **after all routes**:
```js
app.use((req, res) => res.status(404).json({ message: "Route not found" }));
```

### Q96. What is CORS? How to enable it in Express?
Cross-Origin Resource Sharing: the browser blocks requests from a different origin unless the server allows it via headers.
```js
const cors = require("cors");
app.use(cors({ origin: "https://myfrontend.com", credentials: true }));
```
It's a **browser** security rule, not a server-to-server rule.

### Q97. What is `body-parser` / `express.json()`?
Parses the request body. `express.json()` for JSON, `express.urlencoded({ extended: true })` for form data. (Built into Express 4.16+; the separate body-parser package isn't needed.)

### Q98. What is `morgan`? `helmet`? `compression`? `cookie-parser`?
- **morgan:** HTTP request logger
- **helmet:** sets secure HTTP headers
- **compression:** gzip responses
- **cookie-parser:** reads cookies into `req.cookies`

### Q99. How do you upload files in Express?
Using **multer** (multipart/form-data).
```js
const multer = require("multer");
const upload = multer({ dest: "uploads/", limits: { fileSize: 2 * 1024 * 1024 } });

app.post("/upload", upload.single("avatar"), (req, res) => {
  res.json({ file: req.file.filename });
});
```
Validate file type/size; for production, store in **S3/Cloudinary** rather than local disk.

### Q100. `app.use()` vs `app.get()`?
`app.use(path, fn)` runs for **all methods** and any path **starting with** that path (used for middleware). `app.get(path, fn)` runs **only for GET** on the **exact** path.

### Q101. What are route parameters validation (`router.param`) and route chaining?
```js
router.route("/users/:id").get(getUser).put(updateUser).delete(deleteUser);
```
`router.route()` chains multiple methods for the same path.

### Q102. What is `next("route")` and `next(err)`?
`next()` → next middleware. `next(err)` → skips to the **error handler.** `next("route")` → skips the remaining handlers of the current route.

### Q103. How do you set up environment-based config (development/production)?
Use `NODE_ENV`. `process.env.NODE_ENV === "production"` to change logging, error detail, etc. Express also optimizes itself in production mode.

### Q104. How do you implement rate limiting?
```js
const rateLimit = require("express-rate-limit");
app.use("/api", rateLimit({ windowMs: 15 * 60 * 1000, max: 100 }));
```
Protects from brute force and abuse.

### Q105. What are cookies and sessions in Express?
**Cookies:** small data stored in the browser. **Sessions:** data stored on the **server** (memory/Redis/DB) with a session ID saved in a cookie (`express-session`). **JWT** is the stateless alternative.

### Q106. What is the difference between Express 4 and Express 5?
Express 5 (stable now) automatically catches **rejected promises/async errors** in handlers, has updated path-matching syntax, and removes some deprecated APIs.

### Q107. What is the difference between `app.listen` and `http.createServer(app)`?
`app.listen()` is a shortcut that internally creates an HTTP server. Use `http.createServer(app)` when you need the server object (e.g., for **Socket.io**).

---

# Part 6 — REST API Design

### Q108. What is an API? What is REST?
**API** = Application Programming Interface: a way for software to talk to each other. **REST** (Representational State Transfer) is an **architectural style** for APIs that uses HTTP methods and URLs to work with **resources** (users, products), usually exchanging **JSON.**

### Q109. What are the REST principles/constraints?
1. **Client–Server** separation
2. **Stateless:** each request has all info needed; the server stores no client session
3. **Cacheable** responses
4. **Uniform interface** (consistent URLs and methods)
5. **Layered system** (proxies, load balancers)
6. (Optional) Code on demand

### Q110. HTTP methods and their use (CRUD)?
| Method | Purpose | Example |
|---|---|---|
| **GET** | Read | `GET /users` |
| **POST** | Create | `POST /users` |
| **PUT** | Replace/update **whole** resource | `PUT /users/5` |
| **PATCH** | Update **part** of resource | `PATCH /users/5` |
| **DELETE** | Delete | `DELETE /users/5` |

### Q111. PUT vs PATCH?
PUT replaces the **entire** resource (send all fields). PATCH changes **only the fields sent.**

### Q112. What are safe and idempotent methods?
- **Safe:** don't change data (GET, HEAD, OPTIONS).
- **Idempotent:** calling many times gives the **same result** as once (GET, PUT, DELETE).
- **POST is not idempotent** (creates a new record each time). PATCH may or may not be.

### Q113. Important HTTP status codes?
- **2xx Success:** 200 OK, 201 Created, 204 No Content
- **3xx Redirect:** 301 Moved Permanently, 302 Found, 304 Not Modified
- **4xx Client error:** 400 Bad Request, 401 Unauthorized (not logged in), 403 Forbidden (no permission), 404 Not Found, 405 Method Not Allowed, 409 Conflict, 422 Unprocessable Entity (validation), 429 Too Many Requests
- **5xx Server error:** 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable

### Q114. 401 vs 403?
**401** = you are **not authenticated** (no/invalid token). **403** = you **are authenticated but not allowed** to access this.

### Q115. REST API URL naming best practices?
- Use **nouns, plural, lowercase**, not verbs: `/users`, `/products/10/reviews`
- ❌ `/getAllUsers`, `/deleteUser`
- Use hyphens, not underscores; avoid deep nesting (max 2 levels)
- Use **HTTP methods** to show the action

### Q116. How do you do pagination, filtering, sorting and searching?
Query parameters: `GET /products?page=2&limit=10&sort=-price&category=phone&search=iphone`
```js
const page = parseInt(req.query.page) || 1;
const limit = parseInt(req.query.limit) || 10;
const products = await Product.find().skip((page - 1) * limit).limit(limit);
const total = await Product.countDocuments();
res.json({ total, page, pages: Math.ceil(total / limit), data: products });
```
For huge datasets, use **cursor-based pagination** (better performance than `skip`).

### Q117. How do you version an API?
`/api/v1/users` (URL versioning, most common), or via a header (`Accept-Version`). Versioning prevents breaking existing clients.

### Q118. What is a good JSON response format?
Consistent structure:
```json
{ "success": true, "message": "User created", "data": { "id": 1 } }
{ "success": false, "message": "Email already exists" }
```

### Q119. What are HTTP headers? Important ones?
Metadata sent with requests/responses: `Content-Type`, `Authorization`, `Accept`, `Cache-Control`, `Set-Cookie`, `Origin`, `User-Agent`, `ETag`.

### Q120. What is the difference between REST and GraphQL?
REST has **multiple endpoints**, fixed responses (possible over/under-fetching). GraphQL has **one endpoint**, and the client asks for **exactly the fields needed.**

### Q121. REST vs SOAP?
SOAP is older, XML-only, strict, heavy. REST is lightweight, uses JSON, easier and faster, so it's the most commonly used.

### Q122. What is idempotency key? (advanced)
A unique key sent with a POST (like a payment) so that **retries don't create duplicates**; the server remembers the key.

### Q123. What is a CRUD REST API example in Express?
```js
const express = require("express");
const app = express();
app.use(express.json());

let users = [{ id: 1, name: "Ravi" }];

app.get("/api/users", (req, res) => res.json(users));

app.get("/api/users/:id", (req, res) => {
  const user = users.find(u => u.id === +req.params.id);
  if (!user) return res.status(404).json({ message: "User not found" });
  res.json(user);
});

app.post("/api/users", (req, res) => {
  const user = { id: Date.now(), name: req.body.name };
  users.push(user);
  res.status(201).json(user);
});

app.put("/api/users/:id", (req, res) => {
  const index = users.findIndex(u => u.id === +req.params.id);
  if (index === -1) return res.status(404).json({ message: "Not found" });
  users[index] = { ...users[index], ...req.body };
  res.json(users[index]);
});

app.delete("/api/users/:id", (req, res) => {
  users = users.filter(u => u.id !== +req.params.id);
  res.status(204).send();
});

app.listen(3000);
```

### Q124. What is HATEOAS, Swagger/OpenAPI, Postman?
- **HATEOAS:** responses include links to related actions (rarely used in practice).
- **Swagger/OpenAPI:** standard to **document** APIs (`swagger-ui-express`).
- **Postman/Thunder Client:** tools to **test** APIs.

### Q125. How do you make a REST API secure and production-ready?
HTTPS, authentication (JWT), authorization (roles), input validation, rate limiting, CORS config, helmet, hashing passwords, no sensitive data in responses/logs, proper status codes, centralized error handling, logging, pagination, API versioning.

---

# Part 7 — Databases

### Q126. SQL vs NoSQL?
| SQL (MySQL, PostgreSQL) | NoSQL (MongoDB) |
|---|---|
| Tables, rows, fixed schema | Documents (JSON-like), flexible schema |
| Good for complex relations/transactions | Good for fast, scalable, changing data |
| Scales mostly vertically | Scales horizontally easily |

### Q127. What is MongoDB? Key terms?
A NoSQL **document database** storing data as **BSON** (binary JSON). Terms: **Database → Collection (like table) → Document (like row) → Field.** Each document has a unique `_id`.

### Q128. What is Mongoose?
An **ODM (Object Data Modeling) library** for MongoDB in Node.js. It gives **schemas, models, validation, middleware (hooks), and query helpers.**

### Q129. Connect to MongoDB using Mongoose?
```js
const mongoose = require("mongoose");
mongoose.connect(process.env.MONGO_URI)
  .then(() => console.log("DB connected"))
  .catch(err => { console.error(err); process.exit(1); });
```

### Q130. What is a Schema and Model? Example?
**Schema** defines the structure; **Model** is the interface to interact with the collection.
```js
const userSchema = new mongoose.Schema({
  name:  { type: String, required: true, trim: true },
  email: { type: String, required: true, unique: true, lowercase: true },
  password: { type: String, required: true, minlength: 6, select: false },
  role:  { type: String, enum: ["user", "admin"], default: "user" },
}, { timestamps: true });

const User = mongoose.model("User", userSchema);
```

### Q131. Common Mongoose CRUD operations?
```js
await User.create({ name, email });            // or new User().save()
await User.find({ role: "user" });
await User.findById(id);
await User.findOne({ email });
await User.findByIdAndUpdate(id, { name }, { new: true, runValidators: true });
await User.findByIdAndDelete(id);
await User.countDocuments();
```

### Q132. Query tools: filter, select, sort, limit, skip, populate?
```js
User.find({ age: { $gte: 18 } })
    .select("name email")
    .sort({ createdAt: -1 })
    .skip(10).limit(10)
    .populate("posts");
```
Operators: `$gt, $gte, $lt, $lte, $in, $nin, $ne, $and, $or, $regex, $exists`. Update: `$set, $inc, $push, $pull, $addToSet`.

### Q133. What is `populate`? Embedding vs referencing?
`populate` replaces a **referenced ObjectId** with the actual document (like a JOIN).
- **Embedding:** store related data **inside** the document (fast reads, good for 1-to-few).
- **Referencing:** store the **ID** of another document (good for 1-to-many/many-to-many, avoids duplication).

### Q134. What is the MongoDB aggregation pipeline?
A series of stages to process/transform data: `$match` (filter) → `$group` (group/sum) → `$sort` → `$project` → `$lookup` (join) → `$limit`.
```js
Order.aggregate([
  { $match: { status: "paid" } },
  { $group: { _id: "$userId", total: { $sum: "$amount" } } },
  { $sort: { total: -1 } },
]);
```

### Q135. What are indexes? Why use them?
Indexes make queries **faster** (like a book index) by avoiding a full collection scan. Trade-off: extra storage and slower writes. Define with `schema.index({ email: 1 })` or `unique: true`. Check query performance with `.explain()`.

### Q136. What are Mongoose middleware (hooks)?
Functions that run before/after operations (`pre("save")`, `post("save")`). Common use: **hash the password before saving.**
```js
userSchema.pre("save", async function (next) {
  if (!this.isModified("password")) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});
```

### Q137. What are virtuals and instance/static methods in Mongoose?
**Virtuals:** computed fields not stored in DB. **Instance methods:** on a document (`user.comparePassword()`). **Static methods:** on the model (`User.findByEmail()`).

### Q138. `find()` vs `findOne()` vs `findById()`?
`find()` → **array** of all matches (empty array if none). `findOne()` → first match or `null`. `findById(id)` → shortcut for `findOne({ _id: id })`.

### Q139. What is `lean()`?
Returns **plain JS objects** instead of full Mongoose documents. It is faster and uses less memory for read-only queries.

### Q140. What are transactions in MongoDB/SQL? ACID?
A transaction groups multiple operations: **all succeed or all fail.** **ACID:** Atomicity, Consistency, Isolation, Durability. Needed e.g. for money transfer. MongoDB supports multi-document transactions on replica sets.

### Q141. What is SQL in Node.js? (MySQL/PostgreSQL)
Use drivers (`mysql2`, `pg`) or ORMs (**Sequelize, Prisma, TypeORM, Knex**). Use **parameterized queries** to prevent SQL injection.
```js
const [rows] = await pool.execute("SELECT * FROM users WHERE id = ?", [id]);
```

### Q142. Common SQL concepts asked?
`SELECT, WHERE, ORDER BY, GROUP BY, HAVING, JOIN (INNER/LEFT/RIGHT), PRIMARY KEY, FOREIGN KEY, INDEX`, normalization, and the difference between `DELETE`, `TRUNCATE`, `DROP`.

### Q143. What is connection pooling?
Reusing a **set of open DB connections** instead of creating a new one per request, which saves time and resources. Mongoose and `pg`/`mysql2` do this.

### Q144. What is Redis? Why use it with Node?
An **in-memory key–value store**, extremely fast. Uses: **caching** API/DB results, session storage, rate limiting, queues/pub-sub.

---

# Part 8 — Authentication and Authorization

### Q145. Authentication vs Authorization?
**Authentication** = *who are you?* (login). **Authorization** = *what are you allowed to do?* (permissions/roles).

### Q146. What is JWT? Structure?
**JSON Web Token**: a signed token used for **stateless authentication.** Three parts separated by dots: **Header . Payload . Signature.**
- Header: algorithm/type
- Payload: data (user id, role, expiry). **It is only encoded, not encrypted, so never put passwords or secrets in it.**
- Signature: ensures the token wasn't changed (made using a secret key)

### Q147. Explain the JWT login flow.
1. User sends email + password to `/login`.
2. Server verifies the password (bcrypt compare).
3. Server creates a JWT (`jwt.sign`) and sends it back.
4. Client sends it with each request: `Authorization: Bearer <token>`.
5. Server middleware verifies it (`jwt.verify`) and allows or rejects.

### Q148. Implement JWT login and auth middleware.
```js
const jwt = require("jsonwebtoken");
const bcrypt = require("bcryptjs");

// Register
exports.register = async (req, res) => {
  const { name, email, password } = req.body;
  const hashed = await bcrypt.hash(password, 10);
  const user = await User.create({ name, email, password: hashed });
  res.status(201).json({ id: user._id, email: user.email });
};

// Login
exports.login = async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email }).select("+password");
  if (!user || !(await bcrypt.compare(password, user.password)))
    return res.status(401).json({ message: "Invalid credentials" });

  const token = jwt.sign({ id: user._id, role: user.role }, process.env.JWT_SECRET, { expiresIn: "1d" });
  res.json({ token });
};

// Auth middleware
exports.protect = (req, res, next) => {
  const header = req.headers.authorization;
  if (!header?.startsWith("Bearer ")) return res.status(401).json({ message: "No token" });
  try {
    req.user = jwt.verify(header.split(" ")[1], process.env.JWT_SECRET);
    next();
  } catch {
    res.status(401).json({ message: "Invalid or expired token" });
  }
};

// Role-based authorization
exports.restrictTo = (...roles) => (req, res, next) =>
  roles.includes(req.user.role) ? next() : res.status(403).json({ message: "Forbidden" });

// Usage
router.delete("/users/:id", protect, restrictTo("admin"), deleteUser);
```

### Q149. Why hash passwords? What is bcrypt? What is salt?
Never store plain passwords. **Hashing** is a one-way function. **bcrypt** is a slow, secure hashing algorithm that adds a **salt** (random data) so the same password gives different hashes and rainbow-table attacks fail. The "10" is the **salt rounds** (cost factor).

### Q150. Hashing vs encryption vs encoding?
- **Hashing:** one-way, can't be reversed (passwords)
- **Encryption:** two-way with a key (can be decrypted)
- **Encoding:** just a format change, no security (Base64)

### Q151. Access token vs refresh token?
**Access token:** short-lived (e.g., 15 min) used for API calls. **Refresh token:** long-lived, used only to get a **new access token** without logging in again. Store the refresh token in an **httpOnly cookie** (and keep a copy/hash in the DB to be able to revoke it).

### Q152. Where to store JWT on the client? 
- **localStorage:** easy but vulnerable to **XSS.**
- **httpOnly, secure, sameSite cookie:** JavaScript can't read it (safer against XSS), but needs **CSRF** protection (`sameSite` helps).

### Q153. JWT vs session authentication?
| JWT | Session |
|---|---|
| Stateless (nothing stored on server) | Stateful (server stores session) |
| Scales easily across servers | Needs shared store (Redis) for scaling |
| Hard to revoke before expiry | Easy to invalidate |

### Q154. How do you log out with JWT?
Remove the token on the client; if using refresh tokens/cookies, clear the cookie and delete the refresh token from DB. For immediate revoke, maintain a **blacklist** (Redis) or keep expiry short.

### Q155. What is OAuth 2.0? What is Passport.js?
**OAuth 2.0** lets users log in using another provider (Google, GitHub) without sharing their password. **Passport.js** is Express middleware with "strategies" for local, JWT, Google, Facebook, etc.

### Q156. What is RBAC?
**Role-Based Access Control:** permissions are given based on roles (admin, editor, user). Implemented using the `restrictTo` middleware above.

### Q157. How does password reset work?
User requests reset → server generates a random token (`crypto.randomBytes`), stores its **hash** with an expiry → emails a link with the token (Nodemailer) → user submits a new password with the token → server verifies the token and expiry → hashes and saves the new password.

### Q158. What is 2FA / OTP? 
Second verification step (OTP via SMS/email/authenticator app) in addition to a password.

---

# Part 9 — Security

### Q159. How do you secure a Node.js/Express application?
1. Use **HTTPS**
2. **Helmet** for secure headers
3. **Validate and sanitize** all input
4. **Hash passwords** (bcrypt)
5. **Rate limiting** (`express-rate-limit`)
6. Proper **CORS** configuration
7. Keep **secrets in env variables**
8. **Parameterized queries / sanitize NoSQL input** (prevent injection)
9. Run `npm audit`; keep dependencies updated
10. Don't leak stack traces or sensitive data in production errors
11. Use secure cookies (`httpOnly`, `secure`, `sameSite`)
12. Limit request body size: `express.json({ limit: "10kb" })`

### Q160. What is SQL / NoSQL injection? How to prevent it?
Attacker sends malicious input to change the query. Example in MongoDB: `{ "email": { "$ne": null }, "password": { "$ne": null } }` could bypass login.
**Prevention:** validate types (Joi/Zod), use `express-mongo-sanitize`, parameterized queries/ORM, never build queries by string concatenation.

### Q161. What is XSS? How to prevent it?
**Cross-Site Scripting:** attacker injects malicious script that runs in other users' browsers. Prevent by **escaping/sanitizing output**, using Content Security Policy (via helmet), avoiding `innerHTML`, and using `httpOnly` cookies.

### Q162. What is CSRF? How to prevent it?
**Cross-Site Request Forgery:** a malicious site tricks a logged-in user's browser into sending a request to your server (using cookies automatically). Prevent with **CSRF tokens**, `sameSite` cookies, and checking the Origin header. (APIs using the `Authorization` header instead of cookies are mostly not affected.)

### Q163. What is Helmet.js?
Sets various **HTTP security headers** (X-Content-Type-Options, Strict-Transport-Security, Content-Security-Policy, X-Frame-Options, etc.). Usage: `app.use(helmet())`.

### Q164. What is DDoS / brute force? How to reduce?
Flooding or repeatedly guessing to break/crash your app. Reduce with **rate limiting, account lockout/delay, CAPTCHA, a WAF/CDN (Cloudflare), load balancing.**

### Q165. How do you protect sensitive data and secrets?
Never hardcode secrets or commit `.env`. Use environment variables or secret managers (AWS Secrets Manager, Vault). Rotate keys. Don't log passwords/tokens. Encrypt sensitive data.

### Q166. What is HTTP Parameter Pollution (HPP)?
Sending repeated query params (`?id=1&id=2`) to confuse the app. Use `hpp` middleware and validate input.

### Q167. What are common security headers / what is CSP?
**Content-Security-Policy** restricts which sources scripts/styles/images can load from, reducing XSS risk. **HSTS** forces HTTPS.

### Q168. What are the OWASP Top 10?
A list of the most critical web security risks, such as broken access control, injection, cryptographic failures, insecure design, security misconfiguration, vulnerable/outdated components, authentication failures, and logging/monitoring failures. Just show that you know it exists and a few items.

---

# Part 10 — Error Handling, Logging and Validation

### Q169. How do you handle errors in Node.js?
- `try/catch` with async/await
- `.catch()` for promises
- error-first callbacks
- Express error middleware
- **Custom error classes**
- Global handlers for `uncaughtException` / `unhandledRejection` (log and **restart gracefully**; don't continue running in an unknown state)

### Q170. Operational errors vs programmer errors?
- **Operational:** expected runtime problems (invalid input, DB down, 404, timeout). Handle and respond properly.
- **Programmer errors:** bugs (undefined variable, wrong logic). Fix the code; for unknown crashes, log and restart.

### Q171. Custom error class?
```js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}
// Usage
if (!user) return next(new AppError("User not found", 404));
```

### Q172. Handling uncaught exceptions and unhandled rejections?
```js
process.on("uncaughtException", (err) => { console.error(err); process.exit(1); });
process.on("unhandledRejection", (reason) => { console.error(reason); server.close(() => process.exit(1)); });
```
A process manager (PM2/Docker) restarts the app afterwards.

### Q173. What is graceful shutdown?
On `SIGTERM`/`SIGINT`, stop accepting new requests, **finish ongoing requests, close DB connections,** then exit.
```js
process.on("SIGTERM", () => {
  server.close(() => { mongoose.connection.close(false).then(() => process.exit(0)); });
});
```

### Q174. How do you do logging in Node.js?
`console.log` for simple cases; in production use **Winston** or **Pino** (log levels: error, warn, info, debug; write to files/log services) and **morgan** for HTTP request logs. Never log sensitive data.

### Q175. How do you validate input?
Using libraries: **Joi, Zod, express-validator, Yup**; or Mongoose schema validation. Validate **body, params and query** before processing.
```js
const Joi = require("joi");
const schema = Joi.object({
  name: Joi.string().min(3).required(),
  email: Joi.string().email().required(),
});
const { error } = schema.validate(req.body);
if (error) return res.status(400).json({ message: error.details[0].message });
```

### Q176. Why validate on both client and server?
Client validation is for user experience; it can be bypassed. **Server validation is mandatory for security and data integrity.**

---

# Part 11 — Performance, Scaling and Clustering

### Q177. How do you improve Node.js performance?
- Use **async/non-blocking** code; avoid sync functions in request handlers
- **Cache** (Redis, in-memory) frequent data
- **DB indexes**, pagination, `select` only needed fields, `lean()`
- **Compression** (gzip)
- **Cluster** / load balancing to use all CPU cores
- Offload heavy tasks to **worker threads/queues**
- Use **streams** for large data
- **CDN** for static files
- Keep dependencies small; use latest LTS
- Monitor and profile (clinic.js, `--prof`)

### Q178. What is clustering in Node.js?
Node runs on **one CPU core** by default. The **`cluster` module** creates **multiple worker processes** (one per core) that share the same port, so the app uses all cores.
```js
const cluster = require("cluster");
const os = require("os");

if (cluster.isPrimary) {
  os.cpus().forEach(() => cluster.fork());
  cluster.on("exit", () => cluster.fork());  // restart dead worker
} else {
  require("./server");
}
```
**PM2** can do this easily: `pm2 start server.js -i max`.

### Q179. What is load balancing?
Distributing incoming requests across multiple servers/processes so no single one is overloaded. Tools: **Nginx, HAProxy, AWS ALB.**

### Q180. Horizontal vs vertical scaling?
**Vertical:** add more CPU/RAM to one machine. **Horizontal:** add **more machines/instances** (preferred; needs stateless apps and shared storage/sessions).

### Q181. What is caching? Types and strategies?
Storing frequently-used data for faster access. Levels: browser cache, CDN, **application cache (Redis/node-cache)**, DB cache.
Simple Redis pattern: check cache → if miss, query DB → store in cache with **TTL** (expiry) → return. Also be aware of **cache invalidation** (updating/removing stale data).

### Q182. Why is `process.nextTick` or a long `for` loop dangerous?
They **block the event loop**, so no other request can be handled meanwhile. Keep handlers short and non-blocking.

### Q183. How do you find performance bottlenecks?
Use `console.time`, profiling (`node --prof`, Chrome DevTools via `--inspect`), **clinic.js**, APM tools (New Relic, Datadog), load testing (**autocannon, k6, Apache JMeter**), and check slow DB queries.

### Q184. What is a message queue? Why use it?
A system (RabbitMQ, Kafka, **BullMQ + Redis**, AWS SQS) that stores tasks to be processed **asynchronously in the background** (sending emails, reports, image processing). It makes APIs respond fast and makes the system more reliable.

### Q185. What is rate limiting vs throttling?
Rate limiting **rejects** requests over a limit (429). Throttling **slows down** requests.

### Q186. What are keep-alive connections and compression?
Keep-alive reuses a TCP connection for many requests (saves handshake time). Compression (gzip/brotli) reduces response size.

---

# Part 12 — Testing and Debugging

### Q187. Types of testing?
**Unit** (single function), **Integration** (modules/DB together), **End-to-End (E2E)** (full flow), plus load/performance tests.

### Q188. Which tools do you use to test a Node.js API?
**Jest** or **Mocha + Chai** (test runner/assertions), **Supertest** (HTTP API testing without starting a real server), mocks (`jest.fn()`, `sinon`), **mongodb-memory-server** for DB tests.
```js
const request = require("supertest");
const app = require("../src/app");

describe("GET /api/users", () => {
  it("returns 200 and an array", async () => {
    const res = await request(app).get("/api/users");
    expect(res.statusCode).toBe(200);
    expect(Array.isArray(res.body)).toBe(true);
  });
});
```

### Q189. What is mocking? Why use it?
Replacing real dependencies (DB, external API, email service) with **fake versions** in tests so tests are fast, isolated and predictable.

### Q190. What is TDD?
**Test-Driven Development:** write a failing test first → write code to pass it → refactor.

### Q191. How do you debug Node.js applications?
- `console.log` (quick) / `console.table`, `console.time`
- **`node --inspect`** and Chrome DevTools (`chrome://inspect`)
- **VS Code debugger** with breakpoints (launch.json)
- Read the **stack trace** carefully
- Logs (Winston/Pino), Postman for API checks, DB query logs

### Q192. How to check for security and outdated packages?
`npm audit`, `npm outdated`, Snyk, Dependabot.

---

# Part 13 — Deployment and DevOps Basics

### Q193. How do you deploy a Node.js app?
Options: **VPS/EC2 with PM2 + Nginx**, **Docker containers**, PaaS (**Render, Railway, Heroku, Vercel for serverless**), cloud (AWS, Azure, GCP). Steps: build/install (`npm ci --omit=dev`), set env variables, run with a process manager, reverse proxy with Nginx, HTTPS certificate (Let's Encrypt).

### Q194. What is PM2?
A **production process manager** for Node.js: keeps the app alive (auto-restart), supports **cluster mode**, logging, monitoring, startup scripts. Commands: `pm2 start app.js`, `pm2 list`, `pm2 logs`, `pm2 restart app`, `pm2 startup`.

### Q195. What is a reverse proxy? Why use Nginx with Node?
A server that sits **in front of** Node and forwards requests. Benefits: **SSL/HTTPS termination, load balancing, serving static files, caching, compression, security.**

### Q196. What is Docker? Basic Dockerfile for Node?
Docker packages the app with its dependencies in a **container** so it runs the same everywhere.
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```
Use `.dockerignore` (node_modules, .env).

### Q197. What is CI/CD?
**Continuous Integration:** automatically build and test code on every push. **Continuous Delivery/Deployment:** automatically deploy after tests pass. Tools: GitHub Actions, Jenkins, GitLab CI.

### Q198. How do you manage different environments (dev/staging/prod)?
Different `.env` configs / environment variables per environment, `NODE_ENV`, separate databases, and config files or secret managers.

### Q199. What is a health check endpoint?
A simple route (e.g., `GET /health`) that returns 200 if the app is running (and optionally DB is connected). Used by load balancers/Kubernetes to monitor the app.

### Q200. What is serverless?
Running functions in the cloud **without managing servers** (AWS Lambda, Vercel Functions). You pay per execution; it scales automatically. Good for small APIs/event handlers; watch for **cold starts.**

---

# Part 14 — Advanced Topics

### Q201. What are WebSockets? How are they different from HTTP?
WebSocket is a **persistent, two-way (full-duplex) connection** between client and server. HTTP is request–response (client always starts). WebSockets are used for **chat, live notifications, live dashboards, multiplayer games.**

### Q202. What is Socket.io? Basic example?
A library for real-time communication with features like **auto-reconnect, rooms, broadcasting, fallbacks.**
```js
const http = require("http");
const { Server } = require("socket.io");
const server = http.createServer(app);
const io = new Server(server, { cors: { origin: "*" } });

io.on("connection", (socket) => {
  socket.on("chat message", (msg) => io.emit("chat message", msg));  // broadcast
  socket.on("disconnect", () => console.log("user left"));
});
server.listen(3000);
```

### Q203. Polling vs long polling vs SSE vs WebSockets?
- **Polling:** client asks repeatedly every few seconds (wasteful)
- **Long polling:** server holds the request until data is available
- **SSE (Server-Sent Events):** server pushes updates one-way over HTTP
- **WebSockets:** two-way real-time

### Q204. What is GraphQL? 
A query language for APIs where the client requests **exactly the data it needs** from a **single endpoint.** In Node: Apollo Server / GraphQL Yoga. Concepts: **schema, queries (read), mutations (write), subscriptions (real-time), resolvers.**

### Q205. What are microservices vs monolith?
**Monolith:** one big application. **Microservices:** many small, independent services (users, orders, payments) communicating via APIs/message queues. Pros: independent scaling/deployment. Cons: complexity, network calls, data consistency.

### Q206. What is an API Gateway?
A single entry point for clients that routes requests to the right microservice and handles auth, rate limiting, logging (e.g., Kong, AWS API Gateway, Nginx).

### Q207. What is TypeScript with Node.js? Why use it?
TypeScript adds **static types** to JavaScript, catching errors at compile time and improving maintainability. Run with `ts-node`/`tsx` in dev and compile to JS with `tsc` for production.

### Q208. What is NestJS?
A progressive Node.js framework using **TypeScript, decorators, modules, dependency injection** (similar to Angular), good for large, structured applications.

### Q209. What are design patterns commonly used in Node.js?
**Singleton** (DB connection / module caching), **Factory**, **Observer** (EventEmitter), **Middleware / Chain of Responsibility** (Express), **Dependency Injection**, **Repository** pattern.

### Q210. What is dependency injection?
Passing a component's dependencies **from outside** instead of creating them inside, which makes code loosely coupled and easy to test/mock.

### Q211. What is the difference between `exec`, `spawn`, `fork`? (quick recall)
`exec` buffers output (small), `spawn` streams output (large), `fork` creates a new Node process with IPC messaging.

### Q212. What is event-driven architecture?
Components communicate by **producing and reacting to events** (via EventEmitter, queues, Kafka), which keeps them loosely coupled and scalable.

### Q213. What is a Node.js cluster vs PM2 cluster vs Kubernetes?
`cluster` module = multiple processes on **one machine**. PM2 = easy wrapper/manager for that. **Kubernetes** = orchestrates **containers across many machines** (auto-scale, self-heal).

### Q214. What is Server-Side Rendering with Node?
Rendering HTML on the server (Next.js, EJS/Pug/Handlebars templates) so pages load faster and are SEO-friendly. `res.render("index", { name })` with a view engine.

### Q215. What is middleware vs interceptor vs guard (NestJS awareness)?
In NestJS: **middleware** (before routes), **guards** (authorization), **interceptors** (transform request/response), **pipes** (validation/transformation), **filters** (exception handling).

### Q216. How do you send emails from Node.js?
**Nodemailer** with SMTP or services like SendGrid/AWS SES. Send in the **background (queue)** so the API stays fast.

### Q217. How do you schedule tasks (cron jobs)?
**node-cron**, **node-schedule**, or **BullMQ** repeatable jobs. Example: `cron.schedule("0 0 * * *", cleanup)` runs daily at midnight.

### Q218. How do you handle file uploads to the cloud?
Receive with **multer** (memory storage) → upload to **AWS S3 / Cloudinary** → save the returned URL in the DB. For big files, use **pre-signed URLs** so the client uploads directly to S3.

### Q219. What is the `ESM` top-level await? 
In ES modules you can use `await` at the top level of a file (no need to wrap in an async function).

### Q220. What are some new Node.js features worth mentioning?
Built-in `fetch`, built-in test runner (`node --test`), `--watch` mode, `--env-file`, permission model (experimental), `structuredClone`, `AbortController`, ES modules improvements. *(Only mention what you're sure about.)*

---

# Part 15 — Coding Tasks

> Practice typing these in VS Code. Interviewers often ask for a small API or a JS/async problem.

### Task 1. Hello World server with Express
(See Q82.)

### Task 2. CRUD REST API (in-memory)
(See Q123.)

### Task 3. Read a file asynchronously and handle errors
```js
const fs = require("fs/promises");
async function readData() {
  try {
    const data = await fs.readFile("data.txt", "utf8");
    console.log(data);
  } catch (err) {
    console.error("Error reading file:", err.message);
  }
}
readData();
```

### Task 4. Custom logging middleware + error handler
```js
app.use((req, res, next) => {
  console.log(`${new Date().toISOString()} ${req.method} ${req.originalUrl}`);
  next();
});
app.use((err, req, res, next) => {
  res.status(err.statusCode || 500).json({ message: err.message });
});
```

### Task 5. JWT authentication (register, login, protected route)
(See Q148.)

### Task 6. Convert callback to promise
```js
const readFilePromise = (path) =>
  new Promise((resolve, reject) => {
    fs.readFile(path, "utf8", (err, data) => (err ? reject(err) : resolve(data)));
  });
```

### Task 7. Run multiple API calls in parallel
```js
const [users, posts] = await Promise.all([
  fetch("https://jsonplaceholder.typicode.com/users").then(r => r.json()),
  fetch("https://jsonplaceholder.typicode.com/posts").then(r => r.json()),
]);
```

### Task 8. Simple in-memory rate limiter middleware
```js
const hits = new Map();
const limiter = (req, res, next) => {
  const ip = req.ip;
  const now = Date.now();
  const windowMs = 60_000, max = 5;
  const record = (hits.get(ip) || []).filter(t => now - t < windowMs);
  if (record.length >= max) return res.status(429).json({ message: "Too many requests" });
  record.push(now);
  hits.set(ip, record);
  next();
};
```

### Task 9. Create a custom EventEmitter
```js
const EventEmitter = require("events");
class Order extends EventEmitter {}
const order = new Order();
order.on("placed", (id) => console.log("Order placed:", id));
order.emit("placed", 101);
```

### Task 10. Stream a large file in an HTTP response
```js
app.get("/video", (req, res) => {
  fs.createReadStream("big.mp4").pipe(res);
});
```

### Task 11. Pagination API with Mongoose
(See Q116.)

### Task 12. Simple caching middleware (in-memory)
```js
const cache = new Map();
const cacheMiddleware = (ttl = 30_000) => (req, res, next) => {
  const key = req.originalUrl;
  const hit = cache.get(key);
  if (hit && Date.now() - hit.time < ttl) return res.json(hit.data);
  const originalJson = res.json.bind(res);
  res.json = (data) => { cache.set(key, { data, time: Date.now() }); return originalJson(data); };
  next();
};
```

### Task 13. Implement debounce / retry with async
```js
async function retry(fn, times = 3, delay = 500) {
  for (let i = 0; i < times; i++) {
    try { return await fn(); }
    catch (err) { if (i === times - 1) throw err; await new Promise(r => setTimeout(r, delay)); }
  }
}
```

### Output-based questions (commonly asked)
```js
console.log("A");
setTimeout(() => console.log("B"), 0);
Promise.resolve().then(() => console.log("C"));
process.nextTick(() => console.log("D"));
console.log("E");
// Output: A E D C B
```
```js
console.log(typeof null);   // object
console.log([] == false);   // true
console.log(0.1 + 0.2);     // 0.30000000000000004
(async () => { console.log(1); await null; console.log(2); })(); console.log(3);  // 1 3 2
```

---

# Part 16 — Scenario-Based and HR Questions

### Q221. Your API is slow. How do you find and fix the problem?
1. Measure: check logs, response times, profiling, APM.
2. Find the bottleneck: DB query? External API? CPU-heavy code? Blocking sync code?
3. Fix: add **DB indexes**, optimize queries, use **caching (Redis)**, **pagination**, parallelize with `Promise.all`, move heavy tasks to **workers/queues**, enable **compression**, scale with **cluster/load balancer.**

### Q222. Your Node app crashes in production. What do you do?
Check **logs** and the error/stack trace, reproduce locally, fix the root cause. Meanwhile ensure **PM2/Docker auto-restarts** the app. Add proper error handling, monitoring/alerts (Sentry), and graceful shutdown to avoid repeats.

### Q223. Server memory keeps increasing. What could be wrong?
Likely a **memory leak** (global arrays, uncleared timers, unremoved listeners, big caches). Use `process.memoryUsage()`, heap snapshots via `--inspect`, find what's growing, and fix it.

### Q224. How do you design an API for a todo / e-commerce / chat application?
Identify **resources** (users, products, orders), design **RESTful routes**, **models and relations**, **auth (JWT + roles)**, validation, error handling, pagination, and for chat add **Socket.io + DB for message history.**

### Q225. How do you handle 10,000 concurrent requests?
Use Node's async nature, **cluster/PM2 multiple instances**, **load balancer (Nginx)**, **Redis cache**, **DB connection pooling and indexes**, **queue** heavy jobs, a **CDN** for static content, rate limiting, and horizontal scaling.

### Q226. How do you structure a large Node.js project?
Layered architecture: **routes → controllers → services → repositories/models**, with separate folders for config, middleware, utils, validators, and tests; use environment configs, central error handling, and consistent naming.

### Q227. Tell me about your Node.js project.
Template: "In my project, I built a ___ REST API using Node.js, Express and MongoDB. I implemented **JWT authentication, role-based authorization, CRUD APIs, validation (Joi), error handling middleware, pagination,** and tested with **Postman.** I used **Mongoose** for data modeling and followed a controller–service structure."

### Q228. A challenge you faced and how you solved it?
Pick a real example, e.g., "A route was slow due to missing DB index and N+1 queries; I added an index and used `populate`/aggregation, reducing response time from X to Y." Or "Handled CORS issues," "Fixed unhandled promise rejections."

### Q229. How do you manage API changes without breaking clients?
**Versioning** (`/api/v1`, `/api/v2`), keep old versions supported for a while, **deprecation notices**, backward-compatible changes, and documentation (Swagger).

### Q230. Strengths / weaknesses / why should we hire you / where do you see yourself?
- **Strength:** quick learner, clean and structured code, good debugging.
- **Weakness:** "I'm still learning advanced topics like microservices and system design, and I'm working on them through practice."
- **Why hire me:** "I understand Node/Express fundamentals well, I can build and secure REST APIs, and I learn fast and work well in a team."
- **Future:** "Grow into a strong full-stack/backend engineer."

### Q231. Questions to ask the interviewer
- "What's the tech stack and architecture of the product?"
- "What would my first 3 months look like?"
- "How does the team handle code reviews, testing and deployments?"

---

# Part 17 — Last-Minute Cheat Sheet

### One-line answers (memorize)
| Topic | One-line answer |
|---|---|
| Node.js | JavaScript runtime built on V8 for running JS on the server |
| Why fast | V8 + non-blocking I/O + event loop |
| Single-threaded? | JS runs on one thread; I/O is delegated to libuv/OS |
| Event loop | Moves queued callbacks to the call stack when it's empty |
| libuv | C library providing event loop and thread pool |
| nextTick vs setImmediate | nextTick runs before the loop continues; setImmediate in the check phase |
| Module types | Core, local, third-party |
| CJS vs ESM | require/module.exports vs import/export |
| Stream | Process data in chunks (readable, writable, duplex, transform) |
| Buffer | Raw binary data memory |
| EventEmitter | Create and listen to custom events |
| Cluster | Multiple processes to use all CPU cores |
| Worker thread | Threads for CPU-heavy JS tasks |
| Express | Minimal web framework for Node |
| Middleware | Function with (req, res, next) between request and response |
| Error middleware | 4 params (err, req, res, next), placed last |
| REST | Stateless, resource-based API using HTTP methods |
| PUT vs PATCH | Replace whole vs update part |
| 401 vs 403 | Not authenticated vs not allowed |
| JWT | Signed token: header.payload.signature (stateless auth) |
| bcrypt | Slow hashing with salt for passwords |
| CORS | Browser rule for cross-origin requests; enabled on server |
| Mongoose | ODM for MongoDB with schemas and validation |
| Index | Speeds up queries, costs write speed/storage |
| Redis | In-memory store for caching, sessions, queues |
| PM2 | Process manager (restart, cluster, logs) |
| Nginx | Reverse proxy, load balancer, SSL |
| Socket.io | Real-time two-way communication |

### Top 20 most-asked Node.js questions
1. What is Node.js and why is it fast?
2. Single-threaded model and event loop (phases)
3. Blocking vs non-blocking
4. `nextTick` vs `setImmediate` vs `setTimeout`
5. Callbacks, promises, async/await
6. `require` vs `import`, `module.exports`
7. What is middleware in Express?
8. `req.params` vs `req.query` vs `req.body`
9. REST principles, HTTP methods, status codes
10. PUT vs PATCH, 401 vs 403
11. JWT authentication flow and bcrypt
12. Error handling in Express
13. Streams and Buffers
14. Cluster vs worker threads vs child process
15. MongoDB/Mongoose basics (CRUD, populate, indexes)
16. SQL vs NoSQL
17. How to secure a Node.js app
18. How to improve performance / scale
19. CORS
20. Your project explanation

### Interview day tips
- **Think aloud** and explain your reasoning, even if you're unsure.
- Give **short, structured answers** and offer: *"I can explain with an example if you like."*
- Draw the **event loop** or the **request → middleware → controller → DB → response** flow if you get a whiteboard.
- Connect answers to **your own project** whenever you can.
- If you don't know: *"I haven't worked with it directly, but I understand it as ... and I'm eager to learn."*
- Keep **VS Code, Node, Postman, and a sample Express project** ready for live coding.
- Be calm and confident. **You've prepared well.** 💪

---
**Best of luck with your interview! 🚀**
