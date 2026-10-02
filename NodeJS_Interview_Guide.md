# Master Node.js Backend Interview Guide: Node.js, Express, REST, Auth, MongoDB, PostgreSQL

**For:** Goram Siva Prasad | **Role:** Full Stack Engineer (6 months to 1 year experience) | **Round:** Technical (theory + live coding)

> Every question has a **simple answer** and, where useful, a **"Say it like this"** line. Type the code sections yourself in a small Express project. Reading alone will not be enough for a live coding round.

---

## Table of Contents

1. [How to answer in the interview](#1-how-to-answer-in-the-interview)
2. [Resume based questions (backend side)](#2-resume-based-questions-backend-side)
3. [Node.js fundamentals](#3-nodejs-fundamentals)
4. [Node.js output based questions](#4-nodejs-output-based-questions)
5. [Express.js questions](#5-expressjs-questions)
6. [REST API design](#6-rest-api-design)
7. [Authentication, authorization and security](#7-authentication-authorization-and-security)
8. [MongoDB and Mongoose](#8-mongodb-and-mongoose)
9. [PostgreSQL with Node.js](#9-postgresql-with-nodejs)
10. [Real-time: WebSockets and Socket.io](#10-real-time-websockets-and-socketio)
11. [Performance, scaling and deployment](#11-performance-scaling-and-deployment)
12. [Scenario based questions](#12-scenario-based-questions)
13. [Live coding: project setup and CRUD API](#13-live-coding-project-setup-and-crud-api)
14. [Live coding: authentication and RBAC](#14-live-coding-authentication-and-rbac)
15. [Live coding: validation, errors, pagination, upload](#15-live-coding-validation-errors-pagination-upload)
16. [Live coding: payments, sockets, streams, PostgreSQL](#16-live-coding-payments-sockets-streams-postgresql)
17. [Small coding problems in Node](#17-small-coding-problems-in-node)
18. [Pseudo code guide](#18-pseudo-code-guide)
19. [Interview day strategy](#19-interview-day-strategy)
20. [Last day revision checklist](#20-last-day-revision-checklist)

---

## 1. How to answer in the interview

Use the same pattern for every theory question:

1. **Definition** in one line.
2. **Why we use it** (the problem it solves).
3. **Small example or your project use.**
4. **Pitfall or comparison** (shows depth).

**Example, "What is middleware in Express?"**
"Middleware is a function that runs between the request and the response. It receives `req`, `res` and `next`. It can read or change the request, end the response, or call `next()` to pass control forward. In my projects I used middleware for JWT authentication, role checks, request validation and error handling. The order matters, because Express runs them in the order they are registered."

**For live coding:**
- Ask about inputs, outputs and edge cases first.
- Say your plan: "I will create a router, a model, and a controller, then add validation."
- Write the working version first, then add error handling.
- Always return proper status codes and JSON error messages.
- Use `async/await` with `try/catch` or an async wrapper.
- If you do not know something, say what you know and how you would find out. Never make things up.

---

## 2. Resume based questions (backend side)

Prepare each answer using **Problem, What I built, Tools, Result.** Change the details to match what you really did.

### Q1. Explain the backend of your healthcare platform (eAsha).
- **Stack:** Node.js, Express, MongoDB, Socket.io, Razorpay, deployed on AWS EC2 behind an ALB with Nginx and PM2.
- **What you built:** APIs for three roles (User, Doctor, Admin), appointment booking by department, doctor onboarding with document verification and admin approval, payment flow, real-time appointment tracking, video consultation links, pharmacy, lab and scan booking.
- **Result:** around 40% faster APIs through query optimization and indexing.

**Likely follow-up questions:**
- **How did you handle roles?** JWT contains the user id and role. An `auth` middleware verifies the token. An `authorize("admin")` middleware checks the role before the controller runs.
- **How did you verify Razorpay payments?** Backend creates an order with the amount in paise. After payment, frontend sends `order_id`, `payment_id` and `signature`. Backend computes an HMAC SHA256 of `order_id|payment_id` with the secret key and compares it with the signature. Only then it marks the appointment as paid. (A webhook is the more reliable backup.)
- **How did you improve API speed by 40%?** Be specific. Example: found slow queries, added indexes on fields used in filters (doctorId, date, status), selected only needed fields, used `lean()`, added pagination, and avoided repeated queries inside loops. Say only what you actually did.
- **How did you prevent double booking of an appointment slot?** A unique compound index on `(doctorId, date, slot)` so the database rejects duplicates, plus handling the duplicate key error (code 11000) with a friendly message.
- **What happens if the payment succeeds but the user closes the browser?** The frontend call may never reach the server. A Razorpay webhook (`payment.captured`) updates the booking on the server side. Make the webhook idempotent.
- **How did you deploy?** Code on EC2, Node process managed by PM2 (restart on crash, cluster mode, logs), Nginx as reverse proxy, ALB for load balancing and health checks.

### Q2. Explain the backend of the Agri-Tech ERP.
- Node.js and PostgreSQL. Hierarchy of farms, blocks, rows and beds modeled with related tables (foreign keys).
- Be ready for: table design, joins, indexing, how you stored coordinates, how you returned hierarchical data efficiently, and pagination.

### Q3. How does the AI document extraction backend connect with Node (if asked)?
That service was FastAPI. If the interviewer asks how Node and FastAPI work together: Node (or the frontend) uploads the file, FastAPI processes it with OCR and LLMs, and returns JSON. For long jobs, use async processing: return a job id, process in the background, and let the client poll or receive a socket or webhook update.

### Q4. Explain the backend of your chat application.
- Socket.io for real-time messaging, rooms for channels, JWT check at connection, roles (Member, TL, Admin, Super Admin), AES-256 encryption, invite links with tokens.
- Be ready for: how a message travels from sender to receiver, how you authenticate a socket, how you scale sockets across servers (Redis adapter), and how invite tokens expire.

### Q5. What was the hardest backend problem you solved?
Pick a real one: a slow query, a payment mismatch, duplicate bookings, memory growing in a long running process, or a CORS or auth bug. Explain the symptom, how you found it (logs, profiling, query explain), the fix and the result.

---

## 3. Node.js fundamentals

### Q1. What is Node.js?
A JavaScript runtime built on Chrome's V8 engine that lets you run JavaScript on the server. It is event-driven and uses non-blocking I/O, which makes it good for APIs, real-time apps and I/O heavy work.

### Q2. Node is single threaded. How does it handle many requests?
Your JavaScript runs on one main thread, but I/O work (files, network, database) is handed to the operating system or to libuv's thread pool. When the work finishes, a callback is queued and the event loop runs it. So the main thread is never waiting, and it can accept new requests meanwhile.

**Say it like this:** "Node uses one thread for JavaScript but delegates I/O to the system and the libuv thread pool. The event loop picks up the results, so one thread can serve thousands of connections as long as we do not block it with heavy CPU work."

### Q3. Explain the Node.js event loop and its phases.
The loop runs in phases: **timers** (`setTimeout`, `setInterval`), **pending callbacks**, **poll** (new I/O events), **check** (`setImmediate`), **close callbacks**. Between steps, **`process.nextTick`** callbacks and then **promise microtasks** run.

### Q4. `process.nextTick` vs `setImmediate` vs `setTimeout(fn, 0)`?
- `process.nextTick` runs right after the current operation, before the event loop continues (highest priority).
- Promise `.then` runs in the microtask queue, right after nextTick.
- `setTimeout(fn, 0)` runs in the timers phase.
- `setImmediate` runs in the check phase, after the poll phase.

Order: sync code, then `nextTick`, then promises, then timers or immediate.

### Q5. What is blocking vs non-blocking code?
Blocking code stops the thread until it finishes (`fs.readFileSync`, a heavy loop, `JSON.parse` of a huge string). Non-blocking code starts the work and continues. In a server, one blocking call makes all users wait.

### Q6. When is Node a good choice and when is it not?
**Good:** REST APIs, real-time apps (chat, notifications), streaming, microservices, tools with lots of I/O. **Not ideal:** CPU heavy work (video processing, big calculations, ML) unless you use worker threads or a separate service.

### Q7. What is libuv?
A C library that gives Node the event loop, the thread pool (default size 4) and cross-platform async I/O (files, DNS, network).

### Q8. CommonJS vs ES Modules?
CommonJS: `require` and `module.exports`, loaded synchronously, the default in older Node projects. ES Modules: `import` and `export`, async loading, enabled with `"type": "module"` in `package.json` or `.mjs` files. ESM supports top-level `await`.

### Q9. How does `require` work? What is module caching?
Node loads the module file once, runs it, and caches the exported object. Later `require` calls return the cached object, so modules behave like singletons (this is why a database connection module is created only once).

### Q10. What are `process`, `__dirname`, `Buffer` and `global`?
`process` gives info and control of the running process (`process.env`, `process.argv`, `process.exit`). `__dirname` is the folder of the current file (CommonJS). `Buffer` handles raw binary data. `global` is the global object (like `window` in browsers).

### Q11. What is the error-first callback pattern?
Node callbacks receive the error as the first argument: `fs.readFile(path, (err, data) => { if (err) return handle(err); use(data); })`. Today we use promises and async/await instead, and `util.promisify` converts old callback functions to promises.

### Q12. What is EventEmitter?
A class from the `events` module for the publish and subscribe pattern. Many Node objects (streams, servers, sockets) are event emitters.
```js
const EventEmitter = require("events");
const bus = new EventEmitter();
bus.on("order", (id) => console.log("Order received", id));
bus.emit("order", 101);
```

### Q13. What are streams? Why use them?
Streams process data in chunks instead of loading everything into memory. Types: **Readable**, **Writable**, **Duplex**, **Transform**. Use them for big files, uploads, downloads and piping. Example: streaming a 2GB file to the client with `fs.createReadStream(file).pipe(res)` uses very little memory, while `readFile` would load all 2GB into memory.

### Q14. What is a Buffer?
A fixed-size chunk of memory for binary data (files, network packets, images). Strings can be converted: `Buffer.from("hello").toString("base64")`.

### Q15. `fs.readFile` vs `fs.readFileSync` vs `fs.promises.readFile`?
`readFileSync` blocks the event loop, so use it only at startup. `readFile` uses a callback. `fs.promises.readFile` returns a promise, best with async/await.

### Q16. `child_process`, `cluster` and `worker_threads`?
- `child_process` runs another program or script in a separate process.
- `cluster` starts multiple Node processes (one per CPU core) sharing one port, to use all cores.
- `worker_threads` run JavaScript in parallel threads inside the same process, good for CPU heavy tasks.
PM2's cluster mode gives you the cluster behaviour without writing code.

### Q17. How do you handle environment variables and config?
Store config in environment variables (`process.env.PORT`), load a `.env` file in development with `dotenv`, never commit `.env`, and keep different config per environment. Secrets in production should come from a secret manager (AWS Secrets Manager or Parameter Store).

### Q18. How do you handle uncaught exceptions and unhandled rejections?
Listen to `process.on("unhandledRejection")` and `process.on("uncaughtException")` to log the error and shut down gracefully, because the process may be in an unknown state. A process manager (PM2, Docker, Kubernetes) restarts it. Prevent them with proper `try/catch` and an async error wrapper.

### Q19. What is graceful shutdown?
On `SIGTERM`, stop accepting new requests, finish current requests, close database connections, then exit. This avoids dropped requests during deployments.
```js
process.on("SIGTERM", () => {
  server.close(() => { mongoose.connection.close(false).then(() => process.exit(0)); });
});
```

### Q20. What causes memory leaks in Node and how do you find them?
Global variables that keep growing, unbounded caches, event listeners never removed, timers and intervals never cleared, closures holding big objects. Find them with `process.memoryUsage()`, heap snapshots in Chrome DevTools (`node --inspect`), and monitoring memory over time.

### Q21. How do you create a basic server without Express?
```js
const http = require("http");
const server = http.createServer((req, res) => {
  if (req.method === "GET" && req.url === "/health") {
    res.writeHead(200, { "Content-Type": "application/json" });
    return res.end(JSON.stringify({ status: "ok" }));
  }
  res.writeHead(404);
  res.end();
});
server.listen(3000);
```
Express is a thin layer over this, adding routing, middleware and helpers.

### Q22. `npm` vs `npx` vs `yarn`? `dependencies` vs `devDependencies`?
`npm` installs packages. `npx` runs a package without installing it globally. `yarn` and `pnpm` are alternative package managers. `dependencies` are needed at runtime, `devDependencies` only for development (nodemon, jest, eslint). `package-lock.json` pins exact versions.

### Q23. What is `nodemon` and `PM2`?
`nodemon` restarts the app when files change (development). **PM2** is a production process manager: restarts on crash, runs in cluster mode, keeps logs, starts on boot.

### Q24. How is Node.js different from a traditional multi-threaded server (like Java Spring)?
Traditional servers often use one thread per request, which uses more memory under many connections. Node uses an event loop with non-blocking I/O, so it handles many concurrent connections with less memory, but CPU heavy tasks block it.

### Q25. What is the difference between `Promise.all` and sequential `await`? (very common)
Sequential `await` runs one after another (slower). `Promise.all` starts all at once and waits for all (faster when calls are independent).
```js
const [user, orders] = await Promise.all([getUser(id), getOrders(id)]);
```

---

## 4. Node.js output based questions

**Q1. Event loop order**
```js
console.log("1");
setTimeout(() => console.log("2"), 0);
setImmediate(() => console.log("3"));
process.nextTick(() => console.log("4"));
Promise.resolve().then(() => console.log("5"));
console.log("6");
```
**Answer:** `1 6 4 5` first. Then `2` and `3`. In the main module the order of timeout and immediate can vary. Inside an I/O callback, `setImmediate` always runs before `setTimeout`.

**Q2. Module caching**
```js
// counter.js
let count = 0;
module.exports = { inc: () => ++count };
// a.js: require("./counter").inc();  b.js: require("./counter").inc();
```
**Answer:** both files share the same module instance, so the second call returns `2`.

**Q3. `module.exports` vs `exports`**
`exports` is only a shortcut that points to `module.exports`. If you write `exports = {...}` you break the link and nothing is exported. Always use `module.exports = ...` when exporting a single object or function.

**Q4. Error not caught**
```js
app.get("/user", async (req, res) => {
  const user = await User.findById("bad-id");   // throws
  res.json(user);
});
```
In Express 4, a rejected promise inside an async handler is not passed to the error middleware, so the request may hang and the process may log an unhandled rejection. Fix with `try/catch` and `next(err)`, or an `asyncHandler` wrapper. (Express 5 forwards rejected promises automatically.)

**Q5. Blocking the loop**
```js
app.get("/heavy", (req, res) => {
  let sum = 0;
  for (let i = 0; i < 1e10; i++) sum += i;
  res.send(String(sum));
});
```
**Answer:** while this runs, no other request can be served. Move it to a worker thread or a separate service.

**Q6. Callback vs promise order**
```js
setTimeout(() => console.log("timeout"), 0);
fs.readFile(__filename, () => console.log("file"));
Promise.resolve().then(() => console.log("promise"));
```
**Answer:** `promise` first. `timeout` and `file` come later, and their order depends on timing.

**Q7. `await` in a loop**
```js
for (const id of ids) { await save(id); }          // sequential
await Promise.all(ids.map(id => save(id)));        // parallel
```
Choose parallel when order does not matter, but limit concurrency for very large lists to avoid overloading the database.

**Q8. `JSON.stringify` surprises**
`JSON.stringify({ a: undefined, b: () => {}, c: new Date(0) })` gives `{"c":"1970-01-01T00:00:00.000Z"}`. Functions and `undefined` are dropped, and dates become strings.

---
## 5. Express.js questions

### Q1. What is Express? Why use it?
A minimal, fast web framework for Node.js. It gives routing, middleware, request and response helpers, and a large ecosystem, so we write less boilerplate than with the plain `http` module.

### Q2. What is middleware? Types?
A function `(req, res, next)` that runs during the request-response cycle. Types: **application-level** (`app.use`), **router-level** (`router.use`), **built-in** (`express.json()`, `express.static()`), **third-party** (`cors`, `helmet`, `morgan`), **error-handling** (four arguments `(err, req, res, next)`).

### Q3. How does the request flow through Express?
Request goes through middleware in the order registered (logging, CORS, body parsing, auth), then to the matching route handler, which sends a response. If any middleware calls `next(err)`, Express skips to the error-handling middleware.

### Q4. What does `next()` do? What is `next(err)`?
`next()` passes control to the next middleware. `next(err)` skips normal middleware and jumps to the error-handling middleware.

### Q5. `app.use()` vs `app.get()`?
`app.use(path, fn)` runs for all methods and any path that starts with `path` (middleware). `app.get(path, fn)` runs only for GET requests on that exact route.

### Q6. `req.params` vs `req.query` vs `req.body`?
- `req.params`: route parameters, `/users/:id` gives `req.params.id`.
- `req.query`: query string, `/users?role=doctor&page=2` gives `req.query.role`.
- `req.body`: request payload (needs `express.json()`).

### Q7. How do you structure an Express project?
Layered structure, for example:
```
src/
  config/        (db, env)
  models/        (Mongoose schemas)
  routes/        (URL to controller mapping)
  controllers/   (handle req and res)
  services/      (business logic)
  middlewares/   (auth, validate, error)
  utils/         (helpers, asyncHandler, ApiError)
  app.js         (express app)
  server.js      (starts the server)
```
Keep controllers thin and put logic in services, which makes testing easier. Separate `app.js` and `server.js` so tests can import the app without starting the server.

### Q8. How do you handle errors in Express?
Use a custom error class, an async wrapper to catch rejected promises, and one central error-handling middleware that sends a consistent JSON response. Handle 404 for unknown routes before the error handler.

### Q9. What is `express.Router`?
A mini application that groups related routes (`/users`, `/appointments`) in separate files, then mounts them with `app.use("/api/users", userRouter)`.

### Q10. What is CORS and how do you set it up?
Browsers block calls from one origin to another unless the server allows it. Use the `cors` package and allow only your frontend origin, with credentials if you use cookies.
```js
app.use(cors({ origin: process.env.CLIENT_URL, credentials: true }));
```

### Q11. How do you serve static files?
`app.use(express.static("public"))`. For production, prefer Nginx, S3 or a CDN.

### Q12. How do you handle file uploads?
`multer` middleware parses `multipart/form-data`. Validate file type and size, store on disk or memory, then upload to S3 or Cloudinary. Never trust the file name or mime type from the client alone.

### Q13. How do you do logging?
`morgan` for HTTP request logs and `winston` or `pino` for application logs with levels and JSON format. Never log passwords, tokens or card data.

### Q14. What is `helmet`?
Middleware that sets secure HTTP headers (like `X-Content-Type-Options` and `Strict-Transport-Security`) to reduce common attacks.

### Q15. What is rate limiting? Why?
Limiting how many requests a client can make in a time window, to stop brute force login attacks, scraping and abuse. Use `express-rate-limit`, with a Redis store when you run several servers.

### Q16. How do you validate request data?
Use a schema library (Zod, Joi, express-validator) in a validation middleware. Reject bad input with `400` or `422` before it reaches your controller or database. Never trust client input.

### Q17. How do you version an API?
Prefix the path: `/api/v1/users`. When you make breaking changes, create `/api/v2` and keep the old version for a while.

### Q18. How do you test Express APIs?
Jest (test runner) and **Supertest** (sends HTTP requests to the app without starting a server), with a test database or an in-memory MongoDB. Test success, validation errors, auth failures and edge cases.
```js
const request = require("supertest");
const app = require("../src/app");
test("GET /health", async () => {
  const res = await request(app).get("/health");
  expect(res.statusCode).toBe(200);
});
```

### Q19. How does Express handle cookies and sessions?
`cookie-parser` reads cookies. `express-session` stores a session id in a cookie and the session data on the server (memory in dev, Redis in production). With JWT you can skip server sessions.

### Q20. Express vs Fastify vs NestJS?
Express is minimal and the most popular. Fastify is faster with built-in schema validation. NestJS is a structured, TypeScript-first framework (modules, dependency injection) good for large teams.

---

## 6. REST API design

### Q1. What is REST?
An architectural style for APIs: resources identified by URLs, standard HTTP methods for actions, stateless requests (each request carries all the information needed), and JSON responses.

### Q2. Good URL design
Use nouns, plural, and nesting only when it makes sense.
```
GET    /api/v1/appointments          list (with filters and pagination)
GET    /api/v1/appointments/:id      one item
POST   /api/v1/appointments          create
PUT    /api/v1/appointments/:id      replace
PATCH  /api/v1/appointments/:id      partial update
DELETE /api/v1/appointments/:id      delete
GET    /api/v1/doctors/:id/appointments   nested resource
```
Avoid verbs like `/getAllUsers`.

### Q3. PUT vs PATCH? POST vs PUT?
`PUT` replaces the whole resource (idempotent). `PATCH` updates part of it. `POST` creates (not idempotent: two calls create two items). Idempotent means repeating the same request gives the same result.

### Q4. Important status codes
`200` OK, `201` Created, `204` No Content, `400` Bad Request, `401` Unauthorized (not logged in), `403` Forbidden (logged in but not allowed), `404` Not Found, `409` Conflict (duplicate), `422` Validation error, `429` Too Many Requests, `500` Server Error.

### Q5. Consistent response format
```json
{ "success": true, "data": { }, "message": "Appointment created" }
{ "success": false, "error": { "code": "VALIDATION_ERROR", "message": "Email is required" } }
```

### Q6. Pagination, filtering, sorting
`GET /users?page=2&limit=10&sort=-createdAt&role=doctor&search=ravi`. Return `total`, `page`, `limit`, `totalPages`. Offset pagination (`skip` and `limit`) is simple but slow for very large offsets. **Cursor pagination** (use the last id or date) is faster and stable for big data.

### Q7. What is idempotency and why does it matter?
An operation that gives the same result when repeated. It matters for retries on network failure (for example payments). Use an idempotency key so a retried payment request does not charge twice.

### Q8. REST vs GraphQL vs gRPC?
REST is simple and cacheable. GraphQL lets the client ask for exactly the fields it needs (fewer round trips, more server complexity). gRPC is binary and fast, used between internal services.

### Q9. How do you design an API for a long running task (like document processing)?
Return `202 Accepted` with a job id, process in the background (queue such as BullMQ), expose `GET /jobs/:id` for status, and optionally notify through webhook or WebSocket.

### Q10. How do you handle API documentation?
Swagger / OpenAPI (`swagger-ui-express`) or a Postman collection, so the frontend team always knows the request and response shapes.

---

## 7. Authentication, authorization and security

### Q1. Authentication vs authorization?
Authentication is "who are you" (login). Authorization is "what are you allowed to do" (roles and permissions).

### Q2. How does JWT authentication work? (very common)
1. User logs in with email and password.
2. Server checks the password hash, then creates a signed JWT containing user id and role.
3. Client sends the token in the `Authorization: Bearer <token>` header (or an HttpOnly cookie).
4. Server middleware verifies the signature and expiry, attaches the user to `req`, and continues.

**Say it like this:** "A JWT is a signed token with header, payload and signature. The server can verify it without storing a session. Because the payload is only encoded, not encrypted, I never put sensitive data in it."

### Q3. Structure of a JWT?
Three base64url parts: **header** (algorithm), **payload** (claims such as `sub`, `role`, `exp`), **signature** (proof the token was not changed).

### Q4. Access token vs refresh token?
Access token: short-lived (for example 15 minutes), sent with each API call. Refresh token: long-lived (days), used only to get a new access token, stored in an HttpOnly cookie, and stored (hashed) on the server so it can be revoked. Rotate the refresh token on every use.

### Q5. How do you hash passwords?
With **bcrypt** (or argon2): it adds a random salt and is deliberately slow, which makes brute force expensive. Never store plain text or use fast hashes like MD5 or SHA256 for passwords.
```js
const hash = await bcrypt.hash(password, 10);
const ok = await bcrypt.compare(password, user.password);
```

### Q6. What is RBAC?
Role-Based Access Control: permissions are given to roles (user, doctor, admin), and users get roles. In Express, an `authorize(...roles)` middleware checks `req.user.role` after authentication.

### Q7. Where should the client store the token?
HttpOnly + Secure + SameSite cookie is safest against XSS. localStorage is simple but can be stolen if the site has an XSS bug. With cookies, protect against CSRF using SameSite and CSRF tokens.

### Q8. How do you log a user out with JWT?
JWT is stateless, so you cannot delete it from the server. Options: delete it on the client, keep access tokens short, revoke refresh tokens in the database, or keep a token blocklist (for example in Redis) until expiry.

### Q9. OWASP top issues and how you prevent them
- **Injection (SQL and NoSQL):** use parameterized queries and ORMs, validate input, sanitize Mongo operators (`express-mongo-sanitize`).
- **Broken authentication:** hashed passwords, rate limiting, short token life, MFA for admins.
- **Broken access control:** check permissions on the server for every request, and check that the user owns the resource (`appointment.userId === req.user.id`).
- **XSS:** escape output, sanitize HTML, set CSP headers.
- **CSRF:** SameSite cookies, CSRF tokens.
- **Sensitive data exposure:** HTTPS everywhere, no secrets in code, never return password fields.
- **Security misconfiguration:** `helmet`, remove stack traces in production, keep dependencies updated (`npm audit`).

### Q10. What is NoSQL injection?
A request like `{"email": {"$ne": null}, "password": {"$ne": null}}` can bypass login if you pass `req.body` straight into `User.findOne(req.body)`. Prevent by validating types (email must be a string), sanitizing, and never passing raw input to queries.

### Q11. How do you secure an API key or secret?
Keep it in environment variables or a secret manager, never in the repository or frontend code. Rotate it if exposed.

### Q12. What is HTTPS and why does it matter?
HTTP over TLS. It encrypts data in transit, so passwords and tokens cannot be read on the network. Terminate TLS at the load balancer (ALB) or Nginx.

### Q13. How do you implement "forgot password"?
Generate a random token, store its hash and expiry in the database, email a link with the token, verify the token and expiry when the user sets the new password, and then invalidate the token. Give the same response whether the email exists or not (do not leak which emails are registered).

### Q14. How do you handle brute force login attacks?
Rate limit the login route, add a delay or temporary account lock after repeated failures, use CAPTCHA, and log suspicious activity.

---

## 8. MongoDB and Mongoose

### Q1. What is MongoDB? SQL vs NoSQL?
A document database that stores JSON-like documents (BSON) in collections, with a flexible schema. SQL databases use fixed tables and relations with strong consistency and joins. Choose MongoDB for flexible, nested, fast-changing data; choose SQL for strongly related data and complex transactions.

### Q2. What is Mongoose?
An ODM (Object Data Modeling) library for MongoDB in Node. It gives schemas, validation, middleware (hooks), virtuals and query helpers.

### Q3. Schema and model example
```js
const appointmentSchema = new mongoose.Schema({
  patient: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true },
  doctor:  { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true },
  date:    { type: Date, required: true },
  slot:    { type: String, required: true },
  status:  { type: String, enum: ["pending", "confirmed", "cancelled", "completed"], default: "pending" },
}, { timestamps: true });

appointmentSchema.index({ doctor: 1, date: 1, slot: 1 }, { unique: true });
module.exports = mongoose.model("Appointment", appointmentSchema);
```

### Q4. Embed vs reference?
**Embed** when data is read together, is small and does not grow without limit (address inside user). **Reference** when data is large, shared, or grows (appointments of a user). MongoDB documents have a 16MB limit.

### Q5. What is `populate`?
Replaces a referenced id with the actual document (like a join): `Appointment.find().populate("doctor", "name specialty")`. Select only needed fields. Populate runs extra queries, so be careful in large lists.

### Q6. What are indexes? How do they speed up queries?
An index is a sorted data structure (B-tree) on one or more fields, so MongoDB finds documents without scanning the whole collection. Use `explain("executionStats")` to check for `IXSCAN` (good) vs `COLLSCAN` (bad). Indexes speed up reads but slow writes and use memory, so add them only for fields you filter and sort on.

### Q7. What is a compound index and field order?
An index on several fields, like `{ doctor: 1, date: 1 }`. It supports queries that use the leftmost fields first. Put equality fields first, then sort or range fields.

### Q8. What does `lean()` do?
Returns plain JavaScript objects instead of full Mongoose documents, which is faster and uses less memory. Use it for read-only responses.

### Q9. Aggregation pipeline example
```js
// Appointments per doctor this month
Appointment.aggregate([
  { $match: { date: { $gte: startOfMonth }, status: "completed" } },
  { $group: { _id: "$doctor", total: { $sum: 1 } } },
  { $sort: { total: -1 } },
  { $limit: 5 },
]);
```
Stages: `$match`, `$group`, `$sort`, `$project`, `$lookup` (join), `$unwind`, `$limit`.

### Q10. Common query operators
`$eq $ne $gt $gte $lt $lte $in $nin $and $or $exists $regex`, and update operators `$set $inc $push $pull $addToSet`.

### Q11. Does MongoDB support transactions?
Yes, multi-document ACID transactions work on replica sets (and sharded clusters). Use `session.withTransaction()`. Prefer designing documents so most operations touch a single document, which is atomic by default.

### Q12. How do you handle duplicate key errors?
Catch error code `11000` and return `409 Conflict` with a friendly message.

### Q13. Pagination in Mongoose
```js
const page = Number(req.query.page) || 1;
const limit = Math.min(Number(req.query.limit) || 10, 100);
const items = await Model.find(filter).sort("-createdAt").skip((page - 1) * limit).limit(limit).lean();
const total = await Model.countDocuments(filter);
```

### Q14. What are Mongoose middleware (hooks)?
Functions that run before or after operations, like `pre("save")` to hash a password before saving.

### Q15. How do you improve MongoDB query performance? (your 40% story)
Add indexes on filter and sort fields, use projection (select only needed fields), `lean()`, pagination, avoid large `$in` lists and unbounded `populate`, avoid regex without an anchor on big collections, and check `explain()` plans.

---

## 9. PostgreSQL with Node.js

### Q1. How do you connect Node to PostgreSQL?
Use the `pg` library with a **connection pool**, or an ORM/query builder (Prisma, Sequelize, TypeORM, Knex).
```js
const { Pool } = require("pg");
const pool = new Pool({ connectionString: process.env.DATABASE_URL, max: 10 });
const { rows } = await pool.query("SELECT * FROM farms WHERE id = $1", [farmId]);
```

### Q2. Why use parameterized queries?
`$1, $2` placeholders send the values separately from the SQL, so user input cannot change the query. This prevents SQL injection. Never build SQL by joining strings.

### Q3. What is a connection pool?
A set of reusable database connections. Opening a connection for every request is slow, so the pool keeps some open and hands them out. Set a sensible `max` so you do not exceed the database's connection limit.

### Q4. Transaction example
```js
const client = await pool.connect();
try {
  await client.query("BEGIN");
  await client.query("UPDATE accounts SET balance = balance - $1 WHERE id = $2", [amount, from]);
  await client.query("UPDATE accounts SET balance = balance + $1 WHERE id = $2", [amount, to]);
  await client.query("COMMIT");
} catch (e) {
  await client.query("ROLLBACK");
  throw e;
} finally {
  client.release();
}
```

### Q5. Modeling a hierarchy (your ERP)
```sql
CREATE TABLE farms  (id SERIAL PRIMARY KEY, name TEXT NOT NULL);
CREATE TABLE blocks (id SERIAL PRIMARY KEY, farm_id INT REFERENCES farms(id) ON DELETE CASCADE, name TEXT);
CREATE TABLE rows   (id SERIAL PRIMARY KEY, block_id INT REFERENCES blocks(id) ON DELETE CASCADE, name TEXT);
CREATE TABLE beds   (id SERIAL PRIMARY KEY, row_id INT REFERENCES rows(id) ON DELETE CASCADE, name TEXT);
CREATE INDEX idx_blocks_farm ON blocks(farm_id);
```
Foreign keys keep data valid. Index foreign key columns used in joins. Joins fetch the hierarchy in one query: `SELECT ... FROM farms f JOIN blocks b ON b.farm_id = f.id JOIN rows r ON r.block_id = b.id`.

### Q6. SQL vs ORM
ORMs speed up development and prevent injection, but can generate inefficient queries (N+1). Raw SQL gives full control for complex reports. Many teams use both.

### Q7. What is the N+1 query problem?
Fetching a list (1 query) and then running one extra query per item (N queries). Fix with a join, `populate` with select, or batching with `WHERE id = ANY($1)`.

---

## 10. Real-time: WebSockets and Socket.io

### Q1. HTTP vs WebSocket?
HTTP is request and response, and the server cannot push data by itself. WebSocket is one persistent two-way connection, so the server can push messages instantly (chat, live status, notifications).

### Q2. Socket.io vs plain WebSocket?
Socket.io adds automatic reconnection, fallback transports, rooms and namespaces, acknowledgements and broadcasting, on top of WebSockets.

### Q3. How do you authenticate a socket connection?
Send the JWT in the handshake (`auth: { token }`), verify it in a Socket.io middleware (`io.use`), and attach the user to the socket. Reject unauthorized connections.

### Q4. What are rooms?
Named groups of sockets. A socket can join `user:123` or `channel:general`, and you send to everyone in the room with `io.to(room).emit(...)`. Use a room per user to send private notifications.

### Q5. How do you scale Socket.io across servers?
Each server only knows its own sockets. Use the **Redis adapter** so events are shared between servers, and use sticky sessions at the load balancer (or WebSocket-only transport).

### Q6. How would real-time appointment tracking work?
When the appointment status changes in the API, the server emits `appointment:update` to the room of the patient and the doctor. The client updates its state. The database stays the source of truth, and the client refetches if it reconnects.

### Q7. WebSocket vs polling vs SSE?
Polling asks repeatedly (simple, wasteful). Server-Sent Events push one-way over HTTP (good for live feeds). WebSockets are two-way (chat, games).

---

## 11. Performance, scaling and deployment

### Q1. How do you make a Node API faster?
Add database indexes, paginate, select only needed fields, cache with Redis, avoid blocking code, use `Promise.all` for independent calls, enable gzip (`compression`), keep connections pooled, run in cluster mode, and move heavy work to queues or workers.

### Q2. What is caching? How would you use Redis?
Store frequently read, rarely changing data in memory (Redis) to avoid repeated slow database calls. Pattern: check the cache, if missing read from DB, store it with a TTL, return it. Invalidate or update the cache when the data changes.
```js
const cached = await redis.get(`doctor:${id}`);
if (cached) return JSON.parse(cached);
const doctor = await Doctor.findById(id).lean();
await redis.set(`doctor:${id}`, JSON.stringify(doctor), "EX", 300);
return doctor;
```

### Q3. How do you scale a Node application?
Vertically (bigger server) or horizontally (more instances behind a load balancer). Make the app **stateless** (no session data in memory), use shared stores (Redis, database), use PM2 or cluster to use all CPU cores, and add Auto Scaling on AWS.

### Q4. What are background jobs and queues?
Slow tasks (emails, PDF generation, document processing) run outside the request using a queue (BullMQ with Redis, or SQS). The API responds fast, and workers process jobs with retries.

### Q5. How did you deploy on AWS? (your eAsha story)
Node app on EC2, managed by **PM2**. **Nginx** as reverse proxy (also handles gzip and static files). **ALB** distributes traffic and does health checks and HTTPS. Database on MongoDB or RDS. Security groups allow only needed ports.

**Nginx reverse proxy config:**
```nginx
server {
  listen 80;
  server_name example.com;
  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;     # needed for WebSockets
    proxy_set_header Connection "upgrade";
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
  }
}
```

### Q6. Why a reverse proxy (Nginx)?
Handles TLS, compression, static files, rate limiting and load balancing, and hides the Node process from the internet. Node listens on a local port only.

### Q7. PM2 commands
```bash
pm2 start server.js -i max --name api    # cluster mode on all cores
pm2 list
pm2 logs api
pm2 restart api
pm2 save && pm2 startup                  # restart after server reboot
```

### Q8. Dockerfile for a Node app
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
CMD ["node", "src/server.js"]
```
Copy `package.json` first so Docker caches the install layer. Use `.dockerignore` for `node_modules` and `.env`.

### Q9. What is a health check endpoint?
`GET /health` returns `200` when the app and database are fine. Load balancers and Kubernetes use it to decide whether to send traffic to an instance.

### Q10. What is CI/CD for a Node app?
On every push: install, lint, test, build a Docker image, push to a registry (ECR), and deploy (Jenkins, GitHub Actions, CodeDeploy). Deploy with zero downtime using rolling updates behind the load balancer.

### Q11. How do you monitor a Node app in production?
Logs (CloudWatch), metrics (CPU, memory, request time, error rate with Prometheus and Grafana), alerts, error tracking (Sentry), and uptime checks on the health endpoint.

---
## 12. Scenario based questions

For each scenario: **name the problem, give the fix, mention a trade-off.**

**S1. The API is slow. How do you find the cause?**
Measure first: check logs with response times, find slow endpoints, check database query plans (`explain`), look at CPU and memory. Common fixes: missing index, N+1 queries, large payloads (add pagination and field selection), blocking code, no caching, many sequential awaits that could be parallel.

**S2. Two users try to book the same appointment slot at the same time.**
Do not rely on "check, then insert" in code, because both requests can pass the check. Use a **unique index** on `(doctor, date, slot)` so the database accepts only one. Catch duplicate key error `11000` and return `409 Conflict`. For SQL, use a unique constraint or `SELECT ... FOR UPDATE` inside a transaction.

**S3. A payment was deducted but the booking shows unpaid.**
Never trust only the frontend callback. Verify the signature on the server and also listen to the payment gateway **webhook**. Make the webhook **idempotent** (check whether the order is already marked paid). Keep an order record with a status (created, paid, failed) and a reconciliation job for mismatches.

**S4. How do you handle a request that takes 2 minutes (for example processing a large document)?**
Do not hold the HTTP request open. Accept the upload, return `202` with a job id, process in a background worker or queue (BullMQ), save the progress, and let the client poll `GET /jobs/:id` or receive a socket event.

**S5. Your server crashes under load. What do you check?**
Memory growth (leak), CPU at 100% (blocking code), database connection pool exhausted, unhandled promise rejections, too many open file descriptors. Use PM2 or Docker restarts to recover, add health checks, and fix the root cause with profiling.

**S6. How do you protect login from brute force attacks?**
Rate limit by IP and by account, add increasing delays or temporary lock after repeated failures, use bcrypt (slow hashing), return the same error for wrong email or wrong password, and add CAPTCHA or MFA for sensitive roles.

**S7. How do you handle file uploads of 500MB?**
Do not buffer in memory. Stream to disk or directly to S3 using a **pre-signed URL** so the file goes from the browser to S3 without passing through your server. Validate type and size, and scan if needed.

**S8. How do you design role-based access for User, Doctor and Admin?**
Put the role in the JWT, use `protect` then `authorize("doctor")` middlewares, and also check ownership inside the controller (a doctor may only see their own appointments). Never rely on frontend hiding.

**S9. A bug appears only in production. How do you debug?**
Check structured logs with request ids, error tracking (Sentry), environment variables and config differences, database data differences, and reproduce locally with a production-like setup. Add more logging around the failing path and deploy carefully.

**S10. How would you deploy with zero downtime?**
Run multiple instances behind a load balancer with health checks. Deploy one instance at a time (rolling update), or blue-green (new environment, then switch traffic). Use graceful shutdown so in-flight requests finish. Run database migrations in a backwards-compatible way.

**S11. How do you store and secure sensitive data (like medical documents)?**
Encrypt in transit (HTTPS) and at rest (S3 encryption, database encryption), restrict access with IAM and signed URLs with short expiry, log access, never expose files publicly, and follow least privilege.

**S12. How would you add search to an API?**
For small data: a regex or text index in the database with pagination. For large data and relevance ranking: Elasticsearch or OpenSearch. Always debounce on the frontend and limit page size on the backend.

**S13. How would you send emails or notifications without slowing the API?**
Push a job to a queue and return the response immediately. A worker sends the email with retries. Use a provider (SES, SendGrid) and store the status.

**S14. The database has grown and queries are slow.**
Check indexes with `explain`, archive old data, add pagination, use read replicas for reporting, cache hot reads, and consider partitioning or sharding only at very large scale.

**S15. How would you design a notification system?**
Events (new appointment, status change) are published, saved as notification records, and pushed in real time with Socket.io rooms (`user:<id>`). Offline users read them from the database on login. Add read and unread status and pagination.

---

## 13. Live coding: project setup and CRUD API

**Practise:** build this from an empty folder in 15 to 20 minutes. This is the most likely live coding task.

### Setup
```bash
mkdir api && cd api
npm init -y
npm i express mongoose dotenv cors helmet morgan bcryptjs jsonwebtoken zod express-rate-limit multer
npm i -D nodemon
```
`package.json` scripts:
```json
"scripts": { "start": "node src/server.js", "dev": "nodemon src/server.js" }
```
`.env`:
```
PORT=3000
MONGO_URI=mongodb://127.0.0.1:27017/interview
JWT_SECRET=change_me
JWT_EXPIRES_IN=15m
CLIENT_URL=http://localhost:5173
```

### src/utils/ApiError.js and asyncHandler.js
```js
// ApiError.js
class ApiError extends Error {
  constructor(statusCode, message) {
    super(message);
    this.statusCode = statusCode;
  }
}
module.exports = ApiError;

// asyncHandler.js (catches rejected promises and sends them to the error middleware)
module.exports = (fn) => (req, res, next) => Promise.resolve(fn(req, res, next)).catch(next);
```

### src/config/db.js
```js
const mongoose = require("mongoose");

module.exports = async function connectDB() {
  await mongoose.connect(process.env.MONGO_URI);
  console.log("MongoDB connected");
};
```

### src/models/Todo.js
```js
const mongoose = require("mongoose");

const todoSchema = new mongoose.Schema(
  {
    title: { type: String, required: [true, "Title is required"], trim: true, maxlength: 100 },
    done: { type: Boolean, default: false },
    owner: { type: mongoose.Schema.Types.ObjectId, ref: "User" },
  },
  { timestamps: true }
);

module.exports = mongoose.model("Todo", todoSchema);
```

### src/controllers/todoController.js
```js
const Todo = require("../models/Todo");
const ApiError = require("../utils/ApiError");
const asyncHandler = require("../utils/asyncHandler");

exports.getTodos = asyncHandler(async (req, res) => {
  const page = Math.max(Number(req.query.page) || 1, 1);
  const limit = Math.min(Number(req.query.limit) || 10, 100);
  const filter = {};
  if (req.query.done) filter.done = req.query.done === "true";
  if (req.query.search) filter.title = { $regex: req.query.search, $options: "i" };

  const [items, total] = await Promise.all([
    Todo.find(filter).sort("-createdAt").skip((page - 1) * limit).limit(limit).lean(),
    Todo.countDocuments(filter),
  ]);
  res.json({ success: true, data: items, page, limit, total, totalPages: Math.ceil(total / limit) });
});

exports.getTodo = asyncHandler(async (req, res) => {
  const todo = await Todo.findById(req.params.id);
  if (!todo) throw new ApiError(404, "Todo not found");
  res.json({ success: true, data: todo });
});

exports.createTodo = asyncHandler(async (req, res) => {
  const todo = await Todo.create({ title: req.body.title });
  res.status(201).json({ success: true, data: todo });
});

exports.updateTodo = asyncHandler(async (req, res) => {
  const todo = await Todo.findByIdAndUpdate(req.params.id, req.body, { new: true, runValidators: true });
  if (!todo) throw new ApiError(404, "Todo not found");
  res.json({ success: true, data: todo });
});

exports.deleteTodo = asyncHandler(async (req, res) => {
  const todo = await Todo.findByIdAndDelete(req.params.id);
  if (!todo) throw new ApiError(404, "Todo not found");
  res.status(204).end();
});
```

### src/routes/todoRoutes.js
```js
const router = require("express").Router();
const c = require("../controllers/todoController");

router.route("/").get(c.getTodos).post(c.createTodo);
router.route("/:id").get(c.getTodo).patch(c.updateTodo).delete(c.deleteTodo);

module.exports = router;
```

### src/middlewares/errorHandler.js
```js
module.exports = (err, req, res, next) => {
  let status = err.statusCode || 500;
  let message = err.message || "Server error";

  if (err.name === "ValidationError") {
    status = 400;
    message = Object.values(err.errors).map((e) => e.message).join(", ");
  }
  if (err.name === "CastError") { status = 400; message = "Invalid id"; }
  if (err.code === 11000) { status = 409; message = "Duplicate value"; }
  if (err.name === "JsonWebTokenError") { status = 401; message = "Invalid token"; }
  if (err.name === "TokenExpiredError") { status = 401; message = "Token expired"; }

  if (process.env.NODE_ENV !== "production") console.error(err);
  res.status(status).json({ success: false, message });
};
```

### src/app.js
```js
const express = require("express");
const cors = require("cors");
const helmet = require("helmet");
const morgan = require("morgan");
const todoRoutes = require("./routes/todoRoutes");
const errorHandler = require("./middlewares/errorHandler");

const app = express();

app.use(helmet());
app.use(cors({ origin: process.env.CLIENT_URL, credentials: true }));
app.use(express.json({ limit: "1mb" }));
if (process.env.NODE_ENV !== "test") app.use(morgan("dev"));

app.get("/health", (req, res) => res.json({ status: "ok" }));
app.use("/api/v1/todos", todoRoutes);

app.use((req, res) => res.status(404).json({ success: false, message: "Route not found" }));
app.use(errorHandler);

module.exports = app;
```

### src/server.js
```js
require("dotenv").config();
const app = require("./app");
const connectDB = require("./config/db");

const PORT = process.env.PORT || 3000;

connectDB()
  .then(() => {
    const server = app.listen(PORT, () => console.log(`Server on ${PORT}`));
    process.on("SIGTERM", () => server.close(() => process.exit(0)));
  })
  .catch((err) => { console.error("DB connection failed", err); process.exit(1); });
```
**Explain while coding:** separate `app.js` and `server.js` (so tests can import the app), layered folders, central error handler, pagination limits, proper status codes (`201`, `204`, `404`).

### Quick version without a database (if they ask for it in 10 minutes)
```js
const express = require("express");
const app = express();
app.use(express.json());

let todos = [];
let nextId = 1;

app.get("/todos", (req, res) => res.json(todos));

app.post("/todos", (req, res) => {
  const { title } = req.body;
  if (!title) return res.status(400).json({ message: "title is required" });
  const todo = { id: nextId++, title, done: false };
  todos.push(todo);
  res.status(201).json(todo);
});

app.put("/todos/:id", (req, res) => {
  const todo = todos.find((t) => t.id === Number(req.params.id));
  if (!todo) return res.status(404).json({ message: "Not found" });
  Object.assign(todo, req.body);
  res.json(todo);
});

app.delete("/todos/:id", (req, res) => {
  const before = todos.length;
  todos = todos.filter((t) => t.id !== Number(req.params.id));
  if (todos.length === before) return res.status(404).json({ message: "Not found" });
  res.status(204).end();
});

app.listen(3000, () => console.log("Running on 3000"));
```

---

## 14. Live coding: authentication and RBAC

### User model
```js
const mongoose = require("mongoose");

const userSchema = new mongoose.Schema(
  {
    name: { type: String, required: true, trim: true },
    email: { type: String, required: true, unique: true, lowercase: true, trim: true },
    password: { type: String, required: true, minlength: 6, select: false }, // hidden by default
    role: { type: String, enum: ["user", "doctor", "admin"], default: "user" },
  },
  { timestamps: true }
);

module.exports = mongoose.model("User", userSchema);
```
`select: false` means the password is not returned unless you ask with `.select("+password")`.

### Auth controller: register and login
```js
const bcrypt = require("bcryptjs");
const jwt = require("jsonwebtoken");
const User = require("../models/User");
const ApiError = require("../utils/ApiError");
const asyncHandler = require("../utils/asyncHandler");

const signToken = (user) =>
  jwt.sign({ id: user._id, role: user.role }, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRES_IN || "15m",
  });

exports.register = asyncHandler(async (req, res) => {
  const { name, email, password } = req.body;
  const exists = await User.findOne({ email });
  if (exists) throw new ApiError(409, "Email already registered");

  const hash = await bcrypt.hash(password, 10);
  // role is never taken from the request body, so users cannot make themselves admin
  const user = await User.create({ name, email, password: hash });

  res.status(201).json({
    success: true,
    token: signToken(user),
    user: { id: user._id, name: user.name, email: user.email, role: user.role },
  });
});

exports.login = asyncHandler(async (req, res) => {
  const { email, password } = req.body;
  const user = await User.findOne({ email }).select("+password");
  // same message for wrong email or wrong password
  if (!user || !(await bcrypt.compare(password, user.password))) {
    throw new ApiError(401, "Invalid email or password");
  }
  res.json({
    success: true,
    token: signToken(user),
    user: { id: user._id, name: user.name, email: user.email, role: user.role },
  });
});

exports.me = asyncHandler(async (req, res) => {
  res.json({ success: true, user: req.user });
});
```

### Auth middleware: protect and authorize
```js
const jwt = require("jsonwebtoken");
const User = require("../models/User");
const ApiError = require("../utils/ApiError");
const asyncHandler = require("../utils/asyncHandler");

exports.protect = asyncHandler(async (req, res, next) => {
  const header = req.headers.authorization;
  if (!header || !header.startsWith("Bearer ")) throw new ApiError(401, "Not logged in");

  const token = header.split(" ")[1];
  const decoded = jwt.verify(token, process.env.JWT_SECRET); // throws if invalid or expired

  const user = await User.findById(decoded.id);
  if (!user) throw new ApiError(401, "User no longer exists");

  req.user = user;
  next();
});

exports.authorize = (...roles) => (req, res, next) => {
  if (!roles.includes(req.user.role)) {
    return next(new ApiError(403, "You do not have permission to do this"));
  }
  next();
};
```

### Using them in routes
```js
const router = require("express").Router();
const { protect, authorize } = require("../middlewares/auth");
const auth = require("../controllers/authController");
const rateLimit = require("express-rate-limit");

const loginLimiter = rateLimit({ windowMs: 15 * 60 * 1000, max: 10 });

router.post("/register", auth.register);
router.post("/login", loginLimiter, auth.login);
router.get("/me", protect, auth.me);

// only admins
router.delete("/users/:id", protect, authorize("admin"), deleteUser);
// doctors and admins
router.get("/patients", protect, authorize("doctor", "admin"), listPatients);
```

### Ownership check (very important)
```js
exports.cancelAppointment = asyncHandler(async (req, res) => {
  const appt = await Appointment.findById(req.params.id);
  if (!appt) throw new ApiError(404, "Appointment not found");

  const isOwner = appt.patient.toString() === req.user.id;
  if (!isOwner && req.user.role !== "admin") throw new ApiError(403, "Not allowed");

  appt.status = "cancelled";
  await appt.save();
  res.json({ success: true, data: appt });
});
```
Role checks are not enough. Always check that the user owns the resource.

### Refresh token flow (explain this)
1. Login returns a short-lived **access token** (15 minutes) and sets a long-lived **refresh token** in an HttpOnly cookie.
2. When the access token expires, the client calls `POST /auth/refresh`.
3. Server verifies the refresh token (and that it exists in the database), issues a new access token, and rotates the refresh token.
4. Logout deletes the refresh token from the database and clears the cookie.

---

## 15. Live coding: validation, errors, pagination, upload

### Validation middleware with Zod
```js
const { z } = require("zod");

const validate = (schema) => (req, res, next) => {
  const result = schema.safeParse(req.body);
  if (!result.success) {
    const message = result.error.issues.map((i) => `${i.path.join(".")}: ${i.message}`).join(", ");
    return res.status(400).json({ success: false, message });
  }
  req.body = result.data;     // cleaned data (unknown fields removed)
  next();
};

const registerSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  password: z.string().min(6),
});

router.post("/register", validate(registerSchema), auth.register);
```

### Appointment booking with double-booking protection
```js
exports.book = asyncHandler(async (req, res) => {
  const { doctorId, date, slot } = req.body;
  try {
    const appt = await Appointment.create({
      patient: req.user.id,
      doctor: doctorId,
      date,
      slot,
    });
    res.status(201).json({ success: true, data: appt });
  } catch (err) {
    if (err.code === 11000) throw new ApiError(409, "This slot is already booked");
    throw err;
  }
});
```
This depends on the unique index on `(doctor, date, slot)` from section 8.

### Prevent NoSQL injection
```js
// Bad: user can send { "email": { "$ne": null } }
User.findOne({ email: req.body.email });

// Good: force it to be a string
User.findOne({ email: String(req.body.email) });
// or validate with Zod (z.string().email()) and use express-mongo-sanitize
```

### File upload with multer
```js
const multer = require("multer");
const path = require("path");

const storage = multer.diskStorage({
  destination: "uploads/",
  filename: (req, file, cb) => cb(null, `${Date.now()}-${Math.round(Math.random() * 1e9)}${path.extname(file.originalname)}`),
});

const upload = multer({
  storage,
  limits: { fileSize: 5 * 1024 * 1024 },                       // 5MB
  fileFilter: (req, file, cb) => {
    const allowed = ["image/jpeg", "image/png", "application/pdf"];
    cb(allowed.includes(file.mimetype) ? null : new Error("Only JPG, PNG or PDF allowed"), allowed.includes(file.mimetype));
  },
});

router.post("/documents", protect, upload.single("file"), (req, res) => {
  if (!req.file) return res.status(400).json({ message: "File is required" });
  res.status(201).json({ filename: req.file.filename, size: req.file.size });
});
```
Note: in production upload to S3 (or use pre-signed URLs) instead of the server disk.

### Search, filter and sort query builder
```js
const buildQuery = (q) => {
  const filter = {};
  if (q.status) filter.status = q.status;
  if (q.from || q.to) {
    filter.date = {};
    if (q.from) filter.date.$gte = new Date(q.from);
    if (q.to) filter.date.$lte = new Date(q.to);
  }
  return filter;
};
const sort = (req.query.sort || "-createdAt").split(",").join(" ");
const items = await Appointment.find(buildQuery(req.query)).sort(sort);
```

### Soft delete (common design question)
Add `isDeleted: { type: Boolean, default: false }` and `deletedAt`. "Delete" sets the flag. Queries filter `isDeleted: false`. This keeps history for audits. Mongoose can apply the filter automatically with a pre-find hook.

---

## 16. Live coding: payments, sockets, streams, PostgreSQL

### Razorpay: create order and verify payment
```js
const Razorpay = require("razorpay");
const crypto = require("crypto");

const razorpay = new Razorpay({
  key_id: process.env.RAZORPAY_KEY_ID,
  key_secret: process.env.RAZORPAY_KEY_SECRET,
});

// 1) Create an order (amount is in paise, so rupees * 100)
exports.createOrder = asyncHandler(async (req, res) => {
  const appt = await Appointment.findById(req.body.appointmentId);
  if (!appt) throw new ApiError(404, "Appointment not found");

  const order = await razorpay.orders.create({
    amount: appt.fee * 100,
    currency: "INR",
    receipt: `appt_${appt._id}`,
  });
  appt.orderId = order.id;
  await appt.save();
  res.json({ success: true, orderId: order.id, amount: order.amount, key: process.env.RAZORPAY_KEY_ID });
});

// 2) Verify the signature sent by the frontend after payment
exports.verifyPayment = asyncHandler(async (req, res) => {
  const { razorpay_order_id, razorpay_payment_id, razorpay_signature } = req.body;

  const expected = crypto
    .createHmac("sha256", process.env.RAZORPAY_KEY_SECRET)
    .update(`${razorpay_order_id}|${razorpay_payment_id}`)
    .digest("hex");

  if (expected !== razorpay_signature) throw new ApiError(400, "Payment verification failed");

  const appt = await Appointment.findOneAndUpdate(
    { orderId: razorpay_order_id },
    { status: "confirmed", paymentId: razorpay_payment_id, paid: true },
    { new: true }
  );
  res.json({ success: true, data: appt });
});
```
**Say:** the secret key stays on the server, the amount comes from the database (not from the client), the signature is checked on the server, and a webhook should also update the status in case the browser closes.

### Socket.io server with JWT authentication
```js
const http = require("http");
const { Server } = require("socket.io");
const jwt = require("jsonwebtoken");
const app = require("./app");

const server = http.createServer(app);
const io = new Server(server, { cors: { origin: process.env.CLIENT_URL, credentials: true } });

io.use((socket, next) => {
  try {
    const token = socket.handshake.auth.token;
    socket.user = jwt.verify(token, process.env.JWT_SECRET);
    next();
  } catch {
    next(new Error("Unauthorized"));
  }
});

io.on("connection", (socket) => {
  socket.join(`user:${socket.user.id}`);               // private room for notifications

  socket.on("join:channel", (channelId) => socket.join(`channel:${channelId}`));

  socket.on("message", ({ channelId, text }) => {
    const msg = { channelId, text, from: socket.user.id, at: Date.now() };
    io.to(`channel:${channelId}`).emit("message", msg);   // send to everyone in the channel
  });

  socket.on("disconnect", () => console.log("disconnected", socket.user.id));
});

// Anywhere in your API code, notify a user of a status change:
// io.to(`user:${patientId}`).emit("appointment:update", { id, status });

server.listen(process.env.PORT || 3000);
```
Client side: `const socket = io(URL, { auth: { token } })`.

### Streams: send a large file and copy with pipeline
```js
const fs = require("fs");
const { pipeline } = require("stream/promises");
const zlib = require("zlib");

// Download a big file without loading it in memory
app.get("/download", (req, res) => {
  res.setHeader("Content-Type", "application/pdf");
  fs.createReadStream("big-report.pdf").pipe(res);
});

// Compress a file using streams (errors are handled by pipeline)
await pipeline(
  fs.createReadStream("input.txt"),
  zlib.createGzip(),
  fs.createWriteStream("input.txt.gz")
);
```

### Read a large file line by line
```js
const readline = require("readline");
const fs = require("fs");

async function countLines(file) {
  const rl = readline.createInterface({ input: fs.createReadStream(file), crlfDelay: Infinity });
  let count = 0;
  for await (const line of rl) count++;
  return count;
}
```

### PostgreSQL CRUD with pg (parameterized)
```js
const { Pool } = require("pg");
const pool = new Pool({ connectionString: process.env.DATABASE_URL });

// GET /farms/:id/blocks
app.get("/farms/:id/blocks", asyncHandler(async (req, res) => {
  const { rows } = await pool.query(
    "SELECT id, name FROM blocks WHERE farm_id = $1 ORDER BY name",
    [req.params.id]
  );
  res.json(rows);
}));

// POST /farms
app.post("/farms", asyncHandler(async (req, res) => {
  const { name } = req.body;
  const { rows } = await pool.query(
    "INSERT INTO farms (name) VALUES ($1) RETURNING *",
    [name]
  );
  res.status(201).json(rows[0]);
}));
```

### Cluster mode (use all CPU cores)
```js
const cluster = require("cluster");
const os = require("os");

if (cluster.isPrimary) {
  os.cpus().forEach(() => cluster.fork());
  cluster.on("exit", () => cluster.fork());     // restart a worker if it dies
} else {
  require("./server");                          // each worker runs the server
}
```
In production, PM2 (`pm2 start server.js -i max`) does this for you.

### Redis cache middleware
```js
const cache = (seconds) => async (req, res, next) => {
  const key = `cache:${req.originalUrl}`;
  const hit = await redis.get(key);
  if (hit) return res.json(JSON.parse(hit));
  const send = res.json.bind(res);
  res.json = (body) => { redis.set(key, JSON.stringify(body), "EX", seconds); return send(body); };
  next();
};
app.get("/api/v1/doctors", cache(60), listDoctors);
```

---

## 17. Small coding problems in Node

### Convert a callback function to a promise
```js
const { promisify } = require("util");
const fs = require("fs");
const readFile = promisify(fs.readFile);
const text = await readFile("data.txt", "utf8");

// manual version
const toPromise = (fn) => (...args) =>
  new Promise((resolve, reject) => fn(...args, (err, data) => (err ? reject(err) : resolve(data))));
```

### Retry with delay
```js
const sleep = (ms) => new Promise((r) => setTimeout(r, ms));

async function retry(fn, times = 3, delay = 500) {
  for (let i = 1; i <= times; i++) {
    try { return await fn(); }
    catch (err) {
      if (i === times) throw err;
      await sleep(delay * i);                  // wait longer each time
    }
  }
}
```

### Run async tasks with a concurrency limit
```js
async function runPool(tasks, limit) {
  const results = [];
  let i = 0;
  async function worker() {
    while (i < tasks.length) {
      const index = i++;
      results[index] = await tasks[index]();
    }
  }
  await Promise.all(Array.from({ length: Math.min(limit, tasks.length) }, worker));
  return results;
}
// await runPool(urls.map(u => () => fetch(u)), 5);
```

### Simple in-memory rate limiter middleware
```js
const hits = new Map();

function rateLimit({ windowMs, max }) {
  return (req, res, next) => {
    const now = Date.now();
    const entry = hits.get(req.ip) || { count: 0, start: now };
    if (now - entry.start > windowMs) { entry.count = 0; entry.start = now; }
    entry.count++;
    hits.set(req.ip, entry);
    if (entry.count > max) return res.status(429).json({ message: "Too many requests" });
    next();
  };
}
app.use(rateLimit({ windowMs: 60000, max: 100 }));
```
Say: this works for one server only. For many servers, store the counters in Redis.

### LRU cache with Map
```js
class LRUCache {
  constructor(capacity) { this.capacity = capacity; this.map = new Map(); }
  get(key) {
    if (!this.map.has(key)) return undefined;
    const value = this.map.get(key);
    this.map.delete(key);          // move to the end (most recently used)
    this.map.set(key, value);
    return value;
  }
  set(key, value) {
    if (this.map.has(key)) this.map.delete(key);
    this.map.set(key, value);
    if (this.map.size > this.capacity) this.map.delete(this.map.keys().next().value);
  }
}
```

### Request logger middleware (write your own)
```js
const logger = (req, res, next) => {
  const start = Date.now();
  res.on("finish", () => {
    console.log(`${req.method} ${req.originalUrl} ${res.statusCode} ${Date.now() - start}ms`);
  });
  next();
};
```

### Timeout middleware
```js
const timeout = (ms) => (req, res, next) => {
  const timer = setTimeout(() => {
    if (!res.headersSent) res.status(503).json({ message: "Request timed out" });
  }, ms);
  res.on("finish", () => clearTimeout(timer));
  next();
};
```

### Group and count data (common in API tasks)
```js
// Orders per status
const count = orders.reduce((acc, o) => { acc[o.status] = (acc[o.status] || 0) + 1; return acc; }, {});

// Total amount per user
const totals = orders.reduce((acc, o) => { acc[o.userId] = (acc[o.userId] || 0) + o.amount; return acc; }, {});
```

---
## 18. Pseudo code guide

The interviewer wants to see your **thinking**, not exact syntax. Write plain English steps that look like code.

**Format**
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

**Example 1: Login API**
```
POST /login with email and password
    VALIDATE email and password exist, ELSE return 400
    FIND user by email (include password hash)
    IF user not found OR bcrypt compare fails THEN return 401 "Invalid email or password"
    CREATE JWT with user id and role, expiry 15 minutes
    RETURN 200 with token and user (without password)
```

**Example 2: Auth middleware**
```
READ Authorization header
IF missing or not starting with "Bearer " THEN return 401
VERIFY token with secret (invalid or expired THEN return 401)
FIND user by id from token
IF user not found THEN return 401
ATTACH user to request
CALL next()
```

**Example 3: Book an appointment without double booking**
```
INPUT: doctorId, date, slot, patient (from token)
VALIDATE inputs
TRY create appointment (database has unique index on doctor + date + slot)
IF duplicate key error THEN return 409 "Slot already booked"
RETURN 201 with appointment
```

**Example 4: Verify a payment**
```
INPUT: order_id, payment_id, signature from client
expected = HMAC_SHA256(order_id + "|" + payment_id, secretKey)
IF expected != signature THEN return 400
FIND appointment by order_id
IF already paid THEN return 200 (idempotent)
SET status = confirmed, paid = true, save payment_id
RETURN 200
```

**Example 5: Pagination**
```
page = query.page OR 1
limit = MIN(query.limit OR 10, 100)
skip = (page - 1) * limit
items = FIND filter, SORT newest first, SKIP skip, LIMIT limit
total = COUNT filter
RETURN items, page, limit, total, totalPages = CEIL(total / limit)
```

**After the pseudo code, always say:**
1. The complexity or the database indexes you would need.
2. One edge case or failure (duplicate, invalid id, empty result, timeout).
3. How you would test it (success, validation error, unauthorized).

---

## 19. Interview day strategy

### Before the round
- Have a ready Express project folder with `express`, `mongoose` and `dotenv` installed, and a local MongoDB (or MongoDB Atlas) so you can start coding quickly.
- Know your resume projects well, especially the backend decisions.
- Test your editor, terminal, internet and screen share.

### During theory questions
- Short definition first, then an example from your project.
- Mention trade-offs (for example "JWT is stateless, but revoking tokens needs extra work").
- If you do not know, say what you know and how you would find out.

### During live coding
1. Ask about the requirement: fields, validation, authentication needed or not.
2. Say the plan: routes, model, controller, error handling.
3. Build the simplest working version (get one route working end to end).
4. Test with Postman, Thunder Client or curl while you code.
5. Add validation, status codes and error handling.
6. Mention what you would add with more time: pagination, tests, rate limiting, logging, Docker.

### Common mistakes to avoid
- Forgetting `app.use(express.json())` (then `req.body` is `undefined`).
- Not handling async errors (missing `try/catch` or `asyncHandler`).
- Returning plain strings with wrong status codes.
- Sending the password hash in responses.
- Taking `role` or `userId` from the request body instead of the token.
- Not checking resource ownership.
- Putting secrets in the code.
- Building SQL strings by concatenation (SQL injection).
- Sequential `await` for independent calls instead of `Promise.all`.
- Silence while thinking. Think aloud.

### Questions you can ask the interviewer
- What does the backend architecture look like (monolith or microservices)?
- How do you handle deployment and code reviews?
- What databases and cloud services does the team use?
- What would a backend developer in this role work on in the first 3 months?

### Rapid answers for tricky "why" questions
- **Why Node.js?** One language across frontend and backend, great for I/O heavy APIs and real-time features, large ecosystem.
- **Why Express?** Minimal and flexible, huge community. For bigger teams I would consider NestJS.
- **Why JWT?** Stateless, scales across servers easily. Trade-off: revoking needs refresh token storage or a blocklist.
- **Why MongoDB for this project?** Flexible, nested data and fast development. For strongly relational data (like the ERP hierarchy) I used PostgreSQL.
- **Why PM2 and Nginx?** PM2 keeps the process alive and uses all cores. Nginx handles TLS, compression and proxying.

---

## 20. Last day revision checklist

**Be able to write from memory (without looking):**
- [ ] Express server with `express.json()`, a router and a 404 handler
- [ ] Full CRUD with Mongoose (model, controller, routes)
- [ ] `asyncHandler`, `ApiError` and the central error middleware
- [ ] Register and login with bcrypt and JWT
- [ ] `protect` and `authorize` middlewares
- [ ] Zod (or Joi) validation middleware
- [ ] Pagination with `skip` and `limit`
- [ ] Razorpay verify signature with HMAC
- [ ] Socket.io server with JWT auth and rooms
- [ ] A `pg` query with parameters and a transaction
- [ ] Retry helper, concurrency pool, rate limiter, LRU cache

**Be able to explain in 30 seconds each:**
- [ ] Event loop and its phases, `nextTick` vs `setImmediate`
- [ ] Why Node handles many requests with one thread
- [ ] Blocking vs non-blocking code
- [ ] Streams and why they save memory
- [ ] Middleware, `next()` and error-handling middleware
- [ ] JWT flow, access vs refresh token, where to store tokens
- [ ] RBAC plus ownership checks
- [ ] 401 vs 403, 400 vs 422, 409, 201 vs 204
- [ ] PUT vs PATCH, idempotency
- [ ] Indexes, compound indexes, `explain`, `lean()`, `populate`
- [ ] Embed vs reference in MongoDB
- [ ] SQL injection and NoSQL injection prevention
- [ ] cluster mode, PM2, Nginx reverse proxy, ALB
- [ ] How you would scale Node and where Redis and queues fit

**Be ready to talk about your projects:**
- [ ] eAsha: roles and authorization, Razorpay verification, double booking, WebSockets, 40% API improvement, EC2 + ALB + Nginx + PM2 deployment
- [ ] Agri-Tech ERP: PostgreSQL schema for the hierarchy, indexes and joins
- [ ] Secure chat app: Socket.io auth, rooms, encryption, invite tokens
- [ ] Document extraction: how Node (or the frontend) talks to the FastAPI service and handles long jobs

---

**Final advice:** For 6 months to 1 year experience, interviewers want correct fundamentals, a clean working API, safe handling of auth and errors, and honest answers about what you built. If you can build a secured CRUD API from scratch and explain every line, you are in a strong position. Good luck!
