**Глава 20. Архитектура на практике: соглашения, правила и поддержка Design System**

### Цель главы

Мы построили большую и современную систему.  
Теперь важно зафиксировать **правила**, по которым с ней нужно работать дальше. Без чётких соглашений даже самая красивая архитектура со временем превращается в хаос.

В этой главе мы:

- Зафиксируем архитектурные правила проекта Nova.
- Опишем соглашения по именованию и организации кода.
- Определим, как правильно добавлять новые компоненты и токены.
- Дадим рекомендации по поддержке и развитию Design System.
- Подведём итог структуры проекта.

Это глава-наставление для будущей работы с системой.

---

### Финальная структура проекта

```
nova/
├── index.html
├── style-guide.html
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

---

### Архитектурные правила Nova Design System

#### 1. Слои (`@layer`) — строго соблюдать порядок

```css
@layer reset, tokens, base, layout, components, utilities, animations, performance, a11y, overrides;
```

- В `overrides` можно писать только в крайнем случае.
- Никогда не повышайте специфичность вместо использования правильного слоя.

#### 2. Токены — единственный источник правды

- Все цвета, отступы, радиусы, размеры шрифтов и тени берутся **только** из `01-tokens.css`.
- Запрещено использовать «магические» значения (`14px`, `#3b82f6`, `8px`) напрямую в компонентах.
- Новые токены добавляются только в `01-tokens.css` с чётким именем.

#### 3. Правила именования компонентов

- Блок: `.card`, `.btn`, `.form-group`
- Элемент: `.card__title`, `.btn__icon`
- Модификатор: `.btn--primary`, `.btn--sm`, `.card--stat`
- Состояние: `.is-active`, `.is-open`, `.is-loading`

#### 4. Контракт компонента

Каждый компонент должен иметь:

- Понятный публичный API (классы + CSS-переменные).
- Поддержку `:focus-visible`.
- Корректную работу с темами.
- Минимальную зависимость от внешнего контекста.

Пример хорошего контракта:

```css
.btn {
  --btn-bg: var(--color-primary);
  --btn-color: var(--color-text-inverse);
  --btn-padding-y: var(--space-2);
  /* ... */
}
```

#### 5. Куда добавлять новый код

| Тип изменений              | Файл / папка              |
|---------------------------|---------------------------|
| Новый токен               | `01-tokens.css`           |
| Новый компонент           | `components/_name.css`    |
| Изменение layout          | `03-layout.css`           |
| Утилита                   | `05-utilities.css`        |
| Анимация                  | `06-animations.css`       |
| Оптимизация               | `07-performance.css`      |
| Доступность               | `08-a11y.css`             |
| Глобальный сброс          | `00-reset.css`            |

#### 6. Работа с темами

- Все новые цвета добавляются как семантические токены.
- Физические цвета (палитра) отделены от семантических.
- Новая тема = новый блок `[data-theme="..."]`.

#### 7. Логические свойства

- По умолчанию используем logical properties.
- Физические свойства — только когда направление должно быть привязано к экрану, а не к тексту.

#### 8. Производительность

- Для карточек и списков используем `contain` и `content-visibility`.
- `will-change` применяем только на время анимации.
- Анимируем преимущественно `transform` и `opacity`.

---

### Содержание ключевых файлов (текущее состояние)

#### `css/main.css`

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

#### `css/04-components.css`

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

#### Остальные файлы

На данном этапе все файлы находятся в зрелом состоянии, сформированном за предыдущие 19 глав:

- `01-tokens.css` — OKLCH-палитры, семантические токены, несколько тем, fluid-типографика, dense-режим.
- Компоненты — полные, с контрактами, поддержкой тем и логическими свойствами.
- `06-animations.css` — View Transitions + Scroll-driven Animations.
- `07-performance.css` — `contain`, `content-visibility`.
- `08-a11y.css` — focus, skip-link, улучшения доступности.
- `js/main.js` — тема, tabs, accordion, dropdown, toast.

Мы намеренно не дублируем полный код всех файлов — он был последовательно построен и стабилизирован в предыдущих главах.

---

### Чек-лист при добавлении нового компонента

1. Создать файл `components/_name.css`.
2. Подключить его в `04-components.css`.
3. Использовать только токены.
4. Добавить модификаторы по принятой схеме.
5. Обеспечить `:focus-visible`.
6. Проверить работу в светлой, тёмной и high-contrast темах.
7. При необходимости добавить пример в `style-guide.html`.
8. Проверить RTL (логические свойства).

---

### Рекомендации по дальнейшей поддержке

- Проводите регулярный аудит токенов (удаляйте неиспользуемые).
- Используйте Stylelint с правилами на порядок слоёв и запрет магических значений.
- Рассмотрите внедрение Lightning CSS для минификации и транспиляции.
- Ведите changelog Design System.
- Документируйте Breaking Changes при изменении токенов или API компонентов.

---

### Итог главы

Теперь у вас есть не только работающий проект, но и **система правил**, которая позволяет развивать его дальше без деградации архитектуры.

Nova Design System готова к реальному использованию и масштабированию командой.