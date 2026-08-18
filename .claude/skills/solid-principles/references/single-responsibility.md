# Single Responsibility Principle (SRP)

## S - Single Responsibility Principle (SRP)

> "A class should have only one reason to change."

### Violation

```java
// ❌ BAD: UserService does too much
public class UserService {

    public User createUser(String name, String email) {
        // validation logic
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }

        // persistence logic
        User user = new User(name, email);
        entityManager.persist(user);

        // notification logic
        String subject = "Welcome!";
        String body = "Hello " + name;
        emailClient.send(email, subject, body);

        // audit logic
        auditLog.log("User created: " + email);

        return user;
    }
}
```

**Problems:**
- Validation changes? Modify UserService
- Email template changes? Modify UserService
- Audit format changes? Modify UserService
- Hard to test each concern separately

### Refactored

```java
// ✅ GOOD: Each class has one responsibility

public class UserValidator {
    public void validate(String name, String email) {
        if (email == null || !email.contains("@")) {
            throw new ValidationException("Invalid email");
        }
    }
}

public class UserRepository {
    public User save(User user) {
        entityManager.persist(user);
        return user;
    }
}

public class WelcomeEmailSender {
    public void sendWelcome(User user) {
        String subject = "Welcome!";
        String body = "Hello " + user.getName();
        emailClient.send(user.getEmail(), subject, body);
    }
}

public class UserAuditLogger {
    public void logCreation(User user) {
        auditLog.log("User created: " + user.getEmail());
    }
}

public class UserService {
    private final UserValidator validator;
    private final UserRepository repository;
    private final WelcomeEmailSender emailSender;
    private final UserAuditLogger auditLogger;

    public User createUser(String name, String email) {
        validator.validate(name, email);
        User user = repository.save(new User(name, email));
        emailSender.sendWelcome(user);
        auditLogger.logCreation(user);
        return user;
    }
}
```

### How to Detect SRP Violations

- Class has many `import` statements from different domains
- Class name contains "And" or "Manager" or "Handler" (often)
- Methods operate on unrelated data
- Changes in one area require touching unrelated methods
- Hard to name the class concisely

### Quick Check Questions

1. Can you describe the class purpose in one sentence without "and"?
2. Would different stakeholders request changes to this class?
3. Are there methods that don't use most of the class fields?
