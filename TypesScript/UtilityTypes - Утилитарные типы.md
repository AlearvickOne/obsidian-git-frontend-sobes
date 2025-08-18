UtilityTypes - это готовые обобщённые типы, которые упрощают работу с типами. Они позволяют трансформировать существующие типы без ручного переопределения.
Все utilityTypes возвращают Type и при работе с interface результат будет type.

Вот несколько основных утилитарных типов
- Для обычных типов
	- `Partial<Type>` - Делает все свойства `Type` необязательными.
	- `Required<Type>` - Делает все свойства `Type` обязательными
	- `Readonly<Type>` - Делает все свойства `Type` только Readonly
	- `Record<Keys, Type>` - Создает тип с ключами Keys и значениями `Type`
	- `Pick<Type, Keys>` - Выбирает только указанные свойства из `Type`
	- `Omit<Type, Keys>` - Удаляет указанные свойства из `Type`
	- `NonNullable<Type>` - Удаляет `null` и `undefined` из `Type`
	- `Awaited<PromiseType>` - Раскрывает `Promise` и возвращает внутренний тип
- Для `UnionType`
	- `Exclude<UnionType, ExcludedMembers>` - Исключает типы из Union
	- `Extract<UnionType, ExtractedMembers>` - Оставляет только указанные типы из Union
- Для `FunctionType`
	- `Parameters<FunctionType>` - Извлекает типы параметров функции в виде кортежа
	- `ReturnType<FunctionType>` - Извлекает тип возвращаемого значение функции
- Для `ClassType`
	- `ConstructorParameters<ClassType>` - Извлекает типы параметров конструктора класса
	- `InstanceType<ClassType>` - Извлекает тип экземпляра класса
	
