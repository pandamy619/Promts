# Prompt — Blameless postmortem

> [🇷🇺 Русский](../ru/prompt-postmortem.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #postmortem #sre #ops #service #single-shot
**Status:** `🧪 Experimental`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [Google SRE Workbook — Postmortem Culture](https://sre.google/workbook/postmortem-culture/), [Atlassian Blameless Postmortem](https://www.atlassian.com/incident-management/postmortem/blameless)
**Delta from original:** requires at least 3 contributing factors (not a single "root cause"), action items with owner and due date, explicit blameless-tone criteria, and a UTC timeline format.

---

## When to use

- After any S1/S2 incident (or a near-miss with serious potential)
- When raw events need to be structured into a useful document
- When the team wants a blameless practice but slips into "who's to blame"

## What to substitute

- `{{incident_id}}` — string, e.g. `INC-2026-0517-001`
- `{{incident_summary}}` — string, 1-2 sentences, e.g. `payments-api returned 500 on 18% of requests for 47 minutes`
- `{{severity}}` — enum: `S1 | S2 | S3`
- `{{detected_at}}` — ISO datetime UTC, e.g. `2026-05-17T14:23:00Z`
- `{{resolved_at}}` — ISO datetime UTC, e.g. `2026-05-17T15:10:00Z`
- `{{impacted_users}}` — string, e.g. `~12k active users; 850 payments failed`
- `{{impacted_services}}` — list, e.g. `payments-api; checkout-frontend; order-service (degraded)`
- `{{timeline_raw}}` — string, raw events for building the chronology (Slack export, alert timings)
- `{{root_cause_hypothesis}}` — string, optional, e.g. `suspect DB connection pool exhaustion`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Timeline in UTC, no gaps between detection and mitigation
- At least 3 contributing factors, not a single "root cause"
- Each action item has an owner (person / team), severity (P0/P1/P2), and due date
- Tone is blameless: causes are processes and systems, not people ("the process didn't prevent X", not "Ivan forgot X")
- TL;DR is no more than 5 lines, readable in 30 seconds

## Prompt

```text
Act as an SRE experienced in running blameless postmortems on large teams. Compose a postmortem for the incident.

Incident context:
- Incident ID: {{incident_id}}
- Summary: {{incident_summary}}
- Severity: {{severity}}
- Detected: {{detected_at}}
- Resolved: {{resolved_at}}
- Impacted users: {{impacted_users}}
- Impacted services: {{impacted_services}}
- Root cause hypothesis (if any): {{root_cause_hypothesis}}

Raw chronology / events:
{{timeline_raw}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare a postmortem with the following structure:

1. [required] TL;DR — no more than 5 lines, understandable to a non-engineer
2. [required] Impact:
   - how many users affected (a number)
   - how many services degraded
   - financial / reputational impact, if measurable
   - duration of user-facing impact (not total incident length)
3. [required] Timeline in UTC:
   - all key events with timestamps
   - tags `[detection]`, `[mitigation]`, `[escalation]`, `[resolution]`
   - show reaction time at each step (delta from previous event)
4. [required] Contributing factors (at least 3):
   - not a single "root cause", several layers
   - technical + process + organizational
   - for each: what happened and why this layer failed to prevent it
5. [required] What went well:
   - specific actions / systems that worked as designed
   - not generic phrases like "the team did great"
6. [required] What went badly:
   - specific failures (slow alert, wrong playbook, missing dashboard)
   - blameless tone
7. [required] Action items:
   - table: what | owner (team/role) | severity (P0/P1/P2) | due date | "done" criterion
   - at least 3 items; no more than 10 (otherwise they won't get done)
   - not "improve monitoring", but "add alert on DB connection pool > 80% — owner platform, P1, due 2026-06-01"
8. [required] Lessons learned:
   - 2-3 main takeaways the team carries from the incident
9. [if room remains] Open questions — what's still unclear and who should dig in

Don't do this:
- A single "root cause" — real incidents are always multiple contributing factors.
- Action item without an owner ("team will add monitoring").
- "Ivan forgot to check" instead of "the process didn't require a second review at this step".
- TL;DR 15 lines long — it stops being TL;DR.
- Timeline without UTC or with local time without a zone.
- Action items like "improve the process" — without a specific action and "done" criterion.

Response format:
- Ready-to-use Markdown document
- Use `[detection]`, `[mitigation]`, `[escalation]`, `[resolution]` markers in the timeline
- Action items — must be a table, not a bullet list
```
