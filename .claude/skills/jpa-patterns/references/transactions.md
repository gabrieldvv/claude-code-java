# Transactions

## Basic Transaction Management

```java
@Service
public class OrderService {

    // Read-only: Optimized, no dirty checking
    @Transactional(readOnly = true)
    public Order findById(Long id) {
        return orderRepository.findById(id).orElseThrow();
    }

    // Write: Full transaction with dirty checking
    @Transactional
    public Order createOrder(CreateOrderRequest request) {
        Order order = new Order();
        // ... set properties
        return orderRepository.save(order);
    }

    // Explicit rollback
    @Transactional(rollbackFor = Exception.class)
    public void processPayment(Long orderId) throws PaymentException {
        // Rolls back on any exception, not just RuntimeException
    }
}
```

## Transaction Propagation

```java
@Service
public class OrderService {

    @Autowired
    private PaymentService paymentService;

    @Transactional
    public void placeOrder(Order order) {
        orderRepository.save(order);

        // REQUIRED (default): Uses existing or creates new
        paymentService.processPayment(order);

        // If paymentService throws, entire order is rolled back
    }
}

@Service
public class PaymentService {

    // REQUIRES_NEW: Always creates new transaction
    // If this fails, order can still be saved
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void processPayment(Order order) {
        // Independent transaction
    }

    // MANDATORY: Must run within existing transaction
    @Transactional(propagation = Propagation.MANDATORY)
    public void updatePaymentStatus(Order order) {
        // Throws if no transaction exists
    }
}
```

## Common Transaction Mistakes

```java
// ❌ BAD: Calling @Transactional method from same class
@Service
public class OrderService {

    public void processOrder(Long id) {
        updateOrder(id);  // @Transactional is IGNORED!
    }

    @Transactional
    public void updateOrder(Long id) {
        // Transaction not started because called internally
    }
}

// ✅ GOOD: Inject self or use separate service
@Service
public class OrderService {

    @Autowired
    private OrderService self;  // Or use separate service

    public void processOrder(Long id) {
        self.updateOrder(id);  // Now transaction works
    }

    @Transactional
    public void updateOrder(Long id) {
        // Transaction properly started
    }
}
```
