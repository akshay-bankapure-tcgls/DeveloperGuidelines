# 🛠️ Engineering Standards - Python Backend (AWS Context)

## 📌 Overview
This document defines backend engineering standards for Python projects deployed on AWS. It ensures code is reliable, scalable, secure, and maintainable. Use this as the reference for development, code reviews, and onboarding.

---

## 🔀 Git Commit & Merge Standards
Follow these practices for clear, traceable history and stable collaboration.

### Branching Strategy
- Use feature branches: `feature/<ticket-or-feature-name>`
- Bug fixes: `bugfix/<issue-description>`
- Hotfixes: `hotfix/<critical-patch>`

### Commit Message Format
Follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) standard:
```
type(scope): short summary

body (optional)
```
Examples:
- `feat(auth): add JWT token refresh handler`
- `fix(db): correct NULL constraint error in user model`
- `chore(deps): bump boto3 from 1.20.0 to 1.21.0`

### Commit Types
- `feat`: new feature
- `fix`: bug fix
- `docs`: documentation only
- `style`: code formatting, no logic changes
- `refactor`: code change that neither fixes nor adds features
- `test`: adding or updating tests
- `chore`: updates to build tools or dependencies

### Pull Request Guidelines
- Always open a pull request (PR), even for small changes
- Link the PR to the corresponding issue/ticket
- Add reviewers and request a review
- Ensure CI checks (lint, type check, test) pass before merging
- Squash commits before merging unless preserving detailed history is intentional
- Use protected branches and prevent force-push on `main`

### Pull Request Template
```
### Description
Briefly describe the purpose of this pull request.

### Changes Made
- Bullet point the key changes made
- Mention any new dependencies or breaking changes

### Checklist
- [ ] Code has been tested locally
- [ ] Documentation has been updated (if necessary)
- [ ] This pull request has a related issue
- [ ] I have followed the coding style guidelines
```

---

## ⚠️ Common Developer Mistakes (Python + AWS)
Avoid these frequent human errors by enforcing standards and performing code reviews:

### Configuration & Secrets
- ❌ Committing `.env` or secrets to version control
- ❌ Hardcoding AWS credentials or tokens
- ❌ Missing environment variable validation before runtime

### Code Quality
- ❌ No type hints or inconsistent usage
- ❌ Mixing tabs and spaces
- ❌ Catching bare `except:` without handling specific exceptions
- ❌ Swallowing errors without logging or re-raising

### Dependency Management
- ❌ Using `latest` versions of dependencies
- ❌ Forgetting to pin dependency versions
- ❌ Failing to regularly audit dependencies for security issues

### Logging & Monitoring
- ❌ Printing instead of using the logging module
- ❌ Logging sensitive info (e.g., passwords, tokens)
- ❌ Logging at `DEBUG` level in production

### Testing
- ❌ Not writing any tests or missing critical paths
- ❌ Using real AWS services in tests without mocks
- ❌ Missing teardown/cleanup after tests (e.g., leaving test resources on S3)

### API Design
- ❌ Returning 200 OK for error conditions
- ❌ Inconsistent response formats across endpoints
- ❌ No schema validation for inputs

### AWS Integration
- ❌ Not retrying failed AWS calls (e.g., due to throttling)
- ❌ Not releasing AWS resources (e.g., open connections)
- ❌ Granting full admin IAM permissions for every Lambda/EC2/app

### CI/CD
- ❌ Missing lint/type/test checks in CI pipeline
- ❌ Directly deploying from local without using CI/CD
- ❌ Skipping Docker build validation in CI

### Git & Version Control
- ❌ Using vague or unrelated commit messages
- ❌ Committing directly to `main` or `master` without PRs
- ❌ Not rebasing before merge, causing noisy histories
- ❌ Pushing code without reviewing diffs
- ❌ Not squashing redundant or WIP commits

---

## 📁 Project Structure
**Standardized layout improves maintainability and readability.**

```
project-name/
├── src/
│   ├── app/                # Core application logic
│   ├── config/             # Configuration loaders
│   ├── services/           # External integrations (AWS, APIs, etc.)
│   ├── models/             # Data schemas and ORM models
│   ├── utils/              # Common helper functions
│   └── main.py             # App entry point
├── tests/                  # Unit and integration tests
├── scripts/                # DevOps or migration scripts
├── requirements.txt        # Dependencies
└── README.md               # Project documentation
```

---

## 🐍 Python Standards
- Follow [PEP8](https://peps.python.org/pep-0008/) using `flake8`, `black`, or [`ruff`](https://docs.astral.sh/ruff/).
- Use type hints and run [mypy](http://mypy-lang.org/) for type safety.
- Prefer [f-strings](https://realpython.com/python-f-strings/) over `.format()` or `%`.
- Isolate dependencies using `venv`, [`poetry`](https://python-poetry.org/), or [`pipenv`](https://pipenv.pypa.io/en/latest/).

---

## 📦 Dependency Management
- Pin exact versions: `requirements.txt` or `poetry.lock`.
- Run [`pip-audit`](https://pypi.org/project/pip-audit/) regularly.
- Avoid unused or bloated packages.
- Use [`pip-tools`](https://github.com/jazzband/pip-tools) for clean dependency resolution (optional).

---

## ⚙️ Configuration Management
- Use environment variables with a `.env` file. Load them using [`python-dotenv`](https://saurabh-kumar.com/python-dotenv/).
- Never commit `.env` files or sensitive configuration.
- Structure your `config.py` to load and validate config via [`pydantic.BaseSettings`](https://docs.pydantic.dev/latest/usage/settings/).
- Use hierarchical config and switch environments via env vars like `APP_ENV=production`.
- Example:
  ```python
  from pydantic import BaseSettings
  class Settings(BaseSettings):
      db_url: str
      aws_region: str
      secret_key: str

      class Config:
          env_file = ".env"
  ```

---

## 📜 Logging & Monitoring
- Use the built-in [`logging`](https://docs.python.org/3/library/logging.html) module.
- Prefer structured logs using [`python-json-logger`](https://github.com/madzak/python-json-logger).
- Integrate with observability tools: [AWS CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/), DataDog, New Relic.
- Use log levels appropriately: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`.
- Do not log secrets or personal data (PII).

---

## ✅ Testing
- Use [`pytest`](https://docs.pytest.org/) as the primary framework.
- Organize tests to match the source layout: `tests/<module>/test_<feature>.py`.
- Use [pytest fixtures](https://docs.pytest.org/en/latest/how-to/fixtures.html) to avoid setup duplication.
- Mock AWS and DB calls using `unittest.mock` or [`moto`](https://github.com/getmoto/moto) for AWS.
- Ensure coverage ≥80% using [`coverage.py`](https://coverage.readthedocs.io/).

---

## 🌐 API Design (REST)
- Use [FastAPI](https://fastapi.tiangolo.com/) or [Flask](https://flask.palletsprojects.com/) if FastAPI isn't suitable.
- Always use URL versioning (e.g. `/v1/users`).
- Define clear request/response schemas using `pydantic`.
- Handle validation errors with custom exception handlers.
- Return proper status codes and messages (avoid generic 500s).
- Use OpenAPI docs (FastAPI auto-generates).

---

## 🔒 Security Guidelines
- Use [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) or [SSM Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) to manage credentials.
- Load secrets at runtime; never hardcode.
- Validate inputs to avoid XSS, SQL injection, etc.
- Use `bandit` for static security analysis.
- Keep packages updated with known vulnerability patches.
- Apply the [Principle of Least Privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) to IAM policies.

---

## ☁️ AWS Integration Best Practices
- Use [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html) or [aiobotocore](https://aiobotocore.readthedocs.io/).
- Implement retry mechanisms using [`botocore.config.Config(retries=...)`](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/retries.html).
- Handle edge cases: rate limits, service quotas, timeouts.
- Separate AWS credentials using named profiles or IAM roles in EC2/Lambda/ECS.
- Store object metadata/tags in S3 for better tracing and debugging.

---

## 🔁 CI/CD and DevOps
- Use [GitHub Actions](https://docs.github.com/en/actions) or [GitLab CI/CD](https://docs.gitlab.com/ee/ci/).
- Use separate workflows for: lint, test, build, deploy.
- Example GitHub Actions steps:
  - `black . --check`
  - `mypy .`
  - `pytest --cov=src tests/`
- Prefer `Docker` for reproducible builds.
- Use `Makefile` or `justfile` to document commands.
- Infrastructure should be code-managed via [Terraform](https://www.terraform.io/) or [AWS CDK](https://docs.aws.amazon.com/cdk/).

---

## 📝 Documentation
- Document all public APIs and business logic using [docstrings](https://realpython.com/documenting-python-code/).
- Use [Sphinx](https://www.sphinx-doc.org/) for API docs generation.
- FastAPI auto-generates [OpenAPI](https://swagger.io/specification/) docs via `/docs`.
- Your `README.md` should include:
  - Project purpose
  - Setup instructions (including env setup)
  - Dependency management instructions
  - API reference or a link to auto-generated docs

---

## ⏭️ Next Sections (To Be Added)
- Node.js backend standards
- CI/CD reusable templates (YAML examples)
- Frontend-backend integration guidelines
- Infrastructure & monitoring standards (Terraform, AWS CDK, Prometheus, etc.)

---

## 📚 References
- [PEP8 Style Guide](https://peps.python.org/pep-0008/)
- [FastAPI Docs](https://fastapi.tiangolo.com/)
- [Python Logging](https://docs.python.org/3/library/logging.html)
- [Boto3 Docs](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
- [OWASP Python Security Guide](https://owasp.org/www-project-python-security/)
- [python-dotenv](https://saurabh-kumar.com/python-dotenv/)
- [pydantic BaseSettings](https://docs.pydantic.dev/latest/usage/settings/)
- [Bandit Security Scanner](https://bandit.readthedocs.io/en/latest/)
- [pip-audit](https://pypi.org/project/pip-audit/)
- [pytest Fixtures](https://docs.pytest.org/en/latest/how-to/fixtures.html)
- [moto AWS mocks](https://github.com/getmoto/moto)
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)

---

*Last updated: 2025-04-03*


