# Candidate Instructions - Quick Start Guide

Welcome! This document will help you get started with the assessment quickly.

## Before You Begin

### Time Allocation (2 hours total)
- **0-15 min:** Read requirements, setup project structure
- **15-60 min:** Backend API development
- **60-105 min:** Frontend UI development
- **105-120 min:** Documentation, testing, final touches

### What You Need Installed
- **Backend:** Python 3.12+ OR .NET 8.0+
- **Frontend:** Node.js 18+
- **Optional:** Docker, Git
- **Editor:** VS Code (recommended) or your preferred IDE

---

## Step-by-Step Walkthrough

### Step 1: Project Setup (5 minutes)

Create your project structure:

```bash
# Create main project folder
mkdir task-management && cd task-management

# Create backend folder (choose one)
mkdir backend
# OR
mkdir api

# Create frontend folder
mkdir frontend

# Initialize git
git init
```

### Step 2: Backend Setup (Choose Your Stack)

#### Option A: Python + FastAPI (Recommended for speed)

```bash
cd backend

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install fastapi uvicorn pydantic pytest

# Create your files
touch main.py models.py repository.py
mkdir tests && touch tests/test_api.py

# Create requirements.txt
pip freeze > requirements.txt
```

**Quick Start Code:**
```python
# main.py - Minimal starter
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(title="Task Management API")

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/health")
def health_check():
    return {"status": "healthy"}

# Add your endpoints here...
```

**Run the server:**
```bash
uvicorn main:app --reload --port 8000
```

**API Documentation:** http://localhost:8000/docs

#### Option B: .NET + ASP.NET Core

```bash
cd backend

# Create new Web API project
dotnet new webapi -n TaskApi
cd TaskApi

# Add packages
dotnet add package Swashbuckle.AspNetCore

# Run the server
dotnet run
```

**API Documentation:** http://localhost:5000/swagger

### Step 3: Frontend Setup (10 minutes)

```bash
cd frontend

# Option A: Create React App (more familiar)
npx create-react-app . --template typescript

# Option B: Vite (faster builds)
npm create vite@latest . -- --template react-ts

# Install dependencies
npm install axios

# Optional: Install UI library
npm install @mui/material @emotion/react @emotion/styled
# OR
npm install tailwindcss

# Start development server
npm start  # or npm run dev for Vite
```

**Quick Start Component:**
```typescript
// src/App.tsx - Minimal starter
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const API_BASE = 'http://localhost:8000/api';

function App() {
  const [tasks, setTasks] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    axios.get(`${API_BASE}/tasks`)
      .then(res => setTasks(res.data))
      .catch(err => console.error(err))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <div>Loading...</div>;

  return (
    <div>
      <h1>Task Management</h1>
      {/* Add your components here */}
    </div>
  );
}

export default App;
```

---

## Development Workflow

### 1. Start with the Backend

**Priority Order:**
1. ✅ Define data models (Task, TaskCreate, etc.)
2. ✅ Implement in-memory storage or simple database
3. ✅ Create POST /api/tasks endpoint
4. ✅ Create GET /api/tasks endpoint
5. ✅ Add filtering and pagination to GET
6. ✅ Implement PUT and DELETE endpoints
7. ✅ Add GET /api/tasks/stats
8. ✅ Write 2-3 tests
9. ✅ Add error handling

**Testing Your API:**
```bash
# Test with curl
curl -X POST http://localhost:8000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Test Task","description":"Description","status":"todo","priority":"high"}'

# Or use the Swagger UI at http://localhost:8000/docs
```

### 2. Build the Frontend

**Priority Order:**
1. ✅ Create types/interfaces for Task
2. ✅ Create API service (taskService.ts)
3. ✅ Build TaskList component (display tasks)
4. ✅ Build TaskForm component (create/edit)
5. ✅ Add delete confirmation
6. ✅ Implement filtering UI
7. ✅ Add error handling and loading states
8. ✅ Style the application

**Component Structure:**
```
src/
├── types/
│   └── task.ts
├── services/
│   └── taskService.ts
├── components/
│   ├── TaskList.tsx
│   ├── TaskCard.tsx
│   ├── TaskForm.tsx
│   └── TaskFilters.tsx
├── hooks/
│   └── useTasks.ts
└── App.tsx
```

### 3. Documentation (Final 15 minutes)

Create these files in your root directory:

**README.md:**
```markdown
# Task Management System

## Setup Instructions

### Backend
```bash
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --reload
```

### Frontend
```bash
cd frontend
npm install
npm start
```

## Running Tests
```bash
cd backend
pytest
```

## Tech Stack
- Backend: Python + FastAPI
- Frontend: React + TypeScript
- Testing: Pytest
```

**ARCHITECTURE.md:**
```markdown
# Architecture

## System Design
[Describe your architecture]

## AWS Deployment
[How you would deploy to AWS]

## Database Design
[Your schema and indexes]

## Scaling Strategy
[How to handle growth]
```

**DEPLOYMENT.md:**
```markdown
# Deployment Strategy

## Docker Setup
[Dockerfile examples]

## Infrastructure as Code
[Terraform or CloudFormation approach]

## CI/CD Pipeline
[How you'd automate deployments]
```

---

## Common Pitfalls to Avoid

### ❌ Don't Do This:
- Spend 30 minutes setting up the perfect dev environment
- Try to implement authentication (not required)
- Build a complex database schema (in-memory is fine)
- Optimize prematurely (focus on working first)
- Write perfect documentation (clear is better than perfect)

### ✅ Do This Instead:
- Use simple, working solutions
- Focus on completing all required features
- Write clean, readable code
- Add basic error handling
- Document your decisions briefly

---

## Debugging Tips

### Backend Not Starting?
```bash
# Check if port 8000 is in use
lsof -i :8000  # Mac/Linux
netstat -ano | findstr :8000  # Windows

# Try a different port
uvicorn main:app --reload --port 8001
```

### CORS Errors?
Make sure your backend has CORS middleware configured:
```python
# FastAPI
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Frontend Not Connecting to Backend?
Check your API base URL:
```typescript
const API_BASE = 'http://localhost:8000/api';  // Make sure port matches!
```

---

## Quick Reference: API Endpoints

```
POST   /api/tasks           - Create task
GET    /api/tasks           - List tasks (with ?status=todo&priority=high&page=1&limit=10)
GET    /api/tasks/{id}      - Get single task
PUT    /api/tasks/{id}      - Update task
DELETE /api/tasks/{id}      - Delete task
GET    /api/tasks/stats     - Get statistics
```

---

## Example Task JSON

```json
{
  "title": "Implement user authentication",
  "description": "Add JWT-based authentication to the API",
  "status": "in_progress",
  "priority": "high",
  "dueDate": "2025-12-15T00:00:00Z"
}
```

---

## Time Check Points

### At 30 minutes:
- [ ] Backend project set up
- [ ] At least 2 endpoints working (POST and GET)
- [ ] Basic data model defined

### At 60 minutes:
- [ ] All backend endpoints functional
- [ ] Basic tests written
- [ ] Frontend project set up

### At 90 minutes:
- [ ] Task list displaying
- [ ] Create/edit form working
- [ ] Basic filtering implemented

### At 110 minutes:
- [ ] All features complete
- [ ] Basic styling done
- [ ] Starting documentation

### At 120 minutes:
- [ ] Final testing
- [ ] Documentation complete
- [ ] Code committed to git

---

## Submission Checklist

Before submitting, make sure you have:

- [ ] **Backend:**
  - [ ] All CRUD endpoints working
  - [ ] Filtering and pagination
  - [ ] Statistics endpoint
  - [ ] At least 2-3 tests
  - [ ] Error handling

- [ ] **Frontend:**
  - [ ] Task list view
  - [ ] Create/edit form
  - [ ] Delete with confirmation
  - [ ] Filtering UI
  - [ ] TypeScript types defined

- [ ] **Documentation:**
  - [ ] README.md with setup instructions
  - [ ] ARCHITECTURE.md with design decisions
  - [ ] DEPLOYMENT.md with Docker/IaC concepts
  - [ ] Code comments where needed

- [ ] **Code Quality:**
  - [ ] Clean, readable code
  - [ ] Consistent naming conventions
  - [ ] Git commits with clear messages
  - [ ] No obvious bugs or errors

---

## Need Help?

If you're stuck:
1. **Check the documentation:** Review README.md and SAMPLE_SOLUTIONS.md
2. **Test incrementally:** Make sure each feature works before moving to the next
3. **Keep it simple:** A working simple solution beats a broken complex one
4. **Document assumptions:** If something is unclear, document what you decided

---

## Good Luck! 🚀

Remember: We're looking for clean, working code that demonstrates your ability to build full-stack applications. Focus on:
- ✨ Functionality first
- ✨ Clean code structure
- ✨ Good error handling
- ✨ Clear documentation

You've got this! If you have questions about requirements, make reasonable assumptions and document them.
