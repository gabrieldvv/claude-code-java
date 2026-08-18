# Query Optimization

## Pagination

```java
// ✅ GOOD: Always paginate large result sets
public interface OrderRepository extends JpaRepository<Order, Long> {

    Page<Order> findByStatus(OrderStatus status, Pageable pageable);

    // With sorting
    @Query("SELECT o FROM Order o WHERE o.status = :status")
    Page<Order> findByStatusSorted(
        @Param("status") OrderStatus status,
        Pageable pageable
    );
}

// Usage
Pageable pageable = PageRequest.of(0, 20, Sort.by("createdAt").descending());
Page<Order> orders = orderRepository.findByStatus(OrderStatus.PENDING, pageable);
```

## DTO Projections

```java
// ✅ GOOD: Fetch only needed columns

// Interface-based projection
public interface OrderSummary {
    Long getId();
    String getCustomerName();
    BigDecimal getTotal();
}

@Query("SELECT o.id as id, o.customer.name as customerName, o.total as total " +
       "FROM Order o WHERE o.status = :status")
List<OrderSummary> findOrderSummaries(@Param("status") OrderStatus status);

// Class-based projection (DTO)
public record OrderDTO(Long id, String customerName, BigDecimal total) {}

@Query("SELECT new com.example.dto.OrderDTO(o.id, o.customer.name, o.total) " +
       "FROM Order o WHERE o.status = :status")
List<OrderDTO> findOrderDTOs(@Param("status") OrderStatus status);
```

## Bulk Operations

```java
// ✅ GOOD: Bulk update instead of loading entities
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Modifying
    @Query("UPDATE Order o SET o.status = :status WHERE o.createdAt < :date")
    int updateOldOrdersStatus(
        @Param("status") OrderStatus status,
        @Param("date") LocalDateTime date
    );

    @Modifying
    @Query("DELETE FROM Order o WHERE o.status = :status AND o.createdAt < :date")
    int deleteOldOrders(
        @Param("status") OrderStatus status,
        @Param("date") LocalDateTime date
    );
}

// Usage
@Transactional
public void archiveOldOrders() {
    LocalDateTime threshold = LocalDateTime.now().minusYears(1);
    int updated = orderRepository.updateOldOrdersStatus(
        OrderStatus.ARCHIVED,
        threshold
    );
    log.info("Archived {} orders", updated);
}
```

---

## Optimistic Locking

### Prevent Lost Updates

```java
// ✅ GOOD: Use @Version for optimistic locking
@Entity
public class Order {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Version
    private Long version;

    private OrderStatus status;
    private BigDecimal total;
}

// When two users update same order:
// User 1: loads order (version=1), modifies, saves → version becomes 2
// User 2: loads order (version=1), modifies, saves → OptimisticLockException!
```

### Handling OptimisticLockException

```java
@Service
public class OrderService {

    @Transactional
    public Order updateOrder(Long id, UpdateOrderRequest request) {
        try {
            Order order = orderRepository.findById(id).orElseThrow();
            order.setStatus(request.getStatus());
            return orderRepository.save(order);
        } catch (OptimisticLockException e) {
            throw new ConcurrentModificationException(
                "Order was modified by another user. Please refresh and try again."
            );
        }
    }

    // Or with retry
    @Retryable(value = OptimisticLockException.class, maxAttempts = 3)
    @Transactional
    public Order updateOrderWithRetry(Long id, UpdateOrderRequest request) {
        Order order = orderRepository.findById(id).orElseThrow();
        order.setStatus(request.getStatus());
        return orderRepository.save(order);
    }
}
```

---

## Common Mistakes

### 1. Cascade Misuse

```java
// ❌ BAD: CascadeType.ALL on @ManyToOne
@Entity
public class Book {
    @ManyToOne(cascade = CascadeType.ALL)  // Dangerous!
    private Author author;
}
// Deleting a book could delete the author!

// ✅ GOOD: Cascade only from parent to child
@Entity
public class Author {
    @OneToMany(mappedBy = "author", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Book> books;
}
```

### 2. Missing Index

```java
// ❌ BAD: Frequent queries on non-indexed column
@Query("SELECT o FROM Order o WHERE o.customerEmail = :email")
List<Order> findByCustomerEmail(@Param("email") String email);

// ✅ GOOD: Add index
@Entity
@Table(indexes = @Index(name = "idx_order_customer_email", columnList = "customerEmail"))
public class Order {
    private String customerEmail;
}
```

### 3. toString() with Lazy Fields

```java
// ❌ BAD: toString includes lazy collection
@Entity
public class Author {
    @OneToMany(mappedBy = "author", fetch = FetchType.LAZY)
    private List<Book> books;

    @Override
    public String toString() {
        return "Author{id=" + id + ", books=" + books + "}";  // Triggers lazy load!
    }
}

// ✅ GOOD: Exclude lazy fields from toString
@Override
public String toString() {
    return "Author{id=" + id + ", name='" + name + "'}";
}
```
