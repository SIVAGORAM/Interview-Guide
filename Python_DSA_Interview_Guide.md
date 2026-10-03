# Python + DSA Interview Guide (End to End)
### Python Theory • DSA Theory with Real-Life Examples • Top 100 Coding Programs (Question → Logic → Pseudo Code → Python Code)

> **How to use this document**
> - **Part 1:** Python theory (≈70 questions) — asked in every Python interview
> - **Part 2:** DSA theory (≈45 questions) with a **real-life example** for each
> - **Part 3:** **100 coding programs** — each has: *Question → Example → Logic → Pseudo code → Python code → Complexity*
> - **Part 4:** How to solve any coding question in an interview + cheat sheet
> - **How to answer a coding question in the interview (5 steps):**
>   1. Repeat the question in your own words + ask about edge cases (empty input, negative numbers, duplicates)
>   2. Give one **example** input → output
>   3. Say the **brute force** idea first, then the better idea
>   4. Write **pseudo code**, then the real code
>   5. **Dry run** with the example and state **time/space complexity**
> - Where you don't know: *"I haven't done this exact problem, but my approach would be..."*

---

## Table of Contents
1. [Python Theory Questions](#part-1--python-theory-questions)
2. [DSA Theory Questions with Real-Time Examples](#part-2--dsa-theory-questions-with-real-time-examples)
3. [Top 100 Coding Programs](#part-3--top-100-coding-programs)
   - A. Numbers and Logic (1–20)
   - B. Strings (21–42)
   - C. Arrays / Lists (43–68)
   - D. Sorting (69–73)
   - E. Linked List (74–79)
   - F. Stack and Queue (80–84)
   - G. Trees (85–89)
   - H. Graphs (90–91)
   - I. Recursion, Backtracking and Dynamic Programming (92–100)
4. [Interview Strategy and Cheat Sheet](#part-4--interview-strategy-and-cheat-sheet)

---

# Part 1 — Python Theory Questions

## 1.1 Basics

### Q1. What is Python? Key features?
Python is a **high-level, interpreted, general-purpose programming language** known for simple, readable syntax. Features: easy to learn, **dynamically typed**, **object-oriented** (also supports procedural and functional style), huge standard library and third-party packages, cross-platform, large community. Used for web (Django, Flask, FastAPI), data science (NumPy, pandas), AI/ML, automation, scripting, DevOps.

### Q2. Is Python interpreted or compiled?
Both, in a way. Python source (`.py`) is first **compiled to bytecode** (`.pyc`), and then the **Python Virtual Machine (PVM)** interprets that bytecode line by line. So we normally call Python an **interpreted language.**

### Q3. What is CPython? What other implementations exist?
**CPython** is the default implementation (written in C). Others: **PyPy** (faster, uses JIT), **Jython** (Java), **IronPython** (.NET).

### Q4. Dynamically typed vs statically typed? Is Python strongly typed?
In Python you don't declare variable types; the type is decided **at runtime** (`x = 5`, then `x = "hi"` is allowed). Python is **dynamically typed but strongly typed**: it will not silently mix incompatible types (`"5" + 5` raises `TypeError`).

### Q5. What is PEP 8?
The official **style guide** for Python code: 4 spaces indentation, `snake_case` for variables/functions, `PascalCase` for classes, `UPPER_CASE` for constants, max ~79 chars per line, meaningful names, spaces around operators.

### Q6. What are Python's built-in data types?
- **Numeric:** `int`, `float`, `complex`, `bool`
- **Sequence:** `str`, `list`, `tuple`, `range`
- **Mapping:** `dict`
- **Set types:** `set`, `frozenset`
- **None type:** `None`
- **Binary:** `bytes`, `bytearray`

### Q7. Mutable vs immutable types? (VERY IMPORTANT)
- **Mutable** (can change after creation): `list`, `dict`, `set`, `bytearray`, custom objects.
- **Immutable** (cannot change): `int`, `float`, `str`, `tuple`, `frozenset`, `bool`, `bytes`.
```python
a = [1, 2]; a.append(3)      # same object modified
s = "hi"; s[0] = "H"         # TypeError: str is immutable
```
Only **immutable (hashable)** objects can be dictionary keys or set members.

### Q8. List vs Tuple?
| List | Tuple |
|---|---|
| Mutable | Immutable |
| `[1, 2, 3]` | `(1, 2, 3)` |
| Slower, more memory | Faster, less memory |
| Many methods (append, remove...) | Few methods (count, index) |
| Cannot be dict key | Can be a dict key (if elements are hashable) |
Use tuples for fixed data (coordinates, DB rows); lists for changing collections.

### Q9. List vs Set vs Dictionary?
- **List:** ordered, allows duplicates, indexed.
- **Set:** **unordered, unique elements only**, very fast membership test (`in`).
- **Dict:** key–value pairs, keys unique; since **Python 3.7 dicts keep insertion order.**
```python
s = {1, 2, 2, 3}         # {1, 2, 3}
d = {"name": "Ravi", "age": 25}
d.get("city", "NA")      # safe access with default
```

### Q10. How does a dictionary work internally?
It uses a **hash table.** The key is passed to `hash()`, which decides the storage slot. Average time for get/set/delete is **O(1).** Keys must be **hashable** (immutable).

### Q11. `==` vs `is`?
`==` compares **values.** `is` compares **identity** (same object in memory).
```python
a = [1, 2]; b = [1, 2]
a == b      # True
a is b      # False (different objects)
x = None
x is None   # use "is" for None
```

### Q12. Shallow copy vs deep copy?
```python
import copy
a = [[1, 2], [3, 4]]
b = copy.copy(a)        # shallow: new outer list, SAME inner lists
c = copy.deepcopy(a)    # deep: everything copied independently
a[0].append(99)
print(b[0])   # [1, 2, 99]  (affected)
print(c[0])   # [1, 2]      (not affected)
```
`b = a` is not a copy at all; both names point to the **same object.**

### Q13. Is Python pass-by-value or pass-by-reference?
Neither exactly: it is **pass by object reference** ("call by assignment"). If the object is **mutable**, changes inside the function are visible outside; for **immutable** objects, re-assigning creates a new object locally.
```python
def add(lst): lst.append(1)     # modifies caller's list
def inc(n): n += 1              # caller's int unchanged
```

### Q14. What is the mutable default argument trap?
```python
def add_item(x, items=[]):      # BAD: the list is created ONCE
    items.append(x); return items
add_item(1); add_item(2)        # [1] then [1, 2]  (surprise!)

def add_item(x, items=None):    # GOOD
    if items is None: items = []
    items.append(x); return items
```

### Q15. Common operators to remember?
`+ - * / // % **` (`/` true division, `//` floor division, `%` remainder, `**` power); comparison `== != < > <= >=`; logical `and or not`; membership `in`, `not in`; identity `is`, `is not`; bitwise `& | ^ ~ << >>`.
```python
7 / 2    # 3.5
7 // 2   # 3
-7 // 2  # -4 (floors toward negative infinity)
```

### Q16. String basics: immutability, slicing, common methods?
```python
s = "Hello World"
s[0]; s[-1]; s[0:5]; s[::-1]        # H, d, Hello, dlroW olleH
s.upper(); s.lower(); s.title(); s.strip()
s.split(" "); "-".join(["a","b"]); s.replace("l","L")
s.find("o"); s.count("l"); s.startswith("He"); s.isdigit(); s.isalpha()
f"Name: {name}, Age: {age}"        # f-string (preferred formatting)
```
Slicing: `seq[start:stop:step]` (stop is excluded).

### Q17. `range`, `enumerate`, `zip`?
```python
for i in range(1, 10, 2): ...          # 1,3,5,7,9
for i, v in enumerate(["a","b"], start=1): ...   # (1,'a'), (2,'b')
for a, b in zip([1,2,3], ["x","y","z"]): ...
dict(zip(keys, values))
```

### Q18. `break`, `continue`, `pass`, and `for...else`?
`break` exits the loop; `continue` skips to the next iteration; `pass` does nothing (placeholder). In `for...else`, the **else runs only if the loop finished without `break`.**
```python
for n in range(2, 10):
    if 10 % n == 0: print("found"); break
else:
    print("no divisor")
```

### Q19. Truthy and falsy values?
Falsy: `False, None, 0, 0.0, "", [], (), {}, set()`. Everything else is truthy. So `if my_list:` checks non-empty.

## 1.2 Functions and Functional Features

### Q20. Types of function arguments?
```python
def f(a, b=10, *args, **kwargs): ...
# positional: f(1, 2)    keyword: f(a=1, b=2)    default: b=10
# *args  -> extra positional args as a tuple
# **kwargs -> extra keyword args as a dict
f(1, 2, 3, 4, x=5)  # args=(3,4), kwargs={'x':5}
```
Unpacking in calls: `f(*[1,2])`, `f(**{"a":1})`.

### Q21. What is a lambda function?
A small **anonymous** one-line function.
```python
square = lambda x: x * x
sorted(words, key=lambda w: len(w))
```

### Q22. `map`, `filter`, `reduce`?
```python
nums = [1, 2, 3, 4]
list(map(lambda x: x*2, nums))            # [2, 4, 6, 8]
list(filter(lambda x: x % 2 == 0, nums))  # [2, 4]
from functools import reduce
reduce(lambda a, b: a + b, nums)          # 10
```
Comprehensions are usually more "Pythonic": `[x*2 for x in nums]`.

### Q23. List, dict, set comprehensions and generator expressions?
```python
[x**2 for x in range(5) if x % 2 == 0]     # [0, 4, 16]
{x: x**2 for x in range(3)}                # {0:0, 1:1, 2:4}
{c for c in "hello"}                       # set of unique chars
(x**2 for x in range(5))                   # generator expression (lazy, memory efficient)
```

### Q24. Iterable vs iterator?
**Iterable:** an object you can loop over (has `__iter__`): list, str, dict. **Iterator:** an object that returns items one at a time using `__next__` and remembers its position. `iter(obj)` gives an iterator; `next(it)` gets the next item; raises `StopIteration` at the end.

### Q25. What are generators? Why use them? (VERY IMPORTANT)
A function that uses **`yield`** returns a **lazy iterator**: it produces values **one at a time** and pauses between them, so it uses very little memory (good for big files/streams).
```python
def count_up(n):
    i = 1
    while i <= n:
        yield i
        i += 1

for x in count_up(3): print(x)    # 1 2 3
```
**Real-time use:** reading a 10 GB log file line by line.

### Q26. What is a decorator? Example?
A function that **takes a function and adds extra behavior** without changing its code (`@decorator` syntax). Used for logging, timing, authentication, caching.
```python
import time
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time()-start:.4f}s")
        return result
    return wrapper

@timer
def slow():
    time.sleep(1)
```

### Q27. What is a closure?
An inner function that **remembers variables from its enclosing function** even after the outer function has finished.
```python
def multiplier(n):
    def inner(x): return x * n
    return inner
double = multiplier(2); double(5)   # 10
```

### Q28. Scope and the LEGB rule? `global` and `nonlocal`?
Python looks up names in order: **L**ocal → **E**nclosing → **G**lobal → **B**uilt-in. Use `global x` to modify a global inside a function; `nonlocal x` to modify a variable of the enclosing function.

### Q29. What is recursion? Is there a limit in Python?
A function calling itself with a smaller problem, needing a **base case.** Python's default recursion limit is about **1000**; deeper recursion raises `RecursionError` (`sys.setrecursionlimit()` can change it, but prefer iteration or memoization).

### Q30. What is `functools.lru_cache`?
A decorator that **caches function results (memoization)**, speeding up repeated calls (e.g., recursive Fibonacci).
```python
from functools import lru_cache
@lru_cache(maxsize=None)
def fib(n): return n if n < 2 else fib(n-1) + fib(n-2)
```

### Q31. `sort()` vs `sorted()`?
`list.sort()` sorts **in place** and returns `None`. `sorted(iterable)` returns a **new sorted list** and works on any iterable. Both accept `key=` and `reverse=` and are **stable**; Python uses **Timsort** (O(n log n)).
```python
sorted(people, key=lambda p: (p["age"], p["name"]), reverse=True)
```

### Q32. Useful built-in functions?
`len, sum, min, max, abs, round, sorted, reversed, any, all, zip, enumerate, map, filter, isinstance, type, id, input, print, int, float, str, list, dict, set, tuple, bool, chr, ord, divmod, pow`.

### Q33. Unpacking and swapping?
```python
a, b = b, a                       # swap
first, *rest = [1, 2, 3, 4]       # first=1, rest=[2,3,4]
x, y = (10, 20)
{**d1, **d2}                      # merge dicts (d1 | d2 in Python 3.9+)
```

## 1.3 Exceptions, Files and Modules

### Q34. Exception handling in Python?
```python
try:
    x = int(input())
    result = 10 / x
except ValueError:
    print("Not a number")
except ZeroDivisionError:
    print("Cannot divide by zero")
except Exception as e:
    print("Other error:", e)
else:
    print("No error:", result)      # runs only if no exception
finally:
    print("Always runs")            # cleanup
```
Raise your own: `raise ValueError("bad value")`. Custom exception: `class MyError(Exception): pass`.

### Q35. Common built-in exceptions?
`ValueError, TypeError, KeyError, IndexError, AttributeError, ZeroDivisionError, FileNotFoundError, ImportError/ModuleNotFoundError, NameError, RecursionError, StopIteration, KeyboardInterrupt`.

### Q36. File handling and the `with` statement?
```python
with open("data.txt", "r") as f:       # automatically closes the file
    for line in f:                      # read line by line (memory efficient)
        print(line.strip())

with open("out.txt", "w") as f:         # "w" overwrite, "a" append, "r+" read/write
    f.write("Hello\n")
```
Modes: `r, w, a, rb, wb`. Methods: `read(), readline(), readlines(), write(), writelines()`.

### Q37. What is a context manager?
An object that sets up and cleans up resources automatically with `with` (files, DB connections, locks). Implement with `__enter__/__exit__` or `@contextlib.contextmanager`.

### Q38. Modules vs packages? `import` styles? `__name__ == "__main__"`?
A **module** is a `.py` file; a **package** is a folder with modules (traditionally has `__init__.py`).
```python
import math
from math import sqrt
import numpy as np
```
```python
if __name__ == "__main__":
    main()      # runs only when the file is executed directly, not when imported
```

### Q39. pip, virtual environment, requirements.txt?
`pip install requests` installs packages. A **virtual environment** isolates project dependencies: `python -m venv venv` → activate (`venv\Scripts\activate` on Windows, `source venv/bin/activate` on Linux/Mac). `pip freeze > requirements.txt` and `pip install -r requirements.txt`.

### Q40. JSON handling and serialization?
```python
import json
data = json.loads('{"a": 1}')      # string → dict
text = json.dumps(data, indent=2)  # dict → string
json.load(f); json.dump(data, f)   # with files
```
`pickle` serializes Python objects to bytes (don't unpickle untrusted data).

### Q41. Regular expressions basics?
```python
import re
re.search(r"\d+", "abc123")       # first match
re.findall(r"\w+@\w+\.\w+", text) # all matches
re.sub(r"\s+", " ", text)         # replace
re.match(r"^\d{10}$", phone)      # match at start
```

## 1.4 Object-Oriented Programming

### Q42. What is OOP? Four pillars?
Programming using **classes and objects.** Pillars: **Encapsulation** (bundle data + methods, hide details), **Abstraction** (show only what's necessary), **Inheritance** (reuse a parent's features), **Polymorphism** (same interface, different behavior).

### Q43. Class, object, `__init__`, `self`?
```python
class Student:
    school = "ABC School"              # class variable (shared by all)
    def __init__(self, name, age):     # constructor
        self.name = name               # instance variables
        self.age = age
    def greet(self):
        return f"Hi, I'm {self.name}"

s = Student("Ravi", 20)
s.greet()
```
`self` refers to **the current object.**

### Q44. Class variable vs instance variable?
Class variables belong to the **class** and are shared; instance variables belong to **each object** (defined with `self.`).

### Q45. Inheritance types and `super()`?
Single, multiple, multilevel, hierarchical, hybrid.
```python
class Animal:
    def speak(self): return "..."
class Dog(Animal):
    def speak(self): return "Woof"           # overriding
class Puppy(Dog):
    def __init__(self, name):
        super().__init__()                    # call parent's constructor
```

### Q46. What is MRO?
**Method Resolution Order:** the order in which Python searches classes for a method (uses the **C3 linearization**). Check with `ClassName.__mro__` or `ClassName.mro()`. It solves the **diamond problem** in multiple inheritance.

### Q47. Polymorphism, overriding and overloading?
**Overriding:** child redefines a parent method. **Overloading** (same name, different parameters) is **not directly supported**; the last definition wins. Use default args or `*args`. **Duck typing:** "if it walks like a duck and quacks like a duck, it's a duck" — Python cares about **behavior (methods), not type.**

### Q48. Encapsulation: public, protected, private?
`name` public; `_name` protected (convention only); `__name` private (**name mangling** → `_ClassName__name`). Python does not enforce strict privacy.

### Q49. Abstraction: abstract classes?
```python
from abc import ABC, abstractmethod
class Shape(ABC):
    @abstractmethod
    def area(self): pass
class Circle(Shape):
    def __init__(self, r): self.r = r
    def area(self): return 3.14 * self.r ** 2
# Shape() → TypeError (cannot instantiate abstract class)
```

### Q50. `@staticmethod`, `@classmethod`, `@property`?
```python
class Circle:
    def __init__(self, r): self._r = r
    @property
    def radius(self): return self._r                 # access like attribute: c.radius
    @classmethod
    def from_diameter(cls, d): return cls(d / 2)     # alternative constructor, gets cls
    @staticmethod
    def pi(): return 3.14159                          # no self/cls, utility function
```

### Q51. Dunder (magic) methods?
`__init__` (constructor), `__str__` (readable string for `print`), `__repr__` (developer string), `__len__`, `__eq__`, `__lt__`, `__add__`, `__getitem__`, `__iter__`, `__next__`, `__call__`, `__enter__/__exit__`, `__del__`.
```python
class Point:
    def __init__(self, x, y): self.x, self.y = x, y
    def __add__(self, o): return Point(self.x + o.x, self.y + o.y)
    def __str__(self): return f"({self.x}, {self.y})"
```

### Q52. `__str__` vs `__repr__`?
`__str__` → user-friendly output; `__repr__` → unambiguous, for developers/debugging (ideally can recreate the object). `print()` uses `__str__`; if absent it falls back to `__repr__`.

### Q53. `__init__` vs `__new__`?
`__new__` **creates** the object (rarely overridden); `__init__` **initializes** the already-created object.

### Q54. Dataclasses?
Reduce boilerplate for classes that mostly store data:
```python
from dataclasses import dataclass
@dataclass
class User:
    name: str
    age: int = 18
```
It auto-generates `__init__`, `__repr__`, `__eq__`.

### Q55. What are type hints?
Optional annotations to document types: `def add(a: int, b: int) -> int:`. They are **not enforced at runtime**; tools like `mypy` check them.

## 1.5 Advanced and Internals

### Q56. What is the GIL? (VERY COMMON)
The **Global Interpreter Lock** in CPython allows **only one thread to execute Python bytecode at a time.** So **multithreading does not speed up CPU-bound tasks**, but it **helps I/O-bound tasks** (network, files), because threads release the GIL while waiting. For CPU-bound work, use **multiprocessing**.

### Q57. Multithreading vs multiprocessing vs asyncio?
| | Threading | Multiprocessing | asyncio |
|---|---|---|---|
| Best for | I/O-bound | CPU-bound | Many I/O tasks (high concurrency) |
| Parallel? | No (GIL) | Yes (separate processes) | No (single thread, cooperative) |
| Memory | Shared | Separate | Shared |
```python
import asyncio
async def fetch(n):
    await asyncio.sleep(1); return n
async def main():
    print(await asyncio.gather(fetch(1), fetch(2), fetch(3)))   # runs concurrently ≈ 1s
asyncio.run(main())
```

### Q58. How does Python manage memory? Garbage collection?
Python has a private **heap** for objects, managed by the interpreter. **Reference counting** frees an object when its count becomes 0; a **cyclic garbage collector** (`gc` module) cleans reference cycles. `sys.getrefcount(obj)` shows the count; `del x` removes a name (not necessarily the object).

### Q59. What is the `collections` module?
- `Counter` – counts items: `Counter("hello")`
- `defaultdict` – dict with default values: `defaultdict(list)`
- `deque` – fast appends/pops from both ends (O(1))
- `OrderedDict`, `namedtuple`, `ChainMap`
```python
from collections import Counter, defaultdict, deque
Counter([1,1,2]).most_common(1)   # [(1, 2)]
```

### Q60. `itertools` and `functools` highlights?
`itertools`: `permutations, combinations, product, chain, groupby, accumulate, islice, cycle`. `functools`: `reduce, partial, lru_cache, wraps`.

### Q61. Time complexity of common Python operations?
| Operation | list | dict / set |
|---|---|---|
| Access by index/key | O(1) | O(1) avg |
| Append | O(1) amortized | – |
| Insert/delete at front or middle | O(n) | – |
| Search (`in`) | **O(n)** | **O(1) avg** |
| Sort | O(n log n) | – |
| `deque.appendleft/popleft` | O(1) | – |

### Q62. How to optimize Python code?
Use built-ins and comprehensions, **sets/dicts for lookups**, generators for big data, avoid repeated work (cache), use `join` for strings (not `+=` in loops), choose the right data structure, profile with `cProfile`/`timeit`, use NumPy for numeric work, multiprocessing for CPU tasks.

### Q63. What is monkey patching? What is `*` vs `**` in definitions?
Monkey patching = changing a class/module at runtime (use sparingly). `*args` collects extra positional, `**kwargs` extra keyword arguments (Q20).

### Q64. Unit testing in Python?
Use **`unittest`** (built-in) or **`pytest`** (popular).
```python
# test_math.py
def add(a, b): return a + b
def test_add(): assert add(2, 3) == 5       # run: pytest
```
Also `unittest.mock` / `pytest-mock` for mocking.

### Q65. Logging vs print?
`logging` supports levels (DEBUG, INFO, WARNING, ERROR, CRITICAL), formats, files and handlers; prefer it over `print` in real apps.
```python
import logging
logging.basicConfig(level=logging.INFO)
logging.info("Started")
```

### Q66. Django vs Flask vs FastAPI?
- **Django:** full-featured ("batteries included"): ORM, admin, auth, templates.
- **Flask:** lightweight micro-framework, flexible.
- **FastAPI:** modern, **fast, async**, automatic validation (Pydantic) and docs (OpenAPI/Swagger), great for APIs.

### Q67. NumPy and pandas basics (awareness)?
**NumPy:** fast n-dimensional arrays and math. **pandas:** DataFrames for tabular data (`read_csv`, `groupby`, `merge`, `fillna`). NumPy arrays are faster than lists because they are typed and stored contiguously.

### Q68. What is a virtual environment & why not install globally?
Isolated environment per project, avoiding version conflicts between projects.

### Q69. The walrus operator and f-string tricks?
```python
if (n := len(data)) > 10: print(f"{n=} is large")    # := assigns inside expressions
f"{3.14159:.2f}"   # 3.14        f"{1000000:,}"   # 1,000,000
```

### Q70. What are `*args` and `**kwargs` used for in real life?
Writing flexible functions/wrappers and **decorators** that pass any arguments through to another function.

## 1.6 Output-Based Tricky Questions (very common)

```python
print(type([]), type(()), type({}), type({1}))   # list tuple dict set
print(0.1 + 0.2 == 0.3)                           # False (floating point)
print(bool("False"), bool(""))                    # True False
print([1, 2, 3][5:])                              # []  (slicing never raises IndexError)
print("abc" * 2, [0] * 3)                         # abcabc [0, 0, 0]
a = [1, 2, 3]; b = a; b.append(4); print(a)       # [1, 2, 3, 4]
print(3 * "ab" + "c")                             # abababc
print(10 // 3, -10 // 3, 10 % 3, -10 % 3)         # 3 -4 1 2
x = 5
def f():
    print(x)
    # x = 10  ← if uncommented, UnboundLocalError (x is local in f)
t = (1, [2, 3]); t[1].append(4); print(t)         # (1, [2, 3, 4]) tuple holds a mutable list
print(len({1, 1, 2}), len({}), len(set()))        # 2 0 0
print(sorted("hello"))                             # ['e', 'h', 'l', 'l', 'o']
print(int("10") + int(3.9))                        # 13  (int truncates)
print(round(2.5), round(3.5))                      # 2 4  (banker's rounding)
print(list(range(5, 0, -2)))                       # [5, 3, 1]
print(None == False, None is None)                 # False True
```

---

# Part 2 — DSA Theory Questions with Real-Time Examples

## 2.1 Foundations

### D1. What is a data structure? Types?
A way of **organizing and storing data** so it can be used efficiently.
- **Linear:** Array, Linked List, Stack, Queue (data in a sequence)
- **Non-linear:** Tree, Graph, Heap, Trie (data in hierarchy/network)
- **Hash-based:** Hash table / dictionary / set
**Real-time example:** A library — books on shelves (array), a chain of "next book" notes (linked list), a pile of returned books (stack), a lending queue (queue), the catalog category tree (tree), a map of library branches (graph).

### D2. What is an algorithm? Qualities of a good algorithm?
A **step-by-step procedure to solve a problem.** Good algorithms are **correct, efficient (time and memory), clear, finite.**
**Real-time example:** A recipe. Same dish, different methods; some are faster or use fewer vessels.

### D3. What is time complexity and space complexity? What is Big-O?
- **Time complexity:** how the running time **grows as input size n grows** (not actual seconds).
- **Space complexity:** how the extra memory grows with n.
- **Big-O** describes the **worst-case upper bound.** (Also: Ω best case, Θ tight/average bound.)

| Big-O | Name | Example |
|---|---|---|
| O(1) | Constant | Access `arr[5]`, dict lookup |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Loop through a list once |
| O(n log n) | Linearithmic | Merge sort, `sorted()` |
| O(n²) | Quadratic | Nested loops, bubble sort |
| O(2ⁿ) | Exponential | Recursive Fibonacci, all subsets |
| O(n!) | Factorial | All permutations |

**Real-time example:** Finding a name in a phone book. O(n) = reading every page; O(log n) = open in the middle and discard half each time; O(1) = you already know the page number.

### D4. How do you calculate time complexity quickly?
Single loop = O(n). Nested loops = O(n·m) or O(n²). Halving the input each step = O(log n). Drop constants and lower-order terms (`3n² + 5n + 2` → O(n²)). Sequential steps add (O(n) + O(n²) = O(n²)). Recursive calls: count branches × depth.

### D5. What is amortized time complexity?
The **average cost per operation over a long sequence**, even if some single operations are expensive. Python `list.append` is **O(1) amortized:** it's usually O(1), but occasionally the list resizes (O(n)); averaged out it's O(1).
**Real-time example:** Buying a monthly bus pass: one big payment, but cheap per ride on average.

### D6. Space-time trade-off?
Using **more memory to save time** (e.g., a hash map or cache) or less memory but more time. **Real-time example:** Keeping a written phone-number list (memory) so you don't search the directory (time) each time.

## 2.2 Linear Data Structures

### D7. Array vs Linked List?
| Array | Linked List |
|---|---|
| Contiguous memory | Nodes scattered, linked by pointers |
| Access by index **O(1)** | Access **O(n)** |
| Insert/delete in middle **O(n)** (shifting) | Insert/delete **O(1)** if you have the node |
| Fixed/dynamic size with resizing | Grows/shrinks easily |
| Better cache performance | Extra memory for pointers |
**Real-time example:** Array = numbered seats in a cinema (jump directly to seat 25). Linked list = a treasure hunt, each clue leads to the next.

### D8. How is Python's `list` implemented?
A **dynamic array** (over-allocates memory and resizes when full). So indexing is O(1), `append` is O(1) amortized, `insert(0, x)` / `pop(0)` are O(n). Use `collections.deque` for fast front operations.

### D9. Types of linked lists?
- **Singly:** each node has `data` + `next`
- **Doubly:** `data` + `next` + `prev` (move both ways)
- **Circular:** the last node points back to the first
**Real-time examples:** Singly = one-way chain of a train's coaches moving forward; **Doubly = browser back/forward history or a music playlist (next/previous);** Circular = round-robin turn in a multiplayer game or CPU scheduling.

### D10. What is a Stack? Operations? Applications?
**LIFO** (Last In, First Out). Operations: `push`, `pop`, `peek/top`, `is_empty` — all O(1). In Python use a `list` (`append`, `pop`).
Applications: **undo/redo**, **browser back button**, function **call stack**, **expression evaluation**, **balanced parentheses**, DFS.
**Real-time example:** A stack of plates — you take the top plate you placed last.

### D11. What is a Queue? Variants?
**FIFO** (First In, First Out): `enqueue`, `dequeue`. Use `collections.deque` (O(1) at both ends). Variants: **Circular queue**, **Deque** (double-ended), **Priority queue** (highest priority first).
Applications: **BFS**, task scheduling, printer queue, message queues (Kafka/RabbitMQ), request handling.
**Real-time example:** People standing in a ticket counter line.

### D12. Stack vs Queue?
Stack = LIFO (one open end), Queue = FIFO (two ends). **Real-time:** a stack of books vs a line at a bank.

### D13. What is a Priority Queue / Heap?
A priority queue always gives the **element with highest (or lowest) priority first.** It's usually built with a **binary heap** (a complete binary tree where the parent ≤ children for a **min-heap**). Insert and remove: **O(log n)**; peek: O(1); build heap: O(n). Python: `heapq` (min-heap).
**Real-time example:** Hospital emergency room — the most critical patient is treated first, not the first one who arrived. Also Dijkstra's algorithm, task schedulers, "top K" problems.

### D14. What is a Hash Table? How does it work? Collisions?
Stores **key → value** pairs. A **hash function** converts the key into an index. Average **O(1)** insert/search/delete; worst case O(n) with many collisions.
**Collision** = two keys map to the same index. Solutions: **Chaining** (each slot has a list) and **Open addressing** (probing for the next free slot). **Load factor** = items/slots; when it gets high, the table **resizes (rehashing).**
**Real-time example:** A hotel key rack — room number (key) tells exactly which hook (slot) holds your key. Python's `dict` and `set` are hash tables.

### D15. Why are dictionary lookups faster than list searches?
List `in` scans every element (O(n)); dict/set compute the hash and jump straight to the slot (O(1) average). **Real-time:** reading a book from page 1 to find a word vs using the index at the back.

## 2.3 Trees and Graphs

### D16. Tree terminology?
**Root** (top node), **parent/child**, **leaf** (no children), **edge**, **height** (longest path root→leaf), **depth** (distance from root), **subtree**, **level**.
**Real-time example:** A company org chart, a file system (folders/subfolders), HTML DOM.

### D17. Binary tree vs Binary Search Tree (BST)?
**Binary tree:** each node has at most 2 children. **BST:** left subtree values **<** node **<** right subtree values. Search/insert/delete: **O(log n) average, O(n) worst** (skewed tree).
**Real-time example:** The "guess a number" game or looking up a word in a sorted dictionary.

### D18. Tree traversals?
- **DFS types:** **Inorder** (Left, Root, Right → gives sorted order in a BST), **Preorder** (Root, Left, Right → copy a tree), **Postorder** (Left, Right, Root → delete a tree)
- **BFS / Level order:** level by level (uses a queue)
**Real-time example:** Preorder = listing a folder then its contents; postorder = deleting a folder after deleting its contents; level order = reading an org chart from CEO downward.

### D19. Balanced trees: AVL and Red-Black?
Self-balancing BSTs that keep height ≈ log n using **rotations**, guaranteeing O(log n) operations. AVL is more strictly balanced (faster lookups); Red-Black is faster for inserts/deletes. **Real-time:** database indexes use B-trees (a related balanced multi-way tree); Java `TreeMap` uses Red-Black.

### D20. What is a Trie?
A tree for **strings** where each node represents a character; words share prefixes. Search/insert by word length: O(L).
**Real-time example:** **Autocomplete / search suggestions, spell checkers, phone contacts search.**

### D21. What is a Graph? Types? Representations?
Nodes (**vertices**) connected by **edges.** Types: **directed/undirected, weighted/unweighted, cyclic/acyclic (DAG).**
Representations: **Adjacency list** (dict of lists; space O(V+E); best for sparse graphs) and **adjacency matrix** (V×V grid; O(1) edge check; O(V²) space).
**Real-time examples:** Google Maps (cities = nodes, roads = edges), Facebook/LinkedIn friends, flight routes, internet routers.

### D22. BFS vs DFS?
| BFS | DFS |
|---|---|
| Explores **level by level** | Goes **deep** first, then backtracks |
| Uses a **queue** | Uses **stack/recursion** |
| Finds **shortest path** in unweighted graphs | Good for cycle detection, topological sort, path existence, mazes |
**Real-time example:** BFS = ripples in water spreading outward / finding people within 2 connections on LinkedIn. DFS = exploring a maze by going down one path fully before trying another.

### D23. Shortest-path and spanning tree algorithms?
- **Dijkstra:** shortest path with **non-negative weights** (uses a min-heap), O((V+E) log V). *Real-time:* GPS navigation.
- **Bellman-Ford:** works with **negative weights**, O(V·E).
- **Floyd–Warshall:** all pairs shortest paths.
- **MST (Minimum Spanning Tree) – Kruskal/Prim:** connect all nodes with the **minimum total edge weight.** *Real-time:* laying cables to connect all offices at the least cost.

### D24. What is Topological Sort?
An ordering of a **DAG's** vertices so every edge goes from earlier to later. *Real-time:* **course prerequisites**, build systems (compile dependencies), task scheduling.

### D25. What is Union-Find (Disjoint Set)?
Tracks which elements belong to the same group with `find` and `union` (with path compression and union by rank, almost O(1)). *Real-time:* "Are these two people in the same friend circle?", network connectivity, Kruskal's MST, detecting cycles.

### D26. How do you detect a cycle in a linked list / graph?
Linked list: **Floyd's Tortoise and Hare** (slow moves 1 step, fast moves 2; if they meet → cycle). Graph: DFS with a "visiting" state (directed) or Union-Find (undirected). *Real-time:* a circular dependency between two services (A needs B, B needs A).

## 2.4 Searching and Sorting

### D27. Linear search vs Binary search?
Linear: check each item, **O(n)**, works on any list. **Binary search: O(log n), requires a sorted array;** compare with the middle and discard half each time.
**Real-time example:** Finding a name in an alphabetical dictionary by opening the middle page.

### D28. Compare sorting algorithms.
| Algorithm | Best | Average | Worst | Space | Stable? |
|---|---|---|---|---|---|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes |
Python's `sorted()` uses **Timsort** (merge + insertion, stable, O(n log n)).

### D29. What does "stable" and "in-place" sorting mean?
**Stable:** equal elements keep their original relative order (important when sorting by multiple keys). **In-place:** uses O(1) extra memory.
**Real-time example (stable):** Sort employees by salary; those with the same salary stay in the original alphabetical order.

### D30. Merge sort vs Quick sort? When to use which?
Merge sort: divide into halves, sort, merge; **guaranteed O(n log n), stable, needs O(n) extra space**, good for linked lists and huge data (external sorting). Quick sort: pick a pivot, partition; **fast in practice, in-place, but worst case O(n²)** with bad pivots. *Real-time:* Merge = two teachers each sorting half the exam papers, then merging the piles. Quick = pick a "median" student and split the class into shorter/taller groups.

### D31. Insertion sort — when is it good?
Best for **small or nearly sorted** data. *Real-time:* sorting playing cards in your hand one card at a time.

## 2.5 Algorithm Techniques

### D32. What is Recursion?
A function calling itself on a smaller subproblem, with a **base case** to stop. Every call uses call-stack memory, so very deep recursion causes **stack overflow.** *Real-time:* Russian nesting dolls (open one, inside is a smaller one, until the smallest); folders inside folders.

### D33. Divide and Conquer?
Split the problem into smaller parts, solve each, **combine** results. Examples: merge sort, quick sort, binary search. *Real-time:* Splitting a big cleaning job among a team by rooms.

### D34. What is Dynamic Programming (DP)? (VERY IMPORTANT)
Solving problems by **breaking into overlapping subproblems and storing results** to avoid recomputation. Two properties: **overlapping subproblems** and **optimal substructure.**
- **Memoization (top-down):** recursion + cache
- **Tabulation (bottom-up):** fill a table iteratively
Examples: Fibonacci, climbing stairs, knapsack, coin change, LCS, LIS.
*Real-time example:* Computing 1+1+1+1+1+1+1+1 = ? then "add one more" — you don't recount; you remember the previous total. Also Google Maps caching sub-route times.

### D35. Greedy algorithm vs DP?
**Greedy** makes the **best local choice at each step** hoping for a global optimum (not always correct, but fast). **DP** considers all subproblem choices to guarantee optimal. Greedy examples: activity selection, Huffman coding, Dijkstra, fractional knapsack, coin change for standard currencies. *Real-time:* Giving change with the largest notes first.

### D36. Backtracking?
Build a solution step by step; if a path fails, **undo (backtrack)** and try another. Examples: N-Queens, Sudoku, permutations, subsets, maze solving. *Real-time:* Solving a maze: go down a path, hit a dead end, return and try the next path.

### D37. Two Pointers technique?
Use two indices moving through the data (from both ends or at different speeds) to reduce O(n²) to O(n). Used for: pair sum in a sorted array, reversing, palindromes, merging sorted lists, removing duplicates. *Real-time:* Two people searching a corridor from opposite ends until they meet.

### D38. Sliding Window technique?
Maintain a **window** (range) over the data and **slide** it, updating the result incrementally instead of recomputing. Used for: max sum of k elements, longest substring without repeats, subarray with given sum. *Real-time:* **Moving average of the last 7 days of sales; a TCP sliding window for network packets.**

### D39. Prefix Sum technique?
Precompute running totals so any range sum becomes `prefix[r] - prefix[l-1]` in O(1). *Real-time:* A bank passbook with running balance: balance between two dates = difference of two balances.

### D40. Hashing technique (frequency map)?
Store counts/seen values in a dict/set to turn O(n²) lookups into O(n). Used for: two sum, duplicates, anagrams, first unique character. *Real-time:* A tally sheet at an election counting votes.

### D41. Bit manipulation basics?
Operators: `& | ^ ~ << >>`. Tricks: `n & 1` (odd/even), `n & (n-1) == 0` (power of 2), `a ^ a = 0` and `a ^ 0 = a` (find the single non-repeating number), `n >> 1` (divide by 2), `1 << k` (2^k). *Real-time:* Permissions flags (read/write/execute) stored in a single number.

### D42. Monotonic stack?
A stack that stays sorted (increasing or decreasing) to solve "next greater/smaller element" problems in O(n). *Real-time:* Finding for each day when a warmer day arrives (daily temperatures).

### D43. LRU Cache — how to design?
**Least Recently Used cache:** evicts the item not used for the longest time when full. Needs O(1) `get` and `put` → **hash map + doubly linked list** (Python: `OrderedDict`). *Real-time:* Your phone's recent apps list or browser cache.

### D44. Floyd's cycle detection and fast/slow pointers?
Two pointers moving at different speeds: find cycles, the middle of a linked list, or the start of a loop. *Real-time:* Two runners on a circular track; the faster eventually laps the slower.

### D45. Which data structure for which problem? (Quick guide)
| Need | Use |
|---|---|
| Fast lookup / counting / uniqueness | Hash map / set |
| Last-in-first-out, undo, matching brackets | Stack |
| First-in-first-out, BFS, scheduling | Queue / deque |
| Always get min/max, top-K | Heap |
| Sorted data, range queries | Sorted array / BST |
| Prefix search, autocomplete | Trie |
| Relationships/networks/paths | Graph |
| Group membership / connectivity | Union-Find |
| Hierarchy | Tree |
| Cache with eviction | Hash map + doubly linked list (LRU) |

---

# Part 3 — Top 100 Coding Programs

> **Format for every program:** Question → Example → Logic (simple explanation) → Pseudo code → Python code → Complexity.
> **Tip:** Don't memorize code; understand the *logic* and write the pseudo code first.

---

## A. Numbers and Logic (Programs 1–20)

### Program 1. Check whether a number is even or odd
**Question:** Given an integer, print whether it is even or odd.
**Example:** `7 → Odd`, `10 → Even`
**Logic:** An even number is divisible by 2, so the remainder `n % 2` is 0.
**Pseudo code:**
```
READ n
IF n % 2 == 0 THEN PRINT "Even"
ELSE PRINT "Odd"
```
**Python:**
```python
def even_or_odd(n):
    return "Even" if n % 2 == 0 else "Odd"
```
**Complexity:** O(1)

### Program 2. Swap two numbers (with and without a third variable)
**Question:** Swap the values of `a` and `b`.
**Example:** `a=5, b=10 → a=10, b=5`
**Logic:** Python supports tuple swapping. The classic way uses a temp variable, or arithmetic.
**Pseudo code:**
```
temp = a
a = b
b = temp
```
**Python:**
```python
a, b = 5, 10
a, b = b, a            # Pythonic

# Using arithmetic (no third variable)
a = a + b
b = a - b
a = a - b
```
**Complexity:** O(1)

### Program 3. Factorial of a number
**Question:** Find `n!` = n × (n−1) × ... × 1.
**Example:** `5 → 120`; `0! = 1`
**Logic:** Multiply all numbers from 1 to n. (Or recursively: `n! = n × (n−1)!`, base case `0! = 1`.)
**Pseudo code:**
```
result = 1
FOR i FROM 2 TO n:
    result = result * i
RETURN result
```
**Python:**
```python
def factorial(n):
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

def factorial_rec(n):
    return 1 if n <= 1 else n * factorial_rec(n - 1)
```
**Complexity:** O(n)

### Program 4. Check if a number is prime
**Question:** A prime number is greater than 1 and divisible only by 1 and itself.
**Example:** `7 → True`, `12 → False`, `1 → False`
**Logic:** If `n` has a divisor, one of them is ≤ √n. So check divisors only from 2 up to √n.
**Pseudo code:**
```
IF n < 2 RETURN False
FOR i FROM 2 WHILE i*i <= n:
    IF n % i == 0 RETURN False
RETURN True
```
**Python:**
```python
def is_prime(n):
    if n < 2:
        return False
    i = 2
    while i * i <= n:
        if n % i == 0:
            return False
        i += 1
    return True
```
**Complexity:** O(√n)

### Program 5. Print all primes up to N (Sieve of Eratosthenes)
**Question:** List all prime numbers ≤ N efficiently.
**Example:** `N=20 → [2,3,5,7,11,13,17,19]`
**Logic:** Assume all numbers are prime. For each prime `p`, mark its multiples (starting from p²) as not prime.
**Pseudo code:**
```
create array isPrime[0..N] = True
isPrime[0] = isPrime[1] = False
FOR p FROM 2 WHILE p*p <= N:
    IF isPrime[p]:
        FOR m FROM p*p TO N STEP p: isPrime[m] = False
RETURN all i where isPrime[i] is True
```
**Python:**
```python
def sieve(n):
    if n < 2:
        return []
    is_p = [True] * (n + 1)
    is_p[0] = is_p[1] = False
    for p in range(2, int(n ** 0.5) + 1):
        if is_p[p]:
            for m in range(p * p, n + 1, p):
                is_p[m] = False
    return [i for i in range(n + 1) if is_p[i]]
```
**Complexity:** O(n log log n) time, O(n) space

### Program 6. Fibonacci series
**Question:** Print the first n Fibonacci numbers (each = sum of previous two).
**Example:** `n=7 → 0 1 1 2 3 5 8`
**Logic:** Keep two variables `a, b`; in each step print `a`, then move `a, b = b, a+b`.
**Pseudo code:**
```
a = 0, b = 1
REPEAT n times:
    PRINT a
    next = a + b
    a = b
    b = next
```
**Python:**
```python
def fib_series(n):
    a, b = 0, 1
    result = []
    for _ in range(n):
        result.append(a)
        a, b = b, a + b
    return result
```
**Complexity:** O(n)

### Program 7. Palindrome number
**Question:** A number that reads the same forward and backward.
**Example:** `121 → True`, `123 → False`
**Logic:** Reverse the number and compare with the original (or compare as strings).
**Pseudo code:**
```
original = n; rev = 0
WHILE n > 0:
    rev = rev * 10 + n % 10
    n = n // 10
RETURN original == rev
```
**Python:**
```python
def is_palindrome_number(n):
    if n < 0:
        return False
    original, rev = n, 0
    while n > 0:
        rev = rev * 10 + n % 10
        n //= 10
    return original == rev
# Short: str(n) == str(n)[::-1]
```
**Complexity:** O(log n) (number of digits)

### Program 8. Reverse a number
**Question:** Reverse the digits of an integer.
**Example:** `1234 → 4321`, `-120 → -21`
**Logic:** Repeatedly take the last digit (`n % 10`), append it to `rev`, and remove it from n (`n // 10`).
**Pseudo code:**
```
sign = -1 if n < 0 else 1;  n = abs(n);  rev = 0
WHILE n > 0:
    rev = rev*10 + n%10
    n = n // 10
RETURN sign * rev
```
**Python:**
```python
def reverse_number(n):
    sign = -1 if n < 0 else 1
    n, rev = abs(n), 0
    while n > 0:
        rev = rev * 10 + n % 10
        n //= 10
    return sign * rev
```
**Complexity:** O(log n)

### Program 9. Armstrong number
**Question:** A number equal to the sum of its digits each raised to the power of the number of digits.
**Example:** `153 = 1³ + 5³ + 3³ → True`; `9474 = 9⁴+4⁴+7⁴+4⁴ → True`
**Logic:** Count digits (d), sum `digit**d` for each digit, compare with the original.
**Pseudo code:**
```
d = number of digits;  total = 0;  temp = n
WHILE temp > 0:
    total += (temp % 10) ** d
    temp = temp // 10
RETURN total == n
```
**Python:**
```python
def is_armstrong(n):
    digits = str(n)
    d = len(digits)
    return n == sum(int(ch) ** d for ch in digits)
```
**Complexity:** O(d)

### Program 10. Sum of digits
**Question:** Find the sum of all digits of a number.
**Example:** `1234 → 10`
**Logic:** Add the last digit (`n % 10`), then drop it (`n // 10`) until n is 0.
**Pseudo code:**
```
total = 0
WHILE n > 0:
    total += n % 10
    n = n // 10
RETURN total
```
**Python:**
```python
def digit_sum(n):
    n, total = abs(n), 0
    while n > 0:
        total += n % 10
        n //= 10
    return total
```
**Complexity:** O(d)

### Program 11. GCD (HCF) of two numbers
**Question:** Greatest number that divides both numbers.
**Example:** `gcd(48, 18) = 6`
**Logic:** **Euclid's algorithm:** `gcd(a, b) = gcd(b, a % b)` until b becomes 0.
**Pseudo code:**
```
WHILE b != 0:
    temp = b
    b = a % b
    a = temp
RETURN a
```
**Python:**
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a
```
**Complexity:** O(log(min(a, b)))

### Program 12. LCM of two numbers
**Question:** Smallest number divisible by both.
**Example:** `lcm(4, 6) = 12`
**Logic:** `LCM(a, b) = (a × b) / GCD(a, b)`.
**Pseudo code:**
```
RETURN (a * b) / gcd(a, b)
```
**Python:**
```python
def lcm(a, b):
    return a * b // gcd(a, b)     # gcd from Program 11
```
**Complexity:** O(log(min(a, b)))

### Program 13. Power of a number (x^n) efficiently
**Question:** Compute `x` raised to `n` without using `**`.
**Example:** `2^10 = 1024`
**Logic:** **Fast exponentiation:** if n is odd multiply the result by x; square x and halve n each time (binary exponentiation).
**Pseudo code:**
```
result = 1
WHILE n > 0:
    IF n is odd: result = result * x
    x = x * x
    n = n // 2
RETURN result
```
**Python:**
```python
def power(x, n):
    if n < 0:
        return 1 / power(x, -n)
    result = 1
    while n > 0:
        if n & 1:
            result *= x
        x *= x
        n >>= 1
    return result
```
**Complexity:** O(log n)

### Program 14. Perfect number
**Question:** A number equal to the sum of its proper divisors (excluding itself).
**Example:** `28 = 1+2+4+7+14 → True`; `6 = 1+2+3 → True`
**Logic:** Loop from 1 to n/2, add every divisor, compare with n.
**Pseudo code:**
```
total = 0
FOR i FROM 1 TO n//2:
    IF n % i == 0: total += i
RETURN total == n
```
**Python:**
```python
def is_perfect(n):
    if n < 2:
        return False
    return sum(i for i in range(1, n // 2 + 1) if n % i == 0) == n
```
**Complexity:** O(n)

### Program 15. Leap year
**Question:** Check whether a year is a leap year.
**Example:** `2000 → True`, `1900 → False`, `2024 → True`
**Logic:** Leap if divisible by 4 **and** not by 100, **or** divisible by 400.
**Pseudo code:**
```
IF (year % 400 == 0) OR (year % 4 == 0 AND year % 100 != 0)
    RETURN True
ELSE RETURN False
```
**Python:**
```python
def is_leap(year):
    return year % 400 == 0 or (year % 4 == 0 and year % 100 != 0)
```
**Complexity:** O(1)

### Program 16. Decimal to binary
**Question:** Convert a decimal number to binary.
**Example:** `10 → 1010`
**Logic:** Repeatedly divide by 2 and collect remainders; the binary number is the remainders **read in reverse.**
**Pseudo code:**
```
IF n == 0 RETURN "0"
bits = ""
WHILE n > 0:
    bits = (n % 2) + bits
    n = n // 2
RETURN bits
```
**Python:**
```python
def to_binary(n):
    if n == 0:
        return "0"
    bits = ""
    while n > 0:
        bits = str(n % 2) + bits
        n //= 2
    return bits
# Built-in: bin(10)[2:]
```
**Complexity:** O(log n)

### Program 17. Binary to decimal
**Question:** Convert a binary string to decimal.
**Example:** `"1010" → 10`
**Logic:** Scan left to right: `value = value * 2 + bit`.
**Pseudo code:**
```
value = 0
FOR each bit in binary string:
    value = value * 2 + bit
RETURN value
```
**Python:**
```python
def to_decimal(b):
    value = 0
    for bit in b:
        value = value * 2 + int(bit)
    return value
# Built-in: int("1010", 2)
```
**Complexity:** O(n)

### Program 18. Prime factors of a number
**Question:** Print all prime factors.
**Example:** `60 → [2, 2, 3, 5]`
**Logic:** Divide by 2 as long as possible, then by odd numbers up to √n. If something >1 remains, it's a prime factor.
**Pseudo code:**
```
factors = []
FOR d FROM 2 WHILE d*d <= n:
    WHILE n % d == 0:
        add d to factors
        n = n // d
IF n > 1: add n to factors
RETURN factors
```
**Python:**
```python
def prime_factors(n):
    factors, d = [], 2
    while d * d <= n:
        while n % d == 0:
            factors.append(d)
            n //= d
        d += 1
    if n > 1:
        factors.append(n)
    return factors
```
**Complexity:** O(√n)

### Program 19. FizzBuzz
**Question:** For 1 to n: print "Fizz" for multiples of 3, "Buzz" for multiples of 5, "FizzBuzz" for both, else the number.
**Example:** `15 → 1,2,Fizz,4,Buzz,Fizz,7,8,Fizz,Buzz,11,Fizz,13,14,FizzBuzz`
**Logic:** **Check the "both" case (15) first.**
**Pseudo code:**
```
FOR i FROM 1 TO n:
    IF i % 15 == 0: PRINT "FizzBuzz"
    ELSE IF i % 3 == 0: PRINT "Fizz"
    ELSE IF i % 5 == 0: PRINT "Buzz"
    ELSE PRINT i
```
**Python:**
```python
def fizzbuzz(n):
    for i in range(1, n + 1):
        if i % 15 == 0: print("FizzBuzz")
        elif i % 3 == 0: print("Fizz")
        elif i % 5 == 0: print("Buzz")
        else: print(i)
```
**Complexity:** O(n)

### Program 20. Check if a number is a power of two
**Question:** Is `n` equal to 2^k for some k?
**Example:** `16 → True`, `18 → False`
**Logic:** A power of two has exactly **one bit set**; `n & (n-1)` clears the lowest set bit, so the result is 0 only for powers of two.
**Pseudo code:**
```
RETURN n > 0 AND (n AND (n - 1)) == 0
```
**Python:**
```python
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0
```
**Complexity:** O(1)

---

## B. Strings (Programs 21–42)

### Program 21. Reverse a string
**Question:** Reverse the characters of a string.
**Example:** `"hello" → "olleh"`
**Logic:** Use slicing `[::-1]`, or two pointers swapping from both ends.
**Pseudo code:**
```
left = 0, right = length-1
WHILE left < right:
    swap chars[left], chars[right]
    left++, right--
```
**Python:**
```python
def reverse_string(s):
    return s[::-1]

def reverse_two_pointers(s):
    chars = list(s)
    l, r = 0, len(chars) - 1
    while l < r:
        chars[l], chars[r] = chars[r], chars[l]
        l += 1; r -= 1
    return "".join(chars)
```
**Complexity:** O(n)

### Program 22. Palindrome string
**Question:** Check if a string reads the same backward (ignore case, spaces, punctuation).
**Example:** `"A man, a plan, a canal: Panama" → True`
**Logic:** Clean the string (keep letters/digits, lowercase) and compare using two pointers.
**Pseudo code:**
```
clean = only alphanumeric chars of s in lowercase
l = 0, r = len(clean)-1
WHILE l < r:
    IF clean[l] != clean[r] RETURN False
    l++, r--
RETURN True
```
**Python:**
```python
def is_palindrome(s):
    clean = [c.lower() for c in s if c.isalnum()]
    l, r = 0, len(clean) - 1
    while l < r:
        if clean[l] != clean[r]:
            return False
        l += 1; r -= 1
    return True
```
**Complexity:** O(n)

### Program 23. Count vowels and consonants
**Question:** Count vowels and consonants in a string.
**Example:** `"Hello World" → vowels=3, consonants=7`
**Logic:** Loop through letters only; check membership in `"aeiou"`.
**Pseudo code:**
```
v = 0, c = 0
FOR ch in s (lowercase):
    IF ch is a letter:
        IF ch in "aeiou": v++ ELSE c++
```
**Python:**
```python
def count_vowels_consonants(s):
    v = c = 0
    for ch in s.lower():
        if ch.isalpha():
            if ch in "aeiou": v += 1
            else: c += 1
    return v, c
```
**Complexity:** O(n)

### Program 24. Check if two strings are anagrams
**Question:** Same letters with the same counts in a different order.
**Example:** `"listen", "silent" → True`
**Logic:** Compare sorted strings, or compare character counts (faster).
**Pseudo code:**
```
IF lengths differ RETURN False
count characters of s1 and s2 in two maps
RETURN map1 == map2
```
**Python:**
```python
from collections import Counter
def is_anagram(a, b):
    return Counter(a.lower()) == Counter(b.lower())
# or: sorted(a) == sorted(b)
```
**Complexity:** O(n) with Counter; O(n log n) with sorting

### Program 25. Character frequency
**Question:** Count how many times each character appears.
**Example:** `"banana" → {b:1, a:3, n:2}`
**Logic:** Use a dictionary; increase the count for each character.
**Pseudo code:**
```
freq = empty map
FOR ch in s:
    freq[ch] = freq.get(ch, 0) + 1
```
**Python:**
```python
def char_frequency(s):
    freq = {}
    for ch in s:
        freq[ch] = freq.get(ch, 0) + 1
    return freq
```
**Complexity:** O(n)

### Program 26. First non-repeating character
**Question:** Return the first character that appears only once.
**Example:** `"swiss" → "w"`; `"aabb" → None`
**Logic:** Pass 1: count frequencies. Pass 2: return the first character with count 1.
**Pseudo code:**
```
freq = count of each char
FOR ch in s (in order):
    IF freq[ch] == 1 RETURN ch
RETURN None
```
**Python:**
```python
from collections import Counter
def first_unique(s):
    freq = Counter(s)
    for ch in s:
        if freq[ch] == 1:
            return ch
    return None
```
**Complexity:** O(n)

### Program 27. Remove duplicate characters
**Question:** Remove repeated characters, keeping the first occurrence.
**Example:** `"programming" → "progamin"`
**Logic:** Track seen characters in a set; append only unseen ones.
**Pseudo code:**
```
seen = empty set; result = ""
FOR ch in s:
    IF ch not in seen: add to seen; result += ch
```
**Python:**
```python
def remove_duplicates(s):
    seen, out = set(), []
    for ch in s:
        if ch not in seen:
            seen.add(ch)
            out.append(ch)
    return "".join(out)
```
**Complexity:** O(n)

### Program 28. Longest common prefix
**Question:** Find the longest starting string common to all strings in a list.
**Example:** `["flower","flow","flight"] → "fl"`
**Logic:** Take the first string as the prefix; shorten it until every other string starts with it.
**Pseudo code:**
```
prefix = strs[0]
FOR each s in strs[1:]:
    WHILE s does not start with prefix:
        prefix = prefix without last char
        IF prefix empty RETURN ""
RETURN prefix
```
**Python:**
```python
def longest_common_prefix(strs):
    if not strs:
        return ""
    prefix = strs[0]
    for s in strs[1:]:
        while not s.startswith(prefix):
            prefix = prefix[:-1]
            if not prefix:
                return ""
    return prefix
```
**Complexity:** O(total characters)

### Program 29. Reverse the words in a sentence
**Question:** Reverse the order of words.
**Example:** `"I love Python" → "Python love I"`
**Logic:** Split into words, reverse the list, join.
**Pseudo code:**
```
words = split(s)
reverse(words)
RETURN join(words, " ")
```
**Python:**
```python
def reverse_words(s):
    return " ".join(s.split()[::-1])
```
**Complexity:** O(n)

### Program 30. Check pangram
**Question:** A sentence containing every letter a–z at least once.
**Example:** `"The quick brown fox jumps over the lazy dog" → True`
**Logic:** The set of letters in the sentence must contain all 26 letters.
**Pseudo code:**
```
letters = set of alphabetic chars in lowercase s
RETURN size(letters) == 26
```
**Python:**
```python
import string
def is_pangram(s):
    return set(string.ascii_lowercase) <= set(s.lower())
```
**Complexity:** O(n)

### Program 31. Most frequent character
**Question:** Find the character with the highest count.
**Example:** `"mississippi" → "i" or "s" (tie, 4 each)`
**Logic:** Build a frequency map, then pick the key with the maximum value.
**Pseudo code:**
```
freq = frequency map
RETURN key with maximum freq value
```
**Python:**
```python
from collections import Counter
def most_frequent(s):
    return Counter(s).most_common(1)[0][0]
```
**Complexity:** O(n)

### Program 32. Valid parentheses (balanced brackets)
**Question:** Check if brackets `() {} []` are correctly opened and closed.
**Example:** `"{[()]}" → True`, `"(]" → False`
**Logic:** Use a **stack.** Push opening brackets; for a closing bracket, the top of the stack must be its matching opening bracket.
**Pseudo code:**
```
stack = empty
FOR ch in s:
    IF ch is opening: push ch
    ELSE:
        IF stack empty OR top doesn't match ch RETURN False
        pop
RETURN stack is empty
```
**Python:**
```python
def is_valid(s):
    pairs = {")": "(", "}": "{", "]": "["}
    stack = []
    for ch in s:
        if ch in "({[":
            stack.append(ch)
        elif ch in pairs:
            if not stack or stack.pop() != pairs[ch]:
                return False
    return not stack
```
**Complexity:** O(n)

### Program 33. Longest substring without repeating characters
**Question:** Length of the longest substring with all unique characters.
**Example:** `"abcabcbb" → 3 ("abc")`
**Logic:** **Sliding window.** Keep the last index of each char. If the char repeats inside the current window, move the window start just after its previous position.
**Pseudo code:**
```
last = {}; start = 0; best = 0
FOR i, ch in s:
    IF ch in last AND last[ch] >= start: start = last[ch] + 1
    last[ch] = i
    best = max(best, i - start + 1)
RETURN best
```
**Python:**
```python
def longest_unique_substring(s):
    last, start, best = {}, 0, 0
    for i, ch in enumerate(s):
        if ch in last and last[ch] >= start:
            start = last[ch] + 1
        last[ch] = i
        best = max(best, i - start + 1)
    return best
```
**Complexity:** O(n)

### Program 34. String compression
**Question:** Compress consecutive repeated characters.
**Example:** `"aabcccc" → "a2b1c4"`
**Logic:** Walk through the string, count consecutive equal characters, write char + count.
**Pseudo code:**
```
result = ""; count = 1
FOR i FROM 1 TO len-1:
    IF s[i] == s[i-1]: count++
    ELSE: result += s[i-1] + count; count = 1
result += last char + count
```
**Python:**
```python
def compress(s):
    if not s:
        return ""
    out, count = [], 1
    for i in range(1, len(s)):
        if s[i] == s[i - 1]:
            count += 1
        else:
            out.append(s[i - 1] + str(count))
            count = 1
    out.append(s[-1] + str(count))
    return "".join(out)
```
**Complexity:** O(n)

### Program 35. Check if one string is a rotation of another
**Question:** Can `t` be formed by rotating `s`?
**Example:** `"abcde", "cdeab" → True`
**Logic:** If `t` is a rotation, it appears inside `s + s`, and the lengths must be equal.
**Pseudo code:**
```
IF len(s) != len(t) RETURN False
RETURN t is a substring of (s + s)
```
**Python:**
```python
def is_rotation(s, t):
    return len(s) == len(t) and t in s + s
```
**Complexity:** O(n)

### Program 36. Isomorphic strings
**Question:** Can characters of `s` be replaced to get `t` (one-to-one mapping)?
**Example:** `"egg","add" → True`; `"foo","bar" → False`
**Logic:** Keep two maps (s→t and t→s); the mapping must stay consistent in both directions.
**Pseudo code:**
```
FOR each pair (a, b) from s and t:
    IF a already mapped to something other than b RETURN False
    IF b already mapped from something other than a RETURN False
    record mappings
RETURN True
```
**Python:**
```python
def is_isomorphic(s, t):
    if len(s) != len(t):
        return False
    m1, m2 = {}, {}
    for a, b in zip(s, t):
        if m1.get(a, b) != b or m2.get(b, a) != a:
            return False
        m1[a], m2[b] = b, a
    return True
```
**Complexity:** O(n)

### Program 37. Longest palindromic substring
**Question:** Find the longest substring that is a palindrome.
**Example:** `"babad" → "bab"` (or "aba")
**Logic:** **Expand around center.** Every palindrome has a center (a character or between two characters). Expand while both sides match.
**Pseudo code:**
```
best = ""
FOR each index i:
    p1 = expand(i, i)        # odd length
    p2 = expand(i, i+1)      # even length
    best = longest of best, p1, p2
expand(l, r): WHILE l>=0 AND r<n AND s[l]==s[r]: l--, r++ ; RETURN s[l+1:r]
```
**Python:**
```python
def longest_palindrome(s):
    def expand(l, r):
        while l >= 0 and r < len(s) and s[l] == s[r]:
            l -= 1; r += 1
        return s[l + 1:r]
    best = ""
    for i in range(len(s)):
        best = max(best, expand(i, i), expand(i, i + 1), key=len)
    return best
```
**Complexity:** O(n²)

### Program 38. Find duplicate characters in a string
**Question:** Print characters that occur more than once.
**Example:** `"programming" → r, g, m`
**Logic:** Count frequencies; output those with count > 1.
**Pseudo code:**
```
freq = frequency map
PRINT all chars where freq[ch] > 1
```
**Python:**
```python
from collections import Counter
def duplicate_chars(s):
    return [ch for ch, c in Counter(s).items() if c > 1]
```
**Complexity:** O(n)

### Program 39. String to integer (atoi)
**Question:** Convert a numeric string to an integer without using `int()`.
**Example:** `"-123" → -123`
**Logic:** Handle the sign, then for each digit: `num = num*10 + digit` (digit = `ord(ch) - ord('0')`).
**Pseudo code:**
```
sign = +1; i = 0
IF s[0] is '-' or '+': set sign; i = 1
num = 0
FOR each char from i:
    num = num * 10 + (char - '0')
RETURN sign * num
```
**Python:**
```python
def my_atoi(s):
    s = s.strip()
    if not s:
        return 0
    sign, i, num = 1, 0, 0
    if s[0] in "+-":
        sign = -1 if s[0] == "-" else 1
        i = 1
    while i < len(s) and s[i].isdigit():
        num = num * 10 + (ord(s[i]) - ord("0"))
        i += 1
    return sign * num
```
**Complexity:** O(n)

### Program 40. Group anagrams
**Question:** Group words that are anagrams of each other.
**Example:** `["eat","tea","tan","ate","nat","bat"] → [["eat","tea","ate"],["tan","nat"],["bat"]]`
**Logic:** Anagrams have the same **sorted letters**; use the sorted word as a dictionary key.
**Pseudo code:**
```
groups = map of key → list
FOR word in words:
    key = sorted(word)
    groups[key].append(word)
RETURN values of groups
```
**Python:**
```python
from collections import defaultdict
def group_anagrams(words):
    groups = defaultdict(list)
    for w in words:
        groups["".join(sorted(w))].append(w)
    return list(groups.values())
```
**Complexity:** O(n · k log k)

### Program 41. Find a substring (implement `strStr` / `find`)
**Question:** Return the first index where `needle` appears in `haystack`, or −1.
**Example:** `"hello", "ll" → 2`
**Logic:** Slide over every starting position and compare the next `len(needle)` characters.
**Pseudo code:**
```
FOR i FROM 0 TO len(h) - len(n):
    IF h[i : i+len(n)] == n RETURN i
RETURN -1
```
**Python:**
```python
def str_str(haystack, needle):
    n, m = len(haystack), len(needle)
    for i in range(n - m + 1):
        if haystack[i:i + m] == needle:
            return i
    return -1
```
**Complexity:** O(n·m)

### Program 42. Roman numeral to integer
**Question:** Convert a Roman numeral to a number.
**Example:** `"MCMXCIV" → 1994`
**Logic:** Add each value, but **subtract** it if the next symbol is bigger (like IV = 4).
**Pseudo code:**
```
total = 0
FOR i FROM 0 TO n-1:
    IF i+1 < n AND value[s[i]] < value[s[i+1]]: total -= value[s[i]]
    ELSE total += value[s[i]]
```
**Python:**
```python
def roman_to_int(s):
    val = {"I":1,"V":5,"X":10,"L":50,"C":100,"D":500,"M":1000}
    total = 0
    for i, ch in enumerate(s):
        if i + 1 < len(s) and val[ch] < val[s[i + 1]]:
            total -= val[ch]
        else:
            total += val[ch]
    return total
```
**Complexity:** O(n)

---

## C. Arrays / Lists (Programs 43–68)

### Program 43. Find the largest and smallest element
**Question:** Find the maximum and minimum in a list without using `max()`/`min()`.
**Example:** `[4, 9, 1, 7] → max=9, min=1`
**Logic:** Assume the first element is both; update while scanning.
**Pseudo code:**
```
largest = smallest = arr[0]
FOR x in arr:
    IF x > largest: largest = x
    IF x < smallest: smallest = x
```
**Python:**
```python
def find_min_max(arr):
    largest = smallest = arr[0]
    for x in arr[1:]:
        if x > largest: largest = x
        if x < smallest: smallest = x
    return smallest, largest
```
**Complexity:** O(n)

### Program 44. Second largest element
**Question:** Find the second largest **distinct** value.
**Example:** `[10, 20, 4, 45, 99] → 45`
**Logic:** Track `first` and `second` in one pass; update when you see a bigger value.
**Pseudo code:**
```
first = second = -infinity
FOR x in arr:
    IF x > first: second = first; first = x
    ELSE IF x > second AND x != first: second = x
RETURN second
```
**Python:**
```python
def second_largest(arr):
    first = second = float("-inf")
    for x in arr:
        if x > first:
            first, second = x, first
        elif first > x > second:
            second = x
    return second if second != float("-inf") else None
```
**Complexity:** O(n)

### Program 45. Reverse an array
**Question:** Reverse a list in place.
**Example:** `[1,2,3,4] → [4,3,2,1]`
**Logic:** Swap the first and last, then move inward (two pointers).
**Pseudo code:**
```
i = 0, j = n-1
WHILE i < j: swap arr[i], arr[j]; i++, j--
```
**Python:**
```python
def reverse_array(arr):
    i, j = 0, len(arr) - 1
    while i < j:
        arr[i], arr[j] = arr[j], arr[i]
        i += 1; j -= 1
    return arr
```
**Complexity:** O(n), space O(1)

### Program 46. Remove duplicates from a list
**Question:** Remove duplicates (a) keeping order, (b) from a sorted array in place.
**Example:** `[1,2,2,3,1] → [1,2,3]`
**Logic:** (a) `dict.fromkeys` keeps first occurrences in order. (b) For a sorted array, use a **write pointer** that stores each new value.
**Pseudo code (sorted, in place):**
```
IF empty RETURN 0
w = 1
FOR r FROM 1 TO n-1:
    IF arr[r] != arr[r-1]: arr[w] = arr[r]; w++
RETURN w   # new length
```
**Python:**
```python
def remove_duplicates(arr):
    return list(dict.fromkeys(arr))

def remove_duplicates_sorted(nums):
    if not nums:
        return 0
    w = 1
    for r in range(1, len(nums)):
        if nums[r] != nums[r - 1]:
            nums[w] = nums[r]
            w += 1
    return w
```
**Complexity:** O(n)

### Program 47. Find the missing number (1 to n)
**Question:** A list contains n−1 numbers from 1..n; find the missing one.
**Example:** `[1,2,4,5] (n=5) → 3`
**Logic:** Expected sum `n(n+1)/2` minus the actual sum.
**Pseudo code:**
```
expected = n*(n+1)/2
RETURN expected - sum(arr)
```
**Python:**
```python
def missing_number(arr, n):
    return n * (n + 1) // 2 - sum(arr)
```
**Complexity:** O(n)

### Program 48. Find duplicate elements
**Question:** Print the elements that appear more than once.
**Example:** `[1,2,3,2,4,3] → [2, 3]`
**Logic:** Use a `seen` set; if an element is already seen, it is a duplicate.
**Pseudo code:**
```
seen = set(); dup = set()
FOR x in arr:
    IF x in seen: add x to dup
    ELSE add x to seen
```
**Python:**
```python
def find_duplicates(arr):
    seen, dup = set(), set()
    for x in arr:
        if x in seen:
            dup.add(x)
        seen.add(x)
    return list(dup)
```
**Complexity:** O(n)

### Program 49. Two Sum
**Question:** Return indices of two numbers that add up to a target.
**Example:** `nums=[2,7,11,15], target=9 → [0,1]`
**Logic:** For each number, check if `target - x` was seen earlier (hash map: value → index).
**Pseudo code:**
```
seen = {}
FOR i, x in nums:
    IF (target - x) in seen RETURN [seen[target - x], i]
    seen[x] = i
```
**Python:**
```python
def two_sum(nums, target):
    seen = {}
    for i, x in enumerate(nums):
        if target - x in seen:
            return [seen[target - x], i]
        seen[x] = i
    return []
```
**Complexity:** O(n)

### Program 50. Rotate an array by k positions
**Question:** Rotate the array to the right by k steps.
**Example:** `[1,2,3,4,5,6,7], k=3 → [5,6,7,1,2,3,4]`
**Logic (reversal trick):** reverse the whole array, then reverse the first k elements, then reverse the rest.
**Pseudo code:**
```
k = k % n
reverse(arr, 0, n-1)
reverse(arr, 0, k-1)
reverse(arr, k, n-1)
```
**Python:**
```python
def rotate(nums, k):
    n = len(nums)
    if n == 0:
        return nums
    k %= n
    def rev(i, j):
        while i < j:
            nums[i], nums[j] = nums[j], nums[i]
            i += 1; j -= 1
    rev(0, n - 1); rev(0, k - 1); rev(k, n - 1)
    return nums
# Short: nums[:] = nums[-k:] + nums[:-k]   (when k % n != 0)
```
**Complexity:** O(n), space O(1)

### Program 51. Move all zeros to the end
**Question:** Move zeros to the end keeping the order of non-zero elements.
**Example:** `[0,1,0,3,12] → [1,3,12,0,0]`
**Logic:** A write pointer stores each non-zero value; then fill the rest with zeros.
**Pseudo code:**
```
w = 0
FOR x in arr:
    IF x != 0: arr[w] = x; w++
WHILE w < n: arr[w] = 0; w++
```
**Python:**
```python
def move_zeros(nums):
    w = 0
    for x in nums:
        if x != 0:
            nums[w] = x
            w += 1
    for i in range(w, len(nums)):
        nums[i] = 0
    return nums
```
**Complexity:** O(n)

### Program 52. Merge two sorted arrays
**Question:** Merge two sorted lists into one sorted list.
**Example:** `[1,3,5], [2,4,6] → [1,2,3,4,5,6]`
**Logic:** Two pointers; always take the smaller current element.
**Pseudo code:**
```
i = j = 0; result = []
WHILE i < len(a) AND j < len(b):
    IF a[i] <= b[j]: append a[i]; i++
    ELSE append b[j]; j++
append the remaining elements
```
**Python:**
```python
def merge_sorted(a, b):
    i = j = 0
    res = []
    while i < len(a) and j < len(b):
        if a[i] <= b[j]:
            res.append(a[i]); i += 1
        else:
            res.append(b[j]); j += 1
    res.extend(a[i:]); res.extend(b[j:])
    return res
```
**Complexity:** O(n + m)

### Program 53. Maximum subarray sum (Kadane's algorithm)
**Question:** Find the contiguous subarray with the largest sum.
**Example:** `[-2,1,-3,4,-1,2,1,-5,4] → 6 ([4,-1,2,1])`
**Logic:** At each element, either **extend** the current subarray or **start fresh** from this element; track the best sum seen.
**Pseudo code:**
```
current = best = arr[0]
FOR x in arr[1:]:
    current = max(x, current + x)
    best = max(best, current)
RETURN best
```
**Python:**
```python
def max_subarray(nums):
    current = best = nums[0]
    for x in nums[1:]:
        current = max(x, current + x)
        best = max(best, current)
    return best
```
**Complexity:** O(n)

### Program 54. Majority element (appears more than n/2 times)
**Question:** Find the element that appears more than ⌊n/2⌋ times.
**Example:** `[2,2,1,1,2,2,3] → 2`
**Logic:** **Boyer–Moore voting:** keep a candidate and a count; same element → count+1, different → count−1; at 0 choose a new candidate.
**Pseudo code:**
```
count = 0; candidate = None
FOR x in arr:
    IF count == 0: candidate = x
    count += 1 IF x == candidate ELSE -1
RETURN candidate
```
**Python:**
```python
def majority_element(nums):
    count, candidate = 0, None
    for x in nums:
        if count == 0:
            candidate = x
        count += 1 if x == candidate else -1
    return candidate
```
**Complexity:** O(n), space O(1)

### Program 55. Intersection and union of two arrays
**Question:** Find common elements and all unique elements of two lists.
**Example:** `[1,2,3,4], [3,4,5] → intersection [3,4], union [1,2,3,4,5]`
**Logic:** Convert to sets and use set operations.
**Pseudo code:**
```
A = set(a); B = set(b)
intersection = A AND B
union = A OR B
```
**Python:**
```python
def intersection(a, b): return list(set(a) & set(b))
def union(a, b): return list(set(a) | set(b))
```
**Complexity:** O(n + m)

### Program 56. Product of array except self
**Question:** For each index, product of all other elements **without division.**
**Example:** `[1,2,3,4] → [24,12,8,6]`
**Logic:** `result[i] = (product of everything to the left) × (product of everything to the right)`; two passes.
**Pseudo code:**
```
result = [1]*n
left = 1
FOR i FROM 0 TO n-1: result[i] = left; left *= arr[i]
right = 1
FOR i FROM n-1 DOWN TO 0: result[i] *= right; right *= arr[i]
```
**Python:**
```python
def product_except_self(nums):
    n = len(nums)
    res = [1] * n
    left = 1
    for i in range(n):
        res[i] = left
        left *= nums[i]
    right = 1
    for i in range(n - 1, -1, -1):
        res[i] *= right
        right *= nums[i]
    return res
```
**Complexity:** O(n)

### Program 57. Best time to buy and sell stock
**Question:** Given daily prices, find the maximum profit from one buy then one sell.
**Example:** `[7,1,5,3,6,4] → 5 (buy at 1, sell at 6)`
**Logic:** Track the minimum price so far; profit = today's price − min price; keep the maximum.
**Pseudo code:**
```
minPrice = infinity; maxProfit = 0
FOR p in prices:
    minPrice = min(minPrice, p)
    maxProfit = max(maxProfit, p - minPrice)
```
**Python:**
```python
def max_profit(prices):
    min_price, best = float("inf"), 0
    for p in prices:
        min_price = min(min_price, p)
        best = max(best, p - min_price)
    return best
```
**Complexity:** O(n)

### Program 58. Equilibrium index
**Question:** Index where the sum of elements on the left equals the sum on the right.
**Example:** `[1,3,5,2,2] → 2 (left 1+3=4, right 2+2=4)`
**Logic:** Keep `left_sum`; right sum = `total − left_sum − arr[i]`.
**Pseudo code:**
```
total = sum(arr); left = 0
FOR i, x in arr:
    IF left == total - left - x RETURN i
    left += x
RETURN -1
```
**Python:**
```python
def equilibrium(arr):
    total, left = sum(arr), 0
    for i, x in enumerate(arr):
        if left == total - left - x:
            return i
        left += x
    return -1
```
**Complexity:** O(n)

### Program 59. Subarray with a given sum (positive numbers)
**Question:** Find a contiguous subarray whose sum equals `target` (all numbers positive).
**Example:** `[1,4,20,3,10,5], target=33 → indices 2..4`
**Logic:** **Sliding window:** expand right and add; while the sum is too big, shrink from the left.
**Pseudo code:**
```
left = 0; window = 0
FOR right FROM 0 TO n-1:
    window += arr[right]
    WHILE window > target AND left <= right: window -= arr[left]; left++
    IF window == target RETURN (left, right)
RETURN none
```
**Python:**
```python
def subarray_with_sum(arr, target):
    left = window = 0
    for right, x in enumerate(arr):
        window += x
        while window > target and left <= right:
            window -= arr[left]
            left += 1
        if window == target:
            return left, right
    return None
```
**Complexity:** O(n)

### Program 60. Sort 0s, 1s and 2s (Dutch National Flag)
**Question:** Sort an array containing only 0, 1, 2 in one pass without `sort()`.
**Example:** `[2,0,2,1,1,0] → [0,0,1,1,2,2]`
**Logic:** Three pointers: `low` (next 0 position), `mid` (current), `high` (next 2 position).
**Pseudo code:**
```
low = 0, mid = 0, high = n-1
WHILE mid <= high:
    IF arr[mid] == 0: swap(arr[low], arr[mid]); low++; mid++
    ELSE IF arr[mid] == 1: mid++
    ELSE: swap(arr[mid], arr[high]); high--
```
**Python:**
```python
def sort_colors(a):
    low, mid, high = 0, 0, len(a) - 1
    while mid <= high:
        if a[mid] == 0:
            a[low], a[mid] = a[mid], a[low]; low += 1; mid += 1
        elif a[mid] == 1:
            mid += 1
        else:
            a[mid], a[high] = a[high], a[mid]; high -= 1
    return a
```
**Complexity:** O(n)

### Program 61. Binary search
**Question:** Find the index of a target in a **sorted** array, else −1.
**Example:** `[1,3,5,7,9], 7 → 3`
**Logic:** Compare with the middle: if equal, done; if target is smaller search the left half, else the right half.
**Pseudo code:**
```
low = 0, high = n-1
WHILE low <= high:
    mid = (low + high) // 2
    IF arr[mid] == target RETURN mid
    ELSE IF arr[mid] < target: low = mid + 1
    ELSE high = mid - 1
RETURN -1
```
**Python:**
```python
def binary_search(arr, target):
    low, high = 0, len(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid
        if arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return -1
```
**Complexity:** O(log n)

### Program 62. Merge overlapping intervals
**Question:** Merge all overlapping intervals.
**Example:** `[[1,3],[2,6],[8,10],[15,18]] → [[1,6],[8,10],[15,18]]`
**Logic:** Sort by start; if the current interval starts before the last merged one ends, extend the end; otherwise add it.
**Pseudo code:**
```
sort intervals by start
merged = [first]
FOR each (s, e) after first:
    IF s <= merged.last.end: merged.last.end = max(merged.last.end, e)
    ELSE append (s, e)
```
**Python:**
```python
def merge_intervals(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for s, e in intervals[1:]:
        if s <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], e)
        else:
            merged.append([s, e])
    return merged
```
**Complexity:** O(n log n)

### Program 63. Flatten a nested list
**Question:** Flatten a list containing lists at any depth.
**Example:** `[1,[2,[3,4]],5] → [1,2,3,4,5]`
**Logic:** Recursion: if an item is a list, flatten it; else add the item.
**Pseudo code:**
```
FOR item in list:
    IF item is a list: result += flatten(item)
    ELSE result.append(item)
```
**Python:**
```python
def flatten(lst):
    result = []
    for item in lst:
        if isinstance(item, list):
            result.extend(flatten(item))
        else:
            result.append(item)
    return result
```
**Complexity:** O(total elements)

### Program 64. Transpose a matrix
**Question:** Swap rows and columns.
**Example:** `[[1,2,3],[4,5,6]] → [[1,4],[2,5],[3,6]]`
**Logic:** Element `[i][j]` moves to `[j][i]`. In Python, `zip(*matrix)` does it.
**Pseudo code:**
```
result = new matrix (cols x rows)
FOR i, FOR j: result[j][i] = matrix[i][j]
```
**Python:**
```python
def transpose(m):
    return [list(row) for row in zip(*m)]
```
**Complexity:** O(r·c)

### Program 65. Rotate a matrix by 90° (clockwise)
**Question:** Rotate an N×N matrix clockwise.
**Example:** `[[1,2],[3,4]] → [[3,1],[4,2]]`
**Logic:** **Transpose, then reverse each row.**
**Pseudo code:**
```
transpose(matrix)
FOR each row: reverse(row)
```
**Python:**
```python
def rotate_matrix(m):
    return [list(row)[::-1] for row in zip(*m)]
```
**Complexity:** O(n²)

### Program 66. Spiral traversal of a matrix
**Question:** Print matrix elements in spiral order.
**Example:** `[[1,2,3],[4,5,6],[7,8,9]] → 1 2 3 6 9 8 7 4 5`
**Logic:** Keep four boundaries (top, bottom, left, right); walk right, down, left, up and shrink the boundaries.
**Pseudo code:**
```
WHILE top <= bottom AND left <= right:
    traverse top row (left→right); top++
    traverse right column (top→bottom); right--
    IF top <= bottom: traverse bottom row (right→left); bottom--
    IF left <= right: traverse left column (bottom→top); left++
```
**Python:**
```python
def spiral(m):
    res = []
    top, bottom, left, right = 0, len(m) - 1, 0, len(m[0]) - 1
    while top <= bottom and left <= right:
        for j in range(left, right + 1): res.append(m[top][j])
        top += 1
        for i in range(top, bottom + 1): res.append(m[i][right])
        right -= 1
        if top <= bottom:
            for j in range(right, left - 1, -1): res.append(m[bottom][j])
            bottom -= 1
        if left <= right:
            for i in range(bottom, top - 1, -1): res.append(m[i][left])
            left += 1
    return res
```
**Complexity:** O(r·c)

### Program 67. Longest consecutive sequence
**Question:** Length of the longest run of consecutive integers (unsorted input).
**Example:** `[100,4,200,1,3,2] → 4 (1,2,3,4)`
**Logic:** Put numbers in a set. Start counting only from numbers that have **no predecessor** (`x-1` not in set) and extend upward.
**Pseudo code:**
```
s = set(nums); best = 0
FOR x in s:
    IF x-1 not in s:
        y = x
        WHILE y+1 in s: y++
        best = max(best, y - x + 1)
```
**Python:**
```python
def longest_consecutive(nums):
    s, best = set(nums), 0
    for x in s:
        if x - 1 not in s:
            y = x
            while y + 1 in s:
                y += 1
            best = max(best, y - x + 1)
    return best
```
**Complexity:** O(n)

### Program 68. Frequency of elements in a list
**Question:** Count how many times each element occurs.
**Example:** `[1,2,2,3,3,3] → {1:1, 2:2, 3:3}`
**Logic:** Dictionary counting (or `Counter`).
**Pseudo code:**
```
freq = {}
FOR x in arr: freq[x] = freq.get(x, 0) + 1
```
**Python:**
```python
from collections import Counter
def frequency(arr):
    return dict(Counter(arr))
```
**Complexity:** O(n)

---

## D. Sorting (Programs 69–73)

### Program 69. Bubble sort
**Question:** Sort a list by repeatedly swapping adjacent elements that are out of order.
**Example:** `[5,1,4,2] → [1,2,4,5]`
**Logic:** In each pass the largest remaining element "bubbles" to the end. If a pass makes no swaps, the list is sorted (early stop).
**Pseudo code:**
```
FOR i FROM 0 TO n-2:
    swapped = False
    FOR j FROM 0 TO n-i-2:
        IF arr[j] > arr[j+1]: swap; swapped = True
    IF NOT swapped BREAK
```
**Python:**
```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n - 1):
        swapped = False
        for j in range(n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        if not swapped:
            break
    return arr
```
**Complexity:** O(n²) worst, O(n) best

### Program 70. Selection sort
**Question:** Sort by repeatedly selecting the minimum and placing it at the front.
**Example:** `[64,25,12,22] → [12,22,25,64]`
**Logic:** For each position i, find the smallest element in the unsorted part and swap it into position i.
**Pseudo code:**
```
FOR i FROM 0 TO n-2:
    minIndex = i
    FOR j FROM i+1 TO n-1: IF arr[j] < arr[minIndex]: minIndex = j
    swap arr[i], arr[minIndex]
```
**Python:**
```python
def selection_sort(arr):
    n = len(arr)
    for i in range(n - 1):
        m = i
        for j in range(i + 1, n):
            if arr[j] < arr[m]:
                m = j
        arr[i], arr[m] = arr[m], arr[i]
    return arr
```
**Complexity:** O(n²)

### Program 71. Insertion sort
**Question:** Build the sorted list one element at a time.
**Example:** `[12,11,13,5] → [5,11,12,13]`
**Logic:** Take the next element (key) and shift larger elements right to insert it at the correct place — like sorting cards in hand.
**Pseudo code:**
```
FOR i FROM 1 TO n-1:
    key = arr[i]; j = i-1
    WHILE j >= 0 AND arr[j] > key: arr[j+1] = arr[j]; j--
    arr[j+1] = key
```
**Python:**
```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key, j = arr[i], i - 1
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
    return arr
```
**Complexity:** O(n²) worst, O(n) best (nearly sorted)

### Program 72. Merge sort
**Question:** Sort using divide and conquer.
**Example:** `[38,27,43,3,9,82,10] → [3,9,10,27,38,43,82]`
**Logic:** Split into two halves, sort each recursively, then **merge** the two sorted halves (Program 52).
**Pseudo code:**
```
mergeSort(arr):
    IF length <= 1 RETURN arr
    mid = length // 2
    left = mergeSort(arr[:mid]); right = mergeSort(arr[mid:])
    RETURN merge(left, right)
```
**Python:**
```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left, right = merge_sort(arr[:mid]), merge_sort(arr[mid:])
    res, i, j = [], 0, 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            res.append(left[i]); i += 1
        else:
            res.append(right[j]); j += 1
    return res + left[i:] + right[j:]
```
**Complexity:** O(n log n), space O(n), stable

### Program 73. Quick sort
**Question:** Sort using a pivot and partitioning.
**Example:** `[10,7,8,9,1,5] → [1,5,7,8,9,10]`
**Logic:** Choose a pivot; put smaller elements on the left, equal in the middle, larger on the right; sort left and right recursively.
**Pseudo code:**
```
quickSort(arr):
    IF length <= 1 RETURN arr
    pivot = middle element
    left = items < pivot; mid = items == pivot; right = items > pivot
    RETURN quickSort(left) + mid + quickSort(right)
```
**Python:**
```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    mid = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quick_sort(left) + mid + quick_sort(right)
```
**Complexity:** O(n log n) average, O(n²) worst (this version uses O(n) extra space; the in-place version uses O(log n))

---

## E. Linked List (Programs 74–79)

> **Node class used in all linked list programs:**
```python
class Node:
    def __init__(self, data):
        self.data = data
        self.next = None
```

### Program 74. Create a linked list and traverse it
**Question:** Build a singly linked list (insert at end) and print it.
**Example:** `insert 1,2,3 → 1 -> 2 -> 3 -> None`
**Logic:** Keep a `head`. To append, walk to the last node (where `next` is None) and attach the new node.
**Pseudo code:**
```
append(data):
    new = Node(data)
    IF head is None: head = new; RETURN
    cur = head
    WHILE cur.next: cur = cur.next
    cur.next = new
```
**Python:**
```python
class LinkedList:
    def __init__(self):
        self.head = None
    def append(self, data):
        new = Node(data)
        if not self.head:
            self.head = new
            return
        cur = self.head
        while cur.next:
            cur = cur.next
        cur.next = new
    def display(self):
        cur, out = self.head, []
        while cur:
            out.append(str(cur.data)); cur = cur.next
        print(" -> ".join(out) + " -> None")
```
**Complexity:** append O(n), traverse O(n)

### Program 75. Reverse a linked list
**Question:** Reverse the direction of all links.
**Example:** `1->2->3 → 3->2->1`
**Logic:** Use three pointers `prev, cur, next`; at each node point `cur.next` backward to `prev`.
**Pseudo code:**
```
prev = None; cur = head
WHILE cur:
    nxt = cur.next
    cur.next = prev
    prev = cur
    cur = nxt
RETURN prev   # new head
```
**Python:**
```python
def reverse_list(head):
    prev, cur = None, head
    while cur:
        nxt = cur.next
        cur.next = prev
        prev, cur = cur, nxt
    return prev
```
**Complexity:** O(n), space O(1)

### Program 76. Detect a cycle in a linked list
**Question:** Does the list contain a loop?
**Example:** `1->2->3->back to 2 → True`
**Logic:** **Floyd's Tortoise and Hare:** slow moves 1 step, fast moves 2. If there is a cycle they meet; otherwise fast reaches the end.
**Pseudo code:**
```
slow = fast = head
WHILE fast AND fast.next:
    slow = slow.next; fast = fast.next.next
    IF slow == fast RETURN True
RETURN False
```
**Python:**
```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow, fast = slow.next, fast.next.next
        if slow is fast:
            return True
    return False
```
**Complexity:** O(n), space O(1)

### Program 77. Find the middle of a linked list
**Question:** Return the middle node.
**Example:** `1->2->3->4->5 → 3`; `1->2->3->4 → 3 (second middle)`
**Logic:** Fast pointer moves twice as fast; when it reaches the end, slow is at the middle.
**Pseudo code:**
```
slow = fast = head
WHILE fast AND fast.next: slow = slow.next; fast = fast.next.next
RETURN slow
```
**Python:**
```python
def middle(head):
    slow = fast = head
    while fast and fast.next:
        slow, fast = slow.next, fast.next.next
    return slow
```
**Complexity:** O(n)

### Program 78. Merge two sorted linked lists
**Question:** Merge two sorted lists into one sorted list.
**Example:** `1->3->5 and 2->4->6 → 1->2->3->4->5->6`
**Logic:** Use a dummy head; attach the smaller node each time, then attach the leftover list.
**Pseudo code:**
```
dummy = Node(0); tail = dummy
WHILE a AND b:
    IF a.data <= b.data: tail.next = a; a = a.next
    ELSE tail.next = b; b = b.next
    tail = tail.next
tail.next = a OR b
RETURN dummy.next
```
**Python:**
```python
def merge_lists(a, b):
    dummy = tail = Node(0)
    while a and b:
        if a.data <= b.data:
            tail.next, a = a, a.next
        else:
            tail.next, b = b, b.next
        tail = tail.next
    tail.next = a or b
    return dummy.next
```
**Complexity:** O(n + m)

### Program 79. Remove the Nth node from the end
**Question:** Delete the nth node counting from the end, in one pass.
**Example:** `1->2->3->4->5, n=2 → 1->2->3->5`
**Logic:** Two pointers: move `fast` n steps ahead, then move both until `fast` reaches the end; `slow` is just before the node to delete.
**Pseudo code:**
```
dummy -> head; slow = fast = dummy
MOVE fast n+1 steps
WHILE fast: slow = slow.next; fast = fast.next
slow.next = slow.next.next
RETURN dummy.next
```
**Python:**
```python
def remove_nth_from_end(head, n):
    dummy = Node(0)
    dummy.next = head
    slow = fast = dummy
    for _ in range(n + 1):
        fast = fast.next
    while fast:
        slow, fast = slow.next, fast.next
    slow.next = slow.next.next
    return dummy.next
```
**Complexity:** O(n)

---

## F. Stack and Queue (Programs 80–84)

### Program 80. Implement a stack
**Question:** Implement `push`, `pop`, `peek`, `is_empty`.
**Example:** `push 1,2,3; pop → 3`
**Logic:** A Python list works as a stack (end of list = top).
**Pseudo code:**
```
push(x): append x to list
pop(): IF empty ERROR ELSE remove and return last
peek(): return last
```
**Python:**
```python
class Stack:
    def __init__(self): self.items = []
    def push(self, x): self.items.append(x)
    def pop(self):
        if self.is_empty(): raise IndexError("pop from empty stack")
        return self.items.pop()
    def peek(self): return self.items[-1] if self.items else None
    def is_empty(self): return not self.items
    def size(self): return len(self.items)
```
**Complexity:** all operations O(1)

### Program 81. Implement a queue using two stacks
**Question:** Build FIFO behavior using only stack operations.
**Example:** `enqueue 1,2,3; dequeue → 1`
**Logic:** Push into `in_stack`. For dequeue, if `out_stack` is empty, move everything from `in_stack` to `out_stack` (this reverses the order), then pop.
**Pseudo code:**
```
enqueue(x): push x to inStack
dequeue():
    IF outStack empty: WHILE inStack not empty: push(pop(inStack)) onto outStack
    RETURN pop(outStack)
```
**Python:**
```python
class MyQueue:
    def __init__(self):
        self.in_stack, self.out_stack = [], []
    def enqueue(self, x):
        self.in_stack.append(x)
    def dequeue(self):
        if not self.out_stack:
            while self.in_stack:
                self.out_stack.append(self.in_stack.pop())
        return self.out_stack.pop() if self.out_stack else None
```
**Complexity:** O(1) amortized

### Program 82. Min Stack (get minimum in O(1))
**Question:** A stack that supports `push`, `pop`, `top` and `get_min` in O(1).
**Example:** `push 3,5,2 → get_min = 2; pop → get_min = 3`
**Logic:** Store each value together with the **minimum so far** as a pair.
**Pseudo code:**
```
push(x): minSoFar = min(x, top.min if stack else x); push (x, minSoFar)
get_min(): RETURN top.min
```
**Python:**
```python
class MinStack:
    def __init__(self): self.stack = []
    def push(self, x):
        m = min(x, self.stack[-1][1]) if self.stack else x
        self.stack.append((x, m))
    def pop(self): return self.stack.pop()[0]
    def top(self): return self.stack[-1][0]
    def get_min(self): return self.stack[-1][1]
```
**Complexity:** O(1) for all operations

### Program 83. Next greater element
**Question:** For each element, find the next element to its right that is greater; else −1.
**Example:** `[4,5,2,25] → [5,25,25,-1]`
**Logic:** **Monotonic stack** of indices waiting for a greater value. When a new element is larger than the stack top, it is the answer for that top.
**Pseudo code:**
```
result = [-1]*n; stack = []
FOR i, x in arr:
    WHILE stack AND arr[stack.top] < x: result[stack.pop()] = x
    push i
```
**Python:**
```python
def next_greater(nums):
    res = [-1] * len(nums)
    stack = []
    for i, x in enumerate(nums):
        while stack and nums[stack[-1]] < x:
            res[stack.pop()] = x
        stack.append(i)
    return res
```
**Complexity:** O(n)

### Program 84. LRU Cache
**Question:** Design a cache of fixed capacity with `get` and `put` in O(1); evict the **least recently used** item when full.
**Example:** `capacity=2: put(1,1), put(2,2), get(1)→1, put(3,3) evicts key 2, get(2)→-1`
**Logic:** `OrderedDict` keeps the order of use. On every access, move the key to the end (most recent). When over capacity, remove the first item (least recent).
**Pseudo code:**
```
get(key): IF missing RETURN -1; move key to end; RETURN value
put(key, val): set key; move to end; IF size > capacity: remove first (oldest) item
```
**Python:**
```python
from collections import OrderedDict
class LRUCache:
    def __init__(self, capacity):
        self.cap = capacity
        self.cache = OrderedDict()
    def get(self, key):
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]
    def put(self, key, value):
        self.cache[key] = value
        self.cache.move_to_end(key)
        if len(self.cache) > self.cap:
            self.cache.popitem(last=False)
```
**Complexity:** O(1) per operation

---

## G. Trees (Programs 85–89)

> **TreeNode class used in all tree programs:**
```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
```

### Program 85. Tree traversals (inorder, preorder, postorder)
**Question:** Print the nodes of a binary tree in each order.
**Example:** tree `1(2,3)` → inorder `2 1 3`, preorder `1 2 3`, postorder `2 3 1`
**Logic:** Recursion; only the position of "visit root" changes: **Pre** (root first), **In** (root middle), **Post** (root last).
**Pseudo code:**
```
inorder(node):  IF node: inorder(left); visit(node); inorder(right)
preorder(node): IF node: visit(node); preorder(left); preorder(right)
postorder(node):IF node: postorder(left); postorder(right); visit(node)
```
**Python:**
```python
def inorder(root):
    return inorder(root.left) + [root.val] + inorder(root.right) if root else []
def preorder(root):
    return [root.val] + preorder(root.left) + preorder(root.right) if root else []
def postorder(root):
    return postorder(root.left) + postorder(root.right) + [root.val] if root else []
```
**Complexity:** O(n)

### Program 86. Level order traversal (BFS)
**Question:** Return values level by level.
**Example:** `3(9, 20(15, 7)) → [[3],[9,20],[15,7]]`
**Logic:** Use a queue; process all nodes currently in the queue (one level), adding their children.
**Pseudo code:**
```
queue = [root]
WHILE queue:
    level = []
    REPEAT size(queue) times:
        node = dequeue; level.append(node.val)
        enqueue node.left, node.right if exist
    result.append(level)
```
**Python:**
```python
from collections import deque
def level_order(root):
    if not root:
        return []
    res, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(level)
    return res
```
**Complexity:** O(n)

### Program 87. Height (maximum depth) of a binary tree
**Question:** Number of nodes on the longest root-to-leaf path.
**Example:** `3(9, 20(15, 7)) → 3`
**Logic:** `height = 1 + max(height(left), height(right))`; empty tree = 0.
**Pseudo code:**
```
height(node):
    IF node is None RETURN 0
    RETURN 1 + max(height(left), height(right))
```
**Python:**
```python
def height(root):
    if not root:
        return 0
    return 1 + max(height(root.left), height(root.right))
```
**Complexity:** O(n)

### Program 88. BST: insert, search and validate
**Question:** Implement insert/search for a BST and check if a tree is a valid BST.
**Example:** insert `5,3,7` → search `7 → True`
**Logic:** Smaller values go left, larger go right. To validate, every node must stay within a `(low, high)` range inherited from its ancestors.
**Pseudo code:**
```
insert(node, v): IF None RETURN new; IF v < node.val: node.left = insert(...) ELSE node.right = insert(...)
search(node, v):  IF None RETURN False; IF equal RETURN True; go left or right
valid(node, low, high): IF None RETURN True; IF not (low < val < high) RETURN False;
                        RETURN valid(left, low, val) AND valid(right, val, high)
```
**Python:**
```python
def insert(root, v):
    if not root:
        return TreeNode(v)
    if v < root.val:
        root.left = insert(root.left, v)
    else:
        root.right = insert(root.right, v)
    return root

def search(root, v):
    if not root:
        return False
    if v == root.val:
        return True
    return search(root.left, v) if v < root.val else search(root.right, v)

def is_valid_bst(root, low=float("-inf"), high=float("inf")):
    if not root:
        return True
    if not (low < root.val < high):
        return False
    return is_valid_bst(root.left, low, root.val) and is_valid_bst(root.right, root.val, high)
```
**Complexity:** insert/search O(h) (h = height; O(log n) balanced), validate O(n)

### Program 89. Invert (mirror) a binary tree
**Question:** Swap left and right children at every node.
**Example:** `4(2,7) → 4(7,2)`
**Logic:** Recursively invert both subtrees and swap them.
**Pseudo code:**
```
invert(node):
    IF None RETURN None
    swap node.left, node.right
    invert(node.left); invert(node.right)
    RETURN node
```
**Python:**
```python
def invert_tree(root):
    if not root:
        return None
    root.left, root.right = invert_tree(root.right), invert_tree(root.left)
    return root
```
**Complexity:** O(n)

---

## H. Graphs (Programs 90–91)

### Program 90. BFS and DFS of a graph
**Question:** Traverse a graph (adjacency list) from a start node.
**Example:** `{A:[B,C], B:[D], C:[D], D:[]}, start A → BFS: A B C D ; DFS: A B D C`
**Logic:** BFS uses a **queue** (level by level). DFS uses **recursion/stack** (deep first). Keep a `visited` set to avoid infinite loops.
**Pseudo code:**
```
BFS: queue=[start]; visited={start}
     WHILE queue: node = dequeue; visit; FOR each neighbor not visited: mark visited; enqueue
DFS: dfs(node): mark visited; visit; FOR each neighbor not visited: dfs(neighbor)
```
**Python:**
```python
from collections import deque

def bfs(graph, start):
    visited, order, q = {start}, [], deque([start])
    while q:
        node = q.popleft()
        order.append(node)
        for nb in graph[node]:
            if nb not in visited:
                visited.add(nb)
                q.append(nb)
    return order

def dfs(graph, node, visited=None, order=None):
    if visited is None: visited, order = set(), []
    visited.add(node); order.append(node)
    for nb in graph[node]:
        if nb not in visited:
            dfs(graph, nb, visited, order)
    return order
```
**Complexity:** O(V + E)

### Program 91. Number of islands
**Question:** In a grid of `1` (land) and `0` (water), count connected groups of land (up/down/left/right).
**Example:** grid `[[1,1,0],[0,1,0],[0,0,1]] → 2`
**Logic:** Scan every cell; when you find unvisited land, count one island and use DFS to "sink" all connected land (mark as 0).
**Pseudo code:**
```
count = 0
FOR each cell (r, c):
    IF grid[r][c] == 1: count++; dfs(r, c)
dfs(r, c): IF out of bounds OR grid[r][c] == 0 RETURN
           grid[r][c] = 0; dfs in 4 directions
```
**Python:**
```python
def num_islands(grid):
    rows, cols = len(grid), len(grid[0])
    def dfs(r, c):
        if r < 0 or c < 0 or r >= rows or c >= cols or grid[r][c] != 1:
            return
        grid[r][c] = 0
        dfs(r + 1, c); dfs(r - 1, c); dfs(r, c + 1); dfs(r, c - 1)
    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 1:
                count += 1
                dfs(r, c)
    return count
```
**Complexity:** O(rows × cols)

---

## I. Recursion, Backtracking and Dynamic Programming (Programs 92–100)

### Program 92. Fibonacci with memoization (DP)
**Question:** Compute the nth Fibonacci number efficiently.
**Example:** `fib(10) = 55`
**Logic:** Plain recursion repeats the same calls (O(2ⁿ)). Store results in a cache (memoization) → O(n).
**Pseudo code:**
```
fib(n): IF n < 2 RETURN n
        IF n in memo RETURN memo[n]
        memo[n] = fib(n-1) + fib(n-2); RETURN memo[n]
```
**Python:**
```python
from functools import lru_cache
@lru_cache(maxsize=None)
def fib(n):
    return n if n < 2 else fib(n - 1) + fib(n - 2)

def fib_iter(n):                # O(1) space
    a, b = 0, 1
    for _ in range(n):
        a, b = b, a + b
    return a
```
**Complexity:** O(n)

### Program 93. Climbing stairs
**Question:** You can climb 1 or 2 steps at a time. In how many ways can you reach step n?
**Example:** `n=4 → 5`
**Logic:** To reach step n you come from n−1 or n−2: `ways(n) = ways(n-1) + ways(n-2)` — a Fibonacci pattern.
**Pseudo code:**
```
a = 1 (ways to step 0), b = 1 (ways to step 1)
REPEAT n-1 times: a, b = b, a + b
RETURN b
```
**Python:**
```python
def climb_stairs(n):
    a, b = 1, 1
    for _ in range(n - 1):
        a, b = b, a + b
    return b
```
**Complexity:** O(n)

### Program 94. Coin change (minimum coins)
**Question:** Fewest coins needed to make an amount; −1 if impossible.
**Example:** `coins=[1,2,5], amount=11 → 3 (5+5+1)`
**Logic:** `dp[a]` = min coins for amount a. For each coin c ≤ a: `dp[a] = min(dp[a], dp[a-c] + 1)`.
**Pseudo code:**
```
dp[0] = 0; dp[1..amount] = infinity
FOR a FROM 1 TO amount:
    FOR each coin c: IF c <= a: dp[a] = min(dp[a], dp[a-c] + 1)
RETURN dp[amount] if finite else -1
```
**Python:**
```python
def coin_change(coins, amount):
    INF = float("inf")
    dp = [0] + [INF] * amount
    for a in range(1, amount + 1):
        for c in coins:
            if c <= a:
                dp[a] = min(dp[a], dp[a - c] + 1)
    return dp[amount] if dp[amount] != INF else -1
```
**Complexity:** O(amount × coins)

### Program 95. 0/1 Knapsack
**Question:** Given item weights and values and a capacity, maximize total value (each item used at most once).
**Example:** `weights=[1,3,4,5], values=[1,4,5,7], W=7 → 9`
**Logic:** `dp[w]` = best value with capacity w. For each item, update capacities from **high to low** so an item is not reused.
**Pseudo code:**
```
dp[0..W] = 0
FOR each item (wt, val):
    FOR w FROM W DOWN TO wt: dp[w] = max(dp[w], dp[w - wt] + val)
RETURN dp[W]
```
**Python:**
```python
def knapsack(weights, values, W):
    dp = [0] * (W + 1)
    for wt, val in zip(weights, values):
        for w in range(W, wt - 1, -1):
            dp[w] = max(dp[w], dp[w - wt] + val)
    return dp[W]
```
**Complexity:** O(n × W)

### Program 96. Longest Common Subsequence (LCS)
**Question:** Length of the longest subsequence (not necessarily contiguous) common to two strings.
**Example:** `"abcde", "ace" → 3 ("ace")`
**Logic:** `dp[i][j]` = LCS of first i chars of A and first j chars of B. If chars match: `1 + dp[i-1][j-1]`, else `max(dp[i-1][j], dp[i][j-1])`.
**Pseudo code:**
```
FOR i FROM 1 TO m: FOR j FROM 1 TO n:
    IF a[i-1] == b[j-1]: dp[i][j] = dp[i-1][j-1] + 1
    ELSE dp[i][j] = max(dp[i-1][j], dp[i][j-1])
RETURN dp[m][n]
```
**Python:**
```python
def lcs(a, b):
    m, n = len(a), len(b)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if a[i - 1] == b[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    return dp[m][n]
```
**Complexity:** O(m × n)

### Program 97. Longest Increasing Subsequence (LIS)
**Question:** Length of the longest strictly increasing subsequence.
**Example:** `[10,9,2,5,3,7,101,18] → 4 (2,3,7,18)`
**Logic:** `dp[i]` = LIS ending at i = 1 + max(dp[j]) for all j < i with `arr[j] < arr[i]`. (A faster O(n log n) method uses `bisect`.)
**Pseudo code:**
```
dp = [1]*n
FOR i FROM 1 TO n-1: FOR j FROM 0 TO i-1:
    IF arr[j] < arr[i]: dp[i] = max(dp[i], dp[j] + 1)
RETURN max(dp)
```
**Python:**
```python
def lis(nums):
    if not nums:
        return 0
    dp = [1] * len(nums)
    for i in range(1, len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)

# O(n log n)
from bisect import bisect_left
def lis_fast(nums):
    tails = []
    for x in nums:
        i = bisect_left(tails, x)
        if i == len(tails): tails.append(x)
        else: tails[i] = x
    return len(tails)
```
**Complexity:** O(n²), or O(n log n) with bisect

### Program 98. Tower of Hanoi
**Question:** Move n disks from rod A to rod C using rod B, one disk at a time, never placing a larger disk on a smaller one.
**Example:** `n=2 → A→B, A→C, B→C`
**Logic:** Move n−1 disks to the helper rod, move the biggest disk to the target, then move the n−1 disks onto it.
**Pseudo code:**
```
hanoi(n, source, target, helper):
    IF n == 0 RETURN
    hanoi(n-1, source, helper, target)
    PRINT "move disk n from source to target"
    hanoi(n-1, helper, target, source)
```
**Python:**
```python
def hanoi(n, src="A", dst="C", helper="B"):
    if n == 0:
        return
    hanoi(n - 1, src, helper, dst)
    print(f"Move disk {n} from {src} to {dst}")
    hanoi(n - 1, helper, dst, src)
```
**Complexity:** O(2ⁿ) — minimum moves = 2ⁿ − 1

### Program 99. Generate all permutations
**Question:** Print all arrangements of the elements.
**Example:** `[1,2,3] → [1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]`
**Logic:** **Backtracking:** choose an unused element, add it to the path, recurse, then remove it (undo) and try the next.
**Pseudo code:**
```
permute(path, used):
    IF len(path) == n: save a copy of path; RETURN
    FOR i FROM 0 TO n-1:
        IF NOT used[i]:
            used[i] = True; path.append(nums[i])
            permute(path, used)
            path.pop(); used[i] = False
```
**Python:**
```python
def permutations(nums):
    res, used = [], [False] * len(nums)
    def backtrack(path):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
                path.append(nums[i])
                backtrack(path)
                path.pop()
                used[i] = False
    backtrack([])
    return res
```
**Complexity:** O(n · n!)

### Program 100. Generate all subsets (power set)
**Question:** Return every possible subset of a list.
**Example:** `[1,2,3] → [],[1],[2],[3],[1,2],[1,3],[2,3],[1,2,3]`
**Logic:** For each element you have two choices: **include** it or **skip** it (backtracking). Total subsets = 2ⁿ.
**Pseudo code:**
```
subsets(start, path):
    save a copy of path
    FOR i FROM start TO n-1:
        path.append(nums[i])
        subsets(i+1, path)
        path.pop()
```
**Python:**
```python
def subsets(nums):
    res = []
    def backtrack(start, path):
        res.append(path[:])
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    backtrack(0, [])
    return res
```
**Complexity:** O(n · 2ⁿ)

---

# Part 4 — Interview Strategy and Cheat Sheet

### How to recognise the pattern (the fastest way to crack coding rounds)
| If the problem says... | Think of... | Programs to review |
|---|---|---|
| Find pair/target sum, duplicates, count frequency | **Hash map / set** | 24–26, 38, 48, 49, 67, 68 |
| Sorted array, pairs, palindromes, merge | **Two pointers** | 21, 22, 45, 52 |
| Longest/shortest substring or subarray, window of size k | **Sliding window** | 33, 53, 57, 59 |
| Sorted data, "find in O(log n)" | **Binary search** | 61 |
| Brackets, undo, next greater, nested structure | **Stack** | 32, 82, 83 |
| Level by level, shortest path (unweighted) | **BFS / Queue** | 86, 90, 91 |
| All combinations/permutations/subsets, puzzles | **Backtracking** | 99, 100 |
| Overlapping subproblems, "min/max/ways/count" | **Dynamic programming** | 92–97 |
| Linked list loops/middle/Nth from end | **Fast & slow pointers** | 76, 77, 79 |
| Intervals, ordering | **Sort first** | 62 |
| Cache, "most recent" | **Hash map + linked list** | 84 |
| Tree problems | **Recursion (DFS)** | 85–89 |

### Python one-liners that save time
```python
s[::-1]                               # reverse string/list
sorted(d.items(), key=lambda x: x[1]) # sort dict by value
Counter(arr).most_common(k)           # top-k frequent
max(arr, key=len)                     # longest string
list(dict.fromkeys(arr))              # remove duplicates, keep order
sum(1 for x in arr if x > 5)          # count with condition
any(x > 5 for x in arr); all(...)     # check conditions
[list(r) for r in zip(*matrix)]       # transpose
divmod(17, 5)                         # (3, 2)
from itertools import permutations, combinations
import heapq; heapq.nlargest(3, arr)  # top 3
```

### Complexity cheat sheet
| Data structure | Access | Search | Insert | Delete |
|---|---|---|---|---|
| Array / list | O(1) | O(n) | O(n) | O(n) |
| Linked list | O(n) | O(n) | O(1)* | O(1)* |
| Stack / Queue | O(n) | O(n) | O(1) | O(1) |
| Hash table / dict / set | – | O(1) avg | O(1) avg | O(1) avg |
| BST (balanced) | – | O(log n) | O(log n) | O(log n) |
| Heap | – | O(n) | O(log n) | O(log n) |
*when the node/position is already known.

### Top 25 must-prepare programs (if time is short)
Prime (4), Fibonacci (6), Palindrome number/string (7, 22), Reverse string/number (8, 21), Factorial (3), Armstrong (9), GCD (11), FizzBuzz (19), Anagram (24), First non-repeating char (26), Valid parentheses (32), Longest substring without repeating (33), Second largest (44), Two Sum (49), Move zeros (51), Kadane (53), Binary search (61), Bubble/Merge/Quick sort (69, 72, 73), Reverse linked list (75), Cycle detection (76), Tree traversals + height (85, 87), BFS/DFS (90), Climbing stairs/Coin change (93, 94), Permutations/Subsets (99, 100).

### Interview day tips
- **Clarify first:** input size, duplicates, negative numbers, empty input, sorted or not.
- **Speak your thinking:** brute force → bottleneck → better idea → pseudo code → code.
- **Always dry-run** with a small example and state **time and space complexity.**
- Use **meaningful variable names** and handle **edge cases** (empty list, single element, `n = 0`).
- If stuck: *"Let me think about a simpler version of the problem"* or try a **hash map, sorting, two pointers or recursion.**
- Practice by **typing** (not just reading) at least 30 programs on LeetCode/HackerRank/GeeksforGeeks. Pattern knowledge beats memorizing answers.
- Be ready to explain **Python theory with a small code example** (generators, decorators, mutable vs immutable, GIL).
- Stay calm and confident. **You've prepared well.** 💪

---
**Best of luck with your interview! 🚀**
