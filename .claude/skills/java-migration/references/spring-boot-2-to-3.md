# Spring Boot Migration

## Spring Boot 2.x → 3.x

**Requirements:**
- Java 17+ (mandatory)
- Jakarta EE 9+ (javax.* → jakarta.*)

**Package Renames:**
```java
// Before (Spring Boot 2.x)
import javax.persistence.*;
import javax.validation.*;
import javax.servlet.*;

// After (Spring Boot 3.x)
import jakarta.persistence.*;
import jakarta.validation.*;
import jakarta.servlet.*;
```

**Find & Replace:**
```bash
# Find all javax imports that need migration
grep -r "import javax\." --include="*.java" src/ | grep -v "javax.crypto" | grep -v "javax.net"
```

**Automated migration:**
```bash
# Use OpenRewrite
mvn -U org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST \
  -Drewrite.activeRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_0
```

### Dependency Updates (Spring Boot 3.x)

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.2</version>
</parent>

<!-- Hibernate 6 (auto-included) -->
<!-- Spring Security 6 (auto-included) -->
```

### Hibernate 5 → 6 Changes

```java
// ID generation strategy changed
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)  // preferred
private Long id;

// Query changes
// Before: createQuery returns raw type
// After: createQuery requires type parameter

// Before
Query query = session.createQuery("from User");

// After
TypedQuery<User> query = session.createQuery("from User", User.class);
```
