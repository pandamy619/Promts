# Промт — Архитектурный review сервиса

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #architecture-review #microservice #risk #backend #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — Architectural Design Review Feedback Generator](https://logicballs.com/ai-prompt/prompts/architectural-design-review-feedback-generator)
**Дельта от оригинала:** добавлены явные требования по severity и owner у каждого finding, привязка проблемы к порогу масштаба и канонический блок поведения при нехватке данных. Кандидат на переписывание в `#critique` жанр (см. бэклог в индексе).

---

## Когда использовать

- Когда уже есть дизайн сервиса и его надо критически проверить
- Когда нужен peer review до реализации или перед архитектурным комитетом
- Когда хочется получить не только критику, но и приоритизированные remediation steps

## Что подставить

- `{{architecture_description}}` — string or link, e.g. `текст design doc или ссылка на Confluence`
- `{{business_context}}` — string, e.g. `обрабатываем платежи b2c, peak в чёрную пятницу x10`
- `{{tech_stack}}` — string, e.g. `Go 1.22, Postgres 16, Redis, Kafka`
- `{{scale_requirements}}` — string, e.g. `500 RPS baseline, 5k RPS peak; p99 < 500ms`
- `{{team_context}}` — string, e.g. `5 backend mid, 1 senior, нет SRE`
- `{{specific_concerns}}` — list, e.g. `согласованность данных при refund; idempotency на retry`
- `{{compliance_needs}}` — string, e.g. `PCI DSS SAQ-D; GDPR; данные не покидают EU`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Каждый finding имеет severity (`critical | high | medium`) и предлагаемого owner-а (`team | role`)
- Где проблема зависит от масштаба — указан порог («проявится при RPS > X»)
- Вердикт явно один из `approve | approve with major changes | request redesign`
- Топ-3 риска — это не пересказ findings, а отдельная приоритизация

## Промт

```text
Выступай как Principal Software Architect, который проводит formal architecture review для критичного сервиса.

Контекст:
- Описание архитектуры: {{architecture_description}}
- Бизнес-контекст: {{business_context}}
- Технологический стек: {{tech_stack}}
- Требования к масштабу: {{scale_requirements}}
- Контекст команды: {{team_context}}
- Специальные зоны риска / concern areas: {{specific_concerns}}
- Compliance / regulatory needs: {{compliance_needs}}

Если для качественного ответа не хватает контекста — задай до 7 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Проведи review по следующим направлениям:
1. [обязательно] Domain boundaries и cohesion
2. [обязательно] Data ownership и consistency model
3. [обязательно] API / event contracts
4. [обязательно] Scalability bottlenecks
5. [обязательно] Reliability / fault tolerance
6. [обязательно] Security / privacy
7. [обязательно] Operability / observability
8. [обязательно] Team fit и operational complexity

На выходе дай:
1. [обязательно] Executive summary
2. [обязательно] Общую оценку зрелости архитектуры по шкале 1-10
3. [обязательно] Топ-3 критических риска
4. [обязательно] Список findings по severity:
   - critical
   - high
   - medium
5. [обязательно] Для каждого finding:
   - в чём проблема
   - почему это опасно
   - какой realistic fix
   - предполагаемый owner (team / role)
   - порог, при котором это проявится (если зависит от масштаба)
6. [обязательно] Вердикт:
   - approve
   - approve with major changes
   - request redesign

Не делай так:
- Finding без severity и без owner.
- «Переписать всё» как рекомендация без обоснования.
- Пересказывать описание архитектуры вместо критики.
- Топ-3 риска, который дублирует findings — это должна быть отдельная приоритизация.

Требования:
- Будь критичен, но прагматичен
- Не советуй "переписать всё" без необходимости
- Если проблема зависит от масштаба, укажи при каком пороге она проявится
- Чётко отделяй факты от предположений

Формат ответа:
- Findings сначала
- Потом open questions / assumptions
- Потом короткое summary
```
