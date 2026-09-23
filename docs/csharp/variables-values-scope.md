# Variables, Values, Parameters & Scope

Статус: 🟢 базовая модель закреплена: values, parameters, scope, `ref` и reference types

Эта тема описывает поток данных в простых C#-методах на примерах с `int`.

## Variable, Type, Value

```csharp
int age = 20;
```

Здесь:

- `int` — **type**, тип данных;
- `age` — **variable**, переменная;
- `20` — **value**, значение.

Полезно разделять переменную и значение:

```text
variable: age
    │
    ▼
value: 20

type: int
```

Имя переменной — не само значение. Переменная хранит значение определённого типа.

## Argument и Parameter

```csharp
void Change(int value)
{
    value = 100;
}

int age = 20;
Change(age);
```

В объявлении метода:

```csharp
int value
```

`value` — **parameter**, параметр метода.

При вызове:

```csharp
Change(age);
```

`age` — **argument**, аргумент вызова.

Модель:

```text
argument expression
        ↓
     evaluate
        ↓
      value
        ↓
    parameter
```

Для обычного параметра `int` значение передаётся **by value**: параметр получает копию значения аргумента.

```text
age                   value
┌────┐                 ┌────┐
│ 20 │ ── copy 20 ──► │ 20 │
└────┘                 └────┘
```

Поэтому изменение параметра:

```csharp
value = 100;
```

не изменяет `age`:

```text
age                   value
┌────┐                 ┌─────┐
│ 20 │                 │ 100 │
└────┘                 └─────┘
```

После завершения вызова локальный параметр `value` больше недоступен, а `age` остаётся равным `20`.

## Argument — не обязательно переменная

Метод может получить результат любого подходящего выражения:

```csharp
Change(age);
Change(10);
Change(age + 5);
```

Сначала C# вычисляет argument expression, затем полученное значение передаётся параметру.

Например:

```text
age + 5
20 + 5
  ↓
 25
  ↓
parameter receives 25
```

## Scope — область видимости

**Scope** определяет, где имя переменной доступно в коде.

Удобная mental model — вложенные коробки:

```text
outer scope
┌──────────────────────────────┐
│ int number = 10;             │
│                              │
│   if scope                   │
│   ┌──────────────────────┐   │
│   │ int bonus = 5;       │   │
│   │ number доступна      │   │
│   │ bonus доступна       │   │
│   └──────────────────────┘   │
│                              │
│ number доступна              │
│ bonus недоступна             │
└──────────────────────────────┘
```

Переменная внешней области обычно доступна во вложенной области. Переменная, объявленная во внутреннем блоке, снаружи этого блока недоступна.

Если обратиться к локальной переменной вне её scope, это **ошибка компиляции**, а не ошибка во время выполнения программы.

## Sibling scopes — соседние области

Два соседних блока могут иметь свои локальные переменные с одинаковым именем:

```csharp
if (true)
{
    int value = 10;
}

if (true)
{
    int value = 20;
}
```

Это две разные переменные в двух разных sibling scopes.

При этом локальные имена во вложенной и содержащей области имеют дополнительные ограничения C#, поэтому нельзя считать правило «scope закончился — имя всегда можно объявить заново» универсальным.

## Одинаковое имя не означает одну переменную

```csharp
int number = 10;

void AddFive(int number)
{
    number = number + 5;
    Console.WriteLine(number);
}

AddFive(number);
Console.WriteLine(number);
```

Здесь две разные переменные `number`:

- внешняя `number` содержит `10`;
- параметр метода `number` получает копию значения `10`, затем становится `15`.

Вывод:

```text
15
10
```

Имя само по себе не определяет переменную — важно, **где это имя объявлено**.

## Return возвращает значение

```csharp
int AddFive(int value)
{
    value = value + 5;
    return value;
}
```

`return` не делает локальную переменную доступной снаружи. Он возвращает **значение**.

```text
value = 15
    ↓
return value
    ↓
return 15
```

Локальная переменная `value` остаётся частью метода.

### Результат нужно использовать

```csharp
int number = 10;
AddFive(number);
```

Метод вернёт `15`, но результат нигде не сохраняется. `number` останется `10`.

Чтобы сохранить результат:

```csharp
number = AddFive(number);
```

Теперь поток такой:

```text
number = 10
    ↓
AddFive(10)
    ↓
return 15
    ↓
number = 15
```

## Return может возвращать expression

Необязательно сначала изменять локальную переменную:

```csharp
int Double(int value)
{
    return value * 2;
}
```

`value * 2` — **expression**, выражение. C# вычисляет его и возвращает полученное значение.

```text
value = 10
    ↓
value * 2
    ↓
20
    ↓
return 20
```

## Методы можно соединять в цепочку вычислений

```csharp
int Double(int value)
{
    return value * 2;
}

int AddOne(int value)
{
    return value + 1;
}

int number = 5;
number = AddOne(Double(number));
```

Вложенное выражение вычисляется изнутри наружу:

```text
Double(5)
   ↓
10
   ↓
AddOne(10)
   ↓
11
   ↓
number = 11
```

Результат одного метода может стать аргументом другого метода.

Если написать только:

```csharp
AddOne(Double(number));
```

оба метода выполнятся и значение `11` будет вычислено, но оно не будет сохранено. Исходная `number` останется прежней.


## `ref` — alias внешней переменной

Обычный параметр получает копию значения:

```csharp
int number = 10;

void Change(int value)
{
    value = 100;
}

Change(number);
```

Здесь есть две независимые переменные:

```text
number = 10
value  = 10   ← copy
```

Поэтому изменение `value` не меняет `number`.

С `ref` модель другая:

```csharp
int number = 10;

void Change(ref int value)
{
    value = 100;
}

Change(ref number);
```

Параметр `value` становится **alias** внешней переменной `number`:

```text
number ───┐
          ▼
        [ 10 ]
          ▲
value ────┘
```

После `value = 100` изменяется то же самое хранилище, поэтому `number == 100`.

`ref` пишется и в объявлении метода, и в вызове:

```csharp
void Change(ref int value)
Change(ref number);
```

Это делает передачу by reference явной с обеих сторон.

Для `ref` нужна переменная, к которой можно привязать alias:

```csharp
Change(ref number);     // можно
Change(ref 10);         // нельзя
Change(ref number + 5); // нельзя
```

`10` и результат `number + 5` — значения выражений, а не подходящие переменные-хранилища для такого вызова.

### Swap показывает разницу особенно хорошо

```csharp
void Swap(ref int a, ref int b)
{
    int temp = a;
    a = b;
    b = temp;
}
```

`a` и `b` — aliases внешних переменных, а `temp` — обычная локальная `int`-переменная, которая получает копию текущего значения `a`.

## Class, object, variable и reference

C# содержит готовые типы вроде `int`, `string`, `bool`, а класс позволяет описать свой тип:

```csharp
class Player
{
    public string Name { get; set; }
    public int Health { get; set; }
}
```

Здесь:

- `Player` — class/type;
- `Name` и `Health` — properties;
- `new Player()` — создание нового object/instance типа `Player`.

```csharp
Player player = new Player();
```

Эта строка совмещает три шага:

```text
Player player
    ↓
объявить переменную player типа Player

new Player()
    ↓
создать новый object

=
    ↓
записать reference на object в player
```

Имя `player` принадлежит переменной, а не объекту.

Каждый отдельный `new Player()` создаёт новый объект:

```csharp
Player first = new Player();
Player second = new Player();
```

```text
first  ───► object #1
second ───► object #2
```

Но присваивание одной reference-type переменной другой не копирует сам объект:

```csharp
Player first = new Player();
Player second = first;
```

Копируется значение-reference:

```text
first  ─┐
        ├──► object #1
second ─┘
```

Поэтому изменение объекта через любую из этих переменных видно через другую:

```csharp
first.Name = "Knight";
second.Name = "Mage";

Console.WriteLine(first.Name); // Mage
```

## Изменить object и изменить reference — разные операции

Это ключевое различие:

```csharp
player.Name = "Mage";
```

меняет **object**, на который ведёт `player`.

А:

```csharp
player = new Player();
```

создаёт новый object и меняет **reference, хранящийся в переменной `player`**.

Например:

```csharp
Player first = new Player();
first.Name = "One";

Player second = first;

first = new Player();
first.Name = "Two";
```

После этого:

```text
first  ───► object #2, Name = "Two"
second ───► object #1, Name = "One"
```

Переменных две, объектов тоже два.

## Reference type parameter всё ещё передаётся by value

Рассмотрим обычный параметр типа `Player`:

```csharp
void Damage(Player target)
{
    target.Health = target.Health - 20;
}
```

Если вызвать:

```csharp
Damage(player);
```

в `target` копируется не весь object, а **значение-reference**, которое хранится в `player`:

```text
player ─┐
        ├──► один object
target ─┘
```

Поэтому:

```csharp
target.Health = 80;
```

изменяет общий object, и изменение видно через `player`.

Но если внутри обычного параметра сделать:

```csharp
target = new Player();
```

изменится только локальная переменная `target`:

```text
player ───► object #1
target ───► object #2
```

Внешняя переменная `player` продолжит хранить старый reference.

Именно поэтому обычный reference-type parameter всё ещё является передачей **by value**: копируется value, просто этим value является reference.

## `ref Player` позволяет заменить reference внешней переменной

Теперь добавим `ref`:

```csharp
void Replace(ref Player target)
{
    target = new Player();
    target.Name = "Mage";
}
```

Вызов:

```csharp
Replace(ref player);
```

делает `target` alias самой переменной `player`.

Поэтому:

```csharp
target = new Player();
```

уже меняет reference во внешней переменной `player`.

Итоговая разница:

```text
Player target
    ↓
копия reference
    ↓
можно менять общий object
но присваивание target = ... не меняет внешнюю переменную


ref Player target
    ↓
alias внешней Player-переменной
    ↓
можно менять и object,
и сам reference внешней переменной
```

## Маленькая синтаксическая деталь: `-=`

```csharp
player.Health -= 30;
```

эквивалентно:

```csharp
player.Health = player.Health - 30;
```

Это сокращённая assignment operation: взять текущее значение, вычесть `30` и записать результат обратно.


## Mental model

```text
variable
   ↓ stores
value
   ↓ has
 type

argument expression
   ↓ evaluate
value
   ↓ copy for ordinary int parameter
parameter
   ↓ method calculation
return expression
   ↓ evaluate
returned value
   ↓
caller may store / pass / ignore it
```

И отдельно:

```text
outer scope
   ↓ visible inside
inner scope

inner local variable
   X
not visible outside its scope
```

## Следующий шаг

Базовая модель `ref` и reference types закреплена. Следующий шаг — перейти к более системному устройству классов и объектов: fields, properties, constructors, encapsulation и поведению экземпляров.
