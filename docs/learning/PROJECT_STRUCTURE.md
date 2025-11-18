# Project Structure

## 🎯 Purpose

This guide helps you navigate the Dify codebase like a pro. By the end, you'll know:
- Where to find any piece of code
- Directory organization principles
- File naming conventions
- Mental models for quick navigation

**Time to read:** 45 minutes

---

## 🗂️ Top-Level Structure

```
dify/
├── api/                # Python/Flask backend (90% of backend logic)
├── web/                # Next.js frontend (100% of UI)
├── docker/             # Docker configurations and compose files
├── docs/               # Documentation (including this!)
├── sdks/               # Client SDKs (Python, Node.js, PHP)
├── scripts/            # Utility scripts
├── dev/                # Development tools and test runners
├── .github/            # GitHub Actions CI/CD workflows
├── Makefile            # Build automation commands
├── CLAUDE.md           # Project conventions (READ THIS!)
├── CONTRIBUTING.md     # Contribution guidelines
└── README.md           # Project overview
```

🧠 **Mental Model:** Think of the repo as **two main applications**:
- `api/` = Backend server (like a Node.js Express app)
- `web/` = Frontend app (like a Next.js or Vite project)

---

## 📁 Backend Structure (`/api`)

### High-Level Overview

```
api/
├── app.py              # ⭐ Application entry point
├── app_factory.py      # Flask app factory
├── dify_app.py         # App instance
├── gunicorn.conf.py    # Production server config
├── commands.py         # CLI commands (like artisan/manage.py)
├── celery_entrypoint.py # Celery worker entry point
│
├── configs/            # ⚙️ Configuration management
├── constants/          # 📌 Application constants
├── contexts/           # 🔗 Flask contexts and middleware
├── controllers/        # 🎮 API routes (Flask Blueprints)
├── core/               # 🧠 Core business logic
├── enums/              # 📋 Enumeration types
├── events/             # 📡 Event handlers
├── extensions/         # 🔌 Flask extensions
├── factories/          # 🏭 Factory patterns
├── fields/             # 📝 Custom fields
├── libs/               # 📚 Shared libraries
├── migrations/         # 🔄 Database migrations (Alembic)
├── models/             # 🗄️ SQLAlchemy models
├── repositories/       # 💾 Data access layer
├── services/           # 🛠️ Business services
├── tasks/              # ⚡ Celery async tasks
├── templates/          # 📧 Email/HTML templates
├── tests/              # 🧪 Unit and integration tests
│
├── .env.example        # Environment variables template
├── pyproject.toml      # Python dependencies (like package.json)
└── README.md           # Backend setup instructions
```

### Core Directories Explained

#### 1. `api/controllers/` - API Routes 🎮

Flask Blueprints organized by API type.

```
controllers/
├── console/            # Admin dashboard API
│   ├── app/           # App management endpoints
│   ├── auth/          # Authentication endpoints
│   ├── datasets/      # Dataset management
│   └── workspace/     # Workspace settings
├── web/                # User-facing web API
│   ├── app.py         # App endpoints
│   ├── conversation.py # Chat conversations
│   └── message.py     # Messages
├── service_api/        # External API for integrations
└── common/             # Shared controllers
```

🔗 **Example:** [console/app/app.py](../../api/controllers/console/app/app.py) - App CRUD endpoints

🌉 **Bridge from Express:**

```javascript
// Express (Node.js)
app.get('/api/apps', getApps)

// Flask (Dify)
@bp.route('/apps', methods=['GET'])
def get_apps():
```

#### 2. `api/core/` - Business Logic 🧠

The heart of Dify. Core engines and domain logic.

```
core/
├── agent/              # Agent orchestration
│   ├── agent_runner.py
│   └── entities/
├── app/                # Application core
│   ├── app_runner/    # App execution engine
│   └── features/       # Feature implementations
├── workflow/           # Workflow engine
│   ├── graph_engine/  # Execution graph
│   ├── nodes/         # Node implementations
│   └── callbacks/     # Event callbacks
├── rag/                # RAG (Retrieval-Augmented Generation)
│   ├── retrieval/     # Document retrieval
│   ├── index/         # Indexing pipeline
│   └── datasource/    # Data source connectors
├── model_runtime/      # LLM provider abstraction
│   ├── model_providers/ # Provider implementations
│   └── entities/       # Runtime entities
├── tools/              # Agent tools
│   ├── builtin/       # Built-in tools
│   └── provider/      # Tool providers
└── file/               # File handling
```

🔗 **Example:** [workflow/graph_engine/graph_engine.py](../../api/core/workflow/graph_engine/graph_engine.py) - Workflow execution

💡 **Aha Moment:** `core/` is where the **magic happens**! This is pure business logic, independent of Flask.

#### 3. `api/models/` - Database Models 🗄️

SQLAlchemy ORM models.

```
models/
├── __init__.py         # Model exports
├── account.py          # User and tenant models
├── dataset.py          # Dataset and document models
├── model.py            # App, conversation, message models
├── provider.py         # Provider configurations
├── workflow.py         # Workflow definitions
└── enums.py            # Model-specific enums
```

🔗 **Example:** [models/model.py](../../api/models/model.py) - App, Conversation, Message models

🌉 **Bridge from Prisma:**

```typescript
// Prisma (TypeScript)
model App {
  id String @id
  name String
}

// SQLAlchemy (Python)
class App(db.Model):
    id = db.Column(String, primary_key=True)
    name = db.Column(String, nullable=False)
```

#### 4. `api/services/` - Service Layer 🛠️

Business logic orchestration. Each service handles a specific domain.

```
services/
├── app_service.py              # App management
├── conversation_service.py     # Conversations
├── dataset_service.py          # Dataset operations
├── workflow_service.py         # Workflow operations
├── auth/                       # Authentication services
├── errors/                     # Service-specific errors
└── entities/                   # Service DTOs
```

🔗 **Example:** [workflow_service.py](../../api/services/workflow_service.py)

🎯 **Remember:** Services orchestrate core logic but don't contain it. They use `core/` for business rules.

#### 5. `api/repositories/` - Data Access 💾

Repository pattern for database queries.

```
repositories/
├── __init__.py
├── app_repository.py
├── dataset_repository.py
└── workflow_repository.py
```

🌉 **Bridge:** Like a **DAO (Data Access Object)** in Java or **Repository** in .NET.

#### 6. `api/tasks/` - Async Tasks ⚡

Celery tasks for background processing.

```
tasks/
├── annotation/         # Annotation tasks
├── rag_pipeline/       # RAG indexing tasks
└── workflow_cfs_scheduler/ # Workflow scheduler
```

🔗 **Example:** [rag_pipeline/index_processor.py](../../api/tasks/rag_pipeline/index_processor.py)

#### 7. `api/migrations/` - Database Migrations 🔄

Alembic migrations for schema changes.

```
migrations/
├── versions/           # Migration files
│   ├── 001_initial.py
│   ├── 002_add_workflow.py
│   └── ...
└── env.py              # Migration environment
```

🌉 **Bridge:** Like **Prisma migrations** or **Sequelize migrations** in Node.js.

---

## 📁 Frontend Structure (`/web`)

### High-Level Overview

```
web/
├── app/                # ⭐ Next.js App Router (all pages/routes)
├── public/             # Static assets
├── i18n/               # Internationalization files
├── i18n-config/        # i18n configuration
├── service/            # API client services
├── context/            # React contexts
├── hooks/              # Custom React hooks
├── models/             # TypeScript models/types
├── utils/              # Utility functions
├── themes/             # Theme configuration
├── config/             # App configuration
├── assets/             # Images, SVGs
├── styles/             # Global styles
├── __tests__/          # Jest tests
├── __mocks__/          # Test mocks
│
├── next.config.js      # Next.js configuration
├── tailwind.config.ts  # Tailwind CSS config
├── tsconfig.json       # TypeScript config
├── package.json        # Dependencies (like package.json)
└── .env.example        # Environment variables
```

### Core Directories Explained

#### 1. `web/app/` - Next.js App Router 📄

All pages and layouts following Next.js App Router conventions.

```
app/
├── layout.tsx          # Root layout
├── page.tsx            # Home page (redirects to /apps)
├── (commonLayout)/     # Shared layout for main app
│   ├── layout.tsx
│   ├── apps/          # Apps list page
│   ├── datasets/      # Datasets page
│   ├── plugins/       # Plugins page
│   └── tools/         # Tools page
├── (shareLayout)/      # Public share layout
│   └── share/
├── account/            # Account settings
├── signin/             # Sign in page
├── signup/             # Sign up page
├── install/            # Installation page
├── activate/           # Account activation
├── forgot-password/    # Password reset
├── components/         # ⭐ All React components
│   ├── base/          # Base UI components
│   ├── app/           # App-specific components
│   ├── workflow/      # Workflow canvas components
│   ├── datasets/      # Dataset components
│   └── header/        # Header components
└── repos/              # Repository pattern for client-side
```

🔗 **Example:** [app/(commonLayout)/apps/page.tsx](../../web/app/(commonLayout)/apps/page.tsx) - Apps list page

💡 **Aha Moment:** Routes are defined by **folder structure**! `app/apps/page.tsx` = `/apps` route.

#### 2. `web/app/components/` - React Components 🎨

The largest directory! All UI components.

```
components/
├── base/               # ⭐ Reusable UI components
│   ├── button/
│   ├── input/
│   ├── modal/
│   ├── select/
│   ├── icons/         # Icon components
│   ├── chat/          # Chat UI components
│   ├── file-uploader/
│   └── ...
├── app/                # App-specific features
│   ├── configuration/  # App configuration UI
│   ├── chat/          # Chat interface
│   └── overview/      # App overview
├── workflow/           # ⭐ Workflow canvas (React Flow)
│   ├── nodes/         # Custom workflow nodes
│   ├── panel/         # Side panels
│   ├── run/           # Workflow execution UI
│   └── hooks/         # Workflow-specific hooks
├── datasets/           # Dataset management UI
│   ├── create/
│   ├── documents/
│   └── hit-testing/
├── header/             # App header components
├── develop/            # Developer console
├── billing/            # Billing UI
├── tools/              # Tools management
└── plugins/            # Plugins UI
```

🔗 **Example:** [base/button/index.tsx](../../web/app/components/base/button/index.tsx) - Reusable button component

🎯 **Remember:** Start with `components/base/` for reusable UI, specific features in their own folders.

#### 3. `web/service/` - API Client 📡

TypeScript services for backend API calls.

```
service/
├── base.ts             # Base HTTP client (ky wrapper)
├── apps.ts             # App API calls
├── datasets.ts         # Dataset API calls
├── conversations.ts    # Chat API calls
├── workflow.ts         # Workflow API calls
├── auth.ts             # Authentication
└── common.ts           # Common utilities
```

🔗 **Example:** [service/apps.ts](../../web/service/apps.ts) - App CRUD operations

🌉 **Bridge from React Query:**

```typescript
// Using React Query with service
import { useQuery } from '@tanstack/react-query'
import { fetchApps } from '@/service/apps'

const { data } = useQuery({
  queryKey: ['apps'],
  queryFn: fetchApps,
})
```

#### 4. `web/i18n/` - Translations 🌍

Internationalization files for all supported languages.

```
i18n/
├── en-US/              # English (default)
│   ├── app.ts         # App-specific strings
│   ├── common.ts      # Common strings
│   ├── dataset.ts     # Dataset strings
│   └── workflow.ts    # Workflow strings
├── zh-Hans/            # Simplified Chinese
├── ja-JP/              # Japanese
├── ko-KR/              # Korean
└── ... (20+ languages)
```

🔗 **Example:** [i18n/en-US/app.ts](../../web/i18n/en-US/app.ts)

⚠️ **Common Pitfall:** Always use i18n keys! No hardcoded text in components.

```typescript
// ❌ Bad
<button>Create App</button>

// ✅ Good
import { useTranslation } from 'react-i18next'
const { t } = useTranslation()
<button>{t('app.createApp')}</button>
```

#### 5. `web/context/` - React Contexts 🔗

Global state using React Context API.

```
context/
├── app-context.tsx      # Current app context
├── dataset-context.tsx  # Dataset context
├── modal-context.tsx    # Modal management
└── ...
```

🔗 **Example:** [app-context.tsx](../../web/context/app-context.tsx)

#### 6. `web/hooks/` - Custom Hooks 🪝

Reusable React hooks.

```
hooks/
├── use-apps.ts          # App management hooks
├── use-datasets.ts      # Dataset hooks
├── use-conversation.ts  # Chat hooks
└── ...
```

---

## 🗃️ Docker Structure (`/docker`)

Docker configurations for local development and production.

```
docker/
├── docker-compose.yaml              # ⭐ Full stack compose (all services)
├── docker-compose.middleware.yaml   # ⭐ Dev middleware only
├── docker-compose-template.yaml     # Template for customization
├── .env.example                     # Environment variables template
├── middleware.env.example           # Middleware environment
│
├── nginx/                           # Nginx reverse proxy
│   └── nginx.conf
├── volumes/                         # Persistent data
│   ├── db/                         # PostgreSQL data
│   ├── redis/                      # Redis data
│   └── weaviate/                   # Weaviate data
├── ssrf_proxy/                      # SSRF protection proxy
├── elasticsearch/                   # Elasticsearch config
├── pgvector/                        # PostgreSQL + pgvector
└── startupscripts/                  # Initialization scripts
```

🔗 **Main Compose:** [docker-compose.middleware.yaml](../../docker/docker-compose.middleware.yaml)

---

## 📍 Where to Find...

### I want to add a new API endpoint

1. Choose API type:
   - Admin dashboard → `api/controllers/console/`
   - User-facing → `api/controllers/web/`
   - External API → `api/controllers/service_api/`

2. Create controller in appropriate directory
3. Register Blueprint in `__init__.py`
4. Add service logic in `api/services/`

🔗 **Guide:** [How-To: Add API Endpoint](./HOW_TO_GUIDE.md#add-new-api-endpoint)

### I want to add a new React component

1. Reusable UI → `web/app/components/base/`
2. Feature-specific → `web/app/components/[feature]/`
3. Create `index.tsx` in component folder
4. Export from `index.tsx`

### I want to add a new database model

1. Create model in `api/models/[domain].py`
2. Add to exports in `api/models/__init__.py`
3. Create migration: `uv run flask db migrate -m "Add new model"`
4. Review migration in `api/migrations/versions/`
5. Apply: `uv run flask db upgrade`

### I want to add a new Celery task

1. Create task in `api/tasks/[domain]/`
2. Decorate with `@shared_task`
3. Import in task module's `__init__.py`
4. Call with `.delay()` or `.apply_async()`

### I want to add translations

1. Add English in `web/i18n/en-US/[file].ts`
2. Run `pnpm auto-gen-i18n` to auto-translate
3. Or manually add to each language folder

---

## 🎯 File Naming Conventions

### Backend (Python)

- **Files:** `snake_case.py` (e.g., `app_service.py`)
- **Classes:** `PascalCase` (e.g., `class AppService`)
- **Functions:** `snake_case` (e.g., `def get_app()`)
- **Constants:** `UPPER_SNAKE_CASE` (e.g., `MAX_FILE_SIZE`)

### Frontend (TypeScript)

- **Files:** `kebab-case.tsx` (e.g., `app-card.tsx`)
- **Components:** `PascalCase` (e.g., `function AppCard()`)
- **Hooks:** `camelCase` starting with `use` (e.g., `useApps`)
- **Types:** `PascalCase` (e.g., `type App = {...}`)
- **Constants:** `UPPER_SNAKE_CASE` (e.g., `const API_URL`)

---

## 🧭 Navigation Tips

### Quick File Lookup

**Backend:**
```bash
# Find a service
find api/services -name "*app*"

# Find a model
find api/models -name "*.py"

# Find a controller
find api/controllers -name "*chat*"
```

**Frontend:**
```bash
# Find a component
find web/app/components -name "*button*"

# Find a hook
find web/hooks -name "use-*.ts"

# Find i18n files
find web/i18n/en-US -name "*.ts"
```

### IDE Navigation

**VS Code shortcuts:**
- `Cmd/Ctrl + P` - Quick file open
- `Cmd/Ctrl + Shift + F` - Search in files
- `Cmd/Ctrl + Click` - Go to definition

**Search patterns:**
```
# Find all services
api/services/**/*.py

# Find all React components
web/app/components/**/index.tsx

# Find all models
api/models/*.py
```

---

## ✅ Quick Check

Test your navigation skills:

1. **Q:** Where would you find the Chat message API endpoint?
   <details>
   <summary>Answer</summary>
   `api/controllers/web/message.py`
   </details>

2. **Q:** Where are the workflow node implementations?
   <details>
   <summary>Answer</summary>
   `api/core/workflow/nodes/`
   </details>

3. **Q:** Where would you add a new base button component?
   <details>
   <summary>Answer</summary>
   `web/app/components/base/button/`
   </details>

4. **Q:** Where are database migrations stored?
   <details>
   <summary>Answer</summary>
   `api/migrations/versions/`
   </details>

5. **Q:** Where would you add English translations for workflows?
   <details>
   <summary>Answer</summary>
   `web/i18n/en-US/workflow.ts`
   </details>

---

## 🎯 Key Takeaways

🧠 **Remember This (CASMRT):**
- **C**ontrollers = API routes (like Express routers)
- **A**pp router = pages by folder structure
- **S**ervices = business logic orchestration
- **M**odels = database tables (SQLAlchemy)
- **R**epositories = data access
- **T**asks = async background jobs (Celery)

---

## 📚 Next Steps

Now that you know where everything is:

1. **[Tech Stack Guide](./TECH_STACK_GUIDE.md)** - Deep dive into technologies
2. **[Backend Architecture](./BACKEND_ARCHITECTURE.md)** - Flask/DDD patterns
3. **[Frontend Architecture](./FRONTEND_ARCHITECTURE.md)** - Next.js/React patterns
4. **[How-To Guide](./HOW_TO_GUIDE.md)** - Practical code examples

---

*Navigate like a pro! The codebase is your playground.* 🗺️
