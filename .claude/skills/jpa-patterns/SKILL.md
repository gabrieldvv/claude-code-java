---
name: jpa-patterns
description: JPA/Hibernate patterns and common pitfalls (N+1, lazy loading, transactions, queries). Use when user has JPA performance issues, LazyInitializationException, or asks about entity relationships and fetching strategies. This is the go-to for persistence/database performance; do NOT use for general code-level performance smells (use performance-smell-detection) or Spring wiring/config (use spring-boot-patterns).
---

# JPA Patterns Skill

Best practices and common pitfalls for JPA/Hibernate in Spring applications.

This SKILL.md is a slim router. Identify the symptom, use the quick-reference table below to pick the matching topic, then load only the relevant reference file from `references/`. Do not load every reference; load the one(s) that match the user's actual problem.

## When to Use
- User mentions "N+1 problem" / "too many queries"
- LazyInitializationException errors
- Questions about fetch strategies (EAGER vs LAZY)
- Transaction management issues
- Entity relationship design
- Query optimization

## Quick Reference: Common Problems

| Problem | Symptom | Solution | Reference |
|---------|---------|----------|-----------|
| N+1 queries | Many SELECT statements | JOIN FETCH, @EntityGraph | `references/n-plus-one.md` |
| LazyInitializationException | Error outside transaction | Open Session in View, DTO projection, JOIN FETCH | `references/fetching-and-lazy-loading.md` |
| Slow queries | Performance issues | Pagination, projections, indexes | `references/query-optimization.md` |
| Dirty checking overhead | Slow updates | Read-only transactions, DTOs | `references/transactions.md` |
| Lost updates | Concurrent modifications | Optimistic locking (@Version) | `references/query-optimization.md` |

## References

Load only the reference(s) that match the problem at hand:

- `references/n-plus-one.md` — load when the user has too many SELECT statements or asks about the N+1 problem and its fixes (JOIN FETCH, @EntityGraph, batch fetching, detecting N+1).
- `references/fetching-and-lazy-loading.md` — load for LazyInitializationException, or questions about LAZY vs EAGER fetch strategies.
- `references/transactions.md` — load for @Transactional usage, propagation, read-only vs write transactions, and self-invocation pitfalls.
- `references/relationships-and-identity.md` — load for entity relationship design (OneToMany/ManyToOne, ManyToMany) and entity equals()/hashCode().
- `references/query-optimization.md` — load for pagination, DTO projections, bulk operations, optimistic locking, and common mistakes (cascade misuse, missing indexes, toString with lazy fields).

## Performance Checklist

When reviewing JPA code, check:

- [ ] No N+1 queries (use JOIN FETCH or @EntityGraph)
- [ ] LAZY fetch by default (especially @ManyToOne)
- [ ] Pagination for large result sets
- [ ] DTO projections for read-only queries
- [ ] Bulk operations for batch updates/deletes
- [ ] @Version for entities with concurrent access
- [ ] Indexes on frequently queried columns
- [ ] No lazy fields in toString()
- [ ] Read-only transactions where applicable

## Related Skills

- `spring-boot-patterns` - Spring Boot controller/service patterns
- `java-code-review` - General code review checklist
- `clean-code` - Code quality principles
