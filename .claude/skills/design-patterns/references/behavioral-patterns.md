# Behavioral Patterns

## Behavioral Patterns

### Strategy

**Use when:** Multiple algorithms for same operation, need to swap at runtime.

```java
// ✅ Strategy pattern
public interface PaymentStrategy {
    void pay(BigDecimal amount);
}

public class CreditCardPayment implements PaymentStrategy {
    private final String cardNumber;

    public CreditCardPayment(String cardNumber) {
        this.cardNumber = cardNumber;
    }

    @Override
    public void pay(BigDecimal amount) {
        System.out.println("Paid " + amount + " with card " + cardNumber);
    }
}

public class PayPalPayment implements PaymentStrategy {
    private final String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    @Override
    public void pay(BigDecimal amount) {
        System.out.println("Paid " + amount + " via PayPal: " + email);
    }
}

public class CryptoPayment implements PaymentStrategy {
    private final String walletAddress;

    public CryptoPayment(String walletAddress) {
        this.walletAddress = walletAddress;
    }

    @Override
    public void pay(BigDecimal amount) {
        System.out.println("Paid " + amount + " to wallet: " + walletAddress);
    }
}

// Context
public class ShoppingCart {
    private PaymentStrategy paymentStrategy;

    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }

    public void checkout(BigDecimal total) {
        paymentStrategy.pay(total);
    }
}

// Usage
ShoppingCart cart = new ShoppingCart();
cart.setPaymentStrategy(new CreditCardPayment("4111-1111-1111-1111"));
cart.checkout(new BigDecimal("99.99"));

// Change strategy at runtime
cart.setPaymentStrategy(new PayPalPayment("user@example.com"));
cart.checkout(new BigDecimal("49.99"));
```

**With Java 8+ (functional):**
```java
// Strategy as functional interface
@FunctionalInterface
public interface PaymentStrategy {
    void pay(BigDecimal amount);
}

// Usage with lambdas
PaymentStrategy creditCard = amount ->
    System.out.println("Card payment: " + amount);

PaymentStrategy paypal = amount ->
    System.out.println("PayPal payment: " + amount);

cart.setPaymentStrategy(creditCard);
```

---

### Observer

**Use when:** Objects need to be notified of changes in another object.

```java
// ✅ Observer pattern (modern Java)
public interface OrderObserver {
    void onOrderPlaced(Order order);
}

public class OrderService {
    private final List<OrderObserver> observers = new ArrayList<>();

    public void addObserver(OrderObserver observer) {
        observers.add(observer);
    }

    public void removeObserver(OrderObserver observer) {
        observers.remove(observer);
    }

    public void placeOrder(Order order) {
        // Process order
        saveOrder(order);

        // Notify all observers
        observers.forEach(observer -> observer.onOrderPlaced(order));
    }
}

// Observers
public class InventoryService implements OrderObserver {
    @Override
    public void onOrderPlaced(Order order) {
        // Reduce inventory
        order.getItems().forEach(item ->
            reduceStock(item.getProductId(), item.getQuantity())
        );
    }
}

public class EmailNotificationService implements OrderObserver {
    @Override
    public void onOrderPlaced(Order order) {
        sendConfirmationEmail(order.getCustomerEmail(), order);
    }
}

public class AnalyticsService implements OrderObserver {
    @Override
    public void onOrderPlaced(Order order) {
        trackOrderEvent(order);
    }
}

// Setup
OrderService orderService = new OrderService();
orderService.addObserver(new InventoryService());
orderService.addObserver(new EmailNotificationService());
orderService.addObserver(new AnalyticsService());
```

**With Spring Events (preferred):**
```java
// Event
public record OrderPlacedEvent(Order order) {}

// Publisher
@Service
public class OrderService {
    private final ApplicationEventPublisher eventPublisher;

    public void placeOrder(Order order) {
        saveOrder(order);
        eventPublisher.publishEvent(new OrderPlacedEvent(order));
    }
}

// Listeners (observers)
@Component
public class InventoryListener {
    @EventListener
    public void handleOrderPlaced(OrderPlacedEvent event) {
        // Reduce inventory
    }
}

@Component
public class EmailListener {
    @EventListener
    public void handleOrderPlaced(OrderPlacedEvent event) {
        // Send email
    }

    @EventListener
    @Async  // Async processing
    public void handleOrderPlacedAsync(OrderPlacedEvent event) {
        // Send email asynchronously
    }
}
```

---

### Template Method

**Use when:** Define algorithm skeleton, let subclasses fill in steps.

```java
// ✅ Template Method pattern
public abstract class DataProcessor {

    // Template method - defines the algorithm
    public final void process() {
        readData();
        processData();
        writeData();
        if (shouldNotify()) {
            notifyCompletion();
        }
    }

    // Steps to be implemented by subclasses
    protected abstract void readData();
    protected abstract void processData();
    protected abstract void writeData();

    // Hook - optional override
    protected boolean shouldNotify() {
        return true;
    }

    protected void notifyCompletion() {
        System.out.println("Processing completed!");
    }
}

public class CsvDataProcessor extends DataProcessor {
    @Override
    protected void readData() {
        System.out.println("Reading CSV file...");
    }

    @Override
    protected void processData() {
        System.out.println("Processing CSV data...");
    }

    @Override
    protected void writeData() {
        System.out.println("Writing to database...");
    }
}

public class ApiDataProcessor extends DataProcessor {
    @Override
    protected void readData() {
        System.out.println("Fetching from API...");
    }

    @Override
    protected void processData() {
        System.out.println("Transforming API response...");
    }

    @Override
    protected void writeData() {
        System.out.println("Writing to cache...");
    }

    @Override
    protected boolean shouldNotify() {
        return false;  // Override hook
    }
}

// Usage
DataProcessor csvProcessor = new CsvDataProcessor();
csvProcessor.process();

DataProcessor apiProcessor = new ApiDataProcessor();
apiProcessor.process();
```
