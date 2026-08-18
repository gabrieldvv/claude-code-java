# Narrative Code Skill

> Refactor or generate code structured as a clear story — identifiable plot, correct narrative levels, and well-named characters

Based on the *Narrative Code* principles from *The Art of Code* (Chapter 2).

## What It Does

Structures code so it reads top-to-bottom like a story a reader can follow without
scrolling back:

- Identifies the **plot** (Delivering, Cleaning, Defense, Archiving, Transformation)
- Organizes methods across four **narrative levels** with chunk budgets:
  Table of contents → Chapter → Scene → Action
- Enforces call rules (a Scene calls an Action, a Chapter calls a Scene, …) and
  extraction gates so methods are extracted only when they earn their place
- Names methods `verb + subject` with precise scope, and names variables as characters
- Emits an ASCII call graph showing the story flow

## When to Use

- "Narrative code refactor" / "story-driven refactor" / "narrative structure review"
- A long method mixing validation, persistence, and side effects inline
- Method names that force you to open the body to understand the flow

> Not for general code-quality reviews, data-model (`clarity-of-intent`) refactors,
> or failure-handling reviews — use those skills instead.

## Key Concepts

- **Chunk budgets** — Action ≤ 3, Scene ≤ 6, Chapter ≤ 8, Table of contents ≤ 8
- **Earn its place** — narrative levels are not a quota; don't extract a method that
  doesn't make the caller easier to read; allow small duplication when it aids reading
- **Mandatory audit** — mechanical checks (budgets, call edges, ordering, naming) must
  pass before output

## Example Usage

```
You: Refactor handleUserRegistration into narrative code

Claude: Plot: Defense + Archiving + Delivering
        [Refactored code, methods ordered high-level → low-level]
        [ASCII narrative structure / call graph]
```

## Related Skills

- `clarity-of-intent` — the data model (this skill focuses on method structure)
- `clean-code` — general readability and function design
- `solid-principles` — class-level responsibilities
