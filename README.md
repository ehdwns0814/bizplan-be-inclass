# Coached Backend Service

> A robust, scalable, and secure backend infrastructure for the "Coached" AI-powered running coach application

[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.0.0-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Gradle](https://img.shields.io/badge/Gradle-8.x-blue.svg)](https://gradle.org/)

## 📋 Table of Contents

- [Vision](#-vision)
- [Core Features](#-core-features)
- [Technical Stack](#-technical-stack)
- [Architecture](#-architecture)
- [Getting Started](#-getting-started)
- [Project Structure](#-project-structure)
- [Development Guidelines](#-development-guidelines)
- [Testing Strategy](#-testing-strategy)
- [API Documentation](#-api-documentation)
- [Success Metrics](#-success-metrics)
- [Contributing](#-contributing)

## 🎯 Vision

To provide a robust, scalable, and secure backend infrastructure for the "Coached" AI-powered running coach application, enabling user data persistence, cross-device synchronization, and advanced analytics (Post-MVP features).

## ✨ Core Features

### User Management
- Authentication and secure identity handling
- Profile management
- Cross-device user data synchronization

### Data Synchronization
- Cloud storage for running analysis results (JSON/Metrics)
- Real-time data sync across devices

### Event Processing
- Asynchronous processing of analysis events
- Advanced insights generation (Post-MVP)

### Notification Service
- Push notifications for user engagement
- Re-analysis reminders
- Personalized coaching alerts

## 🛠 Technical Stack

### Platform
- **Runtime**: Java 21 (LTS)
- **Framework**: Spring Boot 4.0.0
- **Build Tool**: Gradle (Kotlin DSL preferred)
- **Deployment**: Docker / Kubernetes

### Data & Persistence
- **Primary Database**: PostgreSQL (Relational Data)
- **Cache & Session**: Redis (Lettuce/Redisson)
- **Messaging**: Apache Kafka (Event Streaming)
- **ORM**: Spring Data JPA / Hibernate

### Key Libraries
- **Validation**: Hibernate Validator
- **Testing**: JUnit 5, Mockito, Testcontainers
- **API Documentation**: SpringDoc OpenAPI (Swagger)
- **Microservices**: Spring Cloud (if applicable)

## 🏗 Architecture

### Architecture Pattern
This project follows **Hexagonal Architecture** (Ports & Adapters) / **Layered Architecture** principles with clear separation of concerns.

### Package Structure
```
com.example.project
├── api             // Controllers, Request/Response DTOs
├── business        // Service Layer, Business Logic
│   ├── service
│   └── domain      // Domain Models (POJO)
├── data            // JPA Entities, Repositories
├── global          // Global configs, Exceptions, Utils
│   ├── config
│   ├── error
│   └── util
└── infra           // External integrations (Kafka, Redis, 3rd Party APIs)
```

### Architecture Principles
- **Clean Architecture**: Separation of concerns (Domain, Application, Infrastructure)
- **Event-Driven**: Utilizing Kafka for decoupled service interactions
- **Test-Driven**: Comprehensive testing with Testcontainers
- **Documentation First**: API-first design using OpenAPI/Swagger

### Key Design Patterns
- **Saga Pattern**: For distributed transaction management
- **Repository Pattern**: Data access abstraction
- **DTO Pattern**: Separation between entities and API contracts
- **Factory Pattern**: Object creation abstraction

## 🚀 Getting Started

### Prerequisites
- Java 21 or higher
- Gradle 8.x
- Docker & Docker Compose
- PostgreSQL 15+
- Redis 7+
- Apache Kafka 3.x

### Installation

1. **Clone the repository**
```bash
git clone <repository-url>
cd bizplan-be-inclass
```

2. **Set up environment variables**

Create a `.env` file in the project root:
```properties
# Database Configuration
DB_HOST=localhost
DB_PORT=5432
DB_NAME=coached_db
DB_USERNAME=your_username
DB_PASSWORD=your_password

# Redis Configuration
REDIS_HOST=localhost
REDIS_PORT=6379

# Kafka Configuration
KAFKA_BOOTSTRAP_SERVERS=localhost:9092

# Application Configuration
SERVER_PORT=8080
SPRING_PROFILES_ACTIVE=dev
```

3. **Build the project**
```bash
./gradlew clean build
```

4. **Run with Docker Compose** (Recommended for local development)
```bash
docker-compose up -d
```

5. **Run the application**
```bash
./gradlew bootRun
```

The application will be available at `http://localhost:8080`

### Running Tests
```bash
# Run all tests
./gradlew test

# Run with coverage
./gradlew test jacocoTestReport

# Run integration tests only
./gradlew integrationTest
```

## 📁 Project Structure

```
bizplan-be-inclass/
├── .cursor/
│   └── rules/              # Project-specific coding rules and guidelines
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── vibe/bizplan/
│   │   │       └── bizplan_be_inclass/
│   │   │           ├── api/          # Controllers & DTOs
│   │   │           ├── business/     # Services & Domain
│   │   │           ├── data/         # Entities & Repositories
│   │   │           ├── global/       # Configs & Utilities
│   │   │           └── infra/        # External Integrations
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── application-dev.properties
│   │       ├── application-prod.properties
│   │       └── db/migration/         # Flyway migrations
│   └── test/
│       ├── java/                     # Unit & Integration tests
│       └── resources/                # Test configurations
├── build.gradle
├── settings.gradle
├── docker-compose.yml
└── README.md
```

## 📝 Development Guidelines

### Code Standards

#### DTO (Data Transfer Object)
- **Never expose JPA Entities directly** in APIs
- Use Record classes (Java 14+) or `@Value` (Lombok) for immutability
- Implement static factory methods (`from`, `toEntity`) for conversion

#### Controller Layer
- Validate inputs using `@Valid`
- Delegate business logic to Service layer
- Return DTOs wrapped in standardized `ApiResponse<T>`

#### Service Layer
- Use `@Transactional(readOnly = true)` at class level by default
- Apply `@Transactional` on methods that modify data
- Implement core business rules here

#### Dependency Injection
- **Always use Constructor Injection** via `@RequiredArgsConstructor`
- **Avoid Field Injection** (`@Autowired` on fields)

### REST API Design

#### URI Conventions
- Use kebab-case: `/api/v1/user-profiles`
- Use plural nouns: `/users`, not `/user`
- Always include versioning: `/api/v1/...`
- Use nesting for related resources: `/users/{id}/orders`

#### HTTP Methods & Status Codes
| Method | Action | Success Status | Common Error Status |
|--------|--------|----------------|---------------------|
| GET | Retrieve | 200 OK | 404 Not Found |
| POST | Create | 201 Created | 400 Bad Request, 422 Unprocessable Entity |
| PUT | Update/Replace | 200 OK | 404 Not Found, 400 Bad Request |
| PATCH | Partial Update | 200 OK | 404 Not Found, 400 Bad Request |
| DELETE | Delete | 204 No Content | 404 Not Found |

#### Response Format (Envelope Pattern)
```json
{
  "result": "SUCCESS",
  "data": { ... },
  "message": "Operation completed successfully",
  "errorCode": null
}
```

### Database Best Practices

#### Entity Design
- **No `@Setter` on Entities** - use domain-specific methods
- Extend `BaseTimeEntity` for audit fields (created_at, updated_at)
- Use `Long` with `@GeneratedValue(strategy = GenerationType.IDENTITY)`

#### Performance Optimization
- **Prevent N+1 problems**: Use Fetch Join or `@EntityGraph`
- **Always use Lazy Loading**: Set `fetch = FetchType.LAZY`
- Configure batch size: `spring.jpa.properties.hibernate.default_batch_fetch_size`

#### Schema Management
- Use **Flyway** for versioned database migrations
- Set `spring.jpa.hibernate.ddl-auto=validate` in production
- Use snake_case for table and column names

### Logging
- Use `@Slf4j` (Lombok)
- Log levels:
  - `INFO`: Application flow
  - `ERROR`: Exceptions and errors
  - `DEBUG`: Detailed data for debugging
  - `WARN`: Warning conditions

### Constants & Configuration
- Avoid magic numbers/strings
- Use `static final` constants or Enums
- Externalize configuration to `application.properties`

## 🧪 Testing Strategy

### Testing Pyramid
Prioritize: **Unit Tests > Integration Tests > E2E Tests**

### Test Naming Convention
Use descriptive names following the pattern:
```
methodName_ShouldExpectedBehavior_WhenCondition
```

Example: `createUser_ShouldReturnId_WhenInputIsValid`

### Given-When-Then Pattern
```java
@Test
void register_ShouldSaveUser_WhenValidInput() {
    // Given
    UserDto dto = new UserDto("test@example.com", "password");
    
    // When
    Long userId = userService.register(dto);
    
    // Then
    assertThat(userId).isNotNull();
    verify(userRepository).save(any());
}
```

### Unit Tests
- **Framework**: JUnit 5 + Mockito
- **No Spring Context**: Use `@ExtendWith(MockitoExtension.class)`
- **Speed**: Must run fast (< 100ms)
- **Coverage**: > 80% for business logic

### Integration Tests
- **Use Test Slices**:
  - `@WebMvcTest` for Controllers
  - `@DataJpaTest` for Repositories
- **Use `@SpringBootTest`** only when testing full flow

### Testcontainers
Use Testcontainers for external dependencies instead of H2 or embedded mocks:
- PostgreSQL
- Redis
- Kafka

```java
@SpringBootTest
@Testcontainers
class UserIntegrationTest {
    @Container
    static PostgreSQLContainer<?> postgres = 
        new PostgreSQLContainer<>("postgres:15");
    
    // ... test implementation
}
```

### Coverage Goals
- **Business Logic**: > 80% line coverage
- **Critical Paths** (Auth, Payment, Data Critical): 100% coverage

## 📚 API Documentation

API documentation is automatically generated using **SpringDoc OpenAPI**.

### Accessing Swagger UI
```
http://localhost:8080/swagger-ui.html
```

### OpenAPI Specification
```
http://localhost:8080/v3/api-docs
```

### Annotating APIs
```java
@Operation(summary = "Create a new user", description = "Registers a new user in the system")
@ApiResponses(value = {
    @ApiResponse(responseCode = "201", description = "User created successfully"),
    @ApiResponse(responseCode = "400", description = "Invalid input")
})
@PostMapping("/api/v1/users")
public ResponseEntity<ApiResponse<UserDto>> createUser(@Valid @RequestBody UserCreateRequest request) {
    // implementation
}
```

## 📊 Success Metrics

### Performance
- **API Latency**: P99 < 200ms for core endpoints
- **Throughput**: Support 1000+ concurrent requests

### Reliability
- **Uptime**: 99.9% availability
- **Data Integrity**: Zero data loss in pipeline processing

### Quality
- **Test Coverage**: > 80% for business logic
- **Code Quality**: SonarQube quality gate passed

## 🎯 Target Audience

### Primary
- **Mobile App (iOS)** clients consuming REST APIs

### Secondary
- **Admin/Data Analysts** monitoring system performance and user metrics

## 🔄 Git Workflow

This project follows **Git Flow** branching strategy:

### Branch Types
- `main`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: New features
- `bugfix/*`: Bug fixes
- `hotfix/*`: Urgent production fixes
- `release/*`: Release preparation

### Commit Message Format
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

Example:
```
feat(auth): implement JWT token refresh mechanism

- Add refresh token endpoint
- Implement token rotation strategy
- Add integration tests

Closes #123
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes following commit conventions
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Pull Request Guidelines
- Follow the project's coding standards
- Include tests for new features
- Update documentation as needed
- Ensure all tests pass
- Get code review approval

## 📞 Support

For questions or support, please contact:
- **Project Lead**: [Your Name]
- **Email**: [your.email@example.com]
- **Issue Tracker**: GitHub Issues

## 📄 License

This project is licensed under the [MIT License](LICENSE).

## 🙏 Acknowledgments

- Spring Boot Team for the excellent framework
- All contributors who have helped shape this project

---

**Built with ❤️ by the Coached Backend Team**

