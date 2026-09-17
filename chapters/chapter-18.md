**Глава 18. Fluid-типографика и современный адаптивный ритм**

### Цель главы

До этого момента размеры шрифтов и отступы у нас были в основном фиксированными (через токены).  
В современных интерфейсах всё чаще используют **fluid**-подход: типографика и отступы плавно масштабируются вместе с размером экрана.

В этой главе мы:

- Внедрим fluid-типографику с помощью `clamp()`.
- Сделаем часть spacing-токенов адаптивными.
- Улучшим визуальный ритм интерфейса.
- Покажем, как сочетать fluid-значения с Design Tokens.
- Сохраним возможность плотного (dense) режима.

К концу главы типографика и вертикальный ритм Nova станут более современными и естественными на любых экранах.

---

### Ключевые идеи

- `clamp(min, preferred, max)` — основа fluid-типографики.
- Предпочтительное значение обычно строится на `vw` + относительных единицах.
- Токены остаются единым источником правды — мы просто делаем некоторые из них fluid.
- Не всё нужно делать fluid (кнопки, бейджи и мелкие элементы лучше оставлять стабильными).

---

### Структура проекта на данном этапе

```
nova/
├── index.html
├── style-guide.html
├── css/
│   ├── 00-reset.css
│   ├── 01-tokens.css              ← обновлены типографика и часть spacing
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
│   └── main.js
└── assets/
```

Структура файлов не меняется — мы улучшаем содержимое токенов.

---

### Содержание файлов

#### 1. `css/01-tokens.css` — обновлённый блок типографики и spacing

Замените соответствующие части файла на следующие:

```css
/* ========================================
   Typography — Fluid
   ======================================== */

:root {
  /* Базовый размер */
  --text-xs:   0.75rem;
  --text-sm:   0.875rem;
  --text-base: 1rem;

  /* Fluid-заголовки */
  --text-lg:   clamp(1.05rem, 0.95rem + 0.35vw, 1.15rem);
  --text-xl:   clamp(1.15rem, 1.02rem + 0.5vw, 1.35rem);
  --text-2xl:  clamp(1.35rem, 1.15rem + 0.7vw, 1.65rem);
  --text-3xl:  clamp(1.6rem, 1.3rem + 1vw, 2.1rem);
  --text-4xl:  clamp(1.9rem, 1.5rem + 1.4vw, 2.6rem);

  /* Межстрочный интервал */
  --leading-tight: 1.25;
  --leading-snug:  1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.65;
}

/* ========================================
   Spacing — частично fluid
   ======================================== */

:root {
  --space-1:  0.25rem;
  --space-2:  0.5rem;
  --space-3:  0.75rem;
  --space-4:  1rem;
  --space-5:  1.25rem;
  --space-6:  1.5rem;

  /* Более крупные отступы делаем fluid */
  --space-8:  clamp(1.75rem, 1.5rem + 0.8vw, 2.25rem);
  --space-10: clamp(2.25rem, 1.9rem + 1.1vw, 3rem);
  --space-12: clamp(2.75rem, 2.3rem + 1.4vw, 3.75rem);
  --space-16: clamp(3.5rem, 2.8rem + 2vw, 5rem);
  --space-20: clamp(4.5rem, 3.5rem + 2.5vw, 6.5rem);
}

/* ========================================
   Dense mode — переопределяем fluid-значения
   ======================================== */

[data-density="dense"] {
  --text-lg:   1.05rem;
  --text-xl:   1.15rem;
  --text-2xl:  1.3rem;
  --text-3xl:  1.5rem;
  --text-4xl:  1.8rem;

  --space-8:  1.5rem;
  --space-10: 1.75rem;
  --space-12: 2.25rem;
  --space-16: 3rem;
  --space-20: 4rem;

  --header-height: 52px;
  --sidebar-width: 220px;
}
```

> Остальная часть файла (палитры, семантические цвета, темы light/dark/ocean/high-contrast) остаётся без изменений.

#### 2. `css/02-base.css` — небольшие улучшения

```css
body {
  font-family: var(--font-sans);
  font-size: var(--text-base);
  line-height: var(--leading-normal);
  color: var(--color-text);
  background-color: var(--color-bg);
  transition: background-color var(--transition-base), color var(--transition-base);
}

h1, h2, h3, h4 {
  font-weight: 600;
  line-height: var(--leading-tight);
  color: var(--color-text);
}

h1 {
  font-size: var(--text-3xl);
}

h2 {
  font-size: var(--text-2xl);
}

h3 {
  font-size: var(--text-xl);
}

p {
  line-height: var(--leading-relaxed);
}
```

#### 3. Обновления в компонентах (точечные)

В большинстве компонентов ничего менять не нужно — они уже используют токены.  
Благодаря тому, что `--text-*` и крупные `--space-*` стали fluid, заголовки и секции автоматически начинают лучше адаптироваться.

Рекомендуется проверить:

- `.section__title` — теперь будет использовать fluid `--text-2xl`
- `.card__title` — получит чуть более живой размер
- Отступы между секциями (`margin-bottom: var(--space-12)` и т.д.) станут более плавными

#### 4. `style-guide.html`

Можно добавить небольшой блок, демонстрирующий fluid-типографику:

```html
<section class="sg-section" id="typography">
  <h2 class="sg-title">Типографика (Fluid)</h2>
  <p class="sg-description">
    Размеры заголовков плавно масштабируются с помощью clamp(). 
    Измените ширину окна, чтобы увидеть эффект.
  </p>

  <div style="display: grid; gap: var(--space-4);">
    <div>
      <div class="sg-label">text-4xl</div>
      <div style="font-size: var(--text-4xl); font-weight: 700;">Заголовок 4XL</div>
    </div>
    <div>
      <div class="sg-label">text-3xl</div>
      <div style="font-size: var(--text-3xl); font-weight: 700;">Заголовок 3XL</div>
    </div>
    <div>
      <div class="sg-label">text-2xl</div>
      <div style="font-size: var(--text-2xl); font-weight: 600;">Заголовок 2XL</div>
    </div>
    <div>
      <div class="sg-label">text-xl</div>
      <div style="font-size: var(--text-xl); font-weight: 600;">Заголовок XL</div>
    </div>
  </div>
</section>
```

#### 5. Остальные файлы

- `main.css`, все компоненты, анимации, performance, a11y, JavaScript — без изменений.
- Темы (light / dark / ocean / high-contrast) продолжают работать как раньше.
- Dense-режим корректно переопределяет fluid-значения на фиксированные.

---

### Что мы получили в этой главе

- Современную **fluid-типографику** на основе `clamp()`.
- Частично fluid-систему отступов.
- Лучший визуальный ритм на разных размерах экрана.
- Сохранённый контроль через dense-режим.
- Ещё один шаг к по-настоящему современной Design System 2026 года.

---

### Рекомендация

Fluid-типографику лучше применять к заголовкам и крупным отступам.  
Для мелкого текста, кнопок, инпутов и бейджей обычно предпочтительнее стабильные значения — так интерфейс остаётся предсказуемым и лёгким в сопровождении.