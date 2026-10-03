# FastAPI Master Interview Guide (End to End)
### Fundamentals • Parameters & Validation • Pydantic • Dependency Injection • Async • Databases • Security (JWT/OAuth2) • Middleware • WebSockets • Testing • Deployment • Live Coding

> **How to use this document**
> - **Parts 1–4:** Core FastAPI (asked in every interview): fundamentals, request/response handling, Pydantic
> - **Parts 5–8:** Dependency Injection, async/concurrency, databases, authentication/security (the "depth" questions)
> - **Parts 9–12:** Middleware & errors, advanced features, testing, deployment
> - **Part 13:** Architecture, scenarios, HR questions
> - **Part 14:** **25 live coding problems** (complete runnable code, tested against FastAPI 0.14x / Pydantic v2)
> - **Part 15:** Master cheat sheet
> - Answer format that impresses: **Definition → Why we use it → Small code example → "In my project I used..."**
> - **Version note:** Modern FastAPI uses **Pydantic v2** and the `Annotated` style. Older tutorials use Pydantic v1 (`orm_mode`, `@validator`, `regex=`). Know both; examples here use the modern style and mention the old one where it matters.
> - Where you haven't used something: *"I haven't used it in production, but my understanding is..."*

---

## Table of Contents
1. [FastAPI Fundamentals](#part-1--fastapi-fundamentals)
2. [Request Handling: Parameters, Bodies and Validation](#part-2--request-handling)
3. [Responses, Status Codes and Response Models](#part-3--responses-status-codes-and-response-models)
4. [Pydantic](#part-4--pydantic)
5. [Dependency Injection](#part-5--dependency-injection)
6. [Async, Concurrency, Background Tasks and Lifespan](#part-6--async-concurrency-background-tasks-and-lifespan)
7. [Databases and ORMs](#part-7--databases-and-orms)
8. [Authentication, Authorization and Security](#part-8--authentication-authorization-and-security)
9. [Middleware, Exception Handling, Routers, Config and Logging](#part-9--middleware-exceptions-routers-config-and-logging)
10. [Advanced Features](#part-10--advanced-features)
11. [Testing](#part-11--testing)
12. [Deployment, Performance and Production](#part-12--deployment-performance-and-production)
13. [Architecture, Scenarios and HR Questions](#part-13--architecture-scenarios-and-hr-questions)
14. [Live Coding Problems (25, Tested)](#part-14--live-coding-problems)
15. [Master Cheat Sheet](#part-15--master-cheat-sheet)

---

# Part 1 — FastAPI Fundamentals

### Q1. What is FastAPI?
FastAPI is a **modern, high-performance Python web framework for building APIs**, based on standard **Python type hints.** It was created by Sebastián Ramírez (tiangolo). It automatically gives you **data validation, serialization, and interactive API documentation.**

### Q2. Why use FastAPI? Key features?
- **Fast to run:** one of the fastest Python frameworks (built on Starlette + Pydantic, async support).
- **Fast to code:** less boilerplate; editor auto-complete and type checking.
- **Fewer bugs:** automatic validation of request data using type hints.
- **Automatic interactive docs:** Swagger UI (`/docs`) and ReDoc (`/redoc`).
- **Standards-based:** OpenAPI and JSON Schema.
- **Dependency Injection** system built-in.
- **Security tools:** OAuth2, JWT, API keys, HTTP Basic.
- **Async support** (`async`/`await`) plus WebSockets, background tasks, and more.

### Q3. What is FastAPI built on?
- **Starlette:** the ASGI web toolkit (routing, requests/responses, middleware, WebSockets, background tasks, test client).
- **Pydantic:** data validation and serialization (the data parts).
FastAPI = Starlette (web part) + Pydantic (data part) + OpenAPI/dependency injection (its own glue).

### Q4. What is ASGI? ASGI vs WSGI?
- **WSGI** (Web Server Gateway Interface): the old **synchronous** standard (Flask, Django classic). One request per worker at a time.
- **ASGI** (Asynchronous Server Gateway Interface): the **async** successor; supports **async code, WebSockets, HTTP/2, long-lived connections.**
FastAPI is an **ASGI** framework, so it needs an ASGI server (Uvicorn, Hypercorn, Daphne).

### Q5. What is Uvicorn? Why do we need it?
**Uvicorn** is a lightning-fast **ASGI server** (built on `uvloop` and `httptools`). FastAPI is only the application; Uvicorn is the server that listens on a port, receives HTTP requests, and passes them to the app.

### Q6. FastAPI vs Flask vs Django?
| | FastAPI | Flask | Django (DRF) |
|---|---|---|---|
| Type | Async-first API framework | Sync micro-framework | Full-stack framework |
| Validation | Automatic (Pydantic) | Manual / extensions | Serializers (DRF) |
| Docs | Auto (Swagger/ReDoc) | Extensions | Extensions |
| Performance | Very high | Moderate | Moderate |
| Built-ins | DI, security tools | Minimal | ORM, admin, auth, templates |
| Best for | APIs, microservices, ML serving | Small apps, prototypes | Large full-stack, CMS |

### Q7. Install FastAPI and create your first app.
```bash
pip install "fastapi[standard]"       # includes uvicorn, fastapi-cli, httpx, jinja2, python-multipart...
# minimal: pip install fastapi uvicorn
```
```python
# main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}
```
Run:
```bash
fastapi dev main.py                 # development (auto-reload) – needs fastapi[standard]
fastapi run main.py                 # production-style
uvicorn main:app --reload           # classic way (main = file, app = object)
```

### Q8. What are the automatic docs? What is OpenAPI?
FastAPI generates an **OpenAPI schema** (a standard JSON description of your API) at `/openapi.json` and serves:
- **Swagger UI** at `/docs` (interactive: try requests in the browser)
- **ReDoc** at `/redoc` (clean reference docs)
These come for free from your type hints and Pydantic models. You can disable or move them: `FastAPI(docs_url=None, redoc_url=None)` (common in production for private APIs).

### Q9. How does FastAPI use Python type hints?
Type hints declare **what each parameter is** (path, query, body) and its **type.** FastAPI reads them to: **validate** input, **convert** types (`"5"` → `5`), **document** the API, and give **editor support.**
```python
@app.get("/users/{user_id}")
def get_user(user_id: int, active: bool = True): ...   # user_id must be int; active is a query param
```

### Q10. What can you configure in `FastAPI(...)`?
```python
app = FastAPI(
    title="My API", description="Demo", version="1.0.0",
    docs_url="/docs", redoc_url="/redoc", openapi_url="/openapi.json",
    lifespan=lifespan,                 # startup/shutdown logic
    dependencies=[Depends(verify_key)],# global dependencies
    root_path="/api",                  # when behind a proxy with a path prefix
)
```

### Q11. What is a "path operation" (route)?
A combination of an **HTTP method + path + function.** The decorator defines the method and path; the function handles it.
```python
@app.post("/items", status_code=201)       # decorator = path operation decorator
async def create_item(item: Item): ...      # function = path operation function
```
Methods: `@app.get, post, put, patch, delete, options, head`.

### Q12. Why is FastAPI so fast?
Built on **Starlette** (very light ASGI toolkit), runs on **Uvicorn** (uvloop, httptools), uses **async I/O**, and **Pydantic v2's validation core is written in Rust.** Note: it is fast for **I/O-bound** workloads (DB, network). It does not make CPU-heavy Python code faster.

### Q13. Explain the lifecycle of a request in FastAPI.
1. Uvicorn receives the HTTP request and calls the ASGI app.
2. **Middleware** runs (outermost first).
3. **Router** matches the path + method.
4. **Dependencies** are resolved (`Depends`), and parameters are **parsed and validated** (path, query, header, cookie, body).
5. The **path operation function** runs (async directly, or `def` in a threadpool).
6. The return value is validated/filtered by **`response_model`** and **serialized to JSON.**
7. **Middleware** processes the response on the way out; then **background tasks** run after the response is sent.

### Q14. What Python knowledge is needed for FastAPI?
Type hints (`str | None`, `list[int]`, `Annotated`), `async`/`await`, classes and decorators, dictionaries, basic HTTP/REST knowledge.

### Q15. What is a typical FastAPI project structure?
```
app/
 ├─ main.py              # creates FastAPI app, includes routers, middleware
 ├─ core/                # config.py (settings), security.py (JWT, hashing)
 ├─ api/
 │   ├─ deps.py          # shared dependencies (get_db, get_current_user)
 │   └─ routes/          # users.py, items.py, auth.py (APIRouters)
 ├─ models/              # SQLAlchemy models
 ├─ schemas/             # Pydantic models (request/response)
 ├─ crud/ or services/   # business/database logic
 ├─ db/                  # session.py, base.py
 └─ tests/
alembic/  .env  requirements.txt  Dockerfile
```

### Q16. What does `uvicorn main:app --reload` mean?
`main` = the file `main.py`; `app` = the FastAPI object inside it; `--reload` restarts the server when code changes (**development only**).

### Q17. What are the main limitations of FastAPI?
Younger ecosystem than Django (no built-in admin/ORM/auth UI), a **single async event loop** can be blocked by bad code, CPU-bound tasks need extra tools (Celery/process pools), and you design the project structure yourself. Not ideal when you need a batteries-included server-rendered site with admin (Django is better).

### Q18. What is data validation vs serialization?
**Validation:** checking incoming data is correct (type, range, format). **Serialization:** converting Python objects into JSON (and the reverse for input). FastAPI + Pydantic do both automatically.

### Q19. What is REST? Basic methods and status codes (quick recall)?
REST = stateless, resource-based API style. `GET` read, `POST` create, `PUT` replace, `PATCH` partial update, `DELETE` remove. Codes: `200` OK, `201` Created, `204` No Content, `400` Bad Request, `401` Unauthorized, `403` Forbidden, `404` Not Found, `409` Conflict, `422` Validation error, `429` Too Many Requests, `500` Server error.

### Q20. What is `fastapi[standard]`?
An extras install that bundles commonly needed packages: **uvicorn, fastapi-cli, httpx (for TestClient), jinja2, python-multipart, email-validator**, etc., and gives you the `fastapi dev` / `fastapi run` commands.

### Q21. What are the typical use cases?
Microservices, REST backends for React/mobile apps, **ML model serving**, internal tools, real-time dashboards and WebSocket services, and API gateways. (FastAPI is used in production by many companies; avoid naming specific ones in an interview unless you are sure.)

### Q22. Hello World REST API in 10 lines — what does the interviewer look for?
That you can: create the app, define a Pydantic model, write GET/POST endpoints with correct status codes, use path/query params, and open `/docs`. See **Live Coding 1–3.**

---

# Part 2 — Request Handling

### Q23. Path parameters?
Variables inside the URL path, declared with `{}` and a matching function argument. FastAPI converts and validates the type.
```python
@app.get("/users/{user_id}")
def get_user(user_id: int):          # "/users/abc" → 422 validation error
    return {"user_id": user_id}
```

### Q24. Why does the order of path operations matter?
FastAPI matches routes **in the order they are declared.** A fixed path must come **before** a dynamic one.
```python
@app.get("/users/me")          # first
def me(): ...
@app.get("/users/{user_id}")   # second (otherwise "me" would be treated as user_id)
def user(user_id: str): ...
```

### Q25. Path parameters with predefined values (Enum)?
```python
from enum import Enum
class Role(str, Enum):
    admin = "admin"
    user = "user"

@app.get("/roles/{role}")
def get_role(role: Role):       # only "admin" or "user" accepted; docs show a dropdown
    return {"role": role.value}
```

### Q26. Path parameter that contains slashes (a file path)?
Use the `:path` converter: `@app.get("/files/{file_path:path}")` → `/files/home/me/a.txt` gives `file_path="home/me/a.txt"`.

### Q27. Validating path parameters with `Path()`?
```python
from typing import Annotated
from fastapi import Path

@app.get("/items/{item_id}")
def get_item(item_id: Annotated[int, Path(title="Item ID", gt=0, le=1000)]): ...
```
Options: `gt, ge, lt, le, min_length, max_length, pattern`.

### Q28. Query parameters: optional, required, default?
Function parameters **not** in the path are query parameters.
```python
@app.get("/items")
def list_items(skip: int = 0, limit: int = 10, q: str | None = None, sort: str = Query(...)): ...
# skip, limit: optional with defaults; q: optional (None); sort: required
# GET /items?skip=5&limit=20&sort=price
```
A parameter with **no default** is required; with a default (or `None`) it is optional.

### Q29. Validating query parameters with `Query()`? Lists? Alias?
```python
from fastapi import Query

@app.get("/search")
def search(
    q: Annotated[str | None, Query(min_length=3, max_length=50, pattern="^[a-z]+$")] = None,
    tags: Annotated[list[str] | None, Query()] = None,       # /search?tags=a&tags=b
    item_query: Annotated[str | None, Query(alias="item-query")] = None,
    old: Annotated[str | None, Query(deprecated=True)] = None,
): ...
```

### Q30. How are boolean query values handled?
`true, True, 1, on, yes` → `True`; `false, False, 0, off, no` → `False`.

### Q31. Request body: how do you read JSON data?
Declare a **Pydantic model** as a parameter. FastAPI reads the JSON body, validates it, and gives you a typed object.
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float
    description: str | None = None

@app.post("/items", status_code=201)
def create_item(item: Item):
    return item
```
Wrong or missing data → automatic **422** with details.

### Q32. How does FastAPI decide if a parameter is path, query, or body?
- Name appears in the **path** `{}` → **path parameter.**
- **Singular type** (int, str, float, bool) not in path → **query parameter.**
- **Pydantic model** type → **request body.**
```python
@app.put("/items/{item_id}")
def update(item_id: int, item: Item, q: str | None = None): ...
#            path          body        query
```

### Q33. Multiple body parameters / `embed`?
Two models → body becomes `{"item": {...}, "user": {...}}`. For a single model, force a key with `Body(embed=True)`:
```python
@app.post("/items")
def create(item: Annotated[Item, Body(embed=True)]): ...   # body: {"item": {...}}
```

### Q34. Singular values in the body?
```python
from fastapi import Body
@app.put("/items/{id}")
def update(id: int, item: Item, importance: Annotated[int, Body(gt=0)]): ...
# body: {"item": {...}, "importance": 5}
```

### Q35. Nested models and lists of models?
```python
class Image(BaseModel):
    url: str
    name: str

class Product(BaseModel):
    name: str
    tags: set[str] = set()
    images: list[Image] = []
```
Validation works at every level. Also `dict[int, float]` for arbitrary key–value bodies.

### Q36. Header parameters?
```python
from fastapi import Header
@app.get("/info")
def info(user_agent: Annotated[str | None, Header()] = None,
         x_token: Annotated[list[str] | None, Header()] = None): ...
```
FastAPI converts underscores to hyphens (`user_agent` → `User-Agent`, `x_token` → `X-Token`).

### Q37. Cookie parameters?
```python
from fastapi import Cookie
@app.get("/me")
def me(session_id: Annotated[str | None, Cookie()] = None): ...
```

### Q38. Form data?
Needs `python-multipart`. Use `Form()` for HTML form fields (e.g., login forms). **You cannot mix `Form` and JSON `Body`** in the same endpoint (HTTP limitation).
```python
from fastapi import Form
@app.post("/login")
def login(username: Annotated[str, Form()], password: Annotated[str, Form()]): ...
```

### Q39. File uploads?
```python
from fastapi import File, UploadFile

@app.post("/upload")
async def upload(file: UploadFile):
    content = await file.read()
    return {"filename": file.filename, "content_type": file.content_type, "size": len(content)}

@app.post("/upload-many")
async def upload_many(files: list[UploadFile]): ...
```

### Q40. `UploadFile` vs `bytes = File()`?
`bytes` loads the **whole file in memory** (OK for small files). **`UploadFile`** uses a **spooled temporary file** (memory up to a limit, then disk), exposes `filename`, `content_type`, async `read()/write()/seek()/close()`, and is better for **large files.**

### Q41. How do you receive additional metadata with a file?
Use `Form()` fields along with `UploadFile` (multipart/form-data): `file: UploadFile, description: Annotated[str, Form()]`.

### Q42. How do you access the raw `Request` object?
```python
from fastapi import Request
@app.get("/client")
async def client(request: Request):
    return {"host": request.client.host, "ua": request.headers.get("user-agent"), "url": str(request.url)}
```
Use it when you need headers, client IP, raw body (`await request.body()`), or `request.app.state`.

### Q43. `Annotated` style vs the older default-value style?
Modern (recommended): `q: Annotated[str | None, Query(max_length=50)] = None`.
Older: `q: str | None = Query(default=None, max_length=50)`.
`Annotated` keeps real defaults, works nicely with reuse (`CommonQ = Annotated[...]`), and with dependencies (`db: Annotated[Session, Depends(get_db)]`).

### Q44. Query parameter models (newer FastAPI)?
Group many query parameters into a Pydantic model (FastAPI 0.115+):
```python
class Filters(BaseModel):
    model_config = {"extra": "forbid"}        # reject unknown query params
    limit: int = Field(10, gt=0, le=100)
    offset: int = Field(0, ge=0)
    tags: list[str] = []

@app.get("/items")
def items(f: Annotated[Filters, Query()]): ...
```

### Q45. Required vs optional vs nullable?
```python
a: str                       # required
b: str = "x"                 # optional (default)
c: str | None = None         # optional, may be null
d: str | None                # REQUIRED but may be null (Pydantic v2)
```
In Pydantic v2 `Optional[str]` without a default is **still required**; add `= None` to make it optional.

### Q46. Extra data types supported?
`UUID`, `datetime`, `date`, `time`, `timedelta`, `Decimal`, `bytes`, `frozenset`, `set`, `Enum`, `HttpUrl`, `EmailStr`, etc. They are parsed from strings and serialized back to JSON-compatible forms.

### Q47. What does a validation error response look like?
Status **422**:
```json
{
  "detail": [
    { "type": "int_parsing", "loc": ["path", "item_id"],
      "msg": "Input should be a valid integer, unable to parse string as an integer",
      "input": "abc" }
  ]
}
```
`loc` tells **where** the error is (`path`, `query`, `body`, `header`), then the field name.

### Q48. Summary: where does each parameter type come from?
| Declaration | Source |
|---|---|
| `{name}` in path + argument | Path |
| simple type argument | Query string |
| Pydantic model / `Body()` | JSON body |
| `Header()` | HTTP headers |
| `Cookie()` | Cookies |
| `Form()` | Form fields |
| `File()` / `UploadFile` | Multipart files |
| `Depends()` | Dependency result |
| `Request` / `Response` / `BackgroundTasks` | Special injected objects |

### Q49. Trailing slash behavior?
If a route is `/items` and the client calls `/items/`, Starlette **redirects (307)** to the version without a slash (`redirect_slashes=True` by default). Be consistent to avoid extra redirects (important with proxies and CORS).

### Q50. How do you accept arbitrary JSON (dict) as the body?
`def f(data: dict)` or `data: dict[str, Any]`. You lose validation, so prefer typed models when you can.

---

# Part 3 — Responses, Status Codes and Response Models

### Q51. What is `response_model`? Why use it?
It declares the **shape of the response.** FastAPI uses it to **validate, filter, and document** the output. Most important use: **hide sensitive fields** (like passwords).
```python
class UserIn(BaseModel):
    username: str
    password: str
    email: EmailStr

class UserOut(BaseModel):
    username: str
    email: EmailStr

@app.post("/users", response_model=UserOut, status_code=201)
def create_user(user: UserIn):
    return user          # password is removed from the response automatically
```

### Q52. Response model options?
`response_model_exclude_unset=True` (omit fields not set), `response_model_exclude_defaults`, `response_model_exclude_none=True`, `response_model_include={"a"}`, `response_model_exclude={"password"}`. Prefer separate output models over include/exclude sets.

### Q53. Return type annotation vs `response_model`?
Modern FastAPI lets you write `def f() -> UserOut:` and uses it as the response model (with full editor support). Use `response_model=` when the function returns something different from the declared output (for example an ORM object or dict) or when you want to set it explicitly.

### Q54. How do you set the status code? Common ones?
```python
from fastapi import status
@app.post("/items", status_code=status.HTTP_201_CREATED)
```
Common: `200` default, `201` created (POST), `204` no content (DELETE; **return nothing**), `400`, `401`, `403`, `404`, `409`, `422`, `429`, `500`.

### Q55. Available response classes?
`JSONResponse` (default), `HTMLResponse`, `PlainTextResponse`, `RedirectResponse`, `FileResponse`, `StreamingResponse`, `UJSONResponse`/`ORJSONResponse` (alternate JSON serializers, need extra packages). Set per route with `response_class=HTMLResponse`.
```python
@app.get("/page", response_class=HTMLResponse)
def page(): return "<h1>Hello</h1>"
```

### Q56. Can you return a `Response` directly?
Yes. Returning a `Response` (like `JSONResponse(content=..., status_code=...)`) **bypasses** `response_model` validation and serialization. Use it for full control (custom headers, media types, status).

### Q57. How do you set custom headers, cookies, or status inside an endpoint?
Declare a `Response` parameter:
```python
from fastapi import Response
@app.get("/set")
def set_stuff(response: Response):
    response.headers["X-Custom"] = "yes"
    response.set_cookie("session", "abc", httponly=True, secure=True, samesite="lax")
    response.status_code = 202
    return {"ok": True}
```

### Q58. How do you document multiple possible responses?
```python
@app.get("/items/{id}", response_model=Item,
         responses={404: {"description": "Item not found"}, 403: {"description": "No access"}})
```
For different successful shapes use `response_model=Cat | Dog` (a Union).

### Q59. Best practice: separate input/output/DB models.
`UserCreate` (input, has password) → DB model `User` (hashed_password) → `UserRead` (output, no password). Never reuse the DB model as the API response.

### Q60. How do you return errors?
```python
from fastapi import HTTPException
raise HTTPException(status_code=404, detail="Item not found",
                    headers={"X-Error": "NotFound"})
```
`detail` can be a string, dict, or list. For global control use **exception handlers** (Part 9).

### Q61. What is `jsonable_encoder`?
Converts complex objects (Pydantic models, `datetime`, `UUID`) into JSON-compatible Python types (dict, str). Useful when storing in a NoSQL DB or building a custom `JSONResponse`.

### Q62. Returning ORM objects?
Set `model_config = ConfigDict(from_attributes=True)` on the response schema (Pydantic v1: `orm_mode = True`). FastAPI then reads attributes of the SQLAlchemy object.

### Q63. Why do you get a 500 "ResponseValidationError"?
Your function returned data that doesn't match the `response_model` (missing field, wrong type). It is a **server bug** (500), not a client error (422).

### Q64. 204 No Content gotcha?
A 204 response must have **no body.** Return `None` and use `status_code=204`; in recent versions FastAPI handles it correctly. For delete endpoints:
```python
@app.delete("/items/{id}", status_code=204)
def delete(id: int): ...        # no return value
```

### Q65. Pagination response format?
```json
{ "items": [...], "total": 125, "page": 2, "size": 20, "pages": 7 }
```
Use limit/offset or page/size params, a generic `Page[T]` model (Live Coding 16), or cursor pagination for very large data.

### Q66. How do you serve files and streams?
`FileResponse(path, filename="report.pdf")` for files; `StreamingResponse(generator(), media_type="text/csv")` for large or generated content (Live Coding 18).

### Q67. How do you hide a route from docs or mark it deprecated?
`@app.get("/internal", include_in_schema=False)` and `@app.get("/old", deprecated=True)`.

### Q68. How do you improve the docs of an endpoint?
```python
@app.post("/items", tags=["items"], summary="Create an item",
          description="Creates a new item.", response_description="The created item")
def create(...):
    """The docstring is also shown as the description (supports Markdown)."""
```
Also `Field(examples=[...])`, `model_config = {"json_schema_extra": {...}}`, and `openapi_tags` on the app.

---

# Part 4 — Pydantic

### Q69. What is Pydantic? What is its role in FastAPI?
Pydantic is a Python library for **data validation and settings management using type hints.** In FastAPI it defines **request bodies, response models and settings**, validates data, converts types, and generates the **JSON Schema** used in the OpenAPI docs.

### Q70. Basic `BaseModel` and type coercion?
```python
from pydantic import BaseModel

class User(BaseModel):
    id: int
    name: str
    active: bool = True

u = User(id="5", name="Ravi")     # "5" is converted to 5 (lax mode)
u.id        # 5
User(id="abc", name="x")          # raises ValidationError
```
Pydantic converts compatible types by default ("lax"); **strict mode** disables that.

### Q71. `Field()` constraints?
```python
from pydantic import BaseModel, Field

class Product(BaseModel):
    name: str = Field(min_length=2, max_length=50, description="Product name")
    price: float = Field(gt=0, le=1_000_000)
    sku: str = Field(pattern=r"^[A-Z]{3}-\d{4}$")          # v1 used regex=
    qty: int = Field(default=1, ge=0)
    tags: list[str] = Field(default_factory=list, max_length=10)
```
Other options: `alias`, `examples`, `title`, `exclude`, `frozen`.

### Q72. Required, optional, default in Pydantic v2?
`x: int` required • `x: int = 0` optional with default • `x: int | None = None` optional/nullable • `x: int | None` required but can be `None`. Use `default_factory` for mutable defaults (`list`, `dict`).

### Q73. Validators: `field_validator` and `model_validator`?
```python
from pydantic import BaseModel, field_validator, model_validator

class Signup(BaseModel):
    username: str
    password: str
    confirm: str

    @field_validator("username")
    @classmethod
    def clean_username(cls, v: str) -> str:
        if not v.isalnum():
            raise ValueError("username must be alphanumeric")
        return v.lower()

    @model_validator(mode="after")
    def passwords_match(self):
        if self.password != self.confirm:
            raise ValueError("passwords do not match")
        return self
```
Pydantic v1 used `@validator` and `@root_validator`. Modes: `before` (raw input), `after` (typed value), `wrap`, `plain`.

### Q74. Useful special types?
`EmailStr` (needs `email-validator`), `HttpUrl`, `SecretStr` (hidden in logs/repr), `constr`/`conint` (older constrained types), `PositiveInt`, `UUID4`, `FilePath`, `IPvAnyAddress`, `Json`, `AwareDatetime`, `StrictInt`.

### Q75. Nested models?
Models can contain other models and lists of models; validation and docs work recursively.
```python
class Address(BaseModel):
    city: str
    zip: str

class Customer(BaseModel):
    name: str
    addresses: list[Address]
```

### Q76. Key methods in Pydantic v2 (and the v1 names)?
| Pydantic v2 | v1 name | Purpose |
|---|---|---|
| `model_dump()` | `.dict()` | Model → dict |
| `model_dump_json()` | `.json()` | Model → JSON string |
| `model_validate(data)` | `parse_obj` | dict → model |
| `model_validate_json(s)` | `parse_raw` | JSON string → model |
| `model_copy(update={...})` | `.copy()` | Copy with changes |
| `model_json_schema()` | `.schema()` | JSON Schema |
```python
data = user.model_dump(exclude_unset=True)    # only fields that were actually provided
```

### Q77. What is `from_attributes` (formerly `orm_mode`)?
Lets Pydantic read data from **object attributes** (SQLAlchemy models) instead of only dicts.
```python
from pydantic import ConfigDict
class UserRead(BaseModel):
    model_config = ConfigDict(from_attributes=True)
    id: int
    email: str
```
`UserRead.model_validate(db_user)`.

### Q78. Aliases?
```python
class Item(BaseModel):
    model_config = ConfigDict(populate_by_name=True)
    item_id: int = Field(alias="itemId")                 # accept "itemId" in input
    full_name: str = Field(serialization_alias="fullName")  # output key
```
Use `by_alias` when dumping (FastAPI uses aliases for responses by default).

### Q79. Important `ConfigDict` options?
```python
model_config = ConfigDict(
    extra="forbid",               # reject unknown fields ("ignore" is default; "allow" keeps them)
    str_strip_whitespace=True,    # trim strings
    frozen=True,                  # immutable & hashable
    strict=True,                  # no type coercion
    from_attributes=True,
    populate_by_name=True,
    validate_assignment=True,     # validate when attributes are assigned later
)
```

### Q80. Pydantic v1 vs v2 — main differences?
| Topic | v1 | v2 |
|---|---|---|
| Core | Python | **Rust (`pydantic-core`)**, much faster |
| Methods | `.dict()`, `.json()`, `parse_obj` | `model_dump()`, `model_dump_json()`, `model_validate` |
| ORM mode | `class Config: orm_mode = True` | `ConfigDict(from_attributes=True)` |
| Validators | `@validator`, `@root_validator` | `@field_validator`, `@model_validator` |
| `Optional[X]` | Implicitly default `None` | **Required** unless you give a default |
| Regex | `Field(regex=)` | `Field(pattern=)` |
| Settings | `pydantic.BaseSettings` | separate package **`pydantic-settings`** |
| Config | inner `class Config` | `model_config = ConfigDict(...)` |

### Q81. How do you manage settings and environment variables?
```python
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env")
    app_name: str = "My API"
    database_url: str
    secret_key: str
    access_token_expire_minutes: int = 30
    debug: bool = False

settings = Settings()        # reads env vars / .env, validates types, fails fast if missing
```
Use it as a **cached dependency** with `@lru_cache` (Live Coding 21). Never commit `.env`.

### Q82. Computed fields?
```python
from pydantic import computed_field
class Rect(BaseModel):
    w: float
    h: float
    @computed_field
    @property
    def area(self) -> float:
        return self.w * self.h       # included in output/schema
```

### Q83. Discriminated (tagged) unions?
Choose the right model by a field value, faster and clearer errors:
```python
from typing import Literal, Annotated, Union
class Cat(BaseModel):
    pet_type: Literal["cat"]; meows: int
class Dog(BaseModel):
    pet_type: Literal["dog"]; barks: float
Pet = Annotated[Union[Cat, Dog], Field(discriminator="pet_type")]
```

### Q84. Generic models (for reusable response envelopes)?
```python
from typing import Generic, TypeVar
T = TypeVar("T")
class Page(BaseModel, Generic[T]):
    items: list[T]
    total: int
    page: int
    size: int
# response_model=Page[UserRead]
```

### Q85. How do you implement a partial update (PATCH)?
Make all fields optional in an update model and apply only the fields the client sent:
```python
class ItemUpdate(BaseModel):
    name: str | None = None
    price: float | None = None

@app.patch("/items/{id}")
def patch(id: int, data: ItemUpdate):
    update = data.model_dump(exclude_unset=True)     # ONLY provided fields
    stored = items[id].model_copy(update=update)
    items[id] = stored
    return stored
```
`exclude_unset` is the key: it distinguishes "not sent" from "sent as null".

### Q86. Pydantic model vs dataclass vs TypedDict?
Pydantic = **validation + parsing + serialization** (use at API boundaries). `dataclass` = lightweight container without validation (FastAPI also supports Pydantic dataclasses). `TypedDict` = type hints for dicts only (no runtime validation unless used with Pydantic).

### Q87. How do you handle `ValidationError` yourself?
```python
from pydantic import ValidationError
try:
    User(id="x", name=1)
except ValidationError as e:
    print(e.errors())        # list of dicts: type, loc, msg, input
    print(e.json())
```
FastAPI converts it into a 422 response automatically for request data.

### Q88. Enums in models?
```python
class Status(str, Enum):
    active = "active"; blocked = "blocked"
class User(BaseModel):
    status: Status = Status.active
```
Inheriting from `str` makes it JSON-friendly. In docs it shows allowed values.

### Q89. Why is Pydantic v2 faster?
Its validation/serialization core (`pydantic-core`) is written in **Rust**, typically many times faster than v1 for parsing and validating data.

### Q90. How do you add examples to the docs?
```python
class Item(BaseModel):
    name: str = Field(examples=["Laptop"])
    price: float
    model_config = {"json_schema_extra": {"examples": [{"name": "Laptop", "price": 999.9}]}}
```

---

# Part 5 — Dependency Injection

### Q91. What is Dependency Injection in FastAPI? (VERY IMPORTANT)
A system where you declare things your function **needs** (DB session, current user, config, pagination params) with **`Depends()`**, and FastAPI **creates and passes them automatically** on each request. Benefits: **reuse, cleaner code, easy testing (override dependencies), separation of concerns, shared validation/security logic.**

### Q92. Simple function dependency?
```python
from fastapi import Depends

def pagination(skip: int = 0, limit: int = Query(10, le=100)):
    return {"skip": skip, "limit": limit}

@app.get("/items")
def items(page: Annotated[dict, Depends(pagination)]):
    return page
```
Reusable alias: `Pagination = Annotated[dict, Depends(pagination)]`.

### Q93. Class as a dependency?
A class is callable, so FastAPI calls it and injects the instance:
```python
class Pagination:
    def __init__(self, skip: int = 0, limit: int = 10):
        self.skip, self.limit = skip, limit

@app.get("/users")
def users(p: Annotated[Pagination, Depends()]):     # Depends() with no arg uses the annotation
    return {"skip": p.skip, "limit": p.limit}
```

### Q94. Sub-dependencies?
A dependency can have its own dependencies; FastAPI resolves the whole tree.
```python
def get_token(authorization: Annotated[str, Header()]): ...
def get_current_user(token: Annotated[str, Depends(get_token)]): ...
def get_admin(user: Annotated[User, Depends(get_current_user)]): ...
```

### Q95. Dependencies that don't return a value?
Use the decorator-level `dependencies=[...]` for checks/side effects (e.g., verify an API key):
```python
@app.get("/secure", dependencies=[Depends(verify_key)])
def secure(): ...
```

### Q96. Router-level and app-level (global) dependencies?
```python
router = APIRouter(prefix="/admin", dependencies=[Depends(require_admin)])   # for all routes in the router
app = FastAPI(dependencies=[Depends(log_request)])                           # for the whole app
```

### Q97. Dependencies with `yield` (setup and cleanup)? (VERY COMMON)
Code **before `yield`** runs before the endpoint; code **after** runs when the request is done (cleanup). Perfect for DB sessions and connections.
```python
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()          # always closes, even if an error occurred

@app.get("/users")
def users(db: Annotated[Session, Depends(get_db)]): ...
```

### Q98. Are dependencies cached within a request?
Yes. If the same dependency is used several times in one request (e.g., `get_current_user` used by multiple sub-dependencies), it is **executed once** and the result is reused. Disable with `Depends(dep, use_cache=False)`.

### Q99. How do you override dependencies in tests?
```python
app.dependency_overrides[get_db] = override_get_db
app.dependency_overrides[get_current_user] = lambda: fake_user
# ...after tests
app.dependency_overrides.clear()
```
This is the main reason DI makes FastAPI easy to test (Live Coding 15).

### Q100. Parameterized dependencies (a dependency factory)?
```python
def require_role(*roles: str):
    def checker(user: Annotated[User, Depends(get_current_user)]):
        if user.role not in roles:
            raise HTTPException(403, "Not enough permissions")
        return user
    return checker

@app.delete("/users/{id}", dependencies=[Depends(require_role("admin"))])
```
Alternative: a class with `__call__`.

### Q101. Dependencies vs middleware?
**Middleware** runs for **every request** at the app level and sees raw request/response (CORS, logging, timing, headers). **Dependencies** run per **endpoint/router**, can return values, receive parsed parameters, and are visible in OpenAPI (security schemes). Use dependencies for auth/permissions/DB; middleware for cross-cutting concerns.

### Q102. How do you use dependencies for authentication?
`get_current_user` reads the token (OAuth2PasswordBearer), decodes it, loads the user, and raises 401 on failure. Endpoints just declare `user: Annotated[User, Depends(get_current_user)]` (Part 8).

### Q103. Singleton resources (settings, HTTP client)?
Use `@lru_cache` on a function returning the settings object, or create heavy resources once in **lifespan** and store them in `app.state` (reach them via `request.app.state`).
```python
@lru_cache
def get_settings(): return Settings()
```

### Q104. Can dependencies be async?
Yes. Mix `def` and `async def` freely. Sync dependencies run in a threadpool; async ones run on the event loop. Prefer async I/O libraries inside `async def`.

### Q105. What happens if a dependency raises an exception?
If it raises `HTTPException`, the endpoint is not executed and that error response is returned (e.g., 401/403). Exceptions in `yield` dependencies after the response starts are handled specially; always put cleanup in `finally`.

### Q106. What is `Security()`?
Like `Depends()` but for security dependencies; supports **OAuth2 scopes** and documents them in OpenAPI: `Depends(get_user)` → `Security(get_user, scopes=["items:read"])`.

### Q107. Common shared dependencies (the "commons" pattern)?
Pagination params, sorting/filter params, DB session, current user, settings, rate limiter, pagination of query params, tenant/organization resolver from a header.

### Q108. Why does DI matter in interviews?
It shows you can design **clean, testable, reusable** FastAPI code: auth, DB sessions, config, and validation are separated from business logic and swapped in tests.

---

# Part 6 — Async, Concurrency, Background Tasks and Lifespan

### Q109. `def` vs `async def` endpoints — when to use which? (VERY IMPORTANT)
- **`async def`:** use when you call **async libraries** with `await` (async DB driver, `httpx.AsyncClient`, `asyncio.sleep`). Runs directly on the **event loop.**
- **`def`:** use for **blocking/sync code** (sync SQLAlchemy, `requests`, file I/O, CPU-ish libraries). FastAPI runs it in a **threadpool** so it does not block the event loop.
- If unsure, use plain `def`. It is safe. **Never put blocking calls inside `async def`.**

### Q110. What happens when you use `time.sleep()` or `requests.get()` inside `async def`?
It **blocks the entire event loop**, so *every* other request waits. Fix: use `await asyncio.sleep()`, `httpx.AsyncClient`, async DB drivers, or make the endpoint a plain `def`, or run blocking code in a thread: `await run_in_threadpool(func)` / `await anyio.to_thread.run_sync(func)`.

### Q111. What is the event loop?
A single-threaded scheduler that runs many coroutines **cooperatively**: when one coroutine hits `await` (waiting for I/O), the loop switches to another. That is how one process handles thousands of concurrent connections.

### Q112. Concurrency vs parallelism, and the GIL?
**Concurrency** = handling many tasks at once by switching (asyncio, threads). **Parallelism** = executing at the same time on multiple CPU cores (multiple processes). The Python **GIL** lets only one thread run Python bytecode at a time, so threads/asyncio don't speed up **CPU-bound** work. For CPU-heavy tasks use **multiple worker processes, a process pool, or a task queue (Celery/ARQ).**

### Q113. How do you run several async calls concurrently?
```python
import asyncio, httpx

@app.get("/dashboard")
async def dashboard():
    async with httpx.AsyncClient(timeout=5) as client:
        a, b = await asyncio.gather(client.get(URL_A), client.get(URL_B))
    return {"a": a.json(), "b": b.json()}
```
Total time ≈ the slowest call, not the sum.

### Q114. What are Background Tasks?
Functions that run **after the response is sent**, so the client doesn't wait (send email, write audit log, resize image).
```python
from fastapi import BackgroundTasks

def send_email(to: str, msg: str): ...

@app.post("/signup")
def signup(email: str, tasks: BackgroundTasks):
    tasks.add_task(send_email, email, "Welcome!")
    return {"message": "Signed up"}
```

### Q115. BackgroundTasks vs Celery/ARQ/RQ?
| BackgroundTasks | Celery / ARQ / RQ |
|---|---|
| In the same process | Separate worker processes/servers |
| No retries, no persistence — lost if the server restarts | Retries, scheduling, monitoring, persistence (broker: Redis/RabbitMQ) |
| Good for small, quick jobs | Good for heavy/long/critical jobs |

### Q116. What are lifespan events? (startup/shutdown)
Code that runs **once when the app starts and stops** (open DB pool, load ML model, connect to Redis, close them at shutdown). Modern way: a **lifespan async context manager.**
```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.model = load_model()        # startup
    yield                                  # app runs here
    app.state.model = None                 # shutdown cleanup

app = FastAPI(lifespan=lifespan)
```

### Q117. `@app.on_event("startup")` — still used?
It is **deprecated** in favor of `lifespan`. You may see it in older code; mention you'd migrate to lifespan.

### Q118. What is `app.state`?
A place to store objects shared across requests (DB engine, HTTP client, ML model). Access via `request.app.state.x`.

### Q119. `httpx` vs `requests`?
`requests` is **synchronous** (blocking). **`httpx`** supports **sync and async** (`httpx.AsyncClient`), HTTP/2, timeouts; use it in async endpoints. Reuse one client (lifespan) instead of creating a new one per request in high-traffic apps.

### Q120. How do you call blocking/CPU code from an async endpoint?
```python
from fastapi.concurrency import run_in_threadpool
result = await run_in_threadpool(heavy_sync_function, arg)
```
For true CPU-bound work use `ProcessPoolExecutor` via `loop.run_in_executor`, or a task queue.

### Q121. What is the threadpool limit for `def` endpoints?
AnyIO's default thread limiter is **40 threads.** Many slow sync endpoints can exhaust it; then new requests wait. Increase carefully, or convert to async, or scale workers.

### Q122. How do workers relate to concurrency?
Each **Uvicorn worker process** has its own event loop. `--workers 4` = 4 processes (use multiple CPU cores). Total concurrency ≈ workers × (requests handled concurrently per loop). Don't share in-memory state across workers (use Redis/DB).

### Q123. `asyncio.create_task` vs `BackgroundTasks`?
`create_task` starts a task on the loop immediately and you must keep a reference and handle errors/cancellation yourself. `BackgroundTasks` is the framework-managed way that runs after the response is sent. Prefer BackgroundTasks for simple post-response work.

### Q124. Timeouts and cancellation?
Use client timeouts (`httpx.AsyncClient(timeout=5)`), `asyncio.wait_for(coro, timeout=3)`, and handle `asyncio.CancelledError` when clients disconnect during long operations.

### Q125. What are async generators / streaming in FastAPI?
An `async def` generator with `yield` used with `StreamingResponse` sends data in chunks without loading everything in memory (large files, CSV exports, SSE, LLM token streaming).

### Q126. What are `uvloop` and `httptools`?
Fast drop-in components used by Uvicorn (installed with `uvicorn[standard]`): `uvloop` is a faster event loop; `httptools` is a fast HTTP parser.

### Q127. Common async mistakes (interview favorites)?
Blocking calls in `async def` • forgetting `await` (you get a coroutine object, not a result) • creating a new `httpx` client per request • sharing a DB session across tasks • mixing sync SQLAlchemy in async endpoints • long CPU work on the event loop.

### Q128. Quick decision table
| Situation | Use |
|---|---|
| Async DB driver / httpx / aiofiles | `async def` + `await` |
| Sync DB (SQLAlchemy sync), `requests`, pandas | plain `def` |
| Heavy CPU | Celery / process pool / more workers |
| Fire-and-forget small job | `BackgroundTasks` |
| Reliable, retryable job | Celery / ARQ / RQ |

---

# Part 7 — Databases and ORMs

### Q129. Which databases and libraries work with FastAPI?
FastAPI is **database-agnostic.** Common choices:
- **SQL:** **SQLAlchemy** (most popular; sync + async), **SQLModel** (SQLAlchemy + Pydantic by the FastAPI author), Tortoise ORM, raw drivers (`asyncpg`, `psycopg`).
- **NoSQL:** MongoDB via **Motor / PyMongo async** or **Beanie** (ODM); Redis via `redis-py`.
- Migrations: **Alembic** (for SQLAlchemy).

### Q130. Set up SQLAlchemy (sync) with FastAPI.
```python
# db.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, DeclarativeBase

DATABASE_URL = "postgresql+psycopg://user:pass@localhost/mydb"   # or sqlite:///./app.db
engine = create_engine(DATABASE_URL, pool_pre_ping=True)
SessionLocal = sessionmaker(bind=engine, autoflush=False, expire_on_commit=False)

class Base(DeclarativeBase):
    pass

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```
For SQLite add `connect_args={"check_same_thread": False}`.

### Q131. Define a model (SQLAlchemy 2.0 style).
```python
from sqlalchemy import String, ForeignKey
from sqlalchemy.orm import Mapped, mapped_column, relationship

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    email: Mapped[str] = mapped_column(String(255), unique=True, index=True)
    hashed_password: Mapped[str]
    is_active: Mapped[bool] = mapped_column(default=True)
    posts: Mapped[list["Post"]] = relationship(back_populates="owner")

class Post(Base):
    __tablename__ = "posts"
    id: Mapped[int] = mapped_column(primary_key=True)
    title: Mapped[str]
    owner_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    owner: Mapped[User] = relationship(back_populates="posts")
```
Create tables in dev: `Base.metadata.create_all(engine)`; in production use **Alembic.**

### Q132. SQLAlchemy model vs Pydantic schema?
The **SQLAlchemy model** maps to a **database table** (persistence). The **Pydantic schema** defines the **API contract** (validation, response shape). Keep them separate: `UserCreate` (input), `User` (DB), `UserRead` (output).

### Q133. Write CRUD operations (SQLAlchemy 2.0 query style).
```python
from sqlalchemy import select

def get_user(db: Session, user_id: int):
    return db.get(User, user_id)

def list_users(db: Session, skip=0, limit=10):
    return db.scalars(select(User).offset(skip).limit(limit)).all()

def create_user(db: Session, email: str, hashed: str):
    user = User(email=email, hashed_password=hashed)
    db.add(user)
    db.commit()
    db.refresh(user)          # load generated id/defaults
    return user

def delete_user(db: Session, user: User):
    db.delete(user)
    db.commit()
```

### Q134. Why a separate CRUD/repository/service layer?
Keeps route functions thin (HTTP only), makes DB logic reusable and testable, and lets you change the persistence layer without rewriting endpoints.

### Q135. How do you use async SQLAlchemy?
```python
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession

engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/mydb")   # sqlite+aiosqlite:///./app.db
AsyncSessionLocal = async_sessionmaker(engine, expire_on_commit=False)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/users/{id}")
async def read_user(id: int, db: Annotated[AsyncSession, Depends(get_db)]):
    user = await db.get(User, id)
    ...
```
Key points: use async drivers (`asyncpg`, `aiosqlite`), `await` every DB call, set `expire_on_commit=False`, and **eager-load relationships** (`selectinload`) because lazy loading in async raises `MissingGreenlet`. (Live Coding 24)

### Q136. Sessions, commit, rollback, flush?
A **session** is a unit of work. `add()` stages changes; `flush()` sends SQL to the DB but keeps the transaction open; `commit()` saves permanently; `rollback()` undoes. Use one session **per request** (via `Depends(get_db)`), never a global session shared between requests.

### Q137. What is Alembic? Basic commands?
Alembic is the **migration tool** for SQLAlchemy (version-controlled schema changes).
```bash
alembic init alembic
alembic revision --autogenerate -m "create users table"
alembic upgrade head          # apply
alembic downgrade -1          # roll back one
```
Set `target_metadata = Base.metadata` in `env.py`, **review autogenerated scripts**, and run migrations in CI/CD before deploying.

### Q138. What is SQLModel?
A library by the FastAPI author combining **SQLAlchemy + Pydantic**: one class acts as both table model and schema. Great for small/medium apps and tutorials; for large apps many teams still keep separate models.

### Q139. What is the N+1 problem? How to fix it?
One query loads N parents, then N more queries load each parent's children lazily. Fix with **eager loading:** `selectinload()` (extra IN query, good for collections) or `joinedload()` (JOIN).
```python
from sqlalchemy.orm import selectinload
stmt = select(User).options(selectinload(User.posts))
```

### Q140. Connection pooling settings?
`create_engine(url, pool_size=10, max_overflow=20, pool_timeout=30, pool_recycle=1800, pool_pre_ping=True)`. Remember: **total connections = workers × (pool_size + max_overflow)** must stay under the database's max connections (use PgBouncer if needed).

### Q141. How do you implement pagination at the DB level?
`select(Item).order_by(Item.id).offset(skip).limit(limit)` plus a `count()` query for totals. For large tables use **keyset/cursor pagination** (`WHERE id > :last_id ORDER BY id LIMIT n`) since big offsets are slow.

### Q142. How do you use MongoDB with FastAPI?
Use **Motor** (async driver), the newer **PyMongo async API**, or **Beanie** (ODM based on Pydantic). Convert `ObjectId` to `str` for responses.
```python
from motor.motor_asyncio import AsyncIOMotorClient
client = AsyncIOMotorClient(settings.mongo_uri)
db = client.mydb

@app.get("/items")
async def items():
    return [{**d, "_id": str(d["_id"])} async for d in db.items.find().limit(10)]
```
Create the client in **lifespan**, not per request.

### Q143. How do you handle unique-constraint errors?
Catch `IntegrityError`, roll back, and return **409 Conflict**:
```python
from sqlalchemy.exc import IntegrityError
try:
    db.add(user); db.commit()
except IntegrityError:
    db.rollback()
    raise HTTPException(409, "Email already registered")
```

### Q144. How do you prevent SQL injection?
Use the ORM or **parameterized queries** (`text("... WHERE id = :id")` with bound params). Never build SQL with f-strings from user input. Pydantic validation adds another layer.

### Q145. Why not use one global DB session?
Sessions are **not thread-safe/task-safe** and hold state (identity map, transactions). Sharing causes stale data, leaked transactions, and race conditions. Use a **session per request** via a `yield` dependency.

### Q146. How do you test with a database?
Use an **in-memory SQLite** (with `StaticPool` and `check_same_thread=False`) or a Postgres container, create tables per test session, and **override `get_db`** with `app.dependency_overrides` (see Live Coding 15).

### Q147. Soft delete and timestamps?
Add `deleted_at` / `is_deleted` and filter it in queries; add `created_at` (`server_default=func.now()`) and `updated_at` (`onupdate=func.now()`).

### Q148. How do you run multiple DB changes atomically?
Do them in one session and a single `commit()`; if any step raises, `rollback()` leaves nothing half-written. Use `with db.begin():` or handle exceptions explicitly.

### Q149. How do you add caching (Redis)?
Check Redis first; on miss query the DB and store with a **TTL**; **invalidate** on writes. Libraries: `redis-py` (async), `fastapi-cache2`. Cache read-heavy, rarely-changing endpoints; watch for stale data.

### Q150. ORM vs query builder vs raw SQL?
ORM = productive, safe, relationships; query builder (SQLAlchemy Core) = more control; raw SQL = maximum control for complex/performance-critical queries. Mix as needed.

---

# Part 8 — Authentication, Authorization and Security

### Q151. Authentication vs Authorization?
**Authentication** = *who are you?* (login, token). **Authorization** = *what are you allowed to do?* (roles, permissions). Auth failure → **401**; permission failure → **403**.

### Q152. What security tools does FastAPI provide?
In `fastapi.security`: `OAuth2PasswordBearer`, `OAuth2PasswordRequestForm`, `HTTPBearer`, `HTTPBasic`, `APIKeyHeader/Query/Cookie`, `SecurityScopes`. They integrate with the docs (the **Authorize** button in Swagger UI).

### Q153. Explain the OAuth2 password flow + JWT login in FastAPI.
1. Client sends `username` + `password` as **form data** to `/token`.
2. Server verifies the user and **password hash**, then creates a **JWT** access token.
3. Client sends the token in `Authorization: Bearer <token>` on later requests.
4. A dependency (`get_current_user`) **decodes/verifies** the token and loads the user.
`OAuth2PasswordBearer(tokenUrl="token")` just tells FastAPI/Swagger where to get the token and how to read it from the header.

### Q154. What is a JWT? Structure and claims?
**JSON Web Token** = `header.payload.signature` (base64url-encoded). The payload holds **claims:** `sub` (subject/user id), `exp` (expiry), `iat` (issued at), `jti` (token id), plus custom ones (role). The **signature** proves it wasn't tampered with. The payload is **encoded, not encrypted**, so never put secrets in it. Algorithms: **HS256** (shared secret) vs **RS256** (private/public key, good for multi-service setups).

### Q155. How do you store passwords?
**Never plain text.** Hash with a slow, salted algorithm: **Argon2** or **bcrypt**. Modern FastAPI docs use **`pwdlib`** (`PasswordHash.recommended()` = Argon2). The older approach used **`passlib`** with bcrypt (passlib is unmaintained and can conflict with newer bcrypt releases).
```python
from pwdlib import PasswordHash
password_hash = PasswordHash.recommended()
hashed = password_hash.hash("secret")
password_hash.verify("secret", hashed)       # True
```

### Q156. Which JWT library?
**PyJWT** (`import jwt`) is the modern choice. `python-jose` was used in older docs but is less maintained.
```python
import jwt
from datetime import datetime, timedelta, timezone

def create_token(sub: str, minutes: int = 30) -> str:
    payload = {"sub": sub, "exp": datetime.now(timezone.utc) + timedelta(minutes=minutes)}
    return jwt.encode(payload, SECRET_KEY, algorithm="HS256")

payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])     # always pass algorithms=
```
Errors: `jwt.ExpiredSignatureError`, `jwt.InvalidTokenError`.

### Q157. `get_current_user` dependency (core pattern)?
```python
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def get_current_user(token: Annotated[str, Depends(oauth2_scheme)], db: Annotated[Session, Depends(get_db)]):
    credentials_error = HTTPException(401, "Could not validate credentials",
                                      headers={"WWW-Authenticate": "Bearer"})
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        email = payload.get("sub")
        if email is None:
            raise credentials_error
    except jwt.InvalidTokenError:
        raise credentials_error
    user = db.scalar(select(User).where(User.email == email))
    if user is None:
        raise credentials_error
    return user
```
(Full working version: **Live Coding 5.**)

### Q158. Access token vs refresh token?
**Access token:** short-lived (5–30 min), sent with every request. **Refresh token:** long-lived (days), used only at `/refresh` to get a new access token. Store the refresh token in an **httpOnly, secure cookie** (and keep a hashed copy/`jti` server-side so you can revoke it). Rotate refresh tokens on use.

### Q159. How do you implement role-based access control (RBAC)?
Put the role in the user record (or token), then use a **dependency factory** (`require_role("admin")`, Q100). For finer control use **permissions** or **OAuth2 scopes** with `Security(get_user, scopes=[...])`. (Live Coding 6)

### Q160. API key authentication?
```python
from fastapi.security import APIKeyHeader
api_key_header = APIKeyHeader(name="X-API-Key", auto_error=False)

def verify_api_key(key: Annotated[str | None, Depends(api_key_header)]):
    if key is None or not secrets.compare_digest(key, settings.api_key):
        raise HTTPException(401, "Invalid API key")
```
Use for machine-to-machine access; store only **hashed** keys in a DB for multiple clients.

### Q161. HTTP Basic auth?
`HTTPBasic()` reads `Authorization: Basic ...`. Use only over **HTTPS**; fine for simple internal tools (like protecting `/docs`).

### Q162. What is CORS and how do you configure it? (VERY COMMON)
Browsers block a page on one origin from calling an API on another origin unless the API allows it via CORS headers. It's enforced by the **browser**, not by Postman/curl.
```python
from fastapi.middleware.cors import CORSMiddleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://myapp.com", "http://localhost:3000"],
    allow_credentials=True,          # needed for cookies/Authorization from browsers
    allow_methods=["*"],
    allow_headers=["*"],
)
```
Pitfalls: with `allow_credentials=True` you **cannot use `"*"` as origin** in browsers; origin must match exactly (scheme + host + port); add the middleware properly (errors raised before it can lose CORS headers); preflight `OPTIONS` requests are handled by the middleware.

### Q163. CSRF — does it affect FastAPI APIs?
CSRF abuses **cookies sent automatically** by the browser. APIs that use the `Authorization: Bearer` header are largely safe from CSRF. If you authenticate with **cookies**, use `SameSite` cookies, CSRF tokens, and check `Origin`.

### Q164. How do you do rate limiting?
Options: **`slowapi`** (decorator-based, Redis backend), a custom **dependency/middleware** (Live Coding 12), or at the **gateway** (Nginx, Cloudflare, API Gateway) which is the most robust. Return **429** with a `Retry-After` header. Limit by IP or API key/user.

### Q165. HTTPS and related middleware?
Terminate TLS at Nginx/load balancer in production. FastAPI middlewares: `HTTPSRedirectMiddleware`, `TrustedHostMiddleware(allowed_hosts=[...])` (prevents Host header attacks), and set HSTS via a header middleware or the proxy. Behind a proxy use `--proxy-headers` so URLs/IPs are correct.

### Q166. Common vulnerabilities and defenses (API context)?
- **SQL/NoSQL injection:** ORM/parameterized queries, Pydantic validation.
- **Broken authentication:** strong hashing, short-lived tokens, rate limit logins.
- **BOLA/IDOR (accessing other users' objects):** always check **ownership** (`item.owner_id == user.id`).
- **Excessive data exposure:** use `response_model` to filter fields.
- **Mass assignment:** don't accept fields like `is_admin` in input models.
- **Misconfiguration:** disable docs in prod if private, restrict CORS, no debug mode.
- **XSS:** escape output if you render HTML; send correct content types.
- **SSRF:** validate URLs the server will fetch.

### Q167. How do you manage secrets?
Environment variables / secret managers (AWS Secrets Manager, Vault, Kubernetes Secrets), `pydantic-settings` for loading and validating, never commit `.env`, rotate keys, use different secrets per environment. Generate a strong key: `openssl rand -hex 32`.

### Q168. Why is Pydantic validation a security feature?
It rejects malformed or unexpected input early (types, lengths, patterns, `extra="forbid"`), reducing injection and logic bugs.

### Q169. Where should the client store tokens?
`localStorage` is simple but exposed to XSS; **httpOnly + Secure + SameSite cookies** protect against XSS token theft but need CSRF care. Mobile apps use secure storage (Keychain/Keystore).

### Q170. How do you log out / revoke a JWT?
JWTs are stateless, so: keep **access tokens short-lived**; delete tokens on the client; for real revocation maintain a **blocklist of `jti`** (Redis with TTL = token expiry) or store refresh tokens in the DB and delete them on logout.

### Q171. OAuth2 flows — what should you know?
**Authorization Code (+PKCE):** for web/mobile apps with third-party login (Google, GitHub). **Client Credentials:** service-to-service. **Password flow:** your own first-party login (as in the FastAPI tutorial; discouraged by the OAuth2 spec for third-party clients). Libraries: **Authlib** for social login. Identity providers: Auth0, Keycloak, AWS Cognito, Firebase.

### Q172. Useful security headers?
`Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `X-Frame-Options`, `Content-Security-Policy`, `Referrer-Policy`. Add via middleware or the reverse proxy.

### Q173. Timing attacks and `secrets.compare_digest`?
Normal `==` on secrets can leak information through timing. Use `secrets.compare_digest(a, b)` to compare API keys/tokens in constant time.

### Q174. Brute-force protection on login?
Rate limit by IP + username, add incremental delays or temporary lockouts, monitor failed attempts, use CAPTCHA/MFA for sensitive apps, and return the **same error** for "user not found" and "wrong password."

### Q175. How does the Swagger "Authorize" button work?
`OAuth2PasswordBearer(tokenUrl="token")` adds a security scheme to the OpenAPI schema. Swagger UI shows **Authorize**; you enter username/password, it calls `/token`, stores the token, and adds the `Authorization` header to later "Try it out" calls.

### Q176. Common JWT mistakes?
Using a weak/hard-coded secret • not setting `exp` • not restricting `algorithms=` when decoding • putting sensitive data in the payload • very long-lived access tokens • storing tokens insecurely • not validating `sub`/user status (deleted/disabled users).

### Q177. Multi-tenancy basics?
Identify the tenant (from subdomain, header, or token claim) in a dependency and scope **every query** by `tenant_id` (or use schema/DB per tenant). The risk is a missed filter leaking data between tenants.

### Q178. A secure endpoint checklist (say this in interviews)
HTTPS • authentication dependency • authorization/ownership check • input validation with Pydantic • `response_model` that hides sensitive fields • rate limiting • proper status codes without leaking internals • logging/audit • secrets from env • CORS restricted.

---
