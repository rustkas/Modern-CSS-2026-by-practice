**Глава 16. Продвинутые паттерны и подготовка к продакшену**

### Цель главы

Глава 15 подвела итоги основного пути.  
Эта глава — следующий уровень. Мы добавляем несколько важных продакшен-паттернов и доводим проект до состояния, когда его уже можно использовать как основу реальной Design System.

В этой главе мы:

- Добавим компоненты **Skeleton** и **Pagination**.
- Улучшим организацию токенов и добавим densе-режим (компактная тема).
- Введём простые CSS-утилиты для состояний загрузки.
- Подготовим проект к возможному подключению сборщика (Lightning CSS / PostCSS).
- Сделаем финальные штрихи перед использованием в реальных задачах.

---

### Структура проекта на данном этапе

```
nova/
├── index.html
├── style-guide.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css              ← добавлен densе-режим
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
│   │   ├── _toast.css
│   │   ├── _skeleton.css          ← новый
│   │   └── _pagination.css        ← новый
│   ├── 04-components.css
│   ├── 05-utilities.css
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

#### 1. `css/04-components.css`

```css
@import url("components/_buttons.css");
@import url("components/_badges.css");
@import url("components/_cards.css");
@import url("components/_forms.css");
@import url("components/_navigation.css");
@import url("components/_overlays.css");
@import url("components/_tabs.css");
@import url("components/_accordion.css");
@import url("components/_toast.css");
@import url("components/_skeleton.css");
@import url("components/_pagination.css");
```

#### 2. `css/components/_skeleton.css`

```css
/* ========================================
   Skeleton (состояние загрузки)
   ======================================== */

.skeleton {
  --skeleton-bg: color-mix(in oklch, var(--color-surface-2) 70%, var(--color-border));
  --skeleton-highlight: color-mix(in oklch, var(--color-surface) 60%, transparent);

  background: linear-gradient(
    90deg,
    var(--skeleton-bg) 25%,
    var(--skeleton-highlight) 50%,
    var(--skeleton-bg) 75%
  );
  background-size: 200% 100%;
  animation: skeleton-loading 1.4s ease-in-out infinite;
  border-radius: var(--radius-md);
  color: transparent !important;
  pointer-events: none;
  user-select: none;
}

.skeleton--text {
  height: 0.9em;
  margin-bottom: 0.4em;
  border-radius: var(--radius-sm);
}

.skeleton--title {
  height: 1.3em;
  width: 60%;
  margin-bottom: 0.6em;
}

.skeleton--avatar {
  width: 2.5rem;
  height: 2.5rem;
  border-radius: var(--radius-full);
}

.skeleton--card {
  height: 180px;
  border-radius: var(--radius-xl);
}

@keyframes skeleton-loading {
  0% {
    background-position: 200% 0;
  }
  100% {
    background-position: -200% 0;
  }
}

@media (prefers-reduced-motion: reduce) {
  .skeleton {
    animation: none;
    background: var(--skeleton-bg);
  }
}
```

#### 3. `css/components/_pagination.css`

```css
/* ========================================
   Pagination
   ======================================== */

.pagination {
  display: flex;
  align-items: center;
  gap: var(--space-1);
  flex-wrap: wrap;
}

.pagination__item {
  min-width: 2.25rem;
  height: 2.25rem;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding-inline: var(--space-2);
  font-size: var(--text-sm);
  font-weight: 500;
  color: var(--color-text);
  background-color: transparent;
  border: 1px solid transparent;
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: background-color var(--transition-fast), border-color var(--transition-fast), color var(--transition-fast);
}

.pagination__item:hover:not(:disabled):not(.is-active) {
  background-color: var(--color-surface-2);
}

.pagination__item.is-active {
  background-color: var(--color-primary);
  color: var(--color-text-inverse);
  border-color: var(--color-primary);
}

.pagination__item:disabled {
  opacity: 0.45;
  cursor: not-allowed;
}

.pagination__item:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.pagination__ellipsis {
  min-width: 2.25rem;
  height: 2.25rem;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  color: var(--color-text-muted);
  font-size: var(--text-sm);
  pointer-events: none;
}
```

#### 4. Обновление `css/01-tokens.css` — добавление densе-режима

Добавьте в конец файла:

```css
/* ========================================
   Dense mode (компактный интерфейс)
   ======================================== */

[data-density="dense"] {
  --space-1:  0.2rem;
  --space-2:  0.4rem;
  --space-3:  0.6rem;
  --space-4:  0.8rem;
  --space-5:  1rem;
  --space-6:  1.2rem;
  --space-8:  1.6rem;

  --text-sm:  0.8125rem;
  --text-base: 0.9375rem;

  --header-height: 52px;
  --sidebar-width: 220px;
}
```

#### 5. Пример использования в `index.html` / `style-guide.html`

**Skeleton:**

```html
<div class="cards-grid">
  <div class="card">
    <div class="skeleton skeleton--title"></div>
    <div class="skeleton skeleton--text"></div>
    <div class="skeleton skeleton--text" style="width: 80%"></div>
    <div class="skeleton skeleton--text" style="width: 60%"></div>
  </div>
  <!-- можно повторить несколько раз -->
</div>
```

**Pagination:**

```html
<nav class="pagination" aria-label="Пагинация">
  <button class="pagination__item" disabled aria-label="Предыдущая страница">←</button>
  <button class="pagination__item is-active" aria-current="page">1</button>
  <button class="pagination__item">2</button>
  <button class="pagination__item">3</button>
  <span class="pagination__ellipsis">…</span>
  <button class="pagination__item">8</button>
  <button class="pagination__item" aria-label="Следующая страница">→</button>
</nav>
```

#### 6. `js/main.js`

Можно добавить переключатель плотности (по желанию):

```js
// Пример переключения densе-режима
function toggleDensity() {
  const current = document.documentElement.getAttribute("data-density");
  document.documentElement.setAttribute(
    "data-density",
    current === "dense" ? "" : "dense"
  );
}
```

#### 7. Остальные файлы

Все предыдущие файлы (`main.css`, токены, компоненты, анимации, performance, a11y и т.д.) сохраняются в финальном состоянии из Глав 14–15.  
Мы только расширяем систему новыми компонентами и возможностью densе-режима.

---

### Что мы получили в этой главе

- Компонент **Skeleton** для состояний загрузки.
- Компонент **Pagination**.
- Поддержку **dense-режима** через токены.
- Более готовую к реальному использованию Design System.
- Понимание, как аккуратно наращивать систему после основного каркаса.

---

### Рекомендация на будущее

На этом этапе проект Nova уже можно:

- Использовать как основу внутренней Design System.
- Подключить к React / Vue / Svelte / Web Components.
- Добавить Storybook или собственную документацию.
- Настроить Lightning CSS + Stylelint.
- Вынести токены в отдельный пакет.

Вы прошли полный цикл: от архитектуры → к полноценной, расширяемой системе на современном CSS.