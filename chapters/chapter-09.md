**Глава 9. Popover API, модальные окна и изоляция компонентов**

### Цель главы

Мы уже умеем делать тултипы и выпадающие меню с помощью Anchor Positioning.  
Теперь добавим полноценные **модальные окна** и научимся правильно изолировать стили компонентов.

В этой главе мы:

- Используем нативный **Popover API**.
- Создадим компонент **Modal / Dialog**.
- Покажем, как сочетать Popover API с современным CSS.
- Улучшим изоляцию стилей с помощью `@scope` (где это уместно).
- Приведём архитектуру компонентов к более строгому и масштабируемому виду.

К концу главы в проекте Nova появится рабочее модальное окно, а компоненты станут ещё более предсказуемыми.

---

### Ключевые технологии

- Popover API (`popover`, `popovertarget`, `:popover-open`)
- Anchor Positioning (для позиционирования, если нужно)
- `@scope`
- Улучшение контрактов компонентов
- Правильная работа с фокусом и доступностью (базовый уровень)

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
│   ├── 04-components.css      ← добавлен Modal + улучшения
│   ├── 05-utilities.css
│   ├── 06-animations.css
│   └── main.css
├── js/
│   └── main.js                ← логика открытия/закрытия модалки
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
          <button class="btn" popovertarget="create-project-modal" popovertargetaction="show">
            Новый проект
          </button>

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
            </div>
          </section>

          <section class="section">
            <h2 class="section__title">Быстрые действия</h2>
            <div class="card reveal">
              <p class="card__text">Создайте новый проект с помощью модального окна.</p>
              <div class="card__footer">
                <button class="btn" popovertarget="create-project-modal">Создать проект</button>
              </div>
            </div>
          </section>
        </div>
      </main>
    </div>
  </div>

  <!-- ====================== MODAL ====================== -->
  <div id="create-project-modal" popover class="modal">
    <div class="modal__content">
      <div class="modal__header">
        <h2 class="modal__title">Новый проект</h2>
        <button class="modal__close" popovertarget="create-project-modal" popovertargetaction="hide" aria-label="Закрыть">
          ×
        </button>
      </div>

      <form class="form" novalidate>
        <div class="form-group">
          <label class="label" for="modal-name">Имя проекта</label>
          <input class="input" type="text" id="modal-name" name="name" placeholder="Например, Nova Redesign" required>
        </div>

        <div class="form-group">
          <label class="label" for="modal-email">Email ответственного</label>
          <input class="input" type="email" id="modal-email" name="email" placeholder="team@example.com" required>
        </div>

        <div class="form-group">
          <label class="label" for="modal-description">Описание</label>
          <textarea class="textarea" id="modal-description" name="description" rows="3" placeholder="Краткое описание..."></textarea>
        </div>

        <div class="form-actions">
          <button class="btn" type="submit">Создать</button>
          <button class="btn btn--secondary" type="button" popovertarget="create-project-modal" popovertargetaction="hide">
            Отмена
          </button>
        </div>
      </form>
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

#### 3. Основные файлы (`00-reset.css` – `03-layout.css`, `06-animations.css`)

Оставляем без изменений.

#### 4. `css/04-components.css` — добавляем Modal и улучшения

Ниже приведены **новые и изменённые** части файла. Остальные компоненты (Button, Card, Form, Tooltip, Dropdown и т.д.) сохраняются из предыдущих глав.

```css
/* ========================================
   Modal (Popover API)
   ======================================== */

.modal {
  /* Сброс стилей браузера для popover */
  border: none;
  padding: 0;
  background: transparent;
  max-width: calc(100vw - 2rem);
  max-height: calc(100dvh - 2rem);
}

.modal::backdrop {
  background-color: oklch(0 0 0 / 0.45);
  backdrop-filter: blur(4px);
  transition: opacity 0.25s ease;
}

.modal__content {
  width: min(28rem, 100%);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-lg);
  padding: var(--space-6);
  animation: modal-in 0.25s cubic-bezier(0.22, 1, 0.36, 1);
}

.modal__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: var(--space-6);
}

.modal__title {
  font-size: var(--text-xl);
  font-weight: 600;
}

.modal__close {
  width: 2rem;
  height: 2rem;
  display: grid;
  place-items: center;
  border-radius: var(--radius-md);
  font-size: 1.4rem;
  line-height: 1;
  color: var(--color-text-muted);
  transition: background-color var(--transition-fast), color var(--transition-fast);
}

.modal__close:hover {
  background-color: var(--color-surface-2);
  color: var(--color-text);
}

/* Анимация появления */
@keyframes modal-in {
  from {
    opacity: 0;
    transform: scale(0.95) translateY(10px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}

/* Когда модалка открыта */
.modal:popover-open {
  display: flex;
  align-items: center;
  justify-content: center;
}

/* ========================================
   Улучшение изоляции с помощью @scope (пример)
   ======================================== */

@scope (.card) {
  :scope {
    /* стили, которые применяются только внутри .card */
  }

  .card__title {
    /* можно дополнительно уточнять */
  }
}
```

> Примечание: `@scope` здесь показан как пример. В реальном проекте его особенно полезно применять, когда нужно жёстко ограничить влияние стилей внутри большого компонента или при работе с сторонним HTML.

#### 5. `js/main.js`

Логика темы и dropdown остаётся. Добавлять специальный JS для модалки не обязательно — Popover API работает нативно через `popovertarget`.  

Однако можно добавить небольшой улучшайзинг (фокус, закрытие по Escape уже работает нативно в большинстве браузеров):

```js
// ... предыдущий код темы и dropdown ...

// Дополнительно: закрытие модалки по клику на backdrop уже работает нативно.
// Popover API сам обрабатывает Escape.
```

#### 6. `css/05-utilities.css`

По-прежнему можно оставлять пустым или начать добавлять мелкие утилиты по необходимости.

---

### Что мы получили в этой главе

- Полноценное **модальное окно** на нативном Popover API.
- Красивое затемнение фона (`::backdrop`) + плавная анимация появления.
- Кнопки открытия/закрытия без написания сложного JavaScript.
- Пример использования `@scope` для изоляции.
- Ещё один важный паттерн современной UI-библиотеки.

В следующей главе мы начнём собирать всё в единую **Design System**: наведём порядок в токенах, стандартизируем API компонентов и подготовим проект к масштабированию.