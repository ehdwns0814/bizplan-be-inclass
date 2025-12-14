# [#013] REQ-NF-006-SEC-001: 데이터 저장/전송 암호화 및 보안 구성

## 📋 Issue Metadata
- **Issue Number**: #013
- **Epic**: EPIC 3 - Non-Functional & Operations
- **Type**: `non-functional`
- **Component**: `backend`, `security`
- **Priority**: `Must`
- **Estimated Effort**: M
- **Parallelizable**: ✅ Yes (Can develop with #014, #015)

## 🎯 목적
민감한 사업 데이터를 보호하기 위한 암호화 및 보안 구성을 구현합니다.

## 📝 설명
데이터 저장 시 암호화, HTTPS 통신, 보안 헤더 설정, 입력 검증 등 전반적인 보안 구성을 적용합니다.

## ✅ 범위 (In-Scope)
- 민감 데이터 암호화 (AES-256)
- HTTPS 강제 적용
- Security Headers 설정
- CORS 설정
- Input Validation & Sanitization
- SQL Injection 방어
- XSS 방어

## ❌ 제외 범위 (Out-of-Scope)
- OAuth2 인증 (추후 구현)
- 침입 탐지 시스템 (IDS)
- DDoS 방어 (인프라 레벨)

## 🔨 구현 힌트
1. **Package Structure**:
   ```
   global/
     ├── config/
     │   ├── SecurityConfig
     │   └── CorsConfig
     ├── security/
     │   ├── EncryptionService
     │   └── AuditLogger
     └── error/
         └── SecurityExceptionHandler
   ```

2. **Data Encryption**:
   ```java
   @Service
   public class EncryptionService {
       private static final String ALGORITHM = "AES/GCM/NoPadding";
       
       @Value("${encryption.secret-key}")
       private String secretKey;
       
       public String encrypt(String plainText) {
           // AES-256-GCM encryption
       }
       
       public String decrypt(String encryptedText) {
           // Decryption logic
       }
   }
   ```

3. **Security Configuration**:
   ```java
   @Configuration
   @EnableWebSecurity
   public class SecurityConfig {
       
       @Bean
       public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
           http
               .csrf(csrf -> csrf.disable()) // For API, use token-based auth
               .cors(cors -> cors.configurationSource(corsConfigurationSource()))
               .headers(headers -> headers
                   .contentSecurityPolicy("default-src 'self'")
                   .xssProtection()
                   .frameOptions().deny()
                   .httpStrictTransportSecurity()
               )
               .requiresChannel(channel -> channel
                   .anyRequest().requiresSecure() // Force HTTPS
               );
           return http.build();
       }
   }
   ```

4. **Input Validation**:
   ```java
   @RestController
   @Validated
   public class ProjectController {
       
       @PostMapping("/api/v1/projects")
       public ResponseEntity<?> createProject(
           @Valid @RequestBody ProjectCreateRequest request) {
           // Validated input
       }
   }
   
   public class ProjectCreateRequest {
       @NotBlank(message = "Title is required")
       @Size(max = 255, message = "Title must be less than 255 characters")
       @Pattern(regexp = "^[a-zA-Z0-9가-힣 ._-]+$", message = "Invalid characters in title")
       private String title;
   }
   ```

5. **Sensitive Data Handling**:
   ```java
   @Entity
   @Table(name = "financial_data")
   public class FinancialDataEntity extends BaseTimeEntity {
       
       @Convert(converter = EncryptedStringConverter.class)
       @Column(name = "revenue", nullable = false)
       private String revenue; // Encrypted at rest
       
       @Convert(converter = EncryptedStringConverter.class)
       @Column(name = "cost_structure")
       private String costStructure;
   }
   
   @Converter
   public class EncryptedStringConverter implements AttributeConverter<String, String> {
       @Autowired
       private EncryptionService encryptionService;
       
       @Override
       public String convertToDatabaseColumn(String attribute) {
           return encryptionService.encrypt(attribute);
       }
       
       @Override
       public String convertToEntityAttribute(String dbData) {
           return encryptionService.decrypt(dbData);
       }
   }
   ```

6. **Security Headers**:
   ```yaml
   # application.yml
   spring:
     security:
       headers:
         content-security-policy: "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'"
         strict-transport-security: "max-age=31536000; includeSubDomains"
         x-content-type-options: nosniff
         x-frame-options: DENY
         x-xss-protection: "1; mode=block"
   ```

## ✅ 완료 조건
- [ ] 암호화 서비스 구현 완료
- [ ] Spring Security 설정 완료
- [ ] Security Headers 적용
- [ ] CORS 설정 완료
- [ ] Input Validation 적용
- [ ] Sensitive Data 암호화 적용
- [ ] Unit Tests 작성
- [ ] Security Audit 수행
- [ ] 보안 설정 문서화

## 🔗 의존성
- **Depends on**: #006 (기본 API 필요)
- **Blocks**: None

## 🧪 테스트
### Unit Tests
```java
@ExtendWith(MockitoExtension.class)
class EncryptionServiceTest {
    @Test
    void encrypt_ShouldReturnDifferentValue_WhenSameInputEncryptedTwice() {
        // Test encryption randomness (GCM mode)
    }
    
    @Test
    void decryption_ShouldReturnOriginalValue() {
        String original = "sensitive-data";
        String encrypted = service.encrypt(original);
        String decrypted = service.decrypt(encrypted);
        assertThat(decrypted).isEqualTo(original);
    }
}
```

### Security Tests
```java
@SpringBootTest
@AutoConfigureMockMvc
class SecurityConfigTest {
    @Test
    void api_ShouldRejectHttp_AndRequireHttps() {
        // Test HTTPS enforcement
    }
    
    @Test
    void api_ShouldIncludeSecurityHeaders() {
        // Test security headers
    }
    
    @Test
    void api_ShouldRejectMaliciousInput() {
        // Test XSS, SQL injection prevention
    }
}
```

## 📅 로드맵
- **Phase**: Phase 5 (NFR & QA)
- **Parallel Group**: NFR (Can run with #014, #015)

## 🏷️ Labels
`epic-3`, `non-functional`, `security`, `priority-must`

## 📋 Security Checklist
- [ ] AES-256-GCM encryption for sensitive data
- [ ] HTTPS enforced (HSTS enabled)
- [ ] Security headers configured
- [ ] CORS properly configured
- [ ] Input validation on all endpoints
- [ ] SQL injection prevented (JPA/Parameterized queries)
- [ ] XSS prevented (output encoding)
- [ ] Secrets stored in environment variables (not in code)
- [ ] Audit logging for sensitive operations

## 📋 Environment Variables
```env
# Encryption
ENCRYPTION_SECRET_KEY=your-32-byte-base64-encoded-key
ENCRYPTION_ALGORITHM=AES/GCM/NoPadding

# Security
ALLOWED_ORIGINS=https://bizplan-frontend.com
HTTPS_ONLY=true
```

---
**Related Tasks**: #006, #014, #015
**Execution Order**: Phase 5 - Parallel (NFR)

