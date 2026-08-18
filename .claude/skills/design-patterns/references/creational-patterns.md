# Creational Patterns

## Creational Patterns

### Builder

**Use when:** Object has many parameters, some optional.

```java
// ❌ Telescoping constructor antipattern
public class User {
    public User(String name) { }
    public User(String name, String email) { }
    public User(String name, String email, int age) { }
    public User(String name, String email, int age, String phone) { }
    // ... explosion of constructors
}

// ✅ Builder pattern
public class User {
    private final String name;      // required
    private final String email;     // required
    private final int age;          // optional
    private final String phone;     // optional
    private final String address;   // optional

    private User(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.age = builder.age;
        this.phone = builder.phone;
        this.address = builder.address;
    }

    public static Builder builder(String name, String email) {
        return new Builder(name, email);
    }

    public static class Builder {
        // Required
        private final String name;
        private final String email;
        // Optional with defaults
        private int age = 0;
        private String phone = "";
        private String address = "";

        private Builder(String name, String email) {
            this.name = name;
            this.email = email;
        }

        public Builder age(int age) {
            this.age = age;
            return this;
        }

        public Builder phone(String phone) {
            this.phone = phone;
            return this;
        }

        public Builder address(String address) {
            this.address = address;
            return this;
        }

        public User build() {
            return new User(this);
        }
    }
}

// Usage
User user = User.builder("John", "john@example.com")
    .age(30)
    .phone("+1234567890")
    .build();
```

**With Lombok:**
```java
@Builder
@Getter
public class User {
    private final String name;
    private final String email;
    @Builder.Default private int age = 0;
    private String phone;
}
```

---

### Factory Method

**Use when:** Need to create objects without specifying exact class.

```java
// ✅ Factory Method pattern
public interface Notification {
    void send(String message);
}

public class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Email: " + message);
    }
}

public class SmsNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("SMS: " + message);
    }
}

public class PushNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("Push: " + message);
    }
}

// Factory
public class NotificationFactory {

    public static Notification create(String type) {
        return switch (type.toUpperCase()) {
            case "EMAIL" -> new EmailNotification();
            case "SMS" -> new SmsNotification();
            case "PUSH" -> new PushNotification();
            default -> throw new IllegalArgumentException("Unknown type: " + type);
        };
    }
}

// Usage
Notification notification = NotificationFactory.create("EMAIL");
notification.send("Hello!");
```

**With Spring (preferred):**
```java
public interface NotificationSender {
    void send(String message);
    String getType();
}

@Component
public class EmailSender implements NotificationSender {
    @Override public void send(String message) { /* ... */ }
    @Override public String getType() { return "EMAIL"; }
}

@Component
public class SmsSender implements NotificationSender {
    @Override public void send(String message) { /* ... */ }
    @Override public String getType() { return "SMS"; }
}

@Component
public class NotificationFactory {
    private final Map<String, NotificationSender> senders;

    public NotificationFactory(List<NotificationSender> senderList) {
        this.senders = senderList.stream()
            .collect(Collectors.toMap(
                NotificationSender::getType,
                Function.identity()
            ));
    }

    public NotificationSender getSender(String type) {
        return Optional.ofNullable(senders.get(type))
            .orElseThrow(() -> new IllegalArgumentException("Unknown: " + type));
    }
}
```

---

### Singleton

**Use when:** Exactly one instance needed (use sparingly!).

```java
// ✅ Modern singleton (enum-based, thread-safe)
public enum DatabaseConnection {
    INSTANCE;

    private Connection connection;

    DatabaseConnection() {
        // Initialize connection
    }

    public Connection getConnection() {
        return connection;
    }
}

// Usage
Connection conn = DatabaseConnection.INSTANCE.getConnection();
```

**With Spring (preferred):**
```java
@Component  // Default scope is singleton
public class DatabaseConnection {
    // Spring manages single instance
}
```

**Warning:** Singletons can be problematic:
- Hard to test (global state)
- Hidden dependencies
- Consider dependency injection instead
