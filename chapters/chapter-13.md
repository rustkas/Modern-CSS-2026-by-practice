**Глава 13. Доступность и полировка Design System**

### Цель главы

Мы создали современную, производительную и богатую возможностями UI-систему.  
Теперь нужно сделать её по-настоящему качественной с точки зрения **доступности** и удобства использования.

В этой главе мы:

- Улучшим клавиатурную навигацию.
- Добавим и доработаем ARIA-атрибуты.
- Сделаем фокус более заметным и предсказуемым.
- Добавим skip-link.
- Проверим и улучшим контрастность.
- Приведём все интерактивные компоненты к единому уровню доступности.

К концу главы проект Nova будет значительно более дружелюбным для пользователей клавиатуры, скринридеров и людей с ограниченными возможностями.

---

### Основные принципы доступности, которые мы применяем

1. Все интерактивные элементы доступны с клавиатуры.
2. Фокус всегда виден (`:focus-visible`).
3. Используем правильные ARIA-роли и состояния.
4. Не полагаемся только на цвет.
5. Поддерживаем `prefers-reduced-motion` (уже сделано ранее).
6. Логический порядок фокуса соответствует визуальному.

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
│   ├── 05-utilities.css
│   ├── 06-animations.css
│   ├── 07-performance.css
│   ├── 08-a11y.css              ← новый файл
│   └── main.css
├── js/
│   └── main.js
└── assets/
```

---

### Содержание файлов

#### 1. `css/main.css`

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

#### 2. `css/08-a11y.css` (новый файл)

```css
/* ========================================
   Accessibility & Focus Management
   ======================================== */

/* Skip link */
.skip-link {
  position: absolute;
  top: -100%;
  left: var(--space-4);
  z-index: 1000;
  padding: var(--space-3) var(--space-4);
  background-color: var(--color-primary);
  color: var(--color-text-inverse);
  font-weight: 600;
  font-size: var(--text-sm);
  border-radius: var(--radius-md);
  transition: top var(--transition-fast);
}

.skip-link:focus {
  top: var(--space-4);
  outline: 2px solid var(--color-text-inverse);
  outline-offset: 2px;
}

/* Глобальный стилизованный focus-visible */
:focus {
  outline: none; /* убираем браузерный по умолчанию */
}

:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* Более мягкий фокус внутри тёмных элементов */
.btn:focus-visible,
.nav-link:focus-visible,
.tabs__trigger:focus-visible {
  outline-offset: 3px;
}

/* Улучшение фокуса для checkbox */
.checkbox input:focus-visible + .checkbox__control {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* Модальные окна — ловушка фокуса будет через JS, стили уже есть */

/* Улучшение контраста для текста на subtle-фонах */
.badge--success,
.badge--warning,
.badge--danger {
  /* уже используем color-mix + достаточно контрастные цвета */
}

/* Скрытие декоративных элементов от скринридеров */
.scroll-progress,
.theme-toggle__icon {
  aria-hidden: true; /* через атрибут в HTML */
}

/* Уменьшение движения уже обрабатывается в 06-animations.css */
```

#### 3. Обновления в `index.html`

Добавляем skip-link и улучшаем семантику:

```html
<body>
  <!-- Skip link -->
  <a href="#main-content" class="skip-link">Перейти к основному содержимому</a>

  <div class="app">
    <!-- Sidebar -->
    <aside class="sidebar" aria-label="Основная навигация">
      <div class="sidebar__brand">Nova</div>
      <nav class="sidebar__nav" aria-label="Разделы">
        <a href="#" class="nav-link is-active" aria-current="page">Dashboard</a>
        <a href="#" class="nav-link">Projects</a>
        <a href="#" class="nav-link">Analytics</a>
        <a href="#" class="nav-link">Settings</a>
      </nav>
    </aside>

    <div class="main">
      <header class="header">
        <h1 class="header__title">Dashboard</h1>
        <!-- actions -->
      </header>

      <main class="content" id="main-content">
        <!-- весь существующий контент -->
      </main>
    </div>
  </div>

  <!-- Modal и Toast container остаются -->
</body>
```

Также рекомендуется проверить, что у всех интерактивных элементов есть понятные accessible names (через текст кнопки, `aria-label` или `aria-labelledby`).

#### 4. Улучшения в компонентах

**`css/components/_buttons.css`** — уже имеет хороший `:focus-visible`. Можно усилить:

```css
.btn:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 3px;
}
```

**`css/components/_navigation.css`** (nav-link):

```css
.nav-link:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.nav-link[aria-current="page"] {
  /* стили активного состояния уже есть через .is-active */
}
```

**`css/components/_tabs.css`** и **`_accordion.css`** — уже используют правильные ARIA-атрибуты (`aria-selected`, `aria-expanded`, `aria-controls`, `role="tablist"` и т.д.). В этой главе мы только убеждаемся, что они корректно обновляются из JavaScript.

#### 5. `js/main.js` — небольшие улучшения доступности

```js
/* ===== Улучшение Tabs (клавиатура) ===== */
document.querySelectorAll("[data-tabs]").forEach(tabs => {
  const triggers = [...tabs.querySelectorAll(".tabs__trigger")];
  const panels = tabs.querySelectorAll(".tabs__panel");

  triggers.forEach((trigger, index) => {
    trigger.addEventListener("click", () => activateTab(tabs, index));

    trigger.addEventListener("keydown", (e) => {
      let newIndex = null;

      if (e.key === "ArrowRight" || e.key === "ArrowDown") {
        newIndex = (index + 1) % triggers.length;
      } else if (e.key === "ArrowLeft" || e.key === "ArrowUp") {
        newIndex = (index - 1 + triggers.length) % triggers.length;
      } else if (e.key === "Home") {
        newIndex = 0;
      } else if (e.key === "End") {
        newIndex = triggers.length - 1;
      }

      if (newIndex !== null) {
        e.preventDefault();
        triggers[newIndex].focus();
        activateTab(tabs, newIndex);
      }
    });
  });
});

function activateTab(tabs, index) {
  const triggers = tabs.querySelectorAll(".tabs__trigger");
  const panels = tabs.querySelectorAll(".tabs__panel");

  triggers.forEach((t, i) => {
    const selected = i === index;
    t.setAttribute("aria-selected", selected);
    t.tabIndex = selected ? 0 : -1;
  });

  panels.forEach((p, i) => {
    const active = i === index;
    p.classList.toggle("is-active", active);
    p.hidden = !active;
  });
}

/* ===== Accordion — можно добавить поддержку Home/End при желании ===== */

/* Остальной код (theme, dropdown, toast) сохраняется */
```

#### 6. Остальные файлы

Все предыдущие файлы (`01-tokens.css`, компоненты, анимации, performance и т.д.) остаются без изменений по основной логике.  
Мы только добавляем слой `a11y` и точечные улучшения.

---

### Что мы получили в этой главе

- Skip-link для быстрой навигации с клавиатуры.
- Единый и заметный стиль `:focus-visible`.
- Улучшенную клавиатурную навигацию для Tabs.
- Более корректную семантику и ARIA.
- Повышение общей доступности Design System.
- Проект, который гораздо лучше соответствует современным стандартам a11y.

В следующей главе мы займёмся финальной сборкой, проверкой всех состояний компонентов, мелкими UX-улучшениями и подготовкой проекта к завершению практического руководства.