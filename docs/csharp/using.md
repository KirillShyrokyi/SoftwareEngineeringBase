# Using — namespaces and resource cleanup

## Two meanings

`using System.IO;` makes types from the namespace available by their short names, such as `StreamReader`. It does not create objects or import runtime resources.

`using (var reader = new StreamReader("data.txt")) { ... }` arranges for `reader.Dispose()` to be called on leaving that block, even when its body throws an exception. It does not catch that exception.

`using var reader = new StreamReader("data.txt");` instead calls `Dispose()` when the containing scope ends (for example, at the end of the method).

## Connection to finally

```csharp
var reader = new StreamReader("data.txt");
try
{
    Console.WriteLine(reader.ReadToEnd());
}
finally
{
    reader.Dispose();
}
```

This is the conceptual equivalent of the resource-managing `using` statement when construction succeeds.

## IDisposable

`IDisposable` defines the `Dispose()` contract. A class implements resource cleanup; `using` calls that cleanup automatically. C# also supports compatible pattern-based `Dispose` implementations.

```csharp
class TestResource : IDisposable
{
    public void Dispose() => Console.WriteLine("D");
}

using (var resource = new TestResource())
{
    Console.WriteLine("A");
}
Console.WriteLine("B");
```

Output: `A`, `D`, `B`.

## Mental model

- `using Namespace;` shortens type names.
- `using (...) { }` disposes after the block.
- `using var ...;` disposes at the end of the containing scope.
- `IDisposable` provides a cleanup contract.
- `using` manages resources; it does not handle exceptions.

**Status: 🟢 Understands.** The two meanings of `using`, disposal timing, exception behavior and the role of `IDisposable` have been demonstrated. Independent practice for 🔵 has not been verified.
