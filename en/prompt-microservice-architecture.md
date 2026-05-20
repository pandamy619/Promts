# Prompt — Microservice architecture

> [🇷🇺 Русский](../ru/prompt-microservice-architecture.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #microservice #architecture #backend #service #single-shot #staged
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v2
**Source idea:** [LogicBalls — AI Microservice Architecture Planner](https://logicballs.com/ai-prompt/prompts/microservice-architecture-planner), [DocsBot — Microservices Architecture Diagram](https://docsbot.ai/prompts/technical/microservices-architecture-diagram)
**Delta from original:** added explicit "is a microservice even justified" pushback, required container-level Mermaid diagram, risk table, and the canonical "behavior when context is missing" block. **v2:** added `staged` mode via `{{mode}}` — for serious work the model first builds a plan and waits for confirmation, then executes step by step. `quick` mode (old behavior) is preserved for drafts.

---

## When to use

- When you need to decide whether a microservice is justified at all
- When you need to design a new service or carve one out of a monolith
- When it's important to lock down data ownership, integrations, risks, and migration strategy up front

**When to pick `quick`:** you need a quick single-pass draft for back-of-envelope reasoning or to bring to the team for discussion.

**When to pick `staged`:** real design work where the cost of getting bounded contexts / data ownership wrong is high, and you want the option to correct the plan before all the Mermaid and risk tables get generated.

## What to substitute

- `{{mode}}` — enum: `quick | staged`
- `{{domain}}` — string, e.g. `payments`
- `{{business_goal}}` — string, e.g. `reduce checkout latency to p95 < 300ms`
- `{{functional_requirements}}` — list, e.g. `create payment; idempotent retry; refund`
- `{{non_functional_requirements}}` — list, e.g. `99.95% availability; PCI DSS; multi-region`
- `{{load_and_sla}}` — string, e.g. `~200 RPS peak, p95 < 300ms, RPO 5m`
- `{{current_landscape}}` — string, e.g. `Django + Postgres monolith, Kafka available`
- `{{stack_constraints}}` — string, e.g. `Python + Go allowed; no Java`
- `{{security_and_compliance_requirements}}` — string, e.g. `PCI DSS SAQ-D, GDPR`
- `{{team_size_and_level}}` — string, e.g. `4 mid backend, 1 senior, no dedicated SRE`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- A container-level Mermaid diagram is included
- The "Risk / Why it'll arise / How to mitigate" table is filled for every risk listed in the response, no empty cells
- The `monolith / modular monolith / microservice` decision is explicitly argued with at least 2 arguments either way
- For each bounded context: who owns it, what data it owns, and what it is forbidden to do
- If a monolith-to-service migration is proposed — the plan is broken into stages, each with a "done" criterion

In `staged` mode additionally:

- The first-step plan contains 3-7 items, each a distinct response artifact
- The model does not begin execution until explicit confirmation is received
- After each plan item, the model waits for `next` or feedback; it does not auto-roll the next step

## Prompt

```text
Act as a Principal Software Architect with experience designing distributed systems, DDD, event-driven integrations, and production backend platforms.

I need to design a service, or to decide whether it should be split out as a microservice at all.

Execution mode: {{mode}}

Context:
- Domain: {{domain}}
- Business goal: {{business_goal}}
- Functional requirements: {{functional_requirements}}
- Non-functional requirements: {{non_functional_requirements}}
- Load and SLA: {{load_and_sla}}
- Current landscape / existing systems: {{current_landscape}}
- Stack constraints: {{stack_constraints}}
- Security and compliance requirements: {{security_and_compliance_requirements}}
- Team size and seniority: {{team_size_and_level}}

If you don't have enough context for a quality answer, ask up to 7 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

First, run a "microservices premium" check (regardless of mode):
1. Assess whether a separate microservice is actually warranted here.
2. If a microservice is not justified, honestly propose a simpler option: modular monolith, extending an existing service, or a separate bounded context inside the current system.
3. Don't pick microservices because they're fashionable.

────────────────────────────────────────────────────────────────
### If {{mode}} = staged

1. First produce a short plan: which sections you'll deliver, in what order, and what depends on what. The plan has 3-7 items, no more.
2. Wait for explicit confirmation (`ok` / `next` / edits to the plan). Do not start execution until confirmed.
3. After confirmation, execute one plan item at a time. After each item, stop and wait for `next` or feedback.
4. If during execution it becomes clear the plan was wrong — propose a revised plan and wait for confirmation again.

In each plan item cover only its section — don't run ahead. Mermaid and risk table are separate plan items, not appendices to other ones.

────────────────────────────────────────────────────────────────
### If {{mode}} = quick

Deliver the response in full, in one message, with all sections below.

────────────────────────────────────────────────────────────────

Sections to be delivered (all at once for quick, one at a time for staged):

1. [required] Final decision: monolith / modular monolith / microservice, with arguments.
2. [required] Bounded contexts and service boundaries.
3. [required] Responsibility of each service and what it is forbidden to do.
4. [required] Data ownership: which tables/collections/events belong to whom.
5. [required] Inter-service communication:
   - synchronous: REST/gRPC
   - asynchronous: Kafka/RabbitMQ/events
   - where eventual consistency is acceptable
6. [required] Contracts:
   - key APIs
   - events
   - idempotency / retry / DLQ requirements
7. [if room remains] Infrastructure components:
   - API gateway
   - auth
   - service discovery
   - observability
   - background workers / schedulers
8. [required] Risks (table):
   - data consistency
   - distributed transactions
   - latency
   - coupling
   - operational overhead
9. [required] Step-by-step migration plan, if moving from monolith to service.
10. [required] Container-level Mermaid diagram.

Response format in quick mode:
- Start with a short executive summary
- Then structured sections
- Then the "Risk / Why it'll arise / How to mitigate" table
- Then Mermaid

Don't do this:
- "Microservices because it's modern" — without arguments for data and team separation.
- One service owns another's tables via direct SQL access.
- "Eventual consistency we'll figure out later" — without saying where exactly and what guarantees.
- In staged mode: dumping all sections at once, ignoring "wait for confirmation".
- In staged mode: invent your own execution order without announcing a plan.

Rules:
- Don't invent requirements that aren't there
- Mark contentious assumptions separately
- Prefer pragmatic solutions
- If there's temptation to over-engineer, indicate a cheaper alternative
```
