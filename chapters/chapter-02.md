**Глава 2. Базовая настройка: Design Tokens, OKLCH и тёмная тема**

### Цель главы

В первой главе мы заложили архитектуру и создали каркас приложения.  
Теперь превращаем систему токенов в настоящую современную основу:

- Переводим цвета на **OKLCH** (самое perceptually uniform цветовое пространство на 2026 год).
- Добавляем полноценную поддержку **светлой и тёмной темы**.
- Делаем токены более структурированными и удобными для масштабирования.
- Подключаем `color-scheme` и готовим почву для `color-mix()` и relative colors.

К концу главы у вас будет полностью рабочая система темизации, которая не потребует переписывания при добавлении новых компонентов.

---

### Что меняется в архитектуре

Мы оставляем ту же структуру файлов, но значительно расширяем `01-tokens.css` и слегка обновляем `02-base.css` и HTML.

Новая логика токенов:

1. Базовые цветовые значения в OKLCH.
2. Семантические токены (`--color-bg`, `--color-text` и т.д.), которые меняются в зависимости от темы.
3. Поддержка `prefers-color-scheme` + возможность ручного переключения.
4. Готовность к будущему использованию `color-mix()` и relative colors.

---

### Структура проекта на данном этапе

```
nova/
├── index.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css          ← значительно расширен
│   ├── 02-base.css            ← обновлён
│   ├── 03-layout.css
│   ├── 04-components.css
│   ├── 05-utilities.css
│   └── main.css
├── js/
│   └── main.js                ← добавлен переключатель темы
└── assets/
```

---

### Содержание файлов

#### 1. `index.html`

```html
<!DOCTYPE html>
<html lang="ru" data-theme="system">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nova — Dashboard</title>
  <meta name="color-scheme" content="light dark">
  <link rel="stylesheet" href="css/main.css">
</head>
<body>
  <div class="app">
    <!-- Sidebar -->
    <aside class="sidebar">
      <div class="sidebar__brand">Nova</div>
      <nav class="sidebar__nav">
        <!-- Навигация появится позже -->
      </nav>
    </aside>

    <!-- Main area -->
    <div class="main">
      <header class="header">
        <h1 class="header__title">Dashboard</h1>

        <div class="header__actions">
          <button class="theme-toggle" type="button" aria-label="Переключить тему">
            <span class="theme-toggle__icon" data-theme-icon>🌓</span>
          </button>
        </div>
      </header>

      <main class="content">
        <p>Добро пожаловать в проект Nova.</p>
        <p>Сейчас работает современная система токенов на OKLCH и поддержка светлой/тёмной темы.</p>
      </main>
    </div>
  </div>

  <script src="js/main.js" type="module"></script>
</body>
</html>
```

#### 2. `css/main.css` (без изменений)

```css
@layer reset, tokens, base, layout, components, utilities, overrides;

@import url("00-reset.css") layer(reset);
@import url("01-tokens.css") layer(tokens);
@import url("02-base.css") layer(base);
@import url("03-layout.css") layer(layout);
@import url("04-components.css") layer(components);
@import url("05-utilities.css") layer(utilities);
```

#### 3. `css/00-reset.css` (без изменений)

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}

* {
  margin: 0;
  padding: 0;
}

html {
  -webkit-text-size-adjust: 100%;
  hanging-punctuation: first last;
}

body {
  min-height: 100dvh;
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

img,
picture,
video,
canvas,
svg {
  display: block;
  max-width: 100%;
}

input,
button,
textarea,
select {
  font: inherit;
  color: inherit;
}

button {
  cursor: pointer;
  background: none;
  border: none;
}

a {
  color: inherit;
  text-decoration: none;
}

ul,
ol {
  list-style: none;
}

p,
h1,
h2,
h3,
h4,
h5,
h6 {
  overflow-wrap: break-word;
}
```

#### 4. `css/01-tokens.css` (полностью обновлён)

```css
/* ========================================
   1. Базовые цветовые значения (OKLCH)
   ======================================== */

:root {
  /* Primary */
  --palette-primary-50:  oklch(0.97 0.02 250);
  --palette-primary-100: oklch(0.93 0.04 250);
  --palette-primary-200: oklch(0.87 0.07 250);
  --palette-primary-300: oklch(0.78 0.11 250);
  --palette-primary-400: oklch(0.68 0.15 250);
  --palette-primary-500: oklch(0.59 0.18 250);   /* основной */
  --palette-primary-600: oklch(0.51 0.17 250);
  --palette-primary-700: oklch(0.43 0.15 250);
  --palette-primary-800: oklch(0.36 0.12 250);
  --palette-primary-900: oklch(0.29 0.09 250);

  /* Neutral (серые) */
  --palette-neutral-0:   oklch(1 0 0);
  --palette-neutral-50:  oklch(0.98 0.005 250);
  --palette-neutral-100: oklch(0.96 0.008 250);
  --palette-neutral-200: oklch(0.92 0.01 250);
  --palette-neutral-300: oklch(0.85 0.015 250);
  --palette-neutral-400: oklch(0.70 0.02 250);
  --palette-neutral-500: oklch(0.55 0.02 250);
  --palette-neutral-600: oklch(0.45 0.02 250);
  --palette-neutral-700: oklch(0.37 0.02 250);
  --palette-neutral-800: oklch(0.28 0.015 250);
  --palette-neutral-900: oklch(0.20 0.01 250);
  --palette-neutral-950: oklch(0.15 0.01 250);
  --palette-neutral-1000: oklch(0.10 0.008 250);

  /* Success / Warning / Danger (пока базовые) */
  --palette-success-500: oklch(0.65 0.17 145);
  --palette-warning-500: oklch(0.75 0.15 85);
  --palette-danger-500:  oklch(0.60 0.20 25);
}

/* ========================================
   2. Семантические токены (светлая тема по умолчанию)
   ======================================== */

:root {
  color-scheme: light;

  /* Фон */
  --color-bg:           var(--palette-neutral-50);
  --color-surface:      var(--palette-neutral-0);
  --color-surface-2:    var(--palette-neutral-100);

  /* Текст */
  --color-text:         var(--palette-neutral-900);
  --color-text-muted:   var(--palette-neutral-500);
  --color-text-inverse: var(--palette-neutral-0);

  /* Границы */
  --color-border:       var(--palette-neutral-200);
  --color-border-strong: var(--palette-neutral-300);

  /* Primary */
  --color-primary:       var(--palette-primary-500);
  --color-primary-hover: var(--palette-primary-600);
  --color-primary-subtle: var(--palette-primary-100);

  /* Состояния */
  --color-success: var(--palette-success-500);
  --color-warning: var(--palette-warning-500);
  --color-danger:  var(--palette-danger-500);

  /* Типографика */
  --font-sans: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  --font-mono: ui-monospace, "Cascadia Code", "Source Code Pro", Menlo, Consolas, monospace;

  --text-xs:  0.75rem;
  --text-sm:  0.875rem;
  --text-base: 1rem;
  --text-lg:  1.125rem;
  --text-xl:  1.25rem;
  --text-2xl: 1.5rem;
  --text-3xl: 1.875rem;
  --text-4xl: 2.25rem;

  /* Отступы */
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

  /* Радиусы */
  --radius-sm:   0.25rem;
  --radius-md:   0.5rem;
  --radius-lg:   0.75rem;
  --radius-xl:   1rem;
  --radius-2xl:  1.25rem;
  --radius-full: 9999px;

  /* Тени */
  --shadow-sm: 0 1px 2px 0 oklch(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px oklch(0 0 0 / 0.08), 0 2px 4px -2px oklch(0 0 0 / 0.05);
  --shadow-lg: 0 10px 15px -3px oklch(0 0 0 / 0.08), 0 4px 6px -4px oklch(0 0 0 / 0.05);

  /* Layout */
  --sidebar-width: 260px;
  --header-height: 64px;

  /* Переходы */
  --transition-fast: 150ms ease;
  --transition-base: 200ms ease;
}

/* ========================================
   3. Тёмная тема
   ======================================== */

@media (prefers-color-scheme: dark) {
  :root {
    color-scheme: dark;

    --color-bg:           var(--palette-neutral-950);
    --color-surface:      var(--palette-neutral-900);
    --color-surface-2:    var(--palette-neutral-800);

    --color-text:         var(--palette-neutral-50);
    --color-text-muted:   var(--palette-neutral-400);
    --color-text-inverse: var(--palette-neutral-950);

    --color-border:       var(--palette-neutral-800);
    --color-border-strong: var(--palette-neutral-700);

    --color-primary:       var(--palette-primary-400);
    --color-primary-hover: var(--palette-primary-300);
    --color-primary-subtle: var(--palette-primary-900);
  }
}

/* Ручное переключение темы через data-theme */
[data-theme="light"] {
  color-scheme: light;

  --color-bg:           var(--palette-neutral-50);
  --color-surface:      var(--palette-neutral-0);
  --color-surface-2:    var(--palette-neutral-100);

  --color-text:         var(--palette-neutral-900);
  --color-text-muted:   var(--palette-neutral-500);
  --color-text-inverse: var(--palette-neutral-0);

  --color-border:       var(--palette-neutral-200);
  --color-border-strong: var(--palette-neutral-300);

  --color-primary:       var(--palette-primary-500);
  --color-primary-hover: var(--palette-primary-600);
  --color-primary-subtle: var(--palette-primary-100);
}

[data-theme="dark"] {
  color-scheme: dark;

  --color-bg:           var(--palette-neutral-950);
  --color-surface:      var(--palette-neutral-900);
  --color-surface-2:    var(--palette-neutral-800);

  --color-text:         var(--palette-neutral-50);
  --color-text-muted:   var(--palette-neutral-400);
  --color-text-inverse: var(--palette-neutral-950);

  --color-border:       var(--palette-neutral-800);
  --color-border-strong: var(--palette-neutral-700);

  --color-primary:       var(--palette-primary-400);
  --color-primary-hover: var(--palette-primary-300);
  --color-primary-subtle: var(--palette-primary-900);
}
```

#### 5. `css/02-base.css` (обновлён)

```css
body {
  font-family: var(--font-sans);
  font-size: var(--text-base);
  color: var(--color-text);
  background-color: var(--color-bg);
  transition: background-color var(--transition-base), color var(--transition-base);
}

h1, h2, h3, h4 {
  font-weight: 600;
  line-height: 1.25;
  color: var(--color-text);
}

p {
  color: var(--color-text);
}

/* Плавный переход для всех элементов, использующих токены */
* {
  transition:
    background-color var(--transition-fast),
    border-color var(--transition-fast),
    color var(--transition-fast);
}
```

#### 6. `css/03-layout.css` (небольшие улучшения)

```css
.app {
  display: grid;
  grid-template-columns: var(--sidebar-width) 1fr;
  min-height: 100dvh;
}

.sidebar {
  background-color: var(--color-surface);
  border-right: 1px solid var(--color-border);
  padding: var(--space-6);
}

.sidebar__brand {
  font-size: var(--text-xl);
  font-weight: 700;
  color: var(--color-text);
  margin-bottom: var(--space-8);
}

.main {
  display: grid;
  grid-template-rows: var(--header-height) 1fr;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-inline: var(--space-8);
  background-color: var(--color-surface);
  border-bottom: 1px solid var(--color-border);
}

.header__title {
  font-size: var(--text-xl);
  font-weight: 600;
}

.header__actions {
  display: flex;
  align-items: center;
  gap: var(--space-3);
}

.content {
  padding: var(--space-8);
}

/* Кнопка переключения темы (простая заготовка) */
.theme-toggle {
  width: 2.5rem;
  height: 2.5rem;
  display: grid;
  place-items: center;
  border-radius: var(--radius-md);
  background-color: var(--color-surface-2);
  border: 1px solid var(--color-border);
  font-size: 1.1rem;
  transition: background-color var(--transition-fast);
}

.theme-toggle:hover {
  background-color: var(--color-primary-subtle);
}
```

#### 7. `css/04-components.css` и `css/05-utilities.css`

Пока остаются пустыми (или с комментарием).

#### 8. `js/main.js`

```js
const html = document.documentElement;
const toggle = document.querySelector(".theme-toggle");
const icon = document.querySelector("[data-theme-icon]");

function getPreferredTheme() {
  const stored = localStorage.getItem("nova-theme");
  if (stored) return stored;
  return window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light";
}

function setTheme(theme) {
  html.setAttribute("data-theme", theme);
  localStorage.setItem("nova-theme", theme);

  // Обновляем иконку
  icon.textContent = theme === "dark" ? "☀️" : "🌙";
}

// Инициализация
setTheme(getPreferredTheme());

// Обработчик клика
toggle?.addEventListener("click", () => {
  const current = html.getAttribute("data-theme");
  const next = current === "dark" ? "light" : "dark";
  setTheme(next);
});

// Следим за системными изменениями (если пользователь выбрал system)
window.matchMedia("(prefers-color-scheme: dark)").addEventListener("change", (e) => {
  if (!localStorage.getItem("nova-theme")) {
    setTheme(e.matches ? "dark" : "light");
  }
});
```

---

### Что мы получили в этой главе

- Полноценная система Design Tokens на **OKLCH**.
- Поддержка светлой и тёмной темы (системная + ручная).
- Плавные переходы между темами.
- Готовность к использованию `color-mix()`, relative colors и более сложных цветовых схем.
- Чистая семантика (`--color-bg`, `--color-text`, `--color-primary` и т.д.).

В следующей главе мы начнём строить настоящие компоненты (кнопки, карточки, формы) и активно использовать `@layer components`, `:has()` и современные селекторы.