# Composition / Композиция

## Главная идея

**Composition / композиция** описывает отношение **HAS-A**, когда один объект использует другой объект как свой компонент.

```text
Car HAS-A Engine
Computer HAS-A Processor
Motor HAS-A Battery
```

В отличие от наследования, объект не становится другим типом. Он хранит ссылку на другой объект и использует его поведение.

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

Здесь `Car` не является `Engine`. У объекта `Car` есть поле `_engine`, которое хранит ссылку на объект `Engine`.

## Поле хранит ссылку на компонент

```csharp
Engine engine = new Engine();
Car car = new Car(engine);
```

При передаче `engine` в конструктор объект `Engine` не копируется.

```csharp
public Car(Engine engine)
{
    _engine = engine;
}
```

В поле `_engine` сохраняется ссылка на тот же объект.

```text
engine ─────────┐
                ▼
             Engine #1
                ▲
                │
car._engine ────┘
```

Здесь существует два объекта:

- один `Engine`;
- один `Car`.

Но на объект `Engine` указывают две ссылки.

## Один компонент можно разделять между объектами

```csharp
Engine engine = new Engine();

Car car1 = new Car(engine);
Car car2 = new Car(engine);
```

Созданы три объекта:

```text
Car #1
Car #2
Engine #1
```

Оба объекта `Car` хранят ссылку на один и тот же `Engine`.

```text
                  ┌── car1._engine
                  │
engine ──→ Engine #1
                  │
                  └── car2._engine
```

Если состояние этого `Engine` изменится, изменение будет видно через любую ссылку на тот же объект.

## Разные компоненты — разное состояние

```csharp
Processor processor1 = new Processor();
Processor processor2 = new Processor();

Computer computer1 = new Computer(processor1);
Computer computer2 = new Computer(processor2);
```

Здесь созданы два разных объекта `Processor`.

```text
computer1 ──→ Processor #1
computer2 ──→ Processor #2
```

Изменение состояния `Processor #1` не изменяет `Processor #2`.

## Argument и parameter

```csharp
Processor processor = new Processor();
Computer computer = new Computer(processor);
```

В выражении:

```csharp
new Computer(processor)
```

`processor` — **argument / аргумент**.

В конструкторе:

```csharp
public Computer(Processor processor)
```

`processor` — **parameter / параметр**.

Параметр получает переданную ссылку, а затем эта ссылка может быть сохранена в поле объекта.

## Delegation / Делегирование

Композиция часто используется вместе с **delegation / делегированием**.

```csharp
class Computer
{
    private Processor _processor;

    public Computer(Processor processor)
    {
        _processor = processor;
    }

    public void RunProgram()
    {
        _processor.Calculate();
    }
}
```

Когда вызывается:

```csharp
computer.RunProgram();
```

цепочка выглядит так:

```text
Computer.RunProgram()
        ↓
_processor.Calculate()
        ↓
Processor.Calculate()
```

`Computer` не выполняет вычисление самостоятельно. Он передаёт эту ответственность своему компоненту `Processor`.

## Цепочка композиции

Объекты могут состоять из других объектов, которые сами используют свои компоненты.

```csharp
class Battery
{
    public void SupplyPower()
    {
        Console.WriteLine("Power supplied");
    }
}

class Processor
{
    private Battery _battery;

    public Processor(Battery battery)
    {
        _battery = battery;
    }

    public void Calculate()
    {
        _battery.SupplyPower();
        Console.WriteLine("Calculating...");
    }
}

class Computer
{
    private Processor _processor;

    public Computer(Processor processor)
    {
        _processor = processor;
    }

    public void RunProgram()
    {
        _processor.Calculate();
    }
}
```

Отношения:

```text
Computer HAS-A Processor
Processor HAS-A Battery
```

При вызове:

```csharp
computer.RunProgram();
```

работа делегируется по цепочке:

```text
Computer
   ↓ delegates
Processor
   ↓ delegates
Battery
```

## Composition и encapsulation

Композиция хорошо сочетается с **encapsulation / инкапсуляцией**.

Внешнему коду не обязательно знать, какие компоненты находятся внутри объекта и какие действия они выполняют.

```csharp
car.Start();
```

Внутри `Car` этот вызов может делегироваться нескольким компонентам:

```csharp
public void Start()
{
    _engine.Start();
    _lights.TurnOn();
}
```

Внешний код работает с понятным поведением `Car`, а детали взаимодействия компонентов остаются внутри.

## Composition и inheritance

Главное различие:

```text
Inheritance:
Dog IS-A Animal

Composition:
Car HAS-A Engine
```

При наследовании объект получает доступное поведение через отношение типов: производный тип является более конкретным видом базового типа.

При композиции один объект использует поведение другого объекта через сохранённую ссылку и делегирование.

```text
Inheritance:
я могу использовать это поведение,
потому что являюсь таким типом.

Composition:
я могу использовать это поведение,
потому что у меня есть объект,
которому я могу делегировать работу.
```

## Итог

**Composition / композиция** строит более сложное поведение объекта из других объектов-компонентов.

Ключевые идеи:

- `HAS-A` описывает отношение композиции;
- поле может хранить ссылку на объект-компонент;
- при передаче ссылочного объекта в конструктор сам объект не копируется;
- несколько объектов могут использовать один общий компонент;
- разные экземпляры компонентов имеют независимое состояние;
- **delegation** позволяет объекту передавать часть работы своим компонентам;
- композиция и **инкапсуляция** позволяют скрывать внутреннее взаимодействие компонентов за простым внешним поведением.
