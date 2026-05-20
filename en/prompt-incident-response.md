# Prompt — Incident response runbook

> [🇷🇺 Русский](../ru/prompt-incident-response.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #incident-response #sre #ops #service #single-shot
**Status:** `🧪 Experimental`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [PagerDuty Incident Response](https://response.pagerduty.com/), [Atlassian Incident Handbook](https://www.atlassian.com/incident-management/handbook)
**Delta from original:** strictly separated roles (commander / comms / ops), formalized severity levels with quantitative criteria, added communication timers and a mandatory link to the postmortem prompt.

---

## When to use

- When the team needs a step-by-step incident response playbook for when alerts fire
- When you want to unify roles, communications, and escalations across services
- When incidents happen but actions don't — each time the team improvises from scratch

## What to substitute

- `{{service_name}}` — string, e.g. `payments-api`
- `{{severity_levels}}` — string, e.g. `S1: revenue impact > $10k/h or > 50% users; S2: partial impact; S3: cosmetic`
- `{{on_call_setup}}` — string, e.g. `primary on-call 24/7, secondary business hours; PagerDuty weekly rotation`
- `{{paging_tool}}` — enum: `pagerduty | opsgenie | grafana_oncall | victorops | manual_slack`
- `{{communication_channels}}` — string, e.g. `Slack #incidents-payments; status page status.example.com; email for S1+`
- `{{stakeholder_groups}}` — list, e.g. `engineering leadership; product; legal (if data breach); customer support`
- `{{compliance_constraints}}` — string, e.g. `PCI: notify within 24h; GDPR data breach: 72h`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Severity levels are formalized quantitatively (`> N users`, `> $X/h`), not "big/small"
- Each role (incident commander / comms / ops) has an explicit list of actions and an owner
- Communication timers are specified (ack within 5 min, first external message within 30 min, etc.)
- The "when a postmortem is mandatory" section is stated unambiguously, no "at team's discretion"
- Message templates (Slack / status page / email) are filled with real text, not placeholders

## Prompt

```text
Act as a Principal SRE with experience organising incident response in teams from 10 to 1000 engineers.

Prepare a practical incident response runbook for the service.

Context:
- Service: {{service_name}}
- Severity levels: {{severity_levels}}
- On-call setup: {{on_call_setup}}
- Paging tool: {{paging_tool}}
- Communication channels: {{communication_channels}}
- Stakeholder groups: {{stakeholder_groups}}
- Compliance constraints: {{compliance_constraints}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare a runbook with the following structure:

1. [required] Severity levels: formalized criteria for each level (quantitative).
2. [required] Detection and acknowledge:
   - what counts as detection
   - SLA for ack (e.g., 5 minutes)
   - what the first respondent does in the first 5 minutes
3. [required] Incident roles:
   - Incident Commander (what they do / what they DON'T do)
   - Communications Lead
   - Operations Lead / SME
   - Scribe (optional)
   - explicitly: one person can't combine commander + ops
4. [required] Triage flow:
   - first checks (dashboards, logs, recent deploys)
   - decision: mitigate vs investigate
   - when to escalate
5. [required] Mitigation playbook:
   - standard actions (rollback, traffic shift, feature flag)
   - decision tree (if X → do Y)
6. [required] Communication cadence:
   - first internal message: within 5 minutes
   - first external (status page): within 30 minutes for S1/S2
   - regular updates (every 30 min for S1, hourly for S2)
   - resolution message
7. [required] Message templates:
   - Slack first message
   - status page text
   - email to stakeholders (for S1)
   - fill with real text, not placeholders
8. [required] Recovery and handoff (what to do at shift change inside an incident)
9. [required] When a postmortem is mandatory: explicit criteria (S1 always; S2 if impact > N minutes; near-miss at commander's discretion)
10. [required] Compliance triggers: what to do additionally (data breach → notify within 72h per GDPR)

Don't do this:
- Severity "big / medium / small" — without quantitative criteria.
- Incident commander also doing ops ("I'll fight the fire and write the updates") — breaks focus.
- "Notify users" without a template and without a clear trigger.
- Postmortem "at the team's discretion" — the criterion must be unambiguous.
- Templates like "insert description here" — fill them in.

Response format:
- Ready-to-use Markdown runbook without theory padding
- At the end, a short checklist for commander "things I should remember in the first 10 minutes"
- A link to the postmortem prompt ([prompt-postmortem](./prompt-postmortem.md)) for follow-up
```
