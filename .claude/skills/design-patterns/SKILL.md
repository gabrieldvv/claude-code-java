---
name: design-patterns
description: Common design patterns with Java examples (Factory, Builder, Strategy, Observer, Decorator, etc.). Use when user asks "implement pattern", "use factory", "strategy pattern", or when designing extensible components. Do NOT use for data-model/domain-type design (use clarity-of-intent), class-level principle checks (use solid-principles), or macro architecture pattern review (use architecture-review).
---

# Design Patterns Skill

Practical design patterns reference for Java with modern examples.

Each pattern family lives in its own reference file with full prose and code examples. Use the quick-reference table below to pick the pattern, then load only the relevant reference file for the detailed implementation.

## When to Use
- User asks to implement a specific pattern
- Designing extensible/flexible components
- Refactoring rigid code structures
- Code review suggests pattern usage

## Quick Reference: When to Use What

| Problem | Pattern | Family |
|---------|---------|--------|
| Complex object construction | **Builder** | Creational |
| Create objects without specifying class | **Factory** | Creational |
| Ensure single instance | **Singleton** | Creational |
| Multiple algorithms, swap at runtime | **Strategy** | Behavioral |
| Notify multiple objects of changes | **Observer** | Behavioral |
| Define algorithm skeleton | **Template Method** | Behavioral |
| Add behavior without changing class | **Decorator** | Structural |
| Convert incompatible interfaces | **Adapter** | Structural |

## Selection Guide

- Match the problem to a pattern in the table above, then load that pattern's family reference.
- If you are choosing between candidates or need to avoid over-engineering, load `references/pattern-selection.md` first (pattern selection guide + anti-patterns to avoid).
- Load only the reference(s) you need — do not load every file by default.

## References

- [references/creational-patterns.md](references/creational-patterns.md) — load when creating/constructing objects: **Builder**, **Factory Method**, **Singleton**.
- [references/behavioral-patterns.md](references/behavioral-patterns.md) — load for algorithm/interaction behavior: **Strategy**, **Observer**, **Template Method**.
- [references/structural-patterns.md](references/structural-patterns.md) — load for composing/adapting types: **Decorator**, **Adapter**.
- [references/pattern-selection.md](references/pattern-selection.md) — load to choose a pattern or avoid misuse: pattern selection guide + anti-patterns to avoid.

## Related Skills

- `solid-principles` - Design principles that patterns help implement
- `clean-code` - Code-level best practices
- `spring-boot-patterns` - Spring-specific implementations
