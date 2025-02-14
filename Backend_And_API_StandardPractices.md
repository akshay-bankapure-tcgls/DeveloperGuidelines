# Backend & API Standard Practices
This document serves as a **comprehensive reference** for backend and API development, covering setup, security, best practices, and common FAQs.

## Table of Contents
1. [Environment Variables & Secrets Management](#environment-variables--secrets-management)
2. [API Design Best Practices](#api-design-best-practices)
3. [Centralized Constants & Configurations](#centralized-constants--configurations)
4. [Backend Security Best Practices](#backend-security-best-practices)
5. [Database Best Practices](#database-best-practices)
6. [Logging & Monitoring](#logging--monitoring)
7. [Performance Optimization](#performance-optimization)
8. [Error Handling & Debugging](#error-handling--debugging)
9. [Testing & CI/CD](#testing--cicd)
10. [Setting Up a Backend Project](#setting-up-a-backend-project)
11. [FAQs](#faqs)
12. [Best Practices Summary](#best-practices-summary)
13. [Further Learning & Resources](#further-learning--resources)

---

## Environment Variables & Secrets Management

### Why Use Environment Variables?
- Keeps sensitive information like API keys, database credentials, and third-party tokens **out of the codebase**.
- Prevents accidental commits of sensitive data.
- Allows different configurations for development, testing, and production environments.

### How to Use `.env` Files
#### Creating an `.env` File
1. Add a `.env` file at the root of your project.
2. Define variables in the format:
   ```env
   DATABASE_URL=postgres://user:password@localhost:5432/dbname
   API_KEY=your-api-key-here
   SECRET_KEY=super-secret-key
   ```
3. Ensure `.env` is added to `.gitignore` to prevent committing it to the repository.

#### Loading Environment Variables
##### **Python (Using `dotenv`)**
```python
from dotenv import load_dotenv
import os

load_dotenv()
DATABASE_URL = os.getenv("DATABASE_URL")
```
##### **JavaScript / TypeScript (Using `dotenv`)**
```javascript
require('dotenv').config();
const databaseUrl = process.env.DATABASE_URL;
```

---

## API Design Best Practices

### REST API Guidelines
- Use **RESTful principles** for resource management.
- **Version APIs** (`/api/v1/resource` instead of `/api/resource`).
- Follow **consistent naming conventions** (`/users` for collections, `/users/:id` for single resources).
- Implement **pagination** (`/users?page=2&limit=10`).
- Use **query parameters** for filtering and sorting (`?sort=asc`).

#### Example REST API Response
```json
{
  "status": "success",
  "data": { "id": 1, "name": "John Doe" },
  "message": "User retrieved successfully"
}
```

### GraphQL Guidelines
- Use **schema-first development**.
- Avoid **over-fetching** or **under-fetching** data.
- Implement **query complexity analysis** to prevent abuse.

#### Example GraphQL Query
```graphql
query GetUser {
  user(id: 1) {
    id
    name
    email
  }
}
```

---

## Centralized Constants & Configurations

### Why Use a Centralized Constants File?
- Avoids hardcoded values in multiple files.
- Improves maintainability and consistency.

#### Example Config File
##### **Python**
```python
# constants.py
DATABASE_URL = "postgres://user:password@localhost:5432/dbname"
MAX_RETRIES = 3
TIMEOUT = 5000
```
##### **JavaScript / TypeScript**
```javascript
// constants.js
export const API_BASE_URL = "https://api.example.com";
export const MAX_RETRIES = 3;
export const TIMEOUT = 5000;
```

---

## Backend Security Best Practices

### Secure API Endpoints
- Use **JWT/OAuth authentication**.
- Implement **role-based access control (RBAC)**.
- Validate **all incoming request payloads**.

#### Example JWT Authentication Middleware (Node.js)
```javascript
const jwt = require("jsonwebtoken");

function authenticateToken(req, res, next) {
  const token = req.header("Authorization");
  if (!token) return res.status(401).send("Access Denied");

  jwt.verify(token, process.env.SECRET_KEY, (err, user) => {
    if (err) return res.status(403).send("Invalid Token");
    req.user = user;
    next();
  });
}
```

---

## Setting Up a Backend Project

### Recommended Project Structure
```
backend-project/
│── src/
│   │── controllers/
│   │── models/
│   │── routes/
│   │── middleware/
│── config/
│── .env
│── server.js
│── package.json
```

### Setting Up a FastAPI Backend (Python)
```bash
pip install fastapi uvicorn
```
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, World!"}
```

### Setting Up an Express Backend (Node.js)
```bash
npm init -y
npm install express dotenv cors
```
```javascript
const express = require("express");
const app = express();

app.get("/", (req, res) => {
  res.json({ message: "Hello, World!" });
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

---

## FAQs

### 1. Why should I use `.env` files?
Using `.env` files keeps sensitive credentials out of the source code, improving security.

### 2. What’s the difference between `PUT` and `PATCH`?
- `PUT` replaces an entire resource.
- `PATCH` updates only specified fields.

### 3. How can I secure an API?
- Use authentication mechanisms (OAuth, JWT).
- Validate all inputs.
- Encrypt sensitive data.

### 4. How do I debug API errors?
- Use logging tools (`winston`, `loguru`).
- Check API responses for status codes.

---

## Further Learning & Resources
- [REST API Best Practices](https://restfulapi.net/)
- [GraphQL Best Practices](https://graphql.org/learn/best-practices/)
- [OWASP API Security Guide](https://owasp.org/www-project-api-security/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Express.js Guide](https://expressjs.com/)

---
