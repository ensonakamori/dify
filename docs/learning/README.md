# Learning Path: Dify Platform

## 👋 Welcome!

Welcome to the **Dify Learning Path** - your comprehensive guide to becoming a productive contributor to the Dify platform!

Dify is an open-source LLM application development platform combining agentic AI workflows, RAG pipelines, and model management. If you're a React/TypeScript developer looking to expand into full-stack Python development, or if you want to understand how modern LLM applications are built, you're in the right place.

This learning path is designed specifically for **mid-level developers** who are:
- Proficient in JavaScript/TypeScript and React
- Eager to learn Python, Flask, and backend architecture
- Interested in understanding production LLM applications
- Ready to contribute meaningfully within 2-3 weeks

---

## 📋 Prerequisites

Before you begin, make sure you have:

- [ ] **JavaScript/TypeScript fundamentals** - You're comfortable with ES6+, async/await, and TypeScript
- [ ] **React basics** - You understand components, hooks, and state management
- [ ] **Git basics** - You can clone, commit, branch, and push
- [ ] **Command line comfort** - You can navigate directories and run scripts
- [ ] **Docker familiarity** (helpful but not required) - Basic understanding of containers
- [ ] **Python basics** (helpful but not required) - We'll teach you what you need!

**System Requirements:**
- Node.js >= v22.11.0
- Docker & Docker Compose
- 8GB+ RAM
- 10GB+ free disk space

---

## 🗺️ Learning Path Overview

Your journey from newcomer to confident contributor:

```
Phase 1: Setup & Architecture (Days 1-3) ⏱️ ~10 hours
        Learn the system, get it running
        ↓
Phase 2: Frontend Deep Dive (Days 4-7) ⏱️ ~12 hours
        Master Next.js 15, React 19, TypeScript
        ↓
Phase 3: Backend Deep Dive (Days 8-11) ⏱️ ~15 hours
        Learn Python, Flask, SQLAlchemy, Celery
        ↓
Phase 4: Database & Data Layer (Days 12-14) ⏱️ ~8 hours
        Understand PostgreSQL, pgvector, Redis
        ↓
Phase 5: Integration & APIs (Days 15-17) ⏱️ ~8 hours
        Explore LLM integrations, auth, APIs
        ↓
Phase 6: Patterns & Quality (Days 18-20) ⏱️ ~8 hours
        Master DDD, testing, debugging
        ↓
Phase 7: First Contribution (Days 21-25) ⏱️ ~12 hours
        Build real features, submit PRs
        ↓
🎉 You're a Dify Contributor!
```

**Total Time Investment:**
- **Minimum** (just to contribute): 8-10 hours
- **Comfortable** (solid understanding): 25-30 hours
- **Comprehensive** (deep mastery): 70-80 hours

---

## 📚 Complete Learning Materials

### Phase 1: Setup & Architecture (Days 1-3)

**Goal:** Understand the system and get it running locally

1. **[Getting Started Guide](./GETTING_STARTED.md)** ⏱️ 30 min
   - Environment setup (Docker, uv, pnpm, Node)
   - Clone and configure the project
   - First successful local build
   - Troubleshooting common issues

2. **[Architecture Overview](./ARCHITECTURE_OVERVIEW.md)** ⏱️ 1 hour
   - System architecture diagrams
   - Frontend ↔ Backend ↔ Database flow
   - Why these technologies?
   - Multi-tenancy explained
   - Request lifecycle

3. **[Project Structure](./PROJECT_STRUCTURE.md)** ⏱️ 45 min
   - Directory organization (`/api`, `/web`, `/docker`)
   - File naming conventions
   - Where to find what you need
   - Mental models for navigation

4. **[Tech Stack Guide](./TECH_STACK_GUIDE.md)** ⏱️ 2 hours
   - Next.js vs traditional React apps
   - Flask vs Express/NestJS
   - Python async vs Node.js async
   - SQLAlchemy vs Prisma
   - Vector databases explained
   - Why we chose each technology

5. **[Data Flow Guide](./DATA_FLOW_GUIDE.md)** ⏱️ 2 hours
   - End-to-end request diagrams
   - Frontend → API → Database → LLM
   - Async tasks with Celery
   - Real-time updates (WebSocket/SSE)
   - RAG pipeline flow

### Phase 2: Frontend Deep Dive (Days 4-7)

**Goal:** Master the Next.js/React frontend

6. **[Frontend Architecture](./FRONTEND_ARCHITECTURE.md)** ⏱️ 2 hours
   - Next.js 15 App Router
   - Server vs Client Components
   - State management (Zustand + React Query)
   - Form handling (React Hook Form + Zod)
   - i18n implementation

7. **[Workflow Canvas Deep Dive](./WORKFLOW_CANVAS.md)** ⏱️ 2 hours
   - React Flow fundamentals
   - Custom nodes and edges
   - State management for workflows
   - Undo/redo implementation

8. **[Frontend State Management](./FRONTEND_STATE_MANAGEMENT.md)** ⏱️ 1.5 hours
   - Zustand stores explained
   - React Query patterns
   - Optimistic updates
   - Cache invalidation

9. **[Component Library Tour](./COMPONENT_LIBRARY.md)** ⏱️ 1.5 hours
   - Base components overview
   - Design system patterns
   - Reusable component patterns
   - Icon system

### Phase 3: Backend Deep Dive (Days 8-11)

**Goal:** Understand Python, Flask, and backend architecture

10. **[Python for JavaScript Developers](./PYTHON_FOR_JS_DEVELOPERS.md)** ⏱️ 3 hours
    - Python fundamentals crash course
    - Type hints vs TypeScript
    - async/await comparison
    - Decorators vs HOFs
    - Virtual environments vs node_modules

11. **[Backend Architecture](./BACKEND_ARCHITECTURE.md)** ⏱️ 3 hours
    - Domain-Driven Design (DDD)
    - Clean Architecture layers
    - Service layer pattern
    - Repository pattern
    - Flask Blueprints (like Express routers)

12. **[Backend Core Domains](./BACKEND_CORE_DOMAINS.md)** ⏱️ 3 hours
    - Workflow engine internals
    - Agent orchestration
    - RAG pipeline architecture
    - Model runtime abstraction

13. **[Celery and Async Tasks](./CELERY_AND_ASYNC.md)** ⏱️ 2 hours
    - What is Celery? (like Bull/BullMQ in Node)
    - Redis as message broker
    - Task patterns in Dify
    - Monitoring and debugging

### Phase 4: Database & Data Layer (Days 12-14)

**Goal:** Master PostgreSQL, SQLAlchemy, and vector databases

14. **[Database Architecture](./DATABASE_ARCHITECTURE.md)** ⏱️ 3 hours
    - PostgreSQL schema overview
    - Multi-tenancy data model
    - SQLAlchemy models (like Prisma)
    - Database migrations (Alembic)

15. **[Vector Databases Explained](./VECTOR_DATABASES.md)** ⏱️ 2 hours
    - What are vector databases?
    - Embeddings 101
    - pgvector vs dedicated solutions
    - RAG retrieval explained

16. **[Database Schema Reference](./DATABASE_SCHEMA.md)** ⏱️ 2 hours
    - Entity-Relationship diagrams
    - Core tables walkthrough
    - Foreign key relationships

### Phase 5: Integration & APIs (Days 15-17)

**Goal:** Understand external integrations and APIs

17. **[API Documentation](./API_DOCUMENTATION.md)** ⏱️ 2 hours
    - REST API design
    - Console vs Service vs Web APIs
    - Authentication & authorization
    - Error handling patterns

18. **[Authentication & Authorization](./AUTHENTICATION_AND_AUTHORIZATION.md)** ⏱️ 2 hours
    - JWT implementation
    - Multi-tenancy auth
    - RBAC (Role-Based Access Control)
    - SSO/OAuth flows

19. **[Integration Guide](./INTEGRATION_GUIDE.md)** ⏱️ 2 hours
    - LLM provider integration
    - Tool/plugin integration
    - Storage providers
    - Vector DB integrations

### Phase 6: Patterns & Best Practices (Days 18-20)

**Goal:** Master code quality and testing

20. **[Patterns and Conventions](./PATTERNS_AND_CONVENTIONS.md)** ⏱️ 2 hours
    - Code style guides
    - DDD patterns in practice
    - Error handling
    - Naming conventions

21. **[Testing Guide](./TESTING_GUIDE.md)** ⏱️ 2 hours
    - Test-Driven Development (TDD)
    - Unit tests (Pytest + Jest)
    - Integration tests
    - Test fixtures and mocking

22. **[Debugging Guide](./DEBUGGING_GUIDE.md)** ⏱️ 2 hours
    - Backend debugging (pdb, debugpy)
    - Frontend debugging (Chrome DevTools)
    - Celery task debugging
    - Common errors and solutions

23. **[Development Workflow](./DEVELOPMENT_WORKFLOW.md)** ⏱️ 1 hour
    - Git workflow
    - Pull request process
    - Code review checklist
    - Local development loop

### Phase 7: Practical Application (Days 21-25)

**Goal:** Build real features and contribute

24. **[How-To Guide](./HOW_TO_GUIDE.md)** ⏱️ 2 hours
    - How to add a new API endpoint
    - How to create a new page
    - How to add a database model
    - How to add a Celery task
    - How to add translations

25. **[Code Tours](./CODE_TOURS.md)** ⏱️ 3 hours
    - Tour: Chat message end-to-end
    - Tour: Workflow execution
    - Tour: RAG pipeline
    - Tour: User authentication

26. **[Hands-On Exercises](./EXERCISES.md)** ⏱️ 4 hours
    - Exercise 1: Simple API endpoint
    - Exercise 2: New React component
    - Exercise 3: Database migration
    - Exercise 4: Celery task
    - Solutions included!

27. **[First Contributions](./FIRST_CONTRIBUTIONS.md)** ⏱️ 2 hours
    - Good first issues
    - Step-by-step first PR
    - Review process
    - Getting unstuck

### Reference Materials (Use As Needed)

28. **[Security Guide](./SECURITY_GUIDE.md)**
    - Security best practices
    - Input validation
    - XSS/CSRF/SQL injection prevention

29. **[Performance Guide](./PERFORMANCE_GUIDE.md)**
    - Optimization techniques
    - Caching strategies
    - Query optimization

30. **[Deployment Guide](./DEPLOYMENT_GUIDE.md)**
    - Docker deployment
    - Production checklist
    - Monitoring and observability

31. **[FAQ](./FAQ.md)**
    - Common questions
    - Troubleshooting
    - "Why did we choose X over Y?"

32. **[Glossary](./GLOSSARY.md)**
    - Technical terms defined
    - Acronyms explained
    - Dify-specific terminology

---

## 🎯 Quick Start (If You're Impatient)

Want to jump right in? Follow this **speedrun** path:

1. **[Getting Started](./GETTING_STARTED.md)** - Get it running ⏱️ 30 min
2. **[Architecture Overview](./ARCHITECTURE_OVERVIEW.md)** - Big picture ⏱️ 1 hour
3. **[Python for JS Developers](./PYTHON_FOR_JS_DEVELOPERS.md)** - Python crash course ⏱️ 1.5 hours
4. **[How-To Guide](./HOW_TO_GUIDE.md)** - Make your first change ⏱️ 1 hour
5. **[First Contributions](./FIRST_CONTRIBUTIONS.md)** - Submit your first PR ⏱️ 2 hours

**Total: ~6 hours to first contribution** 🚀

---

## 🧭 How to Use This Guide

### Learning Styles

**📖 Sequential Learner?**
Follow the phases in order from Phase 1 → Phase 7. This gives you a solid foundation before diving into specifics.

**🎯 Goal-Oriented Learner?**
Jump to the "How-To Guide" and reference other docs as needed. Learn by doing!

**🔍 Explorer?**
Browse topics that interest you. Use hyperlinks to jump between related concepts.

**🏃 In a Hurry?**
Follow the Quick Start path above to get contributing fast.

### Document Structure

Each guide follows this pattern:

1. **Purpose** - What you'll learn
2. **Mental Models** 🧠 - Simplified conceptual frameworks
3. **Bridges from React/JS** 🌉 - Analogies to familiar concepts
4. **Code Examples** 🔗 - Real code with line numbers
5. **Aha Moments** 💡 - Key insights
6. **Common Pitfalls** ⚠️ - What to avoid
7. **Quick Checks** ✅ - Self-test questions
8. **Next Steps** - Where to go next

### Navigation Tips

- **Follow hyperlinks** - Documents are heavily cross-referenced
- **Use Cmd/Ctrl+F** - Search within documents
- **Check the FAQ** - Common questions answered
- **Refer to Glossary** - Look up unfamiliar terms

---

## ⏱️ Time Estimates by Path

### Path 1: "Just Let Me Contribute" (8-10 hours)
Perfect for fixing bugs or adding small features quickly.

- Getting Started (30 min)
- Architecture Overview (1 hour)
- Python for JS Developers (1.5 hours)
- How-To Guide (1 hour)
- Testing Guide (1 hour)
- First Contributions (2 hours)
- Plus 1-2 hours building your feature

### Path 2: "Comfortable Contributor" (25-30 hours)
Solid understanding to work on most features.

- All of Phase 1 (6 hours)
- Frontend Architecture + one deep-dive (3 hours)
- Backend Architecture + Python Guide (6 hours)
- Database Architecture (3 hours)
- How-To Guide + Code Tours (5 hours)
- Exercises + First Contributions (6 hours)

### Path 3: "Deep Expert" (70-80 hours)
Complete mastery of the entire platform.

- Read all documents sequentially
- Complete all exercises
- Explore referenced code thoroughly
- Build multiple features

---

## 🆘 Getting Help

### When You're Stuck

1. **Check the [FAQ](./FAQ.md)** - Common questions answered
2. **Search the [Debugging Guide](./DEBUGGING_GUIDE.md)** - Error solutions
3. **Review [Glossary](./GLOSSARY.md)** - Understand terminology
4. **Ask in Discord** - [Join the community](https://discord.gg/FngNHpbcY7)
5. **Open a Discussion** - [GitHub Discussions](https://github.com/langgenius/dify/discussions)

### Best Practices for Asking Questions

✅ **Good Question:**
> "I'm trying to add a new API endpoint following the How-To Guide. I created the controller in `api/controllers/console/my_controller.py` but I'm getting a 404. I've registered the blueprint in `__init__.py`. What am I missing?"

❌ **Less Helpful:**
> "API doesn't work, help!"

**Include:**
- What you're trying to do
- What you've tried
- Relevant error messages or logs
- Your environment (Docker, local, etc.)

---

## 🎓 Learning Philosophy

This learning path is built on proven pedagogical principles:

### 1. **Analogies to Familiar Concepts**
We constantly compare Python/Flask to JavaScript/Node.js concepts you already know.

### 2. **Progressive Complexity**
Each document starts simple and gradually introduces advanced topics.

### 3. **Hands-On Practice**
Theory is great, but you learn by doing. Exercises reinforce concepts.

### 4. **Spaced Repetition**
Important concepts appear multiple times in different contexts.

### 5. **Mnemonics & Memory Aids**
We provide memorable phrases to help concepts stick.

### 6. **Real Code Examples**
Every concept links to actual production code in the repository.

---

## 🎯 Learning Objectives

By completing this learning path, you will be able to:

- [ ] Set up a local Dify development environment
- [ ] Navigate the codebase confidently
- [ ] Explain the high-level architecture
- [ ] Understand key technology choices
- [ ] Write Python code following project conventions
- [ ] Create new API endpoints
- [ ] Build new React components
- [ ] Write database migrations
- [ ] Create and monitor Celery tasks
- [ ] Write unit and integration tests
- [ ] Debug backend and frontend issues
- [ ] Follow the pull request process
- [ ] Submit quality contributions
- [ ] Help onboard other developers

---

## 📊 Track Your Progress

As you complete each guide, check it off:

### Phase 1: Setup & Architecture ▢
- [ ] Getting Started
- [ ] Architecture Overview
- [ ] Project Structure
- [ ] Tech Stack Guide
- [ ] Data Flow Guide

### Phase 2: Frontend Deep Dive ▢
- [ ] Frontend Architecture
- [ ] Workflow Canvas
- [ ] State Management
- [ ] Component Library

### Phase 3: Backend Deep Dive ▢
- [ ] Python for JS Developers
- [ ] Backend Architecture
- [ ] Backend Core Domains
- [ ] Celery and Async

### Phase 4: Database & Data ▢
- [ ] Database Architecture
- [ ] Vector Databases
- [ ] Database Schema

### Phase 5: Integration & APIs ▢
- [ ] API Documentation
- [ ] Authentication & Authorization
- [ ] Integration Guide

### Phase 6: Patterns & Quality ▢
- [ ] Patterns and Conventions
- [ ] Testing Guide
- [ ] Debugging Guide
- [ ] Development Workflow

### Phase 7: Practical Application ▢
- [ ] How-To Guide
- [ ] Code Tours
- [ ] Exercises
- [ ] First Contributions

---

## 🌟 What Makes Dify Special?

As you learn, you'll discover Dify's unique strengths:

1. **Production-Ready LLM Platform** - Not a toy project, real production code
2. **Domain-Driven Design** - Clean architecture principles in practice
3. **Multi-Tenancy from Day 1** - Learn how to build scalable SaaS
4. **RAG Pipeline** - Deep understanding of retrieval-augmented generation
5. **Workflow Engine** - Visual programming for LLM applications
6. **Modern Stack** - Latest Next.js, React, Python patterns
7. **Active Community** - Thousands of contributors worldwide

---

## 🚀 Ready to Begin?

**Your first step:** [Getting Started Guide](./GETTING_STARTED.md)

Or if you prefer:
- [Architecture Overview](./ARCHITECTURE_OVERVIEW.md) - Start with the big picture
- [Python for JS Developers](./PYTHON_FOR_JS_DEVELOPERS.md) - Python crash course
- [How-To Guide](./HOW_TO_GUIDE.md) - Learn by doing

---

## 📝 Contributing to These Docs

Found an error? Have a suggestion? These docs are open source too!

- Open an issue describing the problem
- Submit a PR with improvements
- Share feedback in Discord

---

## 🙏 Acknowledgments

This learning path was created to help developers like you contribute to Dify. We're excited to have you join our community!

**Happy Learning!** 🎉

---

*Last Updated: November 2025*
*Dify Version: 1.10.0*
*Next.js: 15.5.6 | React: 19.1.1 | Python: 3.11-3.12*
