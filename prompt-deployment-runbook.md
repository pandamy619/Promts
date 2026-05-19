# Промт — Runbook деплоя сервиса

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #runbook #deploy #ops #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [Appaca — Deployment Runbook](https://www.appaca.ai/resources/prompt-library/software/deployment-runbook), [SurePrompts — Operations Runbook](https://sureprompts.com/prompts/technical/runbook-template)
**Дельта от оригинала:** добавлены явные критерии rollback (количественные), шаблон сообщения стейкхолдерам, чек-лист для post-deploy retro и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда надо быстро оформить пошаговую инструкцию по релизу
- Когда хочется уменьшить ручные ошибки в деплое
- Когда нужен шаблон под on-call, rollback и коммуникацию со стейкхолдерами

## Что подставить

- `{{service_name}}` — string, e.g. `payments-api`
- `{{environment}}` — enum: `dev | stage | prod`
- `{{tech_stack}}` — string, e.g. `FastAPI / Postgres / Kubernetes`
- `{{deployment_tool}}` — enum: `helm | argocd | kubectl | terraform | custom`
- `{{owners}}` — string, e.g. `@team-payments; on-call @vladtagaev`
- `{{health_endpoints}}` — list, e.g. `GET /healthz; GET /readyz`
- `{{rollback_method}}` — enum: `kubectl_rollout_undo | helm_rollback | redeploy_previous_image | blue_green_switch`
- `{{stakeholders}}` — list, e.g. `#payments-releases; product-owner; on-call SRE`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- У каждого шага есть либо конкретная команда, либо явный критерий успеха
- Критерии rollback количественные (`error rate > X%`, `p95 > Y ms`, `health check fail > N подряд`)
- Шаблон сообщения для команды/бизнеса заполнен реальным текстом, а не «вставить сюда»
- Раздел «Частые ошибки» содержит минимум 3 конкретных случая с действием

## Промт

```text
Выступай как senior SRE / DevOps engineer. Напиши практический deployment runbook для релиза сервиса.

Контекст:
- Сервис: {{service_name}}
- Окружение: {{environment}}
- Стек: {{tech_stack}}
- Инструмент деплоя: {{deployment_tool}}
- Ответственные: {{owners}}
- Health endpoints / smoke checks: {{health_endpoints}}
- Способ отката: {{rollback_method}}
- Стейкхолдеры для уведомления: {{stakeholders}}

Если для качественного ответа не хватает контекста — задай до 3 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Сделай runbook в таком виде, чтобы его мог выполнить инженер, который не держит весь контекст в голове.

Структура runbook:
1. [обязательно] Цель и когда применять
2. [обязательно] Preconditions / доступы / роли
3. [обязательно] Pre-deploy checklist
4. [обязательно] Пошаговый деплой с командами
5. [обязательно] Проверка успешности:
   - health checks
   - smoke tests
   - логи / метрики / алерты
6. [обязательно] Явные критерии rollback (количественные)
7. [обязательно] Пошаговый rollback
8. [обязательно] Post-deploy monitoring window
9. [обязательно] Шаблон сообщения для команды / бизнеса
10. [обязательно] Частые ошибки и что делать

Не делай так:
- Шаг «убедись, что всё ок» без конкретного критерия.
- Rollback описан как «откатить», без конкретных команд.
- Pre-deploy checklist без owner-ов у пунктов («кто проверил миграцию?»).
- Шаблон сообщения вида «{{insert here}}» — заполни реальным текстом.

Требования:
- Используй нумерованные шаги
- Команды помещай в code blocks
- Если действие опасное, явно помечай это
- Где есть decision point, добавь "если X -> делай Y"
- Не оставляй vague формулировок вроде "проверь, что всё ок"
- Для проверок укажи, что именно считается успешным результатом

Формат ответа:
- Готовый Markdown runbook без лишней теории
- В конце короткий checklist для postmortem / retro улучшений runbook
```
