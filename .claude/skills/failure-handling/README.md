# Failure Handling Skill

> Diagnostic review of error handling, exception strategy, logging, and boundary protection — proposals only, no automatic changes

Based on the *Handling Failure* principles from *The Art of Code* (Chapter 7).

## What It Does

Reviews existing code to understand how failures are handled, detects antipatterns,
and proposes safer, clearer alternatives. It is **diagnostic-only** — it does not
rewrite files, change exception types, or alter behavior; it produces findings for a
human to apply deliberately.

- Builds a **failure map** of the success path and the ways things can go wrong
- Classifies each failure: **business logic error**, **technical issue**,
  **programming error**, or **fatal error** — and judges whether the current handling
  strategy fits the class
- Flags signals: missing boundary validation, lazy/broad `catch`, logging problems
  (console-only, vague, sensitive data, wrong level, double-logging), and flawed
  strategies (empty catch, null fallback, lost cause, leaking stack traces, missing
  retry/timeout/circuit-breaker)
- Filters signals through justification checks before reporting
- Orders findings by severity → confidence → fix effort, with assumptions noted

## When to Use

- "Failure handling review" / "error handling audit" / "exception handling review"
- Before hardening a service that crosses I/O, DB, network, or queue boundaries

> Not for general architecture reviews, code-quality reviews, sustainability audits,
> or refactoring — use `architecture-review` or `sustainability` instead.

## Key Concepts

- **Failure classification** drives the right response: business errors → clear
  user-facing response; technical issues → log + translate/recover; programming
  errors → fail fast and fix; fatal errors → leave to runtime/supervisor.
- **Security is cross-cutting** — classify by cause, but flag security impact separately.
- **No automatic changes** — every response ends confirming nothing was modified.

## Example Usage

```
You: Do a failure-handling review of UserService

Claude: 1. Failure map (success path + classified failure paths)
        2. Findings (severity-ordered, with recommendations + confidence)
        3. Human-review notes
        4. No automatic changes
```

## Related Skills

- `java-code-review` — broad review (this skill goes deep on error strategy)
- `logging-patterns` — how to implement the logging recommendations
- `security-audit` — follow up on flagged security impact
- `architecture-review` — structural/boundary concerns beyond error handling
