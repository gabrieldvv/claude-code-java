# Lazy Loading

## FetchType Basics

```java
@Entity
public class Order {

    // LAZY: Load only when accessed (default for collections)
    @OneToMany(mappedBy = "order", fetch = FetchType.LAZY)
    private List<OrderItem> items;

    // EAGER: Always load immediately (default for @ManyToOne, @OneToOne)
    @ManyToOne(fetch = FetchType.EAGER)  // ⚠️ Usually bad
    private Customer customer;
}
```

## Best Practice: Default to LAZY

```java
// ✅ GOOD: Always use LAZY, fetch when needed
@Entity
public class Order {

    @ManyToOne(fetch = FetchType.LAZY)  // Override EAGER default
    private Customer customer;

    @OneToMany(mappedBy = "order", fetch = FetchType.LAZY)
    private List<OrderItem> items;
}
```

## LazyInitializationException

```java
// ❌ BAD: Accessing lazy field outside transaction
@Service
public class OrderService {

    public Order getOrder(Long id) {
        return orderRepository.findById(id).orElseThrow();
    }
}

// In controller (no transaction)
Order order = orderService.getOrder(1L);
order.getItems().size();  // 💥 LazyInitializationException!
```

## Solutions for LazyInitializationException

**Solution 1: JOIN FETCH in query**
```java
// ✅ Fetch needed associations in query
@Query("SELECT o FROM Order o JOIN FETCH o.items WHERE o.id = :id")
Optional<Order> findByIdWithItems(@Param("id") Long id);
```

**Solution 2: @Transactional on service method**
```java
// ✅ Keep transaction open while accessing
@Service
public class OrderService {

    @Transactional(readOnly = true)
    public OrderDTO getOrderWithItems(Long id) {
        Order order = orderRepository.findById(id).orElseThrow();
        // Access within transaction
        int itemCount = order.getItems().size();
        return new OrderDTO(order, itemCount);
    }
}
```

**Solution 3: DTO Projection (recommended)**
```java
// ✅ BEST: Return only what you need
public interface OrderSummary {
    Long getId();
    String getStatus();
    int getItemCount();
}

@Query("SELECT o.id as id, o.status as status, SIZE(o.items) as itemCount " +
       "FROM Order o WHERE o.id = :id")
Optional<OrderSummary> findOrderSummary(@Param("id") Long id);
```

**Solution 4: Open Session in View (not recommended)**
```yaml
# Keeps session open during view rendering
# ⚠️ Can mask N+1 problems, use with caution
spring:
  jpa:
    open-in-view: true  # Default is true
```
