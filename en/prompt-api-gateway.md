# Prompt — API Gateway for microservices

> [🇷🇺 Русский](../ru/prompt-api-gateway.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #api-gateway #microservice #security #devops #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — API Gateway Architecture Configurator](https://logicballs.com/ai-prompt/prompts/api-gateway-configurator)
**Delta from original:** added explicit edge-concerns / domain-concerns split, requirement to compare gateway options, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need to design a gateway for a set of backend services
- When auth, rate limits, routing, and observability must be considered up front
- When the team is choosing between Kong, NGINX, Traefik, Envoy, or a managed gateway

## What to substitute

- `{{gateway_type}}` — enum: `kong | nginx | traefik | envoy | aws_api_gateway | apisix | other`
- `{{infrastructure_context}}` — string, e.g. `EKS in AWS, ALB in front of the gateway`
- `{{traffic_patterns}}` — string, e.g. `~500 RPS, bursts 2k RPS, 70% reads`
- `{{security_requirements}}` — string, e.g. `OAuth2 + mTLS for service-to-service; PCI scope`
- `{{backend_services}}` — list, e.g. `payments-api; user-api; notifications-api`
- `{{special_requirements}}` — string, e.g. `canary routing 5%; WebSocket for /ws`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Explicitly stated which policies live on the gateway and which inside the services
- If multiple gateway options are viable — a short comparison is provided
- The configuration or IaC fragments are shown in full, not "example config for one route"
- The observability section covers access logs, metrics, and tracing headers — all three

## Prompt

```text
Act as a Principal Cloud Infrastructure Architect. Design a production-grade API gateway configuration for a microservices system.

Context:
- Gateway technology: {{gateway_type}}
- Infrastructure context: {{infrastructure_context}}
- Traffic patterns: {{traffic_patterns}}
- Security and compliance requirements: {{security_requirements}}
- Backend services: {{backend_services}}
- Special constraints: {{special_requirements}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

In the response, prepare:
1. [required] The gateway layer architecture
2. [required] Routing rules:
   - path-based
   - host-based
   - version-based
3. [required] AuthN/AuthZ:
   - JWT / OAuth2 / API key / mTLS
   - where authentication is checked
   - where ACLs / scopes / roles apply
4. [required] Policies:
   - rate limiting
   - request size limits
   - retries
   - circuit breaking
   - caching, if applicable
5. [required] Observability:
   - access logs
   - metrics
   - tracing headers
6. [required] Configuration / IaC fragments
7. [required] Risks and anti-patterns

Don't do this:
- Business logic in the gateway (e.g., complex JSON-to-JSON mapping).
- Auth check in every service AND in the gateway without justification (double check, extra latency).
- A rate limit that only applies to a single route while others are open.
- Gateway without access logs — there'll be nothing to debug an incident with later.

Requirements:
- Don't turn the gateway into a business-logic monolith
- Separate edge concerns from domain concerns
- Explicitly state which policies live on the gateway and which inside services
- If multiple realistic options exist, compare them briefly

Response format:
- Summary
- Architecture
- Policy table
- Config / YAML / declarative examples
- Risks
```
