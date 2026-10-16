# Namespaces

A namespace gives a type a logical name independent of its physical source file path.

```csharp
namespace Shop { public class Product { } }
namespace Warehouse { public class Product { } }
```

These are different types: `Shop.Product` and `Warehouse.Product`. When both namespaces are imported, an unqualified `Product` can be ambiguous (CS0104). Use a fully qualified name such as `Shop.Product` or an alias.

A file in `MyProject/Services/Calculator.cs` can declare `namespace Mathematics;`. The type's full name is then `Mathematics.Calculator`, not `MyProject.Services.Calculator`. Matching namespaces to directories is a useful convention, not a C# requirement.

`using Shop;` lets code refer to available types by short name. Namespaces organize type names but do not enforce architectural boundaries: separate projects and dependency rules serve that purpose.

## Mental model

- Directory: physical file location.
- Namespace: logical type naming.
- Fully qualified name: identifies a type without ambiguity.
- Using directive: allows short names when unambiguous.
- Folder/namespace alignment: convention rather than language rule.

**Status: 🟢 Understands.** Reasoning about duplicate class names, name ambiguity and directory independence was demonstrated. Independent practical use for 🔵 has not been verified.
