**Глава 8. Scroll-driven Animations: анимация от скролла без JavaScript**

### Цель главы

В предыдущей главе мы добавили View Transitions и микро-взаимодействия.  
Теперь делаем интерфейс по-настоящему динамичным с помощью **Scroll-driven Animations** — одной из самых мощных возможностей CSS 2026.

В этой главе мы:

- Научимся привязывать анимации к скроллу страницы и к видимости элементов.
- Создадим эффекты появления карточек при скролле.
- Добавим простой параллакс и анимированные индикаторы прогресса.
- Покажем, как делать это без `IntersectionObserver` и тяжёлого JavaScript.
- Соблюдём доступность (`prefers-reduced-motion`).

К концу главы длинные страницы Nova будут ощущаться живыми и современными.

---

### Ключевые технологии

- `animation-timeline: scroll()`
- `animation-timeline: view()`
- `animation-range`
- `scroll-timeline`
- `view-timeline`
- Сочетание со Sticky-элементами

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
│   ├── 04-components.css
│   ├── 05-utilities.css
│   ├── 06-animations.css      ← значительно расширен
│   └── main.css
├── js/
│   └── main.js
└── assets/
```

---

### Содержание файлов

#### 1. `index.html`

Мы добавляем больше контента, чтобы скролл имел смысл, и размечаем элементы для scroll-driven анимаций.

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
        <!-- Прогресс скролла страницы -->
        <div class="scroll-progress" aria-hidden="true"></div>

        <section class="section">
          <h2 class="section__title">Обзор</h2>

          <div class="stats-grid">
            <article class="card card--stat reveal">
              <div class="card__header">
                <h3 class="card__title">
                  Активные проекты
                  <button class="tooltip-trigger" aria-describedby="tip-projects" type="button">?</button>
                </h3>
                <span class="badge badge--success">12</span>
              </div>
              <p class="card__value">12</p>
              <p class="card__text">+3 за последнюю неделю</p>
              <div class="tooltip" id="tip-projects" role="tooltip">
                Количество проектов со статусом «В работе»
              </div>
            </article>

            <article class="card card--stat reveal">
              <div class="card__header">
                <h3 class="card__title">Задачи</h3>
                <span class="badge badge--warning">28</span>
              </div>
              <p class="card__value">28</p>
              <p class="card__text">5 требуют внимания</p>
            </article>

            <article class="card card--stat reveal">
              <div class="card__header">
                <h3 class="card__title">Ошибки</h3>
                <span class="badge badge--danger">2</span>
              </div>
              <p class="card__value">2</p>
              <p class="card__text">Критические</p>
            </article>

            <article class="card card--stat reveal">
              <div class="card__header">
                <h3 class="card__title">Команда</h3>
                <span class="badge">8</span>
              </div>
              <p class="card__value">8</p>
              <p class="card__text">Участников</p>
            </article>
          </div>
        </section>

        <div class="content-grid">
          <section class="section">
            <h2 class="section__title">Проекты</h2>

            <div class="cards-grid">
              <article class="card reveal">
                <div class="card__header">
                  <h3 class="card__title">Nova Redesign</h3>
                  <span class="badge badge--success">В работе</span>
                </div>
                <p class="card__text">Полный редизайн дизайн-системы и дашборда.</p>
                <div class="card__footer">
                  <button class="btn btn--sm">Открыть</button>
                </div>
              </article>

              <article class="card reveal">
                <div class="card__header">
                  <h3 class="card__title">API Integration</h3>
                  <span class="badge badge--warning">Ожидание</span>
                </div>
                <p class="card__text">Интеграция с внешними сервисами аналитики.</p>
                <div class="card__footer">
                  <button class="btn btn--sm btn--secondary">Подробнее</button>
                </div>
              </article>

              <article class="card reveal">
                <div class="card__header">
                  <h3 class="card__title">Mobile App</h3>
                  <span class="badge">Планируется</span>
                </div>
                <p class="card__text">Разработка мобильного приложения.</p>
                <div class="card__footer">
                  <button class="btn btn--sm btn--ghost">В бэклог</button>
                </div>
              </article>

              <article class="card reveal">
                <div class="card__header">
                  <h3 class="card__title">Design System 2.0</h3>
                  <span class="badge badge--success">В работе</span>
                </div>
                <p class="card__text">Обновление токенов и компонентов.</p>
                <div class="card__footer">
                  <button class="btn btn--sm">Открыть</button>
                </div>
              </article>
            </div>
          </section>

          <section class="section">
            <h2 class="section__title">Новый проект</h2>

            <form class="form reveal" novalidate>
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

        <!-- Дополнительный контент для демонстрации скролла -->
        <section class="section section--tall">
          <h2 class="section__title">Активность</h2>
          <div class="activity-list">
            <div class="activity-item reveal">Обновлён статус проекта «Nova Redesign»</div>
            <div class="activity-item reveal">Добавлен новый участник в команду</div>
            <div class="activity-item reveal">Закрыто 4 задачи</div>
            <div class="activity-item reveal">Создан новый проект «API Integration»</div>
            <div class="activity-item reveal">Обновлены токены дизайн-системы</div>
          </div>
        </section>
      </main>
    </div>
  </div>

  <script src="js/main.js" type="module"></script>
</body>
</html>
```

#### 2. `css/main.css`

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

#### 3. Файлы `00-reset.css` → `04-components.css`

Оставляем без изменений (содержимое из предыдущих глав).

#### 4. `css/06-animations.css` (полностью обновлённый)

```css
/* ========================================
   Reduced Motion
   ======================================== */

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }

  .reveal {
    opacity: 1 !important;
    transform: none !important;
  }
}

/* ========================================
   View Transitions (из предыдущей главы)
   ======================================== */

@view-transition {
  navigation: auto;
}

::view-transition-old(root),
::view-transition-new(root) {
  animation-duration: 0.45s;
  animation-timing-function: cubic-bezier(0.22, 1, 0.36, 1);
}

::view-transition-new(root) {
  animation-name: fade-in-scale;
}

::view-transition-old(root) {
  animation-name: fade-out;
}

@keyframes fade-in-scale {
  from {
    opacity: 0;
    transform: scale(1.02);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

@keyframes fade-out {
  from { opacity: 1; }
  to { opacity: 0; }
}

/* ========================================
   Scroll Progress Bar
   ======================================== */

.scroll-progress {
  position: fixed;
  top: 0;
  left: 0;
  height: 3px;
  width: 100%;
  background: var(--color-primary);
  transform-origin: 0 50%;
  z-index: 100;
  animation: scroll-progress linear;
  animation-timeline: scroll(root);
}

@keyframes scroll-progress {
  from { transform: scaleX(0); }
  to { transform: scaleX(1); }
}

/* ========================================
   Reveal on Scroll (View Timeline)
   ======================================== */

.reveal {
  opacity: 0;
  transform: translateY(30px);
  animation: reveal linear both;
  animation-timeline: view();
  animation-range: entry 0% cover 40%;
}

@keyframes reveal {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Разные задержки для последовательного появления */
.stats-grid .reveal:nth-child(1) { animation-delay: 0ms; }
.stats-grid .reveal:nth-child(2) { animation-delay: 60ms; }
.stats-grid .reveal:nth-child(3) { animation-delay: 120ms; }
.stats-grid .reveal:nth-child(4) { animation-delay: 180ms; }

.cards-grid .reveal:nth-child(1) { animation-delay: 0ms; }
.cards-grid .reveal:nth-child(2) { animation-delay: 80ms; }
.cards-grid .reveal:nth-child(3) { animation-delay: 160ms; }
.cards-grid .reveal:nth-child(4) { animation-delay: 240ms; }

/* ========================================
   Микро-взаимодействия
   ======================================== */

.btn {
  transition:
    background-color var(--transition-fast),
    border-color var(--transition-fast),
    color var(--transition-fast),
    transform var(--transition-fast),
    box-shadow var(--transition-fast);
}

.btn:active:not(:disabled) {
  transform: scale(0.97);
}

.card {
  transition:
    transform var(--transition-base),
    box-shadow var(--transition-base),
    border-color var(--transition-base);
}

.card:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-md);
}

/* Dropdown & Tooltip */
.dropdown__menu {
  transition:
    opacity 0.2s cubic-bezier(0.22, 1, 0.36, 1),
    transform 0.2s cubic-bezier(0.22, 1, 0.36, 1),
    visibility 0.2s;
}

.tooltip {
  transition:
    opacity 0.18s ease,
    visibility 0.18s ease,
    transform 0.18s ease;
  transform: translateY(4px);
}

.tooltip-trigger:hover + .tooltip,
.tooltip-trigger:focus-visible + .tooltip,
.card:has(.tooltip-trigger:hover) .tooltip,
.card:has(.tooltip-trigger:focus-visible) .tooltip {
  transform: translateY(0);
}

/* ========================================
   Activity list (дополнительный пример)
   ======================================== */

.section--tall {
  margin-top: var(--space-16);
}

.activity-list {
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
}

.activity-item {
  padding: var(--space-4);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  font-size: var(--text-sm);
}
```

#### 5. `js/main.js`

Оставляем без изменений (логика темы + dropdown из предыдущих глав).  
Scroll-driven Animations работают полностью на CSS.

#### 6. `css/03-layout.css` — небольшое дополнение

Добавьте в конец файла (или в соответствующее место):

```css
/* Для корректной работы scroll-progress и длинного контента */
.content {
  padding: var(--space-8);
  overflow-x: hidden;
  padding-bottom: var(--space-20);
}
```

---

### Что мы получили в этой главе

- Полноценный **scroll-progress** индикатор на чистом CSS.
- Эффект **reveal** элементов при появлении во viewport с помощью `animation-timeline: view()`.
- Поддержку `prefers-reduced-motion`.
- Понимание разницы между `scroll()` и `view()` timelines.
- Готовность использовать более сложные scroll-driven эффекты (параллакс, sticky-анимации, прогресс секций).

В следующей главе мы начнём собирать всё вместе: создадим более сложные паттерны, улучшим архитектуру слоёв и подготовим проект к финальной дизайн-системе.