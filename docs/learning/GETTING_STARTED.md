# Getting Started with Dify

## 🎯 Purpose

This guide will get you from zero to running Dify locally in about 30 minutes. By the end, you'll have:
- ✅ All required tools installed
- ✅ The Dify codebase running locally
- ✅ Successfully accessed the Dify UI
- ✅ Run your first backend and frontend commands

---

## 📋 Prerequisites Checklist

Before starting, ensure you have:

- [ ] **macOS, Linux, or WSL2 (Windows)**
- [ ] **8GB+ RAM** (16GB recommended)
- [ ] **10GB+ free disk space**
- [ ] **Internet connection** (for downloading dependencies)

---

## 🛠️ Step 1: Install Required Tools

### 1.1 Install Node.js (v22.11.0+)

Dify's frontend requires Node.js v22.11.0 or higher.

**Using nvm (recommended):**

```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Install Node.js 22
nvm install 22
nvm use 22
node --version  # Should show v22.11.0 or higher
```

**Direct download:**
- Download from [nodejs.org](https://nodejs.org/)

### 1.2 Install pnpm

Dify uses `pnpm` instead of `npm` or `yarn`.

```bash
npm install -g pnpm@10.22.0
pnpm --version  # Should show 10.22.0
```

🧠 **Mental Model:** Think of pnpm like npm, but faster and more disk-efficient. It uses hard links instead of copying packages.

### 1.3 Install Python 3.11 or 3.12

**Using pyenv (recommended):**

```bash
# Install pyenv
curl https://pyenv.run | bash

# Install Python 3.11
pyenv install 3.11
pyenv global 3.11
python --version  # Should show Python 3.11.x
```

**Direct download:**
- Download from [python.org](https://www.python.org/downloads/)

⚠️ **Common Pitfall:** Python 3.13+ is not yet supported. Stick to 3.11 or 3.12.

### 1.4 Install uv (Python Package Manager)

Dify uses `uv` instead of `pip` for faster dependency management.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv --version
```

🌉 **Bridge from Node:** `uv` is to Python what `pnpm` is to Node.js - a modern, fast package manager.

### 1.5 Install Docker & Docker Compose

Dify uses Docker for running PostgreSQL, Redis, and other services.

**macOS:**
```bash
# Install Docker Desktop
# Download from https://www.docker.com/products/docker-desktop
```

**Linux:**
```bash
# Install Docker
curl -fsSL https://get.docker.com | sh

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Add your user to docker group
sudo usermod -aG docker $USER
# Log out and back in for this to take effect
```

**Verify installation:**
```bash
docker --version
docker-compose --version
```

---

## 📥 Step 2: Clone the Repository

```bash
# Clone Dify
git clone https://github.com/langgenius/dify.git
cd dify
```

**Repository structure:**
```
dify/
├── api/          # Python/Flask backend
├── web/          # Next.js frontend
├── docker/       # Docker configurations
└── sdks/         # Client SDKs
```

---

## 🚀 Step 3: Quick Setup with Makefile

Dify provides a `Makefile` with a one-command setup.

```bash
# Run the complete dev setup
make dev-setup
```

This command will:
1. ✅ Start Docker middleware (PostgreSQL, Redis, Weaviate)
2. ✅ Set up web environment and install dependencies
3. ✅ Set up API environment and run migrations

⏱️ **Expected time:** 5-10 minutes (depending on internet speed)

💡 **Aha Moment:** The `Makefile` automates what would be 10+ manual commands!

---

## 🔧 Step 4: Manual Setup (Alternative)

If you prefer to understand each step, here's the manual process:

### 4.1 Start Docker Middleware

```bash
cd docker
cp middleware.env.example middleware.env
docker compose -f docker-compose.middleware.yaml --env-file middleware.env -p dify-middlewares-dev up -d
```

This starts:
- **PostgreSQL** (port 5432) - Main database
- **Redis** (port 6379) - Cache and message broker
- **Weaviate** (port 8080) - Vector database
- **Plugin Daemon** - For plugin execution

🔗 **See:** [docker-compose.middleware.yaml](../../docker/docker-compose.middleware.yaml#L1-L50)

### 4.2 Setup Backend (API)

```bash
cd api
cp .env.example .env
uv sync --dev
uv run flask db upgrade
```

**What this does:**
- `cp .env.example .env` - Creates environment config
- `uv sync --dev` - Installs all Python dependencies (~2-3 min)
- `uv run flask db upgrade` - Runs database migrations

🔗 **See:** [api/.env.example](../../api/.env.example)

### 4.3 Setup Frontend (Web)

```bash
cd web
cp .env.example .env
pnpm install
```

**What this does:**
- `cp .env.example .env` - Creates environment config
- `pnpm install` - Installs all Node.js dependencies (~3-5 min)

🔗 **See:** [web/.env.example](../../web/.env.example)

---

## ▶️ Step 5: Start the Development Servers

You'll need **3 terminal windows** (or use tmux/screen):

### Terminal 1: Backend API

```bash
cd api
uv run --dev flask run --host 0.0.0.0 --port 5001 --debug
```

You should see:
```
 * Serving Flask app 'app'
 * Debug mode: on
 * Running on http://0.0.0.0:5001
```

🔗 **Code:** [api/app.py](../../api/app.py#L23-L29)

### Terminal 2: Celery Worker (Background Tasks)

```bash
cd api
uv run --dev celery -A app.celery worker -P gevent -c 1 --loglevel INFO
```

You should see:
```
[tasks]
  . core.rag.retrieval.dataset_retrieval.DatasetRetrieval
  ...
ready.
```

💡 **Aha Moment:** Celery handles async tasks like document processing, similar to Bull in Node.js!

### Terminal 3: Frontend (Web)

```bash
cd web
pnpm dev
```

You should see:
```
  ▲ Next.js 15.5.6
  - Local:        http://localhost:3000
  - Turbopack:    enabled
✓ Ready in 2.1s
```

🌉 **Bridge from React:** Next.js dev server with hot reload, just like Create React App or Vite!

---

## ✅ Step 6: Verify Everything Works

### 6.1 Access the Frontend

Open your browser and go to:
```
http://localhost:3000
```

You should see the Dify installation page! 🎉

### 6.2 Complete Initial Setup

1. Click "Get Started" on the installation page
2. Set up an admin account:
   - **Email:** your-email@example.com
   - **Password:** (choose a secure password)
   - **Name:** Your Name
3. Click "Continue"

### 6.3 Verify Backend API

Open a new terminal and test the API:

```bash
curl http://localhost:5001/health
```

Expected response:
```json
{"status": "ok"}
```

---

## 🔍 Step 7: Explore the Running Application

### Create Your First App

1. Click "Create App" in the dashboard
2. Choose "Chatbot"
3. Give it a name: "My First Chatbot"
4. Explore the interface!

### View Running Services

```bash
# Check Docker containers
docker ps

# You should see:
# - postgres
# - redis
# - weaviate
# - ssrf_proxy
```

---

## 🧪 Step 8: Run Tests (Optional)

### Backend Tests

```bash
cd api
make lint          # Run linters (ruff)
make type-check    # Run type checker (basedpyright)
uv run --project api --dev dev/pytest/pytest_unit_tests.sh  # Run unit tests
```

🔗 **See:** [Makefile](../../Makefile#L48-L65)

### Frontend Tests

```bash
cd web
pnpm lint          # Run ESLint
pnpm type-check    # Run TypeScript compiler
pnpm test          # Run Jest tests
```

---

## 🛑 Stopping the Development Environment

### Stop all running servers:
- Press `Ctrl+C` in each terminal (API, Celery, Web)

### Stop Docker containers:

```bash
cd docker
docker compose -f docker-compose.middleware.yaml -p dify-middlewares-dev down
```

### Clean up everything (optional):

```bash
make dev-clean  # Removes all Docker volumes and storage
```

⚠️ **Warning:** `make dev-clean` deletes all database data!

---

## 🐛 Troubleshooting

### Problem: "Port 5432 already in use"

**Solution:** You have PostgreSQL running locally. Stop it or change the port in `docker/middleware.env`:

```bash
# Stop local PostgreSQL (macOS)
brew services stop postgresql

# Or change port in docker/middleware.env
POSTGRES_PORT=5433
```

### Problem: "Port 3000 already in use"

**Solution:** Change the port for Next.js:

```bash
cd web
PORT=3001 pnpm dev
```

### Problem: "uv: command not found"

**Solution:** Add uv to your PATH:

```bash
# Add to ~/.bashrc or ~/.zshrc
export PATH="$HOME/.cargo/bin:$PATH"
source ~/.bashrc  # or ~/.zshrc
```

### Problem: Docker permissions error

**Solution:** Add your user to the docker group:

```bash
sudo usermod -aG docker $USER
# Log out and back in
```

### Problem: "Module not found" in Python

**Solution:** Reinstall dependencies:

```bash
cd api
uv sync --dev --force
```

### Problem: "Module not found" in Node.js

**Solution:** Reinstall dependencies:

```bash
cd web
rm -rf node_modules pnpm-lock.yaml
pnpm install
```

### Problem: Database migration fails

**Solution:** Reset the database:

```bash
cd docker
docker compose -f docker-compose.middleware.yaml -p dify-middlewares-dev down -v
docker compose -f docker-compose.middleware.yaml --env-file middleware.env -p dify-middlewares-dev up -d
cd ../api
uv run flask db upgrade
```

---

## ✅ Quick Check

Test your understanding:

1. **Q:** What command starts the backend API server?
   <details>
   <summary>Answer</summary>
   `uv run --dev flask run --host 0.0.0.0 --port 5001 --debug`
   </details>

2. **Q:** What port does the frontend run on by default?
   <details>
   <summary>Answer</summary>
   Port 3000
   </details>

3. **Q:** What is Celery used for in Dify?
   <details>
   <summary>Answer</summary>
   Background task processing (like document indexing, async operations)
   </details>

4. **Q:** Which package manager does the frontend use?
   <details>
   <summary>Answer</summary>
   pnpm
   </details>

---

## 🎯 Success Criteria

You've successfully completed setup if:

- [x] ✅ You can access http://localhost:3000 and see the Dify UI
- [x] ✅ The backend API responds at http://localhost:5001/health
- [x] ✅ You can create a new app in the Dify interface
- [x] ✅ No error messages in any terminal
- [x] ✅ Docker containers are running (`docker ps` shows postgres, redis, weaviate)

---

## 📚 Next Steps

Congratulations! You're now running Dify locally. 🎉

**Continue your learning journey:**

1. **[Architecture Overview](./ARCHITECTURE_OVERVIEW.md)** - Understand how everything fits together
2. **[Project Structure](./PROJECT_STRUCTURE.md)** - Learn where to find code
3. **[Tech Stack Guide](./TECH_STACK_GUIDE.md)** - Deep dive into technologies

**Or jump straight to coding:**

4. **[How-To Guide](./HOW_TO_GUIDE.md)** - Make your first change
5. **[Python for JS Developers](./PYTHON_FOR_JS_DEVELOPERS.md)** - Python crash course

---

## 🆘 Still Having Issues?

- 📖 Check the [FAQ](./FAQ.md)
- 🐛 See [Debugging Guide](./DEBUGGING_GUIDE.md)
- 💬 Ask in [Discord](https://discord.gg/FngNHpbcY7)
- 🐙 Open a [GitHub Discussion](https://github.com/langgenius/dify/discussions)

---

## 📝 Development Environment Summary

**Running Services:**
```
Frontend:  http://localhost:3000  (Next.js)
Backend:   http://localhost:5001  (Flask)
PostgreSQL: localhost:5432
Redis:      localhost:6379
Weaviate:   localhost:8080
```

**Key Commands:**
```bash
# Start middleware
make dev-setup

# Backend
cd api && uv run --dev flask run --host 0.0.0.0 --port 5001 --debug

# Celery
cd api && uv run --dev celery -A app.celery worker -P gevent -c 1 --loglevel INFO

# Frontend
cd web && pnpm dev

# Tests
cd api && make lint && make type-check
cd web && pnpm lint && pnpm test
```

---

*🎉 Welcome to the Dify development community!*
