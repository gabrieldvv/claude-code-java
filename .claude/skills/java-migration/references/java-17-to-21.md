# Java 17 → 21 Migration

## Breaking Changes

| Change | Impact |
|--------|--------|
| Pattern matching switch (final) | Minor syntax differences from preview |
| `finalize()` deprecated for removal | Replace with `Cleaner` or try-with-resources |
| UTF-8 by default | May affect file reading if assumed platform encoding |

### New Features to Adopt

```java
// Virtual Threads (Project Loom) - MAJOR
try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> handleRequest());
}
// Or simply:
Thread.startVirtualThread(() -> doWork());

// Pattern matching in switch
String formatted = switch (obj) {
    case Integer i -> "int: " + i;
    case String s -> "string: " + s;
    case null -> "null value";
    default -> "unknown";
};

// Record patterns
record Point(int x, int y) {}
if (obj instanceof Point(int x, int y)) {
    System.out.println(x + ", " + y);
}

// Sequenced Collections
List<String> list = new ArrayList<>();
list.addFirst("first");    // new method
list.addLast("last");      // new method
list.reversed();           // reversed view

// String templates (preview in 21)
// May need --enable-preview

// Scoped Values (preview) - replace ThreadLocal
ScopedValue<User> CURRENT_USER = ScopedValue.newInstance();
ScopedValue.where(CURRENT_USER, user).run(() -> {
    // CURRENT_USER.get() available here
});
```
