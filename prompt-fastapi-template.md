# Промт — Шаблон FastAPI сервиса

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #fastapi #backend #service #template #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [DocsBot — FastAPI Project Scaffold](https://docsbot.ai/prompts/programming/fastapi-project-scaffold), [PyPI — create-fastapi-service](https://pypi.org/project/create-fastapi-service/0.1.0/)
**Дельта от оригинала:** версии стека и инструментов вынесены в плейсхолдеры (раньше были зашиты), добавлены требования на async ORM и запреты на «same as above».

---

## Когда использовать

- Когда нужно быстро сгенерировать стартовый шаблон нового backend-сервиса
- Когда команда хочет единый каркас проекта, а не каждый раз собирать сервис с нуля
- Когда важны структура, миграции, health-check, логирование и тесты уже на старте

## Что подставить

- `{{service_name}}` — string, e.g. `payments-api`
- `{{domain_description}}` — string, e.g. `сервис создаёт и проверяет платежи через провайдеров`
- `{{python_version}}` — string, e.g. `3.13`
- `{{database}}` — enum: `postgres | mysql | sqlite | mongodb`
- `{{orm}}` — enum: `sqlalchemy_async | tortoise | sqlmodel`
- `{{migration_tool}}` — enum: `alembic | yoyo | none`
- `{{auth_mode}}` — enum: `jwt | oauth2 | api_key | mtls | none`
- `{{external_integrations}}` — list, e.g. `stripe; sendgrid; kafka`
- `{{background_jobs}}` — enum: `celery | arq | dramatiq | none`
- `{{linter}}` — enum: `ruff | flake8 | none`
- `{{test_framework}}` — enum: `pytest | unittest`
- `{{deployment_target}}` — enum: `docker | kubernetes | bare_metal | serverless`
- `{{repo_conventions}}` — string, e.g. `conventional commits, src layout, poetry`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Дерево каталогов содержит все указанные подпапки и не пропускает ни одной из перечисленных в требованиях
- Каждый ключевой файл приведён целиком, без `same as above` и без `...`
- ORM-слой использует только async API (если `{{orm}}` асинхронный)
- Health и readiness — отдельные endpoint-ы, не один общий
- Есть запускаемый пример теста на health, а не «добавьте сюда тесты»
- `README.md` содержит реальные команды запуска (`make`, `docker compose up`, `pytest`), а не плейсхолдеры

## Промт

```text
Выступай как senior backend/platform engineer. Сгенерируй production-ready шаблон сервиса на FastAPI, а не демо-проект.

Контекст:
- Имя сервиса: {{service_name}}
- Что делает сервис: {{domain_description}}
- Версия Python: {{python_version}}
- База данных: {{database}}
- ORM: {{orm}}
- Инструмент миграций: {{migration_tool}}
- Аутентификация: {{auth_mode}}
- Внешние интеграции: {{external_integrations}}
- Фоновые задачи / scheduler / workers: {{background_jobs}}
- Линтер: {{linter}}
- Тест-фреймворк: {{test_framework}}
- Целевой деплой: {{deployment_target}}
- Репозиторные соглашения команды: {{repo_conventions}}

Если для качественного ответа не хватает контекста — задай до 7 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Требования к шаблону:
1. Python {{python_version}}
2. FastAPI + Pydantic v2
3. ORM согласно {{orm}}, миграции согласно {{migration_tool}} (если не `none`)
4. Конфигурация через settings/env
5. Structured logging
6. Health и readiness endpoints (раздельные)
7. Базовая обработка ошибок и единый error response
8. Dockerfile + compose для локальной разработки (если `{{deployment_target}}` это предполагает)
9. {{linter}} + {{test_framework}}
10. README с запуском

Сконструируй структуру так, чтобы проект был удобен для роста:
- `app/api`
- `app/core`
- `app/db`
- `app/models`
- `app/repositories`
- `app/schemas`
- `app/services`
- `app/tasks`
- `tests`
- `alembic` (или каталог твоего migration_tool)

В ответе обязательно дай:
1. [обязательно] Короткие архитектурные решения и принятые допущения
2. [обязательно] Полное дерево каталогов
3. [обязательно] Содержимое ключевых файлов:
   - `app/main.py`
   - `app/core/config.py`
   - `app/core/logging.py`
   - `app/api/routes/health.py`
   - пример router / schema / model / repository / service
   - конфиг миграций (`alembic/env.py` или аналог)
   - `Dockerfile`
   - `compose.yaml` или `docker-compose.yml`
   - `.env.example`
   - `pyproject.toml`
   - `README.md`
4. [обязательно] Пример теста на health endpoint
5. [если останется место] TODO-список того, что команда должна допилить под конкретный домен

Не делай так:
- Синхронный `Session` вместо `AsyncSession`, если выбран async ORM.
- `from app.main import app` внутри `app/main.py` — циклический импорт.
- `COPY . .` в Dockerfile без `.dockerignore` (тащит `.git`, `__pycache__`, секреты).
- `# TODO: add tests` вместо реального теста на health.

Критически важно:
- Не делай игрушечную структуру
- Не прячь важные куски за "same as above"
- Если какие-то интеграции не заданы, создай расширяемые заглушки
- Не придумывай лишние зависимости

Формат ответа:
- Markdown
- Сначала краткая схема решений
- Затем file tree
- Затем код по файлам в отдельных fenced code blocks
```
