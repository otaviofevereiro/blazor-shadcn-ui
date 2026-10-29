---
name: architecture
description: >
  Four-layer dependency rule: UI -> Application -> Domain; Infrastructure implements
  Application. Use when the product is layered Web / Application / Domain / Infrastructure.
---

# Architecture

**Bind:** always, when the product is a layered Web / Application / Domain / Infrastructure app.

Default shape for a layered app: UI depends on Application and Domain. Infrastructure depends on Application and Domain. Application depends on Domain. Domain depends on nothing.

- Domain: entities and permission functions. No EF, ASP.NET, cloud SDKs, or HTTP.
- Application: use cases and interfaces (persistence, mail, files, payments, search).
- Infrastructure: EF, identity, cloud clients, migrations. Query filters and row-level isolation live here when the app is multi-tenant.
- UI: pages, endpoints, DI composition. No business rules in views. No `DbContext` or cloud client in components.

```csharp
// BAD — use case in a page
@inject AppDbContext Db
var item = await Db.Items.FindAsync(id);

// GOOD — UI calls Application
@inject IItemReader Items
var model = await Items.GetAsync(user, slug, ct);
```

Do not add a mediator, a second API host, or extra "core" projects unless the repo already has that shape.
