# Variables, Values, Parameters & Scope

Статус: 🟢 базовая модель закреплена

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

Следующая тема должна объяснить, что меняется при `ref`, а затем — почему при **reference types** обычная передача по значению иногда выглядит так, будто метод изменил данные снаружи.
