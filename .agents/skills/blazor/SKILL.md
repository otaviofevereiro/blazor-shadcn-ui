---
name: blazor
description: >
  Idiomatic Blazor Web App: SSR vs Interactive Server, inject application services,
  do not treat a hidden button as authorization. Use when *.razor files are in play.
---

# Blazor

**When:** `*.razor` files are in play.

Write idiomatic Razor for the current Blazor Web App model. Use the framework’s own features: `@page`, `@rendermode`, `@inject`, `[Parameter]` / `[CascadingParameter]`, `@bind`, event attributes, `@if` / `@foreach`, `@key` on lists, components over string-built markup.

- Static pages: static SSR. `OnInitializedAsync` is enough — each request is a new instance.
- Interactive pages: Interactive Server unless the project already chose WASM or Auto. Load from route or query in `OnParametersSetAsync`; the router reuses the page instance. Create-and-redirect stays static SSR.
- Shared components: render-mode agnostic. Inject application services, never `DbContext` or cloud SDKs.
- Hiding a button is not authorization. The use case still enforces the real permission check.

```razor
@* BAD *@
@inject AppDbContext Db
@if (user.IsInRole("Admin")) { <button>Publish</button> }

@* GOOD *@
@page "/items/{Id}"
@rendermode InteractiveServer
@inject IItemWriter Items
@* Writer methods check permission server-side *@
```
