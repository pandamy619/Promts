# Промт — Supply chain security (SBOM, signing)

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-supply-chain-security.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #supply-chain #security #sbom #devops #single-shot
**Статус:** `🧪 Experimental`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [SLSA Framework](https://slsa.dev/), [Sigstore — Open source software signing](https://www.sigstore.dev/), [NIST SP 800-218 (SSDF)](https://csrc.nist.gov/publications/detail/sp/800-218/final)
**Дельта от оригинала:** привязка к целевому SLSA level, явное требование signing artifacts во ВСЕХ окружениях (не только prod), отдельный workflow для vulnerability triage.

---

## Когда использовать

- Когда сервис идёт на production и нужно понять, что у нас с зависимостями и build integrity
- При подготовке к compliance аудиту (SOC 2, ISO 27001, FedRAMP)
- После инцидента типа `xz utils backdoor` или `event-stream` — когда становится понятно, что зависимостям доверять нельзя

## Что подставить

- `{{project_type}}` — string, e.g. `python web service (FastAPI)` / `node monorepo (8 пакетов)`
- `{{package_managers}}` — list, e.g. `poetry; npm; apt`
- `{{registry}}` — string, e.g. `pypi public; npm public; private GitHub Packages для internal`
- `{{build_system}}` — string, e.g. `GitHub Actions; Docker buildx multi-arch`
- `{{deployment_target}}` — enum: `kubernetes | ecs | cloud_run | bare_metal | serverless`
- `{{compliance_requirements}}` — string, e.g. `SOC 2 Type II; SLSA level 3 целевой; FedRAMP Moderate`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- SBOM генерируется в CI на каждый билд и публикуется как артефакт
- Указан целевой SLSA level (1/2/3/4) с обоснованием
- Все артефакты подписаны (cosign / sigstore), подпись проверяется при deploy
- Lock files под автоматизированным control (dependabot / renovate / pip-tools)
- Vulnerability triage workflow описан с SLA по severity

## Промт

```text
Выступай как Principal Security Engineer / Platform Engineer с опытом supply chain security. Подготовь практический план supply chain security для проекта.

Контекст:
- Тип проекта: {{project_type}}
- Менеджеры пакетов: {{package_managers}}
- Registry (откуда тянем, куда пушим): {{registry}}
- Build system: {{build_system}}
- Target деплоя: {{deployment_target}}
- Compliance требования: {{compliance_requirements}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь:

1. [обязательно] SBOM (Software Bill of Materials):
   - какой формат (SPDX / CycloneDX)
   - tooling (syft, cdxgen, language-specific)
   - что включает (direct + transitive deps; OS пакеты в контейнере)
   - где хранится и как версионируется
2. [обязательно] Dependency management:
   - lock files (poetry.lock, package-lock.json) под Git
   - автоматизация обновлений (dependabot / renovate)
   - политика для major bumps (review required)
   - SLA на критические обновления (например, security patch < 7 дней)
3. [обязательно] Vulnerability scanning:
   - tooling: deps (Snyk, Trivy, OSV-scanner), container images (Trivy, Grype), code (Semgrep, CodeQL)
   - где запускается (PR, main branch, scheduled)
   - SLA по severity:
     - critical: 24h
     - high: 7 days
     - medium: 30 days
     - low: best effort
   - triage workflow: false positive / accepted / fix
4. [обязательно] Build integrity (SLSA):
   - целевой SLSA level (1/2/3/4) с обоснованием
   - hermetic builds (нет network calls во время build)
   - provenance attestation (in-toto, SLSA provenance)
   - reproducible builds (где применимо)
5. [обязательно] Signing artifacts:
   - tooling (cosign / sigstore / GPG)
   - что подписываем: container images, SBOM, provenance
   - где хранятся подписи (rekor transparency log для sigstore)
   - проверка подписи: на deploy (admission controller для k8s)
   - подпись во ВСЕХ окружениях (dev/stage/prod) — иначе разный артефакт
6. [обязательно] Registry security:
   - pull policies: только из allowlisted registries
   - image pinning по digest (`@sha256:...`), не по тегу
   - mirror / pull-through cache для публичных registries (защита от такого инцидента, когда публичный пакет внезапно исчезает)
   - internal registry: scan на push, immutable tags
7. [обязательно] Incident response для supply chain:
   - что делаем при объявлении CVE в зависимости (как у Log4Shell)
   - как быстро находим все instances использования
   - rollback / patch procedure
   - communication к stakeholders

Не делай так:
- `image: foo:latest` — невоспроизводимо.
- Pulling из публичных registries без mirror — атака типа dependency confusion / sudden disappearance.
- Подпись только в prod — артефакт в stage и prod становится разным, теряем integrity gain.
- SBOM как файл в репо без actual usage — это документ для аудита, а не security control.
- Lock file под контролем, но обновления вручную — security patches задерживаются на месяцы.
- Vulnerability scan только в CI на PR — а зависимости меняются в registry между релизами.

Формат ответа:
- Executive summary с целевым SLSA level
- Таблица «Control | Tool | Where it runs | SLA / cadence»
- Конкретный YAML / конфиг для CI (GitHub Actions / GitLab CI)
- Vulnerability triage runbook
- Чек-лист «готовности к compliance аудиту»
```
