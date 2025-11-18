# Architecture Overview

## 🎯 Purpose

This guide provides a high-level understanding of Dify's architecture. By the end, you'll understand:
- How the system components fit together
- The request lifecycle from user to LLM
- Why specific technologies were chosen
- How multi-tenancy works
- The role of each major component

**Time to read:** 60 minutes

---

## 🏗️ System Architecture Diagram

```mermaid
graph TB
    subgraph "User Interfaces"
        Browser[Web Browser]
        Mobile[Mobile App]
        API_Client[API Clients]
    end

    subgraph "Frontend Layer - Next.js 15"
        NextJS[Next.js App Router<br/>React 19 + TypeScript]
        SSR[Server Components]
        CSR[Client Components]
        NextJS --> SSR
        NextJS --> CSR
    end

    subgraph "API Layer - Flask 3"
        WebAPI[Web API<br/>User-facing]
        ConsoleAPI[Console API<br/>Admin Dashboard]
        ServiceAPI[Service API<br/>External Integration]
        WebAPI --> BL
        ConsoleAPI --> BL
        ServiceAPI --> BL
    end

    subgraph "Business Logic Layer"
        BL[Service Layer<br/>DDD Pattern]
        BL --> WorkflowEngine
        BL --> RAGPipeline
        BL --> AgentOrch
        BL --> ModelRuntime
    end

    subgraph "Core Engines"
        WorkflowEngine[Workflow Engine]
        RAGPipeline[RAG Pipeline]
        AgentOrch[Agent Orchestration]
        ModelRuntime[Model Runtime]
    end

    subgraph "Data Layer"
        Repos[Repositories<br/>Data Access Layer]
        Models[SQLAlchemy Models]
        Repos --> Models
    end

    subgraph "Infrastructure - Docker"
        PostgreSQL[(PostgreSQL<br/>+ pgvector)]
        Redis[(Redis<br/>Cache + Broker)]
        Weaviate[(Weaviate<br/>Vector DB)]
        Celery[Celery Workers<br/>Background Tasks]
    end

    subgraph "External Services"
        LLM[LLM Providers<br/>OpenAI, Anthropic, etc.]
        Storage[Object Storage<br/>S3, Azure, etc.]
        Plugins[Tools & Plugins]
    end

    Browser --> NextJS
    Mobile --> NextJS
    API_Client --> ServiceAPI

    NextJS -->|HTTP/JSON| WebAPI
    NextJS -->|HTTP/JSON| ConsoleAPI

    BL --> Repos
    Models --> PostgreSQL

    Celery -.->|async tasks| BL
    Redis -.->|message queue| Celery
    Redis -.->|cache| BL

    WorkflowEngine --> ModelRuntime
    RAGPipeline --> Weaviate
    AgentOrch --> Plugins
    ModelRuntime --> LLM
    BL --> Storage

    style NextJS fill:#61dafb
    style BL fill:#90EE90
    style PostgreSQL fill:#336791
    style Redis fill:#DC382D
    style Celery fill:#37814A
```

🧠 **Mental Model:** Think of Dify as a **three-layer cake**:
1. **Top Layer (Frontend):** User interface - Next.js/React
2. **Middle Layer (Backend):** Business logic - Flask/Python
3. **Bottom Layer (Data):** Storage - PostgreSQL/Redis/Vector DBs

---

## 🔄 Request Lifecycle

Let's trace a chat message from the user to an LLM response:

```mermaid
sequenceDiagram
    actor User
    participant Browser
    participant Next.js
    participant Flask API
    participant Service Layer
    participant Workflow Engine
    participant Model Runtime
    participant LLM Provider
    participant PostgreSQL
    participant Redis

    User->>Browser: Types message
    Browser->>Next.js: POST /api/chat
    Next.js->>Flask API: POST /v1/chat-messages

    Flask API->>Service Layer: handle_chat()
    Service Layer->>PostgreSQL: Load conversation

    alt Check Cache
        Service Layer->>Redis: Check cached response
        Redis-->>Service Layer: Cache miss
    end

    Service Layer->>Workflow Engine: Execute workflow
    Workflow Engine->>Model Runtime: Prepare LLM call
    Model Runtime->>LLM Provider: Send prompt

    LLM Provider-->>Model Runtime: Stream response
    Model Runtime-->>Workflow Engine: Process tokens
    Workflow Engine-->>Service Layer: Return result

    Service Layer->>PostgreSQL: Save message
    Service Layer->>Redis: Cache response

    Service Layer-->>Flask API: SSE Stream
    Flask API-->>Next.js: SSE Stream
    Next.js-->>Browser: Update UI
    Browser-->>User: Display response
```

💡 **Aha Moment:** The response **streams** back through all layers in real-time! Just like streaming in Node.js, but using Server-Sent Events (SSE).

---

## 🏛️ Clean Architecture Layers

Dify follows **Clean Architecture** principles with clear separation of concerns:

```mermaid
graph TB
    subgraph "1. Presentation Layer"
        Controllers[Controllers/Routes<br/>Flask Blueprints]
    end

    subgraph "2. Application Layer"
        Services[Services<br/>Business Logic]
    end

    subgraph "3. Domain Layer"
        Entities[Entities/Models<br/>Business Rules]
        Events[Domain Events]
    end

    subgraph "4. Infrastructure Layer"
        Repos[Repositories<br/>Data Access]
        External[External APIs<br/>LLM, Storage, etc.]
    end

    Controllers --> Services
    Services --> Entities
    Services --> Events
    Services --> Repos
    Services --> External
    Repos --> Entities

    style Controllers fill:#FFE4B5
    style Services fill:#90EE90
    style Entities fill:#87CEEB
    style Repos fill:#DDA0DD
```

🌉 **Bridge from Node.js:**

| Dify Layer | Node.js Equivalent | Example |
|------------|-------------------|---------|
| Controllers | Express Routes | `app.post('/api/chat', handler)` |
| Services | Service Classes | `ChatService.sendMessage()` |
| Entities | Domain Models | `class Message { ... }` |
| Repositories | Data Access | `MessageRepository.save()` |

🔗 **Code Example:** See [app_service.py](../../api/services/app_service.py#L1-L50) for Service Layer pattern

---

## 🧩 Core Components Explained

### 1. Frontend (Next.js + React)

**Location:** `/web`

**Key Technologies:**
- Next.js 15 (App Router with Server Components)
- React 19 (Server + Client Components)
- Zustand (Global State)
- React Query (Server State)
- React Flow (Workflow Canvas)

**Responsibilities:**
- User interface rendering
- State management
- API communication
- Real-time updates (SSE)

🔗 **Entry Point:** [web/app/layout.tsx](../../web/app/layout.tsx)

🌉 **Bridge from React:** If you've used Create React App or Vite, Next.js App Router is similar but with server-side rendering (SSR) and React Server Components (RSC) for better performance.

### 2. Backend API (Flask)

**Location:** `/api`

**Key Technologies:**
- Flask 3 (Web Framework)
- SQLAlchemy 2 (ORM)
- Pydantic 2 (Validation)
- Celery 5 (Background Tasks)

**Responsibilities:**
- Request handling
- Business logic orchestration
- Authentication/Authorization
- Database operations
- Task scheduling

🔗 **Entry Point:** [api/app.py](../../api/app.py#L23-L29)

### 3. Workflow Engine

**Location:** `/api/core/workflow`

The workflow engine is Dify's visual programming system for LLM applications.

**Key Features:**
- Node-based execution graph
- Variable passing between nodes
- Conditional logic
- Error handling
- Streaming support

🔗 **Core Engine:** [api/core/workflow/graph_engine/graph_engine.py](../../api/core/workflow/graph_engine/graph_engine.py)

🧠 **Mental Model:** Think of it like **Zapier or n8n** - visual automation for LLM calls.

### 4. RAG Pipeline

**Location:** `/api/core/rag`

The RAG (Retrieval-Augmented Generation) pipeline handles:
1. Document ingestion and chunking
2. Embedding generation
3. Vector storage
4. Semantic search
5. Context injection

```mermaid
graph LR
    A[Document Upload] --> B[Text Extraction]
    B --> C[Chunking]
    C --> D[Embedding]
    D --> E[Vector Storage]

    F[User Query] --> G[Query Embedding]
    G --> H[Vector Search]
    H --> I[Retrieve Chunks]
    I --> J[Inject Context]
    J --> K[LLM Call]
```

🔗 **Pipeline Code:** [api/core/rag/retrieval/dataset_retrieval.py](../../api/core/rag/retrieval/dataset_retrieval.py)

### 5. Agent Orchestration

**Location:** `/api/core/agent`

Handles autonomous agents that can:
- Call tools/functions
- Make decisions
- Chain multiple LLM calls
- React to results

🔗 **Agent Runner:** [api/core/agent/agent_runner.py](../../api/core/agent/agent_runner.py)

🌉 **Bridge:** Similar to **LangChain agents** or **AutoGPT** - autonomous task execution.

### 6. Model Runtime

**Location:** `/api/core/model_runtime`

Abstraction layer for LLM providers:
- OpenAI (GPT-4, GPT-3.5)
- Anthropic (Claude)
- Google (Gemini, PaLM)
- Azure OpenAI
- Open-source models (via Ollama, vLLM)

🔗 **Runtime Interface:** [api/core/model_runtime/model_providers](../../api/core/model_runtime/model_providers)

💡 **Aha Moment:** One interface, 50+ LLM providers! Switch models without changing code.

---

## 🗄️ Data Architecture

### Database Schema Overview

```mermaid
erDiagram
    TENANT ||--o{ APP : owns
    TENANT ||--o{ ACCOUNT : has
    ACCOUNT ||--o{ ACCOUNT_TENANT_JOIN : belongs_to
    ACCOUNT_TENANT_JOIN }o--|| TENANT : links

    APP ||--o{ CONVERSATION : contains
    APP ||--o{ WORKFLOW : has
    APP ||--o{ DATASET_JOIN : uses

    CONVERSATION ||--o{ MESSAGE : contains
    MESSAGE ||--o{ MESSAGE_FILE : has
    MESSAGE ||--o{ MESSAGE_ANNOTATION : has

    DATASET ||--o{ DOCUMENT : contains
    DOCUMENT ||--o{ DOCUMENT_SEGMENT : split_into
    DOCUMENT_SEGMENT ||--o{ EMBEDDING : has

    DATASET_JOIN }o--|| DATASET : links
```

🔗 **Models:** [api/models/\__init\_\_.py](../../api/models/__init__.py#L1-L50)

### Multi-Tenancy Model

Dify is **multi-tenant from day one**. Each tenant has isolated:
- Apps
- Conversations
- Datasets
- API keys

**Tenant Isolation:**
- All queries filter by `tenant_id`
- Row-level security
- Separate API tokens per tenant

🔗 **Tenant Model:** [api/models/account.py](../../api/models/account.py)

🌉 **Bridge:** Like Slack workspaces or GitHub organizations - each tenant is completely isolated.

---

## ⚡ Async Architecture (Celery)

Dify uses Celery for long-running tasks:

```mermaid
graph LR
    A[Flask API] -->|enqueue| B[Redis Queue]
    B --> C[Celery Worker 1]
    B --> D[Celery Worker 2]
    B --> E[Celery Worker N]

    C --> F[Task: Index Document]
    D --> G[Task: Generate Embedding]
    E --> H[Task: Process File]

    F --> I[(PostgreSQL)]
    G --> J[(Vector DB)]
    H --> K[(Object Storage)]
```

**Common async tasks:**
- Document indexing
- Embedding generation
- File processing
- Batch operations
- Scheduled jobs

🔗 **Task Definitions:** [api/tasks/](../../api/tasks/)

🌉 **Bridge from Node.js:**

| Python (Dify) | Node.js Equivalent |
|---------------|-------------------|
| Celery | Bull, BullMQ |
| Redis broker | Redis queue |
| Celery worker | Worker process |
| Task | Job |

---

## 🔐 Authentication & Authorization

### Authentication Flow

```mermaid
sequenceDiagram
    actor User
    participant Frontend
    participant API
    participant Database
    participant JWT

    User->>Frontend: Login (email/password)
    Frontend->>API: POST /console/api/login
    API->>Database: Verify credentials
    Database-->>API: User found
    API->>JWT: Generate token
    JWT-->>API: Access token + Refresh token
    API-->>Frontend: Tokens
    Frontend->>Frontend: Store in httpOnly cookie

    User->>Frontend: Make API request
    Frontend->>API: Request + Cookie (token)
    API->>JWT: Verify token
    JWT-->>API: Valid + tenant_id
    API->>Database: Query with tenant_id
    Database-->>API: Filtered data
    API-->>Frontend: Response
```

🔗 **Auth Service:** [api/controllers/console/auth/login.py](../../api/controllers/console/auth/login.py)

⚠️ **Common Pitfall:** Always filter by `tenant_id` in queries to prevent cross-tenant data leaks!

---

## 🎨 Why These Technologies?

### Why Flask instead of FastAPI or Express?

**Flask Pros:**
- ✅ Mature ecosystem (15+ years)
- ✅ Flexible and unopinionated
- ✅ Great ORM support (SQLAlchemy)
- ✅ Extensive extensions
- ✅ Large community

**FastAPI would be great for:**
- ❌ Full async/await (but Dify uses Celery for async)
- ❌ Auto-generated API docs (Dify has custom docs)

### Why Next.js App Router instead of Pages Router?

**App Router Pros:**
- ✅ React Server Components (better performance)
- ✅ Streaming SSR
- ✅ Improved layouts
- ✅ Better TypeScript support
- ✅ Future of Next.js

### Why SQLAlchemy instead of raw SQL?

**SQLAlchemy Pros:**
- ✅ Type safety
- ✅ Relationship management
- ✅ Migration support
- ✅ Multi-database support
- ✅ Query builder (like Prisma in Node.js)

### Why Celery instead of just async/await?

**Celery Pros:**
- ✅ Distributed task queue
- ✅ Retry logic
- ✅ Task scheduling
- ✅ Result backend
- ✅ Scalability (multiple workers)

🌉 **Bridge:** Celery is to Python what **BullMQ** is to Node.js - persistent, distributed task queues.

---

## 🚀 Scalability Considerations

### Horizontal Scaling

```mermaid
graph TB
    LB[Load Balancer]

    subgraph "API Servers"
        API1[Flask API 1]
        API2[Flask API 2]
        API3[Flask API N]
    end

    subgraph "Celery Workers"
        W1[Worker 1]
        W2[Worker 2]
        W3[Worker N]
    end

    subgraph "Databases"
        PG[(PostgreSQL<br/>Read Replicas)]
        Redis[(Redis Cluster)]
        VDB[(Vector DB<br/>Sharded)]
    end

    LB --> API1
    LB --> API2
    LB --> API3

    API1 --> PG
    API2 --> PG
    API3 --> PG

    API1 --> Redis
    API2 --> Redis
    API3 --> Redis

    Redis --> W1
    Redis --> W2
    Redis --> W3
```

**Scaling strategies:**
1. **Frontend:** Edge deployment (Vercel, Cloudflare)
2. **API:** Horizontal scaling (multiple Flask processes)
3. **Celery:** Add more workers
4. **Database:** Read replicas + connection pooling
5. **Redis:** Redis Cluster
6. **Vector DB:** Sharding by tenant

---

## 🔍 Observability

Dify includes built-in observability:

**Tracing:** OpenTelemetry
- Request tracing
- LLM call tracking
- Performance monitoring

**Error Tracking:** Sentry
- Frontend errors
- Backend exceptions
- User impact analysis

**Logging:** Structured logging
- JSON logs
- Log levels
- Correlation IDs

🔗 **Observability Config:** [api/configs/observability/](../../api/configs/observability/)

---

## ✅ Quick Check

Test your understanding:

1. **Q:** What are the three main layers of Dify's architecture?
   <details>
   <summary>Answer</summary>
   Frontend (Next.js), Backend API (Flask), Data Layer (PostgreSQL/Redis)
   </details>

2. **Q:** What is Celery used for?
   <details>
   <summary>Answer</summary>
   Asynchronous background task processing (like document indexing, embeddings)
   </details>

3. **Q:** How does Dify achieve multi-tenancy?
   <details>
   <summary>Answer</summary>
   Row-level filtering by tenant_id in all database queries
   </details>

4. **Q:** What pattern does the backend follow for organization?
   <details>
   <summary>Answer</summary>
   Domain-Driven Design (DDD) with Clean Architecture
   </details>

5. **Q:** What's the difference between the Console API and Service API?
   <details>
   <summary>Answer</summary>
   Console API is for admin dashboard, Service API is for external integrations
   </details>

---

## 🎯 Key Takeaways

🧠 **Remember This (SCALRWM):**
- **S**ervice layer orchestrates business logic
- **C**lean architecture separates concerns
- **A**sync tasks use Celery
- **L**LM providers abstracted by Model Runtime
- **R**AG pipeline handles retrieval
- **W**orkflow engine executes graphs
- **M**ulti-tenant by design

---

## 📚 Next Steps

Now that you understand the architecture:

1. **[Project Structure](./PROJECT_STRUCTURE.md)** - Where to find specific code
2. **[Tech Stack Guide](./TECH_STACK_GUIDE.md)** - Deep dive into each technology
3. **[Data Flow Guide](./DATA_FLOW_GUIDE.md)** - Follow requests through the system

Or dive into specific areas:

4. **[Frontend Architecture](./FRONTEND_ARCHITECTURE.md)** - Next.js deep dive
5. **[Backend Architecture](./BACKEND_ARCHITECTURE.md)** - Flask/DDD patterns
6. **[Database Architecture](./DATABASE_ARCHITECTURE.md)** - Schema and models

---

## 🔗 Related Documentation

- [Flask Documentation](https://flask.palletsprojects.com/)
- [Next.js App Router](https://nextjs.org/docs/app)
- [SQLAlchemy ORM](https://docs.sqlalchemy.org/)
- [Celery Documentation](https://docs.celeryq.dev/)
- [Clean Architecture by Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

---

*Understanding architecture is the foundation of effective contribution!*
