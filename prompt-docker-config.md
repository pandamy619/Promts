# Промт — Docker конфигурация сервиса

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #docker #compose #devops #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — AI Docker Configuration Creator](https://logicballs.com/ai-prompt/prompts/docker-configuration-creator), [PromptChat — Generate Docker Compose Configuration Files](https://promptschat.com/prompts/write-docker-compose-file/)
**Дельта от оригинала:** добавлены требования non-root и multi-stage по умолчанию, разделение dev/prod, явный список зависимых сервисов и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужно быстро собрать Dockerfile и Compose для нового сервиса
- Когда хочется унифицировать dev/prod контейнеризацию
- Когда надо получить хороший baseline по healthcheck, volumes, networks и security

## Что подставить

- `{{project_type}}` — string, e.g. `python web service` / `node worker`
- `{{framework}}` — string, e.g. `FastAPI + uvicorn` / `Express` / `Spring Boot`
- `{{environment_mode}}` — enum: `dev_only | prod_only | dev_and_prod`
- `{{runtime_requirements}}` — string, e.g. `требует libpq, ffmpeg; 512Mi memory`
- `{{dependent_services}}` — list, e.g. `postgres; redis; kafka; minio`
- `{{base_image_preference}}` — string, e.g. `python:3.13-slim` / `distroless` / `alpine`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Dockerfile использует multi-stage, если применимо для языка/фреймворка
- Приложение запускается не от root в prod-stage
- Есть `.dockerignore` с реальным содержимым, не пустой
- Для всех зависимых сервисов в compose есть healthcheck
- Раздел «Что проверить перед продом» содержит минимум 5 пунктов

## Промт

```text
Выступай как DevOps engineer, который проектирует production-grade Docker-конфигурации для сервисов.

Контекст проекта:
- Тип проекта: {{project_type}}
- Стек / framework: {{framework}}
- Режимы окружения: {{environment_mode}}
- Особые требования: {{runtime_requirements}}
- Зависимые сервисы: {{dependent_services}}
- Предпочтение по базовому образу: {{base_image_preference}}

Если для качественного ответа не хватает контекста — задай до 3 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Нужно сгенерировать полный комплект контейнеризации для сервиса.

Что обязательно включить:
1. [обязательно] `Dockerfile`
2. [обязательно] `.dockerignore`
3. [обязательно] `compose.yaml` или `docker-compose.yml`
4. [обязательно] Переменные окружения, которые нужны для запуска
5. [обязательно] Healthcheck для критичных контейнеров
6. [обязательно] Volumes и networks
7. [обязательно] Обоснование ключевых решений

Не делай так:
- `COPY . .` без `.dockerignore` (тянем `.git`, `node_modules`, секреты).
- Запуск приложения от root в prod-stage.
- `RUN apt-get update && apt-get install -y X` без `--no-install-recommends` и без очистки кэша.
- Hardcoded секреты в Dockerfile / compose (всегда через env / secrets).

Требования:
- Используй multi-stage build, если это оправдано
- По возможности запускай приложение не от root
- Учитывай быстрый rebuild и кэш слоёв
- Разделяй dev и prod режимы, если это имеет смысл
- Если нужны Postgres/Redis/Kafka/Nginx, добавь их как отдельные сервисы в compose
- Не делай insecure defaults без явной пометки
- Если hot reload нужен только в dev, не тащи его в prod

Формат результата:
1. Короткое summary архитектуры контейнеризации
2. Таблица "Файл / Назначение"
3. Полное содержимое файлов
4. Список команд:
   - build
   - local up
   - logs
   - stop
5. Раздел "Что проверить перед продом"
```
