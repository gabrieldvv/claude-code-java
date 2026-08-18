# Secrets, Deserialization, Dependencies & Hardening

## Secrets Management

### Never Hardcode Secrets

```java
// ❌ BAD: Hardcoded secrets
private static final String API_KEY = "sk-1234567890abcdef";
private static final String DB_PASSWORD = "admin123";

// ✅ GOOD: Environment variables
String apiKey = System.getenv("API_KEY");

// ✅ GOOD: External configuration
@Value("${api.key}")
private String apiKey;

// ✅ GOOD: Secrets manager
@Autowired
private SecretsManager secretsManager;
String apiKey = secretsManager.getSecret("api-key");
```

### Configuration Files

```yaml
# ✅ GOOD: Reference environment variables
spring:
  datasource:
    password: ${DB_PASSWORD}

api:
  key: ${API_KEY}

# ❌ BAD: Hardcoded in application.yml
spring:
  datasource:
    password: admin123  # NEVER!
```

### .gitignore

```gitignore
# Never commit these
.env
*.pem
*.key
*credentials*
*secret*
application-local.yml
```

---

## Secure Deserialization

### Avoid Java Serialization

```java
// ❌ DANGEROUS: Java ObjectInputStream
ObjectInputStream ois = new ObjectInputStream(untrustedInput);
Object obj = ois.readObject();  // Remote Code Execution risk!

// ✅ GOOD: Use JSON with Jackson
ObjectMapper mapper = new ObjectMapper();
// Disable dangerous features
mapper.disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
mapper.activateDefaultTyping(
    LaissezFaireSubTypeValidator.instance,
    ObjectMapper.DefaultTyping.NON_FINAL
);  // Be careful with polymorphic types!

User user = mapper.readValue(json, User.class);
```

### Jackson Security

```java
// ✅ Configure Jackson safely
@Configuration
public class JacksonConfig {

    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper mapper = new ObjectMapper();

        // Prevent unknown properties exploitation
        mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);

        // Don't allow class type in JSON (prevents gadget attacks)
        mapper.deactivateDefaultTyping();

        return mapper;
    }
}
```

---

## Dependency Security

### OWASP Dependency Check

**Maven:**
```xml
<plugin>
    <groupId>org.owasp</groupId>
    <artifactId>dependency-check-maven</artifactId>
    <version>9.0.7</version>
    <executions>
        <execution>
            <goals>
                <goal>check</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <failBuildOnCVSS>7</failBuildOnCVSS>  <!-- Fail on high severity -->
    </configuration>
</plugin>
```

**Run:**
```bash
mvn dependency-check:check
# Report: target/dependency-check-report.html
```

### Keep Dependencies Updated

```bash
# Check for updates
mvn versions:display-dependency-updates

# Update to latest
mvn versions:use-latest-releases
```

---

## Security Headers

### Recommended Headers

| Header | Value | Purpose |
|--------|-------|---------|
| `Content-Security-Policy` | `default-src 'self'` | Prevent XSS |
| `X-Content-Type-Options` | `nosniff` | Prevent MIME sniffing |
| `X-Frame-Options` | `DENY` | Prevent clickjacking |
| `Strict-Transport-Security` | `max-age=31536000` | Force HTTPS |
| `X-XSS-Protection` | `1; mode=block` | Legacy XSS filter |

### Spring Boot Configuration

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http.headers(headers -> headers
        .contentSecurityPolicy(csp -> csp.policyDirectives("default-src 'self'"))
        .frameOptions(frame -> frame.deny())
        .httpStrictTransportSecurity(hsts -> hsts.maxAgeInSeconds(31536000))
        .contentTypeOptions(Customizer.withDefaults())
    );
    return http.build();
}
```

---

## Logging Security Events

```java
// ✅ Log security-relevant events
log.info("User login successful", kv("userId", userId), kv("ip", clientIp));
log.warn("Failed login attempt", kv("username", username), kv("ip", clientIp), kv("attempt", attemptCount));
log.warn("Access denied", kv("userId", userId), kv("resource", resourceId), kv("action", action));
log.error("Authentication failure", kv("reason", reason), kv("ip", clientIp));

// ❌ NEVER log sensitive data
log.info("Login: user={}, password={}", username, password);  // NEVER!
log.debug("Request body: {}", requestWithCreditCard);  // NEVER!
```
