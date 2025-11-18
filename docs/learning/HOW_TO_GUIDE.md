# How-To Guide

## 🎯 Purpose

This is your **practical cookbook** for common development tasks in Dify. Each section provides step-by-step instructions with real code examples.

**Time to complete each:** 15-45 minutes

---

## 📋 Table of Contents

1. [Add a New API Endpoint](#1-add-a-new-api-endpoint)
2. [Create a New React Component](#2-create-a-new-react-component)
3. [Add a Database Model](#3-add-a-database-model)
4. [Create a Celery Task](#4-create-a-celery-task)
5. [Add Translations](#5-add-translations)
6. [Add a New Workflow Node](#6-add-a-new-workflow-node)

---

## 1. Add a New API Endpoint

**Goal:** Create a new REST API endpoint to fetch app statistics.

### Step 1: Create the Controller

```python
# api/controllers/console/app/statistics.py

from flask import request
from flask_restful import Resource, reqparse
from controllers.console import api
from controllers.console.wraps import account_initialization_required
from libs.login import login_required
from services.app_statistics_service import AppStatisticsService

class AppStatisticsApi(Resource):
    @login_required
    @account_initialization_required
    def get(self, app_id):
        """Get app statistics"""
        # Parse query parameters
        parser = reqparse.RequestParser()
        parser.add_argument('period', type=str, default='7d', location='args')
        args = parser.parse_args()

        # Call service
        statistics = AppStatisticsService.get_statistics(
            app_id=app_id,
            tenant_id=g.current_tenant.id,
            period=args['period'],
        )

        return statistics, 200

# Register route
api.add_resource(
    AppStatisticsApi,
    '/apps/<uuid:app_id>/statistics'
)
```

🔗 **Reference:** [api/controllers/console/app/app.py](../../api/controllers/console/app/app.py)

### Step 2: Create the Service

```python
# api/services/app_statistics_service.py

from datetime import datetime, timedelta
from sqlalchemy import func
from models.model import App, Message, Conversation
from extensions.ext_database import db

class AppStatisticsService:
    @staticmethod
    def get_statistics(
        app_id: str,
        tenant_id: str,
        period: str = '7d',
    ) -> dict:
        # Calculate date range
        days = int(period.replace('d', ''))
        start_date = datetime.utcnow() - timedelta(days=days)

        # Verify app ownership
        app = App.query.filter_by(
            id=app_id,
            tenant_id=tenant_id,
        ).first_or_404()

        # Query message count
        message_count = Message.query.filter(
            Message.app_id == app_id,
            Message.created_at >= start_date,
        ).count()

        # Query conversation count
        conversation_count = Conversation.query.filter(
            Conversation.app_id == app_id,
            Conversation.created_at >= start_date,
        ).count()

        # Query active users (unique end_users)
        active_users = db.session.query(
            func.count(func.distinct(Conversation.from_end_user_id))
        ).filter(
            Conversation.app_id == app_id,
            Conversation.created_at >= start_date,
        ).scalar()

        return {
            'period': period,
            'message_count': message_count,
            'conversation_count': conversation_count,
            'active_users': active_users or 0,
        }
```

### Step 3: Test the Endpoint

```bash
# Start the backend
cd api
uv run --dev flask run --host 0.0.0.0 --port 5001 --debug

# Test with curl
curl -X GET \
  'http://localhost:5001/console/api/apps/YOUR_APP_ID/statistics?period=30d' \
  -H 'Cookie: access_token=YOUR_TOKEN'
```

### Step 4: Add Frontend Integration

```typescript
// web/service/apps.ts

export const fetchAppStatistics = (
  appId: string,
  period: string = '7d'
): Promise<{ message_count: number; conversation_count: number; active_users: number }> => {
  return get(`/apps/${appId}/statistics`, { params: { period } })
}

// web/app/components/app/overview/statistics.tsx
import { useQuery } from '@tanstack/react-query'
import { fetchAppStatistics } from '@/service/apps'

export const AppStatistics = ({ appId }: { appId: string }) => {
  const { data, isLoading } = useQuery({
    queryKey: ['appStatistics', appId],
    queryFn: () => fetchAppStatistics(appId, '7d'),
  })

  if (isLoading) return <div>Loading...</div>

  return (
    <div className="grid grid-cols-3 gap-4">
      <StatCard title="Messages" value={data.message_count} />
      <StatCard title="Conversations" value={data.conversation_count} />
      <StatCard title="Active Users" value={data.active_users} />
    </div>
  )
}
```

✅ **Success Criteria:**
- Endpoint returns statistics
- Frontend displays data
- Multi-tenancy enforced (tenant_id check)

---

## 2. Create a New React Component

**Goal:** Create a reusable "Badge" component.

### Step 1: Create Component File

```tsx
// web/app/components/base/badge/index.tsx

import type { FC } from 'react'
import classNames from 'classnames'

type BadgeVariant = 'primary' | 'success' | 'warning' | 'danger'
type BadgeSize = 'sm' | 'md' | 'lg'

export type BadgeProps = {
  children: React.ReactNode
  variant?: BadgeVariant
  size?: BadgeSize
  className?: string
}

const Badge: FC<BadgeProps> = ({
  children,
  variant = 'primary',
  size = 'md',
  className,
}) => {
  return (
    <span
      className={classNames(
        'inline-flex items-center justify-center rounded-full font-medium',
        // Variants
        {
          'bg-blue-100 text-blue-800': variant === 'primary',
          'bg-green-100 text-green-800': variant === 'success',
          'bg-yellow-100 text-yellow-800': variant === 'warning',
          'bg-red-100 text-red-800': variant === 'danger',
        },
        // Sizes
        {
          'px-2 py-0.5 text-xs': size === 'sm',
          'px-2.5 py-1 text-sm': size === 'md',
          'px-3 py-1.5 text-base': size === 'lg',
        },
        className
      )}
    >
      {children}
    </span>
  )
}

export default Badge
```

🔗 **Reference:** [web/app/components/base/button/index.tsx](../../web/app/components/base/button/index.tsx)

### Step 2: Create TypeScript Types (Optional)

```typescript
// web/app/components/base/badge/types.ts

export type BadgeVariant = 'primary' | 'success' | 'warning' | 'danger'
export type BadgeSize = 'sm' | 'md' | 'lg'
```

### Step 3: Export from Index

```typescript
// web/app/components/base/badge/index.tsx (already done above)
export default Badge
export type { BadgeProps } from './index'
```

### Step 4: Use the Component

```tsx
// web/app/components/app/app-card.tsx

import Badge from '@/app/components/base/badge'

export const AppCard = ({ app }) => {
  return (
    <div className="p-4 border rounded">
      <h3>{app.name}</h3>
      <Badge variant={app.status === 'active' ? 'success' : 'danger'}>
        {app.status}
      </Badge>
    </div>
  )
}
```

### Step 5: Write Tests (Optional but Recommended)

```typescript
// web/__tests__/components/base/badge.test.tsx

import { render, screen } from '@testing-library/react'
import Badge from '@/app/components/base/badge'

describe('Badge', () => {
  it('renders children', () => {
    render(<Badge>Test</Badge>)
    expect(screen.getByText('Test')).toBeInTheDocument()
  })

  it('applies variant classes', () => {
    const { container } = render(<Badge variant="success">Success</Badge>)
    expect(container.firstChild).toHaveClass('bg-green-100', 'text-green-800')
  })

  it('applies size classes', () => {
    const { container } = render(<Badge size="lg">Large</Badge>)
    expect(container.firstChild).toHaveClass('px-3', 'py-1.5', 'text-base')
  })
})
```

Run tests:
```bash
cd web
pnpm test badge
```

✅ **Success Criteria:**
- Component renders correctly
- Tailwind classes applied
- TypeScript types work
- Tests pass

---

## 3. Add a Database Model

**Goal:** Create a new "AppTag" model for tagging apps.

### Step 1: Create the Model

```python
# api/models/app_tag.py

from sqlalchemy import Column, String, DateTime, ForeignKey
from sqlalchemy.orm import relationship
from datetime import datetime
from extensions.ext_database import db

class AppTag(db.Model):
    __tablename__ = 'app_tags'
    __table_args__ = (
        db.UniqueConstraint('app_id', 'tag', name='unique_app_tag'),
    )

    id = Column(String(255), primary_key=True, server_default=db.text('uuid_generate_v4()'))
    app_id = Column(String(255), ForeignKey('apps.id', ondelete='CASCADE'), nullable=False)
    tag = Column(String(50), nullable=False)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    created_by = Column(String(255), nullable=False)

    # Relationships
    app = relationship('App', back_populates='tags')

    def __repr__(self):
        return f'<AppTag {self.tag} for app {self.app_id}>'

    def to_dict(self):
        return {
            'id': self.id,
            'app_id': self.app_id,
            'tag': self.tag,
            'created_at': self.created_at.isoformat(),
        }
```

🔗 **Reference:** [api/models/model.py](../../api/models/model.py)

### Step 2: Add Relationship to App Model

```python
# api/models/model.py

class App(db.Model):
    # ... existing fields ...

    # Add this relationship
    tags = db.relationship(
        'AppTag',
        back_populates='app',
        cascade='all, delete-orphan'
    )
```

### Step 3: Export the Model

```python
# api/models/__init__.py

from .app_tag import AppTag

# Add to __all__
__all__ = [
    # ... existing models ...
    'AppTag',
]
```

### Step 4: Create Migration

```bash
cd api

# Generate migration
uv run flask db migrate -m "Add app_tags table"

# Review the generated migration file
# api/migrations/versions/XXXX_add_app_tags_table.py
```

**Review the migration:**

```python
# api/migrations/versions/XXXX_add_app_tags_table.py

def upgrade():
    op.create_table(
        'app_tags',
        sa.Column('id', sa.String(255), nullable=False),
        sa.Column('app_id', sa.String(255), nullable=False),
        sa.Column('tag', sa.String(50), nullable=False),
        sa.Column('created_at', sa.DateTime(), nullable=False),
        sa.Column('created_by', sa.String(255), nullable=False),
        sa.ForeignKeyConstraint(['app_id'], ['apps.id'], ondelete='CASCADE'),
        sa.PrimaryKeyConstraint('id'),
        sa.UniqueConstraint('app_id', 'tag', name='unique_app_tag')
    )

def downgrade():
    op.drop_table('app_tags')
```

### Step 5: Apply Migration

```bash
# Apply migration
uv run flask db upgrade

# Verify
uv run flask db current
```

### Step 6: Test the Model

```python
# Create a tag
tag = AppTag(
    app_id=app.id,
    tag='production',
    created_by=current_user.id,
)
db.session.add(tag)
db.session.commit()

# Query tags
tags = AppTag.query.filter_by(app_id=app.id).all()

# Query app with tags
app = App.query.options(db.joinedload(App.tags)).get(app_id)
print([tag.tag for tag in app.tags])
```

✅ **Success Criteria:**
- Migration runs without errors
- Foreign key works
- Unique constraint enforced
- Relationship queries work

---

## 4. Create a Celery Task

**Goal:** Create a task to cleanup old conversations.

### Step 1: Define the Task

```python
# api/tasks/cleanup_tasks.py

from celery import shared_task
from datetime import datetime, timedelta
from models.model import Conversation
from extensions.ext_database import db
import logging

logger = logging.getLogger(__name__)

@shared_task(bind=True, max_retries=3)
def cleanup_old_conversations(self, days: int = 90):
    """
    Delete conversations older than specified days.

    Args:
        days: Number of days to keep conversations
    """
    try:
        cutoff_date = datetime.utcnow() - timedelta(days=days)

        # Find old conversations
        old_conversations = Conversation.query.filter(
            Conversation.created_at < cutoff_date
        ).all()

        logger.info(f"Found {len(old_conversations)} conversations to delete")

        # Delete in batches
        batch_size = 100
        deleted_count = 0

        for i in range(0, len(old_conversations), batch_size):
            batch = old_conversations[i:i + batch_size]

            for conversation in batch:
                db.session.delete(conversation)

            db.session.commit()
            deleted_count += len(batch)

            # Update progress
            self.update_state(
                state='PROGRESS',
                meta={'deleted': deleted_count, 'total': len(old_conversations)}
            )

            logger.info(f"Deleted {deleted_count}/{len(old_conversations)}")

        return {
            'status': 'completed',
            'deleted_count': deleted_count,
        }

    except Exception as e:
        logger.error(f"Error cleaning up conversations: {str(e)}")
        # Retry with exponential backoff
        raise self.retry(exc=e, countdown=60 * (2 ** self.request.retries))
```

🔗 **Reference:** [api/tasks/rag_pipeline/](../../api/tasks/rag_pipeline/)

### Step 2: Register the Task

```python
# api/tasks/__init__.py

from .cleanup_tasks import cleanup_old_conversations

__all__ = [
    'cleanup_old_conversations',
]
```

### Step 3: Schedule the Task (Optional)

```python
# api/extensions/ext_celery.py or app config

from celery.schedules import crontab

app.conf.beat_schedule = {
    'cleanup-old-conversations': {
        'task': 'tasks.cleanup_tasks.cleanup_old_conversations',
        'schedule': crontab(hour=2, minute=0),  # Run daily at 2 AM
        'args': (90,),  # Keep 90 days
    },
}
```

### Step 4: Queue the Task Manually

```python
# In your controller or service
from tasks.cleanup_tasks import cleanup_old_conversations

# Queue immediately
task = cleanup_old_conversations.delay(days=90)

# Queue with delay (1 hour)
task = cleanup_old_conversations.apply_async(
    args=[90],
    countdown=3600,
)

# Get task status
from celery.result import AsyncResult
result = AsyncResult(task.id)
if result.ready():
    print(result.result)
else:
    print(result.state)  # PENDING, PROGRESS, SUCCESS, FAILURE
```

### Step 5: Monitor the Task

```bash
# Start Celery worker
cd api
uv run --dev celery -A app.celery worker -P gevent -c 1 --loglevel INFO

# In logs, you'll see:
# [tasks]
#   . tasks.cleanup_tasks.cleanup_old_conversations
# [INFO] Found 150 conversations to delete
# [INFO] Deleted 100/150
# [INFO] Deleted 150/150
```

✅ **Success Criteria:**
- Task executes successfully
- Progress updates work
- Retry logic handles errors
- Scheduled execution works (if configured)

---

## 5. Add Translations

**Goal:** Add multi-language support for a new feature.

### Step 1: Add English Translations

```typescript
// web/i18n/en-US/app.ts

const translation = {
  // ... existing translations ...

  // Add your new keys
  statistics: {
    title: 'Statistics',
    messageCount: 'Messages',
    conversationCount: 'Conversations',
    activeUsers: 'Active Users',
    period: {
      '7d': 'Last 7 days',
      '30d': 'Last 30 days',
      '90d': 'Last 90 days',
    },
  },
}

export default translation
```

🔗 **Reference:** [web/i18n/en-US/app.ts](../../web/i18n/en-US/app.ts)

### Step 2: Auto-Generate Other Languages

```bash
cd web

# Auto-translate to all languages
pnpm auto-gen-i18n

# This will update:
# - web/i18n/zh-Hans/app.ts (Chinese)
# - web/i18n/ja-JP/app.ts (Japanese)
# - web/i18n/ko-KR/app.ts (Korean)
# - ... (20+ languages)
```

### Step 3: Use Translations in Components

```typescript
// web/app/components/app/statistics.tsx

import { useTranslation } from 'react-i18next'

export const AppStatistics = () => {
  const { t } = useTranslation()

  return (
    <div>
      <h2>{t('app.statistics.title')}</h2>
      <div>
        <span>{t('app.statistics.messageCount')}: </span>
        <span>{data.messageCount}</span>
      </div>
    </div>
  )
}
```

### Step 4: Verify All Languages

```bash
# Check for missing translations
pnpm check-i18n

# Expected output:
# ✓ All translations are in sync
# or
# ✗ Missing keys in ja-JP: app.statistics.activeUsers
```

⚠️ **Common Pitfall:** Always use `t()` function! Never hardcode text.

```typescript
// ❌ Bad
<button>Create App</button>

// ✅ Good
<button>{t('app.createApp')}</button>
```

✅ **Success Criteria:**
- English translations added
- Auto-generated translations exist
- `check-i18n` passes
- UI shows translated text

---

## 6. Add a New Workflow Node

**Goal:** Create a custom "Uppercase" workflow node.

### Step 1: Create Node Class

```python
# api/core/workflow/nodes/uppercase.py

from typing import Any
from core.workflow.nodes.base import BaseNode
from core.workflow.entities.node_entities import NodeRunResult

class UppercaseNode(BaseNode):
    """
    Transform text to uppercase.
    """
    _node_type = 'uppercase'
    _node_title = 'Uppercase'

    def _run(self, inputs: dict[str, Any]) -> NodeRunResult:
        """
        Execute the node.

        Args:
            inputs: Dictionary containing 'text' key

        Returns:
            NodeRunResult with uppercase text
        """
        # Get input
        text = inputs.get('text', '')

        # Validate
        if not isinstance(text, str):
            return NodeRunResult(
                status='failed',
                error='Input must be a string',
            )

        # Transform
        result = text.upper()

        return NodeRunResult(
            status='success',
            outputs={'result': result},
            metadata={'original_length': len(text)},
        )

    @classmethod
    def get_default_config(cls) -> dict:
        """Default configuration for the node."""
        return {
            'inputs': {
                'text': {
                    'type': 'string',
                    'required': True,
                },
            },
            'outputs': {
                'result': {
                    'type': 'string',
                },
            },
        }
```

🔗 **Reference:** [api/core/workflow/nodes/](../../api/core/workflow/nodes/)

### Step 2: Register the Node

```python
# api/core/workflow/nodes/__init__.py

from .uppercase import UppercaseNode

# Add to node registry
NODE_TYPES = {
    # ... existing nodes ...
    'uppercase': UppercaseNode,
}
```

### Step 3: Create Frontend Node Component

```tsx
// web/app/components/workflow/nodes/uppercase/index.tsx

import type { FC } from 'react'
import { useTranslation } from 'react-i18next'
import { NodeProps } from '@/app/components/workflow/types'

const UppercaseNode: FC<NodeProps> = ({
  data,
  onChange,
}) => {
  const { t } = useTranslation()

  return (
    <div className="p-4 bg-white rounded border">
      <div className="font-medium mb-2">
        {t('workflow.nodes.uppercase.title')}
      </div>
      <div className="text-sm text-gray-500">
        Transforms text to uppercase
      </div>
      {/* Node configuration UI */}
      <div className="mt-4">
        <label className="text-sm">Input Text</label>
        <input
          type="text"
          value={data.config.text || ''}
          onChange={(e) => onChange({ text: e.target.value })}
          className="w-full border rounded px-2 py-1"
        />
      </div>
    </div>
  )
}

export default UppercaseNode
```

### Step 4: Register Frontend Node

```typescript
// web/app/components/workflow/nodes/index.ts

import UppercaseNode from './uppercase'

export const NODE_COMPONENTS = {
  // ... existing nodes ...
  uppercase: UppercaseNode,
}
```

### Step 5: Test the Node

```python
# Test in Python
from core.workflow.nodes.uppercase import UppercaseNode

node = UppercaseNode(
    tenant_id='test',
    config={'text': 'hello world'},
)

result = node.run({'text': 'hello world'})
assert result.outputs['result'] == 'HELLO WORLD'
```

```bash
# Test in workflow
# 1. Create workflow in UI
# 2. Add "Uppercase" node
# 3. Connect to LLM node
# 4. Run workflow
# 5. Verify output is uppercase
```

✅ **Success Criteria:**
- Node appears in workflow palette
- Node executes correctly
- Output displays properly
- Error handling works

---

## ✅ Quick Reference

### Common Commands

```bash
# Backend
cd api
uv sync --dev                          # Install dependencies
uv run flask run --debug               # Start dev server
uv run flask db migrate -m "message"   # Create migration
uv run flask db upgrade                # Run migrations
make lint                              # Run linter
make type-check                        # Type check

# Frontend
cd web
pnpm install                           # Install dependencies
pnpm dev                               # Start dev server
pnpm lint                              # Run ESLint
pnpm type-check                        # TypeScript check
pnpm test                              # Run tests
pnpm auto-gen-i18n                     # Generate translations

# Docker
cd docker
docker compose -f docker-compose.middleware.yaml up -d  # Start services
docker compose -f docker-compose.middleware.yaml down   # Stop services

# Git
git add .
git commit -m "feat: add new feature"
git push origin your-branch
```

---

## 📚 Next Steps

Now that you can build features:

1. **[Code Tours](./CODE_TOURS.md)** - Follow real feature implementations
2. **[Testing Guide](./TESTING_GUIDE.md)** - Write tests for your code
3. **[Debugging Guide](./DEBUGGING_GUIDE.md)** - Debug issues
4. **[First Contributions](./FIRST_CONTRIBUTIONS.md)** - Submit your first PR

---

*Happy coding! You're now ready to build Dify features.* 🚀
