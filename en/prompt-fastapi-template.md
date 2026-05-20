# Prompt — FastAPI service template

> [🇷🇺 Русский](../ru/prompt-fastapi-template.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #fastapi #backend #service #template #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [DocsBot — FastAPI Project Scaffold](https://docsbot.ai/prompts/programming/fastapi-project-scaffold), [PyPI — create-fastapi-service](https://pypi.org/project/create-fastapi-service/0.1.0/)
**Delta from original:** stack and tool versions are extracted into placeholders (were hardcoded before), added requirements for async ORM and prohibitions on "same as above".

---

## When to use

- When you need to quickly generate a starter template for a new backend service
- When the team wants a single project skeleton instead of assembling a service from scratch every time
- When structure, migrations, health checks, logging, and tests need to be in place from day one

## What to substitute

- `{{service_name}}` — string, e.g. `payments-api`
- `{{domain_description}}` — string, e.g. `service creates and verifies payments via providers`
- `{{python_version}}` — string, e.g. `3.13`
- `{{database}}` — enum: `postgres | mysql | sqlite | mongodb`
- `{{orm}}` — enum: `sqlalchemy_async | tortoise | sqlmodel`
- `{{migration_tool}}` — enum: `alembic | yoyo | none`
- `{{auth_mode}}` — enum: `jwt | oauth2 | api_key | mtls | none`
- `{{external_integrations}}` — list, e.g. `stripe; sendgrid; kafka`
- `{{background_jobs}}` — enum: `celery | arq | dramatiq | none`
- `{{linter}}` — enum: `ruff | flake8 | none`
- `{{test_framework}}` — enum: `pytest | unittest`
- `{{deployment_target}}` — enum: `docker | kubernetes | bare_metal | serverless`
- `{{repo_conventions}}` — string, e.g. `conventional commits, src layout, poetry`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- The directory tree contains all listed subfolders and doesn't omit any of those required
- Every key file is shown in full, no `same as above` and no `...`
- The ORM layer uses async API only (if `{{orm}}` is asynchronous)
- Health and readiness are separate endpoints, not a single shared one
- A runnable health-endpoint test is included, not "add tests here"
- `README.md` contains real run commands (`make`, `docker compose up`, `pytest`), not placeholders

## Prompt

```text
Act as a senior backend/platform engineer. Generate a production-ready FastAPI service template, not a demo project.

Context:
- Service name: {{service_name}}
- What the service does: {{domain_description}}
- Python version: {{python_version}}
- Database: {{database}}
- ORM: {{orm}}
- Migration tool: {{migration_tool}}
- Authentication: {{auth_mode}}
- External integrations: {{external_integrations}}
- Background jobs / scheduler / workers: {{background_jobs}}
- Linter: {{linter}}
- Test framework: {{test_framework}}
- Deployment target: {{deployment_target}}
- Team repo conventions: {{repo_conventions}}

If you don't have enough context for a quality answer, ask up to 7 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Template requirements:
1. Python {{python_version}}
2. FastAPI + Pydantic v2
3. ORM per {{orm}}, migrations per {{migration_tool}} (if not `none`)
4. Configuration via settings/env
5. Structured logging
6. Health and readiness endpoints (separate)
7. Basic error handling and unified error response
8. Dockerfile + compose for local development (if `{{deployment_target}}` implies it)
9. {{linter}} + {{test_framework}}
10. README with run instructions

Lay out the structure so the project is convenient to grow:
- `app/api`
- `app/core`
- `app/db`
- `app/models`
- `app/repositories`
- `app/schemas`
- `app/services`
- `app/tasks`
- `tests`
- `alembic` (or your migration_tool's directory)

In the response, you must provide:
1. [required] Brief architectural decisions and the assumptions taken
2. [required] Full directory tree
3. [required] Contents of key files:
   - `app/main.py`
   - `app/core/config.py`
   - `app/core/logging.py`
   - `app/api/routes/health.py`
   - example router / schema / model / repository / service
   - migrations config (`alembic/env.py` or equivalent)
   - `Dockerfile`
   - `compose.yaml` or `docker-compose.yml`
   - `.env.example`
   - `pyproject.toml`
   - `README.md`
4. [required] An example test for the health endpoint
5. [if room remains] TODO list of what the team should adapt for their domain

Don't do this:
- Synchronous `Session` instead of `AsyncSession` when an async ORM is selected.
- `from app.main import app` inside `app/main.py` — circular import.
- `COPY . .` in Dockerfile without `.dockerignore` (pulls in `.git`, `__pycache__`, secrets).
- `# TODO: add tests` instead of a real health test.

Critically important:
- Don't produce a toy structure
- Don't hide important pieces behind "same as above"
- If certain integrations aren't specified, create extensible stubs
- Don't invent unnecessary dependencies

Response format:
- Markdown
- First a brief overview of decisions
- Then the file tree
- Then code per file in separate fenced code blocks
```
