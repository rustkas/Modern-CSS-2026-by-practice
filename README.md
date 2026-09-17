# Modern CSS 2026 by Practice

## Практическое руководство по современному CSS

> Практическое продолжение книги [Modern CSS 2026](https://github.com/rustkas/Modern-CSS-2026).  
> Не просто теория — полноценный проект, Design System и навыки работы со всеми ключевыми возможностями CSS 2026.

---

## О руководстве

За последние годы CSS превратился из языка оформления в полноценную платформу проектирования интерфейсов.  
Это практическое руководство показывает, как применять современный CSS **в реальном проекте**.

В ходе прохождения вы создадите:

- приложение-дашборд **Nova**;
- собственную **Design System**;
- набор переиспользуемых компонентов;
- Style Guide;
- архитектуру на `@layer`, Design Tokens, OKLCH, Container Queries, View Transitions и других возможностях CSS 2026.

После прохождения у вас будет **законченный проект** и практические навыки работы со всеми основными возможностями современного CSS.

---

## Связь с теоретической книгой

| Теория | Практика |
|--------|----------|
| [Modern CSS 2026](https://github.com/rustkas/Modern-CSS-2026) | **Modern CSS 2026 by Practice** (этот репозиторий) |

Теоретическая книга объясняет *что* и *почему*.  
Это руководство показывает *как* применить знания на практике, шаг за шагом собирая один цельный проект.

---

## Для кого это руководство

- Frontend-разработчиков
- Разработчиков дизайн-систем
- Тех, кто хочет перейти с Sass/Bootstrap на современный CSS
- Тех, кто хочет системно освоить CSS 2026 на практике

---

## Что вы освоите

- Cascade Layers (`@layer`)
- Design Tokens и OKLCH
- CSS Nesting
- `:has()`
- Container Queries
- CSS Grid и Flexbox
- Anchor Positioning
- Popover API
- View Transitions API
- Scroll-driven Animations
- Производительность (`contain`, `content-visibility`)
- Доступность и logical properties
- Построение и поддержку Design System

---

## Структура репозитория

```text
Modern-CSS-2026-by-practice/
├── README.md
├── chapters/
│   ├── chapter01.md
│   ├── chapter02.md
│   ├── ...
│   └── chapter29.md
├── nova/                    # Итоговый проект (по мере прохождения)
│   ├── packages/
│   │   └── design-system/
│   └── apps/
│       └── dashboard/
└── docs/
```

---

## Оглавление

### Часть I. Фундамент

1. [Архитектура проекта и структура файлов](chapters/chapter01.md)
2. [Базовая настройка: Design Tokens, OKLCH и тёмная тема](chapters/chapter02.md)
3. [CSS как язык компонентов: первые компоненты и контракты](chapters/chapter03.md)
4. [Формы, состояния и сила селектора `:has()`](chapters/chapter04.md)

### Часть II. Layout и позиционирование

5. [Современный Layout: CSS Grid, Subgrid и Container Queries](chapters/chapter05.md)
6. [Anchor Positioning: Tooltip, Dropdown и Popover без JavaScript](chapters/chapter06.md)

### Часть III. Анимация

7. [Современные анимации: View Transitions и плавные взаимодействия](chapters/chapter07.md)
8. [Scroll-driven Animations: анимация от скролла без JavaScript](chapters/chapter08.md)

### Часть IV. Компоненты и Design System

9. [Popover API, модальные окна и изоляция компонентов](chapters/chapter09.md)
10. [Построение Design System: токены, контракты и масштабирование](chapters/chapter10.md)
11. [Расширение Design System: Tabs, Accordion и Toast](chapters/chapter11.md)

### Часть V. Качество и полировка

12. [Производительность CSS: contain, content-visibility и оптимизация](chapters/chapter12.md)
13. [Доступность и полировка Design System](chapters/chapter13.md)
14. [Финальная полировка и живой Style Guide](chapters/chapter14.md)
15. [Финальный проект, чек-лист и дальнейшее развитие](chapters/chapter15.md)

### Часть VI. Продвинутый уровень

16. [Продвинутые паттерны и подготовка к продакшену](chapters/chapter16.md)
17. [Продвинутая темизация: несколько тем и high-contrast](chapters/chapter17.md)
18. [Fluid-типографика и современный адаптивный ритм](chapters/chapter18.md)
19. [Логические свойства и подготовка к интернационализации (RTL)](chapters/chapter19.md)

### Часть VII. Архитектура, процессы и масштабирование

20. [Архитектура на практике: соглашения, правила и поддержка Design System](chapters/chapter20.md)
21. [Инструменты и подготовка к продакшену](chapters/chapter21.md)
22. [Миграция существующих проектов на архитектуру Nova](chapters/chapter22.md)
23. [Итоговый обзор: соответствие книге и мастер-чеклист](chapters/chapter23.md)
24. [Упаковка Design System для повторного использования](chapters/chapter24.md)
25. [Документация, передача и контроль качества Design System](chapters/chapter25.md)
26. [Полностью современный проект — финальная сборка](chapters/chapter26.md)
27. [Эволюция Design System: поддержка, governance и развитие](chapters/chapter27.md)
28. [Метрики и эффективность Design System](chapters/chapter28.md)
29. [Онбординг команды в Design System](chapters/chapter29.md)

---

## Как проходить руководство

1. Читайте главы последовательно.
2. После каждой главы обновляйте проект `nova`.
3. Сверяйте структуру файлов с указанной в главе.
4. Не перескакивайте через архитектуру — слои, токены и контракты компонентов важны не меньше «красивых фич».
5. В конце у вас должен получиться рабочий проект и Style Guide.

---

## Итоговый результат

После прохождения руководства у вас будет:

- рабочий dashboard-проект **Nova**;
- переиспользуемая Design System;
- набор современных CSS-паттернов;
- Style Guide;
- понимание, как масштабировать и поддерживать CSS-архитектуру в реальной команде.

---

## Статус

🚧 В активной разработке

Версия: `0.1`

---

## Лицензия

MIT

---

## Связанные материалы

- Теоретическая книга: [Modern CSS 2026](https://github.com/rustkas/Modern-CSS-2026)
- Исходные спецификации и документация:
  - [CSS Working Group](https://www.w3.org/Style/CSS/)
  - [MDN Web Docs](https://developer.mozilla.org/)
  - [web.dev](https://web.dev/)
