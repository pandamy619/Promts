# Library standards

> [🇷🇺 Русский](../ru/standards.md) · 🇬🇧 **English**

[← Index](./README.md)

> Single set of rules that every prompt in this folder follows. When editing an existing prompt or adding a new one, reference this file.

Reference card: [template](./template.md)

---

## 1. Behavior when context is missing

Each prompt must have a unified rule for what the model does when there isn't enough context for a quality answer. Without it, behavior varies between prompts — sometimes the model asks questions, sometimes it silently invents.

Canonical block (insert verbatim, adjust the question count by complexity):

> If you don't have enough context for a quality answer, ask up to N short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Question count by prompt complexity:

- Simple (API docs, Docker, GraphQL) — up to 3
- Medium (REST API, K8s, CI/CD, WebSocket, load testing, API tests) — up to 5
- Complex (Architecture, Review, FastAPI template) — up to 7

## 2. Definition of done

Each prompt ends with a `Definition of done` section — criteria for when an answer is considered complete. Universal minimum that applies everywhere:

- All requested sections are present and not collapsed into a single line
- Commands, code, and configs — in fenced code blocks with language specified
- No vague phrases: "check it's OK", "configure as needed", "adapt to your case"
- Assumptions are separated from facts into an explicit `Assumptions` section
- If the prompt requests a table, diagram, or checklist — they are actually included, not replaced with prose
- If the response is cut off by length — the last produced section is still complete, not left hanging

On top of this, task-specific criteria. Examples:

- FastAPI template: "directory tree contains all required subfolders", "every key file is shown in full, no `same as above`"
- Runbook: "each step has either a concrete command or a success criterion"
- Architecture: "Mermaid container-level diagram is present", "risk table is filled in for every listed risk"

## 3. Length prioritization and splitting

Heavy prompts (≥ 6 sections in the output, or ≥ 5 files to produce) support two modes:

- `quick` — everything in one response. For drafts, prototypes, exploration.
- `staged` — plan → confirmation → step-by-step execution. For serious work where the cost of a mistake is high.

The `What to substitute` block adds `{{mode}}` with values `quick | staged`.

**For `quick` mode**, response sections are tagged:

- `[required]` — must not be skipped
- `[if room remains]` — may be shortened but not omitted

Mermaid diagrams, executive summary, and risk tables are always tagged `[required]`.

**For `staged` mode**, this block is added at the start of the prompt:

> First produce a short plan (3-7 items) of how you'll approach the task and in what order you'll deliver results. Wait for confirmation. Do not start execution until confirmed. After confirmation, execute one plan item at a time and wait for `next` or feedback each time.

Simple prompts (API docs, Docker, Runbook, GraphQL for a small domain) stay single-shot, no modes.

## 4. Genres

Each prompt belongs to one of four genres. The genre is set via a header tag and a column in `README.md`.

| Genre | Tag | When it fits |
| --- | --- | --- |
| Single-shot | `#single-shot` | Instruction → big response. Default for simple tasks. |
| Staged | `#staged` | Plan → confirmation → step-by-step execution. For heavy tasks where the first attempt is expensive to get wrong. |
| Critique | `#critique` | Draft → self-review → improve. For reviews, QA, audits. |
| Interview | `#interview` | The model asks questions, waits for answers, then acts. For tasks where context is objectively missing. |

One prompt may support two genres in a single card via `{{mode}}` — for example, Architecture: `staged` as the main mode + `quick` as fallback. In that case, both tags appear in the header. No file duplication needed.

## 5. Placeholders

- Names — English, snake_case: `{{service_name}}`, `{{auth_method}}`.
- Format — double curly braces: `{{...}}`
- In `What to substitute`, each placeholder includes type and example:
  - `{{service_name}}` — string, e.g. `payments-api`
  - `{{auth_method}}` — enum: `jwt | oauth2 | api_key | mtls`
  - `{{replica_count}}` — int, e.g. `3`
- If a placeholder doesn't actually affect the output — remove it. A silently ignored placeholder is worse than no placeholder.
- Technology assumptions that differ between teams (language version, ORM, linter, test framework) go into placeholders, not hardcoded into the prompt body.

## 6. Anti-examples

Where applicable (documentation, YAML, schemas, code generation), add a `Don't do this:` block with 1-2 lines of negative example. This works better than abstract prohibitions like "don't make RPC under the guise of REST".

Example format:

```
Don't do this:
- POST /users/getById — this is RPC under the guise of REST. Use GET /users/{id}.
- 200 OK with body {"error": "..."} — status and body must agree.
```

## 7. Source idea and delta

If the prompt is built on an external source:

- `**Source idea:** [Name](URL)`
- `**Delta from original:**` — what was added, removed, or reworked (1-2 lines). Without the delta, the link is declarative and doesn't answer "what's the value of *my* version".

Metrics like "`4,612 copies · 4.3/5`" from external sites are not preserved — that's marketing noise, not a useful signal for our context. Internal equivalent — `**Used:** N times, last use YYYY-MM-DD` — kept manually as needed.

## 8. Card metadata

Card header contains:

- `**Status:**` — one of:
  - `📋 Template` — skeleton, needs adaptation for the case
  - `🟢 Ready` — proven, can be used as-is
  - `🧪 Experimental` — built per the standard, but few real runs
- `**Owner:**` — who's accountable for keeping it current
- `**Last edit:**` — ISO date (YYYY-MM-DD)
- `**Version:**` — simple counter: `v1`, `v2`...

## 9. Language convention

- Card body and prose — English (this folder); Russian mirror lives in [/ru/](../ru/standards.md)
- Placeholder names and technical terms (`auth`, `idempotency`, `health check`, `staged`, `rollback`) — English
- Within a single prompt, style is consistent: either "authentication" or `auth`, but not both in adjacent lines

## 10. Checklist for a new prompt

Before committing a new or edited prompt, verify:

- [ ] Card header is filled per [template](./template.md)
- [ ] Topic and genre tags are set
- [ ] "Behavior when context is missing" block is present with question count
- [ ] `Definition of done` section is present with minimum + specific criteria
- [ ] Placeholders are English snake_case, each with type and example
- [ ] If the prompt is heavy — `{{mode}}` with values `quick | staged` is supported
- [ ] In `quick` mode, sections are tagged `[required]` / `[if room remains]`
- [ ] Where applicable — a "Don't do this:" block is added
- [ ] Source and delta from original are specified (if applicable)
- [ ] Card is added to `README.md` with the genre column
