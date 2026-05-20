# Prompt — Kubernetes manifests

> [🇷🇺 Русский](../ru/prompt-kubernetes-manifests.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #kubernetes #devops #yaml #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — Kubernetes Config Generator](https://logicballs.com/ai-prompt/prompts/kubernetes-config-generator)
**Delta from original:** added requirements for separate liveness/readiness probes, mandatory resources, PDB for critical services, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need a baseline for deploying a service into Kubernetes quickly
- When you don't want to forget probes, resources, securityContext, and ingress
- When the team is standardizing manifests for new services

## What to substitute

- `{{application_name}}` — string, e.g. `payments-api`
- `{{container_image}}` — string, e.g. `registry.example.com/payments-api:v1.4.2`
- `{{namespace}}` — string, e.g. `payments`
- `{{environment_type}}` — enum: `dev | stage | prod`
- `{{replica_count}}` — int, e.g. `3`
- `{{resource_requirements}}` — string, e.g. `req cpu 100m / mem 256Mi; lim cpu 500m / mem 512Mi`
- `{{exposed_ports}}` — list, e.g. `8080 (http)`
- `{{environment_variables}}` — list, e.g. `DATABASE_URL; KAFKA_BROKERS; LOG_LEVEL`
- `{{storage_requirements}}` — string, e.g. `no PVC` or `1 PVC 10Gi gp3`
- `{{ingress_requirements}}` — string, e.g. `payments.example.com, TLS, rate-limit 100rps`
- `{{security_constraints}}` — string, e.g. `runAsNonRoot, readOnlyRootFilesystem, no privileged`
- `{{additional_requirements}}` — string, e.g. `HPA on CPU 70%; PDB minAvailable 2`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Liveness and readiness — separate probes, not the same endpoint
- Every container has `resources.requests` and `resources.limits` set
- In prod, `PodDisruptionBudget` exists if the service is critical
- securityContext: `runAsNonRoot: true`, `readOnlyRootFilesystem: true` (or an explicit justification why not)
- "What to check before prod" contains at least 5 items

## Prompt

```text
Act as a senior Kubernetes architect. Generate production-grade Kubernetes manifests for the service.

Context:
- Application name: {{application_name}}
- Container image: {{container_image}}
- Namespace: {{namespace}}
- Environment type: {{environment_type}}
- Replica count: {{replica_count}}
- Resources: {{resource_requirements}}
- Ports: {{exposed_ports}}
- Environment variables: {{environment_variables}}
- Storage requirements: {{storage_requirements}}
- Ingress / exposure: {{ingress_requirements}}
- Security constraints: {{security_constraints}}
- Additional requirements: {{additional_requirements}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare:
1. [if room remains] `Namespace` if needed
2. [required] `Deployment`
3. [required] `Service`
4. [required] `ConfigMap` and `Secret` placeholders
5. [required] `Ingress` or another publishing method
6. [if room remains] `HorizontalPodAutoscaler` if justified
7. [required for prod] `PodDisruptionBudget` if the service is critical

Always include:
- readiness and liveness probes — separate
- requests / limits
- non-root execution where possible
- rolling update strategy
- labels / selectors / naming conventions
- configuration kept separate from the image
- notes on secure secret storage

Don't do this:
- Deployment without `resources` requests and limits.
- Liveness probe = readiness probe (breaks rolling update, causes unnecessary restarts).
- Secrets in `ConfigMap` or directly in the manifest.
- `imagePullPolicy: Always` for tagged images — extra pull and potential instability.

Response format:
1. Brief summary of decisions
2. "Manifest / Purpose" table
3. Full YAML per resource
4. "What to check before prod" section
```
