# [#007] REQ-FUNC-002-BE-001: Wizard 단계별 답변 저장/조회 API

## 📋 Issue Metadata
- **Issue Number**: #007
- **Epic**: EPIC 1 - 과제 통과 Job (To pass the test)
- **Type**: `feature`
- **Component**: `backend`, `api`, `wizard`
- **Priority**: `Must`
- **Estimated Effort**: M
- **Parallelizable**: ❌ No (Depends on #006)

## 📅 Roadmap Schedule
- **Phase**: Phase 1 - Core Backend Foundation
- **Start Date**: 2025-12-19 (Thursday)
- **End Date**: 2025-12-20 (Friday)
- **Duration**: 2 days
- **Week**: Week 1
- **⚠️ Blocker**: Cannot start until #006 is complete

## 🎯 목적
사업계획서 작성 Wizard의 단계별 답변을 저장하고 조회하는 API를 구현합니다.

## 📝 설명
사용자가 Wizard에서 입력한 답변을 단계별로 저장하고, 자동저장 기능을 지원하며, 이전 답변을 조회할 수 있는 API를 구현합니다.

## ✅ 범위 (In-Scope)
- 답변 저장 API (`POST /api/v1/projects/{id}/wizard/answers`)
- 답변 조회 API (`GET /api/v1/projects/{id}/wizard/answers`)
- 단계별 답변 조회 API (`GET /api/v1/projects/{id}/wizard/steps/{stepId}/answers`)
- 자동저장 지원 (Idempotent API)

## ❌ 제외 범위 (Out-of-Scope)
- 답변 검증 로직 (기본 검증만)
- 버전 관리
- 협업 편집

## 🔨 구현 힌트
1. **Package Structure**:
   ```
   api/
     ├── WizardController
     └── dto/
         ├── WizardAnswerRequest
         ├── WizardAnswerResponse
         └── WizardStepResponse
   business/
     ├── service/WizardService
     └── domain/WizardAnswer
   data/
     ├── entity/WizardAnswerEntity
     └── repository/WizardAnswerRepository
   ```

2. **API Endpoints**:
   - `POST /api/v1/projects/{projectId}/wizard/answers` - 답변 저장
   - `GET /api/v1/projects/{projectId}/wizard/answers` - 전체 답변 조회
   - `GET /api/v1/projects/{projectId}/wizard/steps/{stepId}/answers` - 단계별 조회

3. **Database Schema**:
   ```sql
   CREATE TABLE wizard_answers (
     id BIGSERIAL PRIMARY KEY,
     project_id BIGINT NOT NULL REFERENCES projects(id),
     step_id VARCHAR(50) NOT NULL,
     question_id VARCHAR(100) NOT NULL,
     answer_type VARCHAR(50),
     answer_value TEXT,
     created_at TIMESTAMP,
     updated_at TIMESTAMP,
     UNIQUE(project_id, step_id, question_id)
   );
   ```

## ✅ 완료 조건
- [ ] API endpoints 구현 완료
- [ ] JPA Entities 및 Repositories 구현
- [ ] Service Layer 비즈니스 로직 구현
- [ ] 자동저장 Idempotent 처리 구현
- [ ] Unit Tests 작성 (Service Layer)
- [ ] Integration Tests 작성 (Controller Layer)
- [ ] Swagger API Documentation 작성
- [ ] Flyway Migration Script 작성

## 🔗 의존성
- **Depends on**: #006 (Project API 필요)
- **Blocks**: #008 (문서 생성 오케스트레이션)

## 🧪 테스트
### Unit Tests
```java
@ExtendWith(MockitoExtension.class)
class WizardServiceTest {
    @Test
    void saveAnswer_ShouldUpsert_WhenSameQuestionAnswered() {
        // Test idempotent behavior
    }
}
```

### Integration Tests
```java
@SpringBootTest
@AutoConfigureMockMvc
class WizardControllerTest {
    @Test
    void saveAnswer_ShouldReturn200_WhenValidRequest() {
        // Test auto-save
    }
}
```

## 📅 로드맵
- **Phase**: Phase 2 (Core BE)
- **Parallel Group**: None (Sequential after #006)

## 🏷️ Labels
`epic-1`, `backend`, `api`, `feature`, `priority-must`, `wizard`

## 📋 Checklist
- [ ] Build successful (Gradle build)
- [ ] Unit/Integration tests added and passing
- [ ] Code style checked (Checkstyle/Spotless)
- [ ] API Documentation updated (Swagger/OpenAPI)
- [ ] Database migrations tested
- [ ] Idempotent behavior verified

---
**Related Tasks**: #006, #008
**Execution Order**: Phase 2 - Sequential (After #006)

