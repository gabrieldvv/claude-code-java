# Input Validation

## Input Validation (All Frameworks)

### Bean Validation (JSR 380)

Works in Spring, Quarkus, Jakarta EE, and standalone.

```java
// ✅ GOOD: Validate at boundary
public class CreateUserRequest {

    @NotNull(message = "Username is required")
    @Size(min = 3, max = 50, message = "Username must be 3-50 characters")
    @Pattern(regexp = "^[a-zA-Z0-9_]+$", message = "Username can only contain letters, numbers, underscore")
    private String username;

    @NotNull
    @Email(message = "Invalid email format")
    private String email;

    @NotNull
    @Size(min = 8, max = 100)
    @Pattern(regexp = "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d).*$",
             message = "Password must contain uppercase, lowercase, and number")
    private String password;

    @Min(value = 0, message = "Age cannot be negative")
    @Max(value = 150, message = "Invalid age")
    private Integer age;
}

// Controller/Resource - trigger validation
public Response createUser(@Valid CreateUserRequest request) {
    // request is already validated
}
```

### Custom Validators

```java
// Custom annotation
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = SafeHtmlValidator.class)
public @interface SafeHtml {
    String message() default "Contains unsafe HTML";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

// Validator implementation
public class SafeHtmlValidator implements ConstraintValidator<SafeHtml, String> {

    private static final Pattern DANGEROUS_PATTERN = Pattern.compile(
        "<script|javascript:|on\\w+\\s*=", Pattern.CASE_INSENSITIVE
    );

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        if (value == null) return true;
        return !DANGEROUS_PATTERN.matcher(value).find();
    }
}
```

### Allowlist vs Blocklist

```java
// ❌ BAD: Blocklist (attackers find bypasses)
if (input.contains("<script>")) {
    throw new ValidationException("Invalid input");
}

// ✅ GOOD: Allowlist (only permit known-good)
private static final Pattern SAFE_NAME = Pattern.compile("^[a-zA-Z\\s'-]{1,100}$");

if (!SAFE_NAME.matcher(input).matches()) {
    throw new ValidationException("Invalid name format");
}
```
