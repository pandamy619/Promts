# Промт — Postmortem (blameless)

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-postmortem.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #postmortem #sre #ops #service #single-shot
**Статус:** `🧪 Experimental`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [Google SRE Workbook — Postmortem Culture](https://sre.google/workbook/postmortem-culture/), [Atlassian Blameless Postmortem](https://www.atlassian.com/incident-management/postmortem/blameless)
**Дельта от оригинала:** требование минимум 3 contributing factors (а не одной «root cause»), action items с owner и due date, явные критерии blameless tone и формат timeline в UTC.

---

## Когда использовать

- После любого инцидента S1/S2 (или near-miss с серьёзным потенциалом)
- Когда нужно структурировать сырые события в полезный документ
- Когда команда хочет blameless-практику, но скатывается в «кто виноват»

## Что подставить

- `{{incident_id}}` — string, e.g. `INC-2026-0517-001`
- `{{incident_summary}}` — string, 1-2 предложения, e.g. `payments-api отдавал 500 на 18% запросов в течение 47 минут`
- `{{severity}}` — enum: `S1 | S2 | S3`
- `{{detected_at}}` — ISO datetime UTC, e.g. `2026-05-17T14:23:00Z`
- `{{resolved_at}}` — ISO datetime UTC, e.g. `2026-05-17T15:10:00Z`
- `{{impacted_users}}` — string, e.g. `~12k активных пользователей; 850 платежей failed`
- `{{impacted_services}}` — list, e.g. `payments-api; checkout-frontend; order-service (degraded)`
- `{{timeline_raw}}` — string, сырые события для построения хронологии (Slack-выгрузка, тайминги алертов)
- `{{root_cause_hypothesis}}` — string, optional, e.g. `подозрение на DB connection pool exhaustion`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Timeline в UTC, без пропусков между detection и mitigation
- Минимум 3 contributing factors, а не один «root cause»
- Каждый action item имеет owner (человек / команда), severity (P0/P1/P2) и due date
- Tone blameless: причины — это процессы и системы, не люди («процесс не предотвратил X», не «Иван забыл X»)
- TL;DR не больше 5 строк, читается за 30 секунд

## Промт

```text
Выступай как SRE с опытом ведения blameless постмортемов в больших командах. Составь постмортем по инциденту.

Контекст инцидента:
- Incident ID: {{incident_id}}
- Сводка: {{incident_summary}}
- Severity: {{severity}}
- Detected: {{detected_at}}
- Resolved: {{resolved_at}}
- Impacted users: {{impacted_users}}
- Impacted services: {{impacted_services}}
- Гипотеза причины (если есть): {{root_cause_hypothesis}}

Сырая хронология / события:
{{timeline_raw}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь постмортем со следующей структурой:

1. [обязательно] TL;DR — не больше 5 строк, понятно для не-инженера
2. [обязательно] Impact:
   - сколько пользователей затронуто (число)
   - сколько сервисов в degraded состоянии
   - финансовый / репутационный impact, если оцениваем
   - длительность user-facing impact (не total длительность инцидента)
3. [обязательно] Timeline в UTC:
   - все ключевые события с тайм-стампом
   - пометки `[detection]`, `[mitigation]`, `[escalation]`, `[resolution]`
   - покажи время реакции на каждом шаге (delta от предыдущего события)
4. [обязательно] Contributing factors (минимум 3):
   - не один «root cause», а несколько слоёв
   - технические + процессные + организационные
   - для каждого: что именно произошло и почему этот слой не сработал
5. [обязательно] What went well:
   - конкретные действия / системы, которые сработали как задумано
   - не общие фразы вроде «команда молодец»
6. [обязательно] What went badly:
   - конкретные провалы (медленный alert, неправильный playbook, отсутствующий dashboard)
   - blameless tone
7. [обязательно] Action items:
   - таблица: что | owner (команда/роль) | severity (P0/P1/P2) | due date | критерий «сделано»
   - минимум 3 пункта; не больше 10 (иначе они не сделаются)
   - не «улучшить мониторинг», а «добавить alert на DB connection pool > 80% — owner platform, P1, due 2026-06-01»
8. [обязательно] Lessons learned:
   - 2-3 главных вывода, которые команда уносит из инцидента
9. [если останется место] Open questions — что ещё неясно и кому копнуть

Не делай так:
- Один «root cause» — реальные инциденты это всегда multiple contributing factors.
- Action item без owner-а («команда добавит мониторинг»).
- «Иван забыл проверить» вместо «процесс не требовал второго ревью на этом шаге».
- TL;DR на 15 строк — он перестаёт быть TL;DR.
- Timeline без UTC или с локальным временем без указания зоны.
- Action items вида «улучшить процесс» — без конкретного действия и критерия готовности.

Формат ответа:
- Готовый Markdown документ
- Используй `[detection]`, `[mitigation]`, `[escalation]`, `[resolution]` маркеры в timeline
- Action items — обязательно таблица, не bullet-список
```
