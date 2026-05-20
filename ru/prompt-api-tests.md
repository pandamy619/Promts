# Промт — API тесты сервиса

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-api-tests.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #api-testing #qa #backend #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — API Test Scenario Builder](https://logicballs.com/ai-prompt/prompts/api-test-scenario-builder)
**Дельта от оригинала:** добавлен сводный риск-обзор и таблица покрытия в начале, явное требование к assertion-ам и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда надо быстро получить тестовый набор по OpenAPI или описанию endpoint-ов
- Когда важно не забыть негативные кейсы, auth, edge cases и contract checks
- Когда нужно подготовить базу для ручных тестов, BDD или автотестов

## Что подставить

- `{{api_specification}}` — string or link, e.g. `OpenAPI 3.1 в /api/openapi.yaml` или текст спеки
- `{{auth_method}}` — enum: `jwt | oauth2 | api_key | mtls | none`
- `{{test_focus_areas}}` — enum: `functional | security | performance | contract | all`
- `{{output_format}}` — enum: `bdd | manual | json | csv`
- `{{risk_level}}` — enum: `low | medium | high | critical`
- `{{environment_constraints}}` — string, e.g. `тестовый Postgres seed; не вызывать prod провайдеров`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Покрыты все 9 групп сценариев из задачи (positive, negative, auth, boundary, rate limit, contract, errors, idempotency, integrations), или явно указано, почему какая-то не применима
- Для каждого теста заполнены все 6 полей (название, предусловия, шаги, данные, ожидаемый результат, приоритет)
- В шагах есть конкретные assertion-ы (`status_code == 404`, `body.error == "..."`), а не «проверить ответ»
- Сводка рисков указывает 3-5 ключевых

## Промт

```text
Выступай как Senior QA Engineer, специализирующийся на API testing и микросервисах. Подготовь полный набор API test scenarios по входной спецификации.

Контекст:
- API specification: {{api_specification}}
- Метод аутентификации: {{auth_method}}
- Фокус тестирования: {{test_focus_areas}}
- Формат вывода: {{output_format}}
- Уровень риска: {{risk_level}}
- Ограничения окружения: {{environment_constraints}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Нужно покрыть:
1. [обязательно] Positive path
2. [обязательно] Negative validation cases
3. [обязательно] Authentication / authorization
4. [обязательно] Boundary values
5. [обязательно] Rate limiting / throttling
6. [обязательно] Contract consistency
7. [обязательно] Error handling
8. [обязательно, если релевантно] Idempotency / retry
9. [обязательно] Integration edge cases с внешними зависимостями

Для каждого теста укажи:
- Название
- Предусловия
- Шаги
- Тестовые данные
- Ожидаемый результат (с конкретным assertion)
- Приоритет

Не делай так:
- Только happy path — это не тестовый набор, а демо.
- Тест без assertion (`response.status_code` не проверяется).
- «Ожидаемый результат: правильный ответ» — без конкретики.
- Один тест проверяет 5 разных вещей сразу.

Требования:
- Не ограничивайся happy path
- Если API публичный или критичный, усили security coverage
- Если в спецификации есть пробелы, явно помечай missing contract details
- Если входные данные большие, сначала сгруппируй сценарии по endpoint-ам

Формат ответа:
- Сводка рисков
- Таблица покрытия
- Далее сами тесты в выбранном формате
```
