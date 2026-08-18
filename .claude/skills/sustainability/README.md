# Sustainability Skill

> Green software / efficiency audit across five lean patterns — proposals only, no automatic changes

Based on the green patterns from *The Art of Code* (Chapter 8).

## What It Does

Reviews code or architecture for resource waste (CPU, memory, storage, network,
package size, hardware pressure) and proposes the smallest safe improvement that
preserves behavior. It is **review-only** — it produces findings, it does not modify
code, config, infrastructure, or data.

Audits against five patterns:

- **Lean Packaging** — unused/transitive dependencies, dead code, dev assets in prod, missing tree-shaking
- **Lean Storage** — oversized types, duplicated data, unbounded logs/temp/backups, missing retention
- **Lean Communication** — chatty/N+1 client calls, aggressive polling, missing caching, over-fetching, heavy payloads
- **Efficient Execution** — poor algorithms/data structures, DB inefficiencies, missing pooling, sync heavy tasks
- **Memory Efficiency** — long-lived retention, full-load vs streaming, unbounded caches, leaked resources

## When to Use

- "Sustainability audit" / "green review" / "efficiency audit"
- Investigating resource waste at the code or architecture level

> Not for general architecture/code-quality reviews (use `architecture-review`),
> code-level Java micro-performance smells like streams/boxing/regex (use
> `performance-smell-detection`), or JPA/database performance (use `jpa-patterns`).

## Key Concepts

- **Smallest safe improvement** — each finding proposes the least-invasive change that
  preserves behavior, with an explicit trade-off.
- **Findings carry** Pattern · Issue · Wasted resource · Why it matters · Improvement ·
  Trade-off · Severity · Confidence (+ assumptions when confidence is medium/low),
  ordered by severity → confidence → fix effort.
- **Policy-aware** — respects legal retention and privacy rules (GDPR/CCPA) for storage.

## Example Usage

```
You: Run a sustainability audit on this service

Claude: [Findings grouped by pattern, each with wasted resource + smallest safe
         improvement + trade-off + severity/confidence, ordered by priority]
        [Explicit "no optimization recommended" where nothing meaningful applies]
```

## Related Skills

- `performance-smell-detection` — code-level Java micro-smells (this skill is broader, resource-focused)
- `jpa-patterns` — database/persistence performance
- `architecture-review` — structural quality (its own review scopes)
- `maven-dependency-audit` — dependency version/CVE audit (complements Lean Packaging)
