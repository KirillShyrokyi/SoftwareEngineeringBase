# Tuples — несколько значений как один результат

[← C#](index.md) · [Roadmap](../roadmap.md)

## Зачем нужен tuple

Когда методу нужно вернуть несколько связанных значений, не всегда требуется создавать отдельный `class` или `record`. Кортеж (*tuple*) позволяет объединить их в один результат с известными типами элементов.

```csharp
(int Age, string Name) GetUser()
{
    return (25, "Alex");
}

var user = GetUser();
Console.WriteLine(user.Age);  // 25
Console.WriteLine(user.Name); // Alex
```

Метод возвращает **одно значение типа `(int Age, string Name)`**, внутри которого находятся два элемента. У неименованных элементов используются имена `Item1`, `Item2` и далее.

## Deconstruction — деконструкция

```csharp
var (age, name) = GetUser();
Console.WriteLine(name); // Alex
Console.WriteLine(age);  // 25
```

Деконструкция раскладывает элементы кортежа по отдельным переменным в соответствующем порядке. В данном примере `age` — самостоятельная переменная: изменение `age` не изменяет `user.Age`.

## ValueTuple — value type

Обычные кортежи вида `(int, string)` в C# используют `System.ValueTuple`, то есть являются **value types**.

```csharp
(int Age, string Name) user = (20, "Alex");
var second = user;

second.Age = 30;

Console.WriteLine(user.Age);   // 20
Console.WriteLine(second.Age); // 30
Console.WriteLine(user == second); // False
```

При `second = user` копируется **значение кортежа**, а не ссылка на тот же объект. Поэтому изменение элемента `second` не меняет `user`. Оператор `==` сравнивает соответствующие элементы кортежей.

Это копирование самого `ValueTuple` не означает глубокое копирование объектов, если среди его элементов есть ссылочные типы: ссылки внутри элементов тоже копируются как значения.

## Связь с record class

`record User(string Name, int Age)` по умолчанию создаёт **ссылочный** `record class`, а не `ValueTuple`. При `User second = first` копируется ссылка. Выражение `second with { Age = 30 }` создаёт **новый объект** и затем позволяет присвоить ссылку на него переменной `second`; исходный объект не изменяется.

```csharp
record User(string Name, int Age);

User first = new("Alex", 20);
User second = first;

Console.WriteLine(ReferenceEquals(first, second)); // True

second = second with { Age = 30 };

Console.WriteLine(ReferenceEquals(first, second)); // False
Console.WriteLine(first.Age);                       // 20
```

## Mental model

```text
tuple = одно значение с несколькими типизированными элементами
deconstruction = извлечь элементы в отдельные переменные
ValueTuple assignment = копия значения кортежа
record class assignment = копия ссылки
record class with = новый объект на основе старого
```

**Статус: 🟢 Понимаю.** Различие между копированием `ValueTuple`, копированием ссылки на `record class` и созданием нового объекта через `with` подтверждено разбором поведения кода. Самостоятельная практика для 🔵 здесь не зафиксирована.
