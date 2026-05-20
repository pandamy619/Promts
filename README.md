# Promts

> A bilingual library of LLM prompts for backend / platform engineering tasks.
> Двуязычная библиотека LLM-промтов для backend / platform задач.

---

## Choose your language / Выберите язык

<table>
<tr>
<td align="center" width="50%">

### 🇷🇺 Русский

[📖 Открыть индекс](./ru/README.md)

[Стандарты](./ru/standards.md) · [Шаблон](./ru/template.md)

</td>
<td align="center" width="50%">

### 🇬🇧 English

[📖 Open index](./en/README.md)

[Standards](./en/standards.md) · [Template](./en/template.md)

</td>
</tr>
</table>

---

## What's inside / Что внутри

20 production-grade prompts covering architecture, APIs, infrastructure, operations, and testing. Each prompt follows a single standard: explicit metadata, behavior for missing context, definition of done, anti-examples, and genre tag (single-shot / staged / critique / interview).

Heavy prompts (microservice architecture, FastAPI template, K8s, CI/CD) ship with a `{{mode}}` switch — `quick` for fast drafts, `staged` or `critique` for serious work that benefits from explicit phases.

20 production-grade промтов по архитектуре, API, инфраструктуре, операционке и тестированию. Каждый промт по единому стандарту: явные метаданные, поведение при нехватке контекста, definition of done, антипримеры, тег жанра (single-shot / staged / critique / interview).

Тяжёлые промты (архитектура микросервиса, FastAPI шаблон, K8s, CI/CD) поддерживают переключатель `{{mode}}` — `quick` для быстрых драфтов, `staged` или `critique` для серьёзной работы, которой нужны явные фазы.

---

## Structure / Структура

```
.
├── README.md          ← you are here / вы здесь (language picker)
├── ru/                ← Russian version / русская версия
│   ├── README.md      ← index / индекс
│   ├── standards.md
│   ├── template.md
│   └── prompt-*.md    × 20
└── en/                ← English version / английская версия
    ├── README.md
    ├── standards.md
    ├── template.md
    └── prompt-*.md    × 20
```

Each file has a 🇷🇺/🇬🇧 toggle at the top — one click takes you to the same file in the other language.

В каждом файле сверху — переключатель 🇷🇺/🇬🇧, один клик переводит на тот же файл на другом языке.

---

## License

(добавь позже / add later, e.g. MIT, Apache 2.0, CC-BY-SA)
