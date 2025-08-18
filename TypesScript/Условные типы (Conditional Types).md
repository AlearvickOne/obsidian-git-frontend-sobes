## Что такое условные типы?

Условные типы — это механизм в TypeScript, который позволяет создавать типы, выбирая один из двух вариантов в зависимости от проверки "является ли тип `T` подтипом типа `U`".

Синтаксис:

```ts
T extends U ? X : Y
```

- Если тип `T` может быть присвоен типу `U` — результатом будет тип `X`.
    
- Иначе — тип `Y`.
    

Это мощный инструмент для создания более гибких и динамичных типов.

---

## Пример: проверка типа на string

```ts
type IsString<T> = T extends string ? true : false;

type A = IsString<'hello'>;  // true
type B = IsString<42>;       // false
```

Здесь `IsString<'hello'>` возвращает `true`, потому что `'hello'` — это строка. А `IsString<42>` — `false`.

---

## Условные типы с React Props

Часто в React компонентах нам нужно типизировать пропсы в зависимости от других параметров.

```ts
type Props<T> = T extends { disabled: boolean } 
  ? { disabled: boolean } 
  : { enabled: boolean };

// Использование:
type ButtonProps = Props<{ disabled: boolean }>; // { disabled: boolean }
type LabelProps = Props<{ text: string }>;      // { enabled: boolean }
```

Это полезно, если у компонента разные пропсы в зависимости от состояния.

---

## Встроенные условные типы

TypeScript уже содержит полезные условные типы:

- `Exclude<T, U>` — исключает из типа `T` все варианты, совместимые с `U`.
- `Extract<T, U>` — оставляет только варианты из `T`, совместимые с `U`.

```ts
type T1 = Exclude<'a' | 'b' | 'c', 'a' | 'b'>;  // 'c'
type T2 = Extract<'a' | 'b' | 'c', 'a' | 'b'>;  // 'a' | 'b'
```

---

## Условная типизация `children` в React-компоненте

```tsx
type ComponentProps<T> = T extends { children: React.ReactNode } 
  ? { children: React.ReactNode } 
  : {};

function MyComponent<T>(props: ComponentProps<T>) {
  // ...
}
```

Так компонент будет корректно принимать пропсы с детьми или без, в зависимости от того, как вызывается.

---

## Использование `infer` с условными типами

`infer` позволяет извлечь тип внутри другого типа.

Пример — получить тип элементов массива:

```ts
type ElementType<T> = T extends (infer U)[] ? U : T;

type T3 = ElementType<string[]>;  // string
type T4 = ElementType<number>;    // number
```

Если `T` — массив, то `ElementType<T>` будет типом его элементов.

---

## Более сложный пример — фильтрация типов в union

```ts
type Filter<T, U> = T extends U ? T : never;

type OnlyStrings = Filter<string | number | boolean, string>;  // string
```

Этот тип выбирает из union только те типы, которые совместимы с `U`.

---

## Почему это полезно?

- Позволяет создавать универсальные и адаптивные типы.
- Уменьшает дублирование кода.
- Позволяет более точно типизировать функции и компоненты.
