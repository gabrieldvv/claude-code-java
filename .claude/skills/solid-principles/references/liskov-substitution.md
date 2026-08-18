# Liskov Substitution Principle (LSP)

## L - Liskov Substitution Principle (LSP)

> "Subtypes must be substitutable for their base types."

### Violation

```java
// ❌ BAD: Square violates Rectangle contract
public class Rectangle {
    protected int width;
    protected int height;

    public void setWidth(int width) {
        this.width = width;
    }

    public void setHeight(int height) {
        this.height = height;
    }

    public int getArea() {
        return width * height;
    }
}

public class Square extends Rectangle {
    @Override
    public void setWidth(int width) {
        this.width = width;
        this.height = width;  // Violates expected behavior!
    }

    @Override
    public void setHeight(int height) {
        this.width = height;  // Violates expected behavior!
        this.height = height;
    }
}

// This test fails for Square!
void testRectangle(Rectangle r) {
    r.setWidth(5);
    r.setHeight(4);
    assert r.getArea() == 20;  // Square returns 16!
}
```

### Refactored

```java
// ✅ GOOD: Separate abstractions

public interface Shape {
    int getArea();
}

public class Rectangle implements Shape {
    private final int width;
    private final int height;

    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public int getArea() {
        return width * height;
    }
}

public class Square implements Shape {
    private final int side;

    public Square(int side) {
        this.side = side;
    }

    @Override
    public int getArea() {
        return side * side;
    }
}
```

### LSP Rules

| Rule | Meaning |
|------|---------|
| Preconditions | Subclass cannot strengthen (require more) |
| Postconditions | Subclass cannot weaken (promise less) |
| Invariants | Subclass must maintain parent's invariants |
| History | Subclass cannot modify inherited state unexpectedly |

### How to Detect LSP Violations

- Subclass throws exception parent doesn't
- Subclass returns null where parent returns object
- Subclass ignores or overrides parent behavior unexpectedly
- `instanceof` checks before calling methods
- Empty or throwing implementations of interface methods

### Quick Check

```java
// If you see this, LSP might be violated
if (bird instanceof Penguin) {
    // don't call fly()
} else {
    bird.fly();
}
```
