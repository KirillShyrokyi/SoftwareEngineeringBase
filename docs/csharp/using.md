# Using — пространства имён и освобождение ресурсов

## Два разных назначения

`using System.IO;` — директива, позволяющая обращаться к типам из пространства имён по короткому имени. Например, писать `StreamReader` вместо `System.IO.StreamReader`. Она не создаёт объекты и не загружает библиотеку автоматически.

`using` для ресурса решает другую задачу: своевременно вызвать метод освобождения ресурсов `Dispose()`.

## Using statement — освобождение после блока

```csharp
using (var reader = new StreamReader("data.txt"))
{
    Console.WriteLine(reader.ReadToEnd());
}
Console.WriteLine("Готово");
```

После выхода из блока `using` вызывается `Dispose()`, если объект был успешно создан. Это происходит и при исключении внутри блока. При этом `using` не перехватывает исключение.

По смыслу конструкция близка к `try/finally`:

```csharp
var reader = new StreamReader("data.txt");
try
{
    Console.WriteLine(reader.ReadToEnd());
}
finally
{
    reader.Dispose();
}
```

## Using declaration — освобождение после области видимости

```csharp
void Read()
{
    using var reader = new StreamReader("data.txt");
    Console.WriteLine("A");
    Console.WriteLine(reader.ReadToEnd());
    Console.WriteLine("B");
}
```

Здесь `Dispose()` вызывается при выходе из текущей области видимости, то есть после строки `B`, перед завершением метода.

## Зачем нужен IDisposable

`IDisposable` — интерфейс с контрактом `Dispose()`. Класс определяет, как освобождать свои ресурсы, а `using` автоматически вызывает этот метод. В C# также допускается подходящий метод `Dispose()` по шаблону (pattern-based disposal).

```csharp
class Resource : IDisposable
{
    public void Dispose() => Console.WriteLine("D");
}

using (var resource = new Resource())
{
    Console.WriteLine("A");
}
Console.WriteLine("B");
```

Результат: `A`, `D`, `B`.

## Ментальная модель (mental model)

- `using Namespace;` — сокращает имена типов.
- `using (...) { }` — вызывает `Dispose()` при выходе из блока.
- `using var ...;` — вызывает `Dispose()` при выходе из текущей области.
- `IDisposable` — контракт освобождения ресурсов.
- `using` управляет ресурсами, но не обрабатывает исключения.

**Статус: 🟢 Понимаю.** Подтверждены оба назначения `using`, момент освобождения ресурсов, поведение при исключении и связь с `IDisposable`. Самостоятельная практика для 🔵 пока не подтверждена.
