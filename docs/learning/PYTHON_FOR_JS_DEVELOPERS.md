# Python for JavaScript Developers

## 🎯 Purpose

This guide is a **crash course in Python** designed specifically for JavaScript/TypeScript developers. By the end, you'll:
- Understand Python syntax and idioms
- Know how Python concepts map to JavaScript
- Be productive in Dify's Python codebase
- Understand Python's async story

**Time to read:** 2-3 hours

---

## 🚀 Quick Start: Python in 10 Minutes

### Hello World

```python
# Python
print("Hello, World!")

# JavaScript
console.log("Hello, World!")
```

### Variables

```python
# Python - no const/let/var
name = "John"
age = 30
is_active = True  # Note: True, not true

# Type hints (optional but recommended)
name: str = "John"
age: int = 30
is_active: bool = True

# JavaScript
const name = "John"
let age = 30
let isActive = true
```

💡 **Aha Moment:** Python uses `snake_case`, JavaScript uses `camelCase`!

### Functions

```python
# Python
def greet(name: str) -> str:
    return f"Hello, {name}!"

result = greet("Alice")

# JavaScript
function greet(name: string): string {
  return `Hello, ${name}!`
}

const result = greet("Alice")
```

### Classes

```python
# Python
class User:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age

    def greet(self) -> str:
        return f"Hello, I'm {self.name}"

user = User("Alice", 30)
print(user.greet())

# JavaScript
class User {
  constructor(name: string, age: number) {
    this.name = name
    this.age = age
  }

  greet(): string {
    return `Hello, I'm ${this.name}`
  }
}

const user = new User("Alice", 30)
console.log(user.greet())
```

🌉 **Bridge:** `__init__` = `constructor`, `self` = `this`

---

## 📚 Core Concepts

### 1. Data Structures

#### Lists (Arrays)

```python
# Python list = JavaScript array
numbers = [1, 2, 3, 4, 5]
names = ["Alice", "Bob", "Charlie"]
mixed = [1, "two", 3.0, True]

# Access
first = numbers[0]      # 1
last = numbers[-1]      # 5 (negative indexing!)

# Slicing
subset = numbers[1:3]   # [2, 3]
first_three = numbers[:3]  # [1, 2, 3]

# JavaScript equivalent
const numbers = [1, 2, 3, 4, 5]
const first = numbers[0]
const last = numbers[numbers.length - 1]
const subset = numbers.slice(1, 3)
```

💡 **Aha Moment:** Python has **negative indexing**: `list[-1]` = last item!

#### Dictionaries (Objects)

```python
# Python dict = JavaScript object
user = {
    "name": "Alice",
    "age": 30,
    "active": True
}

# Access
name = user["name"]  # or user.get("name")

# Add
user["email"] = "alice@example.com"

# JavaScript
const user = {
  name: "Alice",
  age: 30,
  active: true
}

const name = user.name // or user["name"]
```

#### Sets

```python
# Python - unique collection
tags = {"python", "flask", "api"}
tags.add("celery")
tags.remove("api")

# JavaScript equivalent (ES6)
const tags = new Set(["python", "flask", "api"])
tags.add("celery")
tags.delete("api")
```

#### Tuples (Immutable Arrays)

```python
# Python - immutable list
coords = (10, 20)
x, y = coords  # Tuple unpacking

# Can't modify
coords[0] = 15  # ❌ Error!

# JavaScript - use const array (not truly immutable)
const coords = [10, 20]
const [x, y] = coords
```

### 2. Control Flow

#### If Statements

```python
# Python
if age >= 18:
    print("Adult")
elif age >= 13:
    print("Teen")
else:
    print("Child")

# JavaScript
if (age >= 18) {
  console.log("Adult")
} else if (age >= 13) {
  console.log("Teen")
} else {
  console.log("Child")
}
```

⚠️ **Common Pitfall:** Python uses **indentation** instead of braces! Tabs vs spaces matters.

#### For Loops

```python
# Python - iterate over items
for name in names:
    print(name)

# With index
for i, name in enumerate(names):
    print(f"{i}: {name}")

# Range
for i in range(5):
    print(i)  # 0, 1, 2, 3, 4

# JavaScript
for (const name of names) {
  console.log(name)
}

names.forEach((name, i) => {
  console.log(`${i}: ${name}`)
})

for (let i = 0; i < 5; i++) {
  console.log(i)
}
```

#### While Loops

```python
# Python
count = 0
while count < 5:
    print(count)
    count += 1

# JavaScript
let count = 0
while (count < 5) {
  console.log(count)
  count++
}
```

### 3. List Comprehensions 🔥

**This is Python magic!**

```python
# Create a list with transformation
squares = [x**2 for x in range(10)]
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# With condition
evens = [x for x in range(10) if x % 2 == 0]
# [0, 2, 4, 6, 8]

# JavaScript equivalent
const squares = Array.from({length: 10}, (_, x) => x**2)
// or
const squares = [...Array(10)].map((_, x) => x**2)

const evens = [...Array(10)].map((_, x) => x).filter(x => x % 2 === 0)
```

💡 **Aha Moment:** List comprehensions = `map` + `filter` in one line!

**Dictionary comprehension:**

```python
# Python
user_ages = {name: len(name) for name in ["Alice", "Bob"]}
# {"Alice": 5, "Bob": 3}

# JavaScript
const userAges = Object.fromEntries(
  ["Alice", "Bob"].map(name => [name, name.length])
)
```

### 4. Functions

#### Function Syntax

```python
# Python
def add(a: int, b: int) -> int:
    return a + b

# Default arguments
def greet(name: str, greeting: str = "Hello") -> str:
    return f"{greeting}, {name}!"

greet("Alice")  # "Hello, Alice!"
greet("Bob", "Hi")  # "Hi, Bob!"

# Keyword arguments
greet(name="Alice", greeting="Hey")
greet(greeting="Hey", name="Alice")  # Order doesn't matter!

# JavaScript
function add(a: number, b: number): number {
  return a + b
}

function greet(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`
}
```

#### Lambda Functions (Arrow Functions)

```python
# Python lambda
square = lambda x: x**2
add = lambda a, b: a + b

# Used with map/filter
numbers = [1, 2, 3, 4, 5]
squares = list(map(lambda x: x**2, numbers))
evens = list(filter(lambda x: x % 2 == 0, numbers))

# JavaScript arrow functions
const square = (x) => x**2
const add = (a, b) => a + b

const squares = numbers.map(x => x**2)
const evens = numbers.filter(x => x % 2 === 0)
```

🎯 **Remember:** Python lambdas are **single-expression** only!

#### *args and **kwargs

```python
# Python - variable arguments
def sum_all(*args):
    return sum(args)

sum_all(1, 2, 3)  # 6
sum_all(1, 2, 3, 4, 5)  # 15

# Keyword arguments
def configure(**kwargs):
    for key, value in kwargs.items():
        print(f"{key} = {value}")

configure(debug=True, timeout=30)
# debug = True
# timeout = 30

# JavaScript equivalent (rest parameters)
function sumAll(...args) {
  return args.reduce((a, b) => a + b, 0)
}

function configure(options) {
  for (const [key, value] of Object.entries(options)) {
    console.log(`${key} = ${value}`)
  }
}
```

### 5. Decorators (Higher-Order Functions) 🎨

```python
# Python decorator
def logged(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Result: {result}")
        return result
    return wrapper

@logged
def add(a, b):
    return a + b

add(2, 3)
# Calling add
# Result: 5

# JavaScript equivalent
function logged(func) {
  return function(...args) {
    console.log(`Calling ${func.name}`)
    const result = func(...args)
    console.log(`Result: ${result}`)
    return result
  }
}

const add = logged((a, b) => a + b)
```

**Common Dify decorators:**

```python
# Flask route decorator
@app.route('/api/apps', methods=['GET'])
def get_apps():
    return jsonify(apps)

# Authentication decorator
@require_auth
def protected_route():
    return {"user": g.current_user}

# Celery task decorator
@shared_task
def process_document(doc_id):
    # async task
    pass
```

🔗 **Example:** [api/controllers/console/app/app.py](../../api/controllers/console/app/app.py)

### 6. Context Managers (try/finally pattern)

```python
# Python
with open('file.txt', 'r') as f:
    content = f.read()
# File automatically closed

# Database session
with db.session.begin():
    user = User(name="Alice")
    db.session.add(user)
# Auto-commit or rollback

# JavaScript equivalent
let f
try {
  f = fs.openSync('file.txt', 'r')
  const content = fs.readFileSync(f, 'utf8')
} finally {
  if (f) fs.closeSync(f)
}
```

💡 **Aha Moment:** `with` guarantees cleanup, like `finally` but cleaner!

### 7. String Formatting

```python
# Python f-strings (preferred)
name = "Alice"
age = 30
message = f"Hello, {name}! You are {age} years old."

# With expressions
message = f"Next year you'll be {age + 1}!"

# Old ways (still seen)
message = "Hello, %s! You are %d years old." % (name, age)
message = "Hello, {}! You are {} years old.".format(name, age)

# JavaScript template literals
const message = `Hello, ${name}! You are ${age} years old.`
```

---

## 🔄 Async/Await

### Python Async

```python
# Python async/await
import asyncio

async def fetch_data(url: str) -> dict:
    # Simulate async I/O
    await asyncio.sleep(1)
    return {"data": "..."}

async def main():
    result = await fetch_data("https://api.example.com")
    print(result)

# Run
asyncio.run(main())

# JavaScript
async function fetchData(url) {
  await new Promise(resolve => setTimeout(resolve, 1000))
  return {data: "..."}
}

async function main() {
  const result = await fetchData("https://api.example.com")
  console.log(result)
}

main()
```

⚠️ **Important:** Dify uses **Celery** for async tasks, not async/await!

### Dify's Async Pattern (Celery)

```python
# Define async task
from celery import shared_task

@shared_task
def process_document(document_id: str):
    # Long-running task
    document = Document.query.get(document_id)
    # ... process ...
    return {"status": "completed"}

# Queue the task
task = process_document.delay(document_id)

# Check status later
result = AsyncResult(task.id)
if result.ready():
    print(result.result)
```

🔗 **Example:** [api/tasks/rag_pipeline/](../../api/tasks/rag_pipeline/)

---

## 📦 Modules and Imports

### Import Syntax

```python
# Python
# Import module
import os
os.path.join('a', 'b')

# Import specific items
from os.path import join
join('a', 'b')

# Import with alias
import pandas as pd
df = pd.DataFrame()

# Import everything (avoid!)
from os import *

# JavaScript equivalent
import os from 'os'
import { join } from 'path'
import * as pd from 'pandas'
```

### Dify Import Patterns

```python
# Models
from models import App, Conversation, Message

# Services
from services.app_service import AppService

# Core
from core.workflow.graph_engine import GraphEngine

# Extensions
from extensions.ext_database import db
```

---

## 🧪 Type Hints (Like TypeScript)

```python
# Python type hints
from typing import List, Dict, Optional, Union

def process_users(
    users: List[str],
    config: Dict[str, any],
    limit: Optional[int] = None,
) -> Union[List[Dict], None]:
    if limit is None:
        limit = 10

    results: List[Dict] = []
    for user in users[:limit]:
        results.append({"name": user})

    return results

# Modern Python 3.10+ (Dify uses this)
def process_users(
    users: list[str],
    config: dict[str, any],
    limit: int | None = None,
) -> list[dict] | None:
    pass
```

🔗 **Example:** [api/services/app_service.py](../../api/services/app_service.py)

---

## 🏛️ Object-Oriented Python

### Classes and Inheritance

```python
# Base class
class Animal:
    def __init__(self, name: str):
        self.name = name

    def speak(self) -> str:
        raise NotImplementedError("Subclass must implement")

# Inheritance
class Dog(Animal):
    def speak(self) -> str:
        return f"{self.name} says Woof!"

# Usage
dog = Dog("Buddy")
print(dog.speak())  # "Buddy says Woof!"

# JavaScript
class Animal {
  constructor(name) {
    this.name = name
  }

  speak() {
    throw new Error("Subclass must implement")
  }
}

class Dog extends Animal {
  speak() {
    return `${this.name} says Woof!`
  }
}
```

### Special Methods (Magic Methods)

```python
class App:
    def __init__(self, name: str):
        self.name = name

    def __str__(self) -> str:
        # Like toString() in JavaScript
        return f"App({self.name})"

    def __repr__(self) -> str:
        # Developer representation
        return f"<App name='{self.name}'>"

    def __eq__(self, other) -> bool:
        # Equality comparison
        return isinstance(other, App) and self.name == other.name

app = App("ChatBot")
print(str(app))  # App(ChatBot)
print(repr(app))  # <App name='ChatBot'>
```

---

## ⚡ Common Patterns in Dify

### 1. Service Classes

```python
# api/services/app_service.py
class AppService:
    @staticmethod
    def create_app(
        tenant_id: str,
        name: str,
        mode: str,
    ) -> App:
        app = App(
            tenant_id=tenant_id,
            name=name,
            mode=mode,
        )
        db.session.add(app)
        db.session.commit()
        return app

    @staticmethod
    def get_app(app_id: str, tenant_id: str) -> App:
        return App.query.filter_by(
            id=app_id,
            tenant_id=tenant_id,
        ).first_or_404()
```

### 2. SQLAlchemy Models

```python
# api/models/model.py
class App(db.Model):
    __tablename__ = 'apps'

    id = db.Column(db.String(255), primary_key=True)
    tenant_id = db.Column(db.String(255), nullable=False)
    name = db.Column(db.String(255), nullable=False)
    mode = db.Column(db.String(255), nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    # Relationships
    conversations = db.relationship('Conversation', back_populates='app')

    def to_dict(self) -> dict:
        return {
            'id': self.id,
            'name': self.name,
            'mode': self.mode,
        }
```

### 3. Pydantic Validation

```python
# api/controllers/console/app/app.py
from pydantic import BaseModel, Field

class CreateAppRequest(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    mode: str = Field(..., pattern="^(chat|workflow|agent-chat|channel)$")
    description: str | None = None

# Usage
@bp.route('/apps', methods=['POST'])
def create_app():
    try:
        request_data = CreateAppRequest(**request.json)
    except ValidationError as e:
        return {'errors': e.errors()}, 400

    app = AppService.create_app(
        tenant_id=g.current_tenant.id,
        name=request_data.name,
        mode=request_data.mode,
    )
    return app.to_dict()
```

---

## ✅ Quick Check

1. **Q:** What's the Python equivalent of `const arr = [1, 2, 3]`?
   <details><summary>Answer</summary>`arr = [1, 2, 3]` (or `arr: list[int] = [1, 2, 3]` with type hints)</details>

2. **Q:** How do you iterate with index in Python?
   <details><summary>Answer</summary>`for i, item in enumerate(items):`</details>

3. **Q:** What's the Python equivalent of arrow functions?
   <details><summary>Answer</summary>`lambda` functions (e.g., `lambda x: x + 1`)</details>

4. **Q:** What's `self` in Python?
   <details><summary>Answer</summary>Like `this` in JavaScript - refers to the instance</details>

5. **Q:** How do you create a list of squares in Python (one line)?
   <details><summary>Answer</summary>`squares = [x**2 for x in range(10)]`</details>

---

## 🎯 Key Takeaways

🧠 **Python vs JavaScript (SILDEF):**
- **S**nake_case vs camelCase
- **I**ndentation matters (no braces!)
- **L**ist comprehensions = map + filter
- **D**ecorators = higher-order functions
- **E**numerate = forEach with index
- **F**-strings = template literals

---

## 📚 Next Steps

1. **[Backend Architecture](./BACKEND_ARCHITECTURE.md)** - Flask patterns
2. **[Tech Stack Guide](./TECH_STACK_GUIDE.md)** - Deep dives
3. **[How-To Guide](./HOW_TO_GUIDE.md)** - Write Python code
4. **[Code Tours](./CODE_TOURS.md)** - Read real Python code

---

## 📖 Resources

- [Official Python Tutorial](https://docs.python.org/3/tutorial/)
- [Real Python](https://realpython.com/)
- [Python for JavaScript Developers](https://www.valentinog.com/blog/python-for-js/)

---

*You're now ready to write Python like a pro!* 🐍
