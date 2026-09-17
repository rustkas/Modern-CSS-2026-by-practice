**Глава 11. Расширение Design System: Tabs, Accordion и Toast**

### Цель главы

В предыдущей главе мы привели проект к структуре настоящей Design System.  
Теперь расширяем библиотеку компонентов важными паттернами, которые встречаются почти в каждом современном интерфейсе.

В этой главе мы:

- Создадим компонент **Tabs**.
- Создадим компонент **Accordion**.
- Добавим систему **Toast** (уведомления).
- Улучшим доступность и клавиатурную навигацию.
- Продолжим следовать установленным контрактам компонентов.

К концу главы UI-библиотека Nova станет заметно богаче и ближе к реальной дизайн-системе.

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
│   │   ├── _tabs.css          ← новый
│   │   ├── _accordion.css     ← новый
│   │   └── _toast.css         ← новый
│   ├── 04-components.css
│   ├── 05-utilities.css
│   ├── 06-animations.css
│   └── main.css
├── js/
│   └── main.js                ← расширен
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
```

#### 2. `css/components/_tabs.css`

```css
/* ========================================
   Tabs
   ======================================== */

.tabs {
  display: flex;
  flex-direction: column;
  gap: var(--space-4);
}

.tabs__list {
  display: flex;
  gap: var(--space-1);
  border-bottom: 1px solid var(--color-border);
  padding-bottom: 0;
}

.tabs__trigger {
  position: relative;
  padding: var(--space-3) var(--space-4);
  font-size: var(--text-sm);
  font-weight: 500;
  color: var(--color-text-muted);
  background: transparent;
  border: none;
  border-radius: var(--radius-md) var(--radius-md) 0 0;
  cursor: pointer;
  transition: color var(--transition-fast), background-color var(--transition-fast);

  &:hover {
    color: var(--color-text);
    background-color: var(--color-surface-2);
  }

  &[aria-selected="true"] {
    color: var(--color-primary);
  }

  &[aria-selected="true"]::after {
    content: "";
    position: absolute;
    left: 0;
    right: 0;
    bottom: -1px;
    height: 2px;
    background-color: var(--color-primary);
    border-radius: var(--radius-full);
  }

  &:focus-visible {
    outline: 2px solid var(--color-primary);
    outline-offset: 2px;
  }
}

.tabs__panel {
  display: none;
  animation: fade-in 0.2s ease;
}

.tabs__panel.is-active {
  display: block;
}

@keyframes fade-in {
  from { opacity: 0; transform: translateY(4px); }
  to { opacity: 1; transform: translateY(0); }
}
```

#### 3. `css/components/_accordion.css`

```css
/* ========================================
   Accordion
   ======================================== */

.accordion {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-xl);
  overflow: hidden;
  background-color: var(--color-surface);
}

.accordion__item {
  border-bottom: 1px solid var(--color-border);

  &:last-child {
    border-bottom: none;
  }
}

.accordion__trigger {
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-4);
  padding: var(--space-4) var(--space-5);
  font-size: var(--text-sm);
  font-weight: 500;
  color: var(--color-text);
  background: transparent;
  border: none;
  cursor: pointer;
  text-align: left;
  transition: background-color var(--transition-fast);

  &:hover {
    background-color: var(--color-surface-2);
  }

  &:focus-visible {
    outline: 2px solid var(--color-primary);
    outline-offset: -2px;
  }

  &::after {
    content: "";
    width: 0.5rem;
    height: 0.5rem;
    border-right: 2px solid var(--color-text-muted);
    border-bottom: 2px solid var(--color-text-muted);
    transform: rotate(45deg);
    transition: transform var(--transition-fast);
    flex-shrink: 0;
  }

  &[aria-expanded="true"]::after {
    transform: rotate(-135deg);
  }
}

.accordion__panel {
  display: grid;
  grid-template-rows: 0fr;
  transition: grid-template-rows 0.25s cubic-bezier(0.22, 1, 0.36, 1);
}

.accordion__panel.is-open {
  grid-template-rows: 1fr;
}

.accordion__content {
  overflow: hidden;
  padding: 0 var(--space-5);
}

.accordion__panel.is-open .accordion__content {
  padding-bottom: var(--space-4);
}
```

#### 4. `css/components/_toast.css`

```css
/* ========================================
   Toast / Notifications
   ======================================== */

.toast-container {
  position: fixed;
  bottom: var(--space-6);
  right: var(--space-6);
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
  z-index: 1000;
  pointer-events: none;
}

.toast {
  pointer-events: auto;
  min-width: 280px;
  max-width: 360px;
  padding: var(--space-4);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-lg);
  display: flex;
  align-items: flex-start;
  gap: var(--space-3);
  animation: toast-in 0.3s cubic-bezier(0.22, 1, 0.36, 1);
}

.toast--success {
  border-left: 4px solid var(--color-success);
}

.toast--danger {
  border-left: 4px solid var(--color-danger);
}

.toast--warning {
  border-left: 4px solid var(--color-warning);
}

.toast__content {
  flex: 1;
  font-size: var(--text-sm);
  line-height: 1.4;
}

.toast__title {
  font-weight: 600;
  margin-bottom: var(--space-1);
}

.toast__close {
  flex-shrink: 0;
  width: 1.5rem;
  height: 1.5rem;
  display: grid;
  place-items: center;
  border-radius: var(--radius-sm);
  color: var(--color-text-muted);
  font-size: 1rem;
  line-height: 1;
  transition: background-color var(--transition-fast);

  &:hover {
    background-color: var(--color-surface-2);
    color: var(--color-text);
  }
}

@keyframes toast-in {
  from {
    opacity: 0;
    transform: translateY(1rem) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.toast.is-leaving {
  animation: toast-out 0.25s ease forwards;
}

@keyframes toast-out {
  to {
    opacity: 0;
    transform: translateY(0.5rem) scale(0.95);
  }
}
```

#### 5. `index.html` (ключевые новые части)

Добавляем демонстрацию новых компонентов:

```html
<!-- ... существующий код header и sidebar ... -->

<main class="content">
  <!-- ... stats и projects ... -->

  <!-- Tabs -->
  <section class="section">
    <h2 class="section__title">Вкладки</h2>

    <div class="tabs" data-tabs>
      <div class="tabs__list" role="tablist">
        <button class="tabs__trigger" role="tab" aria-selected="true" aria-controls="tab-1" id="tab-btn-1">Обзор</button>
        <button class="tabs__trigger" role="tab" aria-selected="false" aria-controls="tab-2" id="tab-btn-2">Активность</button>
        <button class="tabs__trigger" role="tab" aria-selected="false" aria-controls="tab-3" id="tab-btn-3">Настройки</button>
      </div>

      <div class="tabs__panel is-active" role="tabpanel" id="tab-1" aria-labelledby="tab-btn-1">
        <p>Содержимое вкладки «Обзор». Здесь может быть любая информация о проекте.</p>
      </div>
      <div class="tabs__panel" role="tabpanel" id="tab-2" aria-labelledby="tab-btn-2" hidden>
        <p>Лента активности появится здесь.</p>
      </div>
      <div class="tabs__panel" role="tabpanel" id="tab-3" aria-labelledby="tab-btn-3" hidden>
        <p>Настройки проекта.</p>
      </div>
    </div>
  </section>

  <!-- Accordion -->
  <section class="section">
    <h2 class="section__title">Часто задаваемые вопросы</h2>

    <div class="accordion" data-accordion>
      <div class="accordion__item">
        <button class="accordion__trigger" aria-expanded="false" aria-controls="acc-1">
          Как создать новый проект?
        </button>
        <div class="accordion__panel" id="acc-1">
          <div class="accordion__content">
            Нажмите кнопку «Новый проект» в шапке или используйте быстрые действия.
          </div>
        </div>
      </div>

      <div class="accordion__item">
        <button class="accordion__trigger" aria-expanded="false" aria-controls="acc-2">
          Можно ли изменить тему?
        </button>
        <div class="accordion__panel" id="acc-2">
          <div class="accordion__content">
            Да, используйте переключатель темы в правом верхнем углу. Поддерживается светлая, тёмная и системная тема.
          </div>
        </div>
      </div>

      <div class="accordion__item">
        <button class="accordion__trigger" aria-expanded="false" aria-controls="acc-3">
          Как работают уведомления?
        </button>
        <div class="accordion__panel" id="acc-3">
          <div class="accordion__content">
            Система Toast показывает временные сообщения о успешных действиях и ошибках.
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Кнопка для демонстрации Toast -->
  <section class="section">
    <button class="btn" id="show-toast-btn">Показать уведомление</button>
  </section>
</main>

<!-- Контейнер для Toast -->
<div class="toast-container" id="toast-container"></div>
```

#### 6. `js/main.js` (расширенная версия)

```js
/* ===== Theme (из предыдущих глав) ===== */
const html = document.documentElement;
const themeToggle = document.querySelector(".theme-toggle");
const themeIcon = document.querySelector("[data-theme-icon]");

function getPreferredTheme() {
  return localStorage.getItem("nova-theme") ||
    (window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light");
}

function applyTheme(theme) {
  html.setAttribute("data-theme", theme);
  localStorage.setItem("nova-theme", theme);
  themeIcon.textContent = theme === "dark" ? "☀️" : "🌙";
}

function setTheme(theme) {
  if (document.startViewTransition) {
    document.startViewTransition(() => applyTheme(theme));
  } else {
    applyTheme(theme);
  }
}

applyTheme(getPreferredTheme());

themeToggle?.addEventListener("click", () => {
  const next = html.getAttribute("data-theme") === "dark" ? "light" : "dark";
  setTheme(next);
});

/* ===== Dropdown ===== */
const dropdown = document.querySelector(".dropdown");
const dropdownBtn = document.querySelector("#user-menu-btn");

dropdownBtn?.addEventListener("click", (e) => {
  e.stopPropagation();
  const isOpen = dropdown.classList.toggle("is-open");
  dropdownBtn.setAttribute("aria-expanded", isOpen);
});

document.addEventListener("click", () => {
  dropdown?.classList.remove("is-open");
  dropdownBtn?.setAttribute("aria-expanded", "false");
});

/* ===== Tabs ===== */
document.querySelectorAll("[data-tabs]").forEach(tabs => {
  const triggers = tabs.querySelectorAll(".tabs__trigger");
  const panels = tabs.querySelectorAll(".tabs__panel");

  triggers.forEach(trigger => {
    trigger.addEventListener("click", () => {
      const targetId = trigger.getAttribute("aria-controls");

      triggers.forEach(t => t.setAttribute("aria-selected", "false"));
      panels.forEach(p => {
        p.classList.remove("is-active");
        p.hidden = true;
      });

      trigger.setAttribute("aria-selected", "true");
      const panel = tabs.querySelector(`#${targetId}`);
      panel.classList.add("is-active");
      panel.hidden = false;
    });
  });
});

/* ===== Accordion ===== */
document.querySelectorAll("[data-accordion]").forEach(accordion => {
  accordion.querySelectorAll(".accordion__trigger").forEach(trigger => {
    trigger.addEventListener("click", () => {
      const panel = document.getElementById(trigger.getAttribute("aria-controls"));
      const isOpen = trigger.getAttribute("aria-expanded") === "true";

      trigger.setAttribute("aria-expanded", !isOpen);
      panel.classList.toggle("is-open", !isOpen);
    });
  });
});

/* ===== Toast ===== */
const toastContainer = document.getElementById("toast-container");

function showToast({ title, message, type = "success", duration = 4000 }) {
  const toast = document.createElement("div");
  toast.className = `toast toast--${type}`;
  toast.innerHTML = `
    <div class="toast__content">
      <div class="toast__title">${title}</div>
      <div>${message}</div>
    </div>
    <button class="toast__close" aria-label="Закрыть">×</button>
  `;

  toastContainer.appendChild(toast);

  const close = () => {
    toast.classList.add("is-leaving");
    toast.addEventListener("animationend", () => toast.remove());
  };

  toast.querySelector(".toast__close").addEventListener("click", close);
  setTimeout(close, duration);
}

document.getElementById("show-toast-btn")?.addEventListener("click", () => {
  showToast({
    title: "Успешно",
    message: "Проект был успешно создан",
    type: "success"
  });
});
```

#### 7. Остальные файлы

- `01-tokens.css`, `00-reset.css`, `02-base.css`, `03-layout.css`, `06-animations.css` — без изменений.
- Компоненты `_buttons.css`, `_cards.css`, `_forms.css`, `_overlays.css` и др. — сохраняются из Главы 10.

---

### Что мы получили в этой главе

- Полноценные компоненты **Tabs**, **Accordion** и **Toast**.
- Хорошую базовую доступность (ARIA-атрибуты, клавиатура).
- Анимации открытия/закрытия на чистом CSS.
- Ещё один шаг к зрелой Design System.
- Переиспользуемую систему уведомлений.

В следующей главе мы сосредоточимся на производительности, оптимизации CSS и финальной полировке проекта перед завершением.