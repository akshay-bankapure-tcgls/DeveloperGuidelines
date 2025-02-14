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
- [Python Cheatsheet](https://gto76.github.io/python-cheatsheet/)
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
