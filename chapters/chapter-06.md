**Глава 6. Anchor Positioning: Tooltip, Dropdown и Popover без JavaScript**

### Цель главы

В современных интерфейсах постоянно нужны всплывающие элементы: подсказки, выпадающие меню, контекстные панели. Раньше для этого почти всегда требовался JavaScript.  

В 2026 году у нас есть **Anchor Positioning** — мощный нативный механизм, который позволяет позиционировать элементы относительно других элементов декларативно.

В этой главе мы:

- Разберём основы Anchor Positioning.
- Создадим компоненты **Tooltip**, **Dropdown** и простой **Popover**.
- Покажем, как делать всплывающие элементы без JavaScript (или с минимальным JS только для открытия/закрытия).
- Интегрируем новые компоненты в существующий интерфейс Nova.

К концу главы у вас появятся переиспользуемые паттерны позиционирования, которые можно применять в любой части приложения.

---

### Ключевые возможности, которые мы используем

- `anchor-name` / `position-anchor`
- `position-area`
- `position-try-fallbacks` (для умного переворота)
- `@position-try`
- Появление/исчезновение через CSS (в сочетании с `:popover-open` или классами)

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
│   ├── 04-components.css      ← добавлены Tooltip, Dropdown, Popover
│   ├── 05-utilities.css
│   └── main.css
├── js/
│   └── main.js                ← минимальная логика открытия dropdown
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
          <!-- Пример Dropdown -->
          <div class="dropdown">
            <button class="btn btn--secondary" id="user-menu-btn" aria-expanded="false">
              Аккаунт
            </button>
            <div class="dropdown__menu" id="user-menu" role="menu">
              <a href="#" class="dropdown__item" role="menuitem">Профиль</a>
              <a href="#" class="dropdown__item" role="menuitem">Настройки</a>
              <div class="dropdown__divider"></div>
              <a href="#" class="dropdown__item dropdown__item--danger" role="menuitem">Выйти</a>
            </div>
          </div>

          <button class="theme-toggle" type="button" aria-label="Переключить тему">
            <span class="theme-toggle__icon" data-theme-icon>🌓</span>
          </button>
        </div>
      </header>

      <main class="content">
        <!-- Статистика -->
        <section class="section">
          <h2 class="section__title">Обзор</h2>

          <div class="stats-grid">
            <article class="card card--stat">
              <div class="card__header">
                <h3 class="card__title">
                  Активные проекты
                  <button class="tooltip-trigger" aria-describedby="tip-projects" type="button">?</button>
                </h3>
                <span class="badge badge--success">12</span>
              </div>
              <p class="card__value">12</p>
              <p class="card__text">+3 за последнюю неделю</p>

              <!-- Tooltip -->
              <div class="tooltip" id="tip-projects" role="tooltip">
                Количество проектов со статусом «В работе»
              </div>
            </article>

            <article class="card card--stat">
              <div class="card__header">
                <h3 class="card__title">Задачи</h3>
                <span class="badge badge--warning">28</span>
              </div>
              <p class="card__value">28</p>
              <p class="card__text">5 требуют внимания</p>
            </article>

            <article class="card card--stat">
              <div class="card__header">
                <h3 class="card__title">Ошибки</h3>
                <span class="badge badge--danger">2</span>
              </div>
              <p class="card__value">2</p>
              <p class="card__text">Критические</p>
            </article>

            <article class="card card--stat">
              <div class="card__header">
                <h3 class="card__title">Команда</h3>
                <span class="badge">8</span>
              </div>
              <p class="card__value">8</p>
              <p class="card__text">Участников</p>
            </article>
          </div>
        </section>

        <!-- Основной контент -->
        <div class="content-grid">
          <section class="section">
            <h2 class="section__title">Проекты</h2>

            <div class="cards-grid">
              <article class="card">
                <div class="card__header">
                  <h3 class="card__title">Nova Redesign</h3>
                  <span class="badge badge--success">В работе</span>
                </div>
                <p class="card__text">Полный редизайн дизайн-системы и дашборда.</p>
                <div class="card__footer">
                  <button class="btn btn--sm">Открыть</button>
                </div>
              </article>

              <article class="card">
                <div class="card__header">
                  <h3 class="card__title">API Integration</h3>
                  <span class="badge badge--warning">Ожидание</span>
                </div>
                <p class="card__text">Интеграция с внешними сервисами аналитики.</p>
                <div class="card__footer">
                  <button class="btn btn--sm btn--secondary">Подробнее</button>
                </div>
              </article>

              <article class="card">
                <div class="card__header">
                  <h3 class="card__title">Mobile App</h3>
                  <span class="badge">Планируется</span>
                </div>
                <p class="card__text">Разработка мобильного приложения.</p>
                <div class="card__footer">
                  <button class="btn btn--sm btn--ghost">В бэклог</button>
                </div>
              </article>
            </div>
          </section>

          <section class="section">
            <h2 class="section__title">Новый проект</h2>

            <form class="form" novalidate>
              <div class="form-group">
                <label class="label" for="name">Имя проекта</label>
                <input class="input" type="text" id="name" name="name" placeholder="Например, Nova Redesign" required>
                <p class="form-hint">Обязательное поле</p>
              </div>

              <div class="form-group">
                <label class="label" for="email">Email ответственного</label>
                <input class="input" type="email" id="email" name="email" placeholder="team@example.com" required>
              </div>

              <div class="form-group">
                <label class="label" for="description">Описание</label>
                <textarea class="textarea" id="description" name="description" rows="4" placeholder="Краткое описание..."></textarea>
              </div>

              <div class="form-group">
                <label class="checkbox">
                  <input type="checkbox" name="notify">
                  <span class="checkbox__control"></span>
                  <span class="checkbox__label">Отправлять уведомления</span>
                </label>
              </div>

              <div class="form-actions">
                <button class="btn" type="submit">Создать</button>
                <button class="btn btn--secondary" type="button">Отмена</button>
              </div>
            </form>
          </section>
        </div>
      </main>
    </div>
  </div>

  <script src="js/main.js" type="module"></script>
</body>
</html>
```

#### 2. `css/main.css`, `00-reset.css`, `01-tokens.css`, `02-base.css`, `03-layout.css`

Оставляем без изменений (из предыдущих глав).

#### 3. `css/04-components.css` (добавлены новые компоненты)

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

  &:hover {
    background-color: var(--color-surface-2);
    color: var(--color-text);
  }

  &.is-active {
    background-color: var(--color-primary-subtle);
    color: var(--color-primary);
  }
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

  &:hover:not(:disabled) {
    background-color: var(--btn-hover-bg);
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
   Card + Container Queries
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
  position: relative; /* для tooltip */
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
  color: var(--color-text);
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

.card--stat .card__value {
  margin-block: var(--space-2);
}

/* ========================================
   Form Components (без изменений)
   ======================================== */

.form {
  display: flex;
  flex-direction: column;
  gap: var(--space-6);
  max-width: 32rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);

  &:has(:invalid:not(:placeholder-shown):not(:focus)) {
    .input,
    .textarea {
      border-color: var(--color-danger);
    }

    .form-hint {
      color: var(--color-danger);
    }
  }

  &:has(:valid:not(:placeholder-shown)) {
    .input,
    .textarea {
      border-color: var(--color-success);
    }
  }
}

.label {
  font-size: var(--text-sm);
  font-weight: 500;
  color: var(--color-text);
}

.form-hint {
  font-size: var(--text-xs);
  color: var(--color-text-muted);
}

.input,
.textarea {
  width: 100%;
  padding: var(--space-3) var(--space-4);
  font-size: var(--text-sm);
  color: var(--color-text);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
  transition: border-color var(--transition-fast), box-shadow var(--transition-fast);

  &::placeholder {
    color: var(--color-text-muted);
  }

  &:hover {
    border-color: var(--color-border-strong);
  }

  &:focus {
    outline: none;
    border-color: var(--color-primary);
    box-shadow: 0 0 0 3px color-mix(in oklch, var(--color-primary) 25%, transparent);
  }
}

.textarea {
  resize: vertical;
  min-height: 6rem;
}

.checkbox {
  display: inline-flex;
  align-items: flex-start;
  gap: var(--space-3);
  cursor: pointer;
  user-select: none;

  input {
    position: absolute;
    opacity: 0;
    width: 0;
    height: 0;
  }
}

.checkbox__control {
  flex-shrink: 0;
  width: 1.15rem;
  height: 1.15rem;
  margin-top: 0.15rem;
  border: 1.5px solid var(--color-border-strong);
  border-radius: var(--radius-sm);
  background-color: var(--color-surface);
  display: grid;
  place-items: center;
  transition: background-color var(--transition-fast), border-color var(--transition-fast);

  &::after {
    content: "";
    width: 0.35rem;
    height: 0.6rem;
    border: solid white;
    border-width: 0 2px 2px 0;
    transform: rotate(45deg) scale(0);
    transition: transform var(--transition-fast);
  }
}

.checkbox input:checked + .checkbox__control {
  background-color: var(--color-primary);
  border-color: var(--color-primary);

  &::after {
    transform: rotate(45deg) scale(1);
  }
}

.checkbox input:focus-visible + .checkbox__control {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.checkbox__label {
  font-size: var(--text-sm);
  color: var(--color-text);
  line-height: 1.4;
}

.form-actions {
  display: flex;
  gap: var(--space-3);
  margin-top: var(--space-2);
}

/* ========================================
   Tooltip (Anchor Positioning)
   ======================================== */

.tooltip-trigger {
  display: inline-grid;
  place-items: center;
  width: 1.15rem;
  height: 1.15rem;
  font-size: 0.7rem;
  font-weight: 700;
  border-radius: var(--radius-full);
  background-color: var(--color-surface-2);
  color: var(--color-text-muted);
  border: 1px solid var(--color-border);
  cursor: help;

  anchor-name: --tooltip-trigger;
}

.tooltip {
  position: absolute;
  position-anchor: --tooltip-trigger;
  position-area: block-start;
  margin-bottom: var(--space-2);

  max-width: 16rem;
  padding: var(--space-2) var(--space-3);
  font-size: var(--text-xs);
  line-height: 1.4;
  color: var(--color-text-inverse);
  background-color: var(--color-text);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-md);

  opacity: 0;
  visibility: hidden;
  transition: opacity var(--transition-fast), visibility var(--transition-fast);
  z-index: 50;
  pointer-events: none;
}

/* Показываем tooltip при наведении / фокусе */
.tooltip-trigger:hover + .tooltip,
.tooltip-trigger:focus-visible + .tooltip,
.card:has(.tooltip-trigger:hover) .tooltip,
.card:has(.tooltip-trigger:focus-visible) .tooltip {
  opacity: 1;
  visibility: visible;
}

/* ========================================
   Dropdown (Anchor Positioning)
   ======================================== */

.dropdown {
  position: relative;
}

.dropdown > .btn {
  anchor-name: --dropdown-trigger;
}

.dropdown__menu {
  position: absolute;
  position-anchor: --dropdown-trigger;
  position-area: block-end span-inline-end;
  position-try-fallbacks: flip-block, flip-inline;

  min-width: 11rem;
  margin-top: var(--space-2);
  padding: var(--space-1);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-lg);

  opacity: 0;
  visibility: hidden;
  transform: translateY(-4px);
  transition: opacity var(--transition-fast), transform var(--transition-fast), visibility var(--transition-fast);
  z-index: 40;
}

.dropdown.is-open .dropdown__menu {
  opacity: 1;
  visibility: visible;
  transform: translateY(0);
}

.dropdown__item {
  display: block;
  padding: var(--space-2) var(--space-3);
  font-size: var(--text-sm);
  color: var(--color-text);
  border-radius: var(--radius-md);
  transition: background-color var(--transition-fast);

  &:hover {
    background-color: var(--color-surface-2);
  }
}

.dropdown__item--danger {
  color: var(--color-danger);
}

.dropdown__divider {
  height: 1px;
  margin: var(--space-1) 0;
  background-color: var(--color-border);
}
```

#### 4. `js/main.js` (добавлена логика Dropdown)

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
  icon.textContent = theme === "dark" ? "☀️" : "🌙";
}

setTheme(getPreferredTheme());

toggle?.addEventListener("click", () => {
  const current = html.getAttribute("data-theme");
  const next = current === "dark" ? "light" : "dark";
  setTheme(next);
});

window.matchMedia("(prefers-color-scheme: dark)").addEventListener("change", (e) => {
  if (!localStorage.getItem("nova-theme")) {
    setTheme(e.matches ? "dark" : "light");
  }
});

/* ===== Dropdown ===== */
const dropdown = document.querySelector(".dropdown");
const dropdownBtn = document.querySelector("#user-menu-btn");

dropdownBtn?.addEventListener("click", (e) => {
  e.stopPropagation();
  const isOpen = dropdown.classList.toggle("is-open");
  dropdownBtn.setAttribute("aria-expanded", isOpen);
});

// Закрытие при клике снаружи
document.addEventListener("click", () => {
  dropdown?.classList.remove("is-open");
  dropdownBtn?.setAttribute("aria-expanded", "false");
});
```

---

### Что мы получили в этой главе

- Рабочий **Tooltip** на чистом CSS с использованием Anchor Positioning.
- **Dropdown**-меню с умным позиционированием и fallback-поведением.
- Минимальный JavaScript только для управления состоянием открытия (сам позиционный механизм — полностью CSS).
- Понимание, как создавать всплывающие интерфейсы без сложных библиотек.
- Готовность использовать тот же подход для Popover, Context Menu и других паттернов.

В следующей главе мы перейдём к анимациям: View Transitions и Scroll-driven Animations, чтобы сделать интерфейс живым и современным.