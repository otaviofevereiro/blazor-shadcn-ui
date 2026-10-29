---
name: csharp
description: >
  Idiomatic C# for the current language version: nullable, async + CancellationToken,
  Domain stays framework-free. Use when *.cs files are in play.
---

# C#

**When:** `*.cs` files are in play.

Write idiomatic C# for the current language version. Use the language’s own features instead of ceremony or a house dialect: file-scoped namespaces, pattern matching, collection expressions, `required`/`init`, expression-bodied members, `using` declarations. Do not invent extra style that fights the language.

- Nullable enabled. Do not silence with `!` unless a framework annotation is wrong (comment why).
- `record` for immutable values/DTOs; `class` for entities with identity.
- I/O methods: `Async` suffix, take `CancellationToken`. No `.Result` / `.Wait()`. No `async void` except UI events that cannot return `Task`. Do not wrap a `Task` in `async`/`await` only to return it.
- Domain must not reference `DbContext` or ASP.NET types.

```csharp
// BAD
public async void Save() => await _db.SaveChangesAsync();
var article = GetAsync().Result;

// GOOD — suffix, token, return the Task
public Task SaveAsync(CancellationToken cancellationToken) =>
    _db.SaveChangesAsync(cancellationToken);
```
