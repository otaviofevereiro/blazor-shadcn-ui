---
name: testing
description: >
  Default .NET test split. After any code change, run all tests in the current project
  before calling the work done. A user-visible UI change also needs a UI integration case.
  Use when test projects are in play, or the product has a user-visible UI.
---

# Testing

**When:** test projects (`*Tests*`, `*UiTests*`) are in play, or the product has a user-visible UI.

- xUnit + FluentAssertions. NSubstitute for Application interfaces. `WebApplicationFactory` / Testcontainers for host and SQL. Playwright for UI integration. bUnit for Razor UX only — it does not replace a flow-level UI case.
- Domain: unit tests, no `DbContext`, no mocks of the type under test.
- Application: unit tests with fakes. Web/Infrastructure: integration tests, never against production.

```csharp
// BAD - "authz" only via bUnit visibility
cut.Find("article-body").Markup.Should().NotBeEmpty();

// GOOD - host test asserts the HTTP contract
response.StatusCode.Should().Be(HttpStatusCode.NotFound);
```

A change to authorization, search, or media without tests is incomplete.

After any code change in an issue: run **all project tests except the UI suite** before calling that issue done. Use the runner and test projects this repo already documents (`dotnet test` on the solution, npm test, every non-UI `*Tests*` project — not a filter to one file, not a neighboring repo). Focused runs during the red-green loop are fine. A red project test is a defect in this change; fix it and re-run. Do not call the issue done while any project test is red. If the suite cannot run here, say what you could not run.

The initiative-end run is every test project, including the UI suite when the user agreed.

If the product has a user-visible UI, a change to that UI is incomplete until a UI integration case covers the new or changed flow. Write that case in the issue. Run the UI suite once at the end of the initiative, after the user agrees, together with all project tests. If there is no UI test project, add one with Playwright (or the runner the repo already documents). Host HTTP tests still cover authorization and private bytes.

The procedure is the `ui-testing` skill. When Playwright is the harness, also follow `playwright`.
