# Polymorphism / Полиморфизм

## Главная идея

**Polymorphism / полиморфизм** позволяет работать с разными объектами через общий базовый тип, а каждый реальный объект может выполнять свою реализацию поведения.

```csharp
class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Animal");
    }
}

class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Dog");
    }
}

class Cat : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Cat");
    }
}
```

Теперь:

```csharp
Animal dog = new Dog();
Animal cat = new Cat();

dog.MakeSound();
cat.MakeSound();
```

выведет:

```text
Dog
Cat
```

## Тип переменной и реальный тип объекта

```csharp
Animal animal = new Dog();
```

Здесь:

- объявленный тип переменной — `Animal`;
- реальный тип объекта — `Dog`.

Полезная модель:

```text
variable type
    ↓
можно ли вызвать этот член?

actual object type
    ↓
какая override-реализация virtual-метода выполнится?
```

Через переменную `Animal` можно вызвать только доступные члены, известные типу `Animal` и его базовым типам.

Если вызываемый метод `virtual`, а реальный объект переопределил его через `override`, во время выполнения вызывается реализация реального объекта.

## virtual и override

Базовый класс разрешает переопределение:

```csharp
public virtual void MakeSound()
{
    Console.WriteLine("Animal");
}
```

Производный класс переопределяет поведение:

```csharp
public override void MakeSound()
{
    Console.WriteLine("Dog");
}
```

Тогда:

```csharp
Animal animal = new Dog();
animal.MakeSound();
```

вызывает `Dog.MakeSound()`.

## Если override нет

```csharp
class Dog : Animal
{
}
```

При:

```csharp
Animal animal = new Dog();
animal.MakeSound();
```

будет использована ближайшая доступная реализация из базовой цепочки — в этом случае `Animal.MakeSound()`.

## Один объект и несколько references

```csharp
Dog dog = new Dog();
Animal animal = dog;
```

Второй объект не создаётся.

```text
dog ───────┐
           ▼
        Dog #1
           ▲
           │
animal ────┘
```

Если `MakeSound()` переопределён через `override`, оба вызова:

```csharp
dog.MakeSound();
animal.MakeSound();
```

выполнят реализацию `Dog`.

## override и скрытие метода

Метод с таким же именем без `override` не становится полиморфическим переопределением.

```csharp
class Dog : Animal
{
    public new void MakeSound()
    {
        Console.WriteLine("Dog");
    }
}
```

Тогда:

```csharp
Dog dog = new Dog();
Animal animal = dog;

dog.MakeSound();     // Dog
animal.MakeSound();  // Animal
```

Коротко:

```text
override
→ участвует в polymorphism
→ реализация выбирается по реальному объекту

new
→ скрывает член базового класса
→ выбор зависит от типа ссылки, через которую идёт вызов
```

## Практическая польза

Полиморфизм позволяет одному коду работать с разными производными объектами.

```csharp
static void PlaySound(Animal animal)
{
    animal.MakeSound();
}
```

Этот один метод может работать с разными объектами:

```csharp
PlaySound(new Dog());
PlaySound(new Cat());
```

Методу не нужно вручную проверять, какой именно объект ему передали. Он работает через общий тип `Animal`, а реальный объект предоставляет свою реализацию поведения.

То же самое удобно для коллекций:

```csharp
Animal[] animals =
{
    new Dog(),
    new Cat(),
    new Dog()
};

foreach (Animal animal in animals)
{
    animal.MakeSound();
}
```

Один и тот же код вызывает разные реализации поведения.

## Связь с inheritance

Полиморфизм здесь опирается на наследование:

```text
Dog IS-A Animal
Cat IS-A Animal
```

Поэтому объект `Dog` или `Cat` можно передать туда, где ожидается `Animal`.

Но главное преимущество не просто в наследовании, а в том, что код может работать через общий тип, а разные реальные объекты выполнять своё поведение через `virtual/override`.

## Итог

**Polymorphism / полиморфизм** позволяет одинаковому коду работать с разными объектами через общий тип.

Ключевые идеи:

- базовая ссылка может хранить объект производного типа;
- тип переменной определяет, какие члены доступны для вызова;
- реальный тип объекта определяет, какая `override`-реализация виртуального метода выполнится;
- `virtual` разрешает переопределение;
- `override` подключает производную реализацию к полиморфическому вызову;
- `new` скрывает член и не заменяет `override`;
- общий параметр типа `Animal` позволяет одному методу работать с `Dog`, `Cat` и другими наследниками.
