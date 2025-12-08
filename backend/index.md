---
layout: doc
title: Backend Development
---

# 🔧 Backend Development

Tài liệu về phát triển server-side - APIs, databases, authentication và architecture.

## 📚 Overview

Backend development xử lý logic nghiệp vụ, database, authentication và APIs. Section này bao gồm:

- **Node.js**: JavaScript runtime cho server-side
- **Python**: Django, FastAPI cho web APIs
- **Databases**: SQL (PostgreSQL) và NoSQL (MongoDB, Redis)
- **APIs**: REST và GraphQL design patterns

---

## 🗂️ Content Structure

### 🟢 Node.js

JavaScript/TypeScript cho backend development.

**Topics:**
- [Express Basics](/backend/nodejs/express-basics) - Web framework phổ biến nhất
- [Middleware Pattern](/backend/nodejs/middleware) - Request/response processing
- [Authentication](/backend/nodejs/authentication) - JWT, sessions, OAuth
- [Error Handling](/backend/nodejs/error-handling) - Centralized error management

**Key Concepts:**
- HTTP methods (GET, POST, PUT, DELETE)
- Request/Response cycle
- Routing & controllers
- Middleware chain
- Async/await patterns
- Environment variables
- File uploads
- Validation

**Popular Frameworks:**
- Express.js - Minimal, flexible
- NestJS - TypeScript, enterprise-ready
- Fastify - High performance
- Koa - Modern, lightweight

---

### 🐍 Python

Python frameworks cho web development.

**Topics:**
- [FastAPI Guide](/backend/python/fastapi-guide) - Modern, fast web framework
- [Django Basics](/backend/python/django-basics) - Full-featured framework

**Key Concepts:**
- ASGI/WSGI servers
- Type hints (Python 3.10+)
- Pydantic models
- Async/await in Python
- ORM (Django ORM, SQLAlchemy)

**Use Cases:**
- FastAPI: APIs, microservices, ML model serving
- Django: Full-stack apps, admin panels, CMS

---

### 🗄️ Databases

SQL và NoSQL database management.

**Topics:**
- [PostgreSQL Tips](/backend/databases/postgresql-tips) - Relational database
- [MongoDB Queries](/backend/databases/mongodb-queries) - Document database
- [Redis Caching](/backend/databases/redis-caching) - In-memory cache/database
- [Prisma ORM](/backend/databases/prisma-orm) - Next-gen TypeScript ORM

**Key Concepts:**

**SQL (PostgreSQL):**
- Tables, relationships (1:1, 1:N, N:N)
- Indexes & query optimization
- Transactions (ACID)
- Joins (INNER, LEFT, RIGHT)
- Migrations

**NoSQL (MongoDB):**
- Collections & documents
- Schema flexibility
- Embedded vs Referenced data
- Aggregation pipeline
- Indexing strategies

**Caching (Redis):**
- Key-value store
- TTL (Time To Live)
- Pub/Sub patterns
- Session storage

---

### 🌐 APIs

REST và GraphQL API design.

**Topics:**
- [REST API Design](/backend/apis/rest-api-design) - RESTful principles
- [GraphQL Basics](/backend/apis/graphql-basics) - Query language for APIs

**Key Concepts:**

**REST:**
- HTTP methods semantics
- Status codes (200, 201, 400, 404, 500)
- Resource naming conventions
- Pagination & filtering
- Versioning (v1, v2)
- HATEOAS

**GraphQL:**
- Schema definition
- Queries, Mutations, Subscriptions
- Resolvers
- DataLoader (N+1 problem)
- Apollo Server

---

## 🎯 Learning Path

### 👶 Beginner (0-2 tháng)

```markdown
1. Node.js Fundamentals
   - JavaScript runtime
   - NPM packages
   - File system operations
   - Event loop

2. Express Basics
   - Routing
   - Middleware
   - Request/Response
   - Simple CRUD API

3. Database Basics
   - SQL fundamentals
   - PostgreSQL setup
   - Basic queries
```

**Goal:** Build simple REST API với Express + PostgreSQL

---

### 💪 Intermediate (2-4 tháng)

```markdown
1. Express Advanced
   - Authentication (JWT)
   - Error handling
   - Validation (Zod, Joi)
   - File uploads

2. Database Design
   - Table relationships
   - Indexes
   - Migrations (Prisma)
   - Query optimization

3. API Design
   - REST best practices
   - API documentation (Swagger)
   - Rate limiting
   - CORS

4. Testing
   - Unit tests
   - Integration tests
   - API testing (Supertest)
```

**Goal:** Production-ready REST API với auth, validation, testing

---

### 🏆 Advanced (4+ tháng)

```markdown
1. Architecture
   - MVC pattern
   - Clean architecture
   - Microservices basics
   - Event-driven architecture

2. Performance
   - Caching strategies (Redis)
   - Database optimization
   - Load balancing
   - Horizontal scaling

3. Security
   - OWASP Top 10
   - Input sanitization
   - Rate limiting
   - Security headers

4. GraphQL
   - Schema design
   - Resolvers
   - DataLoader
   - Subscriptions

5. DevOps
   - Docker containerization
   - CI/CD pipelines
   - Monitoring & logging
```

**Goal:** Senior backend engineer với microservices knowledge

---

## 🚀 Quick Start

### Node.js + Express Setup

```bash
# Initialize project
mkdir my-api && cd my-api
npm init -y

# Install dependencies
npm install express dotenv
npm install -D nodemon typescript @types/node @types/express

# Create basic server
touch index.js
```

**index.js:**
```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.get('/api/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date() });
});

app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
```

```bash
# Run server
node index.js
```

---

### FastAPI Setup (Python)

```bash
# Install FastAPI
pip install fastapi uvicorn

# Create main.py
touch main.py
```

**main.py:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello World"}

@app.get("/api/health")
def health_check():
    return {"status": "OK"}
```

```bash
# Run server
uvicorn main:app --reload
```

---

### PostgreSQL + Prisma Setup

```bash
# Install Prisma
npm install prisma @prisma/client
npx prisma init

# Edit prisma/schema.prisma
```

**schema.prisma:**
```prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
}
```

```bash
# Migrate database
npx prisma migrate dev --name init

# Generate Prisma Client
npx prisma generate
```

---

## 🛠️ Essential Tools

### Development
- **Postman** / **Insomnia** - API testing
- **TablePlus** / **DBeaver** - Database GUI
- **Docker** - Containerization
- **Nodemon** - Auto-restart server

### Monitoring & Debugging
- **Morgan** - HTTP request logger
- **Winston** / **Pino** - Structured logging
- **PM2** - Process manager for Node.js

### Testing
- **Jest** / **Vitest** - Unit testing
- **Supertest** - HTTP assertions
- **MSW** - Mock Service Worker

---

## 📖 Recommended Topics

### For Node.js Developers

1. **Start here:** [Express Basics](/backend/nodejs/express-basics)
2. **Security:** [Authentication](/backend/nodejs/authentication)
3. **Patterns:** [Middleware Pattern](/backend/nodejs/middleware)
4. **Errors:** [Error Handling](/backend/nodejs/error-handling)

### For Database

1. **SQL:** [PostgreSQL Tips](/backend/databases/postgresql-tips)
2. **NoSQL:** [MongoDB Queries](/backend/databases/mongodb-queries)
3. **ORM:** [Prisma ORM](/backend/databases/prisma-orm)
4. **Cache:** [Redis Caching](/backend/databases/redis-caching)

### For APIs

1. **REST:** [REST API Design](/backend/apis/rest-api-design)
2. **GraphQL:** [GraphQL Basics](/backend/apis/graphql-basics)

---

## 💡 Best Practices

### Error Handling

```javascript
// ✅ Good - Centralized error handling
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
  }
}

app.use((err, req, res, next) => {
  const { statusCode = 500, message } = err;
  res.status(statusCode).json({
    status: 'error',
    statusCode,
    message
  });
});

// ❌ Bad - Error handling trong mỗi route
app.get('/user/:id', async (req, res) => {
  try {
    // logic
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

### Environment Variables

```javascript
// ✅ Good - Use .env file
// .env
DATABASE_URL=postgresql://user:pass@localhost:5432/db
JWT_SECRET=your-secret-key
PORT=3000

// Load với dotenv
require('dotenv').config();
const port = process.env.PORT || 3000;

// ❌ Bad - Hard-coded secrets
const JWT_SECRET = "my-secret-123"; // NEVER do this!
```

### Database Queries

```javascript
// ✅ Good - Use parameterized queries (prevent SQL injection)
const user = await db.query(
  'SELECT * FROM users WHERE email = $1',
  [email]
);

// ❌ Bad - String concatenation
const user = await db.query(
  `SELECT * FROM users WHERE email = '${email}'`
); // SQL injection vulnerable!
```

### Validation

```javascript
// ✅ Good - Validate input
const { z } = require('zod');

const userSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
  name: z.string().min(2)
});

app.post('/api/users', async (req, res) => {
  const result = userSchema.safeParse(req.body);
  if (!result.success) {
    return res.status(400).json({ errors: result.error.errors });
  }
  // Process valid data
});

// ❌ Bad - No validation
app.post('/api/users', async (req, res) => {
  const { email, password } = req.body;
  // Directly use without validation
});
```

---

## 🔗 External Resources

### Documentation
- [Node.js Docs](https://nodejs.org/docs/)
- [Express.js Guide](https://expressjs.com/)
- [FastAPI Docs](https://fastapi.tiangolo.com/)
- [Prisma Docs](https://www.prisma.io/docs)

### Learning
- [Backend Roadmap](https://roadmap.sh/backend)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)

---

## 📊 Backend Stack Example

```
Full Backend Stack:
├── Runtime: Node.js (v20+)
├── Framework: Express.js / NestJS / FastAPI
├── Database: PostgreSQL + Redis
├── ORM: Prisma
├── Auth: JWT + bcrypt
├── Validation: Zod / Joi
├── Testing: Jest + Supertest
├── Docs: Swagger / OpenAPI
├── Logging: Winston / Pino
└── Deploy: Docker + CI/CD
```

---

## 🎯 Next Steps

1. **New to backend?** → Start với [Express Basics](/backend/nodejs/express-basics)
2. **Know Express?** → Learn [Authentication](/backend/nodejs/authentication)
3. **Need database?** → [PostgreSQL](/backend/databases/postgresql-tips) + [Prisma](/backend/databases/prisma-orm)
4. **Build APIs?** → [REST API Design](/backend/apis/rest-api-design)
5. **Advanced?** → [GraphQL](/backend/apis/graphql-basics) + Microservices

---

**Happy building! 🚀**

[← Frontend](/frontend/) | [DevOps →](/devops/)