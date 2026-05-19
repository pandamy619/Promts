# Промт — GraphQL схема сервиса

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #graphql #backend #schema #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — GraphQL Schema Generator](https://logicballs.com/ai-prompt/prompts/graphql-schema-generator)
**Дельта от оригинала:** добавлен раздел про N+1 risk zones, явное сравнение «где GraphQL выиграет у REST» и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда сервис строится вокруг GraphQL, а не REST
- Когда нужно быстро получить аккуратную SDL-схему и подход к federation
- Когда важно заранее продумать pagination, auth и resolver boundaries

## Что подставить

- `{{domain_model}}` — list, e.g. `User; Order; OrderItem; Product`
- `{{database_type}}` — enum: `postgres | mysql | mongodb | dynamodb | mixed`
- `{{auth_requirements}}` — string, e.g. `JWT; field-level scope check; admin-only mutations`
- `{{federation_requirements}}` — enum: `none | apollo_federation_v2 | schema_stitching`
- `{{scalar_customization}}` — list, e.g. `DateTime; UUID; Money`
- `{{pagination_type}}` — enum: `cursor (relay) | offset | both`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- SDL приведена целиком для всех типов, без `...` и сокращений
- Указаны N+1 risk zones — минимум 2 конкретных места, где нужен data loader
- Явно проговорено, где GraphQL даёт выигрыш относительно REST, а где нет
- Pagination реализована единообразно во всех list-полях

## Промт

```text
Выступай как principal GraphQL architect. Сгенерируй production-ready GraphQL schema и рекомендации по её реализации.

Контекст:
- Domain model: {{domain_model}}
- Database / storage: {{database_type}}
- Требования к auth / authorization: {{auth_requirements}}
- Federation / architecture: {{federation_requirements}}
- Нужные custom scalars: {{scalar_customization}}
- Стратегия пагинации: {{pagination_type}}

Если для качественного ответа не хватает контекста — задай до 3 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь:
1. [обязательно] Type system:
   - object types
   - input types
   - enums
   - interfaces / unions, если нужно
2. [обязательно] Query и Mutation дизайн
3. [обязательно] Pagination pattern
4. [обязательно] Error handling strategy
5. [обязательно] Auth / field-level authorization подход
6. [обязательно] N+1 risk zones и как их снимать
7. [обязательно] Пример SDL
8. [если останется место] Рекомендации по resolvers / services / data loaders

Не делай так:
- Публичные типы один-в-один повторяют storage-модель (имена и типы полей).
- Один резолвер делает N+1 запросов без data loader.
- `Mutation { doStuff(input: JSON): JSON }` — типизация теряется, GraphQL превращается в RPC.

Требования:
- Не делай схему избыточно "умной", если домен простой
- Явно указывай, где GraphQL здесь выиграет у REST, а где нет
- Не смешивай внутренние storage-модели с публичной схемой без необходимости

Формат ответа:
- Summary
- Список типов
- SDL
- Resolver notes
- Риски и trade-offs
```
