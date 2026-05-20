# Prompt — CI/CD pipeline

> [🇷🇺 Русский](../ru/prompt-ci-cd.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #ci-cd #devops #pipeline #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — Production-Grade CI/CD Pipeline Architect](https://logicballs.com/ai-prompt/prompts/ci-cd-pipeline-generator), [PromptOptimizer — CI/CD Pipeline Prompt Templates](https://promptoptimizer.tools/prompts/development/ci-cd-pipeline)
**Delta from original:** added fast/extended CI split, explicit rollback criteria, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need to design a pipeline for a new service quickly
- When you want to standardize PR checks, releases, and deploy
- When you can't afford to forget security scan, smoke, rollback, and secrets

## What to substitute

- `{{project_type}}` — string, e.g. `python web service` / `node monorepo`
- `{{tech_stack}}` — string, e.g. `Python 3.13, FastAPI, Postgres, Docker`
- `{{repo_platform}}` — enum: `github_actions | gitlab_ci | jenkins | circleci | buildkite`
- `{{deployment_target}}` — enum: `kubernetes | ecs | cloud_run | bare_metal | serverless`
- `{{cloud_provider}}` — enum: `aws | gcp | azure | yandex_cloud | on_premise`
- `{{trigger_strategy}}` — string, e.g. `PR → fast CI; main → full CI + deploy stage; tag → deploy prod`
- `{{testing_requirements}}` — list, e.g. `unit; integration with postgres; smoke after deploy`
- `{{security_level}}` — enum: `basic | standard | strict`
- `{{environment_strategy}}` — string, e.g. `dev (auto) → stage (auto on main) → prod (manual approval)`
- `{{rollback_strategy}}` — enum: `kubectl_rollout_undo | blue_green | canary | manual_redeploy`
- `{{optimization_priority}}` — enum: `speed | cost | security | reliability`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- The config for the selected platform is shown in full, copy-paste ready
- Fast CI (≤ 5 minutes) and extended CI are explicitly split
- Rollback criteria are quantitative (`error rate > X%`, `p95 > Y ms`), not "if something goes wrong"
- Secrets are passed via a secret manager, not embedded in YAML
- The "Risk / What breaks / How to defend" section contains at least 5 risks

## Prompt

```text
Act as a Principal DevOps Engineer. Design a production-ready CI/CD pipeline for the service.

Context:
- Project type: {{project_type}}
- Tech stack: {{tech_stack}}
- Repo / CI platform: {{repo_platform}}
- Deployment target: {{deployment_target}}
- Cloud provider: {{cloud_provider}}
- Trigger strategy: {{trigger_strategy}}
- Test requirements: {{testing_requirements}}
- Security scanning level: {{security_level}}
- Environment strategy: {{environment_strategy}}
- Rollback strategy: {{rollback_strategy}}
- Optimization priority: {{optimization_priority}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Generate a pipeline that covers:
1. [required] Pull/merge request checks
2. [required] Building artifacts or containers
3. [required] Security checks
4. [required] Deploy across environments
5. [required] Smoke / health verification after rollout
6. [required] Rollback path

In the response, provide:
1. [required] Pipeline architecture by stage
2. [required] Full config for the selected platform
3. [required] Fast CI vs extended CI split
4. [if room remains] Caching and build acceleration strategy
5. [required] Secrets and credentials management
6. [required] Security and compliance measures:
   - dependency scanning
   - container scanning
   - secrets detection
   - migration safety checks
7. [required] Deployment verification:
   - smoke tests
   - health checks
   - rollback trigger criteria
8. [required] Adoption checklist
9. [required] "Risk / What breaks / How to defend" table

Don't do this:
- A single stage runs `build && test && deploy` without separation.
- Secrets in YAML or a `.env` file that ships with the repo.
- Rollback described as "roll back manually" without specific commands.
- Post-deploy prod tests without auto-rollback on failure.

Constraints:
- Don't propose overengineering without cause
- Use zero-downtime where it's actually warranted
- YAML/config must be copy-paste ready

Response format:
- Brief summary first
- Then pipeline stages diagram
- Then full config
- Then explanations and checklist
```
