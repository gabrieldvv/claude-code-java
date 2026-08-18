# Dependency Inversion Principle (DIP)

## D - Dependency Inversion Principle (DIP)

> "High-level modules should not depend on low-level modules. Both should depend on abstractions."

### Violation

```java
// ❌ BAD: High-level depends on low-level directly
public class OrderService {
    private MySqlOrderRepository repository;  // Concrete class!
    private SmtpEmailSender emailSender;      // Concrete class!

    public OrderService() {
        this.repository = new MySqlOrderRepository();  // Hard dependency
        this.emailSender = new SmtpEmailSender();      // Hard dependency
    }

    public void createOrder(Order order) {
        repository.save(order);
        emailSender.send(order.getCustomerEmail(), "Order confirmed");
    }
}
```

**Problems:**
- Cannot test without real MySQL database
- Cannot swap email provider
- OrderService knows about MySQL, SMTP details

### Refactored

```java
// ✅ GOOD: Depend on abstractions

// Abstractions (interfaces)
public interface OrderRepository {
    void save(Order order);
    Optional<Order> findById(Long id);
}

public interface NotificationSender {
    void send(String recipient, String message);
}

// High-level module depends on abstractions
public class OrderService {
    private final OrderRepository repository;
    private final NotificationSender notificationSender;

    // Dependencies injected
    public OrderService(OrderRepository repository,
                        NotificationSender notificationSender) {
        this.repository = repository;
        this.notificationSender = notificationSender;
    }

    public void createOrder(Order order) {
        repository.save(order);
        notificationSender.send(order.getCustomerEmail(), "Order confirmed");
    }
}

// Low-level modules implement abstractions
public class MySqlOrderRepository implements OrderRepository {
    @Override
    public void save(Order order) { /* MySQL specific */ }

    @Override
    public Optional<Order> findById(Long id) { /* MySQL specific */ }
}

public class SmtpEmailSender implements NotificationSender {
    @Override
    public void send(String recipient, String message) { /* SMTP specific */ }
}

// Easy to test with mocks!
public class InMemoryOrderRepository implements OrderRepository {
    private Map<Long, Order> orders = new HashMap<>();

    @Override
    public void save(Order order) {
        orders.put(order.getId(), order);
    }

    @Override
    public Optional<Order> findById(Long id) {
        return Optional.ofNullable(orders.get(id));
    }
}
```

### DIP with Spring

```java
// Spring handles dependency injection automatically

@Service
public class OrderService {
    private final OrderRepository repository;
    private final NotificationSender notificationSender;

    // Constructor injection (recommended)
    public OrderService(OrderRepository repository,
                        NotificationSender notificationSender) {
        this.repository = repository;
        this.notificationSender = notificationSender;
    }
}

@Repository
public class JpaOrderRepository implements OrderRepository {
    // Spring provides implementation
}

@Component
@Profile("production")
public class SmtpEmailSender implements NotificationSender { }

@Component
@Profile("test")
public class MockEmailSender implements NotificationSender { }
```

### How to Detect DIP Violations

- `new ConcreteClass()` inside business logic
- Import statements include implementation packages (e.g., `com.mysql`, `org.apache.http`)
- Cannot easily swap implementations
- Tests require real infrastructure (database, network)
