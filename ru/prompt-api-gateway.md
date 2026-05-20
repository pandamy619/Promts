# Промт — API Gateway для микросервисов

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-api-gateway.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #api-gateway #microservice #security #devops #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — API Gateway Architecture Configurator](https://logicballs.com/ai-prompt/prompts/api-gateway-configurator)
**Дельта от оригинала:** добавлено явное разделение edge concerns / domain concerns, требование к сравнению gateway-вариантов и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужно спроектировать gateway для набора backend-сервисов
- Когда важно сразу учесть auth, rate limits, routing и observability
- Когда команда выбирает между Kong, NGINX, Traefik, Envoy или managed gateway

## Что подставить

- `{{gateway_type}}` — enum: `kong | nginx | traefik | envoy | aws_api_gateway | apisix | other`
- `{{infrastructure_context}}` — string, e.g. `EKS в AWS, ALB перед gateway`
- `{{traffic_patterns}}` — string, e.g. `~500 RPS, bursts 2k RPS, 70% reads`
- `{{security_requirements}}` — string, e.g. `OAuth2 + mTLS для service-to-service; PCI scope`
- `{{backend_services}}` — list, e.g. `payments-api; user-api; notifications-api`
- `{{special_requirements}}` — string, e.g. `canary routing 5%; WebSocket для /ws`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Явно проговорено, какие политики живут на gateway, а какие внутри сервисов
- Если возможны несколько gateway-вариантов — приведено короткое сравнение
- Конфигурация или IaC фрагменты приведены целиком, не «пример конфига для одного route»
- Раздел observability покрывает access logs, metrics и tracing headers — все три

## Промт

```text
Выступай как Principal Cloud Infrastructure Architect. Спроектируй production-grade API gateway конфигурацию для микросервисной системы.

Контекст:
- Технология gateway: {{gateway_type}}
- Инфраструктурный контекст: {{infrastructure_context}}
- Характер трафика: {{traffic_patterns}}
- Требования безопасности и compliance: {{security_requirements}}
- Backend-сервисы: {{backend_services}}
- Особые ограничения: {{special_requirements}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

В ответе подготовь:
1. [обязательно] Архитектуру gateway-слоя
2. [обязательно] Правила маршрутизации:
   - path-based
   - host-based
   - version-based
3. [обязательно] AuthN/AuthZ:
   - JWT / OAuth2 / API key / mTLS
   - где проверяется аутентификация
   - где применяются ACL / scopes / roles
4. [обязательно] Политики:
   - rate limiting
   - request size limits
   - retries
   - circuit breaking
   - caching, если уместно
5. [обязательно] Observability:
   - access logs
   - metrics
   - tracing headers
6. [обязательно] Пример конфигурации / IaC фрагменты
7. [обязательно] Риски и анти-паттерны

Не делай так:
- Бизнес-логика в gateway (например, mapping JSON в JSON по сложным правилам).
- Auth check в каждом сервисе ПЛЮС в gateway без обоснования (двойная проверка, лишняя задержка).
- Rate limit, который применяется только к одному маршруту, тогда как остальные открыты.
- Gateway без access logs — потом будет нечем дебажить инцидент.

Требования:
- Не превращай gateway в бизнес-логический монолит
- Разделяй edge concerns и domain concerns
- Явно укажи, какие политики должны жить на gateway, а какие внутри сервисов
- Если есть несколько реалистичных вариантов, сравни их кратко

Формат ответа:
- Summary
- Архитектура
- Таблица политик
- Конфиг / YAML / declarative examples
- Риски
```
