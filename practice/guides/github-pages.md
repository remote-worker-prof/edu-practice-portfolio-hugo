# GitHub Pages: публикация статического сайта

Это руководство объясняет, как публиковать сайт на GitHub Pages и как устроен процесс деплоя для Hugo.

## 1. Что такое GitHub Pages

GitHub Pages — сервис GitHub, который публикует статические сайты прямо из репозитория. Сайт может быть:

- **User/Organization site** — репозиторий с именем `<owner>.github.io`, адрес сайта `https://<owner>.github.io/`.
- **Project site** — любой репозиторий, адрес `https://<owner>.github.io/<repo>/`.

Выбор типа влияет на `baseURL` в Hugo и на ожидания по структуре ссылок.

## 2. Источник публикации

В современных настройках Pages можно выбирать источник публикации. Для сайтов, которые строятся через GitHub Actions, в настройках нужно выбрать:

`Settings → Pages → Source: GitHub Actions`

После этого деплой будет выполняться автоматическими workflow.

## 3. Почему GitHub Actions — базовый путь для Hugo

Hugo генерирует статический сайт. Чтобы публиковать сайт на Pages, нужно:

1. Запустить Hugo в CI.
2. Получить папку `public/` как артефакт.
3. Развернуть артефакт в GitHub Pages.

GitHub Actions дает контролируемую, повторяемую сборку, а также удобный контроль версий и логов. Это лучший вариант для учебных проектов, где важно показывать историю коммитов и автоматизацию.

## 4. Базовый workflow для Hugo Pages

Типичный workflow для Hugo включает шаги:

- checkout репозитория
- установка Hugo (extended)
- сборка `hugo --minify` с `baseURL` из `configure-pages`
- публикация артефакта через `actions/upload-pages-artifact`
- деплой через `actions/deploy-pages`

Ключевые требования:

- права `pages: write` и `id-token: write`
- разнесение `build` и `deploy` как отдельных jobs
- `deploy` зависит от `build` через `needs`

## 5. Кэш изображений и экономия времени

При сборке Hugo имеет смысл включать `cacheDir` в workflow. Это ускоряет повторные сборки и уменьшает нагрузку. В примере workflow используется папка `runner.temp` и `actions/cache`.

## 6. Практика для этого репозитория

В текущем репозитории:

- Workflow находится в `.github/workflows/hugo.yaml`.
- В шаге `configure-pages` вычисляется правильный `baseURL`.
- Сборка делает `hugo --minify` и кладет результат в `public/`.

Проверка публикации:

1. Откройте вкладку **Actions** и убедитесь, что workflow завершился успешно.
2. Перейдите в **Settings → Pages** и проверьте ссылку на сайт.
3. Откройте сайт в браузере и проверьте корректность ссылок.

## 7. SSH‑ключ и агент для публикации (WSL)

Чтобы `bd sync` или `git push` работали без запроса пароля, можно настроить `ssh-agent` и сохранить окружение агента в `~/.ssh/agent.env`. В WSL выполните:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519_github
printf 'export SSH_AUTH_SOCK=%s\nexport SSH_AGENT_PID=%s\n' "$SSH_AUTH_SOCK" "$SSH_AGENT_PID" > ~/.ssh/agent.env
chmod 600 ~/.ssh/agent.env
```

После этого ключ будет подхватываться из агента, а не требовать пароль каждый раз.

## 8. Если доступ к репозиторию с другого аккаунта

Если основной ключ SSH привязан к другому GitHub‑аккаунту, используйте отдельный host‑alias (например, `github-worker`) и замените remote на общий шаблон:

```bash
git remote set-url origin git@github-worker:<owner>/<repo>.git
git push -u origin main
```

## Sources

Официальные материалы:

- https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages
- https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
- https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
- https://gohugo.io/host-and-deploy/host-on-github-pages/
