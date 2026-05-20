# Contributing / Как контрибьютить

> 🇷🇺 [Русский](#на-русском) · 🇬🇧 [English](#in-english)

---

## In English

Thanks for considering a contribution. This repo is a bilingual library of LLM prompts for backend / platform engineering tasks. All prompts follow a single standard so the library stays consistent.

### How to add a new prompt

1. **Read the standard** — [`en/standards.md`](./en/standards.md). It defines the canonical structure for every prompt card.
2. **Copy the template** — [`en/template.md`](./en/template.md). Save as `en/prompt-<topic>.md` in kebab-case.
3. **Pick a genre** — `#single-shot` for simple tasks, `#staged` for plan-then-execute, `#critique` for draft-review-improve, `#interview` when the model needs to ask first. See standards section 4.
4. **Fill in metadata** — Owner, Last edit (ISO date), Version (start at `v1`), Source idea + Delta from original, Status (start at `📋 Template` or `🧪 Experimental`).
5. **Write the prompt body** following all the structural requirements: canonical "missing context" block, `Definition of done` (universal + specific), `Don't do this:` anti-examples, `[required]` / `[if room remains]` section tags for heavy prompts.
6. **Mirror to `/ru/`** — translate to Russian, preserve the same structure, update the language switcher link to point back to `/en/`.
7. **Update both `README.md` files** — add the new prompt to the navigation table in the right topic section, with genre column filled.
8. **Pre-commit checklist** is at the bottom of `standards.md` — run through it before opening a PR.

### How to edit an existing prompt

1. Bump `Version` (e.g., `v1` → `v2`).
2. Update `Last edit` to today's ISO date.
3. Append a short note to `Delta from original` describing what changed (e.g., `v2: added staged mode via {{mode}}`).
4. If the change affects genre — update the genre tag and the `README.md` navigation column.
5. **Mirror to the other language** — RU and EN versions must stay in sync. If the change is non-trivial, it's fine to mark the other-language version as `🧪 Experimental` until reviewed.
6. Add an entry to `CHANGELOG.md`.

### Style guide

- **Placeholders**: English `snake_case` in double curlies, with type + example in `What to substitute`.
- **Technical terms**: keep in English (`auth`, `idempotency`, `rollback`).
- **Prose**: natural English in `/en/`, natural Russian in `/ru/`. Don't translate idioms literally.
- **Concrete over abstract**: `"add alert on DB connection pool > 80% — owner platform, P1, due 2026-06-01"` instead of `"improve monitoring"`. The standard explicitly forbids vague phrasing.
- **No `same as above`** — repeat content if needed. Readers may land on any section directly.

### Submitting

- Issues: report a bug, suggest a new prompt topic, or flag a broken link.
- PRs: small focused changes preferred (one prompt = one PR). Reference the standards section your change touches if non-obvious.

---

## На русском

Спасибо, что думаешь о контрибьюшене. Этот репозиторий — двуязычная библиотека LLM-промтов для backend / platform задач. Все промты построены по единому стандарту, чтобы библиотека оставалась согласованной.

### Как добавить новый промт

1. **Прочитай стандарт** — [`ru/standards.md`](./ru/standards.md). Он задаёт каноническую структуру каждой карточки.
2. **Скопируй шаблон** — [`ru/template.md`](./ru/template.md). Сохрани как `ru/prompt-<тема>.md` в kebab-case (имя файла — английское, содержимое — русское).
3. **Выбери жанр** — `#single-shot` для простых задач, `#staged` для plan-then-execute, `#critique` для draft-review-improve, `#interview` если модель должна сначала спрашивать. См. раздел 4 стандарта.
4. **Заполни метаданные** — Owner, Последняя правка (ISO дата), Версия (начни с `v1`), Источник идеи + Дельта от оригинала, Статус (начни с `📋 Template` или `🧪 Experimental`).
5. **Напиши тело промта** по всем требованиям: канонический блок «при нехватке контекста», `Definition of done` (универсальный + специфика), `Не делай так:` антипримеры, теги `[обязательно]` / `[если останется место]` для тяжёлых промтов.
6. **Зеркаль в `/en/`** — переведи на английский, сохрани структуру, обнови переключатель на ссылку обратно на `/ru/`.
7. **Обнови оба `README.md`** — добавь новый промт в навигационную таблицу в правильную тематическую секцию, с заполненной колонкой жанра.
8. **Pre-commit чек-лист** — в конце `standards.md`. Прогони его перед PR.

### Как править существующий промт

1. Бампни `Версия` (`v1` → `v2`).
2. Обнови `Последняя правка` на сегодняшнюю ISO дату.
3. Добавь короткую заметку в `Дельта от оригинала`, что именно изменилось (например, `v2: добавлен staged режим через {{mode}}`).
4. Если изменение затрагивает жанр — обнови тег жанра и колонку в `README.md`.
5. **Зеркаль на другой язык** — RU и EN версии должны быть синхронны. Если изменение крупное, можно временно пометить другую версию как `🧪 Experimental` до ревью.
6. Добавь запись в `CHANGELOG.md`.

### Style guide

- **Плейсхолдеры**: английский `snake_case` в двойных скобках, с типом + примером в блоке «Что подставить».
- **Технические термины**: оставляй на английском (`auth`, `idempotency`, `rollback`).
- **Текст**: естественный английский в `/en/`, естественный русский в `/ru/`. Идиомы не переводи буквально.
- **Конкретика лучше абстракции**: `«добавить алерт на DB connection pool > 80% — owner platform, P1, due 2026-06-01»` вместо `«улучшить мониторинг»`. Стандарт прямо запрещает vague формулировки.
- **Никаких `same as above`** — лучше повтори. Читатель может прийти прямо в раздел.

### Как присылать изменения

- Issue: баг, предложение новой темы, битая ссылка.
- PR: предпочтительно маленькие сфокусированные изменения (один промт = один PR). Если меняешь нетривиально — сошлись на раздел стандарта, который трогаешь.
