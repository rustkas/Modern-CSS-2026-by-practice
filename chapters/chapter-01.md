**Глава 1. Архитектура проекта и структура файлов**

### Цель главы

В этой главе мы создаём фундамент будущего проекта.  
Мы не будем писать красивые компоненты и сложные макеты.  
Наша задача — заложить правильную, масштабируемую архитектуру CSS-кода, которая позволит в дальнейшем использовать **все** современные возможности CSS 2026 без хаоса, без `!important` и без препроцессоров.

К концу главы у вас будет:

- чёткая структура файлов;
- работающий HTML-каркас;
- базовая система Cascade Layers;
- заготовка Design Tokens;
- возможность легко добавлять новые компоненты в последующих главах.

---

### Выбор итогового проекта

Мы будем создавать **приложение «Nova»** — современную панель управления (dashboard) с элементами каталога, формами, модальными окнами и поддержкой тёмной темы.

Почему именно такое приложение:

- Есть сложный layout (sidebar + header + main + cards).
- Много компонентов разного уровня сложности.
- Нужна адаптивность на уровне компонентов (Container Queries).
- Есть состояния (hover, focus, invalid, selected) → отлично подходит для `:has()`.
- Есть переходы между разделами → View Transitions.
- Есть длинные списки и скролл → Scroll-driven Animations.
- Нужна полноценная темизация (светлая / тёмная) на основе OKLCH и Design Tokens.
- Есть всплывающие элементы (tooltips, dropdowns, popovers) → Anchor Positioning.

Всё это позволит нам последовательно применить каждую главу теоретической книги на одном реальном проекте.

---

### Принципы архитектуры

1. **Никакого Sass / Less / Bootstrap**. Только современный CSS.
2. **Cascade Layers** как основа порядка.
3. **Design Tokens** — единый источник правды для всех значений.
4. **Компонентный подход** с самого начала.
5. **Один файл = одна зона ответственности**.
6. Код должен оставаться понятным через 1–2 года.

Рекомендуемый порядок слоёв (`@layer`):

```css
@layer reset, tokens, base, layout, components, utilities, overrides;
```

---

### Структура проекта на данном этапе

```
nova/
├── index.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css
│   ├── 02-base.css
│   ├── 03-layout.css
│   ├── 04-components.css      ← пока пустой (заготовка)
│   ├── 05-utilities.css       ← пока пустой
│   └── main.css               ← точка входа (импорты + слои)
├── js/
│   └── main.js                ← минимальный (пока пустой)
└── assets/
    └── (пока пусто)
```

---

### Содержание каждого файла

#### 1. `index.html`

```html
<!DOCTYPE html>
<html lang="ru">
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
        <!-- Действия появятся позже -->
      </header>

      <main class="content">
        <p>Добро пожаловать в проект Nova. Здесь будет строиться современный интерфейс.</p>
      </main>
    </div>
  </div>

  <script src="js/main.js" type="module"></script>
</body>
</html>
```

#### 2. `css/main.css` (точка входа)

```css
/* Порядок слоёв — критически важен */
@layer reset, tokens, base, layout, components, utilities, overrides;

/* Импорты */
@import url("00-reset.css") layer(reset);
@import url("01-tokens.css") layer(tokens);
@import url("02-base.css") layer(base);
@import url("03-layout.css") layer(layout);
@import url("04-components.css") layer(components);
@import url("05-utilities.css") layer(utilities);
```

#### 3. `css/00-reset.css`

```css
/* Современный минимальный reset */
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

#### 4. `css/01-tokens.css`

```css
:root {
  /* === Цвета (пока в простых значениях, позже переведём на OKLCH) === */
  --color-bg: #f8fafc;
  --color-surface: #ffffff;
  --color-text: #0f172a;
  --color-text-muted: #64748b;
  --color-border: #e2e8f0;
  --color-primary: #3b82f6;
  --color-primary-hover: #2563eb;

  /* === Типографика === */
  --font-sans: system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
  --font-mono: ui-monospace, "Cascadia Code", "Source Code Pro", Menlo, monospace;

  --text-xs: 0.75rem;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.25rem;
  --text-2xl: 1.5rem;
  --text-3xl: 1.875rem;

  /* === Размеры и отступы === */
  --space-1: 0.25rem;
  --space-2: 0.5rem;
  --space-3: 0.75rem;
  --space-4: 1rem;
  --space-5: 1.25rem;
  --space-6: 1.5rem;
  --space-8: 2rem;
  --space-10: 2.5rem;
  --space-12: 3rem;
  --space-16: 4rem;

  /* === Радиусы === */
  --radius-sm: 0.25rem;
  --radius-md: 0.5rem;
  --radius-lg: 0.75rem;
  --radius-xl: 1rem;
  --radius-full: 9999px;

  /* === Тени === */
  --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
  --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);

  /* === Layout === */
  --sidebar-width: 260px;
  --header-height: 64px;
}
```

#### 5. `css/02-base.css`

```css
body {
  font-family: var(--font-sans);
  font-size: var(--text-base);
  color: var(--color-text);
  background-color: var(--color-bg);
}

h1, h2, h3, h4 {
  font-weight: 600;
  line-height: 1.25;
}
```

#### 6. `css/03-layout.css`

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
  margin-bottom: var(--space-8);
}

.main {
  display: grid;
  grid-template-rows: var(--header-height) 1fr;
}

.header {
  display: flex;
  align-items: center;
  padding-inline: var(--space-8);
  background-color: var(--color-surface);
  border-bottom: 1px solid var(--color-border);
}

.header__title {
  font-size: var(--text-xl);
}

.content {
  padding: var(--space-8);
}
```

#### 7. `css/04-components.css` и `css/05-utilities.css`

Пока оставляем пустыми (или с комментарием):

```css
/* Компоненты будут добавляться в следующих главах */
```

#### 8. `js/main.js`

```js
// Пока пустой. Позже здесь появится логика переключения темы и View Transitions.
console.log("Nova project initialized");
```

---

### Что мы получили

На этом этапе у нас есть:

- Чистая, масштабируемая структура.
- Правильный порядок Cascade Layers.
- Design Tokens как единый источник значений.
- Базовый layout приложения (sidebar + header + content).
- Готовность к добавлению Container Queries, `:has()`, OKLCH, View Transitions и остальных технологий без переписывания архитектуры.

В следующей главе мы переведём токены на современную цветовую систему (OKLCH + `color-mix()`), добавим поддержку тёмной темы и начнём строить первые настоящие компоненты.