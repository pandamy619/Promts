# Promts

> [🇷🇺 Русский](../ru/README.md) · 🇬🇧 **English**

> A collection of prompts for backend / platform engineering tasks. Last updated: 2026-05-20

Before editing or adding — [Library standards](./standards.md) and [Reference template](./template.md).

---

## Navigation

### Architecture and design

| File | What's inside | Genre |
| --- | --- | --- |
| [Prompt — Microservice architecture](./prompt-microservice-architecture.md) | Service decomposition, domain boundaries, data, integrations, migration plan | `#single-shot` `#staged` |
| [Prompt — Architecture review](./prompt-architecture-review.md) | Formal review of risks and design weaknesses | `#single-shot` `#critique` |
| [Prompt — Threat modeling](./prompt-threat-modeling.md) | STRIDE analysis by trust boundary, risk matrix, mitigations | `#single-shot` |

### APIs and services

| File | What's inside | Genre |
| --- | --- | --- |
| [Prompt — FastAPI service template](./prompt-fastapi-template.md) | Production-ready FastAPI backend service template | `#single-shot` |
| [Prompt — REST API service](./prompt-rest-api.md) | REST API design, OpenAPI specs, implementation guide | `#single-shot` |
| [Prompt — GraphQL schema](./prompt-graphql-schema.md) | Types, queries, mutations, federation, auth patterns | `#single-shot` |
| [Prompt — WebSocket service](./prompt-websocket.md) | Real-time backend service template with auth, scaling, protocol | `#single-shot` |
| [Prompt — API Gateway for microservices](./prompt-api-gateway.md) | Gateway config, auth, rate limits, routing, observability | `#single-shot` |
| [Prompt — API documentation](./prompt-api-documentation.md) | Uniform documentation by endpoint spec | `#single-shot` |

### Infrastructure and deploy

| File | What's inside | Genre |
| --- | --- | --- |
| [Prompt — Docker configuration](./prompt-docker-config.md) | Dockerfile, Compose, containerization for dev/prod | `#single-shot` |
| [Prompt — Kubernetes manifests](./prompt-kubernetes-manifests.md) | Deployment, Service, Ingress, ConfigMap, Secret, security baseline | `#single-shot` |
| [Prompt — CI/CD pipeline](./prompt-ci-cd.md) | Full check / build / deploy / rollback pipeline | `#single-shot` |
| [Prompt — Deployment runbook](./prompt-deployment-runbook.md) | Step-by-step release runbook with health checks and rollback | `#single-shot` |
| [Prompt — Supply chain security](./prompt-supply-chain-security.md) | SBOM, signing, vulnerability scanning, SLSA | `#single-shot` |

### Operations and incidents

| File | What's inside | Genre |
| --- | --- | --- |
| [Prompt — Incident response runbook](./prompt-incident-response.md) | Severity levels, incident roles, communication cadence, templates | `#single-shot` |
| [Prompt — Blameless postmortem](./prompt-postmortem.md) | Timeline, contributing factors, action items, blameless tone | `#single-shot` |
| [Prompt — Observability and SLO design](./prompt-observability-slo.md) | SLI/SLO for user journeys, error budget, multi-burn-rate alerting | `#single-shot` |
| [Prompt — Data migration / schema change](./prompt-data-migration.md) | Expand-contract, verification queries, rollback plan | `#single-shot` |

### Testing

| File | What's inside | Genre |
| --- | --- | --- |
| [Prompt — API tests](./prompt-api-tests.md) | Positive, negative, security, and edge-case tests | `#single-shot` |
| [Prompt — Load testing](./prompt-load-testing.md) | k6/Locust-style baseline for latency, throughput, stress | `#single-shot` |

## Status legend

| Status | Meaning |
| --- | --- |
| `📋 Template` | Skeleton, needs adaptation for the specific case |
| `🟢 Ready` | Proven, can be used as-is |
| `🧪 Experimental` | Built per standard, but few real runs |

## Genre legend

| Genre | When it applies |
| --- | --- |
| `#single-shot` | Instruction → big response. Default for simple tasks. |
| `#staged` | Plan → confirmation → step-by-step execution. For heavy tasks. |
| `#critique` | Draft → self-review → improve. For review, QA, audit. |
| `#interview` | The model asks questions, then acts. When context is objectively missing. |

## Selection principle

- Prompts are inspired by templates found online and adapted to an engineering context (mirror version with Russian prose lives in [/ru/](../ru/README.md)).
- Focus on backend and platform tasks: service, infrastructure, documentation, deploy.
- Each card has a link to the source, in case you want to return to the original idea.

## Backlog

What's planned to be rewritten into another genre:

- ~~Microservice architecture → `#staged`~~ ✅ done in v2 (supported via `{{mode}}`)
- ~~Architecture review → `#critique`~~ ✅ done in v2 (supported via `{{mode}}`)
- Threat modeling → `#critique` (natural fit for security review)
- FastAPI template → `#staged` (large output by file count)
- Kubernetes manifests → `#staged` (many manifests, each depends on previous)
- CI/CD → `#staged` (large config)
- Observability / SLO design → `#staged` (can go stepwise: first SLI, then alerting, then dashboards)
- Data migration → `#staged` (strategy → application changes → migration steps in phases)

What's planned to be added:

- MkDocs Material + GitHub Pages for a proper site with search and i18n
