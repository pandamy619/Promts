# Promts

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](./LICENSE)
[![Languages: RU + EN](https://img.shields.io/badge/languages-RU%20%2B%20EN-orange)](#choose-your-language--выберите-язык)
[![Prompts: 20](https://img.shields.io/badge/prompts-20-brightgreen)](#whats-inside--что-внутри)
[![Last commit](https://img.shields.io/github/last-commit/pandamy619/Promts)](https://github.com/pandamy619/Promts/commits/main)
[![PRs welcome](https://img.shields.io/badge/PRs-welcome-brightgreen)](./CONTRIBUTING.md)

> A bilingual library of LLM prompts for backend / platform engineering tasks.
> Двуязычная библиотека LLM-промтов для backend / platform задач.

---

## Choose your language / Выберите язык

<table>
<tr>

<td align="center" width="50%">

[📖 Open index](./en/README.md)

[Standards](./en/standards.md) · [Template](./en/template.md)

<td align="center" width="50%">

[📖 Открыть индекс](./ru/README.md)

[Стандарты](./ru/standards.md) · [Шаблон](./ru/template.md)

</td>


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

---

## Contributing / Контрибьютить

See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to add a new prompt, edit an existing one, and the style guide.

См. [CONTRIBUTING.md](./CONTRIBUTING.md) — как добавить новый промт, как править существующий, и style guide.

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for release history. См. [CHANGELOG.md](./CHANGELOG.md) для истории релизов.

## License

[MIT](./LICENSE) — feel free to copy, adapt, redistribute, and use in commercial products. Attribution appreciated but not required.

[MIT](./LICENSE) — можно копировать, адаптировать, распространять и использовать в коммерческих продуктах. Указание авторства приветствуется, но не обязательно.
