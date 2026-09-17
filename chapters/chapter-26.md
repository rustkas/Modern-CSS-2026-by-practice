**Глава 26. Полностью современный проект — финальная сборка**

### Цель главы

Это кульминационная глава практического руководства.  
Здесь мы подтверждаем, что созданный проект Nova действительно является **полностью современным приложением на чистом CSS 2026** и использует все ключевые возможности, описанные в теоретической книге.

В этой главе мы:

- Соберём финальную картину проекта.
- Проверим, что все важные технологии действительно применены.
- Зафиксируем итоговое состояние файлов.
- Дадим короткий сценарий финальной проверки.

---

### Что должен уметь финальный проект

Согласно цели книги и нашего практического руководства, проект должен использовать:

- CSS Variables / Design Tokens
- `@layer`
- Container Queries
- `:has()`
- CSS Nesting
- Grid + Flexbox
- View Transition API
- Scroll-driven Animations
- Anchor Positioning
- `color-mix()`
- OKLCH
- Dark Theme (и дополнительные темы)
- Современную компонентную архитектуру

Всё это в Nova уже реализовано.

---

### Финальная структура проекта

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
│   └── contribution.md
│
├── package.json
├── stylelint.config.js
└── .stylelintignore
```

---

### Содержание ключевых файлов (финальное состояние)

#### 1. `packages/design-system/css/main.css`

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

#### 2. `packages/design-system/css/04-components.css`

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

#### 3. `packages/design-system/css/01-tokens.css`

Содержит:

- OKLCH-палитры
- Семантические токены
- Темы: `light`, `dark`, `ocean`, `high-contrast`
- Fluid-типографику (`clamp`)
- Dense-режим
- Spacing, radius, shadows, motion-токены

#### 4. Компоненты (`components/*.css`)

Реализованы и используют:

- CSS Nesting
- Design Tokens
- Логические свойства
- Состояния (`:hover`, `:focus-visible`, `:disabled`, `:has()`)
- Модификаторы по BEM-схеме
- Контракты через CSS Custom Properties

#### 5. `06-animations.css`

Содержит:

- View Transitions для смены темы
- Scroll-driven Animations (`animation-timeline: view()`, `scroll()`)
- Reveal-эффекты
- Micro-interactions
- Поддержку `prefers-reduced-motion`

#### 6. `07-performance.css`

Содержит:

- `contain`
- `content-visibility: auto`
- Аккуратную работу с `will-change`

#### 7. `08-a11y.css`

Содержит:

- Skip-link
- Единый `:focus-visible`
- Улучшения доступности

#### 8. `apps/dashboard/index.html`

Демонстрирует:

- Dashboard layout (Grid + Flexbox)
- Карточки с Container Queries
- Формы с `:has()`
- Tooltip и Dropdown (Anchor Positioning)
- Modal (Popover API)
- Tabs, Accordion, Toast
- Scroll-progress и reveal-анимации
- Переключение тем

#### 9. `apps/dashboard/style-guide.html`

Живая документация всех компонентов.

#### 10. `js/main.js`

Отвечает за:

- Переключение тем (с View Transitions)
- Dropdown
- Tabs
- Accordion
- Toast

---

### Финальный сценарий проверки проекта

Проверьте по порядку:

1. **Темы**  
   Переключаются light / dark / ocean / high-contrast / system.

2. **Компоненты**  
   Кнопки, формы, карточки, tabs, accordion, modal, toast работают.

3. **Адаптивность**  
   Карточки реагируют на ширину контейнера (Container Queries).

4. **Формы**  
   Валидация визуально работает через `:has()`.

5. **Оверлеи**  
   Tooltip и Dropdown позиционируются через Anchor Positioning.

6. **Анимации**  
   - Плавная смена темы (View Transitions)  
   - Появление блоков при скролле  
   - Progress-bar скролла

7. **Доступность**  
   - Виден focus  
   - Работает skip-link  
   - Базовая клавиатурная навигация

8. **RTL**  
   При `dir="rtl"` layout и отступы ведут себя корректно.

9. **Сборка и качество**  
   ```bash
   npm run lint:css
   npm run build:ds
   ```

---

### Итог главы

На этом этапе проект Nova полностью соответствует заявленной цели практического руководства:

> Создать законченный современный проект, в котором применены все ключевые возможности CSS 2026, описанные в теоретической книге.

Вы получили:

- Рабочее приложение
- Переиспользуемую Design System
- Документацию и Style Guide
- Правила архитектуры
- Инструменты контроля качества
- Понимание, как развивать систему дальше

Это и есть финальная практическая точка всего руководства.