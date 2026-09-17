**Глава 22. Миграция существующих проектов на архитектуру Nova**

### Цель главы

Большинство разработчиков не начинают проект с нуля.  
Гораздо чаще нужно **внедрять современную архитектуру CSS** в уже существующий продукт.

В этой главе мы разберём практический подход к миграции:

- Как постепенно внедрять Cascade Layers и Design Tokens.
- Как переводить старые компоненты на новую систему.
- Как минимизировать риски и не сломать интерфейс.
- Как организовать миграцию в команде.

К концу главы у вас будет понятный план перехода на архитектуру, которую мы строили на протяжении всего руководства.

---

### Структура проекта на данном этапе

Структура самого проекта Nova остаётся прежней (мы не меняем учебный проект, а учимся мигрировать другие проекты на его подходы):

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
├── package.json
├── stylelint.config.js
└── assets/
```

---

### Стратегия миграции (поэтапная)

#### Этап 1. Подготовка фундамента (низкий риск)

1. Добавьте `01-tokens.css` с Design Tokens.
2. Подключите `@layer` и задайте порядок слоёв.
3. Перенесите глобальный reset в `layer(reset)`.
4. Начните использовать токены только в **новых** компонентах.

Пример подключения в существующем проекте:

```css
@layer reset, tokens, base, layout, components, utilities, overrides;

@import url("tokens.css") layer(tokens);
```

На этом этапе старый CSS продолжает работать как раньше.

#### Этап 2. Введение токенов в существующие компоненты

- Заменяйте «магические» значения на токены постепенно.
- Начинайте с цветов и отступов.
- Не пытайтесь переписать всё сразу.

Пример:

```css
/* Было */
.card {
  background: #fff;
  padding: 24px;
  border-radius: 12px;
}

/* Стало */
.card {
  background: var(--color-surface);
  padding: var(--space-6);
  border-radius: var(--radius-xl);
}
```

#### Этап 3. Перевод компонентов в `@layer components`

- Выносите компоненты в отдельные файлы.
- Подключайте их через `layer(components)`.
- Убирайте `!important` по мере снижения конфликтов специфичности.

#### Этап 4. Внедрение современных возможностей

Только после стабилизации архитектуры добавляйте:

- Container Queries
- `:has()`
- Anchor Positioning
- View Transitions
- Scroll-driven Animations

Так вы снижаете риск регрессий.

#### Этап 5. Темизация и dark mode

- Вынесите цвета в семантические токены.
- Добавьте `data-theme`.
- Подключите светлую/тёмную тему.

---

### Практические правила безопасной миграции

1. **Не смешивайте старый и новый подход в одном компоненте** без необходимости.
2. **Новый код — только на токенах и слоях**.
3. **Старый код удаляйте только после покрытия новым**.
4. Используйте Stylelint, чтобы запретить появление новых магических значений.
5. Делайте миграцию по доменам/страницам, а не «всего проекта сразу».
6. Обязательно имейте визуальный контроль (Style Guide, Chromatic, Playwright screenshots и т.д.).

---

### Пример плана миграции на 6–8 недель

| Неделя | Действия                                      |
|--------|-----------------------------------------------|
| 1      | Токены + `@layer` + Stylelint                 |
| 2–3    | Перевод цветов и spacing на токены            |
| 4–5    | Вынос основных компонентов в layers           |
| 6      | Темизация (light/dark)                        |
| 7      | Container Queries + современные селекторы      |
| 8      | Анимации, performance, a11y-полировка         |

---

### Содержание ключевых файлов Nova (текущее состояние)

На данном этапе учебный проект полностью стабилизирован.  
Мы не вносим новых изменений в CSS-компоненты, а используем его как **эталон архитектуры** для миграции других проектов.

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

Остальные файлы (`01-tokens.css`, компоненты, анимации, performance, a11y, `package.json`, `stylelint.config.js`) остаются в финальном состоянии предыдущих глав.

---

### Что мы получили в этой главе

- Понятный поэтапный план миграции существующих проектов.
- Стратегию снижения рисков.
- Правила, которые позволяют внедрять современный CSS без «большого взрыва».
- Возможность использовать Nova как референс-архитектуру.

---

### Итог

Настоящая ценность руководства — не только в том, что вы создали проект с нуля, но и в том, что теперь умеете **переводить старые проекты** на современную, масштабируемую CSS-архитектуру 2026 года.