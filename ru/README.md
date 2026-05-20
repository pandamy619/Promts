# Promts

> 🇷🇺 **Русский** · [🇬🇧 English](../en/README.md)

> Подборка русскоязычных промтов для проектирования и шаблонизации сервисов. Обновлено: 2026-05-20

Перед правками или добавлением — [Стандарты библиотеки](./standards.md) и [Эталонная карточка](./template.md).

---

## Навигация

### Архитектура и проектирование

| Файл                                         | Что внутри                                                               | Жанр            |
| -------------------------------------------- | ------------------------------------------------------------------------ | --------------- |
| [Промт — Архитектура микросервиса](./prompt-microservice-architecture.md)         | Разбиение на сервисы, границы домена, данные, интеграции и план миграции | `#single-shot` `#staged` |
| [Промт — Архитектурный review сервиса](./prompt-architecture-review.md)     | Формальный разбор рисков и слабых мест существующего дизайна             | `#single-shot` `#critique` |
| [Промт — Threat modeling](./prompt-threat-modeling.md)     | STRIDE-анализ по trust boundaries, risk matrix, mitigations              | `#single-shot` |

### API и сервисы

| Файл                                         | Что внутри                                                               | Жанр            |
| -------------------------------------------- | ------------------------------------------------------------------------ | --------------- |
| [Промт — Шаблон FastAPI сервиса](./prompt-fastapi-template.md)           | Генерация production-ready шаблона backend-сервиса на FastAPI            | `#single-shot` |
| [Промт — REST API сервис](./prompt-rest-api.md)                  | Проектирование REST API, OpenAPI-спеки и implementation guide            | `#single-shot` |
| [Промт — GraphQL схема сервиса](./prompt-graphql-schema.md)            | Типы, queries, mutations, federation и auth-паттерны                     | `#single-shot` |
| [Промт — WebSocket сервис](./prompt-websocket.md)                 | Шаблон real-time backend-сервиса с auth, scaling и протоколом            | `#single-shot` |
| [Промт — API Gateway для микросервисов](./prompt-api-gateway.md)    | Конфигурация gateway, auth, rate limits, routing и observability         | `#single-shot` |
| [Промт — API документация сервиса](./prompt-api-documentation.md)         | Генерация единообразной документации по endpoint-спеке                   | `#single-shot` |

### Инфраструктура и деплой

| Файл                                         | Что внутри                                                               | Жанр            |
| -------------------------------------------- | ------------------------------------------------------------------------ | --------------- |
| [Промт — Docker конфигурация сервиса](./prompt-docker-config.md)      | Dockerfile, Compose и контейнеризация под dev/prod                       | `#single-shot` |
| [Промт — Kubernetes манифесты сервиса](./prompt-kubernetes-manifests.md)     | Deployment, Service, Ingress, ConfigMap, Secret и security baseline      | `#single-shot` |
| [Промт — CI-CD для сервиса](./prompt-ci-cd.md)                | Полный пайплайн проверки, сборки, деплоя и отката                        | `#single-shot` |
| [Промт — Runbook деплоя сервиса](./prompt-deployment-runbook.md)           | Пошаговый runbook релиза с health-check и rollback                       | `#single-shot` |
| [Промт — Supply chain security](./prompt-supply-chain-security.md)           | SBOM, signing, vulnerability scanning, SLSA                              | `#single-shot` |

### Операционка и инциденты

| Файл                                         | Что внутри                                                               | Жанр            |
| -------------------------------------------- | ------------------------------------------------------------------------ | --------------- |
| [Промт — Incident response runbook](./prompt-incident-response.md)        | Severity levels, роли в инциденте, communication cadence, шаблоны        | `#single-shot` |
| [Промт — Postmortem (blameless)](./prompt-postmortem.md)         | Timeline, contributing factors, action items, blameless tone             | `#single-shot` |
| [Промт — Observability и SLO дизайн](./prompt-observability-slo.md)         | SLI/SLO для user journeys, error budget, multi-burn-rate alerting       | `#single-shot` |
| [Промт — Data migration / schema change](./prompt-data-migration.md)         | Expand-contract, verification queries, rollback plan                     | `#single-shot` |

### Тестирование

| Файл                                         | Что внутри                                                               | Жанр            |
| -------------------------------------------- | ------------------------------------------------------------------------ | --------------- |
| [Промт — API тесты сервиса](./prompt-api-tests.md)                | Набор позитивных, негативных, security и edge-case тестов                | `#single-shot` |
| [Промт — Нагрузочное тестирование сервиса](./prompt-load-testing.md) | k6/Locust-подобный baseline для latency, throughput и stress             | `#single-shot` |

## Легенда статусов

| Статус             | Значение                                              |
| ------------------ | ----------------------------------------------------- |
| `📋 Template`      | Каркас, нужно дорабатывать под конкретный кейс        |
| `🟢 Ready`         | Проверен, можно использовать без доработок            |
| `🧪 Experimental`  | Собран по стандарту, но мало реальных прогонов        |

## Легенда жанров

| Жанр            | Когда подходит                                                                 |
| --------------- | ------------------------------------------------------------------------------ |
| `#single-shot`  | Инструкция → большой ответ. Default для простых задач.                         |
| `#staged`       | План → подтверждение → исполнение по шагам. Для тяжёлых задач.                 |
| `#critique`     | Draft → self-review → improve. Для review, QA, аудита.                         |
| `#interview`    | Модель сама задаёт вопросы, потом действует. Когда контекста объективно мало. |

## Принцип подбора

- Все промты собраны по мотивам найденных в интернете шаблонов и переведены/адаптированы под русскоязычный engineering-контекст.
- Упор сделан на backend и platform задачи: сервис, инфраструктура, документация, деплой.
- Внутри каждой заметки есть ссылка на исходники, чтобы при необходимости вернуться к оригинальной идее.

## Бэклог

Что планируется переписать под другой жанр:

- ~~Архитектура микросервиса → `#staged`~~ ✅ сделано в v2 (поддержка через `{{mode}}`)
- ~~Архитектурный review → `#critique`~~ ✅ сделано в v2 (поддержка через `{{mode}}`)
- Threat modeling → `#critique` (естественный жанр для security review)
- FastAPI шаблон → `#staged` (большой выход по файлам)
- Kubernetes манифесты → `#staged` (много манифестов, каждый зависит от предыдущих)
- CI-CD → `#staged` (большой конфиг)
- Observability / SLO дизайн → `#staged` (можно делать поэтапно: сначала SLI, потом alerting, потом dashboards)
- Data migration → `#staged` (стратегия → application changes → migration steps по этапам)

Что планируется добавить:

- Перевод всех промтов на английский в подпапку `/en/` с языковым переключателем
- MkDocs Material + GitHub Pages для нормального сайта с поиском и i18n
