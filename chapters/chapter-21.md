**Глава 21. Инструменты и подготовка к продакшену**

### Цель главы

Мы построили сильную Design System и зафиксировали правила работы с ней.  
Теперь нужно подготовить проект к реальному использованию в команде и к продакшену.

В этой главе мы:

- Добавим рекомендуемую конфигурацию **Stylelint**.
- Рассмотрим использование **Lightning CSS**.
- Настроим простую структуру для сборки.
- Добавим полезные npm-скрипты.
- Покажем, как проверять качество CSS автоматически.

К концу главы проект Nova станет значительно ближе к production-ready состоянию.

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
├── assets/
├── package.json                 ← новый
├── stylelint.config.js          ← новый
└── .stylelintignore             ← новый
```

---

### Содержание новых файлов

#### 1. `package.json`

```json
{
  "name": "nova-design-system",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "lint:css": "stylelint \"css/**/*.css\"",
    "lint:css:fix": "stylelint \"css/**/*.css\" --fix",
    "build:css": "lightningcss --minify --bundle --targets '>= 0.25%' css/main.css -o dist/main.min.css",
    "check": "npm run lint:css"
  },
  "devDependencies": {
    "stylelint": "^16.0.0",
    "stylelint-config-standard": "^36.0.0",
    "stylelint-order": "^6.0.0",
    "lightningcss-cli": "^1.27.0"
  }
}
```

#### 2. `stylelint.config.js`

```js
/** @type {import('stylelint').Config} */
export default {
  extends: ["stylelint-config-standard"],
  plugins: ["stylelint-order"],
  rules: {
    /* Запрещаем магические значения цветов */
    "color-no-hex": true,
    "function-disallowed-list": ["rgb", "rgba", "hsl", "hsla"],

    /* Современный синтаксис */
    "declaration-property-value-no-unknown": true,
    "no-descending-specificity": null,

    /* Порядок свойств */
    "order/properties-alphabetical-order": null,
    "order/order": [
      [
        "custom-properties",
        "declarations"
      ]
    ],

    /* Важные правила для нашей архитектуры */
    "selector-class-pattern": [
      "^([a-z][a-z0-9]*)(-[a-z0-9]+)*(__[a-z0-9]+(-[a-z0-9]+)*)?(--[a-z0-9]+(-[a-z0-9]+)*)?$",
      {
        message: "Используйте BEM-подобную схему: block__element--modifier"
      }
    ],

    /* Разрешаем nesting */
    "max-nesting-depth": 4,

    /* Предпочитаем logical properties */
    "declaration-property-value-disallowed-list": {
      "margin-left": /.*/,
      "margin-right": /.*/,
      "padding-left": /.*/,
      "padding-right": /.*/
    }
  }
};
```

> Примечание: правило на `margin-left/right` можно сделать warning вместо error на первом этапе миграции.

#### 3. `.stylelintignore`

```
dist/
node_modules/
assets/
```

#### 4. Рекомендуемый процесс сборки

После установки зависимостей:

```bash
npm install
```

Проверка качества CSS:

```bash
npm run lint:css
```

Сборка минифицированного файла:

```bash
npm run build:css
```

Результат появится в `dist/main.min.css`.

---

### Обновления существующих файлов

На этом этапе **основные CSS- и HTML-файлы не требуют изменений**.  
Они уже находятся в зрелом состоянии после Глав 1–20.

Мы только добавляем инфраструктуру качества и сборки вокруг существующей Design System.

#### Текущее состояние ключевых файлов:

- `css/main.css` — точка входа со всеми слоями.
- `css/01-tokens.css` — полная система токенов + темы + fluid + dense.
- `css/components/*` — все компоненты.
- `css/06-animations.css` — View Transitions + Scroll-driven Animations.
- `css/07-performance.css` — contain и content-visibility.
- `css/08-a11y.css` — доступность.
- `js/main.js` — интерактивность.

---

### Рекомендуемые дополнительные инструменты (по желанию)

| Инструмент          | Назначение                              |
|---------------------|-----------------------------------------|
| **Lightning CSS**   | Быстрая сборка и минификация            |
| **Stylelint**       | Контроль качества и соглашений          |
| **PurgeCSS** / Lightning   | Удаление неиспользуемого CSS (осторожно) |
| **axe DevTools**    | Проверка доступности                    |
| **Lighthouse**      | Производительность и Best Practices     |
| **Storybook**       | Документация компонентов (следующий шаг)|

---

### Что мы получили в этой главе

- Готовую конфигурацию **Stylelint** под архитектуру Nova.
- Возможность автоматической проверки CSS.
- Простую и быструю сборку через **Lightning CSS**.
- Понимание, как превратить учебный проект в поддерживаемую production-систему.
- Фундамент для дальнейшей автоматизации (CI, публикация пакета и т.д.).

---

### Итог

Теперь у вас есть не только современная Design System на чистом CSS 2026, но и базовые инженерные практики для её поддержки:

- Правила архитектуры (Глава 20)
- Инструменты контроля качества и сборки (Глава 21)

Проект готов к использованию в реальных командах и к дальнейшему масштабированию.