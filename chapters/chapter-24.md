**Глава 24. Упаковка Design System для повторного использования**

### Цель главы

До этого момента Nova существовала как самостоятельный учебный проект.  
В реальной работе Design System почти всегда нужно **переиспользовать** в нескольких продуктах или репозиториях.

В этой главе мы:

- Подготовим структуру для публикации CSS-слоя Design System.
- Отделим «ядро» системы от демонстрационного приложения.
- Покажем, как подключать Nova в другие проекты.
- Добавим простой подход к версионированию.

К концу главы у вас будет понимание, как превратить учебный проект в переиспользуемый пакет.

---

### Рекомендуемая структура после упаковки

Мы разделяем проект на две логические части:

```
nova/
├── packages/
│   └── design-system/                 ← переиспользуемое ядро
│       ├── css/
│       │   ├── 00-reset.css
│       │   ├── 01-tokens.css
│       │   ├── 02-base.css
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
│       └── README.md
│
├── apps/
│   └── dashboard/                     ← демонстрационное приложение
│       ├── index.html
│       ├── style-guide.html
│       ├── js/
│       │   └── main.js
│       └── css/
│           └── app.css                ← только стили конкретного приложения
│
├── package.json                       ← корневой (workspace)
└── stylelint.config.js
```

> На практике можно использовать npm/pnpm/yarn workspaces.

---

### Содержание ключевых файлов

#### 1. `packages/design-system/package.json`

```json
{
  "name": "@nova/design-system",
  "version": "1.0.0",
  "description": "Nova Design System — современная CSS-дизайн-система 2026",
  "main": "css/main.css",
  "style": "css/main.css",
  "files": [
    "css"
  ],
  "keywords": [
    "design-system",
    "css",
    "oklch",
    "cascade-layers",
    "modern-css"
  ],
  "license": "MIT"
}
```

#### 2. `packages/design-system/css/main.css`

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

> Содержимое всех импортируемых файлов остаётся тем же, что мы последовательно создавали в предыдущих главах.

#### 3. `packages/design-system/css/04-components.css`

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

#### 4. Пример подключения в другом проекте

```html
<link rel="stylesheet" href="node_modules/@nova/design-system/css/main.css">
```

Или при сборке через бандлер / Lightning CSS:

```css
@import "@nova/design-system/css/main.css";
```

#### 5. `apps/dashboard/css/app.css` (стили только приложения)

```css
/* Здесь живут стили, специфичные для конкретного продукта */
/* Например, уникальные страницы, маркетинговые блоки и т.д. */

.dashboard-welcome {
  padding: var(--space-8);
}
```

#### 6. Корневой `package.json` (workspaces)

```json
{
  "name": "nova-monorepo",
  "private": true,
  "workspaces": [
    "packages/*",
    "apps/*"
  ],
  "scripts": {
    "lint:css": "stylelint \"packages/**/*.css\" \"apps/**/*.css\"",
    "build:ds": "lightningcss --minify --bundle packages/design-system/css/main.css -o packages/design-system/dist/main.min.css"
  }
}
```

---

### Версионирование

Рекомендуемый подход:

- **Major** — ломающие изменения токенов или API компонентов.
- **Minor** — новые компоненты или обратно-совместимые улучшения.
- **Patch** — исправления багов и мелкие правки.

Обязательно ведите `CHANGELOG.md` в пакете `design-system`.

---

### Что остаётся без изменений

Содержимое всех CSS-файлов компонентов, токенов, анимаций, performance и a11y **не меняется** по сути.  
Мы только меняем организацию проекта, чтобы ядро Design System можно было подключать повторно.

---

### Что мы получили в этой главе

- Понимание, как отделить Design System от приложения.
- Готовую структуру для переиспользования.
- Подход к версионированию.
- Возможность подключать Nova в другие проекты как пакет.
- Более «взрослый» взгляд на Design System как на продукт.

---

### Рекомендация

Даже если вы пока не публикуете систему в npm, полезно держать её в отдельной папке/`package`.  
Это резко повышает дисциплину: в ядро попадает только то, что действительно является частью Design System, а не конкретного продукта.