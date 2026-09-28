# Composition vs Inheritance / Композиция и наследование

## Главная идея

И **inheritance / наследование**, и **composition / композиция** позволяют повторно использовать поведение, но описывают разные отношения.

```text
Inheritance
→ IS-A
→ один тип является более конкретным видом другого

Composition
→ HAS-A
→ один объект использует другой объект как компонент
```

## Inheritance: IS-A

```csharp
class Animal
{
}

class Dog : Animal
{
}
```

Здесь:

```text
Dog IS-A Animal
```

`Dog` является более конкретным типом `Animal`.

Наследование подходит, когда отношение между типами действительно естественно описывается через **IS-A**.

Примеры:

```text
Dog IS-A Animal
Car IS-A Vehicle
Manager IS-A Employee
```

## Composition: HAS-A

```csharp
class Engine
{
}

class Car
{
    private Engine _engine;

    public Car(Engine engine)
    {
        _engine = engine;
    }
}
```

Здесь:

```text
Car HAS-A Engine
```

`Car` не является `Engine`. Он хранит ссылку на другой объект и использует его как компонент.

Примеры:

```text
Car HAS-A Engine
Computer HAS-A Storage
Smartphone HAS-A Camera
User HAS-A Role
```

## Поведение через inheritance

При наследовании поведение становится доступно через иерархию типов.

```csharp
class Animal
{
    public void Eat()
    {
        Console.WriteLine("Eating");
    }
}

class Dog : Animal
{
}
```

`Dog` может использовать `Eat()`, потому что является `Animal`.

## Поведение через composition

При композиции объект использует поведение другого объекта через reference и delegation.

```csharp
class Engine
{
    public void Start()
    {
        Console.WriteLine("Engine started");
    }
}

class Car
{
    private Engine _engine;

    public Car(Engine engine)
    {
        _engine = engine;
    }

    public void Start()
    {
        _engine.Start();
    }
}
```

`Car` не получает поведение двигателя через наследование. Он делегирует работу своему компоненту `Engine`.

## Когда inheritance выглядит искусственно

Технически можно написать код, который компилируется, но плохо описывает отношение:

```csharp
class Car : Engine
{
}
```

Такой код говорит:

```text
Car IS-A Engine
```

Но машина не является двигателем. Поэтому здесь естественнее composition:

```text
Car HAS-A Engine
```

## Favor composition over inheritance

Полезная рекомендация:

> **Favor composition over inheritance** — предпочитай композицию наследованию, когда задача естественно описывается через HAS-A и поведение можно подключить как компонент.

Это не означает, что inheritance плохой или composition всегда лучше.

Inheritance остаётся хорошим выбором, когда есть настоящее отношение **IS-A**.

Composition часто гибче, потому что поведение можно заменить через другой объект без изменения иерархии типов.

## Composition + Interface

Особенно гибкой композиция становится вместе с interface.

```csharp
interface IMovement
{
    void Move();
}

class WheelsMovement : IMovement
{
    public void Move()
    {
        Console.WriteLine("Moves on wheels");
    }
}

class LegsMovement : IMovement
{
    public void Move()
    {
        Console.WriteLine("Walks on legs");
    }
}

class Robot
{
    private IMovement _movement;

    public Robot(IMovement movement)
    {
        _movement = movement;
    }

    public void Move()
    {
        _movement.Move();
    }
}
```

Теперь:

```csharp
Robot robot1 = new Robot(new WheelsMovement());
Robot robot2 = new Robot(new LegsMovement());
```

Сам `Robot` остаётся тем же типом, но его способ движения определяется переданным компонентом.

```text
Robot HAS-A IMovement
```

## Связь между типами и объектами

Полезная mental model:

```text
Inheritance
→ связывает типы
→ "я являюсь этим типом"

Composition
→ связывает объекты
→ "у меня есть этот объект"
→ "я использую его поведение"
```

## Проверка выбора

Для выбора механизма полезно сначала задать вопрос:

```text
Это действительно IS-A?
```

Если да, inheritance может быть естественным.

Если отношение скорее звучит как:

```text
объект HAS-A другой объект
```

то composition обычно подходит лучше.

Примеры:

```text
Dog IS-A Animal              → inheritance
Car HAS-A Engine             → composition
Smartphone HAS-A Camera      → composition
Manager IS-A Employee        → inheritance
Computer HAS-A Storage       → composition
User HAS-A Role              → composition
PaymentService HAS-A Logger  → composition
```

## Итог

Ключевые идеи:

- inheritance выражает отношение **IS-A**;
- composition выражает отношение **HAS-A**;
- inheritance связывает типы через иерархию;
- composition связывает объекты через references;
- composition позволяет делегировать работу компонентам;
- composition вместе с interfaces часто позволяет менять поведение без изменения основного класса;
- `favor composition over inheritance` — рекомендация, а не абсолютный запрет наследования;
- inheritance уместен, когда производный тип действительно является более конкретным видом базового типа.
