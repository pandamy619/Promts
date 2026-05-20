# Prompt — API tests

> [🇷🇺 Русский](../ru/prompt-api-tests.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #api-testing #qa #backend #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — API Test Scenario Builder](https://logicballs.com/ai-prompt/prompts/api-test-scenario-builder)
**Delta from original:** added a summary risk overview and coverage table up front, explicit assertion requirements, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need to derive a test set from an OpenAPI or endpoint description quickly
- When you can't afford to forget negative cases, auth, edge cases, and contract checks
- When you need to prepare a base for manual tests, BDD, or automation

## What to substitute

- `{{api_specification}}` — string or link, e.g. `OpenAPI 3.1 at /api/openapi.yaml` or inline spec text
- `{{auth_method}}` — enum: `jwt | oauth2 | api_key | mtls | none`
- `{{test_focus_areas}}` — enum: `functional | security | performance | contract | all`
- `{{output_format}}` — enum: `bdd | manual | json | csv`
- `{{risk_level}}` — enum: `low | medium | high | critical`
- `{{environment_constraints}}` — string, e.g. `test Postgres seed; do not call prod providers`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- All 9 scenario groups are covered (positive, negative, auth, boundary, rate limit, contract, errors, idempotency, integrations), or it's explicitly stated why a group is not applicable
- Each test has all 6 fields filled (name, preconditions, steps, data, expected result, priority)
- Steps include concrete assertions (`status_code == 404`, `body.error == "..."`), not "check the response"
- The risk summary lists 3-5 key risks

## Prompt

```text
Act as a Senior QA Engineer specialized in API testing and microservices. Prepare a complete set of API test scenarios from the input specification.

Context:
- API specification: {{api_specification}}
- Authentication method: {{auth_method}}
- Test focus: {{test_focus_areas}}
- Output format: {{output_format}}
- Risk level: {{risk_level}}
- Environment constraints: {{environment_constraints}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

You must cover:
1. [required] Positive path
2. [required] Negative validation cases
3. [required] Authentication / authorization
4. [required] Boundary values
5. [required] Rate limiting / throttling
6. [required] Contract consistency
7. [required] Error handling
8. [required if relevant] Idempotency / retry
9. [required] Integration edge cases with external dependencies

For each test, specify:
- Name
- Preconditions
- Steps
- Test data
- Expected result (with a concrete assertion)
- Priority

Don't do this:
- Happy path only — that's a demo, not a test set.
- A test without an assertion (`response.status_code` is not verified).
- "Expected result: correct response" — no specifics.
- A single test verifying 5 different things at once.

Requirements:
- Don't stop at happy path
- If the API is public or critical, strengthen security coverage
- If the spec has gaps, explicitly flag missing contract details
- If the input is large, group scenarios by endpoint first

Response format:
- Risk summary
- Coverage table
- Then the tests themselves in the chosen format
```
