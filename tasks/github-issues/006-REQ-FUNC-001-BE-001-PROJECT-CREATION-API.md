# [#006] REQ-FUNC-001-BE-001: 프로젝트 생성 및 템플릿 목록 API

## 📋 Issue Metadata
- **Issue Number**: #006
- **Epic**: EPIC 1 - 과제 통과 Job (To pass the test)
- **Type**: `feature`
- **Component**: `backend`, `api`, `project`
- **Priority**: `Must`
- **Estimated Effort**: M
- **Parallelizable**: ❌ No (Foundation task)

## 🎯 목적
사업계획서 프로젝트 생성 및 템플릿 목록 조회를 위한 RESTful API를 구현합니다.

## 📝 설명
사용자가 새로운 사업계획서 프로젝트를 생성하고, 사용 가능한 템플릿 목록을 조회할 수 있는 백엔드 API를 구현합니다.

## ✅ 범위 (In-Scope)
- 프로젝트 생성 API (`POST /api/v1/projects`)
- 템플릿 목록 조회 API (`GET /api/v1/templates`)
- 프로젝트 상세 조회 API (`GET /api/v1/projects/{id}`)
- JPA Entity 설계 (Project, Template)

## ❌ 제외 범위 (Out-of-Scope)
- 사용자 인증 (추후 구현)
- 프로젝트 공유 기능
- 복잡한 권한 관리

## 🔨 구현 힌트
1. **Package Structure**:
   ```
   api/
     ├── ProjectController
     └── dto/
         ├── ProjectCreateRequest
         ├── ProjectResponse
         └── TemplateResponse
   business/
     ├── service/ProjectService
     └── domain/Project
   data/
     ├── entity/ProjectEntity, TemplateEntity
     └── repository/ProjectRepository, TemplateRepository
   ```

2. **API Endpoints**:
   - `POST /api/v1/projects` - 프로젝트 생성
   - `GET /api/v1/projects/{id}` - 프로젝트 조회
   - `GET /api/v1/templates` - 템플릿 목록

3. **Database Schema**:
   ```sql
   CREATE TABLE projects (
     id BIGSERIAL PRIMARY KEY,
     title VARCHAR(255) NOT NULL,
     template_id BIGINT,
     status VARCHAR(50),
     created_at TIMESTAMP,
     updated_at TIMESTAMP
   );
   
   CREATE TABLE templates (
     id BIGSERIAL PRIMARY KEY,
     name VARCHAR(255) NOT NULL,
     description TEXT,
     category VARCHAR(100)
   );
   ```

## ✅ 완료 조건
- [ ] API endpoints 구현 완료
- [ ] JPA Entities 및 Repositories 구현
- [ ] Service Layer 비즈니스 로직 구현
- [ ] DTO <-> Entity 매핑 구현
- [ ] Unit Tests 작성 (Service Layer)
- [ ] Integration Tests 작성 (Controller Layer)
- [ ] Swagger API Documentation 작성
- [ ] Flyway Migration Script 작성

## 🔗 의존성
- **Depends on**: None (최초 백엔드 작업, Frontend PoC는 별도 프로젝트에서 완료)
- **Blocks**: #007 (Wizard API), #009 (문서 생성 API), #013 (보안), #014 (로깅)

## 🧪 테스트
### Unit Tests
```java
@ExtendWith(MockitoExtension.class)
class ProjectServiceTest {
    @Test
    void createProject_ShouldReturnProjectId_WhenValidInput() {
        // Given-When-Then
    }
}
```

### Integration Tests
```java
@SpringBootTest
@AutoConfigureMockMvc
class ProjectControllerTest {
    @Test
    void createProject_ShouldReturn201_WhenValidRequest() {
        // Test with MockMvc
    }
}
```

## 📅 로드맵
- **Phase**: Phase 2 (Core BE)
- **Parallel Group**: None (Foundation)

## 🏷️ Labels
`epic-1`, `backend`, `api`, `feature`, `priority-must`, `project`

## 📋 Checklist
- [ ] Build successful (Gradle build)
- [ ] Unit/Integration tests added and passing
- [ ] Code style checked (Checkstyle/Spotless)
- [ ] API Documentation updated (Swagger/OpenAPI)
- [ ] Database migrations tested
- [ ] Environment variables documented

---
**Related Tasks**: #007, #011
**Execution Order**: Phase 2 - Foundation (Must complete first)

