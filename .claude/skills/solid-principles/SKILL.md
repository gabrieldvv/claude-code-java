---
name: solid-principles
description: SOLID principles checklist with Java examples. Use when reviewing classes, refactoring code, or when user asks about Single Responsibility, Open/Closed, Liskov, Interface Segregation, or Dependency Inversion. Scope is class-level design; do NOT use for macro package/module/dependency structure (use architecture-review), general readability/naming (use clean-code), or applying a specific GoF pattern (use design-patterns).
---

# SOLID Principles Skill

Review and apply SOLID principles in Java code.

This SKILL.md is a slim router. Detailed violations, refactored examples, detection heuristics, and per-principle tables live in `references/`. Load only the reference(s) for the principle(s) in scope; do not load all references by default.

## When to Use
- User says "check SOLID" / "SOLID review" / "is this class doing too much?"
- Reviewing class design
- Refactoring large classes
- Code review focusing on design

## Scope / Selection Guide

Pick the principle(s) that match the concern, then load only those reference files:

| Letter | Principle | One-liner | Load when |
|--------|-----------|-----------|-----------|
| **S** | Single Responsibility | One class = one reason to change | Class does too much / God class / mixed concerns |
| **O** | Open/Closed | Open for extension, closed for modification | Growing `if/else`/`switch` on type or status |
| **L** | Liskov Substitution | Subtypes must be substitutable for base types | Broken inheritance, `instanceof` guards, overrides that change behavior |
| **I** | Interface Segregation | Many specific interfaces > one general interface | Fat interfaces, empty/throwing implementations |
| **D** | Dependency Inversion | Depend on abstractions, not concretions | Hard `new` dependencies, hard-to-test infrastructure coupling |

If reviewing a full class or PR against all of SOLID, load references as each principle becomes relevant rather than up front.

## Quick-Check Table

| Principle | Question |
|-----------|----------|
| **SRP** | Does this class have more than one reason to change? |
| **OCP** | Will adding a new type/feature require modifying this class? |
| **LSP** | Can subclasses be used wherever parent is expected? |
| **ISP** | Are there empty or throwing method implementations? |
| **DIP** | Does high-level code depend on concrete implementations? |

## References

Load only the relevant reference for the principle under review:

- [references/single-responsibility.md](references/single-responsibility.md) — load when a class does too much (validation + persistence + notification + audit), or you need SRP violation/refactor examples, detection signals, and quick-check questions.
- [references/open-closed.md](references/open-closed.md) — load when type/status `if-else`/`switch` chains grow; contains OCP violation/refactor (Strategy) examples, detection signals, and the OCP pattern table.
- [references/liskov-substitution.md](references/liskov-substitution.md) — load for broken inheritance / substitutability concerns; contains the Rectangle/Square example, refactor, LSP rules table, and detection signals.
- [references/interface-segregation.md](references/interface-segregation.md) — load for fat interfaces or empty/throwing implementations; contains the Worker example, refactor, detection signals, and standard-library/`Repository` split example.
- [references/dependency-inversion.md](references/dependency-inversion.md) — load for hard `new` dependencies and testability coupling; contains the OrderService example, abstraction refactor, Spring DI example, and detection signals.
- [references/refactoring-patterns.md](references/refactoring-patterns.md) — load when you need the cross-principle violation → refactoring mapping table.

## Related Skills

- `design-patterns` - Implementation patterns (Factory, Strategy, Observer, etc.)
- `clean-code` - Code-level principles (DRY, KISS, naming)
- `java-code-review` - Comprehensive review checklist
