# Prompt — <task name>

> [🇷🇺 Русский](../ru/template.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md)

tags: #prompt #<topic> #<genre>
**Status:** `📋 Template`
**Owner:** <who's accountable>
**Last edit:** YYYY-MM-DD
**Version:** v1
**Source idea:** [<Source name>](<URL>)
**Delta from original:** <1-2 lines: what was added / removed / reworked>

---

## When to use

- <Case 1>
- <Case 2>
- <Case 3>

## What to substitute

- `{{placeholder_1}}` — string, e.g. `example-value`
- `{{placeholder_2}}` — enum: `option_a | option_b | option_c`
- `{{mode}}` — enum: `quick | staged` <!-- remove if the prompt is single-shot -->

## Definition of done

Universal minimum:

- All requested sections present, not collapsed into a single line
- Commands, code, configs — in fenced code blocks with language
- No vague phrases ("check it's OK", "configure as needed")
- Assumptions are separated from facts into an `Assumptions` section
- Tables / diagrams / checklists — actually included, not replaced with prose

Specific to this prompt:

- <Task-specific criterion 1>
- <Task-specific criterion 2>

## Prompt

```text
Act as <persona: role + experience + context>.

<One-two lines: what exactly the model is asked for and why>.

Context:
- <Param>: {{placeholder_1}}
- <Param>: {{placeholder_2}}
- Mode: {{mode}}  // remove block if single-shot

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

### If {{mode}} = staged
First produce a short plan (3-7 items) of how you'll approach the task and in what order you'll deliver results. Wait for confirmation. Do not start execution until confirmed. After confirmation, execute one plan item at a time and wait for `next` or feedback each time.

### If {{mode}} = quick (or single-shot prompt)
Deliver the response in full. Sections:
1. [required] <Section 1>
2. [required] <Section 2>
3. [if room remains] <Section 3>
4. [required] <Mermaid / risk table / executive summary — if any>

Requirements:
- <Specific requirement 1>
- <Specific requirement 2>

Don't do this:
- <Concrete anti-example 1>
- <Concrete anti-example 2>

Response format:
- <Structure: summary, table, code, checklist — in what order>
```

## Known limitations

- <What this prompt does poorly>
- <When to use a different prompt from the library instead>
