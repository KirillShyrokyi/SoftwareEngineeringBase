# Class, Object, State & Behavior

> Этот Save Point фиксирует базовую модель объекта в C#: объект имеет собственное состояние, создаётся через constructor и предоставляет контролируемое поведение.

## 1. Class и object

`class` описывает структуру и поведение объектов этого типа.

```csharp
class Player
{
    private int _health;
}
```

Конкретный объект появляется во время выполнения программы:

```csharp
Player a = new Player();
Player b = new Player();
```

`new Player()` создаёт новый объект. `a` и `b` — разные переменные, содержащие references на разные объекты.

Если написать:

```csharp
Player a = new Player();
Player b = a;
```

второй объект не создаётся. Обе переменные содержат reference на один объект:

```text
a ──┐
    ├──→ Player object
b ──┘
```

Поэтому изменение состояния через `b` будет видно и через `a`.

## 2. Field — часть состояния объекта

```csharp
class Player
{
    private int _health;
}
```

`_health` — **field (поле)**. Оно хранит часть состояния конкретного экземпляра `Player`.

Важно не смешивать field и local variable:

```csharp
void Test()
{
    int value = 10; // local variable
}
```

Local variable относится к выполнению метода. Instance field является частью состояния объекта и продолжает существовать вместе с объектом после завершения метода.

## 3. `this` — текущий объект

`this` удобно читать как **«этот текущий объект»**.

```csharp
class Player
{
    private int health;

    public void SetHealth(int health)
    {
        this.health = health;
    }
}
```

Здесь:

```text
this.health → field текущего объекта
health      → parameter метода
```

При `player.SetHealth(75)` текущим объектом для выполняющегося метода является `player`.

## 4. Property — контролируемый доступ

Открытое поле позволяет внешнему коду произвольно менять состояние:

```csharp
public int health;
```

Тогда возможно:

```csharp
player.health = -500;
```

Property отделяет интерфейс доступа от внутреннего хранения:

```csharp
private int _health = 100;

public int Health
{
    get
    {
        return _health;
    }

    set
    {
        if (value >= 0 && value <= 100)
        {
            _health = value;
        }
    }
}
```

- `get` возвращает значение;
- `set` обрабатывает запись;
- `value` внутри `set` — значение, которое пытаются присвоить property.

Например, в `player.Health = 75` внутри `set` значение `value` равно `75`.

### Auto-implemented property

Если дополнительная логика не нужна:

```csharp
public int Health { get; set; }
```

компилятор создаёт скрытое backing field для хранения значения.

Можно разрешить чтение снаружи, но ограничить запись:

```csharp
public int Health { get; private set; }
```

Теперь внешний код может прочитать `Health`, но изменить его может только код самого `Player`.

## 5. Constructor — начальное состояние

Constructor выполняется при создании объекта:

```csharp
class Player
{
    public string Name { get; private set; }
    public int Health { get; private set; }

    public Player(string name, int health)
    {
        Name = name;
        Health = health;
    }
}
```

При:

```csharp
Player player = new Player("Alex", 100);
```

```text
"Alex", 100
     ↓ arguments

name, health
     ↓ parameters

constructor
     ↓

Player
├── Name = "Alex"
└── Health = 100
```

Constructor signature заставляет передать необходимые аргументы, но сама по себе не гарантирует корректность значений. Например, `-500` всё ещё является корректным `int`, поэтому бизнес-ограничения проверяются отдельно.

## 6. Constructor overloading

У класса может быть несколько constructors с разными наборами параметров:

```csharp
public Player(string name)
{
    Name = name;
    Health = 100;
}

public Player(string name, int health)
{
    Name = name;
    Health = health;
}
```

C# выбирает подходящую перегрузку по сигнатуре: важны количество, типы и порядок параметров, а не их имена.

```text
(string)
(string, int)
(int, string)
```

### Constructor chaining

Один constructor может вызвать другой constructor того же класса:

```csharp
public Player(string name)
    : this(name, 100)
{
}

public Player(string name, int health)
{
    Name = name;
    Health = health;
}
```

`: this(...) ` помогает не дублировать общую логику инициализации.

## 7. State и behavior

**State (состояние)** — данные, характеризующие конкретный объект сейчас.

**Behavior (поведение)** — то, что объект умеет делать.

```csharp
class BankAccount
{
    public decimal Balance { get; private set; }

    public void Deposit(decimal amount)
    {
        if (amount <= 0)
        {
            return;
        }

        Balance += amount;
    }
}
```

Здесь `Balance` относится к состоянию, а `Deposit` — к поведению.

```text
state before
Balance = 500
     ↓
behavior
Deposit(200)
     ↓
state after
Balance = 700
```

## 8. Encapsulation — инкапсуляция

Инкапсуляция — это не просто наличие `private` или проверки в `set`.

> **Объект защищает своё внутреннее состояние и предоставляет контролируемые способы работы с ним.**

Вместо:

```csharp
account.Balance = account.Balance + 500;
```

внешний код использует предусмотренное поведение:

```csharp
account.Deposit(500);
```

Тогда сам `BankAccount` решает, как проверить операцию и изменить своё состояние.

Полезная ментальная модель:

```text
field
  ↓
хранит состояние

property
  ↓
предоставляет / контролирует доступ

constructor
  ↓
создаёт начальное состояние

method
  ↓
задаёт поведение

encapsulation
  ↓
объект контролирует своё состояние
и предусмотренные способы его изменения
```

## Контрольный пример

```csharp
class Player
{
    private int _health;

    public string Name { get; private set; }

    public int Health
    {
        get
        {
            return _health;
        }

        private set
        {
            if (value < 0)
            {
                _health = 0;
                return;
            }

            _health = value;
        }
    }

    public Player(string name, int health)
    {
        Name = name;
        Health = health;
    }

    public void TakeDamage(int damage)
    {
        if (damage <= 0)
        {
            return;
        }

        Health = Health - damage;
    }
}
```

При:

```csharp
Player player = new Player("Alex", 100);

player.TakeDamage(30);
player.TakeDamage(90);

int currentHealth = player.Health;
```

переход состояния:

```text
100
 ↓ TakeDamage(30)
70
 ↓ TakeDamage(90)
-20 пытается попасть в Health
 ↓ private set
0

currentHealth = 0
```

Внешний код не может напрямую выполнить `player.Health = ...`, потому что `set` закрыт. Изменение состояния происходит через предусмотренное поведение объекта.

## Связь понятий

```text
CLASS
│
├── STATE
│   ├── field
│   └── property
│
├── CREATION
│   └── constructor
│
└── BEHAVIOR
    └── methods

        +

ENCAPSULATION
    ↓
контролируемое состояние
и способы его изменения
```
