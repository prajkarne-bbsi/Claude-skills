---
name: fullstack-template-generator
description: Generates production-ready fullstack applications with Python FastAPI backend and React TypeScript frontend. Creates complete working code following industry best practices including feature-based architecture, layered backend design, SQLAlchemy models, Pydantic schemas, custom React hooks, and full TypeScript coverage. Use this skill when the user requests a new fullstack project with specific features and requirements.
---

# Fullstack Template Generator

## Overview

This skill generates complete, working fullstack applications based on user requirements. It creates actual code files, not just folder structures:

### Backend Features
- FastAPI framework with async support and full CRUD operations
- Feature-based API organization with layered architecture (routes/controllers/services/repository)
  - *See [coding_standards_python.md](./coding_standards_python.md) Section 4: Business Logic Architecture*
- SQLAlchemy models with relationships and migrations
  - *See [coding_standards_python.md](./coding_standards_python.md) Section 2: Database Models*
- Pydantic schemas for validation and serialization
  - *See [coding_standards_python.md](./coding_standards_python.md) Section 3: API Design*
- JWT authentication and authorization
  - *See [coding_standards_python.md](./coding_standards_python.md) Section 11: Security*
- Database connection pooling and configuration
  - *See [coding_standards_python.md](./coding_standards_python.md) Section 10: Performance*
- CORS, middleware, and error handling
  - *See [coding_standards_python.md](./coding_standards_python.md) Section 5: Error Handling*
- Environment-based settings
- Swagger/ReDoc auto-documentation

### Frontend Features
- React 18+ with TypeScript and Vite build tool
- Feature-sliced design with self-contained modules
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 2: Project Structure*
- Axios integration with interceptors for auth and error handling
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 7: API Integration*
- Custom hooks for data fetching, state management, and business logic
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 6: State Management*
- TypeScript types for all API responses and components
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 5: TypeScript Usage*
- React Router with protected routes
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 9: Routing*
- Context API for global state (auth, theme, etc.)
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 6: State Management*
- Form handling with validation
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 4: Component Architecture*
- Loading states and error boundaries
  - *See [coding_standards_react.md](./coding_standards_react.md) Section 8: Performance Optimization*

## Coding Standards Reference

This skill follows strict coding standards documented in:

- **[coding_standards_python.md](./coding_standards_python.md)** - Backend architecture, database models, API design, security, performance
- **[coding_standards_react.md](./coding_standards_react.md)** - Frontend architecture, component patterns, TypeScript usage, state management

## What This Skill Creates

When invoked, this skill generates a complete working application with:

**Backend Structure:**
```
[project-name]/
└── backend/
   ├── main.py                      # FastAPI app entry point
   ├── requirements.txt             # Python dependencies
   ├── .env.example                 # Environment template
   ├── .env                         # Environment variables (gitignored)
   ├── .gitignore                   # Git ignore file
   ├── README.md                    # Backend documentation
   ├── pytest.ini                   # Test configuration
   ├── api/
   │   └── v1/                         # API version 1
   │       └── [feature_name]/         # Feature-based routes
   │           ├── routes.py           # API endpoints
   │           ├── schemas.py          # Pydantic request/response models
   │           ├── controllers.py      # Feature-specific business logic
   │           ├── services.py         # Reusable business logic
   │           └── repository.py       # Database operations
   ├── app/
   │   ├── cache/                      # Caching strategies
   │   │   └── redis_client.py         # Redis configuration
   │   ├── core/                       # Core application configuration
   │   │   ├── config.py               # Environment variables, settings
   │   │   ├── database.py             # Database connection, session
   │   │   ├── security.py             # Authentication, authorization
   │   │   └── logging.py              # Logging configuration
   │   ├── models/                     # SQLAlchemy models
   │   ├── constants/                  # Status codes, messages, enums
   │   ├── dependencies/               # Dependency injection (DB, auth)
   │   ├── exceptions/                 # Custom exceptions
   │   ├── middleware/                 # Auth, logging, CORS, rate limiting
   │   ├── utils/                      # Pure helper functions only
   │   ├── services/                   # Reusable business logic
   │   ├── integrations/               # Third-party services
   │   └── schemas/                    # Common Pydantic schemas
   ├── alembic/                        # Database migrations
   ├── tests/                          # Test suite
   └── scripts/                        # Utility scripts
```

**Frontend Structure:**
```
[project-name]/
└── frontend/
    ├── package.json                # Dependencies
    ├── vite.config.ts              # Build config (Vite)
    ├── tsconfig.json               # TypeScript config
    ├── .env.example                # Environment template
    ├── .env                        # Environment variables (gitignored)
    ├── .gitignore                  # Git ignore file
    ├── README.md                   # Frontend documentation
    └── src/
        ├── api/
        │   ├── axiosInstance.ts    # Axios setup + interceptors
        │   ├── config.ts           # Environment configs & endpoints
        │   └── globalApi.ts        # Shared API methods
        ├── assets/                 # Static resources
        │   ├── images/
        │   ├── icons/
        │   └── fonts/
        ├── components/             # Shared/reusable components
        │   ├── layout/             # Header, Footer, Sidebar
        │   ├── ui/                 # Button, Input, Card, Modal
        │   └── common/             # Other shared components
        ├── contexts/               # Global React contexts
        │   ├── AuthContext.tsx
        │   ├── ThemeContext.tsx
        │   └── TenantContext.tsx
        ├── hooks/                  # Global custom hooks
        │   ├── useDebounce.ts
        │   ├── useLocalStorage.ts
        │   └── useWindowSize.ts
        ├── routes/                 # Routing configuration
        │   ├── index.tsx           # Main route definitions
        │   └── ProtectedRoute.tsx  # Auth route wrapper
        ├── styles/                 # Global styles
        │   ├── globals.css
        │   ├── variables.css
        │   └── themes.css
        ├── types/                  # Global TypeScript types
        │   ├── api.ts
        │   ├── models.ts
        │   └── common.ts
        ├── utils/                  # Utility functions
        │   ├── formatters.ts
        │   ├── validators.ts
        │   └── helpers.ts
        └── features/[feature-name]/ # Feature modules
            ├── api/                # Feature-specific API calls
            │   └── [feature]Api.ts
            ├── components/         # Feature UI components
            │   ├── [Feature]List.tsx
            │   ├── [Feature]Form.tsx
            │   └── [Feature]Card.tsx
            ├── hooks/              # Feature custom hooks
            │   └── use[Feature].ts
            ├── pages/              # Feature pages/routes
            │   ├── [Feature]Page.tsx
            │   └── [Feature]DetailPage.tsx
            ├── types/              # Feature TypeScript types
            │   └── [feature].types.ts
            └── index.ts            # Public API of the feature
```

**Claude Skills Folder Structure:**
```
.claude/                            # Claude AI skills folder
└── skills/
    └── fullstack-template-generator/
        ├── SKILL.md                # This skill definition
        ├── coding_standards_python.md  # Backend coding standards
        └── coding_standards_react.md   # Frontend coding standards
```

## When to Use This Skill

Invoke this skill when the user:
- Wants to create a fullstack web application with specific features
- Requests a Python FastAPI backend with React TypeScript frontend
- Asks for CRUD operations, authentication, or data management features
- Needs production-ready code following best practices
- Wants feature-based architecture

## How to Generate the Application

### Step 1: Gather Requirements
Ask the user for:
- **Project name** (required)
- **Target directory** (required)
- **Features to implement** (e.g., "user authentication", "blog posts", "products and orders")

**Technology Stack (Fixed):**
- **Frontend**: React with TypeScript
- **Backend**: FastAPI (Python)
- **ORM**: SQLAlchemy
- **Database**: Azure SQL Server

### Step 2: Generate Backend Code

For each feature, generate all required layers following **[coding_standards_python.md](./coding_standards_python.md)**:

1. **Database Layer** (Section 2)
   - ORM models or SQL files
   - Connection pooling configuration (Section 10)

2. **API Layer** (Sections 3 & 4)
   - Pydantic schemas (Section 3.2)
   - Repository for database operations (Section 4.4)
   - Services for business logic (Section 4.3)
   - Controllers for coordination (Section 4.2)
   - Routes for HTTP endpoints (Section 4.1)

3. **Core Infrastructure**
   - Configuration (Section 1)
   - Security (Section 11)
   - Logging (Section 7)
   - Error handling (Section 5)
   - Caching if needed (Section 10)

### Step 3: Generate Frontend Code

For each feature, generate all required components following **[coding_standards_react.md](./coding_standards_react.md)**:

1. **Type Definitions** (Section 5)
   - Interfaces for entities and DTOs
   - Match backend schemas

2. **API Integration** (Section 7)
   - API client with CRUD methods
   - Axios configuration with interceptors

3. **State Management** (Section 6)
   - Custom hooks for data fetching
   - Loading and error states
   - AbortController for cleanup

4. **UI Components** (Section 4)
   - List, Detail, Form components
   - Proper TypeScript typing
   - Page components for routing

5. **Core Infrastructure**
   - Routing configuration (Section 9)
   - Global contexts (Section 6.1)
   - Error boundaries (Section 9)

### Step 4: Configuration Files

Generate all necessary configuration files as specified in:
- **Backend configs**: [coding_standards_python.md](./coding_standards_python.md) Section 1
- **Frontend configs**: [coding_standards_react.md](./coding_standards_react.md) Section 3

Include `.env.example`, `.env`, `.gitignore`, `README.md`, and framework-specific configs.

### Step 5: Follow Coding Standards

**All code generation MUST strictly follow:**
- **[coding_standards_python.md](./coding_standards_python.md)** - For all backend implementation details
- **[coding_standards_react.md](./coding_standards_react.md)** - For all frontend implementation details

Refer to these documents for specific patterns, naming conventions, and best practices.

## Key Architectural Principles

### Backend - Layered Architecture
Detailed in **[coding_standards_python.md](./coding_standards_python.md) Section 4**

- Routes → HTTP endpoints only
- Controllers → Feature coordination
- Services → Business logic
- Repository → Database operations
- Schemas → Request/response validation

### Frontend - Feature-Sliced Design
Detailed in **[coding_standards_react.md](./coding_standards_react.md) Section 2**

- Self-contained feature modules
- Co-located related files
- Public API exports via index.ts
- No cross-feature dependencies

## Code Generation Guidelines

**CRITICAL:** All generated code must follow the patterns and standards defined in:
- **[coding_standards_python.md](./coding_standards_python.md)** - Complete backend implementation patterns
- **[coding_standards_react.md](./coding_standards_react.md)** - Complete frontend implementation patterns

Key reminders:
- Database models: BigInteger IDs, func.sysutcdatetime() timestamps (Python Section 2)
- Schemas: Base/Create/Update/Response variants (Python Section 3)
- Repository: Standard CRUD with eager loading (Python Section 4.4)
- TypeScript: Proper interface naming and typing (React Section 5)
- API clients: CRUD methods with AbortSignal (React Section 7)
- Hooks: Return objects with data/loading/error (React Section 6)

### Variable Replacements

When generating files, replace these placeholders:
- `{{PROJECT_NAME}}` - User's project name
- `{{PROJECT_DESCRIPTION}}` - Brief description
- `{{FEATURE_NAME}}` - Current feature being generated (lowercase)
- `{{FeatureName}}` - PascalCase version for classes/components
- `{{CURRENT_DATE}}` - Today's date

## Setup Instructions for User

After generation, provide setup instructions for both backend and frontend.

**Backend Setup:**
```bash
cd [project-name]/backend
python -m venv venv
venv\Scripts\activate        # Windows | source venv/bin/activate (Linux/Mac)
pip install -r requirements.txt
cp .env.example .env         # Edit with DATABASE_URL and SECRET_KEY
alembic upgrade head         # If using ORM
python -m uvicorn main:app --reload
```

**Frontend Setup:**
```bash
cd [project-name]/frontend
npm install
cp .env.example .env         # Edit with VITE_API_BASE_URL
npm run dev
```

**Connection pooling** is auto-configured per [coding_standards_python.md](./coding_standards_python.md) Section 10.

## Important Notes

- **DO NOT create examples** - Generate only what the user requests
- **Follow coding standards strictly** - Reference coding_standards_python.md and coding_standards_react.md
- **Generate working code** - Not just folder structures
- **Be feature-specific** - Complete implementation for requested features
- **Implement proper patterns** - All patterns detailed in coding standards documents
- **`.claude/` folder** - Contains Claude AI skills and their documentation

## References

When generating code, constantly refer to:
- **[coding_standards_python.md](./coding_standards_python.md)** - For all backend code patterns and best practices
- **[coding_standards_react.md](./coding_standards_react.md)** - For all frontend code patterns and best practices
