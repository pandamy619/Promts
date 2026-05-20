# Changelog

All notable changes to this project are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). Versions use [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] — 2026-05-20

Initial public release. Bilingual library (Russian + English) of 20 standardized prompts for backend / platform engineering tasks.

### Added

- **Standards and reference** — `standards.md` defining the canonical card structure, `template.md` as a fill-in template.
- **20 prompts across 5 topic groups:**
  - **Architecture and design** (3): microservice architecture, architecture review, threat modeling.
  - **APIs and services** (6): FastAPI template, REST API, GraphQL schema, WebSocket, API Gateway, API documentation.
  - **Infrastructure and deploy** (5): Docker, Kubernetes, CI/CD, deployment runbook, supply chain security.
  - **Operations and incidents** (4): incident response, blameless postmortem, observability & SLO, data migration.
  - **Testing** (2): API tests, load testing.
- **Bilingual structure** — every file mirrored in `/ru/` and `/en/` with a one-click language switcher at the top.
- **Two prompts ship with mode switching** via `{{mode}}`:
  - `prompt-microservice-architecture` supports `quick | staged`.
  - `prompt-architecture-review` supports `quick | critique`.
- **Root-level files**: bilingual `README.md` as language picker, `LICENSE` (MIT), `CONTRIBUTING.md`, `.gitignore`, this `CHANGELOG.md`.

### Genres

- All 20 prompts available in `#single-shot` mode.
- 2 prompts additionally support `#staged` or `#critique` via the `{{mode}}` placeholder.

### Standards

Every prompt enforces:

- Unified metadata: Owner, Last edit (ISO date), Version, Source idea, Delta from original, Status.
- Canonical "behavior when context is missing" block (3 / 5 / 7 questions by complexity).
- `Definition of done` section: universal minimum + task-specific criteria.
- `Don't do this:` block with concrete anti-examples (where applicable).
- `[required]` / `[if room remains]` section tags for heavy prompts.

### Source attribution

Most prompts adapted from external templates (LogicBalls, DocsBot, Atlassian, Google SRE Workbook, PagerDuty, SLSA, Sigstore, OWASP, Microsoft STRIDE). Each card lists source + delta from original.

---

## Versioning notes

- This `CHANGELOG.md` tracks the library as a whole.
- Each individual prompt has its own `Version` field in its header (e.g., `v1`, `v2`), tracking changes to that specific card.
- A bump to the library's minor version (e.g., 1.0 → 1.1) usually means new prompts added or significant standards changes.
- A bump to the patch version (1.0.0 → 1.0.1) usually means edits within existing prompts.
