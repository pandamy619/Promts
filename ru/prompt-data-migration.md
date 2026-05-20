# Промт — Data migration / schema change

> 🇷🇺 **Русский** · [🇬🇧 English](../en/prompt-data-migration.md)

[← Index](./README.md) · [Стандарты](./standards.md) · [Шаблон](./template.md)

tags: #prompt #data-migration #database #schema #backend #single-shot
**Статус:** `🧪 Experimental`
**Owner:** Pandamy619
**Последняя правка:** 2026-05-20
**Версия:** v1
**Источник идеи:** [Stripe — Online migrations at scale](https://stripe.com/blog/online-migrations), [GitHub — Online schema changes with gh-ost](https://github.blog/2016-08-01-gh-ost-github-s-online-migration-tool-for-mysql/)
**Дельта от оригинала:** обязательная expand-contract стратегия для несовместимых изменений, явный backward-compatibility window, verification queries приведены конкретно (а не «проверить»), rollback — тоже скриптом.

---

## Когда использовать

- Когда нужно изменить схему БД на работающем сервисе под нагрузкой
- Когда переливаем данные между таблицами / БД / форматами
- Когда нельзя позволить downtime или он жёстко ограничен

## Что подставить

- `{{database_type}}` — enum: `postgres | mysql | mongodb | dynamodb | other`
- `{{table_or_collection}}` — string, e.g. `payments` (≈ 50M строк)
- `{{change_type}}` — enum: `add_column | drop_column | rename_column | change_type | split_table | merge_tables | add_index | backfill_data | denormalize | other`
- `{{data_volume}}` — string, e.g. `~50M строк, ~30 GB, growth 2M/мес`
- `{{traffic_pattern}}` — string, e.g. `200 RPS read, 50 RPS write; peak в 9-11 и 18-22 МСК`
- `{{rollback_requirement}}` — enum: `must_be_reversible | reversible_within_24h | one_way`
- `{{downtime_tolerance}}` — string, e.g. `zero downtime` / `до 5 минут в окно 02:00-04:00 UTC`

## Definition of done

Универсальный минимум — см. [Definition of done](./standards.md#2-definition-of-done).

Специфичные критерии:

- Стратегия названа явно (`expand-contract`, `pt-online-schema-change`, `gh-ost`, `blue-green`)
- Указано, какие application изменения должны быть задеплоены ДО схемы, какие — ПОСЛЕ
- Приведены конкретные SQL/команды для каждого шага, не «выполнить миграцию»
- Verification queries приведены с ожидаемыми результатами
- Rollback план — конкретные команды, не «вернуть всё как было»
- Указан реальный downtime estimate (даже если 0)

## Промт

```text
Выступай как Principal Database Engineer / DBA с опытом миграций на боевых системах. Подготовь подробный план миграции схемы / переливки данных.

Контекст:
- Тип БД: {{database_type}}
- Таблица / коллекция: {{table_or_collection}}
- Тип изменения: {{change_type}}
- Объём данных: {{data_volume}}
- Профиль трафика: {{traffic_pattern}}
- Требование к обратимости: {{rollback_requirement}}
- Допустимый downtime: {{downtime_tolerance}}

Если для качественного ответа не хватает контекста — задай до 7 коротких уточняющих вопросов одним сообщением. Если критичных пробелов нет — действуй с разумными дефолтами и в начале ответа приведи раздел `Допущения` со списком того, что ты предположил.

Подготовь:

1. [обязательно] Risk assessment:
   - что может сломаться
   - какие операции блокирующие (например, ACCESS EXCLUSIVE lock на PG)
   - влияние на репликацию, бэкапы, replication lag
2. [обязательно] Pre-migration checks:
   - текущий размер таблицы и индексов
   - active connections / long-running queries
   - free space на диске (минимум 2x размер таблицы для некоторых операций)
   - проверка backup
3. [обязательно] Стратегия миграции (с явным названием):
   - expand-contract (для несовместимых изменений)
   - online schema change (pt-osc, gh-ost для MySQL; CONCURRENTLY для PG индексов)
   - blue-green / shadow table
   - batched backfill (для больших data updates)
4. [обязательно] Application changes ordering:
   - что задеплоить ДО схемы (dual-read / dual-write для expand)
   - что задеплоить ПОСЛЕ (cleanup, переход на новые поля)
   - backward-compatibility window (сколько недель/деплоев)
5. [обязательно] Migration steps:
   - пронумерованные шаги с конкретным SQL / командой
   - какие выполняются вручную, какие в CI/CD
   - где обязательная пауза для verification
6. [обязательно] Verification queries:
   - count(*) до и после
   - sample comparison (старое поле vs новое)
   - consistency check (sum, checksum, или явный JOIN)
   - конкретные SQL с ожидаемыми результатами
7. [обязательно] Cutover plan:
   - когда переключаемся на новое поле / таблицу
   - как откатиться, если что-то пошло не так в первые N минут
8. [обязательно] Rollback plan:
   - конкретные SQL/команды для каждого шага
   - точка невозврата (после какого шага rollback дорогой)
9. [обязательно] Cleanup (после успешного cutover):
   - удаление старых полей / таблиц
   - выдержка: не раньше N дней после cutover
10. [обязательно] Downtime estimate и monitoring plan

Не делай так:
- `ALTER TABLE huge_table ADD COLUMN ... NOT NULL DEFAULT ...` на больших таблицах PG/MySQL без online schema tool — это блокирующая операция.
- `DROP COLUMN` до того, как application перестал его читать (нужен expand-contract).
- Migration без verification queries — потом не докажешь, что данные не побились.
- Rollback «если что — задеплоим старую версию приложения» — а данные?
- `CREATE INDEX` без `CONCURRENTLY` на PG production.
- Backfill одной транзакцией на миллионы строк — replication lag, long-running transaction.

Формат ответа:
- Risk assessment первым
- Стратегия и её обоснование
- Application ordering (что-до, что-после) — явная таблица
- Пронумерованные migration steps с SQL
- Verification queries — отдельный блок
- Rollback plan — отдельный блок
- Estimated timeline с буферами
```
