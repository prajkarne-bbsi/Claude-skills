# React + TypeScript Coding Standards

**Version:** 2.0  
**Purpose:** Coding standards for React + TypeScript applications with Vite  
**Last Updated:** December 19, 2025

**Technology Stack:**
- **Frontend Framework:** React 18+
- **Language:** TypeScript
- **Build Tool:** Vite
- **HTTP Client:** Axios

---

## Table of Contents
1. [Project Structure](#1-project-structure)
2. [Naming Conventions](#2-naming-conventions)
3. [File Organization](#3-file-organization)
4. [Component Standards](#4-component-standards)
5. [TypeScript Usage](#5-typescript-usage)
6. [State Management](#6-state-management)
7. [API Integration](#7-api-integration)
8. [Performance Optimization](#8-performance-optimization)
9. [Error Handling](#9-error-handling)
10. [Testing Standards](#10-testing-standards)
11. [Code Quality](#11-code-quality)
12. [Git Workflow](#12-git-workflow)

---

## 1. Project Structure

### 1.1 Key Architectural Principles

**Feature-Sliced Design:**
- ✅ MUST use feature-based architecture in `src/features/`
- ✅ One feature = one self-contained folder
- ✅ Co-locate all related files within the feature
- ✅ Export public APIs through `index.ts`
- ✅ Keep shared code in root-level folders
- ❌ No cross-feature imports (use shared folders)
- ❌ No mixing of features in the same folder

**Folder Organization:**
- `/api/` - Global API configuration (axios, config, globalApi)
- `/assets/` - Static resources (images, icons, fonts)
- `/components/` - Shared/reusable components (layout, ui, common)
- `/contexts/` - Global React contexts (auth, theme, tenant)
- `/hooks/` - Global custom hooks (useDebounce, useLocalStorage)
- `/routes/` - Navigation configuration (index, ProtectedRoute)
- `/styles/` - Global styles (globals, variables, themes)
- `/types/` - Global TypeScript definitions (api, models, common)
- `/utils/` - Pure utility functions (formatters, validators, helpers)
- `/features/[feature-name]/` - Feature modules with api, components, hooks, pages, types

**Best Practices:**
- ✅ MUST separate shared components from feature components
- ✅ MUST use `assets/` for images, icons, fonts
- ✅ MUST use `styles/` for global CSS files
- ✅ MUST use `routes/` for routing configuration
- ✅ MUST NOT commit environment files with secrets
- ❌ DO NOT use flat file structures for large applications

---

## 2. Naming Conventions

### 2.1 File Naming

**MUST follow these conventions:**

| File Type | Convention | Examples |
|-----------|------------|----------|
| Components | PascalCase.tsx | `UserProfile.tsx`, `Button.tsx` |
| Pages | PascalCase.tsx | `HomePage.tsx`, `LoginPage.tsx` |
| Hooks | camelCase.ts | `useAuth.ts`, `useDebounce.ts` |
| Utils | camelCase.ts | `formatDate.ts`, `validateEmail.ts` |
| Types | camelCase.ts | `user.ts`, `api.ts` |
| API Services | camelCase.ts | `userApi.ts`, `authApi.ts` |
| Constants | camelCase.ts | `config.ts`, `routes.ts` |
| Contexts | PascalCase.tsx | `AuthContext.tsx` |

### 2.2 Variable and Function Naming

**MUST use:**
- `camelCase` for variables and functions
- `PascalCase` for components and classes
- `SCREAMING_SNAKE_CASE` for constants
- Descriptive names (no abbreviations unless common)

**Boolean Prefixes:**
- `is` for state (isLoading, isVisible, isEnabled)
- `has` for possession (hasPermission, hasError)
- `should` for conditions (shouldUpdate, shouldRender)
- `can` for ability (canEdit, canDelete)

### 2.3 TypeScript Naming

**MUST use:**
- `PascalCase` for interfaces and types
- Suffix `Props` for component props
- Suffix `Response` for API responses
- Suffix `Request` for API request payloads
- Prefix `I` for interfaces (optional, based on team preference)

**MUST NOT:**
- Use lowercase for type names
- Omit Props suffix for component props
- Use generic names like `Data` or `Info`

---

## 3. File Organization

### 3.1 Import Order

**MUST follow this sequence:**

1. React and framework imports
2. External library imports (node_modules)
3. Internal absolute imports (using @ path alias)
4. Relative imports from same feature/folder
5. Type-only imports (at the end)

**Additional Rules:**
- Group related imports together
- Separate groups with blank lines
- Alphabetize within each group
- Place type imports at end with `import type` syntax

### 3.2 Component File Structure

**MUST organize file in this order:**

1. **Import statements** (following import order standards)
2. **Type/interface definitions** for props and local types
3. **Constants** (if any, component-specific)
4. **Component definition** with internal organization:
   - State declarations (useState)
   - Context and custom hook calls
   - useEffect hooks
   - Event handler functions
   - Helper/utility functions
   - Render logic (return statement)
5. **Export statement** (default export at bottom)

**Additional Requirements:**
- One component per file
- Component name matches filename
- Destructure props in function signature

### 3.3 File Size Limits

**SHOULD follow these guidelines:**
- Components: **Max 300 lines**
- Hooks: **Max 150 lines**
- Utils: **Max 100 lines** per function
- API files: **Max 200 lines**

**Standards:**
- ✅ MUST split large files into smaller modules
- ✅ SHOULD extract complex logic into separate functions
- ✅ SHOULD use composition for large components
- ❌ DO NOT create "god files" with multiple responsibilities

---

## 4. Component Standards

### 4.1 Component Types

**Page Components:** (`features/[feature]/pages/`)
- Top-level route components
- Orchestrate feature logic and data fetching via hooks
- Wrap in layout components

**Feature Components:** (`features/[feature]/components/`)
- Feature-specific UI (lists, forms, cards, modals)
- Receive data via props, no direct API calls

**Shared Components:** (`components/`)
- `layout/` - MainLayout, Header, Sidebar, Footer
- `ui/` - Base UI library components (Button, Input, etc.)
- `common/` - Custom reusable components

### 4.2 Component File Structure

**Organization:**
1. Import statements (follow section 3.1 order)
2. TypeScript interface for props
3. Component definition (arrow function)
4. Default export at bottom

**Component Internal Order:**
- Props destructuring in signature
- State hooks (useState)
- Custom hooks and context
- useEffect hooks
- Event handlers
- Return JSX

### 4.3 Component Props

**Rules:**
- Define props interface above component
- Mark optional props with `?`
- Destructure props in signature
- Type all callbacks fully
- Set default values in destructuring

---

## 5. TypeScript Usage

### 5.1 Type Organization

**Global Types:** (`types/`) - api/, models/, common.ts
**Feature Types:** (`features/[feature]/types/`) - Feature-specific interfaces and enums

### 5.2 Type Conventions

- Use `interface` for object shapes
- Use `type` for unions and intersections
- Use literal types for enums (`'active' | 'inactive'`)
- Suffix response types with `Response`
- Suffix props types with `Props`
- snake_case for backend fields, camelCase for frontend

### 5.3 API Response Types

**Standard Response:** `ApiResponse<T>` with `payload`, `message`, `is_error`
**Paginated Response:** `PaginatedResponse<T>` with `items`, `total`, `page`, `page_size`, `total_pages`

---

## 6. State Management

### 6.1 Custom Hooks

**Location:** `features/[feature]/hooks/use[Feature].ts`

**Responsibilities:**
- Data fetching with loading/error states
- AbortController for request cancellation
- CRUD operations and pagination
- Cache refresh logic

**Return Pattern:** `{ data, isLoading, error, createFn, updateFn, deleteFn, refresh, page, totalPages, setPage }`

### 6.2 Form State

**State:** Individual fields, errors object, loading state
**Validation:** Real-time validation in useEffect, disable submit when invalid

### 6.3 Global State (Context)

**Location:** `contexts/[Context]Context.tsx`
**Common:** AuthContext, TenantContext, ThemeContext
**Pattern:** Context → Provider → Custom hook with error check

---

## 7. API Integration with Axios

### 7.1 Axios Setup (`api/axiosInstance.ts`)

**MUST configure Axios with:**
- Base URL from Vite environment variables (`import.meta.env.VITE_API_BASE_URL`)
- Request interceptor: Inject JWT auth token from AuthContext
- Response interceptor: Handle errors, token refresh, logout on 401
- Global loader management (optional loading state)
- Validation error parsing (422 status from FastAPI)

**Example Configuration:**
```typescript
import axios from 'axios';

const axiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000',
  timeout: 30000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor - add auth token
axiosInstance.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('access_token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Response interceptor - handle errors
axiosInstance.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Handle unauthorized - logout user
      localStorage.removeItem('access_token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default axiosInstance;
```

### 7.2 Environment Config (`api/config.ts`)

**MUST use Vite environment variables:**
- Prefix all env vars with `VITE_` to expose to client
- Load from `.env`, `.env.local`, `.env.production`
- Type-safe access with fallbacks
- Never access `import.meta.env` directly in components

**Example:**
```typescript
export const API_CONFIG = {
  BASE_URL: import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000',
  TIMEOUT: 30000,
  API_VERSION: 'v1',
};
```

### 7.3 API Service Pattern

**File Location:** `features/[feature]/api/[feature]Api.ts`

**Standard Methods:**
- `get[Resources](signal?: AbortSignal)` - GET list
- `get[Resource]ById(id: number, signal?: AbortSignal)` - GET single
- `create[Resource](data: CreateDTO, signal?: AbortSignal)` - POST
- `update[Resource](id: number, data: UpdateDTO, signal?: AbortSignal)` - PUT
- `delete[Resource](id: number, signal?: AbortSignal)` - DELETE

**Requirements:**
- Use correct HTTP verbs (GET/POST/PUT/DELETE)
- IDs in URL path, not body
- AbortSignal support for request cancellation
- Typed parameters and return values
- Use axiosInstance, not raw axios

---

## 8. Performance Optimization

### 8.1 Search Optimization
- Debounce search inputs (500ms)
- Use useCallback for debounced functions
- Maintain local state for instant UI feedback

### 8.2 Request Cancellation
- Use AbortController ref in hooks
- Cancel previous request before new one
- Cleanup on unmount
- Ignore AbortError silently

### 8.3 List Rendering Keys
- Use stable, unique IDs (never array index)
- Generate temp IDs for unsaved items: `temp-{timestamp}-{random}`

---

## 9. Error Handling

### 9.1 Error Boundaries
- Location: `components/ErrorBoundary.tsx`
- Class component with getDerivedStateFromError
- Display user-friendly error UI with reload/retry
- Wrap page-level components

### 9.2 Toast Notifications
- Success toasts for mutations
- Error toasts with detailed messages
- Use `error instanceof Error` check

### 9.3 Form Validation
- Show errors below input fields
- Red border for invalid fields
- Real-time validation feedback

---

## 10. Testing Standards (Vitest + React Testing Library)

**Test Organization:**
- Co-locate with source files (`.test.tsx` or `.spec.tsx`)
- Unit tests for utilities/hooks
- Component tests for UI
- Integration tests for features
- E2E for critical flows

**Technology Stack:**
- **Test Runner:** Vitest (faster than Jest, works with Vite)
- **Component Testing:** React Testing Library
- **API Mocking:** MSW (Mock Service Worker)
- **E2E Testing:** Playwright or Cypress

**Vitest Configuration:**
```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
  },
});
```

---

## 11. Code Quality

**ESLint Rules:**
- `@typescript-eslint/recommended`
- `react-hooks/rules-of-hooks`
- `react-hooks/exhaustive-deps`
- Custom import order rules

**Code Review Checklist:**
- [ ] TypeScript strict mode, no `any` types
- [ ] All props typed, error/loading states handled
- [ ] Accessibility attributes added
- [ ] Console logs removed

---

## 12. Git Workflow

**Branch Naming:** `feature/`, `bugfix/`, `hotfix/`, `refactor/`
**Commit Format:** `<type>(<scope>): <subject>`
**Types:** feat, fix, refactor, style, test, docs

---

### Quick Reference Checklist

---

**Last Updated:** December 19, 2025  
**Technology Stack:** React + TypeScript + Vite + Axios  
**Review Cycle:** Quarterly