# Prompt — API documentation

> [🇷🇺 Русский](../ru/prompt-api-documentation.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #api #documentation #backend #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [PromptOT — API Documentation Prompt Template](https://www.promptot.com/use-cases/api-documentation)
**Delta from original:** strict anti-hallucination guardrails (`TBD` instead of inventing), edge-cases section, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need to write documentation for an endpoint spec quickly
- When you want all endpoints documented in a uniform format
- When you want to reduce hallucinations during API doc generation

## What to substitute

- `{{service_name}}` — string, e.g. `payments-api`
- `{{method}}` — enum: `GET | POST | PUT | PATCH | DELETE`
- `{{path}}` — string, e.g. `/v1/payments/{id}`
- `{{endpoint_purpose}}` — string, e.g. `return payment details by id`
- `{{parameters}}` — list, e.g. `id (path, uuid, required); include (query, csv, optional)`
- `{{request_schema}}` — string, e.g. `JSON Schema or model reference`
- `{{response_schema}}` — string, e.g. `JSON Schema or model reference`
- `{{error_codes}}` — list, e.g. `404 PAYMENT_NOT_FOUND; 403 FORBIDDEN; 409 STATE_CONFLICT`
- `{{auth_type}}` — enum: `jwt | oauth2 | api_key | none`
- `{{rate_limits}}` — string, e.g. `100 req/min per token`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- The output has no fields absent from the input spec (or they're marked `TBD`)
- In the parameters table, every field has name, where-passed, type, required-ness, description filled
- The `Errors` section contains all codes from `{{error_codes}}`, no omissions
- Both a request example and a successful response example are shown — real values, not placeholders

## Prompt

```text
Generate clean and strict API documentation for a service endpoint.

Context:
- Service: {{service_name}}
- Endpoint: {{method}} {{path}}
- Purpose: {{endpoint_purpose}}
- Parameters / query / path / headers: {{parameters}}
- Request schema: {{request_schema}}
- Response schema: {{response_schema}}
- Error codes: {{error_codes}}
- Authentication: {{auth_type}}
- Rate limits / special constraints: {{rate_limits}}

If you don't have enough context for a quality answer, ask up to 3 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Task:
1. [required] Write a short, clear description of the endpoint.
2. [required] List all parameters with type, required/optional, and purpose.
3. [required] Generate a realistic request example.
4. [required] Generate a successful response example.
5. [required] Describe errors and what triggers them.
6. [required] Specify auth requirements, constraints, and important notes.

Don't do this:
- Examples with outdated fields not present in the schema.
- "Default" values without explaining what that means or where they came from.
- A `500` error without describing under what conditions it actually fires.
- Empty marketing adjectives like "smart, fast, scalable".

Guardrails:
- Don't invent parameters absent from the input spec
- Don't invent default values without an explicit indication
- If information is missing, mark it as `TBD`
- Required fields must be flagged explicitly
- Don't mix facts from the spec with assumptions

Response format:
## {{method}} {{path}}

Short description

### Authentication

### Parameters
Table with columns:
- Name
- Where passed
- Type
- Required
- Description

### Request example
Code block

### Response example
Code block

### Errors
Table:
- Code
- When it fires
- What it means

### Notes

If, from the endpoint spec alone, you can correctly derive additional constraints or edge cases, add a separate `### Edge cases` section.
```
