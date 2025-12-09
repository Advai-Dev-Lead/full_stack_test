# Full Stack Developer Assessment

**Time Limit:** 2 hours  
**Target Roles:** Junior, Mid, Senior Full Stack Developers  
**Core Technologies:** React, TypeScript, Python/.NET, AWS

## Overview

Build a **Task Management API and UI** that demonstrates your ability to work across the full stack. This exercise evaluates your skills in backend development, frontend implementation, cloud architecture understanding, and software engineering best practices.

## The Challenge

Create a simplified task management system with the following components:

### Part 1: Backend API (45-60 minutes)
**Choose ONE:** Python (FastAPI/Flask) OR .NET (ASP.NET Core)

Build a RESTful API with the following endpoints:

#### Required Endpoints:
- `POST /api/tasks` - Create a new task
- `GET /api/tasks` - List all tasks with filtering & pagination
- `GET /api/tasks/{id}` - Get a specific task
- `PUT /api/tasks/{id}` - Update a task
- `DELETE /api/tasks/{id}` - Delete a task
- `GET /api/tasks/stats` - Get task statistics (completion rate, counts by status/priority)

#### Data Model (Task):
```typescript
{
  id: string;
  title: string;
  description: string;
  status: "todo" | "in_progress" | "done";
  priority: "low" | "medium" | "high";
  createdAt: Date;
  updatedAt: Date;
  dueDate?: Date;
}
```

#### Requirements:
- ✅ Input validation
- ✅ Error handling with appropriate HTTP status codes
- ✅ Query parameters for filtering (status, priority) and pagination (page, limit)
- ✅ In-memory storage is acceptable (bonus: use SQLite or similar)
- ✅ API documentation (Swagger/OpenAPI preferred)
- ✅ At least 2-3 unit tests for critical endpoints

### Part 2: Frontend UI (45-60 minutes)
**Technology:** React + TypeScript

Build a web interface with the following features:

#### Required Features:
1. **Task List View**
   - Display all tasks in a table or card layout
   - Filter by status and priority
   - Show task count and completion statistics
   - Responsive design

2. **Create/Edit Task Form**
   - Modal or separate form to add/edit tasks
   - Form validation (required fields, date validation)
   - Error handling and user feedback

3. **Task Actions**
   - Mark tasks as complete
   - Delete tasks (with confirmation)
   - Edit existing tasks

#### Requirements:
- ✅ TypeScript with proper typing
- ✅ Component-based architecture
- ✅ State management (React hooks, Context API, or lightweight library)
- ✅ Error handling and loading states
- ✅ Clean, maintainable code structure
- ✅ Basic styling (CSS/SCSS or component library like MUI/Tailwind)


## Submission Guidelines

### What to Submit:
1. Complete source code (backend + frontend)
2. `README.md` with:
   - Setup instructions
   - How to run the application
   - How to run tests
   - Any assumptions made
3. `ARCHITECTURE.md` with system design
4. `DEPLOYMENT.md` with DevOps strategy

### How to Submit:
- Fork this repository or create a new repository
- Commit your work with meaningful commit messages
- Share the repository link for review.

### Time Management Tips:
- **0-15 min:** Project setup and planning
- **15-60 min:** Backend API development
- **60-105 min:** Frontend development
- **105-120 min:** Documentation, cleanup, testing

## Bonus Points (Optional)

If you finish early or have the inclination, consider adding:
- AI Task Assistant
- Docker Compose setup for local development
- GitHub Actions CI/CD workflow
- Advanced features (task assignments, tags, search)
- E2E tests (Playwright/Cypress)
- GraphQL API alternative
- Authentication/Authorization design
- Real AWS deployment with Terraform

## Getting Started

### Backend Options:

**Python (FastAPI):**
```bash
python -m venv venv
source venv/bin/activate  # or `venv\Scripts\activate` on Windows
pip install fastapi uvicorn pydantic pytest
```

**.NET:**
```bash
dotnet new webapi -n TaskApi
cd TaskApi
dotnet add package Swashbuckle.AspNetCore
```

### Frontend Setup:

```bash
npx create-react-app task-ui --template typescript
cd task-ui
npm install axios
# Or use Vite for faster builds:
npm create vite@latest task-ui -- --template react-ts
```

## Questions?

This is a practical, hands-on assessment. If you encounter ambiguities:
- Make reasonable assumptions
- Document your decisions
- Explain trade-offs in your architecture document

Good luck! We're excited to see your approach to solving this challenge. 
