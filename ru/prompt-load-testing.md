# Промт — Нагрузочное тестирование сервиса

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-load-testing.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #performance #load-testing #qa #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — Performance Test Script Generator](https://logicballs.com/ai-prompt/prompts/performance-test-script-generator)
**Дельта от оригинала:** добавлены требования к метрикам сервиса (а не только клиента), warm-up, частые ловушки ложноположительных выводов и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужно быстро получить baseline для load/stress/spike/soak теста
- Когда сервис готовится к релизу и хочется проверить latency, throughput и saturation points
- Когда нужен стартовый скрипт для k6, Locust или Artillery

## Что подставить

- `{{target_system}}` — string, e.g. `payments-api в stage, https://stage.payments.example.com`
- `{{test_type}}` — enum: `load | stress | spike | soak | latency_benchmark`
- `{{language_or_framework}}` — enum: `k6 | locust | artillery | jmeter | gatling`
- `{{concurrency}}` — string, e.g. `ramp 0→500 VU за 5m, hold 30m`
- `{{duration}}` — string, e.g. `45m total (5m ramp + 30m hold + 10m ramp-down)`
- `{{success_criteria}}` — list, e.g. `p95 < 300ms; error rate < 0.5%; throughput ≥ 200 RPS`
- `{{auth_model}}` — enum: `jwt | oauth2 | api_key | none`
- `{{test_data_strategy}}` — string, e.g. `pre-seeded users 10k; round-robin по pool;  cleanup после прогона`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Скрипт включает warm-up фазу
- Указано, какие метрики смотреть в самом сервисе (CPU, memory, DB connections, GC), а не только в клиенте
- Критерии успеха количественные и привязаны к перцентилям, а не к среднему
- Есть раздел про частые ложноположительные выводы (например, «p95 хороший, но p99 в полку — значит хвост»)

## Промт

```text
Выступай как Performance Engineer. Подготовь production-grade сценарий нагрузочного тестирования для сервиса.

Контекст:
- Целевая система: {{target_system}}
- Тип теста: {{test_type}}
- Инструмент: {{language_or_framework}}
- Параметры конкурентности: {{concurrency}}
- Длительность теста: {{duration}}
- Критерии успеха: {{success_criteria}}
- Модель аутентификации: {{auth_model}}
- Стратегия тестовых данных: {{test_data_strategy}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь:
1. [обязательно] Цели теста и что именно измеряем
2. [обязательно] Профиль нагрузки:
   - warm-up
   - ramp-up
   - steady state
   - ramp-down
3. [обязательно] Метрики:
   - p50 / p95 / p99 latency
   - throughput
   - error rate
   - saturation signals
   - метрики самого сервиса (CPU, memory, DB connections, GC)
4. [обязательно] Полный пример скрипта
5. [обязательно] Что нужно подготовить до запуска
6. [обязательно] Как интерпретировать результаты
7. [обязательно] Частые ловушки и ложноположительные выводы

Не делай так:
- Запуск без warm-up — первые минуты будут шумом.
- Измерять только client-side latency, без метрик сервиса.
- Считать «среднее» без перцентилей — оно прячет хвост.
- Гонять stress-тест на проде без согласования.

Требования:
- Не ограничивайся только числом RPS
- Если есть внешние зависимости, укажи как они исказят результаты
- Укажи, какие метрики надо смотреть не только в клиенте, но и в самом сервисе

Формат ответа:
- Summary
- Test plan
- Скрипт
- Checklist перед запуском
- Интерпретация результатов
```
