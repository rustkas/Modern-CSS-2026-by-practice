**Глава 12. Производительность CSS: contain, content-visibility и оптимизация**

### Цель главы

К этому моменту у нас уже богатый интерфейс с анимациями, контейнерными запросами, модальными окнами и множеством компонентов.  
Теперь важно сделать так, чтобы он оставался быстрым.

В этой главе мы:

- Разберём ключевые инструменты производительности современного CSS.
- Применим `contain` и `content-visibility`.
- Научимся правильно использовать `will-change`.
- Оптимизируем анимации и длинные списки.
- Добавим практические улучшения в проект Nova.

К концу главы приложение станет заметно более производительным, особенно на страницах с большим количеством карточек и при скролле.

---

### Ключевые технологии главы

- `contain`
- `content-visibility: auto`
- `contain-intrinsic-size`
- `will-change` (с осторожностью)
- `content-visibility` + Scroll-driven Animations
- Оптимизация Rendering Pipeline (Style → Layout → Paint → Composite)

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
│   ├── 07-performance.css     ← новый файл
│   └── main.css
├── js/
│   └── main.js
└── assets/
```

---

### Содержание файлов

#### 1. `css/main.css`

```css
@layer reset, tokens, base, layout, components, utilities, animations, performance, overrides;

@import url("00-reset.css") layer(reset);
@import url("01-tokens.css") layer(tokens);
@import url("02-base.css") layer(base);
@import url("03-layout.css") layer(layout);
@import url("04-components.css") layer(components);
@import url("05-utilities.css") layer(utilities);
@import url("06-animations.css") layer(animations);
@import url("07-performance.css") layer(performance);
```

#### 2. `css/07-performance.css` (новый файл)

```css
/* ========================================
   Performance Optimizations
   ======================================== */

/* 1. Containment для карточек и секций */
.card {
  contain: layout style paint;
}

.section {
  contain: layout style;
}

.stats-grid,
.cards-grid {
  contain: layout;
}

/* 2. content-visibility для длинных списков и секций */
.activity-list,
.cards-grid .card {
  content-visibility: auto;
  contain-intrinsic-size: auto 180px; /* примерная высота карточки */
}

/* Более агрессивно для длинных списков активности */
.activity-item {
  content-visibility: auto;
  contain-intrinsic-size: auto 60px;
}

/* 3. Оптимизация анимаций — только compositor-friendly свойства */
.reveal {
  /* Используем только transform и opacity */
  will-change: transform, opacity;
}

/* Убираем will-change после завершения анимации (через JS или автоматически) */
.reveal.is-visible {
  will-change: auto;
}

/* 4. Модалки и оверлеи */
.modal__content {
  contain: layout style paint;
}

.dropdown__menu,
.tooltip {
  contain: layout style;
}

/* 5. Sidebar и Header — стабильные элементы */
.sidebar,
.header {
  contain: layout style;
}

/* 6. Оптимизация скролл-прогресса */
.scroll-progress {
  will-change: transform;
  transform: scaleX(0);
  transform-origin: left center;
}

/* 7. Уменьшение количества слоёв при необходимости */
@media (prefers-reduced-motion: reduce) {
  .reveal,
  .card,
  .btn {
    will-change: auto;
  }
}
```

#### 3. Обновления в существующих файлах

**`css/components/_cards.css`** (добавляем/уточняем)

```css
.card {
  container-type: inline-size;
  container-name: card;
  contain: layout style paint; /* дублируем для ясности */

  display: flex;
  flex-direction: column;
  gap: var(--space-4);
  padding: var(--space-6);
  background-color: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius-xl);
  box-shadow: var(--shadow-sm);
  position: relative;
  transition: transform var(--transition-base), box-shadow var(--transition-base);
}

/* ... остальные стили карточки без изменений ... */
```

**`css/06-animations.css`** — небольшое улучшение reveal:

```css
.reveal {
  opacity: 0;
  transform: translateY(30px);
  animation: reveal linear both;
  animation-timeline: view();
  animation-range: entry 0% cover 40%;
  will-change: transform, opacity;
}

@keyframes reveal {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* После появления можно сбрасывать will-change через JS при желании */
```

#### 4. `index.html`

Структура остаётся почти такой же, как в Главе 11.  
Мы только добавляем больше элементов в список активности, чтобы было видно эффект `content-visibility`:

```html
<section class="section section--tall">
  <h2 class="section__title">Активность</h2>
  <div class="activity-list">
    <div class="activity-item reveal">Обновлён статус проекта «Nova Redesign»</div>
    <div class="activity-item reveal">Добавлен новый участник в команду</div>
    <div class="activity-item reveal">Закрыто 4 задачи</div>
    <div class="activity-item reveal">Создан новый проект «API Integration»</div>
    <div class="activity-item reveal">Обновлены токены дизайн-системы</div>
    <div class="activity-item reveal">Исправлены критические ошибки</div>
    <div class="activity-item reveal">Добавлен новый компонент Tabs</div>
    <div class="activity-item reveal">Реализован Accordion</div>
    <div class="activity-item reveal">Добавлена система Toast-уведомлений</div>
    <div class="activity-item reveal">Оптимизирована производительность</div>
    <!-- Можно добавить ещё 10–20 элементов для демонстрации -->
  </div>
</section>
```

#### 5. `js/main.js`

Можно добавить небольшой скрипт, который убирает `will-change` после завершения анимации появления (хорошая практика):

```js
// ... предыдущий код ...

// Оптимизация will-change после reveal
const reveals = document.querySelectorAll(".reveal");

const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add("is-visible");
      // Через небольшое время сбрасываем will-change
      setTimeout(() => {
        entry.target.style.willChange = "auto";
      }, 600);
    }
  });
}, { threshold: 0.1 });

reveals.forEach(el => observer.observe(el));
```

> Примечание: сам `IntersectionObserver` здесь используется только для оптимизации `will-change`. Сама анимация появления по-прежнему работает через Scroll-driven Animations.

#### 6. Остальные файлы

- `01-tokens.css`, `00-reset.css`, `02-base.css`, `03-layout.css` — без изменений.
- Все компоненты (`_buttons.css`, `_forms.css`, `_tabs.css`, `_accordion.css`, `_toast.css`, `_overlays.css` и т.д.) сохраняются.
- Логика темы, dropdown, tabs, accordion и toast из Главы 11 остаётся.

---

### Что мы получили в этой главе

- Грамотное использование `contain` для ограничения области пересчёта стилей и layout.
- `content-visibility: auto` для пропуска рендеринга невидимых элементов.
- Правильную работу с `will-change` (включаем только когда нужно).
- Более производительный скролл и анимации.
- Понимание, как современные CSS-свойства помогают браузеру оптимизировать Rendering Pipeline.

В следующей главе мы займёмся финальной полировкой: доступностью, мелкими улучшениями UX, проверкой всех состояний компонентов и подготовкой проекта к завершению.