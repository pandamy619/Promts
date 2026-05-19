# Promts

> Подборка русскоязычных промтов для проектирования и шаблонизации сервисов. Обновлено: 2026-05-19

Перед правками или добавлением — [Стандарты библиотеки](./standards.md) и [Эталонная карточка](./template.md).

---

## Навигация

| Файл                                         | Что внутри                                                               | Жанр            |
| -------------------------------------------- | ------------------------------------------------------------------------ | --------------- |
| [Промт — Архитектура микросервиса](./prompt-microservice-architecture.md)         | Разбиение на сервисы, границы домена, данные, интеграции и план миграции | `#single-shot` |
| [Промт — Шаблон FastAPI сервиса](./prompt-fastapi-template.md)           | Генерация production-ready шаблона backend-сервиса на FastAPI            | `#single-shot` |
| [Промт — REST API сервис](./prompt-rest-api.md)                  | Проектирование REST API, OpenAPI-спеки и implementation guide            | `#single-shot` |
| [Промт — GraphQL схема сервиса](./prompt-graphql-schema.md)            | Типы, queries, mutations, federation и auth-паттерны                     | `#single-shot` |
| [Промт — Docker конфигурация сервиса](./prompt-docker-config.md)      | Dockerfile, Compose и контейнеризация под dev/prod                       | `#single-shot` |
| [Промт — Kubernetes манифесты сервиса](./prompt-kubernetes-manifests.md)     | Deployment, Service, Ingress, ConfigMap, Secret и security baseline      | `#single-shot` |
| [Промт — CI-CD для сервиса](./prompt-ci-cd.md)                | Полный пайплайн проверки, сборки, деплоя и отката                        | `#single-shot` |
| [Промт — Runbook деплоя сервиса](./prompt-deployment-runbook.md)           | Пошаговый runbook релиза с health-check и rollback                       | `#single-shot` |
| [Промт — API Gateway для микросервисов](./prompt-api-gateway.md)    | Конфигурация gateway, auth, rate limits, routing и observability         | `#single-shot` |
| [Промт — API документация сервиса](./prompt-api-documentation.md)         | Генерация единообразной документации по endpoint-спеке                   | `#single-shot` |
| [Промт — API тесты сервиса](./prompt-api-tests.md)                | Набор позитивных, негативных, security и edge-case тестов                | `#single-shot` |
| [Промт — Нагрузочное тестирование сервиса](./prompt-load-testing.md) | k6/Locust-подобный baseline для latency, throughput и stress             | `#single-shot` |
| [Промт — WebSocket сервис](./prompt-websocket.md)                 | Шаблон real-time backend-сервиса с auth, scaling и протоколом            | `#single-shot` |
| [Промт — Архитектурный review сервиса](./prompt-architecture-review.md)     | Формальный разбор рисков и слабых мест существующего дизайна             | `#single-shot` |

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

Что планируется добавить, и в каком жанре:

- Incident response runbook — `#single-shot`
- Postmortem template — `#single-shot`
- Observability / SLO дизайн — `#staged`
- Data migration / schema change — `#staged`
- Threat modeling — `#critique`
- Supply chain security (SBOM, signing) — `#single-shot`

Что планируется переписать под другой жанр:

- Архитектура микросервиса → `#staged` (тяжёлая задача, выгодно идти по шагам)
- Архитектурный review → `#critique` (естественный жанр для review)
- FastAPI шаблон → `#staged` (большой выход по файлам)
- Kubernetes манифесты → `#staged` (много манифестов, каждый зависит от предыдущих)
- CI-CD → `#staged` (большой конфиг)
