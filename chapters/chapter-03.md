**Глава 3. CSS как язык компонентов: первые компоненты и контракты**

### Цель главы

В предыдущих главах мы создали архитектуру и мощную систему Design Tokens.  
Теперь переходим к главному — начинаем писать настоящие **компоненты**.

В этой главе мы:

- Создадим первые переиспользуемые компоненты (Button, Card, Badge).
- Научимся правильно работать со слоем `@layer components`.
- Введём понятие **контракта компонента** через CSS Custom Properties.
- Покажем, как компоненты остаются изолированными и предсказуемыми.
- Начнём использовать современный CSS Nesting.

К концу главы у вас будет небольшая, но уже рабочая UI-библиотека внутри проекта Nova.

---

### Основные принципы, которые мы закрепляем

1. Компонент = самостоятельная единица с чётким публичным API (через CSS-переменные и классы-модификаторы).
2. Внутренние стили компонента живут только в `@layer components`.
3. Компонент не должен «протекать» стилями наружу.
4. Все цвета, отступы и размеры берутся **только** из Design Tokens.
5. Состояния (`:hover`, `:focus-visible`, `:disabled`) описываются внутри компонента.

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
│   ├── 04-components.css      ← теперь содержит реальные компоненты
│   ├── 05-utilities.css
│   └── main.css
├── js/
│   └── main.js
└── assets/
```

Структура файлов не изменилась — мы продолжаем наращивать содержимое внутри существующей архитектуры.

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
        <a href="#" class="nav-link is-active">Dashboard</a>
        <a href="#" class="nav-link">Projects</a>
        <a href="#" class="nav-link">Analytics</a>
        <a href="#" class="nav-link">Settings</a>
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
        <section class="section">
          <h2 class="section__title">Компоненты</h2>

          <!-- Buttons -->
          <div class="demo-row">
            <button class="btn">Primary</button>
            <button class="btn btn--secondary">Secondary</button>
            <button class="btn btn--ghost">Ghost</button>
            <button class="btn btn--danger">Danger</button>
            <button class="btn" disabled>Disabled</button>
          </div>

          <!-- Cards -->
          <div class="cards-grid">
            <article class="card">
              <div class="card__header">
                <h3 class="card__title">Активные проекты</h3>
                <span class="badge badge--success">12</span>
              </div>
              <p class="card__text">Количество проектов, находящихся в работе прямо сейчас.</p>
              <div class="card__footer">
                <button class="btn btn--sm">Подробнее</button>
              </div>
            </article>

            <article class="card">
              <div class="card__header">
                <h3 class="card__title">Задачи на сегодня</h3>
                <span class="badge badge--warning">5</span>
              </div>
              <p class="card__text">Список задач, которые нужно выполнить до конца дня.</p>
              <div class="card__footer">
                <button class="btn btn--sm btn--secondary">Открыть</button>
              </div>
            </article>

            <article class="card">
              <div class="card__header">
                <h3 class="card__title">Ошибки</h3>
                <span class="badge badge--danger">2</span>
              </div>
              <p class="card__text">Критические ошибки, требующие внимания.</p>
              <div class="card__footer">
                <button class="btn btn--sm btn--danger">Исправить</button>
              </div>
            </article>
          </div>
        </section>
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

#### 3. `css/00-reset.css` и `css/01-tokens.css`

Оставляем без изменений (из Главы 2).

#### 4. `css/02-base.css` (небольшие дополнения)

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

/* Плавный переход для токенов */
* {
  transition:
    background-color var(--transition-fast),
    border-color var(--transition-fast),
    color var(--transition-fast),
    box-shadow var(--transition-fast);
}
```

#### 5. `css/03-layout.css` (обновлён)

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
  display: flex;
  flex-direction: column;
}

.sidebar__brand {
  font-size: var(--text-xl);
  font-weight: 700;
  color: var(--color-text);
  margin-bottom: var(--space-8);
}

.sidebar__nav {
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
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

.section {
  margin-bottom: var(--space-10);
}

.section__title {
  font-size: var(--text-2xl);
  margin-bottom: var(--space-6);
}

.demo-row {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-3);
  margin-bottom: var(--space-8);
}

.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: var(--space-6);
}

/* Кнопка переключения темы */
.theme-toggle {
  width: 2.5rem;
  height: 2.5rem;
  display: grid;
  place-items: center;
  border-radius: var(--radius-md);
  background-color: var(--color-surface-2);
  border: 1px solid var(--color-border);
  font-size: 1.1rem;
}

.theme-toggle:hover {
  background-color: var(--color-primary-subtle);
}
```

#### 6. `css/04-components.css` (новый основной файл главы)

```css
/* ========================================
   Nav Link
   ======================================== */

.nav-link {
  display: block;
  padding: var(--space-3) var(--space-4);
  border-radius: var(--radius-md);
  color: var(--color-text-muted);
  font-weight: 500;
  transition: background-color var(--transition-fast), color var(--transition-fast);
}

.nav-link:hover {
  background-color: var(--color-surface-2);
  color: var(--color-text);
}

.nav-link.is-active {
  background-color: var(--color-primary-subtle);
  color: var(--color-primary);
}

/* ========================================
   Button
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
  transition: background-color var(--transition-fast), border-color var(--transition-fast), color var(--transition-fast);
}

.btn:hover:not(:disabled) {
  background-color: var(--btn-hover-bg);
}

.btn:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Модификаторы Button */
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

/* ========================================
   Badge
   ======================================== */

.badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-width: 1.5rem;
  height: 1.5rem;
  padding-inline: var(--space-2);
  font-size: var(--text-xs);
  font-weight: 600;
  border-radius: var(--radius-full);
  background-color: var(--color-surface-2);
  color: var(--color-text);
}

.badge--success {
  background-color: color-mix(in oklch, var(--color-success) 18%, transparent);
  color: var(--color-success);
}

.badge--warning {
  background-color: color-mix(in oklch, var(--color-warning) 18%, transparent);
  color: var(--color-warning);
}

.badge--danger {
  background-color: color-mix(in oklch, var(--color-danger) 18%, transparent);
  color: var(--color-danger);
}

/* ========================================
   Card
   ======================================== */

.card {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  padding: var(--space-6);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
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
}

.card__text {
  color: var(--color-text-muted);
  font-size: var(--text-sm);
  line-height: 1.5;
}

.card__footer {
  margin-top: auto;
  padding-top: var(--space-2);
}
```

#### 7. `css/05-utilities.css`

Пока остаётся пустым.

#### 8. `js/main.js`

Оставляем без изменений (логика переключения темы из Главы 2).

---

### Что мы получили в этой главе

- Три настоящих компонента: **Button**, **Badge**, **Card**.
- Контракт компонента через CSS Custom Properties (`--btn-*`).
- Правильное использование модификаторов (`btn--secondary`, `btn--sm` и т.д.).
- Применение `color-mix()` для создания subtle-версий цветов.
- Компоненты полностью живут в `@layer components` и не конфликтуют с остальным кодом.
- Начало формирования собственной UI-библиотеки.

В следующей главе мы добавим более сложные компоненты (Input, Form, Modal) и начнём активно использовать `:has()` для управления состояниями форм и карточек.