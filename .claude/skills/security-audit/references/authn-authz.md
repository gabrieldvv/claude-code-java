# Authentication & Authorization

## Authentication & Authorization

### Password Storage

```java
// ✅ GOOD: Use BCrypt or Argon2
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.argon2.Argon2PasswordEncoder;

// BCrypt (widely supported)
PasswordEncoder encoder = new BCryptPasswordEncoder(12);  // strength 12
String hash = encoder.encode(rawPassword);
boolean matches = encoder.matches(rawPassword, hash);

// Argon2 (recommended for new projects)
PasswordEncoder encoder = Argon2PasswordEncoder.defaultsForSpringSecurity_v5_8();
String hash = encoder.encode(rawPassword);

// ❌ BAD: MD5, SHA1, SHA256 without salt
String hash = DigestUtils.md5Hex(password);  // NEVER for passwords!
```

### Authorization Checks

```java
// ✅ GOOD: Check authorization at service layer
@Service
public class DocumentService {

    public Document getDocument(Long documentId, User currentUser) {
        Document doc = documentRepository.findById(documentId)
            .orElseThrow(() -> new NotFoundException("Document not found"));

        // Authorization check
        if (!doc.getOwnerId().equals(currentUser.getId()) &&
            !currentUser.hasRole("ADMIN")) {
            throw new AccessDeniedException("Not authorized to access this document");
        }

        return doc;
    }
}

// ❌ BAD: Only check at controller level, trust user input
@GetMapping("/documents/{id}")
public Document getDocument(@PathVariable Long id) {
    return documentRepository.findById(id).orElseThrow();  // No auth check!
}
```

### Spring Security Annotations

```java
@PreAuthorize("hasRole('ADMIN')")
public void adminOnly() { }

@PreAuthorize("hasRole('USER') and #userId == authentication.principal.id")
public void ownDataOnly(Long userId) { }

@PreAuthorize("@authService.canAccess(#documentId, authentication)")
public Document getDocument(Long documentId) { }
```
