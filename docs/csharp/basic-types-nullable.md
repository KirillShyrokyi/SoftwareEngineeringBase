# Basic Types & Nullable Values

Статус: 🟢 Понимаю

Эта заметка сохраняет подтверждённую базовую модель простых значений C#: тип определяет допустимые значения и операции, а `null` обозначает отсутствие значения.

## Basic types

```csharp
int age = 20;
double temperature = 36.6;
decimal balance = 1250.75m;
bool isActive = true;
string name = "Alex";
DateTime createdAt = DateTime.Now;
```

- `int` — целые числа.
- `double` — числа с плавающей точкой; многие десятичные дроби представлены приближённо.
- `decimal` — десятичный тип с высокой точностью, полезный для денежных вычислений.
- `bool` — `true` или `false`.
- `string` — текст.
- `DateTime` — дата и время.

`DateTime.Now` вычисляется в момент выполнения выражения. Уже сохранённое значение не превращается в «живые часы».

## Conversion

`int → double` обычно выполняется неявно. Обратный cast может потерять дробную часть:

```csharp
int value = (int)7.9; // 7
```

Cast к `int` отбрасывает дробную часть, а не округляет её. `Math.Round(7.9)` даёт `8`.

## Integer division

```csharp
double first = 5 / 2;          // 2
double second = (double)5 / 2; // 2.5
```

В первом выражении сначала выполняется целочисленное деление, и только затем результат помещается в `double`.

## String concatenation

```csharp
"Result: " + 10 + 20    // "Result: 1020"
"Result: " + (10 + 20)  // "Result: 30"
10 + 20 + " apples"     // "30 apples"
```

Скобки и порядок вычисления определяют, где происходит арифметика, а где начинается string concatenation.

## Nullable values

`?` позволяет value type представлять отсутствие значения:

```csharp
int? number = null;
int? result = number + 10; // null
int fallback = (number ?? 3) + 10; // 13
```

`null` — не `0` и не пустая строка. Nullable-арифметика распространяет отсутствие значения. `??` задаёт явный fallback.

`HasValue` проверяет наличие значения. Обращение к `.Value` допустимо только когда значение существует; иначе возникает исключение.

## Mental model

```text
type
  ↓ constrains
value and operations

nullable value
  ↓
value OR absence of value (null)

calculation
  ↓
uses operand types first
  ↓
then result can be assigned/converted
```

Эта заметка фиксирует только понимание, продемонстрированное в учебном диалоге. Более глубокая memory model C# здесь не подразумевается.
