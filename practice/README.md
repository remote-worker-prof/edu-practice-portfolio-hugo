# Практическая работа № 1
## Тема
Создание сайта на Hugo с публикацией в GitHub Pages и автоматическим деплоем через GitHub Actions.

## Цели
- Научиться создавать статический сайт с Hugo.
- Освоить базовый Git‑флоу с регулярными коммитами и пушем в GitHub.
- Настроить автоматическую публикацию сайта в GitHub Pages после каждого коммита.

## Ожидаемый результат
- Рабочий статический сайт на Hugo.
- Репозиторий на GitHub с историей осмысленных коммитов.
- Автоматический деплой в GitHub Pages через GitHub Actions, срабатывающий при каждом пуше в основную ветку.

## Предварительные требования
- Аккаунт GitHub и доступ к созданию репозиториев. (см. официальные требования Hugo к деплою на GitHub Pages)
- Установлены Git и **Hugo Extended** (расширенная версия) не ниже 0.146.0.
- Умение работать в командной строке.

## Почему именно Hugo Extended
Hugo Extended нужен для поддержки SCSS/Sass (пайплайн обработки стилей). Многие темы и шаблоны используют SCSS, поэтому без расширенной версии сборка может не пройти. Проверка: `hugo version` — в выводе должно быть `+extended`.

## Задание (пошагово)
1. Определите тип сайта GitHub Pages.
   - Если нужен сайт профиля/организации, репозиторий должен называться `<owner>.github.io` и сайт откроется по адресу `https://<owner>.github.io/`.
   - Если нужен сайт проекта, репозиторий может называться произвольно, а адрес будет `https://<owner>.github.io/<repositoryname>/`.
2. Создайте репозиторий на GitHub под выбранный тип сайта.
3. Создайте новый Hugo‑сайт локально (ориентируйтесь на Hugo Quick Start). Используйте **Hugo Extended**. Минимальный путь:
   - `hugo new site <site>`
   - `cd <site>`
   - `git init`
   - подключите тему (можно Ananke из официального Quick Start)
   - `hugo server` для локального просмотра
4. Добавьте контент (минимум 3 страницы/поста). Убедитесь, что публикации не остаются в `draft = true`.
5. Настройте `baseURL` в `hugo.toml` (должен начинаться с протокола и заканчиваться `/`).
6. Настройте кэш изображений Hugo через `cacheDir` (см. `templates/cache-config.md`).
7. Свяжите локальный репозиторий с GitHub и сделайте первый осмысленный коммит.
8. Добавьте workflow GitHub Actions для деплоя (используйте `templates/hugo.yaml`).
9. В настройках GitHub репозитория выберите `Settings → Pages → Source: GitHub Actions`.
10. Запушьте изменения и дождитесь успешного выполнения workflow. После этого сайт должен быть доступен по адресу GitHub Pages. Каждый следующий пуш должен автоматически публиковать изменения.

## Требования к коммитам
- Минимум 4 осмысленных коммита.
- Коммит‑сообщения отражают смысл изменений (например: `init hugo site`, `add content`, `configure pages`, `add deploy workflow`).

## Требования к workflow GitHub Actions
- В workflow должны использоваться `actions/configure-pages`, `actions/upload-pages-artifact`, `actions/deploy-pages`.
- Деплой‑job должен иметь разрешения `pages: write` и `id-token: write`, быть связан с build‑job через `needs`, и использовать окружение `github-pages` с `url` из `page_url`.

## Что сдавать
- Ссылку на репозиторий GitHub.
- Ссылку на опубликованный сайт GitHub Pages.
- Короткое описание, что реализовано (1–2 абзаца).

## Критерии оценки
См. `rubric.md`.

## Официальные источники
- Hugo Quick Start: https://gohugo.io/getting-started/quick-start/
- Hugo: Host on GitHub Pages: https://gohugo.io/host-and-deploy/host-on-github-pages/
- GitHub Pages: https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages
- GitHub Actions для Pages: https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
