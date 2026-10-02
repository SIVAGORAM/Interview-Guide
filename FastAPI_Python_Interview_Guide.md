# Master FastAPI and Python Interview Guide: Python, FastAPI, Pydantic, SQLAlchemy, Async, AI/LLM Pipelines

**For:** Goram Siva Prasad | **Role:** Full Stack Engineer (6 months to 1 year experience) | **Round:** Technical (theory + live coding + pseudo code)

> The director said they mainly want a **Python backend**: write APIs, write basic Python code and pseudo code. This guide covers Python fundamentals, FastAPI, databases, auth, async, testing, deployment, and the AI document extraction pipeline from your resume. Type every code sample yourself. Reading is not enough for a live coding round.

---

## Table of Contents

1. [How to answer in the interview](#1-how-to-answer-in-the-interview)
2. [Resume based questions](#2-resume-based-questions)
3. [Python fundamentals](#3-python-fundamentals)
4. [Python output based questions](#4-python-output-based-questions)
5. [Python OOP, decorators, generators, context managers](#5-python-oop-decorators-generators-context-managers)
6. [FastAPI fundamentals](#6-fastapi-fundamentals)
7. [Pydantic in depth](#7-pydantic-in-depth)
8. [Dependencies, security and authentication](#8-dependencies-security-and-authentication)
9. [Databases with FastAPI](#9-databases-with-fastapi)
10. [Async, concurrency and background work](#10-async-concurrency-and-background-work)
11. [Other FastAPI features: files, WebSockets, middleware, errors, settings](#11-other-fastapi-features)
12. [Testing, deployment and performance](#12-testing-deployment-and-performance)
13. [AI and LLM document extraction pipeline](#13-ai-and-llm-document-extraction-pipeline)
14. [Scenario based questions](#14-scenario-based-questions)
15. [Live coding: first API and CRUD](#15-live-coding-first-api-and-crud)
16. [Live coding: database and authentication](#16-live-coding-database-and-authentication)
17. [Live coding: more endpoint tasks](#17-live-coding-more-endpoint-tasks)
18. [Python coding problems](#18-python-coding-problems)
19. [Pseudo code guide](#19-pseudo-code-guide)
20. [Interview day strategy](#20-interview-day-strategy)
21. [Last day revision checklist](#21-last-day-revision-checklist)

---

## 1. How to answer in the interview

Use this pattern for theory questions:
1. **Definition** in one line.
2. **Why we use it.**
3. **Small example or your project use.**
4. **Pitfall or comparison** (shows depth).

**Example, "What is Pydantic?"**
"Pydantic is a data validation library that uses Python type hints. You define a model with typed fields, and Pydantic validates and converts incoming data, raising clear errors if it is wrong. FastAPI uses it for request bodies and responses, so I get validation and automatic API docs without extra code. In my document extraction project I used a Pydantic model to validate the JSON that came back from the LLM before saving it."

**For live coding:**
- Ask about inputs, outputs and edge cases first.
- Say your plan: "I will define the schemas, then the routes, then error handling."
- Start with the simplest working version, run it, then improve.
- Use type hints, Pydantic models, correct status codes and `HTTPException`.
- For pure Python problems, write pseudo code first, then code, then test with one example and one edge case.
- Think aloud. If you do not know something, say what you know and how you would find out.

---

## 2. Resume based questions

Prepare each answer with **Problem, What I built, Tools, Result.** Change the details to match what you really did.

### Q1. Explain your AI document extraction pipeline end to end.
**Say it like this (adapt to your real work):**
"Enterprise clients had invoices and PDFs that were entered manually. I built a FastAPI service where a document is uploaded, text is extracted with OCR (or directly from digital PDFs), and an LLM turns that text into structured JSON using a fixed schema. I validate the JSON with Pydantic and business checks, store it in PostgreSQL, and a React interface lets the user review and correct it. This reduced manual data entry by about 70% for two clients."

**Flow to draw on a whiteboard:**
`Upload -> store file -> OCR / text extraction -> prompt LLM with schema -> parse JSON -> validate (Pydantic + totals check) -> save -> review screen -> approved data`

**Follow-up questions to prepare:**
- **Why FastAPI?** Async support for slow I/O (OCR and LLM calls), Pydantic validation, automatic docs, type hints, speed.
- **Why three LLM providers (OpenAI, Claude, Gemini)?** Compare accuracy and cost, fall back if one provider fails or rate limits, and choose per document type. Say what you actually did.
- **How did you make LLM output reliable?** Fixed JSON schema in the prompt, low temperature, "return only JSON", `null` for missing fields, Pydantic validation, retry with the validation error in the prompt, and a manual review status when validation still fails.
- **How do you handle hallucinations?** Never trust blindly. Validate formats (dates, amounts), cross-check totals (sum of line items equals subtotal, subtotal plus tax equals total), keep the source text for audit, and flag low-confidence documents for human review.
- **How did you handle poor scans?** Preprocess images (higher DPI, grayscale, deskew, denoise) before OCR, and fall back to the LLM with the image if text is poor (only if you did this).
- **How did you handle long documents?** Split by page or section, extract per chunk, merge the results, and respect token limits.
- **How did you measure the 70% reduction?** Be ready with a simple honest explanation: time or number of fields entered manually before vs after, or the share of documents that needed no correction. If you do not know the exact method, say how you would measure it (field-level accuracy on a labeled sample set).
- **How did you handle slow processing?** Do not hold the HTTP request. Return a job id, process in the background, and let the client poll the status endpoint.
- **How did you handle errors and retries?** Timeouts, retries with exponential backoff for provider errors and rate limits, and clear statuses (`queued`, `processing`, `needs_review`, `done`, `failed`).
- **How did you deal with cost?** Smaller models for simple documents, cache results, avoid sending unnecessary text, and limit retries.
- **How do you protect sensitive data?** HTTPS, private file storage, access control per client, no sensitive data in logs, and check the provider's data retention terms.

### Q2. How did FastAPI work with the React frontend and the Agri-Tech ERP?
Be ready to say how the React app called the API (JWT in the header, CORS configured for the frontend origin), how Pydantic schemas became the API contract, and how the OpenAPI docs helped the frontend team.

### Q3. How did you do authentication, roles and security in your backends?
JWT access tokens, password hashing, RBAC through dependencies (`Depends(require_roles("admin"))`), validation of all input, and checks that the user owns the resource.

### Q4. How do you deploy a FastAPI service?
Docker image, Uvicorn (or Gunicorn with Uvicorn workers), Nginx or ALB in front, environment variables for secrets, health check endpoint, logs to CloudWatch, deployed on AWS EC2 (and mention Terraform and CI/CD only to the extent you actually used them).

### Q5. What was the hardest problem you solved in Python?
Pick one real example: inconsistent LLM output, a slow endpoint blocked by synchronous code, duplicate processing, memory problems with large PDFs, or timeouts. Explain symptom, how you found it, fix and result.

---

## 3. Python fundamentals

### Q1. What are the main built-in data types? Mutable vs immutable?
Immutable: `int`, `float`, `bool`, `str`, `tuple`, `frozenset`, `bytes`. Mutable: `list`, `dict`, `set`. Immutable objects cannot be changed after creation, so "changing" them creates a new object. Only immutable (hashable) objects can be dictionary keys or set members.

### Q2. List vs tuple vs set vs dict?
| | list | tuple | set | dict |
|---|---|---|---|---|
| Ordered | yes | yes | no | yes (insertion order) |
| Mutable | yes | no | yes | yes |
| Duplicates | yes | yes | no | keys are unique |
| Use | collection that changes | fixed record | unique items, fast membership | key to value lookup |

### Q3. `==` vs `is`?
`==` compares values. `is` compares identity (the same object in memory). Use `is` only for `None` (`if x is None`).

### Q4. Shallow copy vs deep copy?
A shallow copy copies the outer object but shares nested objects. A deep copy copies everything.
```python
import copy
a = [[1, 2], [3]]
shallow = copy.copy(a)        # or a.copy() or a[:]
deep = copy.deepcopy(a)
a[0].append(99)
# shallow[0] also changed, deep[0] did not
```

### Q5. What are `*args` and `**kwargs`?
`*args` collects extra positional arguments into a tuple. `**kwargs` collects extra keyword arguments into a dict.
```python
def show(*args, **kwargs):
    print(args, kwargs)
show(1, 2, name="Siva")   # (1, 2) {'name': 'Siva'}
```

### Q6. What is scope? Explain LEGB.
Python looks up names in this order: **L**ocal, **E**nclosing function, **G**lobal, **B**uilt-in. Use `global` to change a global inside a function and `nonlocal` to change an enclosing function's variable.

### Q7. What is a closure?
A function that remembers variables from the scope where it was created, even after that outer function has finished.
```python
def counter():
    count = 0
    def inc():
        nonlocal count
        count += 1
        return count
    return inc
c = counter(); c(); c()   # 2
```

### Q8. What is a decorator?
A function that takes a function and returns a new function with extra behaviour (logging, timing, auth, retry). The `@name` syntax is just `func = name(func)`. FastAPI's `@app.get("/")` is a decorator that registers a route.

### Q9. Iterator vs generator?
An iterator is any object with `__iter__` and `__next__`. A generator is a simple way to create an iterator using a function with `yield`. It produces values one at a time and keeps its state, so it uses very little memory (good for large files).

### Q10. List comprehension vs generator expression?
`[x*x for x in data]` builds the whole list in memory. `(x*x for x in data)` is lazy and produces values on demand.

### Q11. `lambda`, `map`, `filter`, `reduce`?
`lambda` is a small anonymous function. `map(f, items)` applies `f` to each item, `filter(f, items)` keeps items where `f` is true, and `functools.reduce` combines items into one value. Comprehensions are usually more readable.

### Q12. What is the difference between `append`, `extend` and `insert`?
`append(x)` adds one item to the end. `extend(iterable)` adds every item of an iterable. `insert(i, x)` adds at position `i` (slower, O(n)).

### Q13. `sort()` vs `sorted()`?
`list.sort()` sorts in place and returns `None`. `sorted(iterable)` returns a new sorted list and works on any iterable. Both are stable and accept `key=` and `reverse=`.
```python
users.sort(key=lambda u: u["age"])
sorted(words, key=len, reverse=True)
```

### Q14. How does a dictionary work internally? Time complexity?
A hash table. Average O(1) for get, set, delete and membership. Keys must be hashable. Lists have O(1) append and index access, but O(n) for `in` and `insert(0)`. Sets have O(1) average membership.

### Q15. What is the GIL?
The Global Interpreter Lock in CPython lets only one thread execute Python bytecode at a time. So threads do not speed up CPU-heavy code, but they work well for I/O-bound work because the lock is released while waiting. For CPU-bound work use `multiprocessing`. For many concurrent I/O tasks use `asyncio`.

### Q16. Threading vs multiprocessing vs asyncio?
- **Threading:** several threads, good for blocking I/O, limited by the GIL for CPU work.
- **Multiprocessing:** separate processes with their own memory, true parallelism for CPU work.
- **asyncio:** one thread, tasks cooperate by `await`ing. Best for many network calls (APIs, databases, LLM calls).

### Q17. How does Python manage memory?
Reference counting plus a cyclic garbage collector. An object is freed when nothing refers to it. Memory leaks usually come from references kept in global lists, caches or closures.

### Q18. What is the mutable default argument problem?
```python
def add(item, items=[]):       # the list is created once, when the function is defined
    items.append(item)
    return items
add(1); add(2)                 # [1, 2], surprise!

def add(item, items=None):     # correct
    if items is None:
        items = []
    items.append(item)
    return items
```

### Q19. What is `if __name__ == "__main__":`?
The block runs only when the file is executed directly, not when it is imported as a module.

### Q20. Exceptions: `try`, `except`, `else`, `finally`
```python
try:
    value = int(text)
except ValueError:
    print("Not a number")
except (TypeError, KeyError) as e:
    print(e)
else:
    print("No error")        # runs only if there was no exception
finally:
    print("Always runs")     # cleanup
```
Raise your own errors with `raise ValueError("message")`. Create custom exceptions by subclassing `Exception`. Catch specific exceptions, never a bare `except:`.

### Q21. What is a context manager? Why use `with`?
An object that sets up and cleans up a resource automatically (files, locks, database sessions).
```python
with open("data.txt") as f:
    text = f.read()
# file is closed here, even if an error happened
```

### Q22. What are type hints?
Optional annotations like `def add(a: int, b: int) -> int`. They do not enforce types at runtime, but they help editors, linters (mypy) and frameworks. FastAPI and Pydantic use them to validate data and generate docs.
```python
from typing import Optional
def find(id: int) -> dict | None: ...      # Python 3.10+ union syntax
names: list[str] = []
```

### Q23. f-strings
`f"Hello {name}, total {price:.2f}"`. Format numbers with `:,` for commas, `:.2f` for 2 decimals, `:>10` to align.

### Q24. Useful standard library modules
- `collections`: `Counter`, `defaultdict`, `deque`, `OrderedDict`, `namedtuple`
- `itertools`: `chain`, `groupby`, `combinations`, `permutations`, `islice`
- `functools`: `lru_cache`, `partial`, `wraps`, `reduce`
- `heapq` (priority queue), `bisect` (binary search), `math`, `random`
- `datetime`, `json`, `re` (regex), `pathlib`, `os`, `logging`, `dataclasses`, `enum`, `uuid`, `asyncio`

### Q25. Dataclasses
```python
from dataclasses import dataclass, field

@dataclass
class Item:
    name: str
    price: float
    tags: list[str] = field(default_factory=list)
```
Generates `__init__`, `__repr__` and `__eq__` automatically. Pydantic models add validation on top of the same idea.

### Q26. What are virtual environments and `requirements.txt`?
A virtual environment isolates a project's packages. Create with `python -m venv .venv`, activate, then `pip install -r requirements.txt`. Pin versions (`pip freeze > requirements.txt`) or use Poetry or uv.

### Q27. What are modules and packages?
A module is a `.py` file. A package is a folder of modules (with `__init__.py`). Import with `import pkg.module` or `from pkg.module import name`. Avoid circular imports by moving shared code to a third module or importing inside a function.

### Q28. How does string handling work? Immutability.
Strings are immutable. Common methods: `split`, `join`, `strip`, `replace`, `lower`, `startswith`, `find`. Slicing: `s[::-1]` reverses. Build large strings with `"".join(parts)`, not repeated `+=`.

### Q29. `@staticmethod`, `@classmethod`, instance method?
Instance method gets `self` (the object). `@classmethod` gets `cls` (the class), often used as an alternative constructor. `@staticmethod` gets neither, it is a plain function inside the class namespace.

### Q30. Python vs other languages (why Python for backend and AI)?
Readable, huge ecosystem (FastAPI, Django, pandas, ML and LLM libraries), fast to develop. Weaker at raw CPU speed, so we use async, workers and native extensions where needed.

### Q31. What is `__slots__`, `__init__` vs `__new__`, `__repr__` vs `__str__`?
Know the one-liners: `__init__` initializes an object, `__new__` creates it. `__repr__` is for developers (unambiguous), `__str__` is for users. `__slots__` saves memory by blocking the per-object dict.

### Q32. What are Python's truthy and falsy values?
Falsy: `None`, `False`, `0`, `0.0`, `""`, `[]`, `{}`, `set()`, `()`. Everything else is truthy. (`bool("False")` is `True`.)

### Q33. Pass by value or by reference?
Python passes **object references** ("pass by assignment"). A function can change a mutable argument in place, but reassigning the parameter name does not affect the caller.

### Q34. Common time complexities to remember
List index O(1), list append O(1) amortized, `x in list` O(n), `x in set/dict` O(1) average, sort O(n log n), `deque` append and pop at both ends O(1), `heapq` push and pop O(log n).

---

## 4. Python output based questions

**Q1. Mutable default argument**
```python
def f(x, l=[]):
    l.append(x)
    return l
print(f(1)); print(f(2))
```
**Answer:** `[1]` then `[1, 2]`.

**Q2. Late binding in closures**
```python
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs])
```
**Answer:** `[2, 2, 2]`. The lambdas read `i` when called, after the loop ended. Fix: `lambda i=i: i`.

**Q3. List multiplication aliasing**
```python
grid = [[0] * 3] * 3
grid[0][0] = 1
print(grid)
```
**Answer:** `[[1, 0, 0], [1, 0, 0], [1, 0, 0]]`. All rows are the same list. Fix: `[[0] * 3 for _ in range(3)]`.

**Q4. Same reference**
```python
a = b = []
a.append(1)
print(b)                  # [1]
x = [1, 2]; y = x
y += [3]                  # in place, x also changes
y = y + [4]               # creates a new list, x unchanged
print(x, y)               # [1, 2, 3] [1, 2, 3, 4]
```

**Q5. `try` / `finally`**
```python
def f():
    try:
        return 1
    finally:
        return 2
print(f())
```
**Answer:** `2`. The `finally` return wins.

**Q6. Rounding and division**
```python
print(round(2.5), round(3.5))    # 2 4  (banker's rounding)
print(7 // 2, -7 // 2)           # 3 -4 (floor division)
print(-7 % 3)                    # 2
print(0.1 + 0.2 == 0.3)          # False
print(type(4 / 2))               # <class 'float'>
```

**Q7. Class variable vs instance variable**
```python
class A:
    items = []                   # shared by all instances
    def add(self, x):
        self.items.append(x)
a, b = A(), A()
a.add(1)
print(b.items)                   # [1]
```

**Q8. Generator is exhausted after one pass**
```python
g = (x * 2 for x in range(3))
print(list(g))                   # [0, 2, 4]
print(list(g))                   # []
```

**Q9. `is` and small integers**
```python
a = 256; b = 256
print(a is b)                    # True (CPython caches -5 to 256)
x = 1000; y = 1000
print(x == y)                    # True, always use == for values
```

**Q10. Truthiness and slicing**
```python
print(bool("False"), bool([]), bool(0))     # True False False
print("python"[::-1])                        # nohtyp
print([1, 2, 3, 4][1:3])                     # [2, 3]
print(sorted("banana"))                      # ['a', 'a', 'a', 'b', 'n', 'n']
```

**Q11. Dictionary behaviour**
```python
d = {}
d[1] = "a"; d[1.0] = "b"; d[True] = "c"
print(d)                         # {1: 'c'}  (1, 1.0 and True are equal and hash the same)
```

**Q12. Exception order**
```python
try:
    int("x")
except Exception:
    print("general")
except ValueError:
    print("value")
```
**Answer:** prints `general`. The first matching `except` runs, so put specific exceptions first.

---
## 5. Python OOP, decorators, generators, context managers

### OOP basics: class, inheritance, super, property
```python
class Account:
    bank = "ABC Bank"                      # class variable

    def __init__(self, owner: str, balance: float = 0):
        self.owner = owner                 # instance variables
        self._balance = balance            # single underscore: "protected" by convention

    @property
    def balance(self) -> float:            # read-only attribute
        return self._balance

    def deposit(self, amount: float) -> None:
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self._balance += amount

    def withdraw(self, amount: float) -> None:
        if amount > self._balance:
            raise ValueError("Insufficient funds")
        self._balance -= amount

    def __repr__(self) -> str:
        return f"Account({self.owner!r}, {self._balance})"


class SavingsAccount(Account):
    def __init__(self, owner: str, balance: float = 0, rate: float = 0.04):
        super().__init__(owner, balance)   # call the parent constructor
        self.rate = rate

    def add_interest(self) -> None:
        self.deposit(self._balance * self.rate)
```

### OOP questions
- **The four pillars?** Encapsulation (hide internals, expose a clean interface), inheritance (reuse code), polymorphism (same method name, different behaviour), abstraction (hide complexity behind a simple interface).
- **What is MRO?** Method Resolution Order, the order Python searches parent classes (`ClassName.__mro__`). It uses the C3 linearization and decides which method `super()` calls with multiple inheritance.
- **Composition vs inheritance?** Inheritance means "is a" (SavingsAccount is an Account). Composition means "has a" (an Order has a list of Items). Prefer composition when the relationship is not clearly "is a".
- **Public, protected, private in Python?** Only conventions: `name` public, `_name` internal, `__name` is name-mangled (`_Class__name`) to avoid clashes in subclasses. Python does not enforce privacy.
- **Dunder (magic) methods?** `__init__`, `__repr__`, `__str__`, `__eq__`, `__hash__`, `__len__`, `__getitem__`, `__iter__`, `__enter__`/`__exit__`, `__call__`.
- **What is an abstract class?**
```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self) -> float: ...

class Circle(Shape):
    def __init__(self, r: float): self.r = r
    def area(self) -> float: return 3.14159 * self.r ** 2

# Shape() raises TypeError because it has an abstract method
```
- **Method overloading?** Python has no overloading by signature. Use default arguments, `*args`, or `functools.singledispatch`.

### Decorators
```python
import functools, time

def timer(func):
    @functools.wraps(func)                  # keeps the original name and docstring
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.perf_counter() - start:.4f}s")
        return result
    return wrapper

@timer
def work(n):
    return sum(range(n))
```
**Decorator with arguments (retry):**
```python
def retry(times=3, delay=0.5, exceptions=(Exception,)):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(1, times + 1):
                try:
                    return func(*args, **kwargs)
                except exceptions:
                    if attempt == times:
                        raise
                    time.sleep(delay * attempt)
        return wrapper
    return decorator

@retry(times=3, delay=1)
def call_api(): ...
```
**Why `functools.wraps`?** Without it the wrapped function loses its `__name__` and docstring, which also breaks tools like FastAPI's docs.

**Caching:**
```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)
```
FastAPI uses `@lru_cache` on `get_settings()` so settings are created only once.

### Generators and iterators
```python
def fibonacci(limit):
    a, b = 0, 1
    while a < limit:
        yield a                    # pause here and give a value
        a, b = b, a + b

for n in fibonacci(50):
    print(n)

def read_in_chunks(path, size=1024 * 1024):
    with open(path, "rb") as f:
        while chunk := f.read(size):    # walrus operator
            yield chunk                 # big files without loading all into memory

# Generator expression pipeline
total = sum(len(line) for line in open("big.txt"))
```
**Custom iterator:**
```python
class Countdown:
    def __init__(self, start): self.n = start
    def __iter__(self): return self
    def __next__(self):
        if self.n <= 0:
            raise StopIteration
        self.n -= 1
        return self.n + 1
```
FastAPI dependencies that use `yield` (like `get_db`) are generators, and `StreamingResponse` can stream a generator to the client.

### Context managers
```python
# Class based
class Timer:
    def __enter__(self):
        self.start = time.perf_counter()
        return self
    def __exit__(self, exc_type, exc, tb):
        print(f"Elapsed {time.perf_counter() - self.start:.3f}s")
        return False               # do not hide exceptions

# Using contextlib
from contextlib import contextmanager

@contextmanager
def db_session():
    session = SessionLocal()
    try:
        yield session
        session.commit()
    except Exception:
        session.rollback()
        raise
    finally:
        session.close()
```

### Other things to know
- **`*` and `**` unpacking:** `a, *rest = [1, 2, 3]`, `merged = {**d1, **d2}`, `f(*args, **kwargs)`.
- **Walrus operator `:=`:** assign inside an expression: `if (n := len(data)) > 10:`.
- **`enumerate` and `zip`:** `for i, x in enumerate(items, start=1)`, `dict(zip(keys, values))`.
- **`Enum`:**
```python
from enum import Enum
class Status(str, Enum):
    QUEUED = "queued"
    DONE = "done"
```
- **`pathlib`:** `Path("data") / "file.txt"`, `.exists()`, `.read_text()`, `.suffix`.
- **Regex:** `re.findall(r"\d+", text)`, `re.search`, `re.sub`. Useful for cleaning OCR text.
- **Logging:** `logging.getLogger(__name__)`, with levels `debug, info, warning, error`. Use it instead of `print` in services.
- **`json`:** `json.loads(text)`, `json.dumps(obj, indent=2)`. `json.loads` raises `JSONDecodeError` on bad input, which matters for LLM output.

---

## 6. FastAPI fundamentals

### Q1. What is FastAPI? Why use it?
A modern Python web framework for building APIs, built on **Starlette** (web layer) and **Pydantic** (data validation). It uses Python type hints to validate input, serialize output and generate interactive docs automatically. It supports async and is among the fastest Python frameworks.

**Say it like this:** "FastAPI gives me validation, serialization and API documentation from type hints, so I write less code and get fewer bugs. It supports async, which suits I/O heavy work like OCR calls, LLM calls and database queries."

### Q2. Why is FastAPI fast?
It runs on ASGI servers (Uvicorn) with async support, uses Starlette for routing and Pydantic (with a Rust core in v2) for validation. The speed mostly comes from handling many concurrent I/O waits efficiently, not from faster Python.

### Q3. WSGI vs ASGI?
WSGI (Flask, Django classic) is synchronous, one request per worker at a time, no WebSockets. ASGI supports async, WebSockets and long-lived connections. FastAPI is ASGI, served by Uvicorn or Hypercorn.

### Q4. FastAPI vs Flask vs Django?
| | FastAPI | Flask | Django |
|---|---|---|---|
| Style | API-focused, typed | micro framework | full framework (batteries included) |
| Async | built in | limited | partial support |
| Validation | automatic (Pydantic) | manual or extensions | forms/serializers (DRF) |
| Docs | automatic OpenAPI | extensions | extensions |
| Best for | APIs, microservices, ML services | small apps | large apps with admin, ORM, auth |

### Q5. How do you create and run a FastAPI app?
```python
from fastapi import FastAPI

app = FastAPI(title="Clinic API", version="1.0.0")

@app.get("/health")
def health():
    return {"status": "ok"}
```
Run: `uvicorn app.main:app --reload` (`app.main` is the module path and `app` is the variable). Docs: `/docs` (Swagger UI) and `/redoc`. The OpenAPI JSON is at `/openapi.json`.

### Q6. Path parameters, query parameters and request body?
```python
from fastapi import FastAPI, Query, Path
app = FastAPI()

@app.get("/users/{user_id}")                       # path parameter, type converted to int
def get_user(user_id: int = Path(gt=0)):
    ...

@app.get("/users")                                 # query parameters
def list_users(page: int = Query(1, ge=1), limit: int = Query(10, le=100), q: str | None = None):
    ...

@app.post("/users")                                # body from a Pydantic model
def create_user(user: UserCreate):
    ...
```
Rule: a parameter that appears in the path is a **path** parameter, a Pydantic model is a **body**, and other simple types are **query** parameters. Wrong types return an automatic `422` error.

### Q7. What is `response_model`? Why is it important?
It defines the shape of the response. FastAPI validates and **filters** the output through it, so fields not in the model (like `password_hash`) are never returned.
```python
@app.post("/users", response_model=UserOut, status_code=201)
def create_user(user: UserCreate): ...
```
Use separate schemas: `UserCreate` (input, has password), `UserOut` (output, no password), `UserUpdate` (all fields optional).

### Q8. How does FastAPI generate documentation?
From the route definitions, type hints, Pydantic models, `summary`, `description`, `tags` and `response_model`, it builds an OpenAPI schema and serves Swagger UI automatically. The frontend team can try the API in the browser.

### Q9. `def` vs `async def` endpoints? (very common)
- `async def` endpoints run directly on the **event loop**. Use them when you `await` async libraries (httpx, asyncpg, async SQLAlchemy). **Never call blocking code inside** (like `time.sleep`, `requests.get`, a synchronous DB call), because it blocks every other request.
- Normal `def` endpoints run in a **thread pool**, so blocking code is safe there and does not freeze the event loop.

**Say it like this:** "If my function awaits something, I use `async def`. If it uses blocking libraries like a synchronous database driver or `requests`, I use plain `def` so FastAPI runs it in a thread pool. Mixing blocking calls into `async def` is a common mistake."

### Q10. What is dependency injection in FastAPI? (`Depends`)
A way to declare things an endpoint needs (database session, current user, pagination, settings), and FastAPI creates and passes them. Dependencies can have sub-dependencies, can use `yield` for cleanup, are cached once per request, and can be replaced in tests.
```python
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users")
def users(db: Session = Depends(get_db)): ...
```

### Q11. How do you handle errors?
Raise `HTTPException(status_code=404, detail="Not found")`. For custom errors, register an exception handler:
```python
from fastapi import Request
from fastapi.responses import JSONResponse

class SlotTakenError(Exception):
    pass

@app.exception_handler(SlotTakenError)
async def slot_taken_handler(request: Request, exc: SlotTakenError):
    return JSONResponse(status_code=409, content={"detail": "Slot already booked"})
```
Validation errors return `422` automatically (`RequestValidationError`), and you can override its handler to customize the format.

### Q12. What is `APIRouter`? How do you structure a project?
`APIRouter` groups related endpoints in separate files, then you include them in the app.
```python
# app/routers/users.py
from fastapi import APIRouter
router = APIRouter(prefix="/users", tags=["users"])

@router.get("/")
def list_users(): ...

# app/main.py
app.include_router(users.router, prefix="/api/v1")
```
**Structure:**
```
app/
  main.py            (creates the app, includes routers, middleware)
  core/              (config.py, security.py)
  db/                (session.py, base.py)
  models/            (SQLAlchemy models)
  schemas/           (Pydantic models)
  routers/           (endpoints)
  services/          (business logic)
  dependencies.py    (get_db, get_current_user)
tests/
alembic/
```

### Q13. Status codes you should use
`200` OK, `201` Created, `204` No Content, `400` Bad Request, `401` Unauthorized, `403` Forbidden, `404` Not Found, `409` Conflict, `422` Validation error (FastAPI default), `429` Too Many Requests, `500` Server Error. Import constants with `from fastapi import status` (`status.HTTP_201_CREATED`).

### Q14. Different response types?
`JSONResponse` (default), `HTMLResponse`, `PlainTextResponse`, `RedirectResponse`, `FileResponse` (send a file), `StreamingResponse` (stream large data or LLM tokens).

### Q15. How do you read headers, cookies and form data?
```python
from fastapi import Header, Cookie, Form
@app.post("/x")
def x(user_agent: str | None = Header(None), session: str | None = Cookie(None), name: str = Form(...)): ...
```
Form data and file uploads need `python-multipart` installed.

### Q16. What is middleware? Example
Code that runs for every request before and after the endpoint.
```python
import time

@app.middleware("http")
async def add_process_time(request: Request, call_next):
    start = time.perf_counter()
    response = await call_next(request)
    response.headers["X-Process-Time"] = f"{time.perf_counter() - start:.4f}"
    return response
```
Built-in middleware: `CORSMiddleware`, `GZipMiddleware`, `TrustedHostMiddleware`, `HTTPSRedirectMiddleware`.

### Q17. How do you enable CORS?
```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173", "https://myapp.com"],   # not "*" with credentials
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Q18. What are startup and shutdown events? (lifespan)
Run code when the app starts (create an HTTP client, connect to Redis, load a model) and when it stops (close connections).
```python
from contextlib import asynccontextmanager
import httpx

@asynccontextmanager
async def lifespan(app: FastAPI):
    app.state.http = httpx.AsyncClient(timeout=30)     # startup
    yield
    await app.state.http.aclose()                      # shutdown

app = FastAPI(lifespan=lifespan)
```
(`@app.on_event("startup")` is the older, deprecated way.)

### Q19. How does FastAPI handle JSON serialization of ORM objects?
Return the SQLAlchemy object and set `response_model` with `model_config = ConfigDict(from_attributes=True)` so Pydantic reads attributes from the object.

### Q20. How do you version an API?
Prefix routers: `/api/v1/...`, `/api/v2/...`, keeping old versions for a transition period.

### Q21. How do you do pagination?
```python
@app.get("/items")
def items(page: int = Query(1, ge=1), limit: int = Query(10, ge=1, le=100), db: Session = Depends(get_db)):
    offset = (page - 1) * limit
    rows = db.scalars(select(Item).order_by(Item.id).offset(offset).limit(limit)).all()
    total = db.scalar(select(func.count()).select_from(Item))
    return {"items": rows, "page": page, "limit": limit, "total": total}
```
For very large tables use cursor (keyset) pagination.

### Q22. Difference between `PUT` and `PATCH` in FastAPI?
`PUT` replaces the whole resource (all fields required). `PATCH` updates only the sent fields. For PATCH make all fields optional in a `UpdateSchema` and use `payload.model_dump(exclude_unset=True)` to get only what the client sent.

### Q23. How do you serve a streaming response (like LLM tokens)?
```python
from fastapi.responses import StreamingResponse

async def token_stream():
    for word in ["Hello", " ", "world"]:
        yield word
        await asyncio.sleep(0.1)

@app.get("/stream")
async def stream():
    return StreamingResponse(token_stream(), media_type="text/plain")
```
For browsers use Server-Sent Events (`text/event-stream`) or WebSockets.

### Q24. How do you rate limit a FastAPI app?
Use `slowapi` (or a Redis counter dependency), or limit at Nginx or the API gateway. Key by user id or client IP, return `429`.

### Q25. How do you cache responses?
Cache in Redis (`fastapi-cache2`, or manual cache-aside), and use HTTP headers (`Cache-Control`, `ETag`) for client caching.

### Q26. Is FastAPI good for ML or LLM apps?
Yes. Load the model once at startup (lifespan), keep endpoints thin, run heavy CPU work outside the event loop (worker process, queue or `run_in_threadpool`), and stream long responses.

---

## 7. Pydantic in depth

Pydantic v2 is used with current FastAPI.

### Models and validation
```python
from pydantic import BaseModel, Field, EmailStr, field_validator, model_validator, ConfigDict

class UserCreate(BaseModel):
    name: str = Field(min_length=2, max_length=50)
    email: EmailStr                                   # needs: pip install email-validator
    age: int | None = Field(default=None, ge=0, le=120)
    password: str = Field(min_length=8)

    @field_validator("name")
    @classmethod
    def clean_name(cls, v: str) -> str:
        return v.strip().title()

class PasswordChange(BaseModel):
    new_password: str = Field(min_length=8)
    confirm: str

    @model_validator(mode="after")
    def passwords_match(self):
        if self.new_password != self.confirm:
            raise ValueError("Passwords do not match")
        return self

class UserOut(BaseModel):
    model_config = ConfigDict(from_attributes=True)    # allow reading from ORM objects
    id: int
    name: str
    email: EmailStr
```

### Nested models, enums and literals
```python
from enum import Enum
from typing import Literal

class Role(str, Enum):
    USER = "user"
    DOCTOR = "doctor"
    ADMIN = "admin"

class Address(BaseModel):
    city: str
    pincode: str = Field(pattern=r"^\d{6}$")

class Profile(BaseModel):
    user: UserOut
    address: Address | None = None
    role: Role = Role.USER
    status: Literal["active", "blocked"] = "active"
    tags: list[str] = []
```

### Partial update (PATCH)
```python
class UserUpdate(BaseModel):
    name: str | None = None
    age: int | None = None

@app.patch("/users/{user_id}", response_model=UserOut)
def update(user_id: int, payload: UserUpdate, db: Session = Depends(get_db)):
    user = db.get(User, user_id)
    if not user:
        raise HTTPException(404, "User not found")
    for key, value in payload.model_dump(exclude_unset=True).items():   # only sent fields
        setattr(user, key, value)
    db.commit()
    db.refresh(user)
    return user
```

### Useful methods (v2 names)
| Task | v2 method | old v1 name |
|---|---|---|
| model to dict | `model.model_dump()` | `.dict()` |
| model to JSON string | `model.model_dump_json()` | `.json()` |
| dict to model (validate) | `Model.model_validate(data)` | `parse_obj` |
| JSON string to model | `Model.model_validate_json(text)` | `parse_raw` |
| JSON schema | `Model.model_json_schema()` | `.schema()` |
| read from ORM object | `from_attributes=True` | `orm_mode = True` |
| field validator | `@field_validator` | `@validator` |

### Settings from environment (pydantic-settings)
```python
from functools import lru_cache
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env")
    database_url: str
    secret_key: str
    access_token_minutes: int = 15
    cors_origins: list[str] = ["http://localhost:5173"]

@lru_cache
def get_settings() -> Settings:
    return Settings()
```
Missing required variables stop the app at startup with a clear error.

### Handling validation errors yourself
```python
from pydantic import ValidationError
try:
    invoice = Invoice.model_validate(data)
except ValidationError as e:
    print(e.errors())        # list of dicts: field location, message, type
```

### Pydantic questions
- **Pydantic vs dataclass?** Dataclasses only store data. Pydantic validates and converts types, and can parse JSON and generate schemas.
- **Why separate schemas for input and output?** Security (never return password hashes, never accept `role` or `id` from the client) and clarity.
- **What happens with extra fields?** Ignored by default. Set `model_config = ConfigDict(extra="forbid")` to reject them.
- **What is coercion?** Pydantic converts compatible values (`"5"` to `5`) in default (lax) mode. Strict mode disallows it.
- **How is the `422` response structured?** A JSON `detail` list with `loc` (where), `msg` and `type` for each error.

---

## 8. Dependencies, security and authentication

### Reusable dependencies
```python
from typing import Annotated
from fastapi import Depends

DbSession = Annotated[Session, Depends(get_db)]            # reusable alias

class Pagination:
    def __init__(self, page: int = Query(1, ge=1), limit: int = Query(10, ge=1, le=100)):
        self.offset = (page - 1) * limit
        self.limit = limit

@app.get("/items")
def items(db: DbSession, p: Annotated[Pagination, Depends()]):
    return db.scalars(select(Item).offset(p.offset).limit(p.limit)).all()
```
Apply a dependency to a whole router: `APIRouter(dependencies=[Depends(get_current_user)])`.

### Security helpers
```python
# app/core/security.py
from datetime import datetime, timedelta, timezone
import bcrypt
import jwt                                          # PyJWT
from app.core.config import get_settings

settings = get_settings()

def hash_password(password: str) -> str:
    return bcrypt.hashpw(password.encode(), bcrypt.gensalt()).decode()

def verify_password(password: str, hashed: str) -> bool:
    return bcrypt.checkpw(password.encode(), hashed.encode())

def create_access_token(user_id: int, role: str, minutes: int | None = None) -> str:
    expire = datetime.now(timezone.utc) + timedelta(minutes=minutes or settings.access_token_minutes)
    payload = {"sub": str(user_id), "role": role, "exp": expire}
    return jwt.encode(payload, settings.secret_key, algorithm="HS256")

def decode_token(token: str) -> dict:
    return jwt.decode(token, settings.secret_key, algorithms=["HS256"])   # always pass algorithms
```

### Current user and role dependencies
```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
import jwt

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/login")

def get_current_user(token: str = Depends(oauth2_scheme), db: Session = Depends(get_db)) -> User:
    credentials_error = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = decode_token(token)
    except jwt.ExpiredSignatureError:
        raise HTTPException(status.HTTP_401_UNAUTHORIZED, "Token expired", headers={"WWW-Authenticate": "Bearer"})
    except jwt.InvalidTokenError:
        raise credentials_error

    user = db.get(User, int(payload["sub"]))
    if not user or not user.is_active:
        raise credentials_error
    return user

def require_roles(*roles: str):
    def checker(user: User = Depends(get_current_user)) -> User:
        if user.role not in roles:
            raise HTTPException(status.HTTP_403_FORBIDDEN, "Not enough permissions")
        return user
    return checker

# usage
@router.delete("/users/{user_id}", status_code=204)
def delete_user(user_id: int, db: Session = Depends(get_db), admin: User = Depends(require_roles("admin"))): ...
```
`OAuth2PasswordBearer` also adds the **Authorize** button in Swagger UI.

### Other options
```python
from fastapi.security import APIKeyHeader
import secrets

api_key_header = APIKeyHeader(name="X-API-Key")

def verify_api_key(key: str = Depends(api_key_header)):
    if not secrets.compare_digest(key, settings.service_api_key):     # constant-time compare
        raise HTTPException(403, "Invalid API key")
```
Refresh token in an HttpOnly cookie:
```python
from fastapi import Response
response.set_cookie("refresh_token", token, httponly=True, secure=True, samesite="lax",
                    max_age=7 * 24 * 3600, path="/api/v1/auth")
```

### Security questions
- **How does JWT auth work in FastAPI?** Login verifies the password hash and returns a signed token. A dependency reads the `Authorization: Bearer` header, verifies signature and expiry, loads the user and injects it into the endpoint.
- **How do you protect routes by role?** A `require_roles(...)` dependency that raises `403`. Also check ownership: a user may only access their own records.
- **How do you store passwords?** Hashed with bcrypt (or argon2), never plain text or fast hashes.
- **JWT pitfalls?** Always specify `algorithms=[...]` when decoding, use a long random secret from environment variables, keep access tokens short-lived, never put sensitive data in the payload (it is only encoded, not encrypted).
- **How do you prevent mass assignment?** Separate input schemas: `UserCreate` has no `role` or `id`. Set the role on the server.
- **How do you prevent SQL injection?** Use the ORM or parameterized queries, never build SQL with f-strings.
- **File upload risks?** Limit size, validate the extension and content type, generate your own filename, store outside the web root or in S3, and scan if needed.
- **401 vs 403?** 401: not authenticated (no or invalid token). 403: authenticated but not allowed.
- **How do you handle secrets?** Environment variables or a secret manager, never in Git.

---

## 9. Databases with FastAPI

### SQLAlchemy 2.0 setup (sync)
```python
# app/db/session.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, DeclarativeBase

engine = create_engine(settings.database_url, pool_pre_ping=True, pool_size=10, max_overflow=20)
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

### Models and relationships
```python
from datetime import datetime
from sqlalchemy import String, ForeignKey, DateTime, func, UniqueConstraint
from sqlalchemy.orm import Mapped, mapped_column, relationship

class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    email: Mapped[str] = mapped_column(String(255), unique=True, index=True)
    password_hash: Mapped[str] = mapped_column(String(255))
    role: Mapped[str] = mapped_column(String(20), default="user")
    is_active: Mapped[bool] = mapped_column(default=True)
    created_at: Mapped[datetime] = mapped_column(DateTime(timezone=True), server_default=func.now())

    appointments: Mapped[list["Appointment"]] = relationship(back_populates="patient")


class Appointment(Base):
    __tablename__ = "appointments"
    __table_args__ = (UniqueConstraint("doctor_id", "slot_start", name="uq_doctor_slot"),)

    id: Mapped[int] = mapped_column(primary_key=True)
    patient_id: Mapped[int] = mapped_column(ForeignKey("users.id"), index=True)
    doctor_id: Mapped[int] = mapped_column(ForeignKey("users.id"))
    slot_start: Mapped[datetime] = mapped_column(DateTime(timezone=True))
    status: Mapped[str] = mapped_column(String(20), default="pending")

    patient: Mapped["User"] = relationship(back_populates="appointments", foreign_keys=[patient_id])
```

### CRUD with the 2.0 query style
```python
from sqlalchemy import select, func
from sqlalchemy.exc import IntegrityError

def get_user_by_email(db: Session, email: str) -> User | None:
    return db.scalars(select(User).where(User.email == email)).first()

def create_user(db: Session, data: UserCreate) -> User:
    user = User(name=data.name, email=data.email, password_hash=hash_password(data.password))
    db.add(user)
    try:
        db.commit()
    except IntegrityError:                       # duplicate email
        db.rollback()
        raise HTTPException(409, "Email already registered")
    db.refresh(user)
    return user

def list_users(db: Session, offset: int, limit: int):
    return db.scalars(select(User).order_by(User.id).offset(offset).limit(limit)).all()

def count_users(db: Session) -> int:
    return db.scalar(select(func.count()).select_from(User))
```
Other basics: `db.get(User, id)` fetches by primary key, `db.delete(obj)` then `db.commit()`, `db.flush()` sends SQL without committing.

### Async SQLAlchemy
```python
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker, AsyncSession

engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/db", pool_pre_ping=True)
AsyncSessionLocal = async_sessionmaker(engine, expire_on_commit=False)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/users/{user_id}", response_model=UserOut)
async def get_user(user_id: int, db: AsyncSession = Depends(get_db)):
    user = await db.get(User, user_id)
    if not user:
        raise HTTPException(404, "User not found")
    return user

# queries
result = await db.execute(select(User).where(User.role == "doctor"))
doctors = result.scalars().all()
await db.commit()
```
Use the **async** setup with `async def` endpoints. With sync SQLAlchemy use plain `def` endpoints.

### N+1 problem and eager loading
Accessing `user.appointments` inside a loop runs one query per user. Fix with eager loading:
```python
from sqlalchemy.orm import selectinload, joinedload

users = db.scalars(select(User).options(selectinload(User.appointments))).all()
```
`selectinload` runs one extra query using `IN`, good for collections. `joinedload` uses a JOIN, good for single related objects. In async, lazy loading does not work, so eager loading is required.

### Transactions
Session operations are in a transaction until `commit()`. Use `rollback()` on errors. For multi-step operations, do everything and commit once, so either all steps succeed or none.
```python
try:
    db.add(Appointment(...))
    wallet.balance -= fee
    db.commit()
except Exception:
    db.rollback()
    raise
```

### Alembic migrations
```bash
alembic init alembic
alembic revision --autogenerate -m "create users"
alembic upgrade head          # apply
alembic downgrade -1          # undo the last one
```
Set `target_metadata = Base.metadata` in `alembic/env.py`. `Base.metadata.create_all(engine)` is fine for quick demos, but real projects use migrations so schema changes are tracked and repeatable.

### Database questions
- **Why a session per request?** Each request gets its own session and transaction, which is closed afterwards. Sharing sessions between requests causes data leaks and race conditions.
- **What is the connection pool?** A set of reusable connections (`pool_size`, `max_overflow`). With several workers, total connections = workers x pool size, so keep within the database limit.
- **ORM vs raw SQL?** ORM is safer and faster to write. Raw SQL (`text()` with bound parameters) gives control for complex reports.
- **How do you prevent double booking?** A unique constraint on `(doctor_id, slot_start)`, and handle `IntegrityError` with a `409` response. Do not rely on "check then insert".
- **How do you speed up queries?** Indexes on filter and join columns, select only needed columns, pagination, eager loading, and checking `EXPLAIN ANALYZE`.
- **How do you use MongoDB with FastAPI?** Motor (async driver) or Beanie (ODM based on Pydantic). Pydantic models map naturally to documents.
- **Where does business logic go?** In a service layer, not in the route function. Routes handle HTTP, services handle rules, and repositories or CRUD functions handle the database.

---
## 10. Async, concurrency and background work

### Core concepts
- **Coroutine:** a function defined with `async def`. Calling it returns a coroutine object, it runs only when awaited.
- **`await`:** pause this coroutine until the awaited operation finishes, and let the event loop run other tasks meanwhile.
- **Event loop:** runs tasks one at a time on one thread, switching whenever a task awaits.
- **Concurrency vs parallelism:** asyncio gives concurrency (many tasks in progress, one running at any moment). Parallelism needs several processes.

### Running tasks together
```python
import asyncio

async def fetch(n: int) -> int:
    await asyncio.sleep(1)           # simulates an API or DB call
    return n * 2

async def main():
    results = await asyncio.gather(fetch(1), fetch(2), fetch(3))   # about 1 second, not 3
    print(results)                                                  # [2, 4, 6]

asyncio.run(main())
```
```python
# gather with error handling: keep going if one fails
results = await asyncio.gather(*tasks, return_exceptions=True)

# timeout
try:
    result = await asyncio.wait_for(fetch(1), timeout=2)
except asyncio.TimeoutError:
    ...

# limit concurrency (do not send 500 LLM calls at once)
sem = asyncio.Semaphore(5)
async def limited(doc):
    async with sem:
        return await process(doc)
await asyncio.gather(*(limited(d) for d in docs))
```

### Calling external APIs without blocking
```python
import httpx

@app.get("/weather")
async def weather(request: Request, city: str):
    client: httpx.AsyncClient = request.app.state.http         # created in lifespan
    r = await client.get("https://api.example.com/weather", params={"q": city})
    r.raise_for_status()
    return r.json()
```
Reuse one `AsyncClient` (connection pooling) instead of creating one per request.

### The blocking mistake (very common interview point)
```python
@app.get("/bad")
async def bad():
    time.sleep(5)                 # blocks the whole event loop, all users wait
    requests.get(url)             # same problem
    return "done"

@app.get("/good")
async def good():
    await asyncio.sleep(5)        # does not block
    async with httpx.AsyncClient() as c:
        await c.get(url)

@app.get("/ok-sync")
def ok_sync():                    # plain def runs in a thread pool, blocking is safe here
    time.sleep(5)
    return "done"
```
**If you must call blocking code from `async def`:**
```python
from fastapi.concurrency import run_in_threadpool
result = await run_in_threadpool(blocking_function, arg)
# or: await asyncio.to_thread(blocking_function, arg)
```
For **CPU heavy** work (image processing, large parsing) threads do not help because of the GIL. Use a separate process (`ProcessPoolExecutor`), a worker queue (Celery), or a separate service.

### BackgroundTasks (simple, in the same process)
```python
from fastapi import BackgroundTasks

def send_email(to: str, body: str):
    ...

@app.post("/register", status_code=201)
def register(user: UserCreate, background: BackgroundTasks):
    ...
    background.add_task(send_email, user.email, "Welcome!")
    return {"message": "Registered"}
```
**Limits:** runs after the response in the same process, no retries, lost if the server restarts, not for long or critical jobs. A background function must create its own database session, because the request's session is already closed.

### Celery (a real task queue)
```python
# app/worker.py
from celery import Celery

celery_app = Celery("worker", broker="redis://localhost:6379/0", backend="redis://localhost:6379/1")

@celery_app.task(bind=True, max_retries=3)
def process_document(self, doc_id: int):
    try:
        ...
    except Exception as exc:
        raise self.retry(exc=exc, countdown=2 ** self.request.retries)   # exponential backoff

# in the endpoint
process_document.delay(doc.id)
```
Run the worker: `celery -A app.worker.celery_app worker --loglevel=info`.

| | BackgroundTasks | Celery or ARQ |
|---|---|---|
| Setup | none | needs a broker (Redis or RabbitMQ) and worker process |
| Retries | no | yes |
| Survives restarts | no | yes (jobs stay in the queue) |
| Scales separately | no | yes |
| Use for | small quick tasks (send email) | long jobs (OCR, LLM extraction, reports) |

### Async questions
- **What is the difference between `asyncio.gather` and `create_task`?** `create_task` schedules one coroutine to run in the background. `gather` runs several and waits for all results.
- **Does async make code faster?** It improves **throughput** when waiting on I/O (many requests at once). A single CPU-heavy function is not faster.
- **When would you use threads, processes or asyncio?** I/O with async libraries: asyncio. Blocking I/O libraries: threads. CPU heavy: processes or a queue with workers.
- **What happens if one request blocks the event loop?** Every other request on that worker waits, so latency spikes for everyone.
- **How many workers should you run?** For CPU cores N, a common start is N workers (Gunicorn with Uvicorn workers), then tune with load tests. Each worker has its own event loop and memory.

---

## 11. Other FastAPI features

### File upload
```python
from fastapi import UploadFile, File

@app.post("/upload")
async def upload(file: UploadFile = File(...)):
    content = await file.read()                       # reads everything into memory (small files)
    return {"filename": file.filename, "type": file.content_type, "size": len(content)}
```
- `UploadFile` is better than `bytes` for large files because it spools to disk, and has `.read()`, `.write()` and `.seek()`.
- Multiple files: `files: list[UploadFile]`.
- Requires `pip install python-multipart`.
- Validate type and size, and never use the client's filename directly.

### WebSockets
```python
from fastapi import WebSocket, WebSocketDisconnect

class ConnectionManager:
    def __init__(self):
        self.rooms: dict[str, list[WebSocket]] = {}

    async def connect(self, room: str, ws: WebSocket):
        await ws.accept()
        self.rooms.setdefault(room, []).append(ws)

    def disconnect(self, room: str, ws: WebSocket):
        self.rooms[room].remove(ws)

    async def broadcast(self, room: str, message: str):
        for ws in self.rooms.get(room, []):
            await ws.send_text(message)

manager = ConnectionManager()

@app.websocket("/ws/{room}")
async def chat(ws: WebSocket, room: str):
    await manager.connect(room, ws)
    try:
        while True:
            text = await ws.receive_text()
            await manager.broadcast(room, text)
    except WebSocketDisconnect:
        manager.disconnect(room, ws)
```
Authenticate with a token in the query string or first message. To scale across servers, share messages through Redis pub/sub.

### Custom OpenAPI documentation
```python
@app.get(
    "/appointments/{id}",
    response_model=AppointmentOut,
    summary="Get an appointment",
    description="Returns one appointment if it belongs to the current user.",
    tags=["appointments"],
    responses={404: {"description": "Appointment not found"}},
)
```
```python
class UserCreate(BaseModel):
    model_config = ConfigDict(json_schema_extra={"example": {"name": "Siva", "email": "siva@example.com", "password": "secret123"}})
```

### Validation error customization
```python
from fastapi.exceptions import RequestValidationError

@app.exception_handler(RequestValidationError)
async def validation_handler(request: Request, exc: RequestValidationError):
    errors = [{"field": ".".join(str(p) for p in e["loc"][1:]), "message": e["msg"]} for e in exc.errors()]
    return JSONResponse(status_code=422, content={"success": False, "errors": errors})
```

### Static files, GZip, trusted hosts
```python
from fastapi.staticfiles import StaticFiles
from fastapi.middleware.gzip import GZipMiddleware

app.mount("/static", StaticFiles(directory="static"), name="static")
app.add_middleware(GZipMiddleware, minimum_size=1000)
```

### Logging
```python
import logging
logging.basicConfig(level=logging.INFO, format="%(asctime)s %(levelname)s %(name)s %(message)s")
logger = logging.getLogger(__name__)

logger.info("Document %s processing started", doc_id)
logger.exception("Extraction failed")        # logs the stack trace inside an except block
```
Never log passwords, tokens or full document contents.

### Health checks
```python
@app.get("/health")
def health(db: Session = Depends(get_db)):
    db.execute(text("SELECT 1"))
    return {"status": "ok"}
```

### Server-Sent Events (one-way streaming to the browser)
```python
@app.get("/events")
async def events():
    async def gen():
        for i in range(5):
            yield f"data: update {i}\n\n"
            await asyncio.sleep(1)
    return StreamingResponse(gen(), media_type="text/event-stream")
```

---

## 12. Testing, deployment and performance

### Testing with pytest and TestClient
```python
# tests/conftest.py
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.pool import StaticPool

from app.main import app
from app.db.session import Base, get_db

engine = create_engine("sqlite://", connect_args={"check_same_thread": False}, poolclass=StaticPool)
TestingSession = sessionmaker(bind=engine, autoflush=False, expire_on_commit=False)

@pytest.fixture()
def client():
    Base.metadata.create_all(engine)

    def override_get_db():
        db = TestingSession()
        try:
            yield db
        finally:
            db.close()

    app.dependency_overrides[get_db] = override_get_db       # replace the real database
    with TestClient(app) as c:
        yield c
    app.dependency_overrides.clear()
    Base.metadata.drop_all(engine)
```
```python
# tests/test_auth.py
USER = {"name": "Siva", "email": "siva@test.com", "password": "secret123"}

def test_register(client):
    r = client.post("/api/v1/auth/register", json=USER)
    assert r.status_code == 201
    assert "password" not in r.json()

def test_duplicate_email(client):
    client.post("/api/v1/auth/register", json=USER)
    r = client.post("/api/v1/auth/register", json=USER)
    assert r.status_code == 409

def test_protected_route(client):
    assert client.get("/api/v1/users/me").status_code == 401
    client.post("/api/v1/auth/register", json=USER)
    token = client.post("/api/v1/auth/login", data={"username": USER["email"], "password": USER["password"]}).json()["access_token"]
    r = client.get("/api/v1/users/me", headers={"Authorization": f"Bearer {token}"})
    assert r.status_code == 200
```
**Mock the LLM so tests are fast and free:**
```python
def test_extraction(monkeypatch):
    fake = '{"invoice_number": "INV-1", "vendor_name": "ABC", "total": 118.0, "subtotal": 100.0, "tax": 18.0}'
    monkeypatch.setattr("app.services.extraction.call_llm", lambda prompt: fake)
    invoice = extract_invoice("some text")
    assert invoice.total == 118.0
```
**Testing ideas:** success path, validation errors (422), unauthorized (401), forbidden (403), not found (404), duplicates (409), and the failure modes of external services. Run with `pytest -q`, and measure coverage with `pytest --cov`.

### Running in production
- **Development:** `uvicorn app.main:app --reload`.
- **Production:** several worker processes, either `uvicorn app.main:app --workers 4` or Gunicorn managing Uvicorn workers: `gunicorn -k uvicorn.workers.UvicornWorker -w 4 app.main:app -b 0.0.0.0:8000`.
- Put **Nginx** or an **ALB** in front for TLS, buffering, and load balancing. Behind a proxy run Uvicorn with `--proxy-headers` so the client IP and scheme are correct.
- Install `uvicorn[standard]` for faster uvloop and httptools.

### Dockerfile
```dockerfile
FROM python:3.12-slim
ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
```
Copy `requirements.txt` first so Docker caches the dependency layer. Add a `.dockerignore` (`.venv`, `.git`, `.env`, `__pycache__`). For OCR images, install system packages such as `tesseract-ocr` and `poppler-utils` with `apt-get`.

### docker-compose for local development
```yaml
services:
  api:
    build: .
    ports: ["8000:8000"]
    env_file: .env
    depends_on: [db]
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: appdb
    volumes: ["pgdata:/var/lib/postgresql/data"]
volumes:
  pgdata:
```
The API connects with `postgresql+psycopg2://app:secret@db:5432/appdb` (service name `db` as host).

### Nginx in front of Uvicorn
```nginx
server {
  listen 80;
  server_name api.example.com;
  location / {
    proxy_pass http://127.0.0.1:8000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;        # for WebSockets
    proxy_set_header Connection "upgrade";
  }
}
```

### Performance checklist
1. Do not block the event loop (async libraries, or plain `def`, or thread pool).
2. Database indexes, pagination, eager loading, connection pooling.
3. Cache hot reads in Redis.
4. Move slow work to queues (Celery, ARQ).
5. Reuse HTTP clients, set timeouts on every external call.
6. Enable GZip, return only needed fields (`response_model`).
7. Profile before optimizing (`cProfile`, `py-spy`, request timing middleware).
8. Scale horizontally: several workers and instances behind a load balancer, with stateless services.

### Deployment questions
- **Uvicorn vs Gunicorn?** Uvicorn is the ASGI server. Gunicorn is a process manager that can run several Uvicorn workers and restart them if they crash. Newer Uvicorn can also manage workers itself.
- **How do you pass secrets?** Environment variables from a secret manager, never in the image or Git.
- **How do you do zero-downtime deploys?** Several instances behind a load balancer, rolling updates, a health check endpoint, and graceful shutdown.
- **How do you handle database migrations on deploy?** Run `alembic upgrade head` as a separate step before starting the new version, with backwards-compatible changes.
- **Where did you deploy?** Say what you really did: for example Docker on AWS EC2 with an ALB, with Terraform for infrastructure and Jenkins for CI/CD (only if true).

---

## 13. AI and LLM document extraction pipeline

This is the most important project on your resume for this role, so know it deeply.

### Architecture
```
Client (React)
   |  POST /documents (upload)
FastAPI  --> save file (S3 or disk) --> create Document row (status = queued)
   |  returns 202 + document id
Background worker
   -> extract text (digital PDF text, or OCR for scans)
   -> build prompt with JSON schema
   -> call LLM (OpenAI / Claude / Gemini, with retries and fallback)
   -> parse JSON -> validate with Pydantic -> business checks (totals)
   -> save result JSON, status = done | needs_review | failed
Client polls GET /documents/{id}, reviews and corrects the data, then approves it
```

### Schemas
```python
from datetime import date
from pydantic import BaseModel, Field

class InvoiceLine(BaseModel):
    description: str
    quantity: float = Field(ge=0)
    unit_price: float = Field(ge=0)
    amount: float

class Invoice(BaseModel):
    invoice_number: str
    invoice_date: date | None = None
    vendor_name: str
    currency: str = "INR"
    subtotal: float | None = None
    tax: float | None = None
    total: float
    line_items: list[InvoiceLine] = []
```

### Text extraction (digital PDF first, OCR as fallback)
```python
import pdfplumber
from pdf2image import convert_from_path
import pytesseract

def text_from_digital_pdf(path: str) -> str:
    with pdfplumber.open(path) as pdf:
        return "\n".join((page.extract_text() or "") for page in pdf.pages)

def text_from_scan(path: str) -> str:
    pages = convert_from_path(path, dpi=300)                      # higher DPI gives better OCR
    return "\n".join(pytesseract.image_to_string(img) for img in pages)

def extract_text(path: str) -> str:
    text = text_from_digital_pdf(path)
    if len(text.strip()) < 50:                                     # looks like a scanned document
        text = text_from_scan(path)
    return text
```
Use the tools you actually used on the project. The idea matters more than the library names.

### Prompt and LLM call
```python
import json, re

def build_prompt(text: str) -> str:
    schema = json.dumps(Invoice.model_json_schema())
    return (
        "You are a data extraction system. Extract the invoice fields from the text below.\n"
        "Return ONLY valid JSON that matches this JSON schema. No explanations, no markdown.\n"
        "Use null for any field that is not present in the text. Do not guess or invent values.\n"
        f"Schema: {schema}\n\n"
        f'Text:\n"""\n{text}\n"""'
    )

def call_llm(prompt: str) -> str:
    """Wrap your provider SDK here (OpenAI, Claude or Gemini).
    Use a low temperature for extraction and set a timeout."""
    raise NotImplementedError

def strip_code_fences(raw: str) -> str:
    return re.sub(r"^```(?:json)?\s*|\s*```$", "", raw.strip())
```

### Extract, validate, retry
```python
from pydantic import ValidationError

class ExtractionError(Exception):
    pass

def check_totals(inv: Invoice, tolerance: float = 1.0) -> None:
    if inv.line_items and inv.subtotal is not None:
        line_sum = sum(item.amount for item in inv.line_items)
        if abs(line_sum - inv.subtotal) > tolerance:
            raise ValueError(f"Line items sum {line_sum} does not match subtotal {inv.subtotal}")
    if inv.subtotal is not None and inv.tax is not None:
        if abs(inv.subtotal + inv.tax - inv.total) > tolerance:
            raise ValueError("Subtotal + tax does not match total")

def extract_invoice(text: str, max_retries: int = 2) -> Invoice:
    prompt = build_prompt(text)
    last_error = None
    for attempt in range(max_retries + 1):
        full_prompt = prompt if last_error is None else (
            prompt + f"\n\nYour previous answer was invalid: {last_error}\nReturn corrected JSON only."
        )
        raw = call_llm(full_prompt)
        try:
            invoice = Invoice.model_validate(json.loads(strip_code_fences(raw)))
            check_totals(invoice)
            return invoice
        except (json.JSONDecodeError, ValidationError, ValueError) as e:
            last_error = str(e)[:500]
    raise ExtractionError(f"Could not extract valid data: {last_error}")
```
**Explain:** the LLM is a probabilistic component. The schema in the prompt guides it, Pydantic enforces structure, business checks catch wrong numbers, retries feed the error back, and a failure becomes `needs_review` for a human instead of silently saving wrong data.

### Provider fallback
```python
PROVIDERS = [call_openai, call_claude, call_gemini]      # your wrapper functions

def call_llm(prompt: str) -> str:
    last = None
    for provider in PROVIDERS:
        try:
            return provider(prompt)
        except Exception as e:                            # timeout, rate limit, server error
            logger.warning("Provider %s failed: %s", provider.__name__, e)
            last = e
    raise ExtractionError("All providers failed") from last
```
Add retries with exponential backoff on rate limit (`429`) errors before switching provider.

### Upload endpoint and background processing
```python
from sqlalchemy.dialects.postgresql import JSONB

class Document(Base):
    __tablename__ = "documents"
    id: Mapped[int] = mapped_column(primary_key=True)
    owner_id: Mapped[int] = mapped_column(ForeignKey("users.id"), index=True)
    filename: Mapped[str] = mapped_column(String(255))
    path: Mapped[str] = mapped_column(String(500))
    status: Mapped[str] = mapped_column(String(20), default="queued", index=True)
    result: Mapped[dict | None] = mapped_column(JSONB)
    error: Mapped[str | None] = mapped_column(String(1000))
```
```python
import uuid, shutil
from pathlib import Path

UPLOAD_DIR = Path("uploads")
MAX_SIZE = 10 * 1024 * 1024

@router.post("/documents", status_code=202)
def upload(file: UploadFile, background: BackgroundTasks, db: DbSession, user: CurrentUser):
    if file.content_type != "application/pdf":
        raise HTTPException(415, "Only PDF files are allowed")
    path = UPLOAD_DIR / f"{uuid.uuid4()}.pdf"                    # our own filename
    with path.open("wb") as out:
        shutil.copyfileobj(file.file, out)
    if path.stat().st_size > MAX_SIZE:
        path.unlink()
        raise HTTPException(413, "File too large")

    doc = Document(owner_id=user.id, filename=file.filename, path=str(path))
    db.add(doc); db.commit(); db.refresh(doc)
    background.add_task(process_document, doc.id)               # or Celery: process_document.delay(doc.id)
    return {"id": doc.id, "status": doc.status}

@router.get("/documents/{doc_id}")
def get_document(doc_id: int, db: DbSession, user: CurrentUser):
    doc = db.get(Document, doc_id)
    if not doc or doc.owner_id != user.id:                       # ownership check
        raise HTTPException(404, "Document not found")
    return {"id": doc.id, "status": doc.status, "result": doc.result, "error": doc.error}

def process_document(doc_id: int):
    with SessionLocal() as db:                                   # own session, not the request's
        doc = db.get(Document, doc_id)
        doc.status = "processing"; db.commit()
        try:
            text = extract_text(doc.path)
            invoice = extract_invoice(text)
            doc.result = invoice.model_dump(mode="json")
            doc.status = "done"
        except ExtractionError as e:
            doc.status, doc.error = "needs_review", str(e)
        except Exception as e:
            logger.exception("Processing failed for %s", doc_id)
            doc.status, doc.error = "failed", str(e)[:1000]
        db.commit()
```

### Questions to expect on this pipeline
- **Structured output options?** Many providers offer JSON mode or schema-constrained output (function or tool calling). It improves reliability, but you still validate with Pydantic.
- **Why low temperature?** Extraction should be deterministic, not creative.
- **Prompt injection?** A document can contain text like "ignore previous instructions". Treat document text as data, keep instructions separate from the text, validate outputs strictly, and never let LLM output trigger actions directly.
- **How do you evaluate accuracy?** Build a small labeled set of real documents, compare field by field (exact match for numbers and ids), track accuracy per field and per document type, and re-run it when you change the prompt or the model.
- **Token limits and cost?** Send only the relevant text, split long documents by page, use smaller models for simple documents, cache by file hash so the same document is not processed twice, and cap retries.
- **Idempotency?** Hash the file; if the same hash exists for the same client, return the existing result.
- **How do you handle multiple document types?** First classify the document (invoice, purchase order, receipt) with a cheap call, then use the matching schema and prompt.
- **How do you handle tables?** Layout-aware OCR or table extraction (pdfplumber tables), or send page images to a vision-capable model. Say what you tried.
- **Where do you store the raw text and the model output?** For audit and debugging, store OCR text and the raw LLM response (carefully, if they contain sensitive data).
- **Observability?** Log document id, duration of each step, provider used, token usage, retries and final status. Track failure rate and `needs_review` rate.

---

## 14. Scenario based questions

For each: **name the problem, give the fix, mention a trade-off.**

**S1. An endpoint is slow. How do you find the cause?**
Add timing logs or middleware to find the slow endpoint, then check database queries (`EXPLAIN`, N+1, missing indexes), external calls (timeouts, sequential awaits that could use `gather`), blocking code in `async def`, and payload size. Fix, then measure again.

**S2. All requests become slow when one user uploads a big file.**
Probably blocking work inside `async def`, or a CPU heavy task on the event loop. Use `def` endpoints (thread pool), `run_in_threadpool`, or move the work to a queue and worker. Stream uploads to disk instead of reading them into memory.

**S3. The LLM returns invalid JSON sometimes.**
Strip code fences, parse with `json.loads`, validate with Pydantic, retry with the error message, use JSON or schema mode if available, and mark `needs_review` after the retry limit.

**S4. A document takes 2 minutes to process.**
Return `202` with a document id, process in a background worker with status updates, and let the client poll (or use WebSocket or SSE for live status). Add retries, timeouts and a way to cancel or reprocess.

**S5. The same document is uploaded twice.**
Compute a hash of the file, keep a unique constraint on `(owner_id, file_hash)`, and return the existing record.

**S6. The LLM provider is down or rate limiting you.**
Retry with exponential backoff, fall back to another provider, queue documents and process later, and alert when the failure rate is high.

**S7. How do you secure an API used by the React frontend?**
HTTPS, JWT with short expiry, password hashing, CORS limited to your frontend origin, input validation with Pydantic, role and ownership checks, rate limiting, secrets in environment variables.

**S8. Two users book the same slot at the same time.**
A unique constraint on `(doctor_id, slot_start)`, catch `IntegrityError`, return `409`. Do not rely on "check then insert".

**S9. You must change a database column in production.**
Create an Alembic migration, make it backwards compatible (add the new column first, deploy code that writes both, backfill, then remove the old column later), and run it before the new code starts.

**S10. How do you handle a dependency that fails (for example Redis is down)?**
Set timeouts, catch errors, degrade gracefully (skip the cache and read from the database), log and alert, and use health checks so the load balancer can stop sending traffic to a broken instance.

**S11. How do you keep the API contract stable for the frontend?**
Use Pydantic response models, version the API (`/api/v1`), do not remove or rename fields in place, share the OpenAPI schema, and add tests for response shapes.

**S12. How would you handle large result sets (100,000 rows)?**
Pagination (keyset for very large tables), filters, streaming responses or export jobs for reports, and indexes on sort columns.

**S13. Memory keeps growing in your service.**
Check for global lists or caches that grow, large files read fully into memory, un-closed sessions or clients, and leaked tasks. Use streaming and bounded caches (`lru_cache(maxsize=...)`), and profile with `tracemalloc`.

**S14. How do you design role-based access for User, Doctor and Admin?**
Role in the token (and verified against the database), `require_roles(...)` dependencies, and ownership checks inside services. The frontend hides UI, but the backend always enforces rules.

**S15. How do you make background jobs reliable?**
Use a real queue (Celery or ARQ with Redis), retries with backoff, idempotent tasks (safe to run twice), status tracking in the database, dead-letter handling and monitoring.

---
## 15. Live coding: first API and CRUD

**Practise:** build this from an empty folder in 10 to 15 minutes. This is the most likely live coding task.

### Setup
```bash
mkdir api && cd api
python -m venv .venv
source .venv/bin/activate            # Windows: .venv\Scripts\activate
pip install "fastapi[standard]"      # includes uvicorn
# or: pip install fastapi uvicorn
```
Run: `uvicorn main:app --reload`, then open `http://127.0.0.1:8000/docs`.

### 15.1 In-memory CRUD (no database)
```python
# main.py
from fastapi import FastAPI, HTTPException, Query, status
from pydantic import BaseModel, Field

app = FastAPI(title="Items API")


class ItemCreate(BaseModel):
    name: str = Field(min_length=1, max_length=50)
    price: float = Field(gt=0)
    in_stock: bool = True


class ItemUpdate(BaseModel):
    name: str | None = Field(default=None, min_length=1, max_length=50)
    price: float | None = Field(default=None, gt=0)
    in_stock: bool | None = None


class Item(ItemCreate):
    id: int


db: dict[int, Item] = {}
next_id = 1


@app.get("/health")
def health():
    return {"status": "ok"}


@app.post("/items", response_model=Item, status_code=status.HTTP_201_CREATED)
def create_item(payload: ItemCreate):
    global next_id
    item = Item(id=next_id, **payload.model_dump())
    db[next_id] = item
    next_id += 1
    return item


@app.get("/items", response_model=list[Item])
def list_items(
    q: str | None = None,
    in_stock: bool | None = None,
    page: int = Query(1, ge=1),
    limit: int = Query(10, ge=1, le=100),
):
    items = list(db.values())
    if q:
        items = [i for i in items if q.lower() in i.name.lower()]
    if in_stock is not None:
        items = [i for i in items if i.in_stock == in_stock]
    start = (page - 1) * limit
    return items[start:start + limit]


@app.get("/items/{item_id}", response_model=Item)
def get_item(item_id: int):
    item = db.get(item_id)
    if not item:
        raise HTTPException(status_code=404, detail="Item not found")
    return item


@app.put("/items/{item_id}", response_model=Item)
def replace_item(item_id: int, payload: ItemCreate):
    if item_id not in db:
        raise HTTPException(status_code=404, detail="Item not found")
    db[item_id] = Item(id=item_id, **payload.model_dump())
    return db[item_id]


@app.patch("/items/{item_id}", response_model=Item)
def update_item(item_id: int, payload: ItemUpdate):
    item = db.get(item_id)
    if not item:
        raise HTTPException(status_code=404, detail="Item not found")
    updated = item.model_copy(update=payload.model_dump(exclude_unset=True))
    db[item_id] = updated
    return updated


@app.delete("/items/{item_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_item(item_id: int):
    if item_id not in db:
        raise HTTPException(status_code=404, detail="Item not found")
    del db[item_id]
```
**Explain while coding:** separate schemas for create, update and output, `Field` validation, correct status codes (`201`, `204`, `404`), `exclude_unset=True` for PATCH, query params with defaults and limits.

### 15.2 A Todo API with an Enum and a router
```python
# app/routers/todos.py
from enum import Enum
from fastapi import APIRouter, HTTPException
from pydantic import BaseModel

router = APIRouter(prefix="/todos", tags=["todos"])


class Priority(str, Enum):
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"


class TodoIn(BaseModel):
    title: str
    priority: Priority = Priority.MEDIUM


class Todo(TodoIn):
    id: int
    done: bool = False


todos: list[Todo] = []


@router.post("", response_model=Todo, status_code=201)
def add(payload: TodoIn):
    todo = Todo(id=len(todos) + 1, **payload.model_dump())
    todos.append(todo)
    return todo


@router.get("", response_model=list[Todo])
def list_todos(priority: Priority | None = None, done: bool | None = None):
    result = todos
    if priority:
        result = [t for t in result if t.priority == priority]
    if done is not None:
        result = [t for t in result if t.done == done]
    return result


@router.post("/{todo_id}/complete", response_model=Todo)
def complete(todo_id: int):
    for t in todos:
        if t.id == todo_id:
            t.done = True
            return t
    raise HTTPException(404, "Todo not found")
```
```python
# main.py
from fastapi import FastAPI
from app.routers import todos

app = FastAPI()
app.include_router(todos.router, prefix="/api/v1")
```

### 15.3 What interviewers watch for
- You run the app and test in `/docs` as you build.
- Validation, not found, duplicate and bad input cases are handled.
- Names are clear, routes use nouns and plural, and status codes are correct.
- You mention what you would add next: database, authentication, pagination, tests.

---

## 16. Live coding: database and authentication

A complete small project. Practise until you can type it in about 25 minutes.

### Install and layout
```bash
pip install fastapi uvicorn sqlalchemy psycopg2-binary pydantic-settings bcrypt pyjwt python-multipart email-validator
```
```
app/
  main.py
  core/config.py
  core/security.py
  db/session.py
  models.py
  schemas.py
  deps.py
  routers/auth.py
  routers/users.py
```
`.env`
```
DATABASE_URL=postgresql+psycopg2://app:secret@localhost:5432/appdb
SECRET_KEY=change-me-to-a-long-random-string
```
For a quick demo, use SQLite: `DATABASE_URL=sqlite:///./app.db` (add `connect_args={"check_same_thread": False}` to `create_engine`).

### core/config.py
```python
from functools import lru_cache
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env")
    database_url: str
    secret_key: str
    access_token_minutes: int = 15


@lru_cache
def get_settings() -> Settings:
    return Settings()
```

### db/session.py
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, DeclarativeBase
from app.core.config import get_settings

engine = create_engine(get_settings().database_url, pool_pre_ping=True)
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

### models.py
```python
from datetime import datetime
from sqlalchemy import String, DateTime, func
from sqlalchemy.orm import Mapped, mapped_column
from app.db.session import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    email: Mapped[str] = mapped_column(String(255), unique=True, index=True)
    password_hash: Mapped[str] = mapped_column(String(255))
    role: Mapped[str] = mapped_column(String(20), default="user")
    is_active: Mapped[bool] = mapped_column(default=True)
    created_at: Mapped[datetime] = mapped_column(DateTime(timezone=True), server_default=func.now())
```

### schemas.py
```python
from pydantic import BaseModel, ConfigDict, EmailStr, Field


class UserCreate(BaseModel):
    name: str = Field(min_length=2, max_length=100)
    email: EmailStr
    password: str = Field(min_length=8)            # no role field, the server decides the role


class UserOut(BaseModel):
    model_config = ConfigDict(from_attributes=True)
    id: int
    name: str
    email: EmailStr
    role: str


class Token(BaseModel):
    access_token: str
    token_type: str = "bearer"
```

### core/security.py
```python
from datetime import datetime, timedelta, timezone
import bcrypt
import jwt
from app.core.config import get_settings

settings = get_settings()


def hash_password(password: str) -> str:
    return bcrypt.hashpw(password.encode(), bcrypt.gensalt()).decode()


def verify_password(password: str, hashed: str) -> bool:
    return bcrypt.checkpw(password.encode(), hashed.encode())


def create_access_token(user_id: int, role: str) -> str:
    expire = datetime.now(timezone.utc) + timedelta(minutes=settings.access_token_minutes)
    return jwt.encode({"sub": str(user_id), "role": role, "exp": expire}, settings.secret_key, algorithm="HS256")


def decode_token(token: str) -> dict:
    return jwt.decode(token, settings.secret_key, algorithms=["HS256"])
```

### deps.py
```python
from typing import Annotated
import jwt
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from sqlalchemy.orm import Session

from app.db.session import get_db
from app.core.security import decode_token
from app.models import User

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/api/v1/auth/login")
DbSession = Annotated[Session, Depends(get_db)]


def get_current_user(token: Annotated[str, Depends(oauth2_scheme)], db: DbSession) -> User:
    error = HTTPException(status.HTTP_401_UNAUTHORIZED, "Could not validate credentials",
                          headers={"WWW-Authenticate": "Bearer"})
    try:
        payload = decode_token(token)
    except jwt.InvalidTokenError:                  # also covers expired tokens
        raise error
    user = db.get(User, int(payload["sub"]))
    if not user or not user.is_active:
        raise error
    return user


CurrentUser = Annotated[User, Depends(get_current_user)]


def require_roles(*roles: str):
    def checker(user: CurrentUser) -> User:
        if user.role not in roles:
            raise HTTPException(status.HTTP_403_FORBIDDEN, "Not enough permissions")
        return user
    return checker
```

### routers/auth.py
```python
from typing import Annotated
from fastapi import APIRouter, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordRequestForm
from sqlalchemy import select
from sqlalchemy.exc import IntegrityError

from app.deps import DbSession
from app.models import User
from app.schemas import UserCreate, UserOut, Token
from app.core.security import hash_password, verify_password, create_access_token

router = APIRouter(prefix="/auth", tags=["auth"])


@router.post("/register", response_model=UserOut, status_code=status.HTTP_201_CREATED)
def register(payload: UserCreate, db: DbSession):
    user = User(name=payload.name, email=payload.email.lower(), password_hash=hash_password(payload.password))
    db.add(user)
    try:
        db.commit()
    except IntegrityError:
        db.rollback()
        raise HTTPException(status.HTTP_409_CONFLICT, "Email already registered")
    db.refresh(user)
    return user


@router.post("/login", response_model=Token)
def login(form: Annotated[OAuth2PasswordRequestForm, Depends()], db: DbSession):
    user = db.scalars(select(User).where(User.email == form.username.lower())).first()
    if not user or not verify_password(form.password, user.password_hash):
        raise HTTPException(status.HTTP_401_UNAUTHORIZED, "Invalid email or password",
                            headers={"WWW-Authenticate": "Bearer"})
    return Token(access_token=create_access_token(user.id, user.role))
```
The login form field is called `username` (OAuth2 standard), so the frontend sends the email in it as form data, not JSON.

### routers/users.py
```python
from fastapi import APIRouter, Depends, HTTPException, Query
from sqlalchemy import select, func

from app.deps import DbSession, CurrentUser, require_roles
from app.models import User
from app.schemas import UserOut

router = APIRouter(prefix="/users", tags=["users"])


@router.get("/me", response_model=UserOut)
def me(user: CurrentUser):
    return user


@router.get("", response_model=dict, dependencies=[Depends(require_roles("admin"))])
def list_users(db: DbSession, page: int = Query(1, ge=1), limit: int = Query(10, ge=1, le=100)):
    rows = db.scalars(select(User).order_by(User.id).offset((page - 1) * limit).limit(limit)).all()
    total = db.scalar(select(func.count()).select_from(User))
    return {"items": [UserOut.model_validate(u) for u in rows], "page": page, "limit": limit, "total": total}


@router.delete("/{user_id}", status_code=204, dependencies=[Depends(require_roles("admin"))])
def delete_user(user_id: int, db: DbSession):
    user = db.get(User, user_id)
    if not user:
        raise HTTPException(404, "User not found")
    db.delete(user)
    db.commit()
```

### main.py
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

from app.db.session import Base, engine
from app.routers import auth, users

Base.metadata.create_all(bind=engine)          # demo only, use Alembic migrations in real projects

app = FastAPI(title="Clinic API", version="1.0.0")

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

app.include_router(auth.router, prefix="/api/v1")
app.include_router(users.router, prefix="/api/v1")


@app.get("/health")
def health():
    return {"status": "ok"}
```
**Test in `/docs`:** register, click **Authorize** with the email and password, call `/users/me`, then try the admin routes (they return `403` for a normal user).

### Ownership check example
```python
@router.get("/appointments/{appt_id}", response_model=AppointmentOut)
def get_appointment(appt_id: int, db: DbSession, user: CurrentUser):
    appt = db.get(Appointment, appt_id)
    if not appt:
        raise HTTPException(404, "Appointment not found")
    if appt.patient_id != user.id and user.role != "admin":
        raise HTTPException(403, "Not allowed")
    return appt
```
Role checks are not enough. Always check that the user owns the resource.

### Booking with a double booking guard
```python
@router.post("/appointments", response_model=AppointmentOut, status_code=201)
def book(payload: AppointmentCreate, db: DbSession, user: CurrentUser):
    appt = Appointment(patient_id=user.id, doctor_id=payload.doctor_id, slot_start=payload.slot_start)
    db.add(appt)
    try:
        db.commit()                                 # unique constraint on (doctor_id, slot_start)
    except IntegrityError:
        db.rollback()
        raise HTTPException(409, "This slot is already booked")
    db.refresh(appt)
    return appt
```

---

## 17. Live coding: more endpoint tasks

### CSV upload and parsing
```python
import csv, io
from fastapi import UploadFile, File, HTTPException
from pydantic import ValidationError

@app.post("/import/users")
async def import_users(file: UploadFile = File(...)):
    if not file.filename.endswith(".csv"):
        raise HTTPException(400, "Upload a .csv file")
    text = (await file.read()).decode("utf-8")
    reader = csv.DictReader(io.StringIO(text))
    created, errors = [], []
    for line_no, row in enumerate(reader, start=2):             # line 1 is the header
        try:
            created.append(UserCreate(**row))                   # validate every row with Pydantic
        except ValidationError as e:
            errors.append({"line": line_no, "errors": e.errors()})
    return {"valid": len(created), "invalid": len(errors), "errors": errors}
```

### CSV export (streaming download)
```python
from fastapi.responses import StreamingResponse

@app.get("/export/users")
def export_users(db: DbSession):
    def rows():
        buf = io.StringIO()
        writer = csv.writer(buf)
        writer.writerow(["id", "name", "email"])
        yield buf.getvalue(); buf.seek(0); buf.truncate(0)
        for u in db.scalars(select(User).execution_options(yield_per=100)):
            writer.writerow([u.id, u.name, u.email])
            yield buf.getvalue(); buf.seek(0); buf.truncate(0)
    return StreamingResponse(rows(), media_type="text/csv",
                             headers={"Content-Disposition": "attachment; filename=users.csv"})
```

### Simple in-memory rate limiter dependency
```python
import time
from collections import defaultdict, deque
from fastapi import Request

hits: dict[str, deque] = defaultdict(deque)

def rate_limit(max_requests: int = 10, window: int = 60):
    def dependency(request: Request):
        key = request.client.host
        now = time.time()
        q = hits[key]
        while q and now - q[0] > window:       # drop old timestamps
            q.popleft()
        if len(q) >= max_requests:
            raise HTTPException(429, "Too many requests", headers={"Retry-After": str(window)})
        q.append(now)
    return dependency

@app.post("/auth/login", dependencies=[Depends(rate_limit(5, 60))])
def login(): ...
```
Say: this works for one process only. With several workers, store counters in Redis (`INCR` plus `EXPIRE`).

### Request logging middleware with a request id
```python
import uuid, time, logging
logger = logging.getLogger("api")

@app.middleware("http")
async def log_requests(request: Request, call_next):
    request_id = request.headers.get("X-Request-ID", str(uuid.uuid4()))
    start = time.perf_counter()
    response = await call_next(request)
    duration = (time.perf_counter() - start) * 1000
    logger.info("%s %s %s %.1fms id=%s", request.method, request.url.path, response.status_code, duration, request_id)
    response.headers["X-Request-ID"] = request_id
    return response
```

### Global exception handler for unexpected errors
```python
@app.exception_handler(Exception)
async def unhandled(request: Request, exc: Exception):
    logger.exception("Unhandled error on %s", request.url.path)
    return JSONResponse(status_code=500, content={"detail": "Internal server error"})
```

### Simple response cache decorator for async functions
```python
import functools, time

def ttl_cache(seconds: int):
    def decorator(func):
        cache: dict = {}
        @functools.wraps(func)
        async def wrapper(*args, **kwargs):
            key = (args, tuple(sorted(kwargs.items())))
            hit = cache.get(key)
            if hit and time.time() - hit[0] < seconds:
                return hit[1]
            result = await func(*args, **kwargs)
            cache[key] = (time.time(), result)
            return result
        return wrapper
    return decorator
```

### Bulk create with a transaction
```python
@app.post("/items/bulk", status_code=201)
def bulk_create(payload: list[ItemCreate], db: DbSession):
    items = [ItemModel(**p.model_dump()) for p in payload]
    db.add_all(items)
    db.commit()                                  # all or nothing
    return {"created": len(items)}
```

### Soft delete
Add `is_deleted: Mapped[bool] = mapped_column(default=False)`. "Delete" sets it to `True`. Queries filter `where(Model.is_deleted.is_(False))`. Keeps history for audits.

### Idempotent create (safe to retry)
Accept an `Idempotency-Key` header. Store it with the result (a unique column or a Redis key with `SET NX`). If the key already exists, return the stored response instead of creating a second record.

---

## 18. Python coding problems

For each problem: **say the idea first, write pseudo code, then code, then test with one normal case and one edge case.**

### Strings
```python
# Reverse a string and reverse words
def reverse(s): return s[::-1]
def reverse_words(s): return " ".join(s.split()[::-1])

# Palindrome (ignore case and non-alphanumerics)
def is_palindrome(s):
    t = "".join(c.lower() for c in s if c.isalnum())
    return t == t[::-1]

# Character frequency
from collections import Counter
def frequency(s): return Counter(s)

# First non-repeating character
def first_unique(s):
    count = Counter(s)
    for ch in s:
        if count[ch] == 1:
            return ch
    return None

# Anagram check and grouping
def is_anagram(a, b): return sorted(a) == sorted(b)

from collections import defaultdict
def group_anagrams(words):
    groups = defaultdict(list)
    for w in words:
        groups["".join(sorted(w))].append(w)
    return list(groups.values())

# String compression: "aaabbc" -> "a3b2c1"
def compress(s):
    if not s: return ""
    out, count = [], 1
    for prev, cur in zip(s, s[1:]):
        if cur == prev: count += 1
        else:
            out.append(f"{prev}{count}"); count = 1
    out.append(f"{s[-1]}{count}")
    return "".join(out)

# Longest substring without repeating characters (sliding window)
def longest_unique(s):
    seen, start, best = {}, 0, 0
    for i, ch in enumerate(s):
        if ch in seen and seen[ch] >= start:
            start = seen[ch] + 1
        seen[ch] = i
        best = max(best, i - start + 1)
    return best

# Valid parentheses (stack)
def is_valid(s):
    pairs = {")": "(", "]": "[", "}": "{"}
    stack = []
    for ch in s:
        if ch in pairs:
            if not stack or stack.pop() != pairs[ch]:
                return False
        else:
            stack.append(ch)
    return not stack
```

### Lists and arrays
```python
# Two sum (O(n) with a dict)
def two_sum(nums, target):
    seen = {}
    for i, n in enumerate(nums):
        if target - n in seen:
            return [seen[target - n], i]
        seen[n] = i
    return []

# Remove duplicates keeping order
def unique(items): return list(dict.fromkeys(items))

# Flatten a nested list
def flatten(items):
    for x in items:
        if isinstance(x, list):
            yield from flatten(x)
        else:
            yield x
# list(flatten([1, [2, [3, 4]], 5])) -> [1, 2, 3, 4, 5]

# Largest and second largest
def top_two(nums):
    first = second = float("-inf")
    for n in nums:
        if n > first: first, second = n, first
        elif first > n > second: second = n
    return first, second

# Rotate list right by k
def rotate(nums, k):
    if not nums: return nums
    k %= len(nums)
    return nums[-k:] + nums[:-k] if k else nums[:]

# Move zeros to the end (in place)
def move_zeros(nums):
    pos = 0
    for i, n in enumerate(nums):
        if n != 0:
            nums[pos], nums[i] = nums[i], nums[pos]
            pos += 1
    return nums

# Missing number in 0..n
def missing(nums):
    n = len(nums)
    return n * (n + 1) // 2 - sum(nums)

# Merge two sorted lists
def merge_sorted(a, b):
    i = j = 0; out = []
    while i < len(a) and j < len(b):
        if a[i] <= b[j]: out.append(a[i]); i += 1
        else: out.append(b[j]); j += 1
    return out + a[i:] + b[j:]

# Merge overlapping intervals
def merge_intervals(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = []
    for start, end in intervals:
        if merged and start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged

# Binary search (sorted list)
def binary_search(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target: return mid
        if nums[mid] < target: lo = mid + 1
        else: hi = mid - 1
    return -1

# Top k frequent items
def top_k(items, k): return [x for x, _ in Counter(items).most_common(k)]

# Matrix transpose
def transpose(m): return [list(row) for row in zip(*m)]
```

### Numbers
```python
def is_prime(n):
    if n < 2: return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0: return False
    return True

def sieve(n):                      # all primes up to n
    flags = [True] * (n + 1); flags[0:2] = [False, False]
    for i in range(2, int(n ** 0.5) + 1):
        if flags[i]:
            for j in range(i * i, n + 1, i): flags[j] = False
    return [i for i, ok in enumerate(flags) if ok]

def fib(n):                        # iterative, O(n) time, O(1) space
    a, b = 0, 1
    for _ in range(n): a, b = b, a + b
    return a

def gcd(a, b):
    while b: a, b = b, a % b
    return a

def fizzbuzz(n):
    for i in range(1, n + 1):
        print("FizzBuzz" if i % 15 == 0 else "Fizz" if i % 3 == 0 else "Buzz" if i % 5 == 0 else i)
```

### Data structures and classes
```python
# LRU cache with OrderedDict
from collections import OrderedDict
class LRUCache:
    def __init__(self, capacity):
        self.capacity, self.data = capacity, OrderedDict()
    def get(self, key):
        if key not in self.data: return -1
        self.data.move_to_end(key)
        return self.data[key]
    def put(self, key, value):
        self.data[key] = value
        self.data.move_to_end(key)
        if len(self.data) > self.capacity:
            self.data.popitem(last=False)         # remove the least recently used

# Stack and queue
class Stack:
    def __init__(self): self._items = []
    def push(self, x): self._items.append(x)
    def pop(self):
        if not self._items: raise IndexError("pop from empty stack")
        return self._items.pop()
    def peek(self): return self._items[-1] if self._items else None
    def __len__(self): return len(self._items)

from collections import deque
queue = deque()
queue.append(1); queue.popleft()                   # O(1) at both ends

# Priority queue with heapq
import heapq
heap = []
heapq.heappush(heap, (2, "task b")); heapq.heappush(heap, (1, "task a"))
heapq.heappop(heap)                                 # (1, "task a")

# Event emitter
class EventEmitter:
    def __init__(self): self.handlers = defaultdict(list)
    def on(self, name, fn): self.handlers[name].append(fn)
    def emit(self, name, *args): [fn(*args) for fn in self.handlers[name]]
```

### Practical data tasks (close to your project work)
```python
# Total amount per vendor
def totals_by_vendor(invoices):
    totals = defaultdict(float)
    for inv in invoices:
        totals[inv["vendor"]] += inv["total"]
    return dict(totals)

# Normalize an Indian phone number
import re
def normalize_phone(s):
    digits = re.sub(r"\D", "", s)
    return digits[-10:] if len(digits) >= 10 else None

# Parse and format dates from different formats
from datetime import datetime
def parse_date(text):
    for fmt in ("%d/%m/%Y", "%Y-%m-%d", "%d-%b-%Y"):
        try:
            return datetime.strptime(text, fmt).date()
        except ValueError:
            continue
    return None

# Safe access to nested JSON
def get_path(data, *keys, default=None):
    for k in keys:
        if not isinstance(data, dict) or k not in data:
            return default
        data = data[k]
    return data
# get_path(resp, "choices", "message", default={})

# Read a CSV and compute a column total
import csv
def csv_total(path, column):
    with open(path, newline="") as f:
        return sum(float(row[column]) for row in csv.DictReader(f))

# Word count of a big file (memory friendly)
def word_count(path):
    counts = Counter()
    with open(path) as f:
        for line in f:
            counts.update(line.lower().split())
    return counts

# Clean OCR-like text
def clean_text(text):
    text = re.sub(r"[ \t]+", " ", text)
    text = re.sub(r"\n{3,}", "\n\n", text)
    return text.strip()

# Run work on many items with a thread pool (I/O bound)
from concurrent.futures import ThreadPoolExecutor
with ThreadPoolExecutor(max_workers=5) as pool:
    results = list(pool.map(process_one, items))
```

### OOP design question template
"Design a parking lot / library / inventory system." Answer with: list the entities (classes), their data and behaviour, relationships (composition), then write 2 or 3 core methods and mention validation and errors.
```python
class Book:
    def __init__(self, isbn, title): self.isbn, self.title, self.available = isbn, title, True

class Member:
    def __init__(self, member_id, name): self.member_id, self.name, self.borrowed = member_id, name, []

class Library:
    def __init__(self): self.books, self.members = {}, {}
    def add_book(self, book): self.books[book.isbn] = book
    def borrow(self, member_id, isbn):
        member, book = self.members[member_id], self.books[isbn]
        if not book.available: raise ValueError("Book is already borrowed")
        if len(member.borrowed) >= 3: raise ValueError("Borrow limit reached")
        book.available = False
        member.borrowed.append(book)
```

---
## 19. Pseudo code guide

The interviewer wants to see your **thinking**, not exact syntax. Pseudo code is plain English steps that look like code. No imports, no exact function names, and no colons or brackets are needed.

**Format**
```
FUNCTION name(inputs)
    IF condition THEN
        do something
    ELSE
        do something else
    END IF

    FOR each item IN collection
        do something
    END FOR

    WHILE condition
        do something
    END WHILE

    RETURN result
END FUNCTION
```

**Example 1: Register API**
```
POST /register with name, email, password
    VALIDATE input (email format, password length)  -> 422 if invalid
    IF a user with this email exists THEN return 409
    HASH the password
    SAVE user with default role "user"
    RETURN 201 with user data (without password)
```

**Example 2: Protected endpoint (get_current_user)**
```
READ token from Authorization header
IF token missing THEN return 401
DECODE token with secret and algorithm
IF invalid or expired THEN return 401
FIND user by id in token
IF user not found or inactive THEN return 401
RETURN user
```

**Example 3: Document extraction**
```
POST /documents (file)
    CHECK file type and size
    SAVE file with a random name
    CREATE document record with status "queued"
    START background job with document id
    RETURN 202 with document id

JOB process(document id)
    SET status "processing"
    text = EXTRACT text (digital PDF text, or OCR if text is too short)
    REPEAT up to 3 times
        raw = ASK LLM with schema and text (add last error from previous try)
        TRY parse raw as JSON and validate against schema
        CHECK totals: sum of lines ~ subtotal, subtotal + tax ~ total
        IF all valid THEN save result, status "done", STOP
        ELSE remember the error
    END REPEAT
    SET status "needs_review" with the error
```

**Example 4: Two Sum**
```
FUNCTION two_sum(numbers, target)
    CREATE empty dictionary seen
    FOR each index i and value n IN numbers
        needed = target - n
        IF needed IS IN seen THEN RETURN [seen[needed], i]
        seen[n] = i
    END FOR
    RETURN empty list
END FUNCTION
(time O(n), space O(n))
```

**Example 5: Pagination**
```
page = query page OR 1
limit = MIN(query limit OR 10, 100)
offset = (page - 1) * limit
rows = SELECT items ORDER BY id SKIP offset TAKE limit
total = COUNT items
RETURN rows, page, limit, total
```

**After your pseudo code, always say:**
1. The time and space complexity (or the database index you would need).
2. One edge case (empty input, duplicate, invalid id, timeout).
3. How you would test it (one success case, one failure case).

---

## 20. Interview day strategy

### Before the round
- Have a virtual environment ready with `fastapi`, `uvicorn`, `sqlalchemy`, `pydantic` installed, and an editor with a terminal, so you can start in a minute.
- Know your resume projects well, especially the extraction pipeline and what you personally built.
- Test your internet, screen share and editor font size.

### During theory questions
- Short definition first, then an example from your project.
- Mention a trade-off (for example "BackgroundTasks is simple, but Celery is safer for long jobs").
- If you do not know something, say what you know and how you would find out.

### During live coding
1. Ask about the requirement: fields, validation, authentication, database or in-memory.
2. State the plan: schemas, routes, error handling.
3. Get one route working end to end, and test it in `/docs`.
4. Add validation, correct status codes and error cases.
5. Mention what you would add with more time: database, auth, pagination, tests, Docker.

### Common mistakes to avoid
- Using one model for input and output (leaking `password_hash`).
- Accepting `role` or `id` from the client.
- Blocking calls (`time.sleep`, `requests`) inside `async def`.
- Using a mutable default argument.
- Forgetting `return` or the status code, or returning `200` for created resources.
- Not handling "not found", duplicate or invalid input cases.
- Building SQL with f-strings.
- Sharing one database session across requests, or using the request's session in a background task.
- Silence while thinking. Think aloud.

### Questions you can ask the interviewer
- What does the backend architecture look like (monolith or services)?
- Which database and cloud services does the team use?
- How do you handle deployment, testing and code reviews?
- What would a backend developer in this role work on in the first 3 months?

### Rapid answers for tricky "why" questions
- **Why FastAPI?** Type hints give validation and docs for free, async support for I/O heavy work, great for ML and LLM services.
- **Why Pydantic?** Validation, parsing and clear errors from simple type hints, and the same models drive the API docs.
- **Why SQLAlchemy?** Mature ORM that prevents SQL injection, with migrations through Alembic. For simple queries it is fast to write, and for complex ones raw SQL is available.
- **Why Postgres here?** Strong consistency, relations and constraints (like unique slots), JSONB for flexible extraction results.
- **Why JWT?** Stateless, easy to scale across servers. The trade-off is revoking tokens, handled with short expiry and refresh tokens.
- **Why a queue for document processing?** Long running, can fail and needs retries, and the user should not wait on the HTTP request.

---

## 21. Last day revision checklist

**Be able to write from memory (without looking):**
- [ ] A FastAPI app with CRUD in memory (create, list with filters, get, patch, delete)
- [ ] Pydantic models: create, update (optional fields), output with `from_attributes`
- [ ] `get_db` dependency with `yield`
- [ ] SQLAlchemy model and a CRUD query with `select`
- [ ] Register, login (hash and verify password, create JWT)
- [ ] `get_current_user` and `require_roles` dependencies
- [ ] `HTTPException` and a custom exception handler
- [ ] File upload with `UploadFile`, and a background task
- [ ] Pagination with offset and limit
- [ ] A TestClient test with `dependency_overrides`
- [ ] The extraction function with validate and retry
- [ ] Two Sum, valid parentheses, group anagrams, LRU cache, decorator with `functools.wraps`

**Be able to explain in 30 seconds each:**
- [ ] `def` vs `async def` endpoints, and why blocking code inside `async def` is bad
- [ ] Dependency injection and `Depends`
- [ ] `response_model` and why separate schemas
- [ ] Pydantic validation, `422` errors, `exclude_unset` for PATCH
- [ ] JWT flow, 401 vs 403, where role and ownership checks go
- [ ] Sync vs async SQLAlchemy, N+1 and eager loading, Alembic
- [ ] BackgroundTasks vs Celery
- [ ] GIL, threads vs processes vs asyncio
- [ ] Mutable default arguments, closures, decorators, generators, context managers
- [ ] `is` vs `==`, shallow vs deep copy, `*args` and `**kwargs`
- [ ] Uvicorn vs Gunicorn, Dockerfile, Nginx in front, health checks
- [ ] LLM output reliability: schema, low temperature, validation, retries, human review

**Be ready to talk about your projects:**
- [ ] Document extraction: architecture, OCR step, prompt, validation, retries, providers, status flow, the 70% result
- [ ] Agri-Tech ERP: how FastAPI or Node served the React app, data hierarchy, PostgreSQL
- [ ] Chat and healthcare projects: how Python or Node APIs, auth and roles worked
- [ ] Deployment: Docker, AWS, CI/CD, monitoring (only what you actually did)

---

**Final advice:** For 6 months to 1 year of experience, the interviewers want correct fundamentals, a working API written calmly, safe handling of auth and errors, and honest answers about what you built. If you can build a validated, authenticated CRUD API with FastAPI from an empty folder, and explain the extraction pipeline clearly, you are in a strong position. Good luck!
