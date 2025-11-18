# Tech Stack Guide

## 🎯 Purpose

This guide explains **every major technology** in Dify's stack, comparing them to JavaScript/TypeScript equivalents you already know. By the end, you'll understand:
- What each technology does and why it was chosen
- How it compares to Node.js/React alternatives
- Key concepts you need to know
- Common patterns in Dify

**Time to read:** 2 hours (skim what you know, deep-dive what's new)

---

## 📚 Table of Contents

- [Frontend Stack](#frontend-stack)
  - [Next.js 15](#nextjs-15)
  - [React 19](#react-19)
  - [TypeScript](#typescript)
  - [Tailwind CSS](#tailwind-css)
  - [Zustand](#zustand)
  - [React Query](#react-query)
  - [React Flow](#react-flow)
- [Backend Stack](#backend-stack)
  - [Python 3.11](#python-311)
  - [Flask 3](#flask-3)
  - [SQLAlchemy 2](#sqlalchemy-2)
  - [Pydantic 2](#pydantic-2)
  - [Celery 5](#celery-5)
  - [uv](#uv)
- [Databases](#databases)
  - [PostgreSQL](#postgresql)
  - [Redis](#redis)
  - [Vector Databases](#vector-databases)
- [Infrastructure](#infrastructure)
  - [Docker](#docker)
  - [Gunicorn](#gunicorn)
  - [Gevent](#gevent)

---

## 🎨 Frontend Stack

### Next.js 15

**What it is:** React framework with server-side rendering, routing, and App Router.

**Version in Dify:** 15.5.6 (latest stable with App Router)

🌉 **Equivalent in JS/TS world:**
- Like **Create React App** but with SSR
- Like **Remix** or **SvelteKit**
- Supersedes traditional **React Router**

**Why Dify chose Next.js:**
- ✅ Server-Side Rendering (SSR) for better performance
- ✅ App Router with React Server Components
- ✅ Built-in API routes (though Dify uses separate Flask backend)
- ✅ Image optimization
- ✅ File-based routing
- ✅ Great TypeScript support

**Key Concepts:**

1. **App Router vs Pages Router**
   ```
   App Router (Dify uses this):
   app/
   ├── layout.tsx       # Layout wrapper
   ├── page.tsx         # Page component
   └── apps/
       └── page.tsx     # /apps route

   Pages Router (old):
   pages/
   ├── index.tsx        # / route
   └── apps.tsx         # /apps route
   ```

2. **Server Components vs Client Components**
   ```typescript
   // Server Component (default in App Router)
   // Runs on server, can fetch data directly
   export default async function AppPage() {
     const apps = await fetchApps() // runs on server
     return <div>{apps.map(...)}</div>
   }

   // Client Component (needs 'use client')
   'use client'
   export default function InteractiveButton() {
     const [count, setCount] = useState(0)
     return <button onClick={() => setCount(count + 1)}>{count}</button>
   }
   ```

🔗 **Example:** [web/app/(commonLayout)/apps/page.tsx](../../web/app/(commonLayout)/apps/page.tsx)

💡 **Aha Moment:** Server Components let you fetch data on the server, reducing client bundle size!

⚠️ **Common Pitfall:** Can't use React hooks in Server Components. Mark components with `'use client'` if you need state/effects.

**Resources:**
- [Next.js App Router Docs](https://nextjs.org/docs/app)
- [React Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components)

---

### React 19

**What it is:** JavaScript library for building user interfaces.

**Version in Dify:** 19.1.1 (latest stable)

🌉 **You already know this!** React 19 is just the latest version with:
- React Server Components
- Actions (form handling improvements)
- `use()` hook for promises
- Better Suspense support

**What's new in React 19:**

```typescript
// Old way (React 18)
const [isPending, startTransition] = useTransition()
startTransition(() => {
  setTab('about')
})

// New way (React 19 Actions)
import { useActionState } from 'react'
const [state, formAction] = useActionState(updateProfile, initialState)
<form action={formAction}>
```

🔗 **Example:** Dify components in [web/app/components/base/](../../web/app/components/base/)

---

### TypeScript

**What it is:** Typed superset of JavaScript.

**Version in Dify:** 5.9.3

🌉 **You already know this!** Dify uses strict mode:

```json
// web/tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}
```

⚠️ **Common Pitfall:** Dify enforces strict typing. No `any` types!

```typescript
// ❌ Bad
function doSomething(data: any) { }

// ✅ Good
interface AppData {
  id: string
  name: string
}
function doSomething(data: AppData) { }
```

---

### Tailwind CSS

**What it is:** Utility-first CSS framework.

**Version in Dify:** 3.4.18

🌉 **Equivalent:** Like **styled-components** or **emotion**, but utility classes instead of CSS-in-JS.

**Why Dify chose Tailwind:**
- ✅ Consistent design system
- ✅ No CSS file bloat
- ✅ Great with component libraries
- ✅ Purges unused styles in production

**Example:**

```typescript
// Instead of:
<button className="primary-button">Click</button>
// with: .primary-button { background: blue; padding: 8px; ... }

// Tailwind:
<button className="bg-blue-500 hover:bg-blue-600 px-4 py-2 rounded">
  Click
</button>
```

🔗 **Config:** [web/tailwind.config.ts](../../web/tailwind.config.ts)

---

### Zustand

**What it is:** Lightweight state management library.

**Version in Dify:** 4.5.7

🌉 **Equivalent:**
- Simpler than **Redux**
- Similar to **Jotai** or **Valtio**
- Less boilerplate than **Redux Toolkit**

**Why Dify chose Zustand:**
- ✅ Simple API (no providers needed)
- ✅ TypeScript-first
- ✅ Minimal boilerplate
- ✅ Good DevTools support

**Example:**

```typescript
// Create store
import { create } from 'zustand'

interface AppStore {
  apps: App[]
  setApps: (apps: App[]) => void
}

const useAppStore = create<AppStore>((set) => ({
  apps: [],
  setApps: (apps) => set({ apps }),
}))

// Use in component
function AppsList() {
  const apps = useAppStore(state => state.apps)
  const setApps = useAppStore(state => state.setApps)

  return <div>{apps.map(...)}</div>
}
```

🔗 **Example Store:** [web/app/components/app/store.ts](../../web/app/components/app/store.ts)

💡 **Aha Moment:** No Provider wrapper needed! Just import and use.

---

### React Query (TanStack Query)

**What it is:** Data fetching and caching library.

**Version in Dify:** 5.90.5

🌉 **Equivalent:**
- Better than **useEffect + fetch**
- Like **SWR** but more powerful
- Replaces **Redux** for server state

**Why Dify chose React Query:**
- ✅ Automatic caching
- ✅ Background refetching
- ✅ Optimistic updates
- ✅ Pagination/infinite scroll support

**Example:**

```typescript
import { useQuery, useMutation } from '@tanstack/react-query'

// Fetch data
const { data, isLoading } = useQuery({
  queryKey: ['apps'],
  queryFn: fetchApps,
})

// Mutate data
const mutation = useMutation({
  mutationFn: createApp,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['apps'] })
  },
})
```

🔗 **Example:** Check components using `useQuery` in [web/app/components/](../../web/app/components/)

---

### React Flow

**What it is:** Library for building node-based editors and diagrams.

**Version in Dify:** 11.11.4

🌉 **Equivalent:**
- Like **D3.js** but for interactive node graphs
- Similar to **GoJS** or **JointJS**

**Why Dify chose React Flow:**
- ✅ Perfect for workflow visual programming
- ✅ Custom node/edge support
- ✅ Built-in zooming/panning
- ✅ Great performance

**Example:**

```typescript
import ReactFlow, { Node, Edge } from 'reactflow'

const nodes: Node[] = [
  { id: '1', data: { label: 'Start' }, position: { x: 0, y: 0 } },
  { id: '2', data: { label: 'LLM' }, position: { x: 0, y: 100 } },
]

const edges: Edge[] = [
  { id: 'e1-2', source: '1', target: '2' },
]

<ReactFlow nodes={nodes} edges={edges} />
```

🔗 **Workflow Canvas:** [web/app/components/workflow/](../../web/app/components/workflow/)

---

## 🐍 Backend Stack

### Python 3.11

**What it is:** High-level programming language.

**Version in Dify:** 3.11 or 3.12 (not 3.13+)

🌉 **For JavaScript developers:**

| Python | JavaScript |
|--------|-----------|
| `def func():` | `function func() {}` |
| `list = [1, 2, 3]` | `const list = [1, 2, 3]` |
| `dict = {"key": "value"}` | `const obj = {key: "value"}` |
| `for item in list:` | `for (const item of list) {}` |
| `lambda x: x + 1` | `(x) => x + 1` |
| `async def func():` | `async function func() {}` |
| `with open() as f:` | `using (try/finally pattern)` |

**Key Python concepts:**

1. **Type Hints** (like TypeScript)
   ```python
   def greet(name: str) -> str:
       return f"Hello, {name}"
   ```

2. **List Comprehensions** (like map/filter)
   ```python
   # Python
   squares = [x**2 for x in range(10)]

   # JavaScript equivalent
   const squares = Array.from({length: 10}, (_, x) => x**2)
   ```

3. **Decorators** (like Higher-Order Functions)
   ```python
   @app.route('/api/apps')
   def get_apps():
       return apps

   # Like:
   app.get('/api/apps', getApps)
   ```

🔗 **Deep Dive:** [Python for JavaScript Developers](./PYTHON_FOR_JS_DEVELOPERS.md)

---

### Flask 3

**What it is:** Lightweight web framework for Python.

**Version in Dify:** 3.1.2

🌉 **Equivalent:**
- Like **Express.js** for Node.js
- Simpler than **Django**
- More flexible than **FastAPI** for this use case

**Why Dify chose Flask:**
- ✅ Simple and unopinionated
- ✅ Great SQLAlchemy integration
- ✅ Mature ecosystem (15+ years)
- ✅ Perfect for API-first design
- ✅ Excellent extensions

**Comparison:**

```python
# Flask (Dify)
from flask import Flask, request, jsonify

app = Flask(__name__)

@app.route('/api/apps', methods=['GET'])
def get_apps():
    apps = App.query.all()
    return jsonify([app.to_dict() for app in apps])
```

```javascript
// Express.js (Node.js)
import express from 'express'

const app = express()

app.get('/api/apps', async (req, res) => {
  const apps = await App.findAll()
  res.json(apps.map(app => app.toJSON()))
})
```

🔗 **Flask App:** [api/app_factory.py](../../api/app_factory.py)

**Flask vs FastAPI vs Django:**

| Feature | Flask | FastAPI | Django |
|---------|-------|---------|--------|
| Speed | Fast | Fastest | Slower |
| Async | Limited (gevent) | Native | Limited |
| Learning Curve | Easy | Medium | Hard |
| Batteries Included | No | Some | Yes |
| API Docs | Manual | Auto-generated | Manual |
| ORM | Separate (SQLAlchemy) | Separate | Built-in (Django ORM) |

**Why not FastAPI?** Dify uses Celery for async tasks, not native async/await, so Flask's battle-tested ecosystem wins.

---

### SQLAlchemy 2

**What it is:** Python ORM (Object-Relational Mapper).

**Version in Dify:** 2.0.29

🌉 **Equivalent:**
- Like **Prisma** for Node.js/TypeScript
- Similar to **TypeORM** or **Sequelize**
- More powerful than **Drizzle**

**Why Dify chose SQLAlchemy:**
- ✅ Industry standard Python ORM
- ✅ Type-safe queries
- ✅ Relationship management
- ✅ Migration support (Alembic)
- ✅ Multi-database support

**Comparison:**

```python
# SQLAlchemy (Python)
from sqlalchemy import select

class App(db.Model):
    __tablename__ = 'apps'
    id = db.Column(db.String, primary_key=True)
    name = db.Column(db.String, nullable=False)
    tenant_id = db.Column(db.String, db.ForeignKey('tenants.id'))

    conversations = db.relationship('Conversation', back_populates='app')

# Query
stmt = select(App).where(App.tenant_id == current_tenant_id)
apps = db.session.execute(stmt).scalars().all()
```

```typescript
// Prisma (TypeScript)
model App {
  id            String   @id
  name          String
  tenantId      String
  tenant        Tenant   @relation(fields: [tenantId], references: [id])
  conversations Conversation[]
}

// Query
const apps = await prisma.app.findMany({
  where: { tenantId: currentTenantId }
})
```

🔗 **Models:** [api/models/model.py](../../api/models/model.py)

💡 **Aha Moment:** SQLAlchemy relationships are like Prisma relations - bidirectional and auto-joining!

---

### Pydantic 2

**What it is:** Data validation library using Python type hints.

**Version in Dify:** 2.11.4

🌉 **Equivalent:**
- Like **Zod** for TypeScript
- Similar to **Yup** or **Joi**
- Better than **class-validator**

**Why Dify chose Pydantic:**
- ✅ Type-safe validation
- ✅ Automatic parsing
- ✅ Great error messages
- ✅ Fast (Rust-powered in v2)

**Comparison:**

```python
# Pydantic (Python)
from pydantic import BaseModel, Field

class CreateAppRequest(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    description: str | None = None
    mode: str = Field(..., pattern="^(chat|workflow)$")

# Auto-validates
request_data = CreateAppRequest(**request.json)
```

```typescript
// Zod (TypeScript)
import { z } from 'zod'

const CreateAppRequest = z.object({
  name: z.string().min(1).max(100),
  description: z.string().optional(),
  mode: z.enum(['chat', 'workflow']),
})

// Validate
const requestData = CreateAppRequest.parse(req.body)
```

🔗 **Example:** [api/controllers/console/app/app.py](../../api/controllers/console/app/app.py)

---

### Celery 5

**What it is:** Distributed task queue for Python.

**Version in Dify:** 5.5.2

🌉 **Equivalent:**
- Like **BullMQ** or **Bull** for Node.js
- Similar to **Agenda** or **Bee-Queue**
- More mature than **Quirrel**

**Why Dify chose Celery:**
- ✅ Battle-tested (10+ years)
- ✅ Distributed workers
- ✅ Retry logic
- ✅ Scheduled tasks (like cron)
- ✅ Result backend

**Comparison:**

```python
# Celery (Python)
from celery import shared_task

@shared_task
def index_document(document_id: str):
    # Long-running task
    document = Document.query.get(document_id)
    embeddings = generate_embeddings(document.content)
    store_embeddings(embeddings)

# Call async
index_document.delay(doc_id)
```

```typescript
// BullMQ (Node.js)
import { Queue } from 'bullmq'

const documentQueue = new Queue('documents')

async function indexDocument(documentId: string) {
  const document = await Document.findById(documentId)
  const embeddings = await generateEmbeddings(document.content)
  await storeEmbeddings(embeddings)
}

// Call async
await documentQueue.add('index', { documentId })
```

🔗 **Tasks:** [api/tasks/rag_pipeline/](../../api/tasks/rag_pipeline/)

💡 **Aha Moment:** Celery + Redis = BullMQ + Redis. Same architecture, different language!

---

### uv

**What it is:** Fast Python package manager (written in Rust).

**Version:** Latest stable

🌉 **Equivalent:**
- Like **pnpm** for Node.js
- Faster than **pip**
- Similar to **Poetry** but faster

**Why Dify chose uv:**
- ✅ 10-100x faster than pip
- ✅ Better dependency resolution
- ✅ Lock files (like pnpm-lock.yaml)
- ✅ Virtual environment management

**Comparison:**

```bash
# uv (Dify)
uv sync                  # Install dependencies
uv run flask run         # Run with venv activated
uv add requests          # Add package

# pnpm (Node.js)
pnpm install             # Install dependencies
pnpm run dev             # Run script
pnpm add axios           # Add package
```

🔗 **Config:** [api/pyproject.toml](../../api/pyproject.toml)

---

## 🗄️ Databases

### PostgreSQL

**What it is:** Relational database.

**Why Dify chose PostgreSQL:**
- ✅ pgvector extension for vector search
- ✅ JSON support (like MongoDB)
- ✅ Full-text search
- ✅ ACID compliance
- ✅ Battle-tested scalability

🔗 **Schema:** [Database Schema Reference](./DATABASE_SCHEMA.md)

---

### Redis

**What it is:** In-memory data store.

**Version in Dify:** 6.1.0

**Used for:**
1. **Caching** (session data, query results)
2. **Celery message broker**
3. **Real-time features** (WebSocket pub/sub)

🌉 **Same as in Node.js!** Redis usage is identical across languages.

---

### Vector Databases

**What they are:** Databases optimized for similarity search on embeddings.

**Supported in Dify:**
- **Weaviate** (default for local dev)
- **Qdrant**
- **Milvus**
- **Chroma**
- **pgvector** (PostgreSQL extension)
- **Elasticsearch**

**Why needed:** LLM embeddings are 1536-dimensional vectors. Traditional databases can't efficiently search them.

🔗 **Deep Dive:** [Vector Databases Explained](./VECTOR_DATABASES.md)

---

## 🏗️ Infrastructure

### Docker

**What it is:** Containerization platform.

**Used for:**
- Local development (PostgreSQL, Redis, Weaviate)
- Production deployment

🔗 **Compose:** [docker/docker-compose.middleware.yaml](../../docker/docker-compose.middleware.yaml)

---

### Gunicorn

**What it is:** Python WSGI server for production.

🌉 **Equivalent:** Like **PM2** for Node.js

**Why needed:** Flask's built-in server is development-only. Gunicorn handles:
- Multiple workers
- Process management
- Graceful restarts

🔗 **Config:** [api/gunicorn.conf.py](../../api/gunicorn.conf.py)

---

### Gevent

**What it is:** Coroutine-based networking library.

🌉 **Equivalent:** Like Node.js's event loop, but for Python

**Why Dify uses it:**
- ✅ Async I/O without async/await syntax
- ✅ Green threads (lightweight)
- ✅ Works well with Flask

💡 **Aha Moment:** Gevent monkey-patches Python's standard library to make blocking operations non-blocking!

---

## ✅ Quick Check

1. **Q:** What's the TypeScript equivalent of Pydantic?
   <details><summary>Answer</summary>Zod</details>

2. **Q:** What's the Python equivalent of pnpm?
   <details><summary>Answer</summary>uv</details>

3. **Q:** What's the Python equivalent of BullMQ?
   <details><summary>Answer</summary>Celery</details>

4. **Q:** Why use Zustand over Redux?
   <details><summary>Answer</summary>Simpler API, less boilerplate, no providers needed</details>

---

## 🎯 Key Takeaways

🧠 **Stack Summary (FNPSCR):**
- **F**lask = Express for Python
- **N**ext.js 15 = SSR + App Router
- **P**ydantic = Zod for Python
- **S**QLAlchemy = Prisma for Python
- **C**elery = BullMQ for Python
- **R**eact Query = Server state management

---

## 📚 Next Steps

1. **[Data Flow Guide](./DATA_FLOW_GUIDE.md)** - See technologies in action
2. **[Python for JS Developers](./PYTHON_FOR_JS_DEVELOPERS.md)** - Python crash course
3. **[Backend Architecture](./BACKEND_ARCHITECTURE.md)** - Flask patterns
4. **[Frontend Architecture](./FRONTEND_ARCHITECTURE.md)** - Next.js patterns

---

*Understanding the stack is key to confident development!* 🚀
