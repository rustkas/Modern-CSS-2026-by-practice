**Глава 10. Построение Design System: токены, контракты и масштабирование**

### Цель главы

К этому моменту у нас уже есть рабочее приложение с большим количеством современных CSS-возможностей.  
Теперь мы переходим от «набора компонентов» к настоящей **Design System**.

В этой главе мы:

- Приведём Design Tokens к более зрелому и масштабируемому виду.
- Стандартизируем контракты всех компонентов.
- Улучшим организацию CSS-файлов.
- Введём чёткие правила именования и API.
- Подготовим фундамент для дальнейшего роста UI-библиотеки.

К концу главы проект Nova станет значительно более поддерживаемым и приблизится к виду настоящей дизайн-системы.

---

### Принципы, которые мы закрепляем

1. **Токены — единственный источник правды**.
2. Каждый компонент имеет понятный публичный API (CSS-переменные + модификаторы).
3. Стили компонентов не протекают наружу.
4. Имена следуют единой системе (`btn`, `btn--primary`, `btn--sm` и т.д.).
5. Архитектура слоёв остаётся строгой.

---

### Структура проекта на данном этапе

Мы немного улучшаем организацию:

```
nova/
├── index.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css
│   ├── 02-base.css
│   ├── 03-layout.css
│   ├── components/
│   │   ├── _buttons.css
│   │   ├── _cards.css
│   │   ├── _forms.css
│   │   ├── _navigation.css
│   │   ├── _overlays.css      (tooltip, dropdown, modal)
│   │   └── _badges.css
│   ├── 04-components.css      ← теперь только импорты
│   ├── 05-utilities.css
│   ├── 06-animations.css
│   └── main.css
├── js/
│   └── main.js
└── assets/
```

---

### Содержание файлов

#### 1. `css/main.css`

```css
@layer reset, tokens, base, layout, components, utilities, animations, overrides;

@import url("00-reset.css") layer(reset);
@import url("01-tokens.css") layer(tokens);
@import url("02-base.css") layer(base);
@import url("03-layout.css") layer(layout);
@import url("04-components.css") layer(components);
@import url("05-utilities.css") layer(utilities);
@import url("06-animations.css") layer(animations);
```

#### 2. `css/04-components.css` (теперь точка входа компонентов)

```css
/* Импорт всех компонентов */
@import url("components/_buttons.css");
@import url("components/_badges.css");
@import url("components/_cards.css");
@import url("components/_forms.css");
@import url("components/_navigation.css");
@import url("components/_overlays.css");
```

#### 3. `css/01-tokens.css` (улучшенная версия)

```css
/* ========================================
   Design Tokens — Nova Design System
   ======================================== */

:root {
  /* ---------- Color Palette (OKLCH) ---------- */
  --palette-primary-50:  oklch(0.97 0.02 250);
  --palette-primary-100: oklch(0.93 0.04 250);
  --palette-primary-200: oklch(0.87 0.07 250);
  --palette-primary-300: oklch(0.78 0.11 250);
  --palette-primary-400: oklch(0.68 0.15 250);
  --palette-primary-500: oklch(0.59 0.18 250);
  --palette-primary-600: oklch(0.51 0.17 250);
  --palette-primary-700: oklch(0.43 0.15 250);
  --palette-primary-800: oklch(0.36 0.12 250);
  --palette-primary-900: oklch(0.29 0.09 250);

  --palette-neutral-0:    oklch(1 0 0);
  --palette-neutral-50:   oklch(0.98 0.005 250);
  --palette-neutral-100:  oklch(0.96 0.008 250);
  --palette-neutral-200:  oklch(0.92 0.01 250);
  --palette-neutral-300:  oklch(0.85 0.015 250);
  --palette-neutral-400:  oklch(0.70 0.02 250);
  --palette-neutral-500:  oklch(0.55 0.02 250);
  --palette-neutral-600:  oklch(0.45 0.02 250);
  --palette-neutral-700:  oklch(0.37 0.02 250);
  --palette-neutral-800:  oklch(0.28 0.015 250);
  --palette-neutral-900:  oklch(0.20 0.01 250);
  --palette-neutral-950:  oklch(0.15 0.01 250);

  --palette-success-500: oklch(0.65 0.17 145);
  --palette-warning-500: oklch(0.75 0.15 85);
  --palette-danger-500:  oklch(0.60 0.20 25);

  /* ---------- Semantic Colors (Light) ---------- */
  color-scheme: light;

  --color-bg:             var(--palette-neutral-50);
  --color-surface:        var(--palette-neutral-0);
  --color-surface-2:      var(--palette-neutral-100);
  --color-text:           var(--palette-neutral-900);
  --color-text-muted:     var(--palette-neutral-500);
  --color-text-inverse:   var(--palette-neutral-0);
  --color-border:         var(--palette-neutral-200);
  --color-border-strong:  var(--palette-neutral-300);

  --color-primary:        var(--palette-primary-500);
  --color-primary-hover:  var(--palette-primary-600);
  --color-primary-subtle: var(--palette-primary-100);

  --color-success:        var(--palette-success-500);
  --color-warning:        var(--palette-warning-500);
  --color-danger:         var(--palette-danger-500);

  /* ---------- Typography ---------- */
  --font-sans: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  --font-mono: ui-monospace, "Cascadia Code", "Source Code Pro", Menlo, Consolas, monospace;

  --text-xs:   0.75rem;
  --text-sm:   0.875rem;
  --text-base: 1rem;
  --text-lg:   1.125rem;
  --text-xl:   1.25rem;
  --text-2xl:  1.5rem;
  --text-3xl:  1.875rem;
  --text-4xl:  2.25rem;

  /* ---------- Spacing ---------- */
  --space-1:  0.25rem;
  --space-2:  0.5rem;
  --space-3:  0.75rem;
  --space-4:  1rem;
  --space-5:  1.25rem;
  --space-6:  1.5rem;
  --space-8:  2rem;
  --space-10: 2.5rem;
  --space-12: 3rem;
  --space-16: 4rem;
  --space-20: 5rem;

  /* ---------- Radius ---------- */
  --radius-sm:   0.25rem;
  --radius-md:   0.5rem;
  --radius-lg:   0.75rem;
  --radius-xl:   1rem;
  --radius-2xl:  1.25rem;
  --radius-full: 9999px;

  /* ---------- Shadows ---------- */
  --shadow-sm: 0 1px 2px 0 oklch(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px oklch(0 0 0 / 0.08), 0 2px 4px -2px oklch(0 0 0 / 0.05);
  --shadow-lg: 0 10px 15px -3px oklch(0 0 0 / 0.08), 0 4px 6px -4px oklch(0 0 0 / 0.05);

  /* ---------- Layout ---------- */
  --sidebar-width: 260px;
  --header-height: 64px;

  /* ---------- Motion ---------- */
  --transition-fast: 150ms ease;
  --transition-base: 200ms ease;
  --transition-slow: 300ms cubic-bezier(0.22, 1, 0.36, 1);
}

/* Dark theme */
@media (prefers-color-scheme: dark) {
  :root {
    color-scheme: dark;

    --color-bg:             var(--palette-neutral-950);
    --color-surface:        var(--palette-neutral-900);
    --color-surface-2:      var(--palette-neutral-800);
    --color-text:           var(--palette-neutral-50);
    --color-text-muted:     var(--palette-neutral-400);
    --color-text-inverse:   var(--palette-neutral-950);
    --color-border:         var(--palette-neutral-800);
    --color-border-strong:  var(--palette-neutral-700);
    --color-primary:        var(--palette-primary-400);
    --color-primary-hover:  var(--palette-primary-300);
    --color-primary-subtle: var(--palette-primary-900);
  }
}

[data-theme="light"] {
  color-scheme: light;
  --color-bg:             var(--palette-neutral-50);
  --color-surface:        var(--palette-neutral-0);
  --color-surface-2:      var(--palette-neutral-100);
  --color-text:           var(--palette-neutral-900);
  --color-text-muted:     var(--palette-neutral-500);
  --color-text-inverse:   var(--palette-neutral-0);
  --color-border:         var(--palette-neutral-200);
  --color-border-strong:  var(--palette-neutral-300);
  --color-primary:        var(--palette-primary-500);
  --color-primary-hover:  var(--palette-primary-600);
  --color-primary-subtle: var(--palette-primary-100);
}

[data-theme="dark"] {
  color-scheme: dark;
  --color-bg:             var(--palette-neutral-950);
  --color-surface:        var(--palette-neutral-900);
  --color-surface-2:      var(--palette-neutral-800);
  --color-text:           var(--palette-neutral-50);
  --color-text-muted:     var(--palette-neutral-400);
  --color-text-inverse:   var(--palette-neutral-950);
  --color-border:         var(--palette-neutral-800);
  --color-border-strong:  var(--palette-neutral-700);
  --color-primary:        var(--palette-primary-400);
  --color-primary-hover:  var(--palette-primary-300);
  --color-primary-subtle: var(--palette-primary-900);
}
```

#### 4. Примеры компонентов (новые файлы)

**`css/components/_buttons.css`**

```css
/* ========================================
   Button
   API:
   - .btn
   - .btn--secondary | .btn--ghost | .btn--danger
   - .btn--sm
   ======================================== */

.btn {
  --btn-bg: var(--color-primary);
  --btn-color: var(--color-text-inverse);
  --btn-border: transparent;
  --btn-hover-bg: var(--color-primary-hover);
  --btn-padding-y: var(--space-2);
  --btn-padding-x: var(--space-4);
  --btn-font-size: var(--text-sm);
  --btn-radius: var(--radius-md);

  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--btn-padding-y) var(--btn-padding-x);
  font-size: var(--btn-font-size);
  font-weight: 500;
  line-height: 1.4;
  border-radius: var(--btn-radius);
  background-color: var(--btn-bg);
  color: var(--btn-color);
  border: 1px solid var(--btn-border);
  cursor: pointer;
  white-space: nowrap;
  transition:
    background-color var(--transition-fast),
    border-color var(--transition-fast),
    color var(--transition-fast),
    transform var(--transition-fast);

  &:hover:not(:disabled) {
    background-color: var(--btn-hover-bg);
  }

  &:active:not(:disabled) {
    transform: scale(0.97);
  }

  &:focus-visible {
    outline: 2px solid var(--color-primary);
    outline-offset: 2px;
  }

  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
}

.btn--secondary {
  --btn-bg: var(--color-surface-2);
  --btn-color: var(--color-text);
  --btn-border: var(--color-border);
  --btn-hover-bg: var(--color-border);
}

.btn--ghost {
  --btn-bg: transparent;
  --btn-color: var(--color-text);
  --btn-border: transparent;
  --btn-hover-bg: var(--color-surface-2);
}

.btn--danger {
  --btn-bg: var(--color-danger);
  --btn-color: white;
  --btn-hover-bg: color-mix(in oklch, var(--color-danger) 85%, black);
}

.btn--sm {
  --btn-padding-y: var(--space-1);
  --btn-padding-x: var(--space-3);
  --btn-font-size: var(--text-xs);
}
```

**`css/components/_cards.css`**

```css
/* ========================================
   Card
   ======================================== */

.card {
  container-type: inline-size;
  container-name: card;

  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  padding: var(--space-6);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  position: relative;
  transition: transform var(--transition-base), box-shadow var(--transition-base);
}

.card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

.card__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-3);
}

.card__title {
  font-size: var(--text-lg);
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: var(--space-2);
}

.card__text {
  color: var(--color-text-muted);
  font-size: var(--text-sm);
  line-height: 1.5;
}

.card__value {
  font-size: var(--text-3xl);
  font-weight: 700;
  line-height: 1.1;
}

.card__footer {
  margin-top: auto;
  padding-top: var(--space-2);
}

@container card (max-width: 280px) {
  .card {
    padding: var(--space-4);
  }

  .card__title {
    font-size: var(--text-base);
  }

  .card__value {
    font-size: var(--text-2xl);
  }
}
```

**`css/components/_overlays.css`** (Tooltip + Dropdown + Modal)

```css
/* Tooltip, Dropdown и Modal — переносим сюда стили из предыдущих глав */
/* (содержимое остаётся тем же, просто организовано в отдельный файл) */
```

Аналогично создаются `_forms.css`, `_navigation.css`, `_badges.css` — мы просто переносим существующие стили в соответствующие файлы.

#### 5. `index.html` и `js/main.js`

Оставляем в состоянии Главы 9 (с модальным окном и всеми предыдущими возможностями).

#### 6. Остальные файлы

`00-reset.css`, `02-base.css`, `03-layout.css`, `06-animations.css` — без изменений по логике.

---

### Что мы получили в этой главе

- Настоящую структуру **Design System**.
- Чёткое разделение компонентов по файлам.
- Улучшенные и документированные Design Tokens.
- Стандартизированные контракты компонентов.
- Значительно более поддерживаемую кодовую базу.
- Готовность масштабировать UI-библиотеку дальше (новые компоненты, темы, вариации).

В следующей главе мы добавим больше компонентов (Tabs, Accordion, Toast и др.), улучшим доступность и начнём финальную полировку проекта.