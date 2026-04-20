# IpaStore — Agent & Contributor Guidelines

These rules are **strict**. All contributions — whether from humans or AI agents — must follow them without exception. Reviewers will reject any PR that violates these guidelines.

---

## 1. Modern C# Conventions

### Language & Runtime

- Target **net10.0** exclusively. Do not add any downgrade target-framework entries.
- Enable `<Nullable>enable</Nullable>` and `<ImplicitUsings>enable</ImplicitUsings>` in every `.csproj`.
- Never suppress nullable warnings with `!` (null-forgiving operator) unless the surrounding code makes nullability provably impossible and a brief comment explains why.

### Code Style

- Use **file-scoped namespaces** (`namespace Foo.Bar;`) — never block-scoped.
- Use **primary constructors** for classes and records whenever all constructor work is assignment-only.
- Prefer **records** (`record`, `record struct`) for immutable data shapes.
- Prefer **`init`** over `set` for properties that are only written during construction.
- Use **pattern matching** (`is`, `switch` expressions, list patterns) over type-casting chains.
- Use **collection expressions** (`[a, b, c]`) instead of `new List<T> { }` or `new[] { }`.
- Use **`var`** when the type is obvious from the right-hand side; spell out the type when it aids clarity.
- Prefer `async`/`await` over `.Result`/`.Wait()`. Never block on async code.
- Use **`ValueTask`** when a method is expected to complete synchronously most of the time.
- Prefer **LINQ** for query logic; avoid it in tight hot paths.
- Throw **specific exceptions** (`ArgumentNullException.ThrowIfNull`, `ArgumentOutOfRangeException.ThrowIfNegativeOrZero`, etc.) rather than generic ones.
- Use `ArgumentNullException.ThrowIfNull` and similar static helpers instead of manual null checks + `throw`.
- Keep methods **short and focused** — a single screen of code is a guideline, not a hard limit, but prefer splitting when a method has multiple distinct responsibilities.
- No **magic numbers** or **magic strings** — use named constants or enums.
- XML doc comments (`/// <summary>`) are required on all `public` and `protected` members.

### Formatting

- **4 spaces** — no tabs.
- Opening brace on the **same line** for methods and types (Allman is not used here).
- One blank line between members; two blank lines between top-level type declarations.
- **No trailing whitespace**. Files must end with a single newline.

### Naming

| Symbol | Convention | Example |
|---|---|---|
| Types, methods, properties | PascalCase | `PackageInfo`, `GetPackage` |
| Parameters, local variables | camelCase | `packageId`, `count` |
| Private fields | `_camelCase` | `_repository` |
| Constants | PascalCase | `MaxRetryCount` |
| Interfaces | `I` prefix | `IPackageRepository` |
| Generic type parameters | `T` prefix | `TResult` |

### Project Structure

- All production source lives under `src/`.
- All test projects live under `tests/` (mirroring the `src/` hierarchy).
- Project names follow the pattern `IpaStore.<AreaName>` (e.g. `IpaStore.Core`, `IpaStore.Api`).

---

## 2. Git Commit Conventions

Every commit message must follow **Conventional Commits** (https://www.conventionalcommits.org) with a **gitmoji** prefix.

### Format

```
<emoji> <type>(<scope>): <short description>

[optional body]

[optional footer(s)]
```

- **emoji** — one gitmoji (see table below), placed before the type token.
- **type** — lowercase conventional-commit type.
- **scope** — optional, lowercase, names the project or area changed (e.g. `playground`, `core`, `ci`).
- **short description** — imperative mood, lowercase, no trailing period, ≤ 72 chars total (including emoji and type).
- **body** — wrapped at 72 chars; explains *why*, not *what*.
- **footer** — `Fixes #<issue>`, `Closes #<issue>`, `BREAKING CHANGE: <description>`, co-authors, etc.

### Examples

```
✨ feat(core): add package version resolver

Resolves version conflicts by preferring the highest compatible semver
range rather than the first declaration.

Closes #42
```

```
🐛 fix(api): return 404 when package is not found

Previously the endpoint returned 500 because the repository threw
InvalidOperationException instead of returning null.

Fixes #17
```

```
♻️ refactor(playground): replace List<T> initializers with collection expressions
```

```
✅ test(core): add unit tests for version resolver edge cases
```

---

## 3. Git Emoji Reference

Use exactly one emoji per commit. Choose the one that best describes the **primary intent** of the change.

| Emoji | Code | Type | When to use |
|---|---|---|---|
| ✨ | `:sparkles:` | `feat` | Introducing a new feature |
| 🐛 | `:bug:` | `fix` | Fixing a bug |
| 🚑 | `:ambulance:` | `fix` | Critical hotfix |
| 🔒 | `:lock:` | `fix` | Fixing a security issue |
| ♻️ | `:recycle:` | `refactor` | Refactoring code |
| 🎨 | `:art:` | `style` | Improving structure or format |
| ⚡ | `:zap:` | `perf` | Improving performance |
| ✅ | `:white_check_mark:` | `test` | Adding or updating tests |
| 💚 | `:green_heart:` | `fix` | Fixing CI |
| 👷 | `:construction_worker:` | `ci` | Adding or updating CI pipeline |
| 📦 | `:package:` | `build` | Adding or updating compiled files or packages |
| ⬆️ | `:arrow_up:` | `build` | Upgrading dependencies |
| ⬇️ | `:arrow_down:` | `build` | Downgrading dependencies |
| 📝 | `:memo:` | `docs` | Adding or updating documentation |
| 🔧 | `:wrench:` | `chore` | Adding or updating configuration files |
| 🔨 | `:hammer:` | `chore` | Adding or updating development scripts |
| 🌱 | `:seedling:` | `chore` | Adding or updating seed files |
| 🗃️ | `:card_file_box:` | `chore` | Performing database-related changes |
| 🚀 | `:rocket:` | `chore` | Deploying stuff |
| 🎉 | `:tada:` | `chore` | Beginning a project |
| 💡 | `:bulb:` | `docs` | Adding or updating comments in source code |
| 🔥 | `:fire:` | `chore` | Removing code or files |
| 🚚 | `:truck:` | `refactor` | Moving or renaming files |
| 💥 | `:boom:` | `feat` | Introducing breaking changes |
| 🩹 | `:adhesive_bandage:` | `fix` | Simple fix for a non-critical issue |
| 🧪 | `:test_tube:` | `test` | Adding a failing test |
| 🧹 | `:broom:` | `chore` | Clean up / housekeeping |

> If none of the above fits perfectly, choose the closest match — do **not** invent new emoji.

---

## 4. Branch Naming

```
<type>/<short-kebab-description>
```

Examples: `feat/version-resolver`, `fix/404-on-missing-package`, `docs/contributing-guide`

---

## 5. Pull Request Rules

- Title must follow the same `<emoji> <type>(<scope>): <description>` format as a commit.
- Every PR must reference the issue it resolves (`Closes #<n>`) unless it is a chore with no associated issue.
- Squash-merge is preferred; keep the squash commit message in conventional-commit format.
- All CI checks must be green before merging.
- At least one approving review is required.
