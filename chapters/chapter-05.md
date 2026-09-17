**Глава 5. Современный Layout: CSS Grid, Subgrid и Container Queries**

### Цель главы

До этого момента наш layout был относительно простым.  
Теперь мы переходим к по-настоящему современному подходу к компоновке интерфейсов.

В этой главе мы:

- Углубим использование **CSS Grid**.
- Добавим **Container Queries** — адаптивность на уровне компонентов, а не viewport.
- Покажем, когда и зачем использовать **Subgrid**.
- Сделаем карточки, формы и общую сетку приложения по-настоящему гибкими.
- Подготовим почву для сложных макетов (дашборд, каталог, детальные страницы).

К концу главы компоненты Nova будут адаптироваться к размеру своего контейнера, а не только к ширине экрана.

---

### Ключевые идеи

1. **Media Queries** — для страницы в целом.  
2. **Container Queries** — для компонентов.  
3. Grid отлично подходит для двумерных макетов.  
4. Flexbox остаётся лучшим выбором для одномерных композиций (кнопки, навигация, группы действий).  
5. Container Queries + Design Tokens = настоящая компонентная адаптивность.

---

### Структура проекта на данном этапе

```
nova/
├── index.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css
│   ├── 02-base.css
│   ├── 03-layout.css          ← значительно обновлён
│   ├── 04-components.css      ← добавлены container queries
│   ├── 05-utilities.css
│   └── main.css
├── js/
│   └── main.js
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
                <h3 class="card__title">Активные проекты</h3>
                <span class="badge badge--success">12</span>
              </div>
              <p class="card__value">12</p>
              <p class="card__text">+3 за последнюю неделю</p>
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

        <!-- Основной контент + форма -->
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
                <p class="card__text">Разработка мобильного приложения на React Native.</p>
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

#### 2. `css/main.css`, `00-reset.css`, `01-tokens.css`, `02-base.css`

Без изменений.

#### 3. `css/03-layout.css` (обновлённый)

```css
/* ========================================
   App Shell
   ======================================== */

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
  position: sticky;
  top: 0;
  height: 100dvh;
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
  min-width: 0; /* важно для Grid */
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-inline: var(--space-8);
  background-color: var(--color-surface);
  border-bottom: 1px solid var(--color-border);
  position: sticky;
  top: 0;
  z-index: 10;
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
  overflow-x: hidden;
}

/* ========================================
   Sections & Grids
   ======================================== */

.section {
  margin-bottom: var(--space-12);
}

.section__title {
  font-size: var(--text-2xl);
  margin-bottom: var(--space-6);
}

/* Статистика — адаптивная сетка */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: var(--space-6);
}

/* Основной контент: проекты + форма */
.content-grid {
  display: grid;
  grid-template-columns: 1.6fr 1fr;
  gap: var(--space-10);
  align-items: start;
}

/* Сетка карточек */
.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: var(--space-6);
}

/* Тема */
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

/* ========================================
   Адаптивность на уровне страницы
   ======================================== */

@media (max-width: 1100px) {
  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
  }

  .content-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .app {
    grid-template-columns: 1fr;
  }

  .sidebar {
    display: none; /* в реальном проекте здесь будет мобильное меню */
  }

  .stats-grid {
    grid-template-columns: 1fr;
  }
}
```

#### 4. `css/04-components.css` (добавлены Container Queries)

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

/* Container Query для карточек */
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

/* Статистические карточки */
.card--stat {
  .card__value {
    margin-block: var(--space-2);
  }
}

/* ========================================
   Form Components
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
  --input-bg: var(--color-surface);
  --input-border: var(--color-border);
  --input-color: var(--color-text);
  --input-placeholder: var(--color-text-muted);
  --input-radius: var(--radius-md);
  --input-padding-y: var(--space-3);
  --input-padding-x: var(--space-4);

  width: 100%;
  padding: var(--input-padding-y) var(--input-padding-x);
  font-size: var(--text-sm);
  color: var(--input-color);
  background-color: var(--input-bg);
  border: 1px solid var(--input-border);
  border-radius: var(--input-radius);
  transition: border-color var(--transition-fast), box-shadow var(--transition-fast);

  &::placeholder {
    color: var(--input-placeholder);
  }

  &:hover {
    border-color: var(--color-border-strong);
  }

  &:focus {
    outline: none;
    border-color: var(--color-primary);
    box-shadow: 0 0 0 3px color-mix(in oklch, var(--color-primary) 25%, transparent);
  }

  &:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }
}

.textarea {
  resize: vertical;
  min-height: 6rem;
}

/* Checkbox */
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
```

#### 5. `css/05-utilities.css` и `js/main.js`

Без изменений.

---

### Что мы получили в этой главе

- Полноценное использование **CSS Grid** для сложных макетов (stats, content-grid, cards).
- Введение **Container Queries** — карточки теперь адаптируются к ширине своего контейнера.
- Правильное разделение ответственности: Media Queries для страницы, Container Queries для компонентов.
- Более устойчивый и современный layout приложения.
- Готовность к дальнейшему усложнению интерфейса (каталог, детальные страницы, модальные окна).

В следующей главе мы добавим **Anchor Positioning** (tooltips, dropdowns, popovers) и начнём создавать по-настоящему сложные интерактивные паттерны без JavaScript.