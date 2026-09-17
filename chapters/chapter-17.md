**Глава 17. Продвинутая темизация: несколько тем и high-contrast**

### Цель главы

До этого момента у нас были светлая и тёмная темы.  
В реальных продуктах часто требуется больше: бренд-темы, high-contrast режим для доступности, возможность переключения между несколькими визуальными стилями.

В этой главе мы:

- Расширим систему темизации.
- Добавим **high-contrast** тему.
- Добавим дополнительную бренд-тему (например, «Ocean»).
- Сделаем удобный переключатель тем.
- Покажем, как масштабировать темизацию без хаоса.

К концу главы Design System Nova будет поддерживать несколько полноценных тем.

---

### Структура проекта на данном этапе

```
nova/
├── index.html
├── style-guide.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css              ← значительно расширен
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
│   │   ├── _skeleton.css
│   │   └── _pagination.css
│   ├── 04-components.css
│   ├── 05-utilities.css
│   ├── 06-animations.css
│   ├── 07-performance.css
│   ├── 08-a11y.css
│   └── main.css
├── js/
│   └── main.js                    ← обновлён переключатель тем
└── assets/
```

---

### Содержание файлов

#### 1. `css/01-tokens.css` (расширенная темизация)

Мы оставляем существующую палитру и семантические токены, и добавляем новые темы.

```css
/* ========================================
   Base Palette (OKLCH) — остаётся прежней
   ======================================== */

/* ... существующие --palette-* переменные ... */

/* ========================================
   Semantic tokens — Light (по умолчанию)
   ======================================== */

:root,
[data-theme="light"] {
  color-scheme: light;

  --color-bg:             var(--palette-neutral-50);
  --color-surface:        var(--palette-neutral-0);
  --color-surface-2:      var(--palette-neutral-100);
  --color-text:           var(--palette-neutral-900);
  --color-text-muted:     var(--palette-neutral-500);
  --color-text-inverse:   var(--palette-neutral-0);
  --color-border:         var(--palette-neutral-200);
  --color-border-strong:  var(--palette-neutral-300);

  --color-primary:        var(--palette-primary-500);
  --color-primary-hover:  var(--palette-primary-600);
  --color-primary-subtle: var(--palette-primary-100);

  --color-success:        var(--palette-success-500);
  --color-warning:        var(--palette-warning-500);
  --color-danger:         var(--palette-danger-500);
}

/* ========================================
   Dark theme
   ======================================== */

[data-theme="dark"] {
  color-scheme: dark;

  --color-bg:             var(--palette-neutral-950);
  --color-surface:        var(--palette-neutral-900);
  --color-surface-2:      var(--palette-neutral-800);
  --color-text:           var(--palette-neutral-50);
  --color-text-muted:     var(--palette-neutral-400);
  --color-text-inverse:   var(--palette-neutral-950);
  --color-border:         var(--palette-neutral-800);
  --color-border-strong:  var(--palette-neutral-700);

  --color-primary:        var(--palette-primary-400);
  --color-primary-hover:  var(--palette-primary-300);
  --color-primary-subtle: var(--palette-primary-900);
}

/* ========================================
   Ocean theme (пример бренд-темы)
   ======================================== */

[data-theme="ocean"] {
  color-scheme: light;

  --palette-primary-500: oklch(0.55 0.14 230);
  --palette-primary-600: oklch(0.48 0.13 230);
  --palette-primary-400: oklch(0.65 0.13 230);
  --palette-primary-100: oklch(0.94 0.04 230);
  --palette-primary-900: oklch(0.30 0.08 230);

  --color-bg:             oklch(0.97 0.01 230);
  --color-surface:        oklch(1 0 0);
  --color-surface-2:      oklch(0.95 0.015 230);
  --color-text:           oklch(0.22 0.03 230);
  --color-text-muted:     oklch(0.50 0.03 230);
  --color-text-inverse:   oklch(0.98 0.01 230);
  --color-border:         oklch(0.90 0.02 230);
  --color-border-strong:  oklch(0.82 0.03 230);

  --color-primary:        var(--palette-primary-500);
  --color-primary-hover:  var(--palette-primary-600);
  --color-primary-subtle: var(--palette-primary-100);
}

/* ========================================
   High Contrast theme (доступность)
   ======================================== */

[data-theme="high-contrast"] {
  color-scheme: light;

  --color-bg:             oklch(1 0 0);
  --color-surface:        oklch(1 0 0);
  --color-surface-2:      oklch(0.95 0 0);
  --color-text:           oklch(0 0 0);
  --color-text-muted:     oklch(0.25 0 0);
  --color-text-inverse:   oklch(1 0 0);
  --color-border:         oklch(0 0 0);
  --color-border-strong:  oklch(0 0 0);

  --color-primary:        oklch(0.35 0.18 250);
  --color-primary-hover:  oklch(0.28 0.16 250);
  --color-primary-subtle: oklch(0.90 0.05 250);

  --color-success:        oklch(0.40 0.15 145);
  --color-warning:        oklch(0.50 0.15 85);
  --color-danger:         oklch(0.40 0.20 25);

  --shadow-sm: none;
  --shadow-md: none;
  --shadow-lg: none;
}

/* ========================================
   Dense mode (из предыдущей главы)
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

#### 2. Обновление переключателя темы в HTML

В `index.html` и `style-guide.html` заменяем простую кнопку темы на более продвинутый вариант:

```html
<div class="theme-switcher">
  <label for="theme-select" class="u-sr-only">Выбор темы</label>
  <select id="theme-select" class="input" style="width: auto; min-width: 10rem;">
    <option value="system">Системная</option>
    <option value="light">Светлая</option>
    <option value="dark">Тёмная</option>
    <option value="ocean">Ocean</option>
    <option value="high-contrast">High Contrast</option>
  </select>
</div>
```

#### 3. `js/main.js` — обновлённая логика тем

```js
const html = document.documentElement;
const themeSelect = document.getElementById("theme-select");

function getPreferredTheme() {
  return localStorage.getItem("nova-theme") || "system";
}

function resolveTheme(theme) {
  if (theme === "system") {
    return window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light";
  }
  return theme;
}

function applyTheme(theme) {
  const resolved = resolveTheme(theme);
  html.setAttribute("data-theme", resolved === "light" && theme === "system" ? "light" : resolved);
  
  // Для system оставляем специальное значение
  if (theme === "system") {
    html.setAttribute("data-theme", resolveTheme("system"));
  } else {
    html.setAttribute("data-theme", theme);
  }

  localStorage.setItem("nova-theme", theme);

  if (themeSelect) {
    themeSelect.value = theme;
  }
}

function setTheme(theme) {
  if (document.startViewTransition) {
    document.startViewTransition(() => applyTheme(theme));
  } else {
    applyTheme(theme);
  }
}

// Инициализация
applyTheme(getPreferredTheme());

// Обработчик select
themeSelect?.addEventListener("change", (e) => {
  setTheme(e.target.value);
});

// Реакция на изменение системной темы
window.matchMedia("(prefers-color-scheme: dark)").addEventListener("change", () => {
  if (getPreferredTheme() === "system") {
    setTheme("system");
  }
});

/* Остальной код (dropdown, tabs, accordion, toast и т.д.) сохраняется */
```

#### 4. Небольшие стили для переключателя

Можно добавить в `05-utilities.css` или в `03-layout.css`:

```css
.theme-switcher .input {
  padding-block: var(--space-2);
  font-size: var(--text-sm);
}
```

#### 5. Остальные файлы

Все компоненты, анимации, performance и a11y-файлы остаются в предыдущем состоянии.  
Мы только расширили систему тем.

---

### Что мы получили в этой главе

- Поддержку нескольких тем: Light, Dark, Ocean, High Contrast.
- Возможность выбора «Системная» тема.
- Плавное переключение через View Transitions.
- Масштабируемый подход к темизации через `data-theme`.
- Улучшенную доступность благодаря high-contrast теме.

---

### Рекомендация

Теперь вы можете легко добавлять новые бренд-темы (например, «Forest», «Sunset», «Corporate»), просто создавая новый блок `[data-theme="..."]` и переопределяя нужные семантические токены. Это один из самых чистых и современных подходов к темизации в 2026 году.