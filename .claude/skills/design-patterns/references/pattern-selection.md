# Pattern Selection Guide

## Pattern Selection Guide

| Situation | Consider |
|-----------|----------|
| Object creation is complex | Builder, Factory |
| Need to add features dynamically | Decorator |
| Multiple implementations of algorithm | Strategy |
| React to state changes | Observer |
| Integrate with legacy code | Adapter |
| Common algorithm, varying steps | Template Method |
| Need single instance | Singleton (use sparingly) |

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Better Approach |
|--------------|---------|-----------------|
| Singleton abuse | Global state, hard to test | Dependency Injection |
| Factory everywhere | Over-engineering | Simple `new` if type is known |
| Deep decorator chains | Hard to debug | Keep chains short, consider composition |
| Observer with many events | Spaghetti notifications | Event bus, clear event hierarchy |
