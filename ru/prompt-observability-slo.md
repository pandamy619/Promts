# Промт — Observability и SLO дизайн

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-observability-slo.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #observability #slo #sre #service #single-shot
**Статус:** `🧪 Experimental`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [Google SRE Workbook — Implementing SLOs](https://sre.google/workbook/implementing-slos/), [Liz Fong-Jones — Multi-window multi-burn-rate alerting](https://sre.google/workbook/alerting-on-slos/)
**Дельта от оригинала:** добавлено требование привязки SLI к user journey (а не к internal metric), формат multi-window multi-burn-rate для alerting и явный раздел про logging/tracing correlation fields.

---

## Когда использовать

- Когда сервис идёт в production и нужно понять, что и как наблюдать
- Когда команда тонет в алертах и хочет перейти на SLO-based alerting
- Когда нужно отделить «вижу метрику» от «понимаю, страдает ли пользователь»

## Что подставить

- `{{service_name}}` — string, e.g. `payments-api`
- `{{critical_user_journeys}}` — list, e.g. `создать платёж; проверить статус; refund`
- `{{current_metrics}}` — string, e.g. `Prometheus на /metrics; есть http_requests_total и http_request_duration_seconds`
- `{{logging_stack}}` — enum: `loki | elastic | datadog_logs | cloudwatch | other`
- `{{tracing_stack}}` — enum: `opentelemetry | jaeger | tempo | datadog_apm | none`
- `{{error_budget_policy}}` — string, e.g. `при исчерпании бюджета — freeze фичей до восстановления`
- `{{team_maturity}}` — enum: `none | basic_metrics | mature_metrics_no_slo | slo_in_progress`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Для каждого critical user journey определены минимум 2 SLI (latency + availability)
- SLO привязан к user-visible behavior, не к internal metric (CPU, memory — это не SLO)
- Alerting основан на burn rate (multi-window multi-burn-rate), а не на mgновенных значениях
- Указаны конкретные log fields для correlation (`trace_id`, `request_id`, `user_id`)
- Дашборды разделены на 3 уровня: overview / per-journey / drill-down

## Промт

```text
Выступай как Senior SRE с опытом внедрения SLO-based observability в командах с разной зрелостью. Подготовь практический дизайн observability и SLO для сервиса.

Контекст:
- Сервис: {{service_name}}
- Critical user journeys: {{critical_user_journeys}}
- Что есть сейчас: {{current_metrics}}
- Logging stack: {{logging_stack}}
- Tracing stack: {{tracing_stack}}
- Error budget policy: {{error_budget_policy}}
- Зрелость команды: {{team_maturity}}

Если для качественного ответа не хватает контекста — задай до 7 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь:

1. [обязательно] SLIs:
   - для каждого critical user journey — минимум 2 SLI (availability + latency)
   - формула SLI: numerator / denominator (например, «доля запросов с 2xx/3xx за окно» / «все запросы»)
   - что считается «хорошим» событием и что — «плохим», явно
2. [обязательно] SLOs:
   - целевое значение для каждого SLI (например, 99.9% за 30-day rolling)
   - rolling window (28/30 days — это нормально; не calendar month)
   - обоснование: почему именно эта цифра (а не 99.99% — стоит ли)
3. [обязательно] Error budget и policy:
   - расчёт бюджета (на 30 дней с SLO 99.9% бюджет = 43 минуты)
   - что делает команда при исчерпании
   - сигнал «10% бюджета осталось» / «50% сгорело»
4. [обязательно] Alerting strategy:
   - multi-window multi-burn-rate (fast burn 1h × 14.4; slow burn 6h × 6)
   - НЕ алертить на «один 500 ответ»
   - на что page (S1), на что ticket (warning)
5. [обязательно] Logging:
   - structured logs с конкретными required fields: `timestamp` (ISO8601 UTC), `level`, `service`, `trace_id`, `request_id`, `user_id`, `event`
   - запрещённые поля (PII / PCI данные)
   - где writeлся (stdout → log collector)
6. [обязательно] Tracing:
   - где обязательны spans (входящий request, исходящий call в другой сервис, DB запрос > 50ms)
   - propagation headers (W3C trace-context)
   - sampling strategy (например, 10% общий + 100% при ошибке)
7. [обязательно] Dashboards (hierarchy):
   - overview: SLO status, error budget burn, key SLIs
   - per-journey: SLI разложенный по endpoint / user segment
   - drill-down: технические метрики (CPU, mem, DB pool, queue depth)
   - правило: НЕ начинаем с drill-down
8. [если останется место] Roadmap внедрения по зрелости команды

Не делай так:
- SLO 99.99% «потому что круто» — это требует серьёзной инвестиции, обоснуй или снизь.
- Alert на каждый error log — alert fatigue.
- Logging без structured fields («payment failed for user 123») — потом не запросить.
- Tracing только в одном сервисе — теряется end-to-end видимость.
- SLI = «CPU < 80%» — это не SLI, это internal metric.
- Дашборд начинается с графика CPU — он бесполезен для понимания «страдает ли пользователь».

Формат ответа:
- Executive summary (зачем нам это, что меняется)
- Таблица «User journey | SLI | SLO | Бюджет»
- Расчёт error budget с числами
- Конфиг alerting (PromQL / правила в JSON, в зависимости от стека)
- Описание logging schema и tracing rules
- Mock-up структуры дашбордов (текстом, hierarchy)
```
