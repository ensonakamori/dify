# Frequently Asked Questions (FAQ)

## 📋 Table of Contents

- [Getting Started](#getting-started)
- [Technical Questions](#technical-questions)
- [Development Workflow](#development-workflow)
- [Architecture & Design](#architecture--design)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

---

## Getting Started

### Q: What skills do I need to contribute to Dify?

**Minimum requirements:**
- JavaScript/TypeScript and React basics
- Git basics
- Command line comfort

**Nice to have:**
- Python experience (we have a guide!)
- Docker knowledge
- SQL basics

**Don't worry!** This learning path teaches you everything else.

### Q: How long does it take to become productive?

**Timeline:**
- **Quick start** (first contribution): 8-10 hours
- **Comfortable** (most features): 25-30 hours
- **Expert** (complex features): 70-80 hours

Most developers make their first PR within 2 weeks.

### Q: Do I need to know Python to contribute?

**For frontend-only contributions:** No! You can contribute to:
- React components
- UI improvements
- Styling
- Frontend bug fixes

**For full-stack contributions:** Yes, but we have you covered! See **[Python for JavaScript Developers](./PYTHON_FOR_JS_DEVELOPERS.md)**.

### Q: Why did setup fail with "Port already in use"?

**PostgreSQL (port 5432):**
```bash
# macOS
brew services stop postgresql

# Linux
sudo systemctl stop postgresql

# Or change port in docker/middleware.env
POSTGRES_PORT=5433
```

**Next.js (port 3000):**
```bash
# Change port when starting
cd web
PORT=3001 pnpm dev
```

**Flask (port 5001):**
```bash
# Change port
cd api
uv run --dev flask run --port 5002
```

---

## Technical Questions

### Q: Why Flask instead of FastAPI?

**Short answer:** Flask's maturity and ecosystem win for this use case.

**Detailed answer:**
- ✅ Flask has 15+ years of battle-testing
- ✅ Better SQLAlchemy integration
- ✅ More extensions available
- ✅ Team expertise
- ⚡ Celery handles async (don't need FastAPI's native async)

**When FastAPI would be better:**
- Heavy async/await workload
- Need auto-generated API docs
- Starting from scratch

🔗 **See:** [Tech Stack Guide](./TECH_STACK_GUIDE.md#flask-3)

### Q: Why Next.js App Router instead of Pages Router?

**Short answer:** App Router is the future of Next.js.

**Benefits:**
- ✅ React Server Components (better performance)
- ✅ Streaming SSR
- ✅ Better layouts
- ✅ Improved TypeScript support

**Trade-offs:**
- ❌ Smaller ecosystem (newer)
- ❌ Learning curve

🔗 **See:** [Tech Stack Guide](./TECH_STACK_GUIDE.md#nextjs-15)

### Q: How does Dify handle async operations?

**Backend:** Uses **Celery** (distributed task queue with Redis)

```python
# Queue a task
from tasks.indexing import index_document
index_document.delay(document_id)

# Like BullMQ in Node.js
await documentQueue.add('index', { documentId })
```

**Frontend:** Uses standard React patterns (React Query, SWR)

🔗 **See:** [Data Flow Guide - Async Tasks](./DATA_FLOW_GUIDE.md#2-document-upload-and-indexing)

### Q: How does authentication work?

**Short answer:** JWT tokens in httpOnly cookies.

**Flow:**
1. User logs in → Server generates JWT
2. JWT stored in httpOnly cookie (not localStorage!)
3. All requests include cookie automatically
4. Server validates JWT on each request

**Multi-tenancy:**
- JWT includes `tenant_id`
- All queries filter by tenant

🔗 **See:** [Data Flow Guide - Authentication](./DATA_FLOW_GUIDE.md#4-user-authentication)

### Q: What's the difference between Services and Repositories?

**Repositories:** Data access layer (database queries)

```python
# Repository - just database operations
class AppRepository:
    @staticmethod
    def get_by_id(app_id: str) -> App:
        return App.query.get(app_id)
```

**Services:** Business logic orchestration

```python
# Service - orchestrates business logic
class AppService:
    @staticmethod
    def create_app(name: str) -> App:
        # Validate
        if AppRepository.exists_by_name(name):
            raise DuplicateAppError()

        # Create
        app = App(name=name)

        # Save
        AppRepository.save(app)

        # Trigger events
        EventService.emit('app.created', app)

        return app
```

🌉 **Bridge from Node.js:** Like separating Prisma queries from business logic.

---

## Development Workflow

### Q: What's the workflow for adding a feature?

**Step-by-step:**

1. **Find/create issue** - Get buy-in first!
2. **Create branch** - `feat/your-feature-name`
3. **Develop** - Follow patterns in codebase
4. **Test locally** - Run linters, tests
5. **Commit** - Use conventional commits
6. **Push** - Push to your fork
7. **Create PR** - Fill out template
8. **Address feedback** - Iterate with reviewers
9. **Get merged!** 🎉

🔗 **See:** [First Contributions](./FIRST_CONTRIBUTIONS.md)

### Q: How do I run only specific tests?

**Backend (pytest):**
```bash
# Run single test file
uv run pytest tests/unit_tests/test_app_service.py

# Run single test
uv run pytest tests/unit_tests/test_app_service.py::test_create_app

# Run tests matching pattern
uv run pytest -k "test_app"
```

**Frontend (Jest):**
```bash
# Run single test file
pnpm test button.test.tsx

# Run in watch mode
pnpm test:watch button
```

### Q: How do I add a database field?

**Step-by-step:**

1. **Add field to model:**
   ```python
   # api/models/model.py
   class App(db.Model):
       # ... existing fields ...
       new_field = db.Column(db.String(255))
   ```

2. **Create migration:**
   ```bash
   uv run flask db migrate -m "Add new_field to apps"
   ```

3. **Review migration:**
   ```python
   # api/migrations/versions/XXX_add_new_field.py
   def upgrade():
       op.add_column('apps', sa.Column('new_field', sa.String(255)))
   ```

4. **Apply migration:**
   ```bash
   uv run flask db upgrade
   ```

🔗 **See:** [How-To Guide - Add Database Model](./HOW_TO_GUIDE.md#3-add-a-database-model)

### Q: How do I add a new page in Next.js?

**App Router pattern:**

```
web/app/
└── my-new-page/
    ├── page.tsx      # Route component
    └── layout.tsx    # Optional layout
```

**Example:**
```tsx
// web/app/my-new-page/page.tsx
export default function MyNewPage() {
  return <div>My New Page</div>
}
```

**Route:** `http://localhost:3000/my-new-page`

🔗 **See:** [Project Structure - Frontend](./PROJECT_STRUCTURE.md#frontend-structure-web)

---

## Architecture & Design

### Q: Why Domain-Driven Design (DDD)?

**Benefits:**
- ✅ Clear separation of concerns
- ✅ Business logic isolated from infrastructure
- ✅ Easier to test
- ✅ Scalable codebase

**Layers in Dify:**
1. **Controllers** - HTTP handlers
2. **Services** - Business logic
3. **Repositories** - Data access
4. **Models** - Database entities

🔗 **See:** [Architecture Overview](./ARCHITECTURE_OVERVIEW.md#clean-architecture-layers)

### Q: How does the workflow engine work?

**High-level:**

1. **Graph definition** - Nodes connected by edges
2. **Topological sort** - Determine execution order
3. **Variable pool** - Share data between nodes
4. **Node execution** - Run each node in order
5. **Result aggregation** - Collect outputs

**Mental model:** Think of it like **Redux middleware chain** or **Express middleware**, but with a visual graph.

🔗 **See:** [Data Flow Guide - Workflow Execution](./DATA_FLOW_GUIDE.md#3-workflow-execution)

### Q: How does RAG (Retrieval-Augmented Generation) work in Dify?

**Pipeline:**

```
User Query
    ↓
Generate embedding (vector)
    ↓
Search vector database (similarity)
    ↓
Retrieve top K documents
    ↓
Rerank (optional)
    ↓
Inject into LLM prompt
    ↓
Generate response
```

**Key components:**
- **Embeddings:** OpenAI, Cohere, etc.
- **Vector DB:** Weaviate, Qdrant, pgvector, etc.
- **Chunking:** RecursiveCharacterTextSplitter
- **Reranking:** Cohere, Jina rerankers

🔗 **See:** [Data Flow Guide - RAG Flow](./DATA_FLOW_GUIDE.md#5-rag-retrieval-flow)

### Q: How is multi-tenancy implemented?

**Database level:**
- Every query includes `tenant_id` filter
- Foreign keys enforce relationships
- No cross-tenant data access

**Application level:**
- JWT token contains `tenant_id`
- Middleware extracts `tenant_id` to `g.current_tenant`
- Services validate tenant ownership

**Example:**
```python
# Every query filters by tenant
apps = App.query.filter_by(
    tenant_id=g.current_tenant.id
).all()
```

⚠️ **Critical:** Never forget to filter by `tenant_id`!

---

## Troubleshooting

### Q: Tests are failing locally but pass in CI

**Common causes:**

1. **Different Python/Node versions:**
   ```bash
   python --version  # Should match CI
   node --version    # Should match CI
   ```

2. **Cached dependencies:**
   ```bash
   # Backend
   uv sync --force

   # Frontend
   rm -rf node_modules pnpm-lock.yaml
   pnpm install
   ```

3. **Environment variables:**
   ```bash
   # Check .env files
   cat api/.env
   cat web/.env
   ```

### Q: Database migrations failing

**Common issues:**

1. **Conflicting migrations:**
   ```bash
   # Check current version
   uv run flask db current

   # Downgrade to previous
   uv run flask db downgrade

   # Delete bad migration
   rm api/migrations/versions/XXX_bad_migration.py

   # Recreate
   uv run flask db migrate -m "Correct migration"
   ```

2. **Schema drift:**
   ```bash
   # Reset database (DANGER: deletes data!)
   docker compose -f docker/docker-compose.middleware.yaml down -v
   docker compose -f docker/docker-compose.middleware.yaml up -d

   # Run all migrations
   uv run flask db upgrade
   ```

### Q: Frontend build failing

**Common issues:**

1. **TypeScript errors:**
   ```bash
   pnpm type-check
   # Fix errors, then:
   pnpm build
   ```

2. **Missing dependencies:**
   ```bash
   pnpm install
   ```

3. **Out of memory:**
   ```bash
   # Increase Node memory
   NODE_OPTIONS="--max-old-space-size=4096" pnpm build
   ```

### Q: Celery tasks not running

**Check:**

1. **Worker running?**
   ```bash
   # Start worker
   cd api
   uv run --dev celery -A app.celery worker -P gevent -c 1 --loglevel INFO
   ```

2. **Redis running?**
   ```bash
   docker ps | grep redis
   ```

3. **Task registered?**
   ```python
   # Check task is imported
   # api/tasks/__init__.py
   from .my_tasks import my_task
   ```

---

## Contributing

### Q: How do I propose a new feature?

**Process:**

1. **Search existing issues/discussions**
2. **Open a discussion** (not issue) in [GitHub Discussions](https://github.com/langgenius/dify/discussions)
3. **Describe:**
   - Problem you're solving
   - Proposed solution
   - Alternatives considered
   - Impact on existing features
4. **Wait for feedback** from maintainers
5. **Create issue** once approved
6. **Implement!**

### Q: My PR has been open for a week with no review. What should I do?

**Steps:**

1. **Check CI:** Are all checks passing?
2. **Ping maintainers:** Comment "@maintainer gentle ping for review"
3. **Ask in Discord:** #engineering channel
4. **Be patient:** Maintainers are volunteers

**Average review time:** 1-3 days (but can vary)

### Q: How can I become a maintainer?

**Path:**

1. **Regular contributions** (5+ merged PRs)
2. **Quality work** (well-tested, documented)
3. **Community involvement** (help others, review PRs)
4. **Maintainers notice** and invite you!

**Time:** Usually 3-6 months of consistent contribution

### Q: Can I use Dify in my commercial product?

**Yes!** Dify is open-source under the **Dify Open Source License** (based on Apache 2.0).

**Key points:**
- ✅ Use in commercial products
- ✅ Modify the code
- ✅ Self-host
- ⚠️ Read the full [LICENSE](../../LICENSE)

**Need enterprise features?** Contact [business@dify.ai](mailto:business@dify.ai)

---

## 🔍 Still Have Questions?

### Resources

- 📚 **[Learning Path README](./README.md)** - Start here
- 💬 **[Discord #engineering](https://discord.gg/FngNHpbcY7)** - Real-time help
- 💬 **[GitHub Discussions](https://github.com/langgenius/dify/discussions)** - Long-form Q&A
- 🐛 **[GitHub Issues](https://github.com/langgenius/dify/issues)** - Bug reports

### How to Ask Good Questions

✅ **Good:**
> "I'm trying to add a new API endpoint following the How-To Guide. I created the controller but I'm getting a 404. Here's my code: [gist]. What am I missing?"

❌ **Less helpful:**
> "API doesn't work, help!"

**Include:**
- What you're trying to do
- What you've tried
- Error messages/logs
- Your environment (Docker/local, OS, versions)

---

*Can't find your question? Ask in [Discussions](https://github.com/langgenius/dify/discussions) and we'll add it here!* 💬
