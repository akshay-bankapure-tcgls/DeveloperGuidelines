# API & Backend Development Guidelines

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
1. Install `python-dotenv`:
   ```bash
   pip install python-dotenv
   ```
2. Load variables in your script:
   ```python
   from dotenv import load_dotenv
   import os

   load_dotenv()

   DATABASE_URL = os.getenv("DATABASE_URL")
   ```

##### **JavaScript / TypeScript (Using `dotenv`)**
1. Install `dotenv`:
   ```bash
   npm install dotenv
   ```
2. Load variables in your script:
   ```javascript
   require('dotenv').config();

   const databaseUrl = process.env.DATABASE_URL;
   ```

### Never Hardcode API Keys in Code
- Use **environment variables** instead of hardcoding.
- Use **secrets management tools** like AWS Secrets Manager, Azure Key Vault, or HashiCorp Vault for production.
- **Example of what NOT to do:**
  ```javascript
  const API_KEY = "hardcoded-key-123456"; // ❌ Bad Practice
  ```
- **Use this instead:**
  ```javascript
  const API_KEY = process.env.API_KEY; // ✅ Best Practice
  ```

---

## API Design Best Practices

### General Guidelines
- Use **RESTful principles** or **GraphQL** based on use case.
- Always **version your APIs** (`/api/v1/resource` instead of `/api/resource`).
- Follow **consistent naming conventions** (use plural nouns for collections: `/users`, `/orders`).
- Use **HTTP methods correctly**:
  - `GET` → Retrieve data
  - `POST` → Create a new resource
  - `PUT` → Update an entire resource
  - `PATCH` → Update part of a resource
  - `DELETE` → Remove a resource
- Implement **pagination** for large data responses.
- Use **query parameters** for filtering, sorting, and searching (`?sort=asc&limit=10`).
- Return **proper HTTP status codes**:
  - `200 OK` → Success
  - `201 Created` → New resource created
  - `400 Bad Request` → Invalid client request
  - `401 Unauthorized` → Authentication required
  - `403 Forbidden` → Insufficient permissions
  - `404 Not Found` → Resource doesn’t exist
  - `500 Internal Server Error` → Unexpected server failure

### Response Structure
- Always return JSON responses.
- Use a **consistent response format**:
  ```json
  {
    "status": "success",
    "data": { ... },
    "message": "Operation successful"
  }
  ```
- For errors, provide meaningful messages:
  ```json
  {
    "status": "error",
    "error": "Invalid input data"
  }
  ```

---

## Centralized Constants and Configurations
### Why Use a Centralized Constants File?
- Avoids magic numbers and hardcoded values scattered throughout the codebase.
- Improves maintainability by keeping configurations in one place.
- Allows easy updates without modifying multiple files.

### Creating a Constants File
#### Python
```python
# constants.py
DATABASE_URL = "postgres://user:password@localhost:5432/dbname"
MAX_RETRIES = 3
TIMEOUT = 5000
```
Usage:
```python
from constants import DATABASE_URL, MAX_RETRIES
```

#### JavaScript / TypeScript
```javascript
// constants.js
export const API_BASE_URL = "https://api.example.com";
export const MAX_RETRIES = 3;
export const TIMEOUT = 5000;
```
Usage:
```javascript
import { API_BASE_URL, MAX_RETRIES } from "./constants";
```

### Where to Store Configuration Files
- **Environment-Specific Configurations:** Store them in a `config/` directory.
- **Do Not Store Secrets in Config Files:** Always use `.env` files.

#### Example Structure:
```
project-root/
│── config/
│   │── development.js
│   │── production.js
│── .env
│── src/
│── constants.js
```

---

## Backend Security Best Practices
### Secure API Endpoints
- Always use **authentication** (OAuth, JWT, API keys).
- Implement **role-based access control (RBAC)**.
- Validate **all incoming request payloads** to prevent injection attacks.
- Use **HTTPS** to encrypt data in transit.
- Implement **rate limiting** to prevent abuse.
- Log API requests but **never log sensitive data like passwords**.
- Regularly **rotate API keys and credentials**.

### Secure Database Access
- Always use **parameterized queries** to prevent SQL injection.
- Restrict **database permissions** (use least privilege principle).
- Use **strong encryption** for stored sensitive data (e.g., bcrypt for passwords).
- Regularly backup databases.

---

## Best Practices Summary
| Best Practice | Why? |
|--------------|------|
| Use `.env` files | Keeps secrets out of the codebase |
| Add `.env` to `.gitignore` | Prevents accidental commits of sensitive data |
| Use `dotenv` or `python-dotenv` | Loads environment variables easily |
| Centralize constants in a file | Improves maintainability and avoids magic numbers |
| Use config files per environment | Allows flexibility for dev, test, and production |
| Use secrets managers in production | Provides enhanced security |
| Secure API endpoints | Prevents unauthorized access |
| Encrypt sensitive data | Protects user and system information |
| Implement rate limiting | Mitigates abuse and brute force attacks |

---

## Learn More
- [Twelve-Factor App: Config](https://12factor.net/config)
- [RESTful API Best Practices](https://restfulapi.net/)
- [GraphQL Best Practices](https://graphql.org/learn/best-practices/)
- [OWASP API Security Best Practices](https://owasp.org/www-project-api-security/)
- [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/)
- [Azure Key Vault](https://azure.microsoft.com/en-us/products/key-vault/)
