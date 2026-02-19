# Откуда взялся синтаксис `agents-issue-workflow.cncf.yaml`

Этот документ объясняет происхождение синтаксиса файла `agents-issue-workflow.cncf.yaml` и теорию, на которой он основан.

## 0. Предыстория: что такое CNCF и зачем они делают спецификации

**CNCF (Cloud Native Computing Foundation)** — это фонд под эгидой Linux Foundation, который объединяет разработчиков, пользователей и вендоров вокруг облачно‑нативных технологий. Миссия CNCF — сделать cloud‑native подходы повсеместными и поддерживать экосистему нейтральных open‑source проектов. Это означает, что CNCF не только поддерживает конкретные проекты, но и продвигает **общие стандарты и спецификации**, чтобы разные реализации могли быть совместимыми.

Почему CNCF занимается спецификациями:

- стандарты снижают фрагментацию рынка;
- спецификация задаёт общий язык для инструментов и команд;
- разные реализации могут быть взаимозаменяемыми и совместимыми.

В этом контексте и появилась **CNCF Serverless Workflow** — спецификация для описания workflow как переносимого и стандартизованного DSL.

## 1. Основа: Serverless Workflow DSL (новый формат)

Сейчас `agents-issue-workflow.cncf.yaml` и `agents-issue-workflow.no-sync.cncf.yaml` описаны в **Serverless Workflow DSL** (новой версии синтаксиса). Основной файл DSL включает блок `document` и список задач `do`. DSL описан в `dsl.md`, а точные поля и примеры — в `dsl-reference.md`.

Минимальная структура выглядит так:

```yaml
document:
  dsl: '1.0.3'
  namespace: example
  name: demo
  version: '1.0.0'
do:
  - stepOne:
      run:
        shell:
          command: 'echo \"Hello\"'
```

Поле `document.dsl` фиксирует версию DSL, а список `do` определяет последовательность задач. Порядок задач соответствует порядку декларации.

## 2. Tasks вместо states

В DSL **основной элемент — task**, а не state. Задачи перечисляются в `do` и выполняются по порядку. Для выполнения команд в нашем проекте используется тип задачи **Run**, который позволяет выполнять shell‑команды.

В нашем workflow каждый шаг — это отдельная задача в списке `do`, а выполнение команды описано в `run.shell.command`.

## 3. `run` и `shell`

DSL определяет задачу `run`, которая может запускать контейнеры, скрипты, shell‑команды или другие workflows. В нашем случае используется именно `run.shell` с полем `command`.

Это прямое и читабельное отображение этапов: `bd create` → `bd update` → `git add` → `git commit` → `bd close` → `bd sync`.

## 4. Локальные расширения

Поле `metadata` (внутри `document`) используется для **локальных расширений**. В нём хранится `x-agent`, который связывает workflow с инструкциями и помечает его как рабочий процесс агентной разработки. Такие `x-*` поля допустимы как расширения и не меняют базовую семантику DSL.

## 5. Связь с нашим процессом

В DSL‑версии наш workflow — это **последовательность задач**:

- `createIssue` → `markInProgress` → `doWork` → `stageChanges` → `commitChanges` → `closeIssue` → `syncBeads`.

Каждый шаг — это `run.shell.command`, а блок `do` гарантирует фиксированный порядок. Это проще для чтения, чем state‑машина в старом формате.

## 6. Почему в проекте появился DSL

DSL — это новое направление в Serverless Workflow, ориентированное на **читаемость и декларативность**. В отличие от старой state‑модели (v0.8), DSL ближе к естественному описанию процесса: список задач, выполняемых по порядку, с минимальным шумом.

## Sources

- https://www.cncf.io/about/who-we-are/
- https://serverlessworkflow.io/
- https://www.cncf.io/projects/serverless-workflow/
- https://github.com/serverlessworkflow/specification
- https://github.com/serverlessworkflow/specification/blob/main/dsl.md
- https://github.com/serverlessworkflow/specification/blob/main/dsl-reference.md
