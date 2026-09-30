# OOP — Object-Oriented Programming

> Объектно-ориентированное программирование изучаем не как четыре определения для собеседования, а как способ распределять состояние, поведение и ответственность между объектами.

## В этот раздел войдут

- Class & Object
- Constructor
- Field & Property
- Access Modifiers
- Encapsulation
- Abstraction
- Inheritance
- Composition
- Polymorphism
- Interface
- Abstract Class
- Composition vs Inheritance

## Ключевой вопрос

При разборе любого объекта будем спрашивать:

> **Какое состояние принадлежит этому объекту и кто имеет право его изменять?**

И дальше связывать OOP с:

```text
encapsulation
     ↓
invariants
     ↓
high cohesion
     ↓
low coupling
     ↓
maintainable design
```

Статус блока: 🟢 базовая OOP-модель подтверждена в учебном диалоге. 🔵 не установлен: самостоятельное практическое применение по текущим правилам ещё не подтверждено.


---

## Подтверждённая mental model

Ниже сохранены только формулировки и связи, которые были продемонстрированы в учебном диалоге.

### Class, object, state, field, property, constructor

> Класс можно воспринимать как собственный тип/шаблон, от которого создаются отдельные objects. Каждый `new` создаёт новый object; присваивание одной reference-переменной другой новый object не создаёт.

> Field хранит часть состояния конкретного object. Property — другой member, через который можно читать или контролировать изменение значения.

> Constructor выполняется при создании object через `new` и задаёт начальное состояние. Если parameter имеет то же имя, что и field, `this.field = field` разделяет field текущего object и parameter.

### Encapsulation и access modifiers

> Инкапсуляция — object защищает внутреннее состояние и даёт контролируемые способы работать с ним вместо произвольного изменения снаружи.

> `private` ограничивает прямой доступ объявившим типом; `protected` добавляет доступ наследникам; `internal` ограничен assembly; `public` доступен внешнему коду. Access modifiers отвечают за доступ, а не заменяют саму инкапсуляцию.

### Abstraction

> Абстракция позволяет на текущем уровне не думать о ненужных деталях реализации. Сложность внутри не исчезает; вызывающий код просто работает через более понятную операцию.

> `private` сам по себе не является абстракцией.

### Inheritance

> Inheritance — связь IS-A. При `new Dog()` создаётся один `Dog` object, а не отдельные `Dog` и `Animal`.

> В `Animal animal = new Dog();` variable имеет тип `Animal`, а реальный object остаётся `Dog`. Тип variable определяет, какие members доступны через reference.

> Base constructor выполняется до тела constructor производного класса; `protected` доступен наследнику, `private` напрямую недоступен.

### Composition и delegation

> Composition — связь HAS-A: object хранит reference на другой object и использует его как component.

> Если один component передать двум objects, они работают с одним реальным object; если создать два component через два `new`, их состояние раздельно.

> `Motor HAS-A Battery`, `Robot HAS-A Motor`: Robot делегирует работу Motor, а Motor — Battery.

### Polymorphism

> Если базовая reference ведёт на производный object и метод переопределён через `override`, выполняется реализация реального object.

> Общий parameter базового типа позволяет одному коду работать с разными наследниками без отдельных методов для каждого concrete type.

> `new` скрывает member и не заменяет polymorphic `override`.

### Interface

> Class, который реализует interface, принимает его contract и обязан реализовать обязательные members.

> Один object может реализовывать несколько interfaces; через reference каждого interface type доступны members соответствующего contract.

> Зависимость через interface создаёт непрямую связь между тем, кому нужна работа, и concrete implementation.

### Abstract class

> Abstract class нельзя создать напрямую, но его можно использовать как базовый тип для concrete object.

> Abstract class может дать общее состояние и готовое поведение, а abstract member оставить как обязанность наследнику. Abstract-наследник может передать обязанность дальше; concrete class уже должен её реализовать.

> Для родственных типов abstract class удобен как общий фундамент, а interface — как отдельный contract способности.

### Composition vs inheritance

> Inheritance выбирается для естественного IS-A. Composition — для HAS-A.

> Composition удобнее, когда поведение нужно уметь заменять через component вместо перестройки иерархии inheritance.

> Если «Robot наследует Movement» звучит неестественно, а «Robot имеет способ движения» — естественно, это сигнал в пользу composition.

## Статус практики

В этом диалоге были prediction-задачи, разбор кода и исправление ошибок, но не было достаточно самостоятельной практической задачи или реального кода, который по текущему `STUDY_SYSTEM.md` подтверждал бы 🔵. Поэтому весь блок сохранён на уровне 🟢 без 🔵.
