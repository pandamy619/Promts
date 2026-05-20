# Prompt — Architecture review

> [🇷🇺 Русский](../ru/prompt-architecture-review.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #architecture-review #microservice #risk #backend #single-shot #critique
**Status:** `🟢 Ready`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v2
**Source idea:** [LogicBalls — Architectural Design Review Feedback Generator](https://logicballs.com/ai-prompt/prompts/architectural-design-review-feedback-generator)
**Delta from original:** added explicit severity and owner requirements per finding, scale-threshold linkage, and the canonical "behavior when context is missing" block. **v2:** added `critique` mode via `{{mode}}` — in one pass the model produces a draft, critically analyzes it itself, then produces a refined version. `quick` mode (old behavior) is preserved.

---

## When to use

- When a service design exists and needs a critical look
- When you need a peer review before implementation or before an architecture committee
- When you want not just criticism but prioritized remediation steps

**When to pick `quick`:** you need a fast single-pass review — e.g., to quickly see if there are red flags, or to bring to the team as "here's what I would ask".

**When to pick `critique`:** the review goes to a committee / on the record / before a build decision. The model first produces a draft, then catches its own weak spots (where it was too harsh, too lenient, what it missed), then writes the refined version. Costs roughly twice as many tokens but quality improves noticeably.

## What to substitute

- `{{mode}}` — enum: `quick | critique`
- `{{architecture_description}}` — string or link, e.g. `design doc text or Confluence link`
- `{{business_context}}` — string, e.g. `b2c payment processing, Black Friday x10 peak`
- `{{tech_stack}}` — string, e.g. `Go 1.22, Postgres 16, Redis, Kafka`
- `{{scale_requirements}}` — string, e.g. `500 RPS baseline, 5k RPS peak; p99 < 500ms`
- `{{team_context}}` — string, e.g. `5 mid backend, 1 senior, no SRE`
- `{{specific_concerns}}` — list, e.g. `data consistency on refund; idempotency on retry`
- `{{compliance_needs}}` — string, e.g. `PCI DSS SAQ-D; GDPR; data must stay in EU`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Each finding has severity (`critical | high | medium`) and a proposed owner (`team | role`)
- Where a problem depends on scale — the threshold is named ("manifests at RPS > X")
- The verdict is explicitly one of `approve | approve with major changes | request redesign`
- The top-3 risks are a separate prioritization, not a paraphrase of findings

In `critique` mode additionally:

- Self-review contains at least 4 explicit questions about its own draft (what was missed, where it was harsh, where soft, are severities defensible)
- The refined version differs from the draft — at least 2 substantive changes (new finding, severity reassessment, reformulated action item)
- At the end of the refined version, a brief `Changes relative to draft` section explains what changed and why

## Prompt

```text
Act as a Principal Software Architect conducting a formal architecture review for a critical service.

Execution mode: {{mode}}

Context:
- Architecture description: {{architecture_description}}
- Business context: {{business_context}}
- Tech stack: {{tech_stack}}
- Scale requirements: {{scale_requirements}}
- Team context: {{team_context}}
- Specific risk areas / concerns: {{specific_concerns}}
- Compliance / regulatory needs: {{compliance_needs}}

If you don't have enough context for a quality answer, ask up to 7 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Conduct review along these dimensions:
1. Domain boundaries and cohesion
2. Data ownership and consistency model
3. API / event contracts
4. Scalability bottlenecks
5. Reliability / fault tolerance
6. Security / privacy
7. Operability / observability
8. Team fit and operational complexity

────────────────────────────────────────────────────────────────
### If {{mode}} = quick

Deliver the review in a single pass. Response structure below (section "Review structure").

────────────────────────────────────────────────────────────────
### If {{mode}} = critique

Deliver the response in three explicitly labeled phases. Don't skip any, don't change the order.

## Phase 1 — Draft

Produce a full review per the structure below (section "Review structure"). Don't edit it as you go — this is the draft.

## Phase 2 — Self-review

Critically check your draft. Answer these 4 questions in detail (not one sentence each):

1. **What did I miss?** List 3-5 likely blind spots — areas you covered shallowly or didn't cover at all. Be specific: "didn't check what happens when Kafka consumers are slow", not "scalability could be deeper".
2. **Where was I too harsh?** Which severities did you overstate, which findings are blown up, where did you rewrite the architecture instead of criticizing a specific flaw. Give 1-3 examples.
3. **Where was I too soft?** What did you gloss over, where is severity understated, where did you write "perhaps worth considering" instead of taking a clear position. 1-3 examples.
4. **Are the severities defensible?** Take 3 findings from the draft. For each, ask yourself: if the design author objects "this isn't critical because X" — do you have an answer? If not — reassess the severity.

After the 4 questions, briefly (3-5 bullet points) state: "what I'm changing in the refined version".

## Phase 3 — Refined review

Rewrite the review in full, in the same structure as the draft, incorporating the self-review. At least 2 substantive differences from the draft (new finding, severity reassessment, reformulated action item, changed verdict).

At the end, add a `Changes relative to draft` section:
- finding X added — basis: …
- severity Y raised from medium to high — basis: …
- action item Z reformulated — basis: …

────────────────────────────────────────────────────────────────

## Review structure

1. [required] Executive summary
2. [required] Overall architecture maturity score 1-10
3. [required] Top-3 critical risks (separate prioritization, not a copy of findings)
4. [required] Findings list by severity:
   - critical
   - high
   - medium
5. [required] For each finding:
   - what the problem is
   - why it's dangerous
   - a realistic fix
   - proposed owner (team / role)
   - threshold at which it manifests (if scale-dependent)
6. [required] Verdict:
   - approve
   - approve with major changes
   - request redesign

Don't do this:
- Finding without severity and without owner.
- "Rewrite everything" as a recommendation without justification.
- Paraphrase the architecture description instead of criticizing it.
- Top-3 risks that duplicates findings — it should be separate prioritization.
- In critique mode: self-review in a single sentence "everything's fine, nothing to change" — that's not a self-review, that's a refusal to do the work.
- In critique mode: a refined version identical to the draft — means the self-review was fictional.
- In critique mode: skip phase 2 and jump straight to refined.

Requirements:
- Be critical but pragmatic
- Don't advise "rewrite everything" without need
- If a problem depends on scale, state the threshold at which it manifests
- Clearly separate facts from assumptions

Response format:
- For quick: findings → open questions / assumptions → short summary
- For critique: Phase 1 (draft) → Phase 2 (self-review) → Phase 3 (refined) → Changes relative to draft
```
