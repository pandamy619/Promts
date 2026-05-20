# Промт — WebSocket сервис

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-websocket.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #websocket #realtime #backend #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — WebSocket Service Generator](https://logicballs.com/ai-prompt/prompts/websocket-service-generator)
**Дельта от оригинала:** добавлен honest-альтернативы (SSE / polling), явное требование reconnect-устойчивости и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужен real-time сервис: чат, live updates, коллаборация, игровые события
- Когда надо продумать протокол, масштабирование и auth до старта реализации
- Когда REST уже не покрывает latency / push-сценарии

## Что подставить

- `{{programming_language}}` — string, e.g. `Python 3.13` / `Go 1.22`
- `{{framework}}` — string, e.g. `FastAPI + websockets` / `Starlette` / `gorilla/websocket`
- `{{use_case}}` — string, e.g. `чат для команд до 100 человек`
- `{{scale_requirements}}` — string, e.g. `~50k одновременных соединений; 500 msg/sec на comnnection peak`
- `{{auth_method}}` — enum: `jwt | oauth2 | api_key | session_cookie | signed_url`
- `{{persistence_requirement}}` — string, e.g. `last 100 messages в Redis; долгосрочный архив в S3`
- `{{infrastructure}}` — string, e.g. `Kubernetes, Redis Streams как backplane, ALB с sticky sessions`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Connection lifecycle описан с реальными сообщениями (примеры payload), не на уровне «есть heartbeat»
- Указан конкретный pub/sub backplane (Redis / NATS / Kafka), а не «какой-нибудь broker»
- Reconnect-стратегия проработана: что происходит с пропущенными сообщениями
- Если WebSocket не оптимален — явно предложена альтернатива (SSE, long polling) с обоснованием

## Промт

```text
Выступай как Principal Software Engineer по real-time distributed systems. Спроектируй и опиши production-ready WebSocket сервис.

Контекст:
- Язык: {{programming_language}}
- Framework: {{framework}}
- Use case: {{use_case}}
- Целевой масштаб: {{scale_requirements}}
- Метод аутентификации: {{auth_method}}
- Требования к хранению данных: {{persistence_requirement}}
- Инфраструктура: {{infrastructure}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Сгенерируй:
1. [обязательно] Общую архитектуру WebSocket-сервиса
2. [обязательно] Connection lifecycle:
   - connect
   - authenticate
   - subscribe
   - heartbeat
   - disconnect / reconnect
3. [обязательно] Message protocol:
   - inbound message types
   - outbound message types
   - error payloads
   - versioning
4. [обязательно] Масштабирование:
   - sticky sessions или нет
   - pub/sub backplane
   - fan-out стратегия
5. [обязательно] Надёжность:
   - retries
   - backpressure
   - rate limits
   - duplicate handling
6. [обязательно] Security:
   - auth
   - connection abuse prevention
   - input validation
7. [обязательно] Observability:
   - connection metrics
   - message latency
   - error rates
8. [если останется место] Пример структуры проекта и key handlers

Не делай так:
- Отсутствие heartbeat — соединения «висят» и забивают пул.
- Стейт коннекшена хранится только в памяти процесса (не выживает рестарт пода).
- Auth проверяется только на connect, без re-validate при долгих сессиях.
- Broadcast через цикл по всем connections без backpressure — один медленный клиент тормозит всех.

Требования:
- Избегай игрушечной реализации
- Учитывай, что real-time система часто ломается на reconnect и scale-out
- Если WebSocket здесь не лучший выбор, честно предложи SSE или polling

Формат ответа:
- Summary
- Архитектура
- Протокол сообщений
- Компоненты и infra
- Риски
```
