# Prompt — Threat modeling

> [🇷🇺 Русский](../ru/prompt-threat-modeling.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #threat-modeling #security #review #single-shot
**Status:** `🧪 Experimental`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [Microsoft — STRIDE Threat Modeling](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats), [OWASP — Threat Modeling Process](https://owasp.org/www-community/Threat_Modeling_Process)
**Delta from original:** mandatory DFD (or explicit list of trust boundaries) before STRIDE analysis, required likelihood × impact matrix, explicit residual risks section. Candidate to be rewritten as `#critique` genre (see backlog).

---

## When to use

- Before launching a new service or making a substantial change to an existing one
- Before a security review or compliance audit
- When the team is unsure which threats are real and which are theoretical

## What to substitute

- `{{system_description}}` — string, e.g. `payments-api receives requests from mobile/web, validates, sends to Stripe, persists to Postgres, emits event to Kafka`
- `{{data_classification}}` — string, e.g. `PCI DSS (PAN, CVV); GDPR (PII); secrets (API keys); internal (logs)`
- `{{trust_boundaries}}` — list, e.g. `internet → API gateway; gateway → service; service → DB; service → Stripe; service → Kafka`
- `{{entry_points}}` — list, e.g. `POST /v1/payments (public, authed); GET /v1/admin/payments (internal, JWT scope admin); webhook from Stripe (public, signature-verified)`
- `{{assets_at_risk}}` — list, e.g. `customer payment data; transaction history; API keys; user PII`
- `{{compliance_context}}` — string, e.g. `PCI DSS SAQ-D; GDPR; SOC 2 Type II`
- `{{attacker_profile}}` — string, e.g. `internet attacker (low skill); insider (employee); compromised dependency; nation-state (out of scope)`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- DFD is shown (as text or Mermaid), with trust boundaries explicitly marked
- STRIDE is worked through for each trust boundary, not "for the system as a whole"
- Each risk has likelihood (Low/Med/High), impact (Low/Med/High), and mitigation (existing / proposed / accepted)
- Residual risks are listed — what's NOT closed and why
- For each mitigation, it's specified how to verify it works

## Prompt

```text
Act as a Principal Security Engineer with threat-modeling experience in fintech / health / e-commerce. Conduct threat modeling for the service using the STRIDE methodology.

Context:
- System description: {{system_description}}
- Data classification: {{data_classification}}
- Trust boundaries: {{trust_boundaries}}
- Entry points: {{entry_points}}
- Assets at risk: {{assets_at_risk}}
- Compliance context: {{compliance_context}}
- Attacker profile: {{attacker_profile}}

If you don't have enough context for a quality answer, ask up to 7 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare:

1. [required] System decomposition:
   - DFD (Mermaid or text): components, data flows, trust boundaries
   - explicitly marked crossings of trust boundaries (where data crosses the trust line)
2. [required] STRIDE analysis for EACH trust boundary:
   - **S**poofing (impersonate someone)
   - **T**ampering (data altered in transit or at rest)
   - **R**epudiation (denying an action)
   - **I**nformation disclosure (data leakage)
   - **D**enial of service
   - **E**levation of privilege
   For each: concrete threat (not "DoS is possible", but "attacker overloads /v1/payments via brute force")
3. [required] Risk matrix:
   - table: Threat | Likelihood (L/M/H) | Impact (L/M/H) | Risk score | Status
   - status: existing_mitigation / proposed_mitigation / accepted_risk
4. [required] Mitigations:
   - for every threat at medium priority (L+H, M+M, H+L) and above:
     - what's already implemented
     - what's proposed to add
     - how to verify (test, audit, monitoring)
5. [required] Compliance mapping:
   - which mitigations cover which compliance requirements
   - where the gaps are relative to requirements
6. [required] Residual risks:
   - what's NOT closed and why
   - who owns the accepted risk (sign-off needed)
7. [required] Action items:
   - table: what | owner | severity (P0-P2) | due date
   - not "improve security", but "add rate limiting on /v1/payments — 100 req/min per IP — owner platform, P1, due 2026-06-15"

Don't do this:
- STRIDE "for the system as a whole" — it works by boundaries, not the whole system at once.
- "DoS is possible" — without a concrete attacker action.
- Mitigation without verification — how do you know it works in prod?
- Threat without classification of the data it threatens.
- All risks as "high" — without prioritization that's useless.
- Ignore insider threat unless it's explicitly "out of scope".

Response format:
- DFD (Mermaid) first
- Table "Trust boundary | Threats (STRIDE) | Likelihood | Impact | Mitigation | Verification"
- Compliance gap mapping separately
- Residual risks separately
- Action items as a table
```
