# PROMPT.md — Генератор страницы `index.html`

> Единственный источник правды для генерации и изменения `index.html`.
> Ручное редактирование `index.html` запрещено.
> Любая правка вносится через раздел «ЗАДАЧА» (раздел 10) этого файла.

---

## 0. Как пользоваться

1. Открой этот файл.
2. Впиши в раздел 10 (ЗАДАЧА) одну конкретную правку.
3. Отправь весь текст промта (разделы 1–10) в LLM.
4. Замени `index.html` целиком полученным файлом.
5. Сверь diff: изменения должны совпадать с текстом ЗАДАЧИ.

**Правило одного изменения:** одна ЗАДАЧА — одно логическое изменение.

---

## 1. РОЛЬ

Ты — генератор статических HTML-страниц по спецификации.
Ты **не проявляешь креативность**. Ты строго исполняешь спецификацию.
Ты не улучшаешь код, не добавляешь ничего от себя, не переформатируешь.

---

## 2. ЦЕЛЬ

Сгенерировать **единый файл `index.html`** — статическую демонстрацию
UI-компонентов в стилистике MAX. Без бэкенда, без сборки, без npm.

---

## 3. ЖЁСТКИЕ ПРАВИЛА

1. Вывод — **ровно один файл** `index.html`. Никаких дополнительных файлов.
2. Никаких объяснений, вступлений, выводов и комментариев вне кода.
3. Не добавляй компоненты, секции, стили или токены, которых нет
   в СПЕЦИФИКАЦИИ. Если чего-то нет в спецификации — не делай.
4. Не удаляй и не переименовывай существующие классы и токены.
5. Не меняй порядок секций.
6. Не меняй значения дизайн-токенов, если это не указано в ЗАДАЧЕ.
7. Если ЗАДАЧА противоречит спецификации — задай **один** уточняющий
   вопрос и остановись. Не догадывайся молча.
8. Внешние зависимости строго фиксированы (см. раздел 4).
9. Форматирование, отступы, порядок CSS-правил, порядок объявления
   компонентов — не меняй, если это не требуется ЗАДАЧЕЙ.

---

## 4. DEPENDENCIES (не менять версии)

```html
<script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
```

Других внешних ресурсов быть не должно. Все иконки — inline SVG.

---

## 5. ДИЗАЙН-ТОКЕНЫ

Значения меняются **только по явному указанию в ЗАДАЧЕ**.

```css
:root {
  --max-bg: #f5f5f7;
  --max-surface: #ffffff;
  --max-surface-secondary: #f0f0f3;
  --max-surface-tertiary: #e8e8ec;
  --max-text: #000000;
  --max-text-secondary: #6b6b70;
  --max-text-tertiary: #9a9aa0;
  --max-border: rgba(0, 0, 0, 0.08);
  --max-primary: #007aff;
  --max-primary-hover: #0066d6;
  --max-destructive: #ff3b30;
  --max-radius-sm: 8px;
  --max-radius-md: 12px;
  --max-radius-lg: 16px;
  --max-radius-xl: 20px;
  --max-font: -apple-system, BlinkMacSystemFont, 'Segoe UI',
              Roboto, Helvetica, Arial, sans-serif;
}
```

Тема: **только светлая**.

---

## 6. СТРУКТУРА ФАЙЛА (строго в этом порядке)

### 6.1. `<head>`

1. `<meta charset="UTF-8">`
2. `<meta name="viewport" ...>`
3. `<title>`
4. Три `<script>` из раздела 4 — в указанном порядке.
5. `<style>`:
   - сначала `:root` с токенами;
   - затем стили в порядке:
     `base → typography → panel → button → icon-button →
      tool-button → input → switch → avatar → spinner → counter →
      cell → grid/flex → utilities`.

### 6.2. `<body>`

1. `<div id="root">`
2. `<script type="text/babel">`:
   - SVG-иконки — каждая отдельным компонентом, порядок:
     `SearchIcon, MoreIcon, EditIcon, CloseIcon, HomeIcon, ChevronIcon`;
   - Компоненты — в порядке:
     `Section, Button, IconButton, ToolButton, Switch,
      Avatar, Spinner, Counter, Cell`;
   - `App`;
   - `ReactDOM.createRoot(document.getElementById('root')).render(<App />)`.

---

## 7. СПЕЦИФИКАЦИЯ ЭКРАНА

### 7.1. Контейнер

- `.app` — `max-width: 480px`, `margin: 0 auto`, `padding: 20px 16px 60px`.
- Каждая секция — `.demo-section` с заголовком `.section-title`.

### 7.2. Секции (порядок и содержимое фиксированы)

| # | Секция | Содержимое |
|---|---|---|
| 1 | Header | `h1.t-display` «MAX UI Демо» + `p.t-caption` |
| 2 | Кнопки | Button: primary, secondary, ghost, loading, disabled, destructive («Удалить») |
| 3 | Иконочные кнопки | IconButton: search, more(ghost), close(destructive), edit(disabled) |
| 4 | ToolButton | только иконка / с текстом / secondary / disabled |
| 5 | Поля ввода | input, textarea, switch + подпись состояния |
| 6 | Аватары | 48 circle, 48 squircle, 64 circle, 64 squircle + close |
| 7 | Спиннеры и счётчики | sm/md/lg + 5, 1200, 5600000, 3(muted) |
| 8 | Список ячеек | Cell, Cell(action+chevron), CellHeader, Cell(icon+chevron) |
| 9 | Типографика | display, title-1..3, body, caption |
| 10 | Сетка и Flex | grid-3 (1,2,3) + flex-row (Flex 1, Flex 2) |
| 11 | Панели | default, secondary, tertiary |

### 7.3. Контракт компонентов (не менять сигнатуры)

```
Section({ title, children })
Button({ variant='primary', loading, children, ...props })
IconButton({ variant='default', children, ...props })
ToolButton({ variant='default', icon, children, ...props })
Switch({ checked, onChange })
Avatar({ src, size=48, form='circle', onClose })
Spinner({ size='md' })
Counter({ value, muted })
Cell({ title, subtitle, icon, onClick, chevron })
```

### 7.4. Поведение

| Сценарий | Ожидание |
|---|---|
| Ввод в Input / Textarea | значение хранится в `useState` |
| Клик по Switch | переключает `checked`, подпись обновляется |
| Клик по Cell с `onClick` | `alert('Нажато!')` |
| Клик по close у Avatar | `alert('Закрыть')` |
| Hover / Active / Disabled | согласно CSS-классам |
| Бэкенд | отсутствует |
| Хранение | отсутствует (нет localStorage / cookies) |

### 7.5. Адаптивность

- `≤ 480px` — `.app` на всю ширину.
- `> 480px` — `.app` по центру, `max-width: 480px`.
- `320px` — контент не ломается, `.row` переносится (`flex-wrap`).

---

## 8. ЗАПРЕЩЕНО

- Добавлять новые секции, компоненты, токены, CSS-классы.
- Менять порядок секций и компонентов.
- Использовать другие иконки, шрифты, CDN.
- Добавлять обработку ошибок, фолбэки, console.log, комментарии в коде.
- Переформатировать существующий код, если это не требуется задачей.
- Менять отступы, переносы строк, порядок CSS-правил.
- Добавлять тёмную тему, aria-атрибуты, safe-area, анимации —
  если это не указано в ЗАДАЧЕ явно.

---

## 9. ФОРМАТ ОТВЕТА

Один блок:
<!DOCTYPE html>
...

Ничего больше: ни пояснений, ни резюме, ни списка изменений.

---

## 10. ЗАДАЧА

<!--
  Впиши сюда РОВНО ОДНУ правку.
  Указывай:
    • ЧТО менять (токен / секция / компонент / CSS-правило)
    • ГДЕ именно (селектор, имя секции, имя компонента)
    • НА ЧТО менять (новое значение)
    • ЧТО не трогать (явно: «остальное без изменений»)
-->

Сгенерируй страницу с нуля по спецификации.
