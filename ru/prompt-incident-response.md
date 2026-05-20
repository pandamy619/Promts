# Промт — Incident response runbook

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-incident-response.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #incident-response #sre #ops #service #single-shot
**Статус:** `🧪 Experimental`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [PagerDuty Incident Response](https://response.pagerduty.com/), [Atlassian Incident Handbook](https://www.atlassian.com/incident-management/handbook)
**Дельта от оригинала:** жёстко разнесены роли (commander / comms / ops), формализованы severity levels с количественными критериями, добавлены таймеры коммуникации и обязательная связь с постмортем-промтом.

---

## Когда использовать

- Когда команде нужен пошаговый incident-response плейбук на момент срабатывания алерта
- Когда хочется унифицировать роли, коммуникации и эскалации между сервисами
- Когда инциденты случаются, но action нет — каждый раз разбираются заново

## Что подставить

- `{{service_name}}` — string, e.g. `payments-api`
- `{{severity_levels}}` — string, e.g. `S1: revenue impact > $10k/h или > 50% users; S2: partial impact; S3: cosmetic`
- `{{on_call_setup}}` — string, e.g. `primary on-call 24/7, secondary бизнес-часы; PagerDuty rotation еженедельно`
- `{{paging_tool}}` — enum: `pagerduty | opsgenie | grafana_oncall | victorops | manual_slack`
- `{{communication_channels}}` — string, e.g. `Slack #incidents-payments; статус-страница status.example.com; email для S1+`
- `{{stakeholder_groups}}` — list, e.g. `engineering leadership; product; legal (если data breach); customer support`
- `{{compliance_constraints}}` — string, e.g. `PCI: notify в 24h; GDPR data breach: 72h`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Severity levels формализованы количественно (`> N users`, `> $X/h`), не «большой/маленький»
- Каждая роль (incident commander / comms / ops) имеет явный список действий и owner
- Таймеры коммуникации указаны (ack за 5 мин, первое внешнее сообщение за 30 мин и т.п.)
- Раздел «когда обязателен постмортем» сформулирован однозначно, без «по решению команды»
- Шаблоны сообщений (Slack / status page / email) заполнены реальным текстом, а не плейсхолдерами

## Промт

```text
Выступай как Principal SRE с опытом organising incident response в командах от 10 до 1000 инженеров.

Подготовь практический incident response runbook для сервиса.

Контекст:
- Сервис: {{service_name}}
- Severity levels: {{severity_levels}}
- Setup on-call: {{on_call_setup}}
- Инструмент paging: {{paging_tool}}
- Каналы коммуникации: {{communication_channels}}
- Группы стейкхолдеров: {{stakeholder_groups}}
- Compliance ограничения: {{compliance_constraints}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь runbook со следующей структурой:

1. [обязательно] Severity levels: формализованные критерии каждого уровня (количественные).
2. [обязательно] Detection и acknowledge:
   - что считается обнаружением
   - SLA на ack (например, 5 минут)
   - что делает первый respondent в первые 5 минут
3. [обязательно] Роли в инциденте:
   - Incident Commander (что делает / чего НЕ делает)
   - Communications Lead
   - Operations Lead / SME
   - Scribe (опционально)
   - явно: один человек не может совмещать commander + ops
4. [обязательно] Triage flow:
   - первые проверки (dashboards, logs, recent deploys)
   - решение: mitigate vs investigate
   - когда эскалировать
5. [обязательно] Mitigation playbook:
   - стандартные действия (rollback, traffic shift, feature flag)
   - decision tree (если X → делай Y)
6. [обязательно] Communication cadence:
   - первое внутреннее сообщение: за 5 минут
   - первое внешнее (status page): за 30 минут для S1/S2
   - регулярные апдейты (каждые 30 мин для S1, каждый час для S2)
   - сообщение о resolution
7. [обязательно] Шаблоны сообщений:
   - Slack первое сообщение
   - status page текст
   - email для стейкхолдеров (для S1)
   - заполни реальным текстом, не плейсхолдерами
8. [обязательно] Recovery и handoff (что делать на смене дежурства внутри инцидента)
9. [обязательно] Когда обязателен постмортем: явные критерии (S1 всегда; S2 если impact > N минут; near-miss по решению commander)
10. [обязательно] Compliance triggers: что нужно делать дополнительно (data breach → notify в 72h по GDPR)

Не делай так:
- Severity «большой / средний / маленький» — без количественных критериев.
- Incident commander совмещает с ops («сам тушу, сам пишу») — нарушается фокус.
- «Сообщить пользователям» без шаблона и без чёткого триггера.
- Постмортем «по желанию команды» — критерий должен быть однозначным.
- Шаблоны вида «вставить сюда описание» — заполни.

Формат ответа:
- Готовый Markdown runbook без лишней теории
- В конце короткий чек-лист для commander «что я должен помнить в первые 10 минут»
- Ссылка на постмортем-промт ([prompt-postmortem](./prompt-postmortem.md)) для followup
```
