**Глава 28. Метрики и эффективность Design System**

### Цель главы

Design System существует не ради самой себя.  
Её ценность нужно уметь измерять — иначе сложно доказывать пользу, приоритизировать развитие и понимать, куда система деградирует.

В этой главе мы разберём:

- Какие метрики действительно важны.
- Как измерять adoption.
- Как понять, что система помогает, а не мешает.
- Как встроить метрики в процесс развития Nova.

---

### Структура проекта на данном этапе

Структура остаётся прежней. Мы добавляем только документ с метриками:

```
nova/
├── packages/
│   └── design-system/
│       ├── css/
│       │   ├── 00-reset.css
│       │   ├── 01-tokens.css
│       │   ├── 02-base.css
│       │   ├── 03-layout.css
│       │   ├── components/
│       │   │   ├── _buttons.css
│       │   │   ├── _badges.css
│       │   │   ├── _cards.css
│       │   │   ├── _forms.css
│       │   │   ├── _navigation.css
│       │   │   ├── _overlays.css
│       │   │   ├── _tabs.css
│       │   │   ├── _accordion.css
│       │   │   ├── _toast.css
│       │   │   ├── _skeleton.css
│       │   │   └── _pagination.css
│       │   ├── 04-components.css
│       │   ├── 05-utilities.css
│       │   ├── 06-animations.css
│       │   ├── 07-performance.css
│       │   ├── 08-a11y.css
│       │   └── main.css
│       ├── package.json
│       ├── README.md
│       └── CHANGELOG.md
│
├── apps/
│   └── dashboard/
│       ├── index.html
│       ├── style-guide.html
│       ├── js/
│       │   └── main.js
│       └── css/
│           └── app.css
│
├── docs/
│   ├── architecture.md
│   ├── tokens.md
│   ├── components.md
│   ├── theming.md
│   ├── contribution.md
│   ├── governance.md
│   └── metrics.md                 ← новый файл
│
├── package.json
├── stylelint.config.js
└── .stylelintignore
```

---

### Новый файл: `docs/metrics.md`

```md
# Метрики Nova Design System

## 1. Adoption (внедрение)

- Количество продуктов/приложений, подключивших `@nova/design-system`
- Доля экранов, использующих компоненты Design System
- Доля новых UI-задач, закрываемых существующими компонентами

## 2. Consistency (согласованность)

- Количество уникальных цветов вне токенов
- Количество «магических» значений spacing/radius
- Количество дублирующих локальных кнопок/инпутов/карточек

## 3. Velocity (скорость разработки)

- Среднее время на типовой UI-блок до и после внедрения DS
- Количество повторных UI-реализаций одного и того же паттерна
- Время онбординга нового frontend-разработчика

## 4. Quality (качество)

- Количество UI-багов, связанных со стилями
- Количество a11y-замечаний на регрессионных проверках
- Стабильность визуальных регрессий после обновления DS

## 5. Health (здоровье системы)

- Количество неиспользуемых токенов
- Количество неиспользуемых компонентов
- Средний срок жизни PR в Design System
- Частота breaking changes
```

---

### Какие метрики смотреть в первую очередь

Если упростить, на старте достаточно четырёх вопросов:

1. **Используют ли систему?**  
   (adoption)

2. **Уменьшился ли хаос в CSS?**  
   (меньше магических значений и дублей)

3. **Стало ли быстрее собирать типовые экраны?**  
   (velocity)

4. **Не растёт ли стоимость поддержки самой системы?**  
   (health)

Если adoption низкий — проблема в удобстве, документации или покрытии сценариев.  
Если adoption высокий, но health плохой — система растёт слишком хаотично.

---

### Практический способ измерения в Nova

#### 1. Adoption через зависимость

Проверяйте, какие приложения зависят от `@nova/design-system`.

#### 2. Поиск магических значений

Периодически запускайте lint и/или простой поиск:

- hex-цвета вне `01-tokens.css`
- «сырые» `px` в компонентах продуктов
- локальные `.btn`, `.card`, `.input` вне Design System

#### 3. Аудит Style Guide

Раз в спринт/месяц спрашивайте команду:

- Чего не хватает в Style Guide?
- Какие компоненты приходится обходить?
- Где приходится писать custom CSS слишком часто?

#### 4. Регресс по качеству

После каждого релиза DS проверяйте:

- Style Guide
- 2–3 ключевых экрана продукта
- Темы light/dark
- Базовый RTL и keyboard flow

---

### Содержание ключевых технических файлов

На этом этапе техническое ядро **не меняется**.  
Мы добавляем управленческий и аналитический слой.

#### `packages/design-system/css/main.css`

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

#### `packages/design-system/css/04-components.css`

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

Все компоненты, токены, анимации, performance и a11y остаются в финальном состоянии, достигнутом ранее.

---

### Мини-дашборд здоровья системы (рекомендуемый)

Раз в месяц фиксируйте в таблице:

| Метрика                         | Значение | Комментарий |
|--------------------------------|----------|-------------|
| Продукты на DS                 | 3        |             |
| Неиспользуемые токены          | 5        |             |
| UI-баги за месяц               | 2        |             |
| Breaking changes за квартал    | 0        |             |
| Новые компоненты за месяц      | 1        |             |
| Custom CSS обходные решения    | 4        |             |

---

### Что мы получили в этой главе

- Набор практических метрик для Design System.
- Понимание, как измерять пользу, а не только «количество компонентов».
- Способ вовремя замечать деградацию архитектуры.
- Документ `metrics.md` как часть операционной модели Nova.

---

### Итог

Хорошая Design System не та, в которой много компонентов.  
Хорошая Design System — та, которую **реально используют**, которая **ускоряет работу** и при этом **остаётся управляемой**.

Метрики помогают видеть это трезво и развивать Nova осознанно.