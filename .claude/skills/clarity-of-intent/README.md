# Clarity of Intent Skill

> Refactor or generate code so the data model reveals intent — domain types, precise names, small carriers, invariants, and immutability

Based on the *Clarity of Intent* principles from *The Art of Code* (Chapter 4).

## What It Does

Makes the data model tell the reader what a value means, what unit it uses,
whether it can change, and which rules make it valid — without opening its
definition. By default it **refactors** the code (not just reviews it):

- Replaces ambiguous primitives with domain types (only when the type earns its place)
- Renames vague fields/params to reveal role and unit
- Groups related values into small explicit carriers (records)
- Flattens nested structures that hide meaning
- Pushes invariants onto the type and makes data immutable where mutation isn't needed
- Moves behavior onto the type and simplifies now-redundant interpretation logic

## When to Use

- "Refactor this data model" / "clarity of intent refactor" / "domain type review"
- Code with primitive obsession, unit-free numbers, or swappable same-typed params
- Generic names (`data`, `value`, `flag`, `status`, `payload`)
- Scattered validation, unnecessary mutability, or boilerplate that buries structure

> Not for general architecture reviews, failure-handling reviews, or sustainability
> audits — use `architecture-review` or `failure-handling` instead.

## Key Concepts

- **Earn its place** — a wrapper/carrier is introduced only when it carries a rule,
  unit, identity, or state the raw type cannot enforce. Naming alone is not a reason.
- **Public-boundary safety** — for OpenAPI DTOs, REST models, published events, or
  persisted JPA entities, principles are applied to the internal domain layer, not
  the contract itself.
- **Smallest blast radius first** — rename → group → wrap → categorize → invariants →
  immutability → flatten → construction → simplify behavior.

## Example Usage

```
You: Refactor this Order class for clarity of intent

Claude: [Lists signals found]
        [Returns refactored code with domain types/records]
        [Explains data-modeling choices, behavior simplifications, trade-offs]
```

## Related Skills

- `clean-code` — general readability (this skill focuses on the data model)
- `narrative-code` — method/structure storytelling (this skill focuses on values)
- `solid-principles` — class-level design
- `design-patterns` — construction patterns (Builder, Factory)
