# Промт — Kubernetes манифесты сервиса

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-kubernetes-manifests.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #kubernetes #devops #yaml #service #single-shot
**Статус:** `🟢 Ready`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [LogicBalls — Kubernetes Config Generator](https://logicballs.com/ai-prompt/prompts/kubernetes-config-generator)
**Дельта от оригинала:** добавлены требования к раздельным liveness/readiness probes, обязательные resources, PDB для критичных сервисов и канонический блок поведения при нехватке данных.

---

## Когда использовать

- Когда нужно быстро получить baseline для деплоя сервиса в Kubernetes
- Когда хочется не забыть probes, resources, securityContext и ingress
- Когда команда стандартизирует манифесты под новые сервисы

## Что подставить

- `{{application_name}}` — string, e.g. `payments-api`
- `{{container_image}}` — string, e.g. `registry.example.com/payments-api:v1.4.2`
- `{{namespace}}` — string, e.g. `payments`
- `{{environment_type}}` — enum: `dev | stage | prod`
- `{{replica_count}}` — int, e.g. `3`
- `{{resource_requirements}}` — string, e.g. `req cpu 100m / mem 256Mi; lim cpu 500m / mem 512Mi`
- `{{exposed_ports}}` — list, e.g. `8080 (http)`
- `{{environment_variables}}` — list, e.g. `DATABASE_URL; KAFKA_BROKERS; LOG_LEVEL`
- `{{storage_requirements}}` — string, e.g. `нет PVC` или `1 PVC 10Gi gp3`
- `{{ingress_requirements}}` — string, e.g. `payments.example.com, TLS, rate-limit 100rps`
- `{{security_constraints}}` — string, e.g. `runAsNonRoot, readOnlyRootFilesystem, no privileged`
- `{{additional_requirements}}` — string, e.g. `HPA на CPU 70%; PDB minAvailable 2`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Liveness и readiness — раздельные probe, не один и тот же endpoint
- У всех контейнеров проставлены `resources.requests` и `resources.limits`
- В prod-окружении есть `PodDisruptionBudget`, если сервис критичный
- securityContext: `runAsNonRoot: true`, `readOnlyRootFilesystem: true` (или явное обоснование, почему нет)
- Раздел «Что проверить перед продом» содержит минимум 5 пунктов

## Промт

```text
Выступай как senior Kubernetes architect. Сгенерируй production-grade Kubernetes manifests для сервиса.

Контекст:
- Имя приложения: {{application_name}}
- Контейнерный образ: {{container_image}}
- Namespace: {{namespace}}
- Тип окружения: {{environment_type}}
- Количество реплик: {{replica_count}}
- Ресурсы: {{resource_requirements}}
- Порты: {{exposed_ports}}
- Переменные окружения: {{environment_variables}}
- Требования к хранилищу: {{storage_requirements}}
- Ingress / exposure: {{ingress_requirements}}
- Ограничения безопасности: {{security_constraints}}
- Дополнительные требования: {{additional_requirements}}

Если для качественного ответа не хватает контекста — задай до 5 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь:
1. [если останется место] `Namespace` при необходимости
2. [обязательно] `Deployment`
3. [обязательно] `Service`
4. [обязательно] `ConfigMap` и `Secret` placeholders
5. [обязательно] `Ingress` или иной способ публикации
6. [если останется место] `HorizontalPodAutoscaler`, если он оправдан
7. [обязательно для prod] `PodDisruptionBudget`, если сервис критичный

Обязательно учти:
- readiness и liveness probes — раздельные
- requests / limits
- non-root запуск, если возможно
- rolling update strategy
- labels / selectors / naming conventions
- хранение конфигурации отдельно от образа
- примечания по безопасному хранению секретов

Не делай так:
- Deployment без `resources` requests и limits.
- Liveness probe = readiness probe (мешает rolling update и приводит к лишним рестартам).
- Секреты в `ConfigMap` или прямо в манифесте.
- `imagePullPolicy: Always` для тегированных образов — это лишний pull и потенциальная нестабильность.

Формат ответа:
1. Короткое summary решений
2. Таблица "Манифест / Назначение"
3. Полный YAML по ресурсам
4. Раздел "Что проверить перед продом"
```
