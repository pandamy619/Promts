# Prompt — GraphQL schema

> [🇷🇺 Русский](../ru/prompt-graphql-schema.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #graphql #backend #schema #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — GraphQL Schema Generator](https://logicballs.com/ai-prompt/prompts/graphql-schema-generator)
**Delta from original:** added a section on N+1 risk zones, an explicit "where GraphQL wins over REST" comparison, and the canonical "behavior when context is missing" block.

---

## When to use

- When the service is built around GraphQL rather than REST
- When you need a clean SDL schema and an approach to federation quickly
- When pagination, auth, and resolver boundaries need to be thought through before implementation

## What to substitute

- `{{domain_model}}` — list, e.g. `User; Order; OrderItem; Product`
- `{{database_type}}` — enum: `postgres | mysql | mongodb | dynamodb | mixed`
- `{{auth_requirements}}` — string, e.g. `JWT; field-level scope checks; admin-only mutations`
- `{{federation_requirements}}` — enum: `none | apollo_federation_v2 | schema_stitching`
- `{{scalar_customization}}` — list, e.g. `DateTime; UUID; Money`
- `{{pagination_type}}` — enum: `cursor (relay) | offset | both`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- SDL is shown in full for all types, no `...` or shortcuts
- N+1 risk zones are flagged — at least 2 concrete places where a data loader is needed
- Explicitly stated where GraphQL wins over REST and where it doesn't
- Pagination is consistent across all list fields

## Prompt

```text
Act as a principal GraphQL architect. Generate a production-ready GraphQL schema and implementation recommendations.

Context:
- Domain model: {{domain_model}}
- Database / storage: {{database_type}}
- Auth / authorization requirements: {{auth_requirements}}
- Federation / architecture: {{federation_requirements}}
- Required custom scalars: {{scalar_customization}}
- Pagination strategy: {{pagination_type}}

If you don't have enough context for a quality answer, ask up to 3 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare:
1. [required] Type system:
   - object types
   - input types
   - enums
   - interfaces / unions if needed
2. [required] Query and Mutation design
3. [required] Pagination pattern
4. [required] Error handling strategy
5. [required] Auth / field-level authorization approach
6. [required] N+1 risk zones and how to address them
7. [required] An SDL example
8. [if room remains] Recommendations on resolvers / services / data loaders

Don't do this:
- Public types one-to-one mirror the storage model (field names and types).
- A single resolver triggers N+1 queries without a data loader.
- `Mutation { doStuff(input: JSON): JSON }` — typing is lost, GraphQL turns into RPC.

Requirements:
- Don't make the schema overly "smart" if the domain is simple
- Explicitly state where GraphQL wins over REST and where it doesn't
- Don't mix internal storage models with the public schema without need

Response format:
- Summary
- Type list
- SDL
- Resolver notes
- Risks and trade-offs
```
