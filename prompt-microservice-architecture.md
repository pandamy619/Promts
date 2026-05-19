# Промт — Архитектура микросервиса

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #microservice #architecture #backend #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — AI Microservice Architecture Planner](https://logicballs.com/ai-prompt/prompts/microservice-architecture-planner), [DocsBot — Microservices Architecture Diagram](https://docsbot.ai/prompts/technical/microservices-architecture-diagram)
**Дельта от оригинала:** добавлен явный отскок «оправдан ли микросервис вообще», требование Mermaid контейнерного уровня, таблица рисков и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужно понять, оправдан ли микросервис вообще
- Когда надо спроектировать новый сервис или разрезать монолит
- Когда важно заранее зафиксировать данные, интеграции, риски и стратегию миграции

## Что подставить

- `{{domain}}` — string, e.g. `payments`
- `{{business_goal}}` — string, e.g. `снизить latency оплаты до p95 < 300ms`
- `{{functional_requirements}}` — list, e.g. `создание платежа; повтор по идемпотентному ключу; refund`
- `{{non_functional_requirements}}` — list, e.g. `99.95% доступность; PCI DSS; multi-region`
- `{{load_and_sla}}` — string, e.g. `~200 RPS peak, p95 < 300ms, RPO 5m`
- `{{current_landscape}}` — string, e.g. `монолит на Django + Postgres, есть Kafka`
- `{{stack_constraints}}` — string, e.g. `Python + Go допустимы; Java нет`
- `{{security_and_compliance_requirements}}` — string, e.g. `PCI DSS SAQ-D, GDPR`
- `{{team_size_and_level}}` — string, e.g. `4 backend mid, 1 senior, без выделенного SRE`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Есть Mermaid-диаграмма контейнерного уровня
- Таблица «Риск / Почему возникнет / Как снизить» заполнена для всех перечисленных в ответе рисков, без пустых ячеек
- Решение `monolith / modular monolith / microservice` явно проговорено с минимум 2 аргументами в обе стороны
- Для каждого bounded context указано, кому он принадлежит, какие у него данные и что ему запрещено
- Если предлагается миграция от монолита — план разбит на этапы с критерием готовности каждого этапа

## Промт

```text
Выступай как Principal Software Architect с опытом проектирования распределённых систем, DDD, event-driven интеграций и production backend-платформ.

Мне нужно спроектировать сервис или понять, стоит ли вообще выносить его в отдельный микросервис.

Контекст:
- Домен: {{domain}}
- Бизнес-цель: {{business_goal}}
- Функциональные требования: {{functional_requirements}}
- Нефункциональные требования: {{non_functional_requirements}}
- Нагрузка и SLA: {{load_and_sla}}
- Текущий ландшафт / существующие системы: {{current_landscape}}
- Ограничения по стеку: {{stack_constraints}}
- Требования по безопасности и compliance: {{security_and_compliance_requirements}}
- Размер и уровень команды: {{team_size_and_level}}

Если для качественного ответа не хватает контекста — задай до 7 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Сначала выполни "microservices premium" проверку:
1. Оцени, действительно ли здесь нужен отдельный микросервис.
2. Если микросервис не оправдан, честно предложи более простой вариант: модульный монолит, расширение существующего сервиса или отдельный bounded context внутри текущей системы.
3. Не выбирай микросервисы только из-за моды.

На выходе дай:
1. [обязательно] Итоговое решение: monolith / modular monolith / microservice, с аргументами.
2. [обязательно] Bounded contexts и границы сервиса.
3. [обязательно] Ответственность каждого сервиса и что ему запрещено делать.
4. [обязательно] Владение данными: какие таблицы/коллекции/события кому принадлежат.
5. [обязательно] Коммуникации между сервисами:
   - synchronous: REST/gRPC
   - asynchronous: Kafka/RabbitMQ/events
   - где допустима eventual consistency
6. [обязательно] Контракты:
   - ключевые API
   - события
   - idempotency / retry / DLQ требования
7. [если останется место] Инфраструктурные компоненты:
   - API gateway
   - auth
   - service discovery
   - observability
   - background workers / schedulers
8. [обязательно] Риски (таблица):
   - data consistency
   - distributed transactions
   - latency
   - coupling
   - operational overhead
9. [обязательно] План миграции по шагам, если идём от монолита к сервису.
10. [обязательно] Mermaid-диаграмму контейнерного уровня.

Формат ответа:
- Сначала короткое executive summary
- Затем структурированные разделы
- Затем таблица "Риск / Почему возникнет / Как снизить"
- Затем Mermaid

Не делай так:
- «Микросервисы потому что это современно» — без аргументов в пользу разделения данных и команд.
- Один сервис владеет таблицами другого через прямой SQL-доступ.
- «Eventual consistency решим потом» — без указания, где именно и какие гарантии.

Правила:
- Не придумывай требования, которых нет
- Помечай спорные предположения отдельно
- Отдавай предпочтение прагматичным решениям
- Если есть соблазн сделать слишком сложно, укажи более дешёвую альтернативу
```
