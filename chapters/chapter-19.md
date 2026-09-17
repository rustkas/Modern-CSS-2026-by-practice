**Глава 19. Логические свойства и подготовка к интернационализации (RTL)**

### Цель главы

Большинство современных интерфейсов должны корректно работать не только на LTR-языках (русский, английский), но и на RTL (арабский, иврит).  
Вместо физических свойств (`margin-left`, `padding-right`, `border-left` и т.д.) в современном CSS рекомендуется использовать **логические свойства**.

В этой главе мы:

- Переведём ключевые стили на логические свойства.
- Сделаем интерфейс готовым к RTL.
- Покажем, как тестировать направление текста.
- Сохраним полную совместимость с текущей Design System.

К концу главы Nova будет значительно лучше подготовлена к интернационализации.

---

### Основные логические свойства, которые мы используем

| Физическое свойство     | Логическое свойство              |
|-------------------------|----------------------------------|
| `margin-left/right`     | `margin-inline-start/end`        |
| `padding-left/right`    | `padding-inline-start/end`       |
| `border-left/right`     | `border-inline-start/end`        |
| `left / right`          | `inset-inline-start/end`         |
| `text-align: left`      | `text-align: start`              |
| `float: left`           | `float: inline-start`            |
| `width` (в некоторых случаях) | `inline-size`               |
| `height`                | `block-size`                     |

---

### Структура проекта на данном этапе

```
nova/
├── index.html
├── style-guide.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css
│   ├── 02-base.css
│   ├── 03-layout.css              ← обновлён
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
│   ├── 05-utilities.css           ← добавлены logical-утилиты
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

#### 1. `css/03-layout.css` (ключевые изменения)

```css
.app {
  display: grid;
  grid-template-columns: var(--sidebar-width) 1fr;
  min-height: 100dvh;
}

.sidebar {
  background-color: var(--color-surface);
  border-inline-end: 1px solid var(--color-border); /* вместо border-right */
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
  margin-block-end: var(--space-8);
}

.sidebar__nav {
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
}

.main {
  display: grid;
  grid-template-rows: var(--header-height) 1fr;
  min-width: 0;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-inline: var(--space-8);           /* вместо padding-left/right */
  background-color: var(--color-surface);
  border-block-end: 1px solid var(--color-border);
  position: sticky;
  top: 0;
  z-index: 10;
}

.content {
  padding: var(--space-8);
  overflow-x: hidden;
  padding-block-end: var(--space-20);
}

/* Остальные grid-стили без изменений */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: var(--space-6);
}

.content-grid {
  display: grid;
  grid-template-columns: 1.6fr 1fr;
  gap: var(--space-10);
  align-items: start;
}

.cards-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
  gap: var(--space-6);
}
```

#### 2. Обновления в компонентах (примеры)

**`_navigation.css`**

```css
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
```

**`_forms.css`** (пример)

```css
.form-group {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
}

.label {
  font-size: var(--text-sm);
  font-weight: 500;
}

.input,
.textarea {
  width: 100%;
  padding: var(--space-3) var(--space-4);
  /* padding уже симметричный, логические свойства не обязательны */
  border: 1px solid var(--color-border);
  border-radius: var(--radius-md);
}
```

**`_overlays.css`** (Toast и Dropdown)

```css
.toast-container {
  position: fixed;
  inset-block-end: var(--space-6);     /* вместо bottom */
  inset-inline-end: var(--space-6);    /* вместо right */
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
  z-index: 1000;
  pointer-events: none;
}

.dropdown__menu {
  position: absolute;
  position-anchor: --dropdown-trigger;
  position-area: block-end span-inline-end;
  /* ... */
}
```

#### 3. `css/05-utilities.css` — добавление logical-утилит

```css
/* Logical spacing utilities */
.u-ms-auto  { margin-inline-start: auto; }
.u-me-auto  { margin-inline-end: auto; }
.u-ps-4     { padding-inline-start: var(--space-4); }
.u-pe-4     { padding-inline-end: var(--space-4); }

.u-text-start  { text-align: start; }
.u-text-end    { text-align: end; }
.u-text-center { text-align: center; }

/* Inline / Block helpers */
.u-inline-size-full { inline-size: 100%; }
.u-block-size-full  { block-size: 100%; }
```

#### 4. Как тестировать RTL

Добавьте временно в `<html>`:

```html
<html lang="ar" dir="rtl" data-theme="system">
```

Или переключайте через DevTools / небольшую кнопку:

```js
function toggleDirection() {
  const html = document.documentElement;
  const current = html.getAttribute("dir") || "ltr";
  html.setAttribute("dir", current === "ltr" ? "rtl" : "ltr");
}
```

При правильном использовании логических свойств sidebar, отступы, тосты и выравнивание должны корректно «переворачиваться».

#### 5. Остальные файлы

- `01-tokens.css` — без изменений (fluid-типографика и темы остаются).
- Все компоненты в целом сохраняют свою логику, мы только заменяем физические свойства на логические там, где это важно.
- JavaScript, анимации, performance и a11y — без изменений.

---

### Что мы получили в этой главе

- Переход на **логические свойства**.
- Готовность интерфейса к RTL-языкам.
- Более профессиональный и forward-compatible CSS.
- Дополнительные utility-классы для logical-направления.
- Понимание, как проектировать Design System с учётом интернационализации.

---

### Рекомендация

Начинайте использовать логические свойства по умолчанию во всех новых компонентах.  
Физические свойства (`left/right`, `margin-left` и т.д.) оставляйте только тогда, когда направление действительно должно быть привязано к экрану, а не к направлению текста.