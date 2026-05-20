# Промт — REST API сервис

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-rest-api.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #rest-api #openapi #backend #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — REST API Architect & Generator](https://logicballs.com/ai-prompt/prompts/rest-api-generator)
**Дельта от оригинала:** добавлены требования по idempotency для write-операций, раздел типичных ошибок дизайна и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужно спроектировать новый REST API для сервиса
- Когда хочется получить не только endpoint-идеи, но и OpenAPI-совместимую структуру
- Когда нужно быстро зафиксировать versioning, auth, errors и resource model

## Что подставить

- `{{api_name}}` — string, e.g. `payments-api`
- `{{domain}}` — string, e.g. `обработка платежей и возвратов`
- `{{framework_or_language}}` — string, e.g. `FastAPI / Python 3.13` или `Spring Boot / Java 21`
- `{{auth_method}}` — enum: `jwt | oauth2 | api_key | mtls | session`
- `{{database}}` — enum: `postgres | mysql | mongodb | dynamodb | other`
- `{{business_requirements}}` — list, e.g. `создать платёж; вернуть статус; refund по идемпотентному ключу`
- `{{non_functional_requirements}}` — list, e.g. `p95 < 200ms; 99.9%; PCI DSS`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Для каждого endpoint указаны method, path, request schema, response schema, статус-коды, error model — без пропусков
- Раздел про idempotency для write-операций (POST/PUT/PATCH) реально проработан, а не сведён к одной строке
- Раздел «какие ошибки дизайна здесь наиболее вероятны» содержит как минимум 3 конкретных риска
- Приведён OpenAPI skeleton (или его эквивалент в выбранном `{{specification_format}}`), а не просто упоминание

## Промт

```text
Выступай как API Architect и senior backend engineer. Спроектируй production-ready REST API для сервиса, с акцентом на понятные контракты и эволюцию API без боли.

Контекст:
- Название API: {{api_name}}
- Домен: {{domain}}
- Язык / framework реализации: {{framework_or_language}}
- Метод аутентификации: {{auth_method}}
- База данных / storage: {{database}}
- Бизнес-требования: {{business_requirements}}
- Нефункциональные требования: {{non_functional_requirements}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Нужно подготовить:
1. [обязательно] Общую API strategy:
   - base path
   - versioning
   - naming conventions
   - pagination/filtering/sorting
   - idempotency для write-операций
2. [обязательно] Список ресурсов и операций CRUD / non-CRUD
3. [обязательно] Контракты endpoint-ов:
   - method
   - path
   - request schema
   - response schema
   - status codes
   - error model
4. [обязательно] Правила безопасности:
   - auth
   - authorization
   - rate limiting
   - auditability
5. [обязательно] Минимальную OpenAPI-структуру или YAML-фрагменты
6. [если останется место] Рекомендации по implementation layer:
   - handlers/controllers
   - services
   - repositories
   - validation
7. [обязательно] Раздел "какие ошибки дизайна здесь наиболее вероятны"

Не делай так:
- `POST /users/getById` — это RPC под видом REST. Используй `GET /users/{id}`.
- `200 OK` с телом `{"error": "..."}` — статус и тело должны соответствовать.
- Менять формат `id` между endpoint-ами (`int` в одном, `uuid` в другом).
- Скрывать ошибки валидации под общим `400 Bad Request` без деталей.

Требования:
- Не делай RPC под видом REST без необходимости
- Если ресурсная модель плохая, предложи лучшую
- Не придумывай поля, которые не следуют из контекста
- Для каждого спорного решения покажи trade-off
- Если требования конфликтуют, укажи это явно

Формат ответа:
- Executive summary
- Таблица ресурсов
- Endpoint contracts
- Error model
- OpenAPI skeleton
- Риски и рекомендации
```
