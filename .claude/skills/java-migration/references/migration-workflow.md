# Migration Workflow

## Migration Workflow

### Step 1: Assess Current State

```bash
# Check current Java version
java -version

# Check compiler target in Maven
grep -r "maven.compiler" pom.xml

# Find usage of removed APIs
grep -r "sun\." --include="*.java" src/
grep -r "javax\.xml\.bind" --include="*.java" src/
```

### Step 2: Update Build Configuration

**Maven:**
```xml
<properties>
    <java.version>21</java.version>
    <maven.compiler.source>${java.version}</maven.compiler.source>
    <maven.compiler.target>${java.version}</maven.compiler.target>
</properties>

<!-- Or with compiler plugin -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.12.1</version>
    <configuration>
        <release>21</release>
    </configuration>
</plugin>
```

**Gradle:**
```groovy
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(21)
    }
}
```

### Step 3: Fix Compilation Errors

Run compile and fix errors iteratively:
```bash
mvn clean compile 2>&1 | head -50
```

### Step 4: Run Tests

```bash
mvn test
```

### Step 5: Check Runtime Warnings

```bash
# Run with illegal-access warnings
java --illegal-access=warn -jar app.jar
```

---

## Common Migration Issues

### Issue: Reflection Access Denied

**Symptom:**
```
java.lang.reflect.InaccessibleObjectException: Unable to make field accessible
```

**Fix:**
```bash
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.base/java.lang.reflect=ALL-UNNAMED
```

### Issue: JAXB ClassNotFoundException

**Symptom:**
```
java.lang.ClassNotFoundException: javax.xml.bind.JAXBContext
```

**Fix:** Add JAXB dependencies (see Java 8→11 section)

### Issue: Lombok Not Working

**Fix:** Update Lombok to latest version:
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.30</version>
</dependency>
```

### Issue: Test Failures with Mockito

**Fix:** Update Mockito:
```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.8.0</version>
    <scope>test</scope>
</dependency>
```

---

## Migration Checklist

### Pre-Migration
- [ ] Document current Java version
- [ ] List all dependencies and their versions
- [ ] Identify usage of internal APIs (`sun.*`, `com.sun.*`)
- [ ] Check framework compatibility (Spring, Hibernate, etc.)
- [ ] Backup / create branch

### During Migration
- [ ] Update build tool configuration
- [ ] Add missing Jakarta dependencies
- [ ] Fix `javax.*` → `jakarta.*` imports (if Spring Boot 3)
- [ ] Add `--add-opens` flags if needed
- [ ] Update Lombok, Mockito, other tools
- [ ] Fix compilation errors
- [ ] Run tests

### Post-Migration
- [ ] Remove unnecessary `--add-opens` flags
- [ ] Adopt new language features (records, var, etc.)
- [ ] Update CI/CD pipeline
- [ ] Document changes made

---

## Quick Commands

```bash
# Check Java version
java -version

# Find internal API usage
grep -rn "sun\.\|com\.sun\." --include="*.java" src/

# Find javax imports (for Jakarta migration)
grep -rn "import javax\." --include="*.java" src/

# Compile and show first errors
mvn clean compile 2>&1 | head -100

# Run with verbose module warnings
java --illegal-access=debug -jar app.jar

# OpenRewrite Spring Boot 3 migration
mvn org.openrewrite.maven:rewrite-maven-plugin:run \
  -Drewrite.recipeArtifactCoordinates=org.openrewrite.recipe:rewrite-spring:LATEST \
  -Drewrite.activeRecipes=org.openrewrite.java.spring.boot3.UpgradeSpringBoot_3_0
```

---

## Version Compatibility Matrix

| Framework | Java 8 | Java 11 | Java 17 | Java 21 | Java 25 |
|-----------|--------|---------|---------|---------|---------|
| Spring Boot 2.7.x | ✅ | ✅ | ✅ | ⚠️ | ❌ |
| Spring Boot 3.2.x | ❌ | ❌ | ✅ | ✅ | ✅ |
| Spring Boot 3.4+ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Hibernate 5.6 | ✅ | ✅ | ✅ | ⚠️ | ❌ |
| Hibernate 6.4+ | ❌ | ❌ | ✅ | ✅ | ✅ |
| JUnit 5.10+ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Mockito 5+ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Lombok 1.18.34+ | ✅ | ✅ | ✅ | ✅ | ✅ |

**LTS Support Timeline:**
- Java 21: Oracle free support until September 2028
- Java 25: Oracle free support until September 2033
