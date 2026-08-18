# Interface Segregation Principle (ISP)

## I - Interface Segregation Principle (ISP)

> "Clients should not be forced to depend on interfaces they do not use."

### Violation

```java
// ❌ BAD: Fat interface forces unnecessary implementations
public interface Worker {
    void work();
    void eat();
    void sleep();
    void attendMeeting();
    void writeReport();
}

// Robot can't eat or sleep!
public class Robot implements Worker {
    @Override public void work() { /* OK */ }
    @Override public void eat() { /* Can't eat! */ }
    @Override public void sleep() { /* Can't sleep! */ }
    @Override public void attendMeeting() { /* OK */ }
    @Override public void writeReport() { /* Maybe */ }
}

// Intern doesn't attend meetings or write reports
public class Intern implements Worker {
    @Override public void work() { /* OK */ }
    @Override public void eat() { /* OK */ }
    @Override public void sleep() { /* OK */ }
    @Override public void attendMeeting() { /* Not allowed! */ }
    @Override public void writeReport() { /* Not expected! */ }
}
```

### Refactored

```java
// ✅ GOOD: Segregated interfaces

public interface Workable {
    void work();
}

public interface Feedable {
    void eat();
    void sleep();
}

public interface Manageable {
    void attendMeeting();
    void writeReport();
}

// Combine what you need
public class Employee implements Workable, Feedable, Manageable {
    @Override public void work() { /* ... */ }
    @Override public void eat() { /* ... */ }
    @Override public void sleep() { /* ... */ }
    @Override public void attendMeeting() { /* ... */ }
    @Override public void writeReport() { /* ... */ }
}

public class Robot implements Workable {
    @Override public void work() { /* ... */ }
    // No unnecessary methods!
}

public class Intern implements Workable, Feedable {
    @Override public void work() { /* ... */ }
    @Override public void eat() { /* ... */ }
    @Override public void sleep() { /* ... */ }
    // No meeting/report methods!
}
```

### How to Detect ISP Violations

- Implementations with empty methods or `throw new UnsupportedOperationException()`
- Interface has 10+ methods
- Different clients use completely different subsets of methods
- Changes to interface affect unrelated implementations

### Java Standard Library Violations

```java
// java.util.List has many methods - but this is acceptable for collections
// However, be careful with your own interfaces!

// ❌ This interface is too fat for most use cases
public interface Repository<T> {
    T findById(Long id);
    List<T> findAll();
    T save(T entity);
    void delete(T entity);
    void deleteById(Long id);
    List<T> findByExample(T example);
    Page<T> findAll(Pageable pageable);
    List<T> findAllById(Iterable<Long> ids);
    long count();
    boolean existsById(Long id);
    // ... 20 more methods
}

// ✅ Better: Split by use case
public interface ReadRepository<T> {
    Optional<T> findById(Long id);
    List<T> findAll();
}

public interface WriteRepository<T> {
    T save(T entity);
    void delete(T entity);
}
```
