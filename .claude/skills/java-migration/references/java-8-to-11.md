# Java 8 → 11 Migration

## Removed APIs

| Removed | Replacement |
|---------|-------------|
| `javax.xml.bind` (JAXB) | Add dependency: `jakarta.xml.bind-api` + `jaxb-runtime` |
| `javax.activation` | Add dependency: `jakarta.activation-api` |
| `javax.annotation` | Add dependency: `jakarta.annotation-api` |
| `java.corba` | No replacement (rarely used) |
| `java.transaction` | Add dependency: `jakarta.transaction-api` |
| `sun.misc.Base64*` | Use `java.util.Base64` |
| `sun.misc.Unsafe` (partially) | Use `VarHandle` where possible |

### Add Missing Dependencies (Maven)

```xml
<!-- JAXB (if needed) -->
<dependency>
    <groupId>jakarta.xml.bind</groupId>
    <artifactId>jakarta.xml.bind-api</artifactId>
    <version>4.0.1</version>
</dependency>
<dependency>
    <groupId>org.glassfish.jaxb</groupId>
    <artifactId>jaxb-runtime</artifactId>
    <version>4.0.4</version>
    <scope>runtime</scope>
</dependency>

<!-- Annotation API -->
<dependency>
    <groupId>jakarta.annotation</groupId>
    <artifactId>jakarta.annotation-api</artifactId>
    <version>2.1.1</version>
</dependency>
```

### Module System Issues

If using reflection on JDK internals, add JVM flags:
```bash
--add-opens java.base/java.lang=ALL-UNNAMED
--add-opens java.base/java.util=ALL-UNNAMED
```

**Maven Surefire:**
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <argLine>
            --add-opens java.base/java.lang=ALL-UNNAMED
        </argLine>
    </configuration>
</plugin>
```

### New Features to Adopt

```java
// var (local variable type inference)
var list = new ArrayList<String>();  // instead of ArrayList<String> list = ...

// String methods
"  hello  ".isBlank();      // true for whitespace-only
"  hello  ".strip();        // better trim() (Unicode-aware)
"line1\nline2".lines();     // Stream<String>
"ha".repeat(3);             // "hahaha"

// Collection factory methods (Java 9+)
List.of("a", "b", "c");     // immutable list
Set.of(1, 2, 3);            // immutable set
Map.of("k1", "v1");         // immutable map

// Optional improvements
optional.ifPresentOrElse(
    value -> process(value),
    () -> handleEmpty()
);

// HTTP Client (replaces HttpURLConnection)
HttpClient client = HttpClient.newHttpClient();
HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com"))
    .build();
HttpResponse<String> response = client.send(request,
    HttpResponse.BodyHandlers.ofString());
```
