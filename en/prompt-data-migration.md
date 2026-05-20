# Prompt — Data migration / schema change

> [🇷🇺 Русский](../ru/prompt-data-migration.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #data-migration #database #schema #backend #single-shot
**Status:** `🧪 Experimental`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [Stripe — Online migrations at scale](https://stripe.com/blog/online-migrations), [GitHub — Online schema changes with gh-ost](https://github.blog/2016-08-01-gh-ost-github-s-online-migration-tool-for-mysql/)
**Delta from original:** mandatory expand-contract strategy for incompatible changes, explicit backward-compatibility window, verification queries shown concretely (not "verify"), rollback also as a script.

---

## When to use

- When you need to change the DB schema on a service under load
- When you're moving data between tables / DBs / formats
- When downtime is not allowed or strictly capped

## What to substitute

- `{{database_type}}` — enum: `postgres | mysql | mongodb | dynamodb | other`
- `{{table_or_collection}}` — string, e.g. `payments` (≈ 50M rows)
- `{{change_type}}` — enum: `add_column | drop_column | rename_column | change_type | split_table | merge_tables | add_index | backfill_data | denormalize | other`
- `{{data_volume}}` — string, e.g. `~50M rows, ~30 GB, growth 2M/month`
- `{{traffic_pattern}}` — string, e.g. `200 RPS read, 50 RPS write; peaks 9-11 and 18-22 UTC`
- `{{rollback_requirement}}` — enum: `must_be_reversible | reversible_within_24h | one_way`
- `{{downtime_tolerance}}` — string, e.g. `zero downtime` / `up to 5 minutes in the 02:00-04:00 UTC window`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- Strategy is named explicitly (`expand-contract`, `pt-online-schema-change`, `gh-ost`, `blue-green`)
- Specifies which application changes must be deployed BEFORE the schema, which AFTER
- Concrete SQL/commands per step, not "run the migration"
- Verification queries are shown with expected results
- Rollback plan is concrete commands, not "put everything back"
- A real downtime estimate is given (even if it's 0)

## Prompt

```text
Act as a Principal Database Engineer / DBA with experience running migrations on live production systems. Prepare a detailed migration plan for the schema change / data move.

Context:
- DB type: {{database_type}}
- Table / collection: {{table_or_collection}}
- Change type: {{change_type}}
- Data volume: {{data_volume}}
- Traffic profile: {{traffic_pattern}}
- Reversibility requirement: {{rollback_requirement}}
- Allowed downtime: {{downtime_tolerance}}

If you don't have enough context for a quality answer, ask up to 7 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare:

1. [required] Risk assessment:
   - what could break
   - which operations are blocking (e.g., ACCESS EXCLUSIVE lock on PG)
   - impact on replication, backups, replication lag
2. [required] Pre-migration checks:
   - current table and index size
   - active connections / long-running queries
   - free disk space (at least 2x table size for some operations)
   - backup verification
3. [required] Migration strategy (with explicit name):
   - expand-contract (for incompatible changes)
   - online schema change (pt-osc, gh-ost for MySQL; CONCURRENTLY for PG indexes)
   - blue-green / shadow table
   - batched backfill (for large data updates)
4. [required] Application changes ordering:
   - what to deploy BEFORE schema (dual-read / dual-write for expand)
   - what to deploy AFTER (cleanup, switch to new fields)
   - backward-compatibility window (how many weeks/deploys)
5. [required] Migration steps:
   - numbered steps with concrete SQL / command
   - which run manually, which in CI/CD
   - where a mandatory pause for verification belongs
6. [required] Verification queries:
   - count(*) before and after
   - sample comparison (old field vs new)
   - consistency check (sum, checksum, or explicit JOIN)
   - concrete SQL with expected results
7. [required] Cutover plan:
   - when we switch to the new field / table
   - how to back out if something goes wrong in the first N minutes
8. [required] Rollback plan:
   - concrete SQL/commands per step
   - point of no return (after which step rollback is expensive)
9. [required] Cleanup (after successful cutover):
   - drop old fields / tables
   - holding period: not earlier than N days after cutover
10. [required] Downtime estimate and monitoring plan

Don't do this:
- `ALTER TABLE huge_table ADD COLUMN ... NOT NULL DEFAULT ...` on large PG/MySQL tables without an online schema tool — that's a blocking operation.
- `DROP COLUMN` before the application has stopped reading it (need expand-contract).
- Migration without verification queries — later you can't prove data isn't corrupted.
- Rollback "if something goes wrong, redeploy the previous app version" — what about the data?
- `CREATE INDEX` without `CONCURRENTLY` on PG production.
- Backfill in a single transaction over millions of rows — replication lag, long-running transaction.

Response format:
- Risk assessment first
- Strategy and its justification
- Application ordering (what's before, what's after) — explicit table
- Numbered migration steps with SQL
- Verification queries — separate block
- Rollback plan — separate block
- Estimated timeline with buffers
```
