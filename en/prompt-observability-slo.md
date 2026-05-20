# Prompt — Observability and SLO design

> [🇷🇺 Русский](../ru/prompt-observability-slo.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #observability #slo #sre #service #single-shot
**Status:** `🧪 Experimental`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [Google SRE Workbook — Implementing SLOs](https://sre.google/workbook/implementing-slos/), [Liz Fong-Jones — Multi-window multi-burn-rate alerting](https://sre.google/workbook/alerting-on-slos/)
**Delta from original:** requires SLI linkage to user journey (not internal metric), multi-window multi-burn-rate alerting format, and an explicit section on logging/tracing correlation fields.

---

## When to use

- When a service is moving to production and you need to decide what and how to observe
- When the team is drowning in alerts and wants to move to SLO-based alerting
- When you need to separate "I see the metric" from "I understand whether users are suffering"

## What to substitute

- `{{service_name}}` — string, e.g. `payments-api`
- `{{critical_user_journeys}}` — list, e.g. `create payment; check status; refund`
- `{{current_metrics}}` — string, e.g. `Prometheus on /metrics; have http_requests_total and http_request_duration_seconds`
- `{{logging_stack}}` — enum: `loki | elastic | datadog_logs | cloudwatch | other`
- `{{tracing_stack}}` — enum: `opentelemetry | jaeger | tempo | datadog_apm | none`
- `{{error_budget_policy}}` — string, e.g. `on budget exhaustion — freeze features until recovery`
- `{{team_maturity}}` — enum: `none | basic_metrics | mature_metrics_no_slo | slo_in_progress`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- For each critical user journey, at least 2 SLIs are defined (latency + availability)
- SLO is tied to user-visible behavior, not to internal metrics (CPU, memory aren't SLOs)
- Alerting is based on burn rate (multi-window multi-burn-rate), not instantaneous values
- Specific log fields are listed for correlation (`trace_id`, `request_id`, `user_id`)
- Dashboards are split into 3 levels: overview / per-journey / drill-down

## Prompt

```text
Act as a Senior SRE with experience rolling out SLO-based observability for teams of varying maturity. Prepare a practical observability and SLO design for the service.

Context:
- Service: {{service_name}}
- Critical user journeys: {{critical_user_journeys}}
- What's currently in place: {{current_metrics}}
- Logging stack: {{logging_stack}}
- Tracing stack: {{tracing_stack}}
- Error budget policy: {{error_budget_policy}}
- Team maturity: {{team_maturity}}

If you don't have enough context for a quality answer, ask up to 7 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare:

1. [required] SLIs:
   - for each critical user journey — at least 2 SLIs (availability + latency)
   - SLI formula: numerator / denominator (e.g., "share of requests with 2xx/3xx over the window" / "all requests")
   - explicitly state what counts as a "good" event and what as "bad"
2. [required] SLOs:
   - target value per SLI (e.g., 99.9% over 30-day rolling)
   - rolling window (28/30 days is normal; not calendar month)
   - justification: why this number (not 99.99% — is it worth it)
3. [required] Error budget and policy:
   - budget calculation (over 30 days at SLO 99.9%, budget = 43 minutes)
   - what the team does when it's exhausted
   - signal at "10% budget remaining" / "50% burned"
4. [required] Alerting strategy:
   - multi-window multi-burn-rate (fast burn 1h × 14.4; slow burn 6h × 6)
   - DO NOT alert on a single 500 response
   - what pages (S1) vs what tickets (warning)
5. [required] Logging:
   - structured logs with concrete required fields: `timestamp` (ISO8601 UTC), `level`, `service`, `trace_id`, `request_id`, `user_id`, `event`
   - forbidden fields (PII / PCI data)
   - where written (stdout → log collector)
6. [required] Tracing:
   - where spans are required (incoming request, outgoing call to another service, DB query > 50ms)
   - propagation headers (W3C trace-context)
   - sampling strategy (e.g., 10% baseline + 100% on error)
7. [required] Dashboards (hierarchy):
   - overview: SLO status, error budget burn, key SLIs
   - per-journey: SLI broken down by endpoint / user segment
   - drill-down: technical metrics (CPU, mem, DB pool, queue depth)
   - rule: DO NOT start with drill-down
8. [if room remains] Rollout roadmap by team maturity

Don't do this:
- SLO 99.99% "because it's cool" — that requires serious investment; justify or lower it.
- Alert on every error log — alert fatigue.
- Logging without structured fields ("payment failed for user 123") — can't query later.
- Tracing only in a single service — loses end-to-end visibility.
- SLI = "CPU < 80%" — that's not an SLI, that's an internal metric.
- A dashboard that starts with a CPU graph — useless for "are users suffering".

Response format:
- Executive summary (why we need this, what changes)
- Table "User journey | SLI | SLO | Budget"
- Error budget calculation with numbers
- Alerting config (PromQL / rules in JSON, depending on stack)
- Description of logging schema and tracing rules
- Dashboard structure mock-up (text, hierarchy)
```
