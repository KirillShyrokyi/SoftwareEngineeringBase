# Access Modifiers

> Save Point: базовые модификаторы доступа в C# и их связь с инкапсуляцией.

## Главная идея

**Access modifier** определяет, какой код имеет право обращаться к типу или его члену.

На текущем уровне полезна такая модель:

| Modifier | Базовая граница доступа |
| --- | --- |
| `private` | внутри самого типа |
| `protected` | внутри типа и в производных типах |
| `internal` | внутри той же assembly |
| `public` | доступен внешнему коду, если доступен содержащий тип |

> Для `internal` проект — удобное приближение, но точная граница в C# — **assembly**. Обычно отдельный C# project компилируется в отдельную assembly.

## `private`

`private` скрывает член от обычного внешнего кода.

```csharp
public class BankAccount
{
    private decimal _balance;
}
```

`_balance` является частью внутреннего состояния `BankAccount`. Код самого типа может с ним работать, а внешний код — нет.

Не нужно смешивать `private` со **scope** локальной переменной. Локальная переменная внутри метода имеет область видимости, но не объявляется как `private`.

## `public`

`public` открывает член для внешнего использования.

```csharp
public class BankAccount
{
    public void Deposit(decimal amount)
    {
    }
}
```

Однако `public` не означает автоматически «всё можно читать и менять». У разных аксессоров свойства могут быть разные ограничения:

```csharp
public decimal Balance { get; private set; }
```

Внешний код может прочитать `Balance`, но не может вызвать его setter.

## `protected`

`protected` означает «защищённый»: член закрыт от обычного внешнего доступа, но доступен производным типам.

```csharp
public class Animal
{
    protected int age;
}

public class Dog : Animal
{
    public void ShowAge()
    {
        Console.WriteLine(age);
    }
}
```

Подробно наследование будет разобрано отдельно. Пока достаточно помнить границу доступа: **сам тип + наследники**.

## `internal`

`internal` ограничивает доступ текущей assembly.

```csharp
internal class InternalCalculator
{
}
```

Другой код той же assembly может использовать `InternalCalculator`. Код другой assembly обычно не может обращаться к нему напрямую.

## Access modifiers и инкапсуляция

Модификаторы доступа отвечают на вопрос:

> **Кто имеет доступ?**

**Encapsulation / инкапсуляция** — более широкая идея: объект защищает внутреннее состояние и предоставляет контролируемые способы работы с ним.

```csharp
public class BankAccount
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

Внешний код может вызвать `Deposit()`, но не может произвольно присвоить новое значение `Balance`. Сам объект контролирует изменение своего состояния.

Вспомогательный механизм также можно скрыть:

```csharp
public class BankAccount
{
    private decimal _balance;

    private bool IsValidAmount(decimal amount)
    {
        return amount > 0;
    }

    public void Deposit(decimal amount)
    {
        if (!IsValidAmount(amount))
        {
            return;
        }

        _balance += amount;
    }
}
```

Внешнему коду достаточно знать публичное поведение `Deposit()`. Детали проверки остаются внутри объекта.

## Mental model

```text
private
└── сам тип

protected
└── сам тип + наследники

internal
└── текущая assembly

public
└── внешний доступ
```

Модификаторы доступа создают технические границы, а инкапсуляция использует эти границы для защиты состояния и сокрытия ненужных деталей реализации.

## Проверка понимания

Для члена базового класса:

- `private` не становится доступным наследнику просто из-за наследования;
- `protected` доступен наследнику;
- `internal` зависит от границы assembly;
- `public` доступен внешнему коду;
- `public ... { get; private set; }` позволяет внешнему коду читать свойство, но не использовать его setter.
