# Сравнение: state‑формат v0.8 vs новый DSL

Этот файл даёт краткое и наглядное сравнение двух форматов Serverless Workflow:

- **Классический формат v0.8** (state‑машина: `start`, `states`, `functions`).
- **Новый DSL** (декларативный список задач: `document`, `do`).

## 1. Сравнение структуры

| Критерий | v0.8 (state‑формат) | DSL (document/do) |
|---|---|---|
| Корневой блок | `id`, `specVersion`, `start`, `states`, `functions` | `document`, `do` |
| Основной элемент | `state` | `task` |
| Порядок выполнения | `transition` и `end` | порядок в `do` |
| Действия | `actions` + `functionRef` | `run` (например `run.shell`) |
| Цель формата | формальная state‑машина | читабельная декларация |

## 2. Минимальный пример: один шаг

### v0.8 (state‑формат)

```yaml
id: demo
specVersion: "0.8"
start: stepOne
states:
  - name: stepOne
    type: operation
    actions:
      - name: hello
        functionRef:
          refName: sayHello
    end: true

functions:
  - name: sayHello
    operation: "shell:echo Hello"
```

### DSL (новый формат)

```yaml
document:
  dsl: '1.0.3'
  namespace: demo
  name: demo
  version: '1.0.0'

do:
  - stepOne:
      run:
        shell:
          command: 'echo Hello'
```

## 3. Чем DSL удобнее для учебных workflow

- **Проще читать**: порядок задач соответствует порядку строк.
- **Меньше служебных полей**: нет `states` + `functions`.
- **Легче объяснять**: почти как список команд.

## 4. Когда полезен state‑формат

- Когда нужен строгий контроль переходов.
- Когда workflow содержит сложные ветвления или таймауты.
- Когда нужна максимальная совместимость со старой экосистемой.

## 5. Рекомендация для этого проекта

- **Агентные workflow** — в DSL (просто и прозрачно).
- **Учебные примеры** в `docs/workflows/` — можно держать и в классическом формате для сравнения.
