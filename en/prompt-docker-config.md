# Prompt — Docker configuration

> [🇷🇺 Русский](../ru/prompt-docker-config.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #docker #compose #devops #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — AI Docker Configuration Creator](https://logicballs.com/ai-prompt/prompts/docker-configuration-creator), [PromptChat — Generate Docker Compose Configuration Files](https://promptschat.com/prompts/write-docker-compose-file/)
**Delta from original:** added non-root and multi-stage defaults, dev/prod separation, an explicit list of dependent services, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need to quickly assemble a Dockerfile and Compose for a new service
- When you want to unify dev/prod containerization
- When you need a strong baseline for healthcheck, volumes, networks, and security

## What to substitute

- `{{project_type}}` — string, e.g. `python web service` / `node worker`
- `{{framework}}` — string, e.g. `FastAPI + uvicorn` / `Express` / `Spring Boot`
- `{{environment_mode}}` — enum: `dev_only | prod_only | dev_and_prod`
- `{{runtime_requirements}}` — string, e.g. `requires libpq, ffmpeg; 512Mi memory`
- `{{dependent_services}}` — list, e.g. `postgres; redis; kafka; minio`
- `{{base_image_preference}}` — string, e.g. `python:3.13-slim` / `distroless` / `alpine`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- The Dockerfile uses multi-stage where applicable for the language/framework
- The application runs as non-root in the prod stage
- `.dockerignore` exists with real contents, not empty
- All dependent services in compose have healthchecks
- The "What to check before prod" section contains at least 5 items

## Prompt

```text
Act as a DevOps engineer designing production-grade Docker configurations for services.

Project context:
- Project type: {{project_type}}
- Stack / framework: {{framework}}
- Environment modes: {{environment_mode}}
- Specific requirements: {{runtime_requirements}}
- Dependent services: {{dependent_services}}
- Base image preference: {{base_image_preference}}

If you don't have enough context for a quality answer, ask up to 3 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Generate a complete containerization bundle for the service.

Must include:
1. [required] `Dockerfile`
2. [required] `.dockerignore`
3. [required] `compose.yaml` or `docker-compose.yml`
4. [required] Environment variables needed to run
5. [required] Healthcheck for critical containers
6. [required] Volumes and networks
7. [required] Rationale for key decisions

Don't do this:
- `COPY . .` without `.dockerignore` (pulls in `.git`, `node_modules`, secrets).
- Run the application as root in the prod stage.
- `RUN apt-get update && apt-get install -y X` without `--no-install-recommends` and without cache cleanup.
- Hardcoded secrets in Dockerfile / compose (always via env / secrets).

Requirements:
- Use multi-stage build when justified
- Run the application as non-root where possible
- Account for fast rebuild and layer caching
- Separate dev and prod modes when it makes sense
- If Postgres/Redis/Kafka/Nginx are needed, add them as separate compose services
- Don't ship insecure defaults without explicit flagging
- If hot reload is needed only in dev, don't pull it into prod

Result format:
1. Brief summary of containerization architecture
2. "File / Purpose" table
3. Full file contents
4. Command list:
   - build
   - local up
   - logs
   - stop
5. "What to check before prod" section
```
