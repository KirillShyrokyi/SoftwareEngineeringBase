# Interface / Интерфейс

## Главная идея

**Interface / интерфейс** описывает контракт: какие возможности объект обязан предоставить.

```csharp
interface IFlyable
{
    void Fly();
}
```

Класс, который реализует интерфейс, обязан выполнить его контракт:

```csharp
class Bird : IFlyable
{
    public void Fly()
    {
        Console.WriteLine("Bird flies");
    }
}
```

## Implements

Запись:

```csharp
class Bird : IFlyable
```

означает, что `Bird` **implements / реализует** интерфейс `IFlyable`.

Интерфейс не делает `Bird` наследником готовой реализации. Он задаёт обязательства: если класс реализует `IFlyable`, у него должен быть требуемый член `Fly()`.

## Interface reference

```csharp
IFlyable flyingThing = new Bird();
```

Здесь:

- тип переменной — `IFlyable`;
- реальный объект — `Bird`;
- переменная хранит ссылку на объект `Bird`.

Через ссылку типа `IFlyable` доступны только члены, объявленные в контракте `IFlyable`.

```csharp
flyingThing.Fly();  // OK
```

Если у `Bird` есть дополнительный метод, которого нет в `IFlyable`, через эту ссылку он не виден.

## Один объект — несколько интерфейсных ссылок

Класс может реализовать несколько интерфейсов:

```csharp
interface IFlyable
{
    void Fly();
}

interface ISwimmable
{
    void Swim();
}

class Duck : IFlyable, ISwimmable
{
    public void Fly()
    {
        Console.WriteLine("Duck flies");
    }

    public void Swim()
    {
        Console.WriteLine("Duck swims");
    }
}
```

Один объект можно хранить через разные контрактные ссылки:

```csharp
Duck duck = new Duck();

IFlyable flyingThing = duck;
ISwimmable swimmingThing = duck;
```

Создан один объект `Duck`, но на него указывают три ссылки:

```text
duck ───────────┐
flyingThing ────┼──→ Duck #1
swimmingThing ──┘
```

При этом тип ссылки определяет, какие члены доступны:

```csharp
flyingThing.Fly();      // OK
// flyingThing.Swim();  // compile error

swimmingThing.Swim();   // OK
// swimmingThing.Fly(); // compile error
```

## Все обязательные members должны быть реализованы

Если interface требует несколько members:

```csharp
interface IWorker
{
    void Work();
    void Rest();
}
```

то класс должен реализовать их все:

```csharp
class Programmer : IWorker
{
    public void Work()
    {
    }

    public void Rest()
    {
    }
}
```

Если обязательный member отсутствует, класс не выполняет контракт и код не компилируется.

## Один метод может реализовать два интерфейса

Если два интерфейса требуют метод с одинаковой сигнатурой:

```csharp
interface IReader
{
    void Open();
}

interface IWriter
{
    void Open();
}
```

одного обычного метода достаточно:

```csharp
class Command : IReader, IWriter
{
    public void Open()
    {
        Console.WriteLine("Open");
    }
}
```

Этот метод одновременно выполняет оба контракта.

## Explicit interface implementation

Если одинаковые сигнатуры должны иметь разное поведение, можно реализовать их явно:

```csharp
interface ILeft
{
    void Move();
}

interface IRight
{
    void Move();
}

class Robot : ILeft, IRight
{
    void ILeft.Move()
    {
        Console.WriteLine("Move left");
    }

    void IRight.Move()
    {
        Console.WriteLine("Move right");
    }
}
```

Тогда нужная реализация выбирается через соответствующий interface type.

## Interface как dependency

Класс может зависеть от контракта вместо конкретной реализации:

```csharp
interface IProcessor
{
    void Calculate();
}

class FastProcessor : IProcessor
{
    public void Calculate()
    {
        Console.WriteLine("Fast calculation");
    }
}

class SimpleProcessor : IProcessor
{
    public void Calculate()
    {
        Console.WriteLine("Simple calculation");
    }
}

class Computer
{
    private IProcessor _processor;

    public Computer(IProcessor processor)
    {
        _processor = processor;
    }

    public void Run()
    {
        _processor.Calculate();
    }
}
```

Поле `_processor` хранит ссылку на объект, который реализует `IProcessor`.

```text
Computer
   ↓
IProcessor
   ↑
   ├── FastProcessor
   └── SimpleProcessor
```

`Computer` знает только контракт и не обязан знать детали конкретной реализации.

## Связь с polymorphism

```csharp
IProcessor processor = new FastProcessor();
```

Модель знакома по полиморфизму:

```text
declared type → IProcessor
actual object → FastProcessor
```

Через ссылку доступны члены контракта `IProcessor`, а реальный объект предоставляет конкретное поведение.

## Связь с composition и encapsulation

Если объект хранит интерфейсную зависимость в поле, одновременно работают несколько идей:

```text
Composition
Computer HAS-A IProcessor

Interface
IProcessor задаёт contract

Polymorphism
разные реализации можно использовать через один тип

Encapsulation
Computer не обязан знать внутренние детали реализации
```

## Итог

Ключевые идеи:

- interface задаёт контракт поведения;
- класс, реализующий interface, обязан реализовать все обязательные members;
- один класс может реализовать несколько interfaces;
- interface reference хранит ссылку на реальный объект;
- тип ссылки определяет доступные через неё members;
- один объект можно рассматривать через несколько интерфейсных контрактов;
- одинаковые сигнатуры могут быть реализованы одним методом;
- для разного поведения можно использовать explicit interface implementation;
- зависимость от interface позволяет коду работать с разными реализациями без изменения самого зависимого класса.
