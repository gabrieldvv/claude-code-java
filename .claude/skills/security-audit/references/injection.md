# Injection Prevention (SQL, XSS, CSRF)

## SQL Injection Prevention

### JPA/Hibernate (All Frameworks)

```java
// ✅ GOOD: Parameterized queries
@Query("SELECT u FROM User u WHERE u.email = :email")
Optional<User> findByEmail(@Param("email") String email);

// ✅ GOOD: Criteria API
CriteriaBuilder cb = entityManager.getCriteriaBuilder();
CriteriaQuery<User> query = cb.createQuery(User.class);
Root<User> user = query.from(User.class);
query.where(cb.equal(user.get("email"), email));  // Safe

// ✅ GOOD: Named parameters
TypedQuery<User> query = entityManager.createQuery(
    "SELECT u FROM User u WHERE u.status = :status", User.class);
query.setParameter("status", status);  // Safe

// ❌ BAD: String concatenation
String jpql = "SELECT u FROM User u WHERE u.email = '" + email + "'";  // VULNERABLE!
```

### Native Queries

```java
// ✅ GOOD: Parameterized native query
@Query(value = "SELECT * FROM users WHERE email = ?1", nativeQuery = true)
User findByEmailNative(String email);

// ❌ BAD: Concatenated native query
String sql = "SELECT * FROM users WHERE email = '" + email + "'";  // VULNERABLE!
```

### JDBC (Plain Java)

```java
// ✅ GOOD: PreparedStatement
String sql = "SELECT * FROM users WHERE email = ? AND status = ?";
try (PreparedStatement stmt = connection.prepareStatement(sql)) {
    stmt.setString(1, email);
    stmt.setString(2, status);
    ResultSet rs = stmt.executeQuery();
}

// ❌ BAD: Statement with concatenation
String sql = "SELECT * FROM users WHERE email = '" + email + "'";  // VULNERABLE!
Statement stmt = connection.createStatement();
stmt.executeQuery(sql);
```

---

## XSS Prevention

### Output Encoding

```java
// ✅ GOOD: Use templating engine's auto-escaping

// Thymeleaf - auto-escapes by default
<p th:text="${userInput}">...</p>  // Safe

// To display HTML (dangerous, use carefully):
<p th:utext="${trustedHtml}">...</p>  // Only for trusted content!

// ✅ GOOD: Manual encoding when needed
import org.owasp.encoder.Encode;

String safe = Encode.forHtml(userInput);
String safeJs = Encode.forJavaScript(userInput);
String safeUrl = Encode.forUriComponent(userInput);
```

**Maven dependency for OWASP Encoder:**
```xml
<dependency>
    <groupId>org.owasp.encoder</groupId>
    <artifactId>encoder</artifactId>
    <version>1.2.3</version>
</dependency>
```

### Content Security Policy

```java
// Add CSP header to prevent inline scripts

// Spring Boot
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.headers(headers -> headers
            .contentSecurityPolicy(csp -> csp
                .policyDirectives("default-src 'self'; script-src 'self'; style-src 'self'")
            )
        );
        return http.build();
    }
}

// Servlet Filter (works everywhere)
@WebFilter("/*")
public class SecurityHeadersFilter implements Filter {
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain)
            throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) res;
        response.setHeader("Content-Security-Policy", "default-src 'self'");
        response.setHeader("X-Content-Type-Options", "nosniff");
        response.setHeader("X-Frame-Options", "DENY");
        response.setHeader("X-XSS-Protection", "1; mode=block");
        chain.doFilter(req, res);
    }
}
```

---

## CSRF Protection

### Spring Security

```java
// CSRF enabled by default for browser clients
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // For REST APIs with JWT (stateless) - can disable CSRF
            .csrf(csrf -> csrf.disable())

            // For browser apps with sessions - keep CSRF enabled
            .csrf(csrf -> csrf
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            );
        return http.build();
    }
}
```

### Quarkus

```properties
# application.properties
quarkus.http.csrf.enabled=true
quarkus.http.csrf.cookie-name=XSRF-TOKEN
```
