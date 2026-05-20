# Prompt — Load testing

> [🇷🇺 Русский](../ru/prompt-load-testing.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #performance #load-testing #qa #service #single-shot
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [LogicBalls — Performance Test Script Generator](https://logicballs.com/ai-prompt/prompts/performance-test-script-generator)
**Delta from original:** added requirements for service-side metrics (not just client), warm-up, common false-positive pitfalls, and the canonical "behavior when context is missing" block.

---

## When to use

- When you need a baseline for load/stress/spike/soak testing quickly
- When the service is prepping for a release and you want to verify latency, throughput, and saturation points
- When you need a starter script for k6, Locust, or Artillery

## What to substitute

- `{{target_system}}` — string, e.g. `payments-api in stage, https://stage.payments.example.com`
- `{{test_type}}` — enum: `load | stress | spike | soak | latency_benchmark`
- `{{language_or_framework}}` — enum: `k6 | locust | artillery | jmeter | gatling`
- `{{concurrency}}` — string, e.g. `ramp 0→500 VU over 5m, hold 30m`
- `{{duration}}` — string, e.g. `45m total (5m ramp + 30m hold + 10m ramp-down)`
- `{{success_criteria}}` — list, e.g. `p95 < 300ms; error rate < 0.5%; throughput ≥ 200 RPS`
- `{{auth_model}}` — enum: `jwt | oauth2 | api_key | none`
- `{{test_data_strategy}}` — string, e.g. `pre-seeded 10k users; round-robin from pool; cleanup after run`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- The script includes a warm-up phase
- Specifies which metrics to watch in the service itself (CPU, memory, DB connections, GC), not just in the client
- Success criteria are quantitative and tied to percentiles, not averages
- A section on common false-positive conclusions is included (e.g., "p95 looks good but p99 saturates — the tail is real")

## Prompt

```text
Act as a Performance Engineer. Prepare a production-grade load testing scenario for the service.

Context:
- Target system: {{target_system}}
- Test type: {{test_type}}
- Tool: {{language_or_framework}}
- Concurrency parameters: {{concurrency}}
- Test duration: {{duration}}
- Success criteria: {{success_criteria}}
- Authentication model: {{auth_model}}
- Test data strategy: {{test_data_strategy}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare:
1. [required] Test goals and what exactly is being measured
2. [required] Load profile:
   - warm-up
   - ramp-up
   - steady state
   - ramp-down
3. [required] Metrics:
   - p50 / p95 / p99 latency
   - throughput
   - error rate
   - saturation signals
   - service metrics (CPU, memory, DB connections, GC)
4. [required] Full script example
5. [required] What to prepare before the run
6. [required] How to interpret results
7. [required] Common pitfalls and false-positive conclusions

Don't do this:
- Run without warm-up — the first minutes will be noise.
- Measure client-side latency only, without service metrics.
- Calculate "average" without percentiles — averages hide the tail.
- Run a stress test against prod without approval.

Requirements:
- Don't stop at RPS counts
- If there are external dependencies, state how they'll skew the results
- Specify metrics to watch on both the client and the service
- If input data is sparse, pick conservative defaults and list them explicitly

Response format:
- Summary
- Test plan
- Script
- Pre-run checklist
- Results interpretation
```
