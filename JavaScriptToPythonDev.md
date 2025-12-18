# [title:JavaScript → Python Developer's Handbook]# JavaScript → Python Developer's Handbook

> A side-by-side translation of common JavaScript patterns to Python equivalents — covering logic, loops, lambdas, classes, reduce (with lambdas and named functions), modules, and `__init__.py`.

---

## 🧠 Logic & Control Flow

### None vs null/undefined

**JS**
```js
let x = null;        // intentional absence
let y;               // undefined
let z = undefined;   // explicit undefined

x == null            // true for null and undefined
x === null           // strict equality
```

**Python**
```python
x = None             # only one "no value" type

# Check for None
if x is None:
    pass

if x is not None:
    pass

# Don't use == for None
if x == None:        # ❌ works but not idiomatic
    pass
if x is None:        # ✅ correct way
    pass
```

### Truthiness / Falsiness

**JS**
```js
// Falsy: false, 0, "", null, undefined, NaN
if (!value) { }
Boolean(0)           // false
Boolean("")          // false
Boolean([])          // true (!)
Boolean({})          // true (!)
```

**Python**
```python
# Falsy: None, False, 0, 0.0, "", [], {}, set()
if not value:
    pass

bool(0)              # False
bool("")             # False
bool([])             # False (different from JS!)
bool({})             # False (different from JS!)
bool("0")            # True (string with content)
bool([0])            # True (list with content)

# Empty collections are falsy
if my_list:          # True if list has items
    pass
if not my_dict:      # True if dict is empty
    pass
```

### if / elif / else
```js
if (x > 10) console.log("big");
else if (x === 10) console.log("ten");
else console.log("small");
```
```python
if x > 10:
    print("big")
elif x == 10:
    print("ten")
else:
    print("small")
```

### Ternary
```js
const msg = x > 10 ? "big" : "small";
```
```python
msg = "big" if x > 10 else "small"
```

### try / catch / finally
```js
try { risky(); } catch (e) { handle(e); } finally { cleanup(); }
```
```python
try:
    risky()
except Exception as e:
    handle(e)
finally:
    cleanup()
```

### Logical Shortcuts
```js
a && doThing();
a || doOther();
```
```python
a and do_thing()
a or do_other()
```

---

## 🔁 Loops & Iteration

### for / while
```js
for (const x of arr) console.log(x);
```
```python
for x in arr:
    print(x)
```
```js
for (let i = 0; i < arr.length; i++) console.log(i, arr[i]);
```
```python
for i, x in enumerate(arr):
    print(i, x)
```
```js
let i = 0; while (i < 3) i++;
```
```python
i = 0
while i < 3:
    i += 1
```

### for-else
```python
for x in arr:
    if x == target:
        break
else:
    print("not found")
```

### range
```python
for i in range(5): pass      # 0..4
for i in range(1,6): pass    # 1..5
for i in range(0,10,2): pass # step 2
```

### Iterating Over Data Structures

#### Lists (mutable)
```python
# Basic iteration
items = [1, 2, 3]
for item in items:
    print(item)

# With index
for i, item in enumerate(items):
    print(i, item)

# With index starting at custom value
for i, item in enumerate(items, start=1):
    print(i, item)

# Reverse iteration
for item in reversed(items):
    print(item)
```

#### Tuples (immutable)
```python
# Same as lists - tuples are iterable
coords = (10, 20, 30)
for coord in coords:
    print(coord)

# Unpacking in loop
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]
for num, letter in pairs:
    print(num, letter)
```

#### Dictionaries
```js
// JS: Object iteration
const obj = {a: 1, b: 2};
for (const key in obj) console.log(key, obj[key]);
Object.keys(obj).forEach(k => console.log(k));
Object.values(obj).forEach(v => console.log(v));
Object.entries(obj).forEach(([k,v]) => console.log(k, v));
```
```python
# Python: Dictionary iteration
data = {"a": 1, "b": 2, "c": 3}

# Keys only
for key in data:
    print(key)

for key in data.keys():
    print(key)

# Values only
for value in data.values():
    print(value)

# Key-value pairs
for key, value in data.items():
    print(key, value)

# With enumerate
for i, (key, value) in enumerate(data.items()):
    print(i, key, value)
```

**Accessing Dictionary Values: `[]` vs `.get()`**

```js
// JS: undefined if missing
const val = obj.key;        // undefined if missing
const val = obj?.key;       // optional chaining
```
```python
# Python: KeyError vs None
data = {"a": 1}

# Bracket notation - crashes if missing
val = data["b"]              # KeyError: 'b'

# .get() - returns None or default if missing
val = data.get("b")          # None (safe)
val = data.get("b", 0)       # 0 (custom default)

# Use cases
count = counter.get("item", 0) + 1  # safe increment
user_id = request["user_id"]        # expect it to exist
```

✅ Use `dict['key']` when key must exist (let it crash)
✅ Use `dict.get('key')` when key might be missing (safe with default)

#### Sets (unordered, unique)
```python
# Basic iteration (order not guaranteed)
unique = {1, 2, 3, 4}
for item in unique:
    print(item)

# Sets are useful for checking membership
if 3 in unique:
    print("found")
```

#### Strings (iterable)
```python
# Character by character
for char in "hello":
    print(char)

# With index
for i, char in enumerate("hello"):
    print(i, char)
```

#### Multiple Iterables (zip)
```js
// JS: parallel iteration
const names = ['a', 'b'];
const nums = [1, 2];
names.forEach((n, i) => console.log(n, nums[i]));
```
```python
# Python: zip for parallel iteration
names = ['Alice', 'Bob', 'Carol']
ages = [25, 30, 35]

for name, age in zip(names, ages):
    print(name, age)

# zip stops at shortest iterable
# Use zip_longest from itertools for longest
from itertools import zip_longest
for name, age in zip_longest(names, ages, fillvalue='N/A'):
    print(name, age)
```

#### Nested Iterations
```python
# Nested loops
matrix = [[1, 2], [3, 4], [5, 6]]
for row in matrix:
    for item in row:
        print(item)

# Flattened with comprehension
flat = [item for row in matrix for item in row]
print(flat)  # [1, 2, 3, 4, 5, 6]
```

---

## 🔧 Data Structure Manipulation

### List/Dict/Set Comprehensions

**JS**
```js
// Map
const doubled = arr.map(x => x * 2);
// Filter
const evens = arr.filter(x => x % 2 === 0);
// Map + Filter
const result = arr.filter(x => x > 5).map(x => x * 2);
```

**Python**
```python
# List comprehension
doubled = [x * 2 for x in arr]

# With filter
evens = [x for x in arr if x % 2 == 0]

# Map + Filter combined
result = [x * 2 for x in arr if x > 5]

# Nested comprehension
matrix = [[i * j for j in range(3)] for i in range(3)]
# [[0, 0, 0], [0, 1, 2], [0, 2, 4]]

# Dict comprehension
squared = {x: x**2 for x in range(5)}
# {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Set comprehension
unique_lengths = {len(word) for word in ["a", "ab", "abc", "ab"]}
# {1, 2, 3}

# Conditional expressions in comprehensions
values = [x if x > 0 else 0 for x in [-1, 2, -3, 4]]
# [0, 2, 0, 4]
```

### Unpacking / Destructuring

**JS**
```js
// Array destructuring
const [a, b, ...rest] = [1, 2, 3, 4];
// Object destructuring
const {name, age} = user;
// Swap
[a, b] = [b, a];
```

**Python**
```python
# Tuple/List unpacking
a, b = [1, 2]
a, b, *rest = [1, 2, 3, 4]
# a=1, b=2, rest=[3, 4]

# Ignore values with _
a, _, c = [1, 2, 3]

# Nested unpacking
(a, b), c = [[1, 2], 3]

# Swap (no temp variable needed!)
a, b = b, a

# Dict unpacking
user = {"name": "Alice", "age": 25}
# No direct equivalent, but:
name = user["name"]
age = user["age"]

# Function argument unpacking
def greet(name, age):
    print(f"{name} is {age}")

user = {"name": "Alice", "age": 25}
greet(**user)  # unpacks dict as kwargs

numbers = [1, 2, 3]
print(*numbers)  # unpacks list as positional args
```

### Spread Operator Equivalents

**JS**
```js
// Array spread
const combined = [...arr1, ...arr2];
const copy = [...original];
// Object spread
const merged = {...obj1, ...obj2};
```

**Python**
```python
# List spread
combined = [*arr1, *arr2]
# or
combined = arr1 + arr2

# List copy
copy = [*original]
# or
copy = original.copy()
# or (deep copy)
import copy
deep_copy = copy.deepcopy(original)

# Dict spread (Python 3.9+)
merged = {**obj1, **obj2}
# or
merged = obj1 | obj2  # Python 3.9+

# Unpacking in function calls
def func(a, b, c):
    return a + b + c

args = [1, 2, 3]
func(*args)  # spreads list

kwargs = {"a": 1, "b": 2, "c": 3}
func(**kwargs)  # spreads dict
```

### *args and **kwargs - Dynamic Parameters

**JS**
```js
// Rest parameters (positional)
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4);  // 10

// No direct kwargs equivalent - use options object
function createUser(name, options = {}) {
  console.log(name, options.age, options.email);
}
createUser('Alice', {age: 25, email: 'alice@example.com'});
```

**Python**
```python
# *args - collects positional arguments into tuple
def sum_all(*args):
    return sum(args)

sum_all(1, 2, 3, 4)  # 10

# **kwargs - collects keyword arguments into dict
def greet(**kwargs):
    print(kwargs)  # {'name': 'Alice', 'age': 25}
    print(kwargs.get('name'))

greet(name='Alice', age=25)

# Combining both
def flexible(required, *args, **kwargs):
    print(f"Required: {required}")
    print(f"Positional: {args}")
    print(f"Keyword: {kwargs}")

flexible('data', 1, 2, 3, name='Alice', age=25)
# Required: data
# Positional: (1, 2, 3)
# Keyword: {'name': 'Alice', 'age': 25}
```

**Real-world examples:**

```python
# Dynamic SQL updates
def update_user(user_id, **fields):
    """Update user with any fields provided"""
    set_clauses = [f"{key} = %s" for key in fields.keys()]
    values = list(fields.values())
    sql = f"UPDATE users SET {', '.join(set_clauses)} WHERE id = %s"
    values.append(user_id)
    return sql, values

# Flexible - update any fields
update_user(123, email='new@example.com')
update_user(123, email='new@example.com', name='Alice', active=True)

# API wrapper with flexible query params
def fetch_data(endpoint, **params):
    """All kwargs become query parameters"""
    response = requests.get(f"{base_url}/{endpoint}", params=params)
    return response.json()

fetch_data('users', status='active', role='admin', limit=10)
# GET /users?status=active&role=admin&limit=10

# Logger with arbitrary context
def log_event(message, level='INFO', **context):
    ctx = ', '.join(f"{k}={v}" for k, v in context.items())
    print(f"[{level}] {message} | {ctx}")

log_event("User login", user_id=123, ip='192.168.1.1')
# [INFO] User login | user_id=123, ip=192.168.1.1
```

**Key points:**
- `*args` collects extra positional arguments into a **tuple**
- `**kwargs` collects extra keyword arguments into a **dict**
- Use `**kwargs` to unpack dict as keyword arguments when calling functions
- Common pattern: accept flexible parameters without defining them all upfront

### Slicing

**JS**

```js
// Array slicing
arr.slice(1, 4);      // elements 1-3
arr.slice(-2);        // last 2
arr.slice(0, -1);     // all but last
```

**Python**
```python
# List slicing [start:end:step]
arr[1:4]       # elements at index 1, 2, 3 (not 4)
arr[:3]        # first 3 elements
arr[2:]        # from index 2 to end
arr[-2:]       # last 2 elements
arr[:-1]       # all but last
arr[:]         # shallow copy

# Step/stride
arr[::2]       # every 2nd element
arr[1::2]      # every 2nd, starting at index 1
arr[::-1]      # reverse the list!

# Works on strings too
"hello"[1:4]   # "ell"
"hello"[::-1]  # "olleh"

# Assignment to slices
arr[1:3] = [10, 20]     # replace elements
arr[1:1] = [99]         # insert at index 1
del arr[1:3]            # delete elements
```

---

## ⚙️ Lambdas vs Functions

**JS**
```js
const double = x => x * 2;
```
**Python**
```python
double = lambda x: x * 2
```

Lambdas are **expression-only**, no statements, no loops.

```python
# Named functions can replace lambdas anywhere
def double(x):
    return x * 2

list(map(double, [1,2,3]))  # [2, 4, 6]
```

---

## 🧮 Reducing (Functional Accumulation)

### Lambda-based Reduces (JS-style)

```js
const sum = [1,2,3,4].reduce((acc, n) => acc + n, 0);
```
```python
from functools import reduce
sum_ = reduce(lambda acc, n: acc + n, [1, 2, 3, 4], 0)
```

```js
const odds = [1,2,3,4,5].reduce((acc, n) => (
  n % 2 ? [...acc, n] : acc
), []);
```
```python
odds = reduce(lambda acc, n: acc + [n] if n % 2 else acc, [1,2,3,4,5], [])
```

```js
const grouped = [1,2,3,4].reduce((acc, x) => {
  const k = x % 2 ? "odd" : "even";
  (acc[k] ??= []).push(x);
  return acc;
}, {});
```
```python
from collections import defaultdict
nums = [1,2,3,4]
grouped = reduce(lambda acc, x: (acc["odd" if x % 2 else "even"].append(x) or acc), nums, defaultdict(list))
grouped = dict(grouped)
```

> Lambdas are fine for one-liners — if it gets complex, use a function.

---

## 🧩 Named Reducer Functions (multi-line / testable)

**JS**
```js
function reducer(acc, n) {
  if (n % 2 === 0) acc.evens.push(n);
  else acc.odds.push(n);
  return acc;
}
const grouped = [1,2,3,4].reduce(reducer, {odds: [], evens: []});
```

**Python**
```python
from functools import reduce

def reducer(acc, n):
    if n % 2 == 0:
        acc["evens"].append(n)
    else:
        acc["odds"].append(n)
    return acc

nums = [1, 2, 3, 4]
grouped = reduce(reducer, nums, {"odds": [], "evens": []})
print(grouped)  # {'odds': [1, 3], 'evens': [2, 4]}
```

### Example: Flatten
```python
def flatten(acc, arr):
    return acc + arr

result = reduce(flatten, [[1,2],[3],[4,5]], [])
print(result)  # [1, 2, 3, 4, 5]
```

### Example: Dynamic Reducer Factory
```python
def make_threshold_reducer(threshold):
    def reducer(acc, x):
        if x > threshold:
            acc.append(x)
        return acc
    return reducer

nums = [1, 5, 10, 15]
gt10 = reduce(make_threshold_reducer(10), nums, [])
print(gt10)  # [15]
```

✅ Use lambdas for short, pure expressions.
✅ Use named `def` reducers for complex or reusable logic.

---

## 🎯 Type Hints (TypeScript → Python)

Python's type hints are similar to TypeScript but purely for documentation and static analysis (not runtime enforcement).

### Basic Types

**TypeScript**
```typescript
let name: string = "Alice";
let age: number = 25;
let active: boolean = true;
let data: any = "whatever";
```

**Python**
```python
name: str = "Alice"
age: int = 25
active: bool = True
data: any = "whatever"  # or use Any from typing
```

### Function Signatures

**TypeScript**
```typescript
function greet(name: string, age: number): string {
  return `${name} is ${age}`;
}

const add = (a: number, b: number): number => a + b;
```

**Python**
```python
def greet(name: str, age: int) -> str:
    return f"{name} is {age}"

add = lambda a: int, b: int: a + b  # lambdas rarely typed
```

### Union Types

**TypeScript**
```typescript
let id: string | number;
id = 123;
id = "abc";

function process(value: string | null): void {
  if (value !== null) {
    console.log(value.toUpperCase());
  }
}
```

**Python**
```python
from typing import Union, Optional

id: Union[str, int]
id = 123
id = "abc"

# Optional[T] is shorthand for Union[T, None]
def process(value: Optional[str]) -> None:
    if value is not None:
        print(value.upper())

# Python 3.10+ - use | operator
def process(value: str | None) -> None:
    pass
```

### Generic Types (Collections)

**TypeScript**
```typescript
let names: string[] = ["Alice", "Bob"];
let scores: Array<number> = [95, 87];
let pairs: [string, number][] = [["Alice", 95], ["Bob", 87]];

let userMap: Map<string, number> = new Map();
let uniqueIds: Set<number> = new Set([1, 2, 3]);
```

**Python**
```python
from typing import List, Dict, Set, Tuple

names: List[str] = ["Alice", "Bob"]
scores: list[int] = [95, 87]  # Python 3.9+ lowercase works
pairs: List[Tuple[str, int]] = [("Alice", 95), ("Bob", 87)]

user_map: Dict[str, int] = {}
unique_ids: Set[int] = {1, 2, 3}
```

### Literal Types & Enums

**TypeScript**
```typescript
type Status = "pending" | "active" | "done";
let status: Status = "active";

enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE"
}
```

**Python**
```python
from typing import Literal
from enum import Enum

status: Literal["pending", "active", "done"] = "active"

class Color(Enum):
    RED = "RED"
    GREEN = "GREEN"
    BLUE = "BLUE"

# Using it
color: Color = Color.RED
```

### TypedDict (Interface for Dicts)

**TypeScript**
```typescript
interface User {
  id: number;
  name: string;
  email?: string;  // optional
}

const user: User = {
  id: 1,
  name: "Alice"
};
```

**Python**
```python
from typing import TypedDict, Optional

class User(TypedDict):
    id: int
    name: str
    email: Optional[str]  # optional field

# Or with total=False for all optional
class UserPartial(TypedDict, total=False):
    id: int
    name: str
    email: str

user: User = {
    "id": 1,
    "name": "Alice"
}
```

### Generic Functions

**TypeScript**
```typescript
function identity<T>(value: T): T {
  return value;
}

function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

const num = identity<number>(42);
const str = identity("hello");
```

**Python**
```python
from typing import TypeVar, List, Optional

T = TypeVar('T')

def identity(value: T) -> T:
    return value

def first(arr: List[T]) -> Optional[T]:
    return arr[0] if arr else None

num = identity(42)  # Type inference works
str_val = identity("hello")
```

### Generic Classes

**TypeScript**
```typescript
class Box<T> {
  constructor(private value: T) {}

  getValue(): T {
    return this.value;
  }
}

const numBox = new Box<number>(42);
const strBox = new Box("hello");
```

**Python**
```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, value: T):
        self.value = value

    def get_value(self) -> T:
        return self.value

num_box: Box[int] = Box(42)
str_box: Box[str] = Box("hello")
```

### Callable Types (Function Types)

**TypeScript**
```typescript
type Handler = (event: string) => void;
type Mapper = (x: number) => string;

function register(callback: Handler): void {
  callback("event");
}
```

**Python**
```python
from typing import Callable

Handler = Callable[[str], None]
Mapper = Callable[[int], str]

def register(callback: Handler) -> None:
    callback("event")

# Or inline
def process(func: Callable[[int, int], int]) -> int:
    return func(1, 2)
```

### Advanced: Protocol (Structural Typing)

**TypeScript**
```typescript
interface Drawable {
  draw(): void;
}

function render(obj: Drawable) {
  obj.draw();
}

// Any object with draw() works
render({draw: () => console.log("drawing")});
```

**Python**
```python
from typing import Protocol

class Drawable(Protocol):
    def draw(self) -> None:
        ...

def render(obj: Drawable) -> None:
    obj.draw()

# Any object with draw() method works (duck typing)
class Circle:
    def draw(self) -> None:
        print("drawing circle")

render(Circle())  # Type checker approves
```

### Type Checking with mypy

```bash
# Install type checker
pip install mypy

# Check your code
mypy your_file.py
```

**Key Differences:**
- **TypeScript**: Types enforced at compile time, stripped at runtime
- **Python**: Types are hints only, not enforced at runtime (unless using Pydantic/dataclasses)
- **Static checker**: Use `mypy`, `pyright`, or `pytype` to validate types
- **Runtime validation**: Use Pydantic for runtime type checking

```python
# Type hints don't prevent this at runtime!
def add(a: int, b: int) -> int:
    return a + b

add("hello", "world")  # No error at runtime! Returns "helloworld"

# Use mypy to catch it:
# $ mypy script.py
# error: Argument 1 to "add" has incompatible type "str"; expected "int"
```

---

## 🧱 Classes

**JS**
```js
class Animal {
  constructor(name) { this.name = name; }
  speak() { console.log(`${this.name} makes a sound`); }
}
class Dog extends Animal {
  speak() { console.log(`${this.name} barks`); }
}
```

**Python**
```python
class Animal:
    def __init__(self, name):
        self.name = name
    def speak(self):
        print(f"{self.name} makes a sound")

class Dog(Animal):
    def speak(self):
        print(f"{self.name} barks")
```

### Notes
- `self` is explicit.
- Use `super().__init__()` for inheritance.
- Decorators:
  ```python
  class Math:
      @staticmethod
      def square(x): return x*x

      @classmethod
      def identity(cls): return cls()

      @property
      def computed(self):
          return self.value * 2
  ```

### Dataclasses - Less Boilerplate

**JS**
```js
class User {
  constructor(id, name, email = null) {
    this.id = id;
    this.name = name;
    this.email = email;
  }
}
```

**Python**
```python
from dataclasses import dataclass

@dataclass
class User:
    id: int
    name: str
    email: str = None

    # Auto-generates __init__, __repr__, __eq__

user = User(1, "Alice")
print(user)  # User(id=1, name='Alice', email=None)

# With post-init processing
@dataclass
class Product:
    name: str
    price: float
    tax_rate: float = 0.1
    total: float = None

    def __post_init__(self):
        if self.total is None:
            self.total = self.price * (1 + self.tax_rate)
```

### Common Dunders
| Method                   | Trigger                | JS Equivalent         |
|--------------------------|------------------------|-----------------------|
| `__init__`               | constructor            | `constructor()`       |
| `__str__`                | `str(obj)` / `print()` | `toString()`          |
| `__repr__`               | REPL debug repr        | `JSON.stringify(obj)` |
| `__len__`                | `len(obj)`             | `.length`             |
| `__getitem__`            | `obj[key]`             | `obj[key]`            |
| `__iter__` / `__next__`  | iteration              | `[Symbol.iterator]`   |
| `__eq__` / `__lt__`      | comparisons            | `==`, `<`             |
| `__enter__` / `__exit__` | context mgr            | try/finally cleanup   |

---

## 📦 Modules & Imports

### Import Patterns

**JS**
```js
// ES6 imports
import { add, subtract } from './math.js';
import * as math from './math.js';
import math from './math.js';  // default export
const math = require('./math');  // CommonJS
```

**Python**
```python
# Import entire module
import math
print(math.sqrt(16))

# Import specific items
from math import sqrt, pi
print(sqrt(16))

# Import with alias
import numpy as np
import pandas as pd

# Import all (avoid in production)
from math import *

# Import from package
from utils.math_helpers import add, subtract
```

### Relative vs Absolute Imports

```
project/
├── main.py
└── utils/
    ├── __init__.py
    ├── math_helpers.py
    └── string_helpers.py
```

**Absolute imports** (preferred)
```python
# In main.py
from utils.math_helpers import add

# In utils/string_helpers.py
from utils.math_helpers import add
```

**Relative imports** (within packages)
```python
# In utils/string_helpers.py
from .math_helpers import add           # same directory
from ..other_package import something   # parent directory
```

### `__init__.py` - Package Marker

`__init__.py` marks a directory as a **package** and controls what gets imported.

**utils/__init__.py**
```python
# Re-export specific functions
from .math_helpers import add, subtract
from .string_helpers import capitalize

# Control "from utils import *"
__all__ = ["add", "subtract", "capitalize"]

# Package-level initialization
print("Utils package loaded")
```

**main.py**
```python
# Now you can import directly from utils
from utils import add, subtract
print(add(2, 3))

# Or import the whole package
import utils
utils.add(2, 3)
```

If `__init__.py` is missing → namespace package (PEP 420).
✅ Always include one for explicit package control.

### Export Control with `__all__`

**JS**
```js
// math.js - Explicit exports
export function add(a, b) { return a + b; }
export function subtract(a, b) { return a - b; }
function _internal() { }  // not exported

// OR named exports list
function add(a, b) { return a + b; }
function subtract(a, b) { return a - b; }
function _internal() { }
export { add, subtract };
```

**Python**
```python
# math_helpers.py - Everything is "exported" by default!
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def _internal():  # convention: _ prefix = "private"
    pass

# Control what "from math_helpers import *" imports
__all__ = ["add", "subtract"]  # excludes _internal
```

**Usage:**
```python
# These all work regardless of __all__:
from math_helpers import add
from math_helpers import _internal  # Still works! Just discouraged

# __all__ only affects wildcard imports:
from math_helpers import *  # Only gets add, subtract
```

**Key differences:**
- **JS**: Must explicitly export everything
- **Python**: Everything is importable by default, `__all__` only controls `import *` behavior

### `if __name__ == "__main__"`

**JS**
```js
// No direct equivalent - use package.json scripts
function main() {
  console.log("Running as script");
}
if (require.main === module) main();  // CommonJS only
```

**Python**
```python
# math_helpers.py
def add(a, b):
    return a + b

if __name__ == "__main__":
    # Only runs when executed directly, not when imported
    print("Testing add:", add(2, 3))
```

```bash
python math_helpers.py  # Runs test
# vs
python -c "from math_helpers import add"  # Doesn't run test
```

### Avoiding Circular Imports

```python
# ❌ BAD: circular dependency
# file_a.py
from file_b import func_b

# file_b.py
from file_a import func_a

# ✅ GOOD: import inside function
# file_a.py
def func_a():
    from file_b import func_b
    func_b()
```

---

## 🎁 Context Managers (`with` statements)

**JS** (no direct equivalent)
```js
// Manual cleanup
const file = fs.openSync('data.txt');
try {
  const data = fs.readSync(file);
  process(data);
} finally {
  fs.closeSync(file);  // Must remember to close
}

// Or with callbacks
fs.readFile('data.txt', (err, data) => {
  if (err) throw err;
  process(data);
});
```

**Python**
```python
# Automatic cleanup with context manager
with open('data.txt') as f:
    data = f.read()
    process(data)
# File automatically closed, even if error occurs

# Multiple resources
with open('input.txt') as fin, open('output.txt', 'w') as fout:
    fout.write(fin.read())

# Database connections
with db.get_connection() as conn:
    conn.execute(query)
# Auto-commits and closes

# Locks
from threading import Lock
lock = Lock()

with lock:
    # Thread-safe operation
    shared_resource.modify()
# Lock automatically released
```

### Creating Custom Context Managers

**Decorator approach:**
```python
from contextlib import contextmanager
import time

@contextmanager
def timer(name):
    start = time.time()
    print(f"{name} starting...")
    yield  # Code block runs here
    elapsed = time.time() - start
    print(f"{name} took {elapsed:.2f}s")

with timer("Data processing"):
    process_large_dataset()
# Automatic timing output
```

**Class approach:**
```python
class DatabaseTransaction:
    def __enter__(self):
        self.conn = get_connection()
        self.conn.begin()
        return self.conn

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is None:
            self.conn.commit()
        else:
            self.conn.rollback()
        self.conn.close()

with DatabaseTransaction() as conn:
    conn.execute("INSERT ...")
    conn.execute("UPDATE ...")
# Auto-commits on success, rolls back on error
```

---

## 🎨 Decorators (Function Wrappers)

**JS**
```js
// Higher-order function pattern
function logCalls(func) {
  return function(...args) {
    console.log(`Calling ${func.name} with`, args);
    return func(...args);
  };
}

const add = logCalls((a, b) => a + b);
add(2, 3);  // Logs: Calling add with [2, 3]
```

**Python**
```python
# Decorator syntax
def log_calls(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with {args} {kwargs}")
        return func(*args, **kwargs)
    return wrapper

@log_calls
def add(a, b):
    return a + b

add(2, 3)  # Logs: Calling add with (2, 3) {}
```

### Common Built-in Decorators

```python
from functools import wraps, lru_cache
import time

# Preserve function metadata
def timer(func):
    @wraps(func)  # Preserves func.__name__, __doc__, etc.
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        print(f"{func.__name__} took {time.time() - start:.2f}s")
        return result
    return wrapper

# Memoization/caching
@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

# Property decorator
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def area(self):
        return 3.14159 * self._radius ** 2

    @property
    def diameter(self):
        return self._radius * 2

    @diameter.setter
    def diameter(self, value):
        self._radius = value / 2

circle = Circle(5)
print(circle.area)       # Computed property
circle.diameter = 20     # Setter
```

### Decorators with Arguments

```python
def repeat(times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
# Hello, Alice!
# Hello, Alice!
# Hello, Alice!

# Real-world: retry decorator
def retry(max_attempts=3, delay=1):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    time.sleep(delay)
        return wrapper
    return decorator

@retry(max_attempts=3, delay=2)
def fetch_data():
    # Retries up to 3 times with 2s delay
    return requests.get(url)
```

---

## 🔄 Generators (Lazy Evaluation)

**JS**
```js
// Generator function
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next().value);  // 1
console.log(gen.next().value);  // 2

// Async generators
async function* fetchPages() {
  for (let i = 1; i <= 5; i++) {
    const data = await fetch(`/api/page/${i}`);
    yield data;
  }
}
```

**Python**
```python
# Generator function with yield
def number_generator():
    yield 1
    yield 2
    yield 3

gen = number_generator()
print(next(gen))  # 1
print(next(gen))  # 2

# Or iterate
for num in number_generator():
    print(num)

# Memory-efficient file processing
def read_large_file(path):
    with open(path) as f:
        for line in f:
            yield line.strip()  # Only loads one line at a time

for line in read_large_file('huge.txt'):
    process(line)  # Memory stays constant

# Generator expressions (like list comp but lazy)
squares = (x**2 for x in range(1000000))  # Doesn't compute yet
print(next(squares))  # 0
print(next(squares))  # 1

# vs list comprehension (computes immediately)
squares_list = [x**2 for x in range(1000000)]  # Allocates all memory now
```

### Generator Use Cases

```python
# Infinite sequences
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

fib = fibonacci()
print([next(fib) for _ in range(10)])  # [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

# ETL pipelines - chain generators
def extract(filename):
    with open(filename) as f:
        for line in f:
            yield line.strip()

def transform(lines):
    for line in lines:
        yield line.upper()

def load(lines):
    for line in lines:
        db.insert(line)

# Compose pipeline
load(transform(extract('data.txt')))  # Memory efficient!
```

---

## 🔍 Advanced Iteration Tools

### itertools - Power Tools

```python
from itertools import (
    chain, groupby, islice, cycle,
    combinations, permutations
)

# Chain multiple iterables
combined = chain([1, 2], [3, 4], [5, 6])
list(combined)  # [1, 2, 3, 4, 5, 6]

# Group by key
data = [('A', 1), ('A', 2), ('B', 3), ('B', 4)]
for key, group in groupby(data, lambda x: x[0]):
    print(key, list(group))
# A [('A', 1), ('A', 2)]
# B [('B', 3), ('B', 4)]

# Slice iterator (don't load all into memory)
for item in islice(huge_generator(), 10):
    print(item)  # First 10 items only

# Cycle through items infinitely
colors = cycle(['red', 'green', 'blue'])
print([next(colors) for _ in range(5)])
# ['red', 'green', 'blue', 'red', 'green']

# Combinations and permutations
list(combinations([1, 2, 3], 2))  # [(1, 2), (1, 3), (2, 3)]
list(permutations([1, 2, 3], 2))  # [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]
```

### functools - Functional Utilities

```python
from functools import partial, reduce

# Partial application (pre-fill arguments)
def greet(greeting, name):
    return f"{greeting}, {name}!"

say_hello = partial(greet, "Hello")
say_goodbye = partial(greet, "Goodbye")

say_hello("Alice")    # "Hello, Alice!"
say_goodbye("Alice")  # "Goodbye, Alice!"

# Reduce (you already have this, but here's more)
from operator import add, mul
reduce(add, [1, 2, 3, 4])  # 10
reduce(mul, [1, 2, 3, 4])  # 24
```

---

## 🎯 Pattern Matching (Python 3.10+)

**JS**
```js
switch (status) {
  case 'pending':
    process();
    break;
  case 'done':
    archive();
    break;
  default:
    handleUnknown();
}
```

**Python**
```python
# Match/case with structural pattern matching
match status:
    case 'pending':
        process()
    case 'done':
        archive()
    case _:
        handle_unknown()

# Structural patterns
match point:
    case (0, 0):
        print("Origin")
    case (x, 0):
        print(f"On X-axis at {x}")
    case (0, y):
        print(f"On Y-axis at {y}")
    case (x, y):
        print(f"Point at {x}, {y}")

# Match objects
match response:
    case {"status": 200, "data": data}:
        process(data)
    case {"status": 404}:
        not_found()
    case {"status": code, "error": msg}:
        handle_error(code, msg)

# Match with guards
match user:
    case {"role": "admin", "active": True}:
        grant_access()
    case {"role": role} if role in ["user", "guest"]:
        limited_access()
    case _:
        deny()
```

---

## 🔧 Advanced String Formatting

You have basic f-strings, but here's more:

```python
name = "Alice"
value = 1234.5678
count = 42

# Debug formatting (Python 3.8+)
f"{name=}"              # "name='Alice'"
f"{value=:.2f}"         # "value=1234.57"

# Number formatting
f"{value:.2f}"          # "1234.57" - 2 decimal places
f"{value:,.2f}"         # "1,234.57" - with thousands separator
f"{value:>10}"          # "  1234.5678" - right-align in 10 chars
f"{value:<10}"          # "1234.5678  " - left-align
f"{value:^10}"          # " 1234.5678 " - center

# Percentage
rate = 0.156
f"{rate:.1%}"           # "15.6%"

# Binary/hex
f"{count:b}"            # "101010" - binary
f"{count:x}"            # "2a" - hex
f"{count:08b}"          # "00101010" - padded binary

# Date formatting
from datetime import datetime
now = datetime.now()
f"{now:%Y-%m-%d %H:%M:%S}"  # "2025-12-18 14:30:45"
```

---

## 💡 Walrus Operator (`:=`) - Assignment Expressions

**Python 3.8+** - assign and use in same expression

```python
# Before: repetitive
if len(data) > 10:
    print(f"Large dataset: {len(data)}")

# After: assign while checking
if (n := len(data)) > 10:
    print(f"Large dataset: {n}")

# In while loops
while (line := file.readline()) != "":
    process(line)

# In comprehensions
[y for x in data if (y := transform(x)) is not None]

# Avoid redundant function calls
if (match := regex.search(text)):
    print(match.group(1))
```

---

## 🧩 Functional TL;DR

| JS | Python |
|----|---------|
| `map(fn, arr)` | `[fn(x) for x in arr]` |
| `filter(fn, arr)` | `[x for x in arr if fn(x)]` |
| `reduce(fn, arr, init)` | `reduce(fn, arr, init)` |
| `.some(fn)` | `any(fn(x) for x in arr)` |
| `.every(fn)` | `all(fn(x) for x in arr)` |
| `.forEach(fn)` | `for x in arr: fn(x)` |

---

## ⚡ Async/Await

**JS**
```js
// Async function declaration
async function fetchUser(id) {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();
  return data;
}

// Async arrow function
const getUser = async (id) => {
  return await fetchUser(id);
};

// Calling async functions
fetchUser(123).then(user => console.log(user));

// Or with await (inside async context)
const user = await fetchUser(123);

// Promise.all for parallel execution
const [users, posts] = await Promise.all([
  fetchUsers(),
  fetchPosts()
]);

// Error handling
try {
  const user = await fetchUser(123);
} catch (error) {
  console.error(error);
}
```

**Python**
```python
import asyncio
import aiohttp  # or httpx for async HTTP

# Async function declaration
async def fetch_user(id):
    async with aiohttp.ClientSession() as session:
        async with session.get(f'/api/users/{id}') as response:
            data = await response.json()
            return data

# Calling async functions - must use await in async context
async def main():
    user = await fetch_user(123)
    print(user)

# Run the async function (entry point)
asyncio.run(main())

# asyncio.gather for parallel execution
async def get_all_data():
    users, posts = await asyncio.gather(
        fetch_users(),
        fetch_posts()
    )
    return users, posts

# Error handling - same as sync
async def safe_fetch():
    try:
        user = await fetch_user(123)
    except Exception as error:
        print(f"Error: {error}")

# Running multiple tasks concurrently
async def process_batch(ids):
    tasks = [fetch_user(id) for id in ids]
    results = await asyncio.gather(*tasks)
    return results
```

### Key Differences

**JS:**
- `async`/`await` works everywhere (browser, Node.js)
- Promises are built into the language
- `Promise.all()`, `Promise.race()`, `Promise.allSettled()`
- Event loop runs automatically

**Python:**
- Need `asyncio` library for event loop
- Must use `asyncio.run()` as entry point (or manage loop manually)
- `asyncio.gather()` for parallel execution
- `asyncio.create_task()` for background tasks
- Requires async-compatible libraries (`aiohttp`, `httpx`, `asyncpg` vs `requests`, `psycopg2`)

### Async Iteration

**JS**
```js
for await (const item of asyncGenerator()) {
  console.log(item);
}
```

**Python**
```python
async for item in async_generator():
    print(item)
```

### Context Managers (async with)

**JS** (no direct equivalent)
```js
const resource = await acquire();
try {
  await resource.use();
} finally {
  await resource.release();
}
```

**Python**
```python
# Automatic cleanup with async context manager
async with acquire_resource() as resource:
    await resource.use()
# Automatically released
```

### Common Pitfalls

```python
# ❌ Can't call async function from sync context
def sync_function():
    user = await fetch_user(123)  # SyntaxError

# ✅ Use asyncio.run() to bridge sync → async
def sync_function():
    user = asyncio.run(fetch_user(123))

# ❌ Forgetting to await (returns coroutine object)
async def bad():
    user = fetch_user(123)  # Not awaited!
    print(user)  # <coroutine object>

# ✅ Always await async functions
async def good():
    user = await fetch_user(123)
    print(user)  # actual data
```

---
