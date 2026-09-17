**Глава 14. Финальная полировка и живой Style Guide**

### Цель главы

Мы прошли большой путь: от архитектуры и токенов до сложных компонентов, анимаций, производительности и доступности.  
Теперь нужно привести всё в финальный, аккуратный вид и создать **живой Style Guide** — страницу, на которой можно видеть все компоненты Design System в одном месте.

В этой главе мы:

- Создадим страницу Style Guide.
- Проведём финальную полировку компонентов.
- Добавим недостающие утилиты.
- Унифицируем мелкие детали (отступы, состояния, именование).
- Подготовим проект к завершению практического руководства.

К концу главы у вас будет не просто приложение, а законченная, документированная UI-система.

---

### Структура проекта на данном этапе

```
nova/
├── index.html                  ← основное приложение (Dashboard)
├── style-guide.html            ← новая страница Style Guide
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css
│   ├── 02-base.css
│   ├── 03-layout.css
│   ├── components/
│   │   ├── _buttons.css
│   │   ├── _badges.css
│   │   ├── _cards.css
│   │   ├── _forms.css
│   │   ├── _navigation.css
│   │   ├── _overlays.css
│   │   ├── _tabs.css
│   │   ├── _accordion.css
│   │   └── _toast.css
│   ├── 04-components.css
│   ├── 05-utilities.css        ← расширен
│   ├── 06-animations.css
│   ├── 07-performance.css
│   ├── 08-a11y.css
│   └── main.css
├── js/
│   └── main.js
└── assets/
```

---

### Содержание файлов

#### 1. `style-guide.html` (новая страница)

```html
<!DOCTYPE html>
<html lang="ru" data-theme="system">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Nova Design System — Style Guide</title>
  <meta name="color-scheme" content="light dark">
  <link rel="stylesheet" href="css/main.css">
  <style>
    /* Небольшие стили только для Style Guide */
    .sg-section {
      margin-bottom: var(--space-16);
      padding-bottom: var(--space-10);
      border-bottom: 1px solid var(--color-border);
    }
    .sg-title {
      font-size: var(--text-3xl);
      margin-bottom: var(--space-2);
    }
    .sg-description {
      color: var(--color-text-muted);
      margin-bottom: var(--space-8);
      max-width: 40rem;
    }
    .sg-row {
      display: flex;
      flex-wrap: wrap;
      gap: var(--space-4);
      margin-bottom: var(--space-6);
      align-items: center;
    }
    .sg-label {
      font-size: var(--text-sm);
      font-weight: 600;
      color: var(--color-text-muted);
      margin-bottom: var(--space-2);
    }
  </style>
</head>
<body>
  <a href="#main" class="skip-link">Перейти к содержимому</a>

  <div class="app">
    <aside class="sidebar">
      <div class="sidebar__brand">Nova DS</div>
      <nav class="sidebar__nav" aria-label="Навигация Style Guide">
        <a href="index.html" class="nav-link">← К приложению</a>
        <a href="#colors" class="nav-link">Цвета</a>
        <a href="#typography" class="nav-link">Типографика</a>
        <a href="#buttons" class="nav-link">Buttons</a>
        <a href="#forms" class="nav-link">Forms</a>
        <a href="#cards" class="nav-link">Cards</a>
        <a href="#overlays" class="nav-link">Overlays</a>
        <a href="#tabs" class="nav-link">Tabs & Accordion</a>
      </nav>
    </aside>

    <div class="main">
      <header class="header">
        <h1 class="header__title">Nova Design System</h1>
        <div class="header__actions">
          <button class="theme-toggle" type="button" aria-label="Переключить тему">
            <span class="theme-toggle__icon" data-theme-icon>🌓</span>
          </button>
        </div>
      </header>

      <main class="content" id="main">
        <!-- Colors -->
        <section class="sg-section" id="colors">
          <h2 class="sg-title">Цвета</h2>
          <p class="sg-description">Семантические токены на основе OKLCH. Поддерживают светлую и тёмную тему.</p>
          
          <div class="sg-row">
            <div class="card" style="width: 140px; text-align: center;">
              <div style="height: 48px; background: var(--color-primary); border-radius: var(--radius-md);"></div>
              <p class="sg-label" style="margin-top: 8px;">Primary</p>
            </div>
            <div class="card" style="width: 140px; text-align: center;">
              <div style="height: 48px; background: var(--color-success); border-radius: var(--radius-md);"></div>
              <p class="sg-label" style="margin-top: 8px;">Success</p>
            </div>
            <div class="card" style="width: 140px; text-align: center;">
              <div style="height: 48px; background: var(--color-warning); border-radius: var(--radius-md);"></div>
              <p class="sg-label" style="margin-top: 8px;">Warning</p>
            </div>
            <div class="card" style="width: 140px; text-align: center;">
              <div style="height: 48px; background: var(--color-danger); border-radius: var(--radius-md);"></div>
              <p class="sg-label" style="margin-top: 8px;">Danger</p>
            </div>
          </div>
        </section>

        <!-- Buttons -->
        <section class="sg-section" id="buttons">
          <h2 class="sg-title">Buttons</h2>
          <p class="sg-description">Основной компонент действий. Поддерживает варианты и размеры.</p>

          <div class="sg-label">Варианты</div>
          <div class="sg-row">
            <button class="btn">Primary</button>
            <button class="btn btn--secondary">Secondary</button>
            <button class="btn btn--ghost">Ghost</button>
            <button class="btn btn--danger">Danger</button>
            <button class="btn" disabled>Disabled</button>
          </div>

          <div class="sg-label">Размеры</div>
          <div class="sg-row">
            <button class="btn">Default</button>
            <button class="btn btn--sm">Small</button>
          </div>
        </section>

        <!-- Forms -->
        <section class="sg-section" id="forms">
          <h2 class="sg-title">Forms</h2>
          <p class="sg-description">Поля ввода с поддержкой :has() для состояний валидации.</p>

          <form class="form" style="max-width: 24rem;">
            <div class="form-group">
              <label class="label" for="sg-name">Имя</label>
              <input class="input" type="text" id="sg-name" placeholder="Введите имя" required>
            </div>
            <div class="form-group">
              <label class="label" for="sg-email">Email</label>
              <input class="input" type="email" id="sg-email" placeholder="email@example.com" required>
            </div>
            <div class="form-group">
              <label class="checkbox">
                <input type="checkbox">
                <span class="checkbox__control"></span>
                <span class="checkbox__label">Согласен с условиями</span>
              </label>
            </div>
            <button class="btn" type="submit">Отправить</button>
          </form>
        </section>

        <!-- Cards & Badges -->
        <section class="sg-section" id="cards">
          <h2 class="sg-title">Cards & Badges</h2>
          
          <div class="cards-grid">
            <article class="card">
              <div class="card__header">
                <h3 class="card__title">Заголовок карточки</h3>
                <span class="badge badge--success">Active</span>
              </div>
              <p class="card__text">Пример текста внутри карточки с бейджем.</p>
              <div class="card__footer">
                <button class="btn btn--sm">Действие</button>
              </div>
            </article>
          </div>
        </section>

        <!-- Tabs -->
        <section class="sg-section" id="tabs">
          <h2 class="sg-title">Tabs</h2>
          
          <div class="tabs" data-tabs>
            <div class="tabs__list" role="tablist">
              <button class="tabs__trigger" role="tab" aria-selected="true" aria-controls="sg-tab-1">Первая</button>
              <button class="tabs__trigger" role="tab" aria-selected="false" aria-controls="sg-tab-2">Вторая</button>
              <button class="tabs__trigger" role="tab" aria-selected="false" aria-controls="sg-tab-3">Третья</button>
            </div>
            <div class="tabs__panel is-active" role="tabpanel" id="sg-tab-1">Содержимое первой вкладки</div>
            <div class="tabs__panel" role="tabpanel" id="sg-tab-2" hidden>Содержимое второй вкладки</div>
            <div class="tabs__panel" role="tabpanel" id="sg-tab-3" hidden>Содержимое третьей вкладки</div>
          </div>
        </section>

        <!-- Accordion -->
        <section class="sg-section">
          <h2 class="sg-title">Accordion</h2>
          
          <div class="accordion" data-accordion style="max-width: 32rem;">
            <div class="accordion__item">
              <button class="accordion__trigger" aria-expanded="false" aria-controls="sg-acc-1">
                Вопрос номер один
              </button>
              <div class="accordion__panel" id="sg-acc-1">
                <div class="accordion__content">Ответ на первый вопрос.</div>
              </div>
            </div>
            <div class="accordion__item">
              <button class="accordion__trigger" aria-expanded="false" aria-controls="sg-acc-2">
                Вопрос номер два
              </button>
              <div class="accordion__panel" id="sg-acc-2">
                <div class="accordion__content">Ответ на второй вопрос.</div>
              </div>
            </div>
          </div>
        </section>

        <!-- Toast demo -->
        <section class="sg-section">
          <h2 class="sg-title">Toast</h2>
          <button class="btn" id="sg-toast-btn">Показать Toast</button>
        </section>
      </main>
    </div>
  </div>

  <div class="toast-container" id="toast-container"></div>

  <script src="js/main.js" type="module"></script>
</body>
</html>
```

#### 2. `css/05-utilities.css` (расширенный)

```css
/* ========================================
   Utilities
   ======================================== */

/* Spacing */
.u-mt-0 { margin-top: 0; }
.u-mt-2 { margin-top: var(--space-2); }
.u-mt-4 { margin-top: var(--space-4); }
.u-mt-6 { margin-top: var(--space-6); }
.u-mt-8 { margin-top: var(--space-8); }

.u-mb-0 { margin-bottom: 0; }
.u-mb-2 { margin-bottom: var(--space-2); }
.u-mb-4 { margin-bottom: var(--space-4); }
.u-mb-6 { margin-bottom: var(--space-6); }
.u-mb-8 { margin-bottom: var(--space-8); }

/* Text */
.u-text-muted { color: var(--color-text-muted); }
.u-text-sm { font-size: var(--text-sm); }
.u-text-lg { font-size: var(--text-lg); }
.u-font-medium { font-weight: 500; }
.u-font-semibold { font-weight: 600; }

/* Layout helpers */
.u-flex { display: flex; }
.u-flex-col { flex-direction: column; }
.u-items-center { align-items: center; }
.u-justify-between { justify-content: space-between; }
.u-gap-2 { gap: var(--space-2); }
.u-gap-3 { gap: var(--space-3); }
.u-gap-4 { gap: var(--space-4); }

/* Visibility */
.u-sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

#### 3. `js/main.js`

Добавляем поддержку кнопки Toast на странице Style Guide (уже существующая функция `showToast` работает):

```js
// В конце файла
document.getElementById("sg-toast-btn")?.addEventListener("click", () => {
  showToast({
    title: "Design System",
    message: "Это пример уведомления из Style Guide",
    type: "success"
  });
});
```

Остальная логика (тема, tabs, accordion, dropdown, toast) остаётся без изменений.

#### 4. Остальные файлы

Все предыдущие CSS-файлы и компоненты сохраняются в том виде, в котором они были после Главы 13.  
Мы не переписываем их, а только добавляем Style Guide и утилиты.

---

### Что мы получили в этой главе

- Полноценную страницу **Style Guide** (`style-guide.html`).
- Возможность быстро просматривать все компоненты в одном месте.
- Расширенный набор utility-классов.
- Финальную полировку структуры проекта.
- Проект, который уже можно использовать как основу реальной Design System.

В следующей (заключительной) главе мы подведём итоги всего практического руководства, сделаем финальный чек-лист использованных возможностей современного CSS и дадим рекомендации по дальнейшему развитию проекта.