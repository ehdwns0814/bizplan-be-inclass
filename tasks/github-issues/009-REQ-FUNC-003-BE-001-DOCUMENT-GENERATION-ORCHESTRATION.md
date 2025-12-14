# [#009] REQ-FUNC-003-BE-001: 사업계획서 생성 오케스트레이션 API (Spring Boot)

## 📋 Issue Metadata
- **Issue Number**: #009
- **Epic**: EPIC 1 - 과제 통과 Job (To pass the test)
- **Type**: `feature`
- **Component**: `backend`, `api`, `orchestration`
- **Priority**: `Must`
- **Estimated Effort**: L
- **Parallelizable**: ❌ No (Depends on #007, #008)

## 🎯 목적
Wizard 답변 수집부터 AI 생성, 문서 저장까지 전체 플로우를 오케스트레이션하는 API를 구현합니다.

## 📝 설명
사용자 요청을 받아 Wizard 답변을 조회하고, FastAPI LLM 서비스를 호출하여 문서를 생성하며, 결과를 DB에 저장하는 전체 프로세스를 관리하는 API를 구현합니다.

## ✅ 범위 (In-Scope)
- 문서 생성 요청 API (`POST /api/v1/projects/{id}/generate`)
- 생성 상태 조회 API (`GET /api/v1/projects/{id}/generation-status`)
- FastAPI 서비스 호출 (WebClient)
- 비동기 처리 (Spring Async)
- 생성된 문서 저장

## ❌ 제외 범위 (Out-of-Scope)
- 실시간 진행률 업데이트 (WebSocket)
- 문서 버전 관리
- 협업 기능

## 🔨 구현 힌트
1. **Package Structure**:
   ```
   api/
     ├── DocumentGenerationController
     └── dto/
         ├── GenerationRequest
         ├── GenerationStatusResponse
         └── GeneratedDocumentResponse
   business/
     ├── service/DocumentGenerationService
     ├── service/LLMClientService (WebClient)
     └── domain/GeneratedDocument
   data/
     ├── entity/GeneratedDocumentEntity
     └── repository/GeneratedDocumentRepository
   ```

2. **API Endpoints**:
   - `POST /api/v1/projects/{projectId}/generate` - 생성 시작
   - `GET /api/v1/projects/{projectId}/generation-status` - 상태 조회
   - `GET /api/v1/projects/{projectId}/documents/{docId}` - 문서 조회

3. **Database Schema**:
   ```sql
   CREATE TABLE generated_documents (
     id BIGSERIAL PRIMARY KEY,
     project_id BIGINT NOT NULL REFERENCES projects(id),
     status VARCHAR(50), -- PENDING, PROCESSING, COMPLETED, FAILED
     content JSONB,
     error_message TEXT,
     created_at TIMESTAMP,
     completed_at TIMESTAMP
   );
   ```

4. **Orchestration Flow**:
   ```java
   @Async
   public CompletableFuture<GeneratedDocument> generateDocument(Long projectId) {
       // 1. Wizard 답변 조회
       // 2. FastAPI 호출 (WebClient)
       // 3. 결과 저장
       // 4. 상태 업데이트
   }
   ```

## ✅ 완료 조건
- [ ] API endpoints 구현 완료
- [ ] WebClient로 FastAPI 연동 완료
- [ ] 비동기 처리 구현 (@Async)
- [ ] 상태 관리 로직 구현
- [ ] 에러 핸들링 구현
- [ ] Unit Tests 작성 (Service Layer)
- [ ] Integration Tests 작성 (Controller Layer)
- [ ] Swagger API Documentation 작성
- [ ] Flyway Migration Script 작성

## 🔗 의존성
- **Depends on**: #007 (Wizard API), #008 (FastAPI LLM)
- **Blocks**: #010 (Export 기능), #015 (Performance Test)

## 🧪 테스트
### Unit Tests
```java
@ExtendWith(MockitoExtension.class)
class DocumentGenerationServiceTest {
    @Test
    void generateDocument_ShouldCallLLMService_WhenValidProject() {
        // Given-When-Then
    }
}
```

### Integration Tests
```java
@SpringBootTest
@AutoConfigureMockMvc
class DocumentGenerationControllerTest {
    @Test
    void generateDocument_ShouldReturn202_WhenValidRequest() {
        // Test async processing
    }
}
```

## 📅 로드맵
- **Phase**: Phase 3 (AI Pipeline)
- **Parallel Group**: None (Integration task)

## 🏷️ Labels
`epic-1`, `backend`, `api`, `orchestration`, `feature`, `priority-must`

## 📋 Checklist
- [ ] Build successful (Gradle build)
- [ ] Unit/Integration tests added and passing
- [ ] Code style checked (Checkstyle/Spotless)
- [ ] API Documentation updated (Swagger/OpenAPI)
- [ ] Database migrations tested
- [ ] WebClient configuration tested
- [ ] Async processing verified

---
**Related Tasks**: #007, #008, #010
**Execution Order**: Phase 3 - Integration (After #007, #008)

