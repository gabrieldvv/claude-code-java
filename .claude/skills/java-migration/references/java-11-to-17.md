# Java 11 → 17 Migration

## Breaking Changes

| Change | Impact |
|--------|--------|
| Strong encapsulation | `--illegal-access` no longer works, must use explicit `--add-opens` |
| Sealed classes (final) | If you used preview features |
| Pattern matching instanceof | Preview → final syntax change |

### New Features to Adopt

```java
// Records (immutable data classes)
public record User(String name, String email) {}
// Auto-generates: constructor, getters, equals, hashCode, toString

// Sealed classes
public sealed class Shape permits Circle, Rectangle {}
public final class Circle extends Shape {}
public final class Rectangle extends Shape {}

// Pattern matching for instanceof
if (obj instanceof String s) {
    System.out.println(s.length());  // s already cast
}

// Switch expressions
String result = switch (day) {
    case MONDAY, FRIDAY -> "Work";
    case SATURDAY, SUNDAY -> "Rest";
    default -> "Midweek";
};

// Text blocks
String json = """
    {
        "name": "John",
        "age": 30
    }
    """;

// Helpful NullPointerException messages
// a.b.c.d() → tells exactly which part was null
```
