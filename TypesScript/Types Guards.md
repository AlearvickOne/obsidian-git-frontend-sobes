Type Guards - это механизм сужения типов в условиях (`if`, `switch`,`Тернарный оператор`)
Без использования Type Guard - Type Script не может точно определить тип и при использовании данного способа, код становится более безопасным и приятным

- Основные виды Type Guards
	- `typeof `- Проверяет тип числа, строки, boolean и так далее. Он поддерживает типы `string`,`number`, `boolean`, `symbol`, `undefined`, `object`, `function`. Но не работает с типами `null` и массивами (`typeof []` -> `object`)
	- `instanceof (для классов)` - Проверяет, является ли объект экземпляром класса.
	- `Array.isArray(arr)` - Проверяет, является ли элемент массивом
	- Пользовательский TypeGuard (с `is`: `arg is Type`) - Функции которые возвращают `arg is Type`. 
	  Например: 
	  `function isFish(pet: Fish | Bird): pet is Fish { return "swim" in pet}` 
	  Если в типе `Fish` есть `"swim"`, то вернёт `true` иначе `false`
	- Проверка на `null / undefined` делается через строгое равенство (`===` `!==`)
	- Проверка наличия свойства у объекта делается через `in` - `"drive" in vehicle`, которое вернет `boolean`