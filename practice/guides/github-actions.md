# GitHub Actions: подробный учебник по автоматизации

Этот учебник объясняет принципы GitHub Actions, синтаксис workflow и практику их применения для деплоя Hugo на GitHub Pages.

## 1. Что такое GitHub Actions

GitHub Actions — это механизм CI/CD внутри GitHub. Он запускает workflow в ответ на события (push, pull request, ручной запуск) и выполняет набор шагов.

Основные понятия:

- **Workflow** — YAML‑файл в `.github/workflows/`.
- **Job** — набор шагов, выполняемых на отдельном runner.
- **Step** — отдельная команда или действие (`run` или `uses`).
- **Action** — готовый блок логики, который можно переиспользовать.

## 2. События запуска (`on`)

В workflow можно задавать несколько событий:

- `push` — запуск при пуше в ветку.
- `pull_request` — запуск при PR.
- `workflow_dispatch` — ручной запуск из интерфейса GitHub.

Пример:

```yaml
on:
  push:
    branches: [main]
  workflow_dispatch:
```

## 3. Jobs и steps

Каждый job исполняется на отдельной машине (runner). Внутри job идет список шагов. Примерный каркас:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Build
        run: hugo
```

## 4. Permissions и безопасность

По умолчанию workflow получает `GITHUB_TOKEN` с ограниченными правами. Важные моменты:

- Права можно задавать на уровне workflow или job.
- Для GitHub Pages нужны `pages: write` и `id-token: write`.
- Принцип минимальных прав: выдавайте только то, что реально требуется.

## 5. Контексты и выражения

GitHub Actions использует контексты (`github`, `env`, `steps`) и выражения `${{ ... }}` для подстановок. Это позволяет:

- брать значения из событий
- использовать результаты шагов
- настраивать переменные окружения

Пример:

```yaml
- name: Build
  run: hugo --baseURL "${{ steps.pages.outputs.base_url }}/"
```

## 6. Практика для GitHub Pages

Pages‑workflow обычно разделяет сборку и деплой:

- `build` — сборка сайта и загрузка артефакта.
- `deploy` — публикация артефакта в Pages.

Плюсы:

- безопасное разделение ответственности
- четкое управление доступами
- предсказуемая трассировка ошибок

## 7. Практика для этого репозитория

В `.github/workflows/hugo.yaml`:

- используется `actions/configure-pages`, который возвращает `base_url`.
- применяется `actions/upload-pages-artifact` для загрузки `public/`.
- деплой выполняется `actions/deploy-pages`.

Эти шаги соответствуют официальным рекомендациям GitHub Pages.

## 8. Частые ошибки

- Workflow не запускается — проверьте ветку в `on.push.branches`.
- Ошибка прав — убедитесь, что `permissions` заданы правильно.
- Пустой сайт — проверьте `baseURL` и что Hugo генерирует `public/`.

## Sources

Официальные материалы:

- https://docs.github.com/actions/using-workflows/workflow-syntax-for-github-actions
- https://docs.github.com/en/actions/using-workflows/about-workflows
- https://docs.github.com/en/actions/reference/workflows-and-actions/contexts
- https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
