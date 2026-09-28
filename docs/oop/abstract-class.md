# Abstract Class / Абстрактный класс

## Главная идея

**Abstract class / абстрактный класс** — это базовый класс, который может задавать общий фундамент для родственных типов, но объект самого абстрактного класса напрямую создать нельзя.

```csharp
abstract class Animal
{
}
```

Так можно:

```csharp
Animal animal = new Dog();
```

Но так нельзя:

```csharp
Animal animal = new Animal(); // compile error
```

Абстрактный класс можно использовать как тип переменной, но нельзя создавать через `new` напрямую.

## Один объект, базовая reference

```csharp
abstract class Animal
{
}

class Dog : Animal
{
}

Animal animal = new Dog();
```

Здесь создаётся один объект реального типа `Dog`.

```text
variable type → Animal
actual object → Dog
```

Переменная `animal` хранит ссылку на объект `Dog`.

## Abstract method

Абстрактный класс может объявить метод без готовой реализации:

```csharp
abstract class Animal
{
    public abstract void MakeSound();
}
```

Такой метод создаёт обязанность для конкретных наследников.

```csharp
class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof");
    }
}
```

Конкретный класс должен закрыть оставшиеся abstract members через `override`.

## Передача обязанности дальше

Абстрактный наследник может не реализовывать abstract member и передать эту обязанность следующему наследнику.

```csharp
abstract class Animal
{
    public abstract void MakeSound();
}

abstract class Mammal : Animal
{
}

class Dog : Mammal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof");
    }
}
```

`Mammal` может не реализовывать `MakeSound()`, потому что сам остаётся `abstract`.

Но как только появляется concrete / конкретный класс, он обязан реализовать все оставшиеся abstract members.

## Готовая логика и общая обязанность

Абстрактный класс может одновременно содержать:

- fields / поля;
- properties / свойства;
- constructor;
- готовые методы;
- abstract methods;
- access modifiers.

```csharp
abstract class Vehicle
{
    protected string _name;

    protected Vehicle(string name)
    {
        _name = name;
    }

    public void ShowName()
    {
        Console.WriteLine(_name);
    }

    public abstract void Move();
}
```

Наследники получают общий фундамент:

```csharp
class Car : Vehicle
{
    public Car(string name) : base(name)
    {
    }

    public override void Move()
    {
        Console.WriteLine("Car drives");
    }
}

class Boat : Vehicle
{
    public Boat(string name) : base(name)
    {
    }

    public override void Move()
    {
        Console.WriteLine("Boat sails");
    }
}
```

Использование:

```csharp
Vehicle vehicle1 = new Car("BMW");
Vehicle vehicle2 = new Boat("Boat");

vehicle1.ShowName();
vehicle1.Move();

vehicle2.ShowName();
vehicle2.Move();
```

`ShowName()` уже реализован один раз в `Vehicle`, а `Move()` каждый concrete class реализует сам.

## Constructor и base(...)

Абстрактный класс может иметь constructor:

```csharp
abstract class Person
{
    public string Name { get; }

    protected Person(string name)
    {
        Name = name;
    }
}
```

Наследник вызывает его через `base(...)`:

```csharp
class Programmer : Person
{
    public Programmer(string name) : base(name)
    {
    }
}
```

Несмотря на то, что объект `Person` напрямую создать нельзя, его constructor участвует в создании конкретного наследника.

## Abstract class и interface вместе

Абстрактный класс может реализовывать interface:

```csharp
interface IWorker
{
    void Work();
}

abstract class Person : IWorker
{
    public abstract void Work();
}
```

Абстрактный класс принимает контракт, но может передать обязанность реализации concrete-наследнику:

```csharp
class Programmer : Person
{
    public override void Work()
    {
        Console.WriteLine("Writing code");
    }
}
```

Цепочка:

```text
IWorker
   ↓ contract
Person (abstract)
   ↓ передаёт обязанность
Programmer (concrete)
   ↓ реализует
Work()
```

## Abstract class и Interface

Полезная mental model:

```text
Abstract class
→ общий фундамент родственных типов
→ может хранить состояние
→ может иметь constructor
→ может содержать готовую реализацию
→ может содержать abstract-обязанности

Interface
→ contract способности / поведения
→ разные классы могут быть не родственниками
→ класс может реализовать несколько interfaces
```

Например:

```text
Dog IS-A Animal
Bird IS-A Animal

Bird implements IFlyable
Airplane implements IFlyable
```

Абстрактный класс и interface не конкурируют обязательно друг с другом. Они часто используются вместе для разных задач.

## Итог

Ключевые идеи:

- abstract class можно использовать как тип, но нельзя создать напрямую через `new`;
- abstract class может содержать состояние, constructor и готовую логику;
- abstract method задаёт обязательство для concrete-наследника;
- abstract-наследник может передать обязанность дальше;
- concrete class обязан реализовать все оставшиеся abstract members;
- `override` закрывает abstract-обязанность;
- abstract class подходит для общего фундамента родственных типов;
- interface удобен для отдельного contract поведения;
- оба механизма могут использоваться одновременно.
