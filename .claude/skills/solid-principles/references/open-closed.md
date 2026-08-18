# Open/Closed Principle (OCP)

## O - Open/Closed Principle (OCP)

> "Software entities should be open for extension, but closed for modification."

### Violation

```java
// ❌ BAD: Must modify class to add new discount type
public class DiscountCalculator {

    public double calculate(Order order, String discountType) {
        if (discountType.equals("PERCENTAGE")) {
            return order.getTotal() * 0.1;
        } else if (discountType.equals("FIXED")) {
            return 50.0;
        } else if (discountType.equals("LOYALTY")) {
            return order.getTotal() * order.getCustomer().getLoyaltyRate();
        }
        // Every new discount type = modify this class
        return 0;
    }
}
```

### Refactored

```java
// ✅ GOOD: Add new discounts without modifying existing code

public interface DiscountStrategy {
    double calculate(Order order);
    boolean supports(String discountType);
}

public class PercentageDiscount implements DiscountStrategy {
    @Override
    public double calculate(Order order) {
        return order.getTotal() * 0.1;
    }

    @Override
    public boolean supports(String discountType) {
        return "PERCENTAGE".equals(discountType);
    }
}

public class FixedDiscount implements DiscountStrategy {
    @Override
    public double calculate(Order order) {
        return 50.0;
    }

    @Override
    public boolean supports(String discountType) {
        return "FIXED".equals(discountType);
    }
}

public class LoyaltyDiscount implements DiscountStrategy {
    @Override
    public double calculate(Order order) {
        return order.getTotal() * order.getCustomer().getLoyaltyRate();
    }

    @Override
    public boolean supports(String discountType) {
        return "LOYALTY".equals(discountType);
    }
}

// New discount? Just add new class, no modification needed
public class SeasonalDiscount implements DiscountStrategy {
    @Override
    public double calculate(Order order) {
        return order.getTotal() * 0.2;
    }

    @Override
    public boolean supports(String discountType) {
        return "SEASONAL".equals(discountType);
    }
}

public class DiscountCalculator {
    private final List<DiscountStrategy> strategies;

    public DiscountCalculator(List<DiscountStrategy> strategies) {
        this.strategies = strategies;
    }

    public double calculate(Order order, String discountType) {
        return strategies.stream()
            .filter(s -> s.supports(discountType))
            .findFirst()
            .map(s -> s.calculate(order))
            .orElse(0.0);
    }
}
```

### How to Detect OCP Violations

- `if/else` or `switch` on type/status that grows over time
- Enum-based dispatching with frequent new values
- Changes require modifying core classes

### Common OCP Patterns

| Pattern | Use When |
|---------|----------|
| Strategy | Multiple algorithms for same operation |
| Template Method | Same structure, different steps |
| Decorator | Add behavior dynamically |
| Factory | Create objects without specifying class |
