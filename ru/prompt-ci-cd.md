# Промт — CI-CD для сервиса

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-ci-cd.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #ci-cd #devops #pipeline #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — Production-Grade CI/CD Pipeline Architect](https://logicballs.com/ai-prompt/prompts/ci-cd-pipeline-generator), [PromptOptimizer — CI/CD Pipeline Prompt Templates](https://promptoptimizer.tools/prompts/development/ci-cd-pipeline)
**Дельта от оригинала:** добавлено разделение fast/extended CI, явные критерии rollback и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужно быстро спроектировать пайплайн под новый сервис
- Когда хочется стандартизировать PR-проверки, релизы и деплой
- Когда важно не забыть security scan, smoke, rollback и секреты

## Что подставить

- `{{project_type}}` — string, e.g. `python web service` / `node monorepo`
- `{{tech_stack}}` — string, e.g. `Python 3.13, FastAPI, Postgres, Docker`
- `{{repo_platform}}` — enum: `github_actions | gitlab_ci | jenkins | circleci | buildkite`
- `{{deployment_target}}` — enum: `kubernetes | ecs | cloud_run | bare_metal | serverless`
- `{{cloud_provider}}` — enum: `aws | gcp | azure | yandex_cloud | on_premise`
- `{{trigger_strategy}}` — string, e.g. `PR → fast CI; main → full CI + deploy stage; tag → deploy prod`
- `{{testing_requirements}}` — list, e.g. `unit; integration with postgres; smoke after deploy`
- `{{security_level}}` — enum: `basic | standard | strict`
- `{{environment_strategy}}` — string, e.g. `dev (auto) → stage (auto on main) → prod (manual approval)`
- `{{rollback_strategy}}` — enum: `kubectl_rollout_undo | blue_green | canary | manual_redeploy`
- `{{optimization_priority}}` — enum: `speed | cost | security | reliability`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Конфиг для выбранной платформы приведён целиком, copy-paste ready
- Разделение fast CI (≤ 5 минут) и extended CI явно проработано
- Критерии rollback сформулированы количественно (`error rate > X%`, `p95 > Y ms`), не «если что-то пошло не так»
- Секреты передаются через secret manager, а не зашиты в YAML
- Раздел «Риск / Что сломается / Как защититься» содержит минимум 5 рисков

## Промт

```text
Выступай как Principal DevOps Engineer. Спроектируй production-ready CI/CD pipeline для сервиса.

Контекст:
- Тип проекта: {{project_type}}
- Технологический стек: {{tech_stack}}
- Репозиторий / CI-платформа: {{repo_platform}}
- Target деплоя: {{deployment_target}}
- Cloud provider: {{cloud_provider}}
- Стратегия триггеров: {{trigger_strategy}}
- Требования к тестам: {{testing_requirements}}
- Уровень security scanning: {{security_level}}
- Стратегия окружений: {{environment_strategy}}
- Стратегия отката: {{rollback_strategy}}
- Приоритет оптимизации: {{optimization_priority}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Сгенерируй пайплайн так, чтобы он покрывал:
1. [обязательно] Проверки в pull/merge request
2. [обязательно] Сборку артефактов или контейнеров
3. [обязательно] Security checks
4. [обязательно] Деплой по окружениям
5. [обязательно] Smoke / health verification после выката
6. [обязательно] Rollback-путь

В ответе дай:
1. [обязательно] Архитектуру пайплайна по стадиям
2. [обязательно] Полный конфиг для выбранной платформы
3. [обязательно] Разделение на fast CI и extended CI
4. [если останется место] Стратегию кэширования и ускорения сборок
5. [обязательно] Секреты и управление credentials
6. [обязательно] Security и compliance меры:
   - dependency scanning
   - container scanning
   - secrets detection
   - migration safety checks
7. [обязательно] Deployment verification:
   - smoke tests
   - health checks
   - rollback trigger criteria
8. [обязательно] Чеклист внедрения
9. [обязательно] Таблицу "Риск / Что сломается / Как защититься"

Не делай так:
- Один stage делает `build && test && deploy` без разделения.
- Секреты в YAML или в `.env`, попадающий в репозиторий.
- Rollback описан как «откатить вручную» без конкретных команд.
- Тесты, идущие после деплоя в prod, без auto-rollback при провале.

Ограничения:
- Не предлагай overengineering без причины
- Делай zero-downtime подход там, где это реально уместно
- YAML/конфиг должен быть copy-paste ready

Формат ответа:
- Сначала краткое summary
- Затем схема pipeline stages
- Затем полный конфиг
- Затем пояснения и checklist
```
