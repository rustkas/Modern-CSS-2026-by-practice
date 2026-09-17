**Глава 27. Эволюция Design System: поддержка, governance и развитие**

### Цель главы

Создать Design System — только начало.  
Настоящая сложность начинается потом: когда система живёт в нескольких продуктах, её используют разные команды, а требования постоянно меняются.

В этой главе мы разберём:

- Как организовать поддержку Nova в долгую.
- Как принимать решения об изменениях.
- Как не дать системе деградировать.
- Какие метрики и процессы помогают сохранять качество.

Это глава о зрелости Design System.

---

### Структура проекта на данном этапе

Структура остаётся production-ориентированной, как в Главе 26:

```
nova/
├── packages/
│   └── design-system/
│       ├── css/
│       │   ├── 00-reset.css
│       │   ├── 01-tokens.css
│       │   ├── 02-base.css
│       │   ├── 03-layout.css
│       │   ├── components/
│       │   │   ├── _buttons.css
│       │   │   ├── _badges.css
│       │   │   ├── _cards.css
│       │   │   ├── _forms.css
│       │   │   ├── _navigation.css
│       │   │   ├── _overlays.css
│       │   │   ├── _tabs.css
│       │   │   ├── _accordion.css
│       │   │   ├── _toast.css
│       │   │   ├── _skeleton.css
│       │   │   └── _pagination.css
│       │   ├── 04-components.css
│       │   ├── 05-utilities.css
│       │   ├── 06-animations.css
│       │   ├── 07-performance.css
│       │   ├── 08-a11y.css
│       │   └── main.css
│       ├── package.json
│       ├── README.md
│       └── CHANGELOG.md
│
├── apps/
│   └── dashboard/
│       ├── index.html
│       ├── style-guide.html
│       ├── js/
│       │   └── main.js
│       └── css/
│           └── app.css
│
├── docs/
│   ├── architecture.md
│   ├── tokens.md
│   ├── components.md
│   ├── theming.md
│   ├── contribution.md
│   └── governance.md              ← новый файл
│
├── package.json
├── stylelint.config.js
└── .stylelintignore
```

---

### Новый файл: `docs/governance.md`

```md
# Governance Nova Design System

## Кто принимает решения

- Изменения токенов и публичного API компонентов — только после обсуждения.
- Новые компоненты добавляются, если используются минимум в 2 сценариях/продуктах.
- Экспериментальные идеи сначала живут в product-слое, а не в ядре Design System.

## Типы изменений

### Patch
Исправления багов без изменения API.

### Minor
Новые компоненты, новые модификаторы, обратно-совместимые улучшения.

### Major
Ломающие изменения токенов, переименование классов, удаление компонентов.

## Правила стабильности

1. Не удаляем токены без major-версии.
2. Не меняем смысл существующих семантических токенов молча.
3. Любое breaking change описывается в CHANGELOG и migration notes.
4. Style Guide и документация обновляются в том же PR.

## Процесс релиза

1. Обновить код
2. Обновить документацию и Style Guide
3. Прогнать lint/build
4. Обновить CHANGELOG
5. Повысить версию
6. Опубликовать / слить в main
```

---

### Принципы долгой жизни системы

#### 1. Ядро должно оставаться тонким

В `packages/design-system` попадает только то, что:

- переиспользуется;
- стабильно;
- имеет понятный API;
- не привязано к одному продукту.

Всё остальное — в `apps/*`.

#### 2. Токены важнее компонентов

Компоненты можно заменить.  
Плохо спроектированные токены ломают всю систему.

Поэтому:

- сначала думаем о токенах;
- потом о компонентах;
- и только потом о новых эффектах.

#### 3. Обратная совместимость — по умолчанию

Каждое изменение задавайте вопросом:

> Сколько команд/экранов это сломает?

Если ответ «много» — делайте major и migration path.

#### 4. Документация — часть продукта

Нет документации = нет Design System.  
Style Guide, `docs/` и `CHANGELOG.md` поддерживаются наравне с кодом.

#### 5. Регулярный аудит

Раз в 1–2 месяца полезно проводить аудит:

- какие токены не используются;
- какие компоненты устарели;
- где появились «магические» значения;
- какие паттерны дублируются в продуктах.

---

### Содержание ключевых файлов

На этом этапе **CSS-ядро не расширяется новыми компонентами**.  
Мы фиксируем организационный слой поверх уже готовой технической базы.

#### `packages/design-system/css/main.css`

```css
@layer reset, tokens, base, layout, components, utilities, animations, performance, a11y, overrides;

@import url("00-reset.css") layer(reset);
@import url("01-tokens.css") layer(tokens);
@import url("02-base.css") layer(base);
@import url("03-layout.css") layer(layout);
@import url("04-components.css") layer(components);
@import url("05-utilities.css") layer(utilities);
@import url("06-animations.css") layer(animations);
@import url("07-performance.css") layer(performance);
@import url("08-a11y.css") layer(a11y);
```

#### `packages/design-system/css/04-components.css`

```css
@import url("components/_buttons.css");
@import url("components/_badges.css");
@import url("components/_cards.css");
@import url("components/_forms.css");
@import url("components/_navigation.css");
@import url("components/_overlays.css");
@import url("components/_tabs.css");
@import url("components/_accordion.css");
@import url("components/_toast.css");
@import url("components/_skeleton.css");
@import url("components/_pagination.css");
```

Остальные технические файлы сохраняют финальное состояние, достигнутое к Главе 26.

---

### Практический ритуал поддержки (ежемесячно)

1. Просмотреть новые UI-паттерны в продуктах.
2. Решить, что заслуживает попадания в Design System.
3. Проверить токены на согласованность.
4. Обновить Style Guide.
5. Закрыть технический долг (a11y, RTL, performance).
6. Опубликовать minor/patch при необходимости.

---

### Что мы получили в этой главе

- Модель governance для Design System.
- Понимание, как принимать изменения безопасно.
- Правила долгосрочной поддержки.
- Разделение ответственности между ядром и продуктами.
- Подход, который помогает системе не «умирать» через полгода.

---

### Итог

Технологии современного CSS дали нам мощный фундамент.  
Но устойчивость Design System определяется не только `@layer` и OKLCH, а дисциплиной изменений, документацией и процессом принятия решений.

После этой главы у вас есть не просто проект, а **операционная модель** его развития.