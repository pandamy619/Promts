# Промт — Threat modeling

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-threat-modeling.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #threat-modeling #security #review #single-shot
**Статус:** `🧪 Experimental`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [Microsoft — STRIDE Threat Modeling](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats), [OWASP — Threat Modeling Process](https://owasp.org/www-community/Threat_Modeling_Process)
**Дельта от оригинала:** обязательная DFD (или явное перечисление trust boundaries) до STRIDE-анализа, требование likelihood × impact матрицы и явный раздел residual risks. Кандидат на переписывание в `#critique` жанр (см. бэклог).

---

## Когда использовать

- Перед запуском нового сервиса или существенным изменением в существующем
- Перед security review или compliance аудитом
- Когда команда не уверена, какие угрозы реальны, а какие теоретические

## Что подставить

- `{{system_description}}` — string, e.g. `payments-api принимает запросы от mobile/web, валидирует, отправляет в Stripe, сохраняет в Postgres, эмитит event в Kafka`
- `{{data_classification}}` — string, e.g. `PCI DSS (PAN, CVV); GDPR (PII); secrets (API keys); internal (logs)`
- `{{trust_boundaries}}` — list, e.g. `internet → API gateway; gateway → service; service → DB; service → Stripe; service → Kafka`
- `{{entry_points}}` — list, e.g. `POST /v1/payments (public, authed); GET /v1/admin/payments (internal, JWT scope admin); webhook от Stripe (public, signature-verified)`
- `{{assets_at_risk}}` — list, e.g. `платёжные данные клиентов; история транзакций; API keys; user PII`
- `{{compliance_context}}` — string, e.g. `PCI DSS SAQ-D; GDPR; SOC 2 Type II`
- `{{attacker_profile}}` — string, e.g. `internet attacker (low skill); insider (employee); compromised dependency; nation-state (out of scope)`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- DFD приведена (текстом или Mermaid), с явно отмеченными trust boundaries
- STRIDE проработан для каждой trust boundary, не «в целом по системе»
- Каждый risk имеет likelihood (Low/Med/High), impact (Low/Med/High) и mitigation (existing / proposed / accepted)
- Указаны residual risks — что НЕ закрыто и почему
- Для каждой mitigation сказано, как verify, что она работает

## Промт

```text
Выступай как Principal Security Engineer с опытом threat modeling в финтех / health / e-commerce. Проведи threat modeling для сервиса по методологии STRIDE.

Контекст:
- Описание системы: {{system_description}}
- Классификация данных: {{data_classification}}
- Trust boundaries: {{trust_boundaries}}
- Точки входа: {{entry_points}}
- Активы под угрозой: {{assets_at_risk}}
- Compliance контекст: {{compliance_context}}
- Профиль атакующего: {{attacker_profile}}

Если для качественного ответа не хватает контекста — задай до 7 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь:

1. [обязательно] System decomposition:
   - DFD (Mermaid или текстом): компоненты, потоки данных, trust boundaries
   - явно отмеченные пересечения trust boundaries (где данные пересекают границу доверия)
2. [обязательно] STRIDE-анализ для КАЖДОЙ trust boundary:
   - **S**poofing (выдать себя за кого-то)
   - **T**ampering (изменение данных в пути или в покое)
   - **R**epudiation (отрицание действия)
   - **I**nformation disclosure (утечка данных)
   - **D**enial of service
   - **E**levation of privilege
   Для каждого: конкретная угроза (не «возможен DoS», а «атакующий перегружает /v1/payments через брутфорс»)
3. [обязательно] Risk matrix:
   - таблица: Threat | Likelihood (L/M/H) | Impact (L/M/H) | Risk score | Status
   - status: existing_mitigation / proposed_mitigation / accepted_risk
4. [обязательно] Mitigations:
   - для каждой угрозы middlepriority (L+H, M+M, H+L) и выше:
     - что уже реализовано
     - что предлагается добавить
     - как verify (тест, аудит, мониторинг)
5. [обязательно] Compliance mapping:
   - какие mitigations покрывают какие compliance требования
   - где есть пробелы относительно требований
6. [обязательно] Residual risks:
   - что НЕ закрыто и почему
   - кто owner-ит accepted risk (нужно подтверждение)
7. [обязательно] Action items:
   - таблица: что | owner | severity (P0-P2) | due date
   - не «улучшить безопасность», а «добавить rate limiting на /v1/payments — 100 req/min per IP — owner platform, P1, due 2026-06-15»

Не делай так:
- STRIDE «в целом по системе» — он работает по boundaries, не по всему сразу.
- «Возможен DoS» — без конкретного attacker action.
- Mitigation без verification — как ты узнаешь, что она работает в проде?
- Threat без classification данных, которые он угрожает.
- Все risks как «high» — без приоритизации это бесполезно.
- Игнорировать insider threat, если это не «out of scope» явно.

Формат ответа:
- DFD (Mermaid) первой
- Таблица «Trust boundary | Threats (STRIDE) | Likelihood | Impact | Mitigation | Verification»
- Compliance gap mapping отдельно
- Residual risks отдельно
- Action items в виде таблицы
```
