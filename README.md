# Claude Best Practices

Моя личная коллекция скиллов, конфигов и лучших практик для [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview). Автор: [Роман Пуртов](https://roman-purtow.ru)

## Скиллы

| Скилл | Описание |
|-------|----------|
| [article-extractor](./skills/article-extractor) | Извлечение статей из URL в Markdown (crawl4ai, JS-рендеринг, SPA) |
| [building-wireframes](./skills/building-wireframes) | Генерация lo-fi макетов в HTML с Tailwind CSS |
| [changelog-tracker](./skills/changelog-tracker) | Отслеживание обновлений Claude Code с умными дайджестами |
| [cleanup-nul-files](./skills/cleanup-nul-files) | Удаление файлов `nul` из проекта (артефакт Windows) |
| [committing-changes](./skills/committing-changes) | Коммит и пуш с автогенерацией сообщения |
| [copywriting](./skills/copywriting) | Написание и улучшение маркетинговых текстов для лендингов и страниц |
| [find-skills](./skills/find-skills) | Поиск и установка скиллов по описанию задачи |
| [frontend-design](./skills/frontend-design) | Создание production-grade фронтенд-интерфейсов с высоким качеством дизайна |
| [ilyahov-writer](./skills/ilyahov-writer) | Написание текстов в информационном стиле Ильяхова («Пиши, сокращай») |
| [nextjs-app-router-patterns](./skills/nextjs-app-router-patterns) | Паттерны Next.js 14+ App Router: Server Components, streaming, параллельные роуты |
| [playwright-cli](./skills/playwright-cli) | Автоматизация браузера: тестирование, скриншоты, заполнение форм |
| [pptx](./skills/pptx) | Создание, чтение и редактирование PowerPoint-презентаций (.pptx) |
| [programmatic-seo](./skills/programmatic-seo) | Создание SEO-страниц массово по шаблонам и данным |
| [restarting-server](./skills/restarting-server) | Перезапуск Node.js dev-сервера с очисткой кэша (Vite, Next.js, Nuxt, Remix и др.) |
| [seo-audit](./skills/seo-audit) | Аудит и диагностика SEO-проблем сайта |
| [skill-creator](./skills/skill-creator) | Гайд по созданию новых скиллов для Claude Code |
| [sorting-bookmarks](./skills/sorting-bookmarks) | Сортировка и категоризация закладок браузера из HTML-файла |
| [tailwind-design-system](./skills/tailwind-design-system) | Дизайн-системы на Tailwind CSS: токены, компоненты, паттерны |
| [tailwind-v4-shadcn](./skills/tailwind-v4-shadcn) | Настройка Tailwind v4 + shadcn/ui с CSS-переменными и dark mode |
| [ui-ux-pro-max](./skills/ui-ux-pro-max) | Комплексное руководство по дизайну UI/UX (50+ стилей, палитры, шрифты, стеки) |
| [vercel-react-best-practices](./skills/vercel-react-best-practices) | React/Next.js оптимизация производительности от Vercel Engineering |
| [web-design-guidelines](./skills/web-design-guidelines) | Ревью UI-кода по Web Interface Guidelines |

## Промпты

| Промпт | Описание |
|--------|----------|
| [humanize-ai-text](./prompts/humanize-ai-text.md) | Переписывание текста для обхода ИИ-детекторов (Grammarly, GPTZero, Turnitin) |

## Конфигурация

В директории [`claude-config/`](./claude-config) находятся конфигурационные файлы Claude Code:

- **[`settings.json`](./claude-config/settings.json)** — настройки: язык общения, канал обновлений, переменные окружения
- **[`CLAUDE.md`](./claude-config/CLAUDE.md)** — глобальные инструкции: язык, Windows-специфика (`/dev/null` вместо `nul`), автоочистка артефактов, интеграция с Context7 MCP, декомпозиция задач
- **[`mcp-setup.md`](./claude-config/mcp-setup.md)** — гайд по настройке MCP-серверов (Context7, Chrome DevTools и др.)

## Установка

Скиллы устанавливаются по [официальной документации Anthropic](https://docs.anthropic.com/en/docs/claude-code/skills).

## Использование

После установки скиллы вызываются слэш-командами в Claude Code:

```
/building-wireframes — создать wireframe
/changelog-tracker   — посмотреть обновления Claude Code
/committing-changes  — закоммитить изменения
/copywriting         — написать маркетинговый текст
/ilyahov-writer      — написать статью в инфостиле
/playwright-cli      — автоматизировать браузер
/restarting-server   — перезапустить сервер
/seo-audit           — аудит SEO сайта
/sorting-bookmarks   — отсортировать закладки браузера
```

Или опишите задачу естественным языком:

> «Создай wireframe для лендинга SaaS-продукта»
> «Что нового в Claude Code?»
> «Сделай коммит всех текущих изменений»
> «Извлеки статью из этого URL»
> «Создай презентацию на 10 слайдов»

## Структура скиллов

```
skills/
├── skill-name/
│   ├── SKILL.md           # Определение скилла
│   ├── assets/            # Шаблоны, изображения
│   └── references/        # Дополнительная документация
```

## Лицензия

[MIT](LICENSE)
