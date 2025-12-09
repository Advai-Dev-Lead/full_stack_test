# Bonus Challenges (Optional)

If you complete the main exercise early, here are additional features you can implement to showcase advanced skills. These are **completely optional** and won't negatively impact your assessment if not completed.


### 1. Docker Compose Setup
Create a `docker-compose.yml` that runs the entire stack locally.

**What to include:**
- Backend service
- Frontend service  
- PostgreSQL database (if you switched from in-memory)
- Redis cache (optional)

**Bonus points for:**
- Volume mounts for development
- Health checks
- Environment variable configuration
- README instructions for running

---

### 2. Enhanced Filtering & Search
Add more sophisticated filtering capabilities.

**Backend:**
- Full-text search on title/description
- Date range filtering (created between dates)
- Multiple status/priority filters (OR logic)
- Sorting (by date, priority, status)

**Frontend:**
- Search input with debouncing
- Advanced filter panel
- Sort dropdown
- Clear all filters button

---

### 3. Task Categories/Tags
Add the ability to categorize tasks.

**Backend:**
- Add `tags` field (array of strings)
- Filter by tags
- Endpoint to get all unique tags

**Frontend:**
- Tag input component (multi-select)
- Filter by tags
- Tag display with colors

---

### 4. Real Database with Migrations
Switch from in-memory storage to a proper database.

**Python + SQLAlchemy:**
```python
from sqlalchemy import create_engine, Column, String, DateTime
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

Base = declarative_base()

class Task(Base):
    __tablename__ = "tasks"
    
    id = Column(String, primary_key=True)
    title = Column(String(200), nullable=False)
    # ... other fields
```

**Or .NET + Entity Framework:**
```csharp
public class TaskDbContext : DbContext
{
    public DbSet<Task> Tasks { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Task>()
            .HasIndex(t => t.Status);
    }
}
```

---

### 5. Authentication & Authorization
Implement basic JWT authentication.

**Backend:**
- User registration/login endpoints
- JWT token generation
- Protected routes (require authentication)
- User ID associated with tasks

**Frontend:**
- Login/register forms
- Token storage (localStorage/cookies)
- Axios interceptors for auth headers
- Protected routes

---

### 6. AI Task Assistant with Free LLM API
Add an AI assistant that helps users with their tasks using a free LLM API.

**Recommended Free API: Groq**
- Sign up at https://console.groq.com (free tier available)
- Get API key (no credit card required)
- Fast inference with open models (Llama 3, Mixtral, etc.)

**Alternative Free APIs:**
- Hugging Face Inference API (https://huggingface.co/inference-api)
- Together AI (https://api.together.xyz) - free tier available
- Anthropic Claude (limited free tier)

**Backend Implementation:**

**Python (FastAPI):**
```python
from groq import Groq
import os

# Initialize Groq client
client = Groq(api_key=os.environ.get("GROQ_API_KEY"))

@app.post("/api/tasks/{task_id}/ai-suggestions")
async def get_ai_suggestions(task_id: str, task: Task):
    """Generate AI suggestions for improving a task."""
    
    prompt = f"""Given this task:
Title: {task.title}
Description: {task.description}
Status: {task.status}
Priority: {task.priority}

Provide 3 helpful suggestions to improve or complete this task. Be concise and actionable."""

    try:
        chat_completion = client.chat.completions.create(
            messages=[
                {
                    "role": "user",
                    "content": prompt,
                }
            ],
            model="llama-3.1-8b-instant",  # Fast, free model
            temperature=0.7,
            max_tokens=300
        )
        
        suggestions = chat_completion.choices[0].message.content
        
        return {
            "task_id": task_id,
            "suggestions": suggestions,
            "model": "llama-3.1-8b-instant"
        }
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"AI service error: {str(e)}")

@app.post("/api/tasks/generate")
async def generate_task_from_prompt(prompt: str):
    """Generate a task from natural language."""
    
    system_prompt = """You are a task creation assistant. Convert the user's request into a structured task.
Respond ONLY with valid JSON in this format:
{
  "title": "short title",
  "description": "detailed description",
  "priority": "low|medium|high"
}"""

    try:
        chat_completion = client.chat.completions.create(
            messages=[
                {"role": "system", "content": system_prompt},
                {"role": "user", "content": prompt}
            ],
            model="llama-3.1-8b-instant",
            temperature=0.5,
            max_tokens=200
        )
        
        import json
        task_data = json.loads(chat_completion.choices[0].message.content)
        
        return task_data
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"AI generation error: {str(e)}")
```

**.NET (ASP.NET Core):**
```csharp
using System.Net.Http;
using System.Text.Json;

public class GroqService
{
    private readonly HttpClient _httpClient;
    private readonly string _apiKey;

    public GroqService(IConfiguration config)
    {
        _httpClient = new HttpClient();
        _apiKey = config["GROQ_API_KEY"];
        _httpClient.DefaultRequestHeaders.Add("Authorization", $"Bearer {_apiKey}");
    }

    public async Task<string> GetTaskSuggestions(TaskItem task)
    {
        var request = new
        {
            model = "llama-3.1-8b-instant",
            messages = new[]
            {
                new { role = "user", content = $"Given this task: {task.Title}. Provide 3 suggestions." }
            },
            temperature = 0.7,
            max_tokens = 300
        };

        var response = await _httpClient.PostAsJsonAsync(
            "https://api.groq.com/openai/v1/chat/completions",
            request
        );

        var result = await response.Content.ReadFromJsonAsync<GroqResponse>();
        return result.Choices[0].Message.Content;
    }
}
```

**Frontend Features:**

```typescript
// AiAssistant.tsx
import React, { useState } from 'react';

interface AiAssistantProps {
  taskId: string;
  task: Task;
}

const AiAssistant: React.FC<AiAssistantProps> = ({ taskId, task }) => {
  const [suggestions, setSuggestions] = useState<string>('');
  const [loading, setLoading] = useState(false);

  const getSuggestions = async () => {
    setLoading(true);
    try {
      const response = await fetch(`/api/tasks/${taskId}/ai-suggestions`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(task)
      });
      const data = await response.json();
      setSuggestions(data.suggestions);
    } catch (error) {
      console.error('AI error:', error);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="ai-assistant">
      <button onClick={getSuggestions} disabled={loading}>
        {loading ? '🤔 Thinking...' : '✨ Get AI Suggestions'}
      </button>
      {suggestions && (
        <div className="ai-suggestions">
          <h4>AI Suggestions:</h4>
          <p>{suggestions}</p>
        </div>
      )}
    </div>
  );
};

// Smart Task Creator
const SmartTaskCreator: React.FC = () => {
  const [prompt, setPrompt] = useState('');
  
  const createFromPrompt = async () => {
    const response = await fetch('/api/tasks/generate', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ prompt })
    });
    const taskData = await response.json();
    // Create task with AI-generated data
  };

  return (
    <div>
      <textarea 
        placeholder="Describe your task in plain language..."
        value={prompt}
        onChange={(e) => setPrompt(e.target.value)}
      />
      <button onClick={createFromPrompt}>Create with AI</button>
    </div>
  );
};
```

**Environment Setup:**
```bash
# .env file
GROQ_API_KEY=your_groq_api_key_here
```

**Installation:**
```bash
# Python
pip install groq

# Node.js (if using TS/Node backend)
npm install groq-sdk
```

**What to showcase:**
- API integration skills
- Error handling for external services
- Rate limiting awareness
- User experience during async operations
- Proper secret management

**Bonus points for:**
- Streaming responses (real-time text generation)
- Caching AI responses to reduce API calls
- Retry logic with exponential backoff
- UI loading states and error messages
- Token usage tracking

---

### 7. Real-time Updates with WebSockets
Make the task list update in real-time.

**Backend (FastAPI):**
```python
from fastapi import WebSocket

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    # Broadcast task updates to all connected clients
```

**Frontend:**
```typescript
const ws = new WebSocket('ws://localhost:8000/ws');

ws.onmessage = (event) => {
  const task = JSON.parse(event.data);
  // Update local state
};
```

---

### 7. Comprehensive Test Suite
Write extensive tests covering multiple layers.

**Backend:**
- Unit tests for business logic
- Integration tests for API endpoints
- Test fixtures and factories
- >80% code coverage

**Frontend:**
- Component tests (React Testing Library)
- Integration tests
- Mock API calls
- Snapshot tests

**Example:**
```typescript
// TaskList.test.tsx
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import TaskList from './TaskList';

test('displays tasks after loading', async () => {
  render(<TaskList />);
  
  await waitFor(() => {
    expect(screen.getByText('Test Task')).toBeInTheDocument();
  });
});
```

---

### 8. Advanced Deployment with Terraform
Create actual Infrastructure as Code.

**What to include:**
- VPC and networking setup
- ECS cluster and service definitions
- RDS database configuration
- ALB and target groups
- CloudFront distribution
- Route53 DNS records
- Secrets Manager for sensitive data

**Bonus points for:**
- Multiple environments (dev, staging, prod)
- Remote state management (S3 + DynamoDB)
- Terraform modules for reusability
- Output variables

---

### 9. CI/CD Pipeline Implementation
Set up a working GitHub Actions pipeline.

**Pipeline stages:**
1. Lint and format check
2. Run tests
3. Build Docker images
4. Push to ECR
5. Deploy to ECS
6. Run smoke tests

**Example workflow:**
```yaml
name: Deploy Production

on:
  push:
    branches: [ main ]

jobs:
  test:
    # Run tests
  
  build-and-deploy:
    needs: test
    # Build and deploy
  
  smoke-test:
    needs: build-and-deploy
    # Verify deployment
```

---

### 10. Monitoring & Observability
Implement comprehensive monitoring.

**Backend:**
- Structured logging (JSON format)
- Custom metrics (Prometheus/CloudWatch)
- Request tracing (OpenTelemetry)
- Performance monitoring

**Example:**
```python
import logging
import time
from functools import wraps

logger = logging.getLogger(__name__)

def track_performance(func):
    @wraps(func)
    async def wrapper(*args, **kwargs):
        start = time.time()
        result = await func(*args, **kwargs)
        duration = time.time() - start
        
        logger.info({
            "function": func.__name__,
            "duration_ms": duration * 1000,
            "status": "success"
        })
        
        return result
    return wrapper
```

---


### 11. GraphQL API Alternative
Provide a GraphQL interface alongside REST.

**Backend:**
```python
import strawberry
from strawberry.fastapi import GraphQLRouter

@strawberry.type
class Task:
    id: str
    title: str
    description: str
    status: str
    priority: str

@strawberry.type
class Query:
    @strawberry.field
    def tasks(self, status: Optional[str] = None) -> List[Task]:
        # Return tasks
        pass

schema = strawberry.Schema(query=Query)
graphql_app = GraphQLRouter(schema)

app.include_router(graphql_app, prefix="/graphql")
```

---

### 12. Caching Layer with Redis
Implement intelligent caching.

**What to cache:**
- Task lists (5-minute TTL)
- Statistics (1-minute TTL)
- User profile data

**Cache strategies:**
- Cache-aside (lazy loading)
- Write-through for updates
- Cache invalidation on mutations

**Example:**
```python
import redis
import json

redis_client = redis.Redis(host='localhost', port=6379)

def get_tasks_cached(status: str = None):
    cache_key = f"tasks:status:{status or 'all'}"
    
    # Try cache first
    cached = redis_client.get(cache_key)
    if cached:
        return json.loads(cached)
    
    # Fetch from database
    tasks = fetch_tasks_from_db(status)
    
    # Cache for 5 minutes
    redis_client.setex(cache_key, 300, json.dumps(tasks))
    
    return tasks
```

---

### 13. Microservices Architecture
Split the application into multiple services.

**Services:**
1. **Task Service**: CRUD operations
2. **Stats Service**: Analytics and reporting
3. **Notification Service**: Email/push notifications
4. **API Gateway**: Request routing

**Communication:**
- Synchronous: REST or gRPC
- Asynchronous: Message queue (RabbitMQ/SQS)

---

### 14. Advanced Features

**Task Templates:**
- Predefined task templates
- Template library
- Quick create from template

**Task Dependencies:**
- Link tasks together
- Block tasks until dependencies complete
- Gantt chart visualization

**Recurring Tasks:**
- Schedule recurring tasks (daily, weekly, monthly)
- Cron-like scheduling
- Automatic task creation

**Collaboration:**
- Assign tasks to users
- Task comments/notes
- Activity history log
- @mentions in comments

**File Attachments:**
- Upload files to tasks
- S3 integration
- File preview
- Download/delete files

**Bulk Operations:**
- Select multiple tasks
- Bulk status change
- Bulk delete
- Bulk priority update

---

## 💡 Suggestions for Showcasing Skills

### For Junior Developers:
Focus on 1-2 Level 1 bonuses that show:
- You can follow best practices
- You understand Docker basics
- You can write clean, working code

### For Mid-Level Developers:
Complete 1-2 Level 2 bonuses that demonstrate:
- Database knowledge
- Testing capabilities
- Security awareness
- Understanding of modern development practices

### For Senior Developers:
Tackle 1-2 Level 3 or Expert bonuses that showcase:
- System design thinking
- Production-readiness mindset
- Deep technical expertise
- Trade-off analysis

---

## 📝 Documentation for Bonuses

If you implement any bonuses, make sure to:

1. **Update README.md** with setup instructions
2. **Document trade-offs** in ARCHITECTURE.md
3. **Explain implementation** in code comments
4. **Add to your submission notes** what you built and why

---

## ⏱️ Time Management

If you're finishing early:

**With 30 minutes left:** Try Level 1 bonus (Docker Compose or Search)
**With 45 minutes left:** Try Level 2 bonus (Database or Tests)
**With 60+ minutes left:** Try Level 3 or Expert bonus

Remember: **Quality > Quantity**

Better to have the main requirements done perfectly than to rush through bonuses!

---

## 🎯 What Impresses Us Most

Rather than implementing many bonuses, we prefer to see:

✨ **Clean, production-ready code**
✨ **Thoughtful architecture decisions**
✨ **Comprehensive error handling**
✨ **Good test coverage**
✨ **Clear, concise documentation**
✨ **Security awareness**
✨ **Performance considerations**

Choose bonuses that align with your strengths and demonstrate skills relevant to the senior/mid/junior role you're applying for!

Good luck! 🚀
