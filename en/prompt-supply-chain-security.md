# Prompt — Supply chain security (SBOM, signing)

> [🇷🇺 Русский](../ru/prompt-supply-chain-security.md) · 🇬🇧 **English**

[← Index](./README.md) · [Standards](./standards.md) · [Template](./template.md)

tags: #prompt #supply-chain #security #sbom #devops #single-shot
**Status:** `🧪 Experimental`
**Owner:** Pandamy619
**Last edit:** 2026-05-20
**Version:** v1
**Source idea:** [SLSA Framework](https://slsa.dev/), [Sigstore — Open source software signing](https://www.sigstore.dev/), [NIST SP 800-218 (SSDF)](https://csrc.nist.gov/publications/detail/sp/800-218/final)
**Delta from original:** ties target SLSA level, explicit requirement to sign artifacts in ALL environments (not only prod), separate workflow for vulnerability triage.

---

## When to use

- When a service is going to production and you need to know where you stand on dependencies and build integrity
- When preparing for a compliance audit (SOC 2, ISO 27001, FedRAMP)
- After an incident like the `xz utils backdoor` or `event-stream` — when it becomes clear that dependencies can't be trusted blindly

## What to substitute

- `{{project_type}}` — string, e.g. `python web service (FastAPI)` / `node monorepo (8 packages)`
- `{{package_managers}}` — list, e.g. `poetry; npm; apt`
- `{{registry}}` — string, e.g. `pypi public; npm public; private GitHub Packages for internal`
- `{{build_system}}` — string, e.g. `GitHub Actions; Docker buildx multi-arch`
- `{{deployment_target}}` — enum: `kubernetes | ecs | cloud_run | bare_metal | serverless`
- `{{compliance_requirements}}` — string, e.g. `SOC 2 Type II; SLSA level 3 target; FedRAMP Moderate`

## Definition of done

Universal minimum — see [Definition of done](./standards.md#2-definition-of-done).

Specific criteria:

- SBOM is generated in CI on every build and published as an artifact
- Target SLSA level (1/2/3/4) is specified with justification
- All artifacts are signed (cosign / sigstore), and signature is verified on deploy
- Lock files under automated control (dependabot / renovate / pip-tools)
- Vulnerability triage workflow is described with SLA by severity

## Prompt

```text
Act as a Principal Security Engineer / Platform Engineer with supply chain security experience. Prepare a practical supply chain security plan for the project.

Context:
- Project type: {{project_type}}
- Package managers: {{package_managers}}
- Registry (where we pull from, where we push): {{registry}}
- Build system: {{build_system}}
- Deployment target: {{deployment_target}}
- Compliance requirements: {{compliance_requirements}}

If you don't have enough context for a quality answer, ask up to 5 short clarifying questions in a single message. If there are no critical gaps, proceed with reasonable defaults and lead the response with an `Assumptions` section listing what you assumed.

Prepare:

1. [required] SBOM (Software Bill of Materials):
   - format (SPDX / CycloneDX)
   - tooling (syft, cdxgen, language-specific)
   - what's included (direct + transitive deps; OS packages in container)
   - where stored and how versioned
2. [required] Dependency management:
   - lock files (poetry.lock, package-lock.json) under Git
   - update automation (dependabot / renovate)
   - policy for major bumps (review required)
   - SLA for critical updates (e.g., security patch < 7 days)
3. [required] Vulnerability scanning:
   - tooling: deps (Snyk, Trivy, OSV-scanner), container images (Trivy, Grype), code (Semgrep, CodeQL)
   - where it runs (PR, main branch, scheduled)
   - SLA by severity:
     - critical: 24h
     - high: 7 days
     - medium: 30 days
     - low: best effort
   - triage workflow: false positive / accepted / fix
4. [required] Build integrity (SLSA):
   - target SLSA level (1/2/3/4) with justification
   - hermetic builds (no network calls during build)
   - provenance attestation (in-toto, SLSA provenance)
   - reproducible builds (where applicable)
5. [required] Signing artifacts:
   - tooling (cosign / sigstore / GPG)
   - what to sign: container images, SBOM, provenance
   - where signatures are stored (rekor transparency log for sigstore)
   - signature verification: at deploy time (admission controller for k8s)
   - sign in ALL environments (dev/stage/prod) — otherwise the artifact in stage and prod differs, losing integrity guarantees
6. [required] Registry security:
   - pull policies: allowlisted registries only
   - image pinning by digest (`@sha256:...`), not by tag
   - mirror / pull-through cache for public registries (defense against an incident where a public package suddenly disappears)
   - internal registry: scan on push, immutable tags
7. [required] Supply chain incident response:
   - what to do when a CVE is announced in a dependency (Log4Shell-style)
   - how quickly we find all usage instances
   - rollback / patch procedure
   - communication to stakeholders

Don't do this:
- `image: foo:latest` — non-reproducible.
- Pulling from public registries without a mirror — dependency confusion / sudden disappearance.
- Sign only in prod — artifact in stage and prod ends up different, losing integrity gains.
- SBOM as a file in the repo without actual use — that's a paperwork artifact for audit, not a security control.
- Lock file under control but updates done manually — security patches lag for months.
- Vulnerability scan only in CI on PR — but dependencies change in the registry between releases.

Response format:
- Executive summary with target SLSA level
- Table "Control | Tool | Where it runs | SLA / cadence"
- Concrete YAML / config for CI (GitHub Actions / GitLab CI)
- Vulnerability triage runbook
- Checklist "ready for compliance audit"
```
