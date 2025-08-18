## 1. Поверхностное копирование (Shallow Copy)

Только верхний уровень копируется. Вложенные объекты сохраняют ссылку.

### Способы:

- **Object.assign**
```javascript
const obj = { a: 1, b: 2 };
const copy = Object.assign({}, obj);
```

- **Спред-оператор `...`**
```javascript
const obj = { a: 1, b: 2 };
const copy = { ...obj };
```

> 💡 Самый популярный способ — через спред `{ ...obj }`.

---

## 2. Глубокое копирование (Deep Copy)

Копируются все уровни объекта, включая вложенные структуры.

### Способы:

- **JSON.parse + JSON.stringify**
```javascript
const obj = { a: 1, b: { c: 2 } };
const deepCopy = JSON.parse(JSON.stringify(obj));
```
> ⚠️ Минусы: потеряются функции, `undefined`, `Symbol`, `Date`, `Map`, `Set`.

- **structuredClone** (если поддерживается)
```javascript
const obj = { a: 1, b: { c: 2 } };
const deepCopy = structuredClone(obj);
```
> ✅ structuredClone поддерживает даты, Map, Set и др.

---

## 3. Копирование через библиотеки

- **lodash**
```javascript
import _ from 'lodash';

const deepCopy = _.cloneDeep(obj);
```

📦 Отлично подходит для сложных объектов с вложенными структурами.

---

## 🚀 Сводная таблица

| Ситуация                      | Способ                         |
|--------------------------------|---------------------------------|
| Поверхностная копия простого объекта | `{ ...obj }` или `Object.assign()` |
| Глубокая копия простого объекта | `JSON.parse(JSON.stringify(obj))` |
| Глубокая копия сложного объекта | `structuredClone(obj)` или `_.cloneDeep(obj)` |

---

## ⚡ Частые ошибки

- Использовать поверхностную копию для вложенных объектов ➔ Мутация данных.
- Использовать `JSON.stringify` для объектов с функциями, датами ➔ Потеря информации.

---

## 🔥 Лучшие практики

- Для простых объектов ➔ `...obj`
- Для сложных объектов ➔ `structuredClone()` или `_.cloneDeep()`
