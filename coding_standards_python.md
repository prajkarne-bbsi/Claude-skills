# Backend Coding Standards - FastAPI + SQLAlchemy + Azure SQL Server

**Last Updated:** December 19, 2025

**Technology Stack:**
- **Backend Framework:** FastAPI (Python)
- **ORM:** SQLAlchemy
- **Database:** Azure SQL Server

---

## 1. Project Structure

### 1.1 Database Configuration

**MUST use SQLAlchemy ORM:**
- Use `app/models/` for SQLAlchemy ORM models
- Use `alembic/` for database migrations
- Configure connection pooling in `app/core/database.py`
- Use Azure SQL Server as the database

### 1.2 Connection Pooling for Azure SQL Server

**MUST configure connection pooling in `app/core/database.py`:**

```python
from sqlalchemy import create_engine
from sqlalchemy.engine import URL

# Azure SQL Server connection string
connection_url = URL.create(
    "mssql+pyodbc",
    username=settings.DB_USER,
    password=settings.DB_PASSWORD,
    host=settings.DB_HOST,
    port=settings.DB_PORT,
    database=settings.DB_NAME,
    query={
        "driver": "ODBC Driver 18 for SQL Server",
        "TrustServerCertificate": "yes",
        "Encrypt": "yes"
    }
)

engine = create_engine(
    connection_url,
    pool_size=10,           # Number of connections to keep open
    max_overflow=20,        # Additional connections in high demand
    pool_timeout=30,        # Seconds to wait for connection
    pool_recycle=3600,      # Recycle connections after 1 hour
    pool_pre_ping=True      # Verify connections before using
)
```

**Azure SQL Server Specific Settings:**
- Use `pyodbc` driver with ODBC Driver 18 for SQL Server
- Enable encryption and TrustServerCertificate for Azure
- Configure connection timeout appropriately
- Use connection pooling to handle concurrent requests

### 1.3 Key Architectural Principles

**File Organization Rules:**
- One file, one responsibility
- Constants/Exceptions/Middleware → `app/` level
- Utils → Pure helper functions only
- Services (app/services/) → Reusable business logic across all features
- Services (api/v1/[feature]/services.py) → Feature-specific reusable logic

**Layer Responsibilities:**
- **routes.py**: HTTP endpoints only
- **controllers.py**: Feature-specific business logic coordination
- **services.py** (in feature folder): Feature-specific reusable logic
- **app/services/**: Cross-feature reusable business logic
- **repository.py**: Database operations only
- **schemas.py**: Pydantic request/response models

---

## 2. Database Models

### 2.1 Column Standards for Azure SQL Server
- Use `mapped_column()` with type hints (SQLAlchemy 2.0 syntax)
- IDs: Always `BigInteger` (supports 9+ quintillion records)
- Timestamps: Use `func.sysutcdatetime()` for Azure SQL Server (UTC timezone)
- Booleans: `expression.true()` / `expression.false()`
- Foreign keys: Always include `ondelete='CASCADE'`
- Use appropriate SQL Server data types (NVARCHAR for Unicode text)

### 2.2 Schema Management
- Use environment variables for schema in `__table_args__`
- Different schemas for dev/qa/prod

### 2.3 Alembic Migrations for Azure SQL Server
```bash
# Create new migration
alembic revision --autogenerate -m "Description"

# Apply migrations
alembic upgrade head

# Rollback last migration
alembic downgrade -1
```

**Azure SQL Server Considerations:**
- Alembic may require manual adjustments for Azure SQL specific features
- Test migrations in development environment first
- Use schema parameter in `__table_args__` for multi-schema deployments
- Be aware of Azure SQL reserved keywords and escape them properly

### 2.4 Data Seeding
- Create scripts in `scripts/` folder
- Load from JSON files
- Check existence before inserting

---

## 3. API Design

### 3.1 HTTP Methods

| Operation | Method | Endpoint |
|-----------|--------|----------|
| Create | `POST` | `/resources` |
| Read (list) | `GET` | `/resources` |
| Read (one) | `GET` | `/resources/{id}` |
| Update | `PUT` | `/resources/{id}` |
| Delete | `DELETE` | `/resources/{id}` |

### 3.2 Parameters
- **Path parameters:** Resource identifiers (IDs)
- **Query parameters:** Filters, search, pagination

### 3.3 HTTP Status Codes
- `200 OK` - Successful GET/PUT/DELETE
- `201 Created` - Successful POST
- `400 Bad Request` - Invalid input
- `404 Not Found` - Resource doesn't exist
- `409 Conflict` - Duplicate/constraint violation
- `500 Server Error` - Unexpected errors

### 3.4 Response Format
```json
{
  "payload": {},
  "message": "Success",
  "is_error": false
}
```

### 3.5 Pagination
- Default: `page=1`, `page_size=10`, max `page_size=100`
- Return: `items`, `total`, `page`, `page_size`, `total_pages`
- Cursor-based for large datasets

---

## 4. Business Logic

### 4.1 Architecture
- **Controllers:** Business logic, callable from routes
- **Services:** Reusable business logic across features
- **Repository:** Database operations only
- Use dependency injection for DB sessions, auth, services

### 4.2 Async vs Sync
- **Async:** Database, external APIs, file I/O, network requests
- **Sync:** CPU computations, synchronous libraries
- Don't mix sync/async database operations
- Use `httpx` not `requests` for async

### 4.3 Background Tasks in FastAPI
- **Use FastAPI BackgroundTasks for:** Emails, logging, cleanup (<30s)
- **Use Celery with Redis for:** Long-running tasks, critical operations, status tracking
- FastAPI's built-in BackgroundTasks runs after response is sent
- For distributed task queues, integrate Celery with Redis as message broker

### 4.4 Transactions with SQLAlchemy
```python
try:
    db.add(record)
    db.flush()  # Get ID without commit
    db.commit()
except Exception:
    db.rollback()
    raise
```

**SQLAlchemy Transaction Best Practices:**
- Always use try-except-finally blocks
- Call `db.rollback()` in exception handlers
- Use `db.flush()` to get generated IDs before commit
- Commit only after all operations succeed
- Use `Session` context manager for automatic cleanup

---

## 5. Error Handling in FastAPI
- Create custom exception hierarchy (base → specific status codes)
- Use `@app.exception_handler()` decorator for global exception handlers
- Return consistent error responses with proper HTTP status codes
- Handle SQLAlchemy exceptions (IntegrityError, DataError, etc.)
- Use FastAPI's HTTPException for standard HTTP errors
- Log all exceptions with stack traces for debugging

## 6. Logging
- **Levels:** INFO (success), WARNING (recoverable), ERROR (failures), EXCEPTION (unexpected)
- Never log: passwords, tokens, credit cards, PII
- Use JSON format in production
- Include correlation IDs for request tracing
- Use audit trail while logging

## 7. Caching
- **Use Redis** for distributed cache
- Cache: user profiles, computations, external APIs, reference data
- Don't cache: rapidly changing data, large objects (>1MB)
- **Invalidation:** TTL for predictable changes, event-based for consistency

---

## 8. Rate Limiting
- **Per-user:** Track by user ID (e.g., 100 req/min)
- **Per-IP:** Track by IP (e.g., 20 req/min for anonymous)
- **Response:** Return 429, include `X-RateLimit-*` headers and `Retry-After`

---

## 9. Code Style

### 9.1 Imports (PEP-8)
1. Standard library
2. Third-party packages
3. Local application

### 9.2 Naming

| Type | Convention | Example |
|------|------------|---------|
| Classes | PascalCase | `ReviewWorkflow` |
| Functions | snake_case | `create_workflow()` |
| Variables | snake_case | `workflow_id` |
| Constants | UPPER_SNAKE_CASE | `MAX_PAGE_SIZE` |
| Private | `_prefix` | `_validate_name()` |

### 9.3 Type Hints
Always use type hints for parameters and return values

---

## 10. Performance Optimization

### 10.1 SQLAlchemy Query Optimization
- **Avoid N+1 queries:** Use `joinedload()` or `selectinload()` for eager loading
- **Select specific columns:** Use `with_only_columns()` instead of loading entire objects
- Add **indexes** on frequently queried columns in Azure SQL Server
- Use `lazy='selectin'` for relationships to reduce queries

### 10.2 Azure SQL Server Optimization
- **Connection pool:** Configure 10-20 connections with proper timeout/recycle settings
- Use **read replicas** for read-heavy operations (Azure SQL can have read replicas)
- Enable **query statistics** in Azure portal to identify slow queries
- Use **columnstore indexes** for analytical queries on large tables

### 10.3 FastAPI Performance
- **GZIP compression:** Enable for responses >1KB
- **Async endpoints:** Use async/await for I/O operations
- **Paginate** large datasets (default: 10-100 items per page)
- **Response caching:** Use Redis for frequently accessed data
- **Connection pooling:** Properly configured for concurrent requests

---

## 11. Security

### 11.1 Input Validation
- Validate all inputs with Pydantic schemas
- Sanitize: strip HTML/JS, validate emails, limit string lengths
- Never concatenate user input into SQL (use parameterized queries)

### 11.2 Authentication
- **JWT:** Strong keys (32+ chars), 15-60min expiration, refresh tokens
- **Passwords:** bcrypt/argon2, enforce strength, account lockout
- Check permissions on every protected endpoint (RBAC)

### 11.3 Configuration
- **CORS:** Exact origins (no wildcards in production)
- **Security headers:** `X-Content-Type-Options`, `X-Frame-Options`, `HSTS`
- Use HTTPS only
- Environment variables for secrets

### 11.4 Never Log
Passwords, API keys, JWT tokens, credit cards, SSN, PHI

---

## 12. Monitoring

### 12.1 Health Checks
- `GET /health` - Basic liveness (<100ms)
- `GET /health/detailed` - Check DB, cache, external services

### 12.2 Metrics
- Request count, response times (p50, p95, p99), error rates
- Database query times, connection pool stats
- Cache hit/miss ratio

### 12.3 Error Tracking
- Integrate Sentry/New Relic/DataDog
- Alert on error rate spikes
- Include request context, user ID, stack traces

---

## Quick Reference Checklist

### Database (SQLAlchemy + Azure SQL Server)
- [ ] Use SQLAlchemy with `mapped_column()` and type hints
- [ ] `BigInteger` for all ID columns
- [ ] Environment-based schema configuration
- [ ] Use `func.sysutcdatetime()` for timestamps (Azure SQL Server)
- [ ] Foreign keys with CASCADE delete
- [ ] Alembic for all schema changes and migrations
- [ ] Automated seeding scripts for initial data
- [ ] Eager loading (`joinedload()`, `selectinload()`) to avoid N+1 queries
- [ ] Database indexes on frequently queried columns
- [ ] Azure SQL Server connection with pyodbc driver
- [ ] Connection pooling configured (pool_size, max_overflow, pool_recycle)

### API
- [ ] Correct HTTP methods (POST/GET/PUT/DELETE)
- [ ] IDs in path, filters in query
- [ ] Proper HTTP status codes
- [ ] Consistent response format
- [ ] Pagination implemented (offset or cursor-based)
- [ ] API documentation with summaries and examples
- [ ] Rate limiting configured
- [ ] CORS properly configured

### Architecture (FastAPI)
- [ ] Dependency injection for database sessions (FastAPI Depends)
- [ ] Dependency injection for authentication (OAuth2PasswordBearer)
- [ ] Async/await used for I/O operations
- [ ] FastAPI BackgroundTasks for non-blocking operations
- [ ] Service layer for reusable business logic
- [ ] Controller pattern for feature-specific business logic
- [ ] SQLAlchemy transaction management with rollback
- [ ] Layered architecture (routes → controllers → services → repository)

### Performance
- [ ] Connection pooling configured
- [ ] GZIP compression enabled
- [ ] Response pagination implemented
- [ ] Caching strategy for frequently accessed data
- [ ] Cache invalidation strategy defined

### Security
- [ ] Input validation with Pydantic schemas
- [ ] SQL injection prevention (parameterized queries)
- [ ] Strong JWT secret keys
- [ ] Password hashing with bcrypt/argon2
- [ ] RBAC implemented for authorization
- [ ] Security headers configured
- [ ] Sensitive data never logged
- [ ] HTTPS enforced

### Monitoring & Observability
- [ ] Health check endpoints implemented
- [ ] Structured logging with JSON format
- [ ] Correlation IDs for request tracing
- [ ] Error tracking integrated (Sentry/New Relic)
- [ ] Application metrics collected
- [ ] Alerts configured for error spikes

### Code Quality
- [ ] Custom exception hierarchy
- [ ] Comprehensive logging
- [ ] PEP-8 import organization
- [ ] Type hints everywhere
- [ ] Naming conventions followed

---

**Last Updated:** December 19, 2025  
**Technology Stack:** FastAPI + SQLAlchemy + Azure SQL Server