---
name: java-migration
description: Guide for upgrading Java projects between major versions (8→11→17→21→25). Use when user says "upgrade Java", "migrate to Java 25", "update Java version", or when modernizing legacy projects.
---

# Java Migration Skill

Step-by-step guide for upgrading Java projects between major versions.

## When to Use
- User says "upgrade to Java 25" / "migrate from Java 8" / "update Java version"
- Modernizing legacy projects
- Spring Boot 2.x → 3.x → 4.x migration
- Preparing for LTS version adoption

## Migration Paths

```
Java 8 (LTS) → Java 11 (LTS) → Java 17 (LTS) → Java 21 (LTS) → Java 25 (LTS)
     │              │               │              │               │
     └──────────────┴───────────────┴──────────────┴───────────────┘
                         Always migrate LTS → LTS
```

---

## Quick Reference: What Breaks

| From → To | Major Breaking Changes |
|-----------|------------------------|
| 8 → 11 | Removed `javax.xml.bind`, module system, internal APIs |
| 11 → 17 | Sealed classes (preview→final), strong encapsulation |
| 17 → 21 | Pattern matching changes, `finalize()` deprecated for removal |
| 21 → 25 | Security Manager removed, Unsafe methods removed, 32-bit dropped |

---

## Workflow

Every migration follows the same version-agnostic loop: assess current state, update
build configuration, fix compilation errors, run tests, and check runtime warnings.
Load [references/migration-workflow.md](references/migration-workflow.md) for the full
step-by-step workflow, dependency-update guidance, common issues, the migration
checklist, quick commands, and the version compatibility matrix.

Then load only the reference(s) for the version hop(s) the user is performing.

---

## References

Load only the relevant reference for the migration the user is performing:

- [references/migration-workflow.md](references/migration-workflow.md) — load this for any migration: the general workflow (assess → build config → compile → test → runtime), common migration issues, migration checklist, quick commands, and the version compatibility matrix.
- [references/java-8-to-11.md](references/java-8-to-11.md) — load this when the user is migrating Java 8 → 11 (removed APIs, Jakarta dependencies, module system flags, new features to adopt).
- [references/java-11-to-17.md](references/java-11-to-17.md) — load this when the user is migrating Java 11 → 17 (strong encapsulation, sealed classes, records, switch expressions, text blocks).
- [references/java-17-to-21.md](references/java-17-to-21.md) — load this when the user is migrating Java 17 → 21 (virtual threads, pattern matching in switch, record patterns, sequenced collections, scoped values).
- [references/java-21-to-25.md](references/java-21-to-25.md) — load this when the user is migrating Java 21 → 25 (Security Manager/Unsafe removal, scoped values final, structured concurrency, stable values, automatic performance improvements, OpenRewrite).
- [references/spring-boot-2-to-3.md](references/spring-boot-2-to-3.md) — load this when the user is migrating Spring Boot 2.x → 3.x (javax → jakarta, dependency updates, Hibernate 5 → 6 changes).
