# Java 21 → 25 Migration

## Breaking Changes

| Change | Impact |
|--------|--------|
| Security Manager removed | Applications relying on it need alternative security approaches |
| `sun.misc.Unsafe` methods removed | Use `VarHandle` or FFM API instead |
| 32-bit platforms dropped | No more x86-32 support |
| Record pattern variables final | Cannot reassign pattern variables in switch |
| `ScopedValue.orElse(null)` disallowed | Must provide non-null default |
| Dynamic agents restricted | Requires `-XX:+EnableDynamicAgentLoading` flag |

### Check for Unsafe Usage

```bash
# Find sun.misc.Unsafe usage
grep -rn "sun\.misc\.Unsafe" --include="*.java" src/

# Find Security Manager usage
grep -rn "SecurityManager\|System\.getSecurityManager" --include="*.java" src/
```

### New Features to Adopt

```java
// Scoped Values (FINAL in Java 25) - replaces ThreadLocal
private static final ScopedValue<User> CURRENT_USER = ScopedValue.newInstance();

public void handleRequest(User user) {
    ScopedValue.where(CURRENT_USER, user).run(() -> {
        processRequest();  // CURRENT_USER.get() available here and in child threads
    });
}

// Structured Concurrency (Preview, redesigned API in 25)
try (StructuredTaskScope.ShutdownOnFailure scope = StructuredTaskScope.open()) {
    Subtask<User> userTask = scope.fork(() -> fetchUser(id));
    Subtask<Orders> ordersTask = scope.fork(() -> fetchOrders(id));

    scope.join();
    scope.throwIfFailed();

    return new Profile(userTask.get(), ordersTask.get());
}

// Stable Values (Preview) - lazy initialization made easy
private static final StableValue<ExpensiveService> SERVICE =
    StableValue.of(() -> new ExpensiveService());

public void useService() {
    SERVICE.get().doWork();  // Initialized on first access, cached thereafter
}

// Compact Object Headers - automatic, no code changes
// Objects now use 64-bit headers instead of 128-bit (less memory)

// Primitive Patterns in instanceof (Preview)
if (obj instanceof int i) {
    System.out.println("int value: " + i);
}

// Module Import Declarations (Preview)
import module java.sql;  // Import all public types from module
```

### Performance Improvements (Automatic)

Java 25 includes several automatic performance improvements:
- **Compact Object Headers**: 8 bytes instead of 16 bytes per object
- **String.hashCode() constant folding**: Faster Map lookups with String keys
- **AOT class loading**: Faster startup with ahead-of-time cache
- **Generational Shenandoah GC**: Better throughput, lower pauses

### Migration with OpenRewrite

```bash
# Automated Java 25 migration
mvn -U org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-migrate-java:LATEST \
  -Drewrite.activeRecipes=org.openrewrite.java.migrate.UpgradeToJava25
```
