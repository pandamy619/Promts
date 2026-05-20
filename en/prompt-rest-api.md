# Prompt — REST API service

> [🇷🇺 Русский](../ru/prompt-rest-api.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #rest-api #openapi #backend #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — REST API Architect & Generator](https://logicballs.com/ai-prompt/prompts/rest-api-generator)
**Delta from original:** added requirements for idempotency on write operations, a section on typical design mistakes, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need to design a new REST API for a service
- When you want not just endpoint ideas but an OpenAPI-compatible structure
- When you need to lock down versioning, auth, errors, and resource model quickly

## What to substitute

- `{{api_name}}` — string, e.g. `payments-api`
- `{{domain}}` — string, e.g. `payment and refund processing`
- `{{framework_or_language}}` — string, e.g. `FastAPI / Python 3.13` or `Spring Boot / Java 21`
- `{{auth_method}}` — enum: `jwt | oauth2 | api_key | mtls | session`
- `{{database}}` — enum: `postgres | mysql | mongodb | dynamodb | other`
- `{{business_requirements}}` — list, e.g. `create a payment; return status; refund by idempotency key`
- `{{non_functional_requirements}}` — list, e.g. `p95 < 200ms; 99.9%; PCI DSS`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- For each endpoint: method, path, request schema, response schema, status codes, error model — all filled, no gaps
- The idempotency-for-write-operations section (POST/PUT/PATCH) is actually worked out, not collapsed to a single line
- "What design mistakes are most likely here" section contains at least 3 concrete risks
- An OpenAPI skeleton (or equivalent in the chosen `{{specification_format}}`) is provided, not merely mentioned

## Prompt

```text
Act as an API Architect and senior backend engineer. Design a production-ready REST API for the service, with a focus on clear contracts and painless API evolution.

Context:
- API name: {{api_name}}
- Domain: {{domain}}
- Implementation language / framework: {{framework_or_language}}
- Authentication method: {{auth_method}}
- Database / storage: {{database}}
- Business requirements: {{business_requirements}}
- Non-functional requirements: {{non_functional_requirements}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

You need to prepare:
1. [required] Overall API strategy:
   - base path
   - versioning
   - naming conventions
   - pagination/filtering/sorting
   - idempotency for write operations
2. [required] List of resources and CRUD / non-CRUD operations
3. [required] Endpoint contracts:
   - method
   - path
   - request schema
   - response schema
   - status codes
   - error model
4. [required] Security rules:
   - auth
   - authorization
   - rate limiting
   - auditability
5. [required] Minimal OpenAPI structure or YAML fragments
6. [if room remains] Implementation layer recommendations:
   - handlers/controllers
   - services
   - repositories
   - validation
7. [required] A section on "what design mistakes are most likely here"

Don't do this:
- `POST /users/getById` — that's RPC under the guise of REST. Use `GET /users/{id}`.
- `200 OK` with body `{"error": "..."}` — status and body must agree.
- Change the format of `id` between endpoints (`int` in one, `uuid` in another).
- Hide validation errors under a generic `400 Bad Request` with no details.

Requirements:
- Don't make RPC under the guise of REST without need
- If the resource model is bad, propose a better one
- Don't invent fields that don't follow from the context
- For every contentious decision, show the trade-off
- If requirements conflict, state it explicitly

Response format:
- Executive summary
- Resources table
- Endpoint contracts
- Error model
- OpenAPI skeleton
- Risks and recommendations
```
