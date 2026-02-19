# Язык шаблонов Hugo: подробный учебник с примерами

Этот учебник объясняет, как устроены шаблоны в Hugo, как писать их правильно и как применять в проекте. В Hugo используется синтаксис Go Templates, дополненный собственными функциями и правилами выбора шаблонов.

## 1. Что такое шаблоны в Hugo

Шаблоны — это набор HTML‑файлов с управляющими конструкциями (условия, циклы, подстановки), которые Hugo заполняет данными страницы и сайта. В Hugo есть:

- **Базовые шаблоны** (`baseof.html`) и **блоки** (`block`, `define`).
- **Типы шаблонов** (single, list, home, section, taxonomy, term и др.).
- **Частичные шаблоны** (partials) для переиспользования.
- **Shortcodes** для встраивания логики в контент.

Hugo использует Go `text/template` и `html/template`, поэтому шаблоны имеют знакомую синтаксис‑модель: `{{ ... }}`.

## 2. Где лежат шаблоны и как они выбираются

Основные директории:

- `layouts/` — шаблоны проекта.
- `themes/<theme>/layouts/` — шаблоны темы.

Hugo объединяет проект и тему в общий «виртуальный» слой. Если файл существует в проекте и в теме, используется **файл из проекта**. Это позволяет безопасно переопределять шаблоны темы.

Выбор конкретного шаблона происходит по правилам **lookup order** — это системный порядок, который учитывает тип страницы, раздел, язык, формат и т.д.

## 3. Типы шаблонов

Основные типы страниц и соответствующие шаблоны:

- **Single** — одиночная страница (например, проект). Обычно `layouts/_default/single.html`.
- **List** — список страниц раздела. Обычно `layouts/_default/list.html`.
- **Home** — главная страница `layouts/index.html`.
- **Section** — отдельный раздел.
- **Taxonomy / Term** — страницы таксономий.

Практика: для портфолио чаще всего используются `single` для карточек проектов и `list` для разделов.

## 4. Синтаксис Go Templates: основные конструкции

### 4.1. Подстановка значений

```html
<h1>{{ .Title }}</h1>
<p>{{ .Summary }}</p>
```

`.Title` и `.Summary` — свойства текущей страницы.

### 4.2. Условия

```html
{{ if .Params.featured }}
  <span class="badge">Featured</span>
{{ else }}
  <span class="badge">Project</span>
{{ end }}
```

### 4.3. Циклы

```html
<ul>
  {{ range .Pages }}
    <li><a href="{{ .RelPermalink }}">{{ .Title }}</a></li>
  {{ end }}
</ul>
```

### 4.4. Контекст и `.`

`{{ . }}` — это «текущий контекст». При `range` и `with` контекст меняется.

```html
{{ with .Params.cover }}
  <img src="{{ . }}" alt="Cover">
{{ end }}
```

Если нужен доступ к исходному контексту, используйте `$`:

```html
{{ $page := . }}
{{ range .Pages }}
  <a href="{{ .RelPermalink }}">{{ .Title }}</a>
  <!-- доступ к корню -->
  {{ $page.Params.siteTagline }}
{{ end }}
```

## 5. Переменные, словари и пайплайны

### 5.1. Переменные

```html
{{ $count := len .Pages }}
<p>Всего проектов: {{ $count }}</p>
```

### 5.2. Словари (dict)

```html
{{ $data := dict "title" "Проекты" "count" (len .Pages) }}
<h2>{{ $data.title }} ({{ $data.count }})</h2>
```

### 5.3. Пайплайны

```html
{{ "My Title" | lower | urlize }}
```

Пайплайны позволяют делать цепочки обработки значений.

## 6. Функции Hugo

Hugo расширяет Go Templates большим набором функций:

- `where`, `sort`, `first`, `last` — фильтрация и сортировка.
- `time`, `dateFormat` — работа с датами.
- `markdownify`, `safeHTML` — управление рендерингом.
- `partial`, `partialCached` — подключение частичных шаблонов.

Пример фильтрации:

```html
{{ range where .Pages "Section" "docs" }}
  <a href="{{ .RelPermalink }}">{{ .Title }}</a>
{{ end }}
```

## 7. Partials: переиспользование кода

Частичные шаблоны лежат в `layouts/_partials/`.

Пример `layouts/_partials/card.html`:

```html
<article class="card">
  <h3><a href="{{ .RelPermalink }}">{{ .Title }}</a></h3>
  <p>{{ .Summary }}</p>
</article>
```

Подключение:

```html
{{ range .Pages }}
  {{ partial "card.html" . }}
{{ end }}
```

Если нужно передать несколько значений, используйте `dict`:

```html
{{ partial "card.html" (dict "page" . "variant" "compact") }}
```

## 8. Base templates и блоки

Базовый шаблон (`baseof.html`) задаёт каркас страниц и содержит блоки:

```html
<!doctype html>
<html>
  <body>
    {{ block "main" . }}{{ end }}
  </body>
</html>
```

В дочернем шаблоне:

```html
{{ define "main" }}
  <h1>{{ .Title }}</h1>
  {{ .Content }}
{{ end }}
```

Это основной способ разделить «каркас» и «контент».

## 9. Lookup order: как выбирается шаблон

Hugo выбирает шаблон по строгому порядку. Он учитывает:

- тип страницы (single/list/home/section),
- раздел (например, `docs`),
- язык,
- формат.

Поэтому часто создают специальные шаблоны вида:

```
layouts/docs/single.html
layouts/_default/single.html
```

Hugo сначала проверит более специфичный вариант, затем fallback‑шаблон.

## 10. Shortcodes

Shortcodes — это мини‑шаблоны, которые вставляются прямо в Markdown.

Пример `layouts/shortcodes/figure.html`:

```html
<figure>
  <img src="{{ .Get "src" }}" alt="{{ .Get "alt" }}">
  <figcaption>{{ .Get "caption" }}</figcaption>
</figure>
```

Использование в контенте:

```md
{{< figure src="/img/demo.png" alt="Demo" caption="Пример" >}}
```

## 11. Render hooks (кратко)

Render hooks позволяют переопределять, как Hugo рендерит Markdown‑элементы (заголовки, изображения, ссылки). Они живут в `layouts/_default/_markup/`. Это удобно для единого оформления контента.

## 12. Данные из `data/`

Файлы в `data/` можно читать из шаблонов через `.Site.Data`.

Пример `data/skills.yaml`:

```yaml
- name: UX Research
- name: Frontend
```

Использование:

```html
<ul>
  {{ range .Site.Data.skills }}
    <li>{{ .name }}</li>
  {{ end }}
</ul>
```

## 13. Частые ошибки

- Забыли `.` при передаче контекста в partial.
- Разные пути шаблонов в теме и проекте.
- Неправильная вложенность `range` и `with`, из‑за чего теряется контекст.
- Ошибки в `lookup order` приводят к выбору не того шаблона.

## 14. Практика для этого репозитория

- Все основные шаблоны живут в `layouts/`.
- Дополнительные блоки (например, header/футер) подключаются через частичные шаблоны.
- Языковая логика управляется через `Site.Language`, а текстовые параметры через `languages.<lang>.params` в `config.toml`.

## 15. Рекомендуемые гайды и документация

Топовые источники для изучения:

- https://gohugo.io/templates/introduction/
- https://gohugo.io/templates/lookup-order/
- https://gohugo.io/templates/types/
- https://gohugo.io/functions/
- https://gohugo.io/templates/partials/
- https://gohugo.io/templates/shortcode-templates/
- https://gohugo.io/templates/overview/
- https://pkg.go.dev/text/template
- https://pkg.go.dev/html/template

## Sources

Официальные материалы Hugo и Go:

- https://gohugo.io/templates/introduction/
- https://gohugo.io/templates/lookup-order/
- https://gohugo.io/templates/types/
- https://gohugo.io/functions/
- https://gohugo.io/templates/partials/
- https://gohugo.io/templates/shortcode-templates/
- https://gohugo.io/templates/overview/
- https://pkg.go.dev/text/template
- https://pkg.go.dev/html/template
