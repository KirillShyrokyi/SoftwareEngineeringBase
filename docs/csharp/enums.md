# Enums

[Roadmap](../roadmap.md)

An enum defines a named type with a finite set of named members.

```csharp
enum OrderStatus
{
    Pending,
    Processing,
    Completed
}
```

`OrderStatus` is a type. `OrderStatus.Completed` is a named value. Compared with arbitrary strings, this communicates the expected set of states and prevents accidental use of undeclared member names.

An enum member does not need a matching `switch` case to be valid. The enum defines the named members; the switch selects behavior.

Numeric values start at zero by default and increment by one. An explicitly assigned number changes the starting point for subsequent implicit values:

```csharp
enum Level
{
    Low = 3,
    Medium,      // 4
    High = 10,
    Critical     // 11
}
```

These numbers are underlying values, not list indexes. An explicit cast such as `(OrderStatus)999` is allowed, even without a matching named member; `Enum.IsDefined` can check defined values.

Use enums for a small known set of states or directions, not arbitrary user names. A `List<OrderStatus>` combines a collection of elements with an enum element type.
