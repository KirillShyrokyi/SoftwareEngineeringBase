# Generics — обобщённый код без потери типа

[← C#](index.md) · [Roadmap](../roadmap.md)

## Проблема

Если одна и та же логика нужна для разных типов, отдельные `UserStorage`, `ProductStorage` и похожие классы приводят к повторению и захламлению кода.

Generics позволяют описать общую логику один раз и выбрать конкретный тип при использовании.

```csharp
class Box<T>
{
    public T Value { get; set; }
}

Box<int> numberBox = new();
Box<string> textBox = new();
```

Здесь `T` — **type parameter**: место для типа, который будет указан позже. В `Box<int>` значение `T` уже равно `int`, а в `Box<string>` — `string`.

## Несколько параметров типа

Параметров типа может быть несколько:

```csharp
Dictionary<string, int> counts = new();
```

Для `Dictionary<TKey, TValue>` здесь:

```text
TKey   = string
TValue = int
```

Один и тот же параметр типа означает одну общую роль типа, а разные параметры позволяют выбирать типы независимо.

## Type inference

В generic-методе компилятор иногда может вывести конкретный тип из аргумента:

```csharp
T GetValue<T>(T value) => value;

int number = GetValue(25);
string text = GetValue("Hello");
```

Это **type inference**: не метод выбирает тип во время выполнения, а компилятор выводит `T` из вызова.

## Constraints

Неограниченный `T` не обещает generic-коду специальных возможностей. **Constraint** добавляет такую гарантию.

```csharp
interface IHasId
{
    int Id { get; }
}

class Storage<T> where T : IHasId
{
    public int GetId(T item)
    {
        return item.Id;
    }
}
```

`where T : IHasId` означает, что конкретный тип, подставленный вместо `T`, обязан реализовать `IHasId`. Поэтому компилятор разрешает обращаться к `item.Id`.

Совпадения структуры недостаточно: класс с таким же свойством `Id`, но без явной реализации `IHasId`, этому constraint не удовлетворяет.

Ещё один пример:

```csharp
class Factory<T> where T : new()
{
    public T Create()
    {
        return new T();
    }
}
```

`new()` гарантирует, что `T` можно создать через `new T()` без аргументов. Просто наличие конструктора с параметрами этой гарантии не даёт.

Constraints можно комбинировать:

```csharp
class Repository<T> where T : IHasId, new()
{
}
```

Здесь тип должен одновременно реализовать `IHasId` и допускать создание через `new T()`.

## Type safety

Generics сохраняют информацию о конкретном типе:

```csharp
List<string> names = new();
names.Add("Anna");

string name = names[0];
```

Для этого списка `T = string`, поэтому полученный элемент уже имеет тип `string`: дополнительное приведение к `string` не требуется.

`List<object>` тоже является generic-типом — просто в нём `T = object`, поэтому точной информации о конкретных элементах меньше.

## Моя рабочая модель

```text
generic type / method
        ↓
T — параметр типа
        ↓
при использовании выбирается конкретный тип
        ↓
одна общая логика работает с разными типами
без дублирования и без потери строгой типизации

constraint
        ↓
дополнительная гарантия о допустимом T
```

Главная идея: generics нужны не только для сокращения повторяющегося кода. Они позволяют совместить повторное использование общей логики с **type safety**.
