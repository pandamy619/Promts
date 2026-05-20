# Prompt — Deployment runbook

> [🇷🇺 Русский](../ru/prompt-deployment-runbook.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #runbook #deploy #ops #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [Appaca — Deployment Runbook](https://www.appaca.ai/resources/prompt-library/software/deployment-runbook), [SurePrompts — Operations Runbook](https://sureprompts.com/prompts/technical/runbook-template)
**Delta from original:** added explicit (quantitative) rollback criteria, a stakeholder message template, a post-deploy retro checklist, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need to formalize step-by-step release instructions quickly
- When you want to reduce manual deploy errors
- When you need a template for on-call, rollback, and stakeholder communications

## What to substitute

- `{{service_name}}` — string, e.g. `payments-api`
- `{{environment}}` — enum: `dev | stage | prod`
- `{{tech_stack}}` — string, e.g. `FastAPI / Postgres / Kubernetes`
- `{{deployment_tool}}` — enum: `helm | argocd | kubectl | terraform | custom`
- `{{owners}}` — string, e.g. `@team-payments; on-call @vladtagaev`
- `{{health_endpoints}}` — list, e.g. `GET /healthz; GET /readyz`
- `{{rollback_method}}` — enum: `kubectl_rollout_undo | helm_rollback | redeploy_previous_image | blue_green_switch`
- `{{stakeholders}}` — list, e.g. `#payments-releases; product-owner; on-call SRE`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Every step has either a concrete command or an explicit success criterion
- Rollback criteria are quantitative (`error rate > X%`, `p95 > Y ms`, `health check failures > N in a row`)
- The team/business message template is filled with real text, not "insert here"
- The "Common mistakes" section contains at least 3 concrete cases with actions

## Prompt

```text
Act as a senior SRE / DevOps engineer. Write a practical deployment runbook for a service release.

Context:
- Service: {{service_name}}
- Environment: {{environment}}
- Stack: {{tech_stack}}
- Deployment tool: {{deployment_tool}}
- Owners: {{owners}}
- Health endpoints / smoke checks: {{health_endpoints}}
- Rollback method: {{rollback_method}}
- Stakeholders to notify: {{stakeholders}}

If you don't have enough context for a quality answer, ask up to 3 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Make the runbook executable by an engineer who doesn't hold the full context in their head.

Runbook structure:
1. [required] Purpose and when to apply
2. [required] Preconditions / access / roles
3. [required] Pre-deploy checklist
4. [required] Step-by-step deploy with commands
5. [required] Success verification:
   - health checks
   - smoke tests
   - logs / metrics / alerts
6. [required] Explicit rollback criteria (quantitative)
7. [required] Step-by-step rollback
8. [required] Post-deploy monitoring window
9. [required] Team/business message template
10. [required] Common mistakes and what to do

Don't do this:
- A step "make sure everything is OK" with no concrete criterion.
- Rollback described as "roll back", without specific commands.
- Pre-deploy checklist without owners on the items ("who checked the migration?").
- A message template like "{{insert here}}" — fill it with real text.

Requirements:
- Use numbered steps
- Put commands in code blocks
- If an action is dangerous, mark it explicitly
- Where there's a decision point, add "if X → do Y"
- Don't leave vague phrasing like "check it's all OK"
- For checks, specify what exactly counts as a successful result

Response format:
- A ready-to-use Markdown runbook without theory padding
- At the end, a short checklist for postmortem / retro improvements to the runbook
```
