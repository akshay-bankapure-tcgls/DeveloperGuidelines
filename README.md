# DeveloperGuidelines
This document serves as a comprehensive reference for all level of developers to maintain standardized, up-to-date, and high-quality coding practices.

## Naming Conventions & Guidelines

### File Naming Conventions

#### Python
- **Modules:** `module_name.py`
- **Classes:** `PascalCase`
- **Functions & Methods:** `snake_case`
- **Constants:** `UPPER_CASE`
- **Private Variables:** `_single_leading_underscore`
- **Dunder Methods:** `__double_leading_underscore__`

#### JavaScript / TypeScript
- **Components:** `ComponentName.tsx`
- **Services:** `serviceName.ts`
- **Utilities:** `utilName.ts`
- **Styles:** `component-name.module.css`
- **Configurations:** `config.ts`
- **Constants:** `constants.ts`

---

## Python Development Guidelines

### General Guidelines
- Follow **PEP 8** for styling.
- Use **snake_case** for variables and functions.
- Use **PascalCase** for class names.
- Type hints **must be used** (`def func(x: int) -> str:`).
- Keep imports **structured** and **avoid circular dependencies**.
- Prefer list comprehensions over loops when possible.
- Use `enumerate()` instead of manually tracking indexes.
- Use `zip()` for parallel iterations.
- Write docstrings using **PEP 257**.
- Use context managers (`with open(...)`) for handling files.
- Use `logging` module instead of `print()` for debugging.
- Avoid using `global` variables.
- Avoid deep nesting; refactor code into functions/classes where necessary.
- Use virtual environments for dependency management (`venv`, `pipenv`, `poetry`).
- Follow `__main__` convention for script execution.
- Handle exceptions gracefully using `try-except`.

### Common Mistakes & How to Avoid Them
| Mistake | Solution |
|---------|----------|
| Using mutable default arguments | Use `None` and handle inside function |
| Not closing file handles | Use `with open()` context manager |
| Overwriting built-in functions | Avoid using names like `list`, `dict` |
| Ignoring exceptions | Always catch exceptions and log properly |
| Hardcoding configuration values | Use environment variables or config files |

### Python Cheatsheet
| Concept | Example |
|---------|---------|
| List Comprehensions | `[x**2 for x in range(10)]` |
| Dictionary Comprehensions | `{x: x**2 for x in range(10)}` |
| Lambda Functions | `add = lambda x, y: x + y` |
| Enumerate | `for i, val in enumerate(my_list):` |
| Zip | `for x, y in zip(list1, list2):` |
| Generators | `yield` instead of `return` |
| Context Managers | `with open('file.txt') as f:` |

### Learn More
- [PEP 8](https://peps.python.org/pep-0008/)
- [Python Cheatsheet](https://github.com/akshay-bankapure-tcgls/DeveloperGuidelines/blob/main/python-cheatsheet.md)
- [Python Best Practices](https://realpython.com/tutorials/best-practices/)
- [Common Python Mistakes](https://realpython.com/python-mistakes/)

---

## JavaScript Development Guidelines

### General Guidelines
- Prefer **ES6+ syntax**.
- Use **const** and **let**, avoid **var**.
- Follow **camelCase** for variables and functions.
- Use **PascalCase** for class names.
- Use **modules** to avoid polluting the global scope.
- Avoid using `==` (use `===` instead for strict equality).
- Use template literals instead of concatenation (`\`${var}\``).
- Use `async/await` instead of callbacks.
- Prefer **forEach/map/filter** over `for` loops where possible.
- Minimize direct DOM manipulations; use frameworks/libraries where needed.
- Avoid using `eval()`.
- Always handle promises correctly using `.catch()`.
- Keep JavaScript files modular to ensure maintainability.

### JavaScript Cheatsheet
| Concept | Example |
|---------|---------|
| Arrow Functions | `const add = (x, y) => x + y;` |
| Template Literals | `` `Hello, ${name}!` `` |
| Spread Operator | `const newArr = [...arr, 4, 5]` |
| Destructuring | `const {name, age} = obj;` |
| Async/Await | `await fetch(url)` |
| Ternary Operator | `isTrue ? 'Yes' : 'No'` |
| Optional Chaining | `user?.profile?.email` |

### Learn More
- [JavaScript.info](https://javascript.info/)
- [JavaScript Cheatsheet](https://htmlcheatsheet.com/js/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Common JavaScript Mistakes](https://rollbar.com/blog/top-10-javascript-errors/)

---

## TypeScript Development Guidelines

### General Guidelines
- Use **strict types**.
- Define **interfaces and types** instead of `any`.
- Prefer **readonly properties** when applicable.
- Always define function return types.
- Keep TypeScript **configurations strict** (`strict: true`).
- Use `as const` for fixed values to maintain strict type safety.
- Use `never` for unreachable code to ensure correctness.
- Keep your interfaces clean and modular.

### TypeScript Cheatsheet
| Concept | Example |
|---------|---------|
| Type Alias | `type Point = { x: number; y: number };` |
| Interface | `interface User { name: string; age: number; }` |
| Readonly | `readonly arr: number[]` |
| Generics | `function identity<T>(arg: T): T { return arg; }` |
| Enum | `enum Color { Red, Green, Blue }` |
| Optional Properties | `interface User { age?: number }` |
| Type Assertion | `const input = myVar as string;` |

### Learn More
- [TypeScript Docs](https://www.typescriptlang.org/docs/)
- [TypeScript Cheatsheet](https://typescript-cheatsheets.dev/)
- [Common TypeScript Mistakes](https://khalilstemmler.com/blogs/typescript/common-mistakes/)

---

## Coding Standards
- **Write reusable, modular code**.
- **Use linters and formatters** (`ESLint`, `Prettier`, `Black`, `Flake8`).
- **Use meaningful logs and error messages**.
- **Document APIs and public functions**.
- **Use dependency management properly** (`package.json`, `requirements.txt`).
- **Test code before pushing** (`unit tests, integration tests`).
- **Follow Git workflow best practices** (`rebase, merge, conflict resolution`).

---


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

