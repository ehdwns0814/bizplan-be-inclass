# [#010] REQ-FUNC-011-BE-001: HWP/PDF 내보내기 기능

## 📋 Issue Metadata
- **Issue Number**: #010
- **Epic**: EPIC 1 - 과제 통과 Job (To pass the test)
- **Type**: `feature`
- **Component**: `backend`, `api`, `export`
- **Priority**: `Must`
- **Estimated Effort**: M
- **Parallelizable**: ❌ No (Depends on #009)

## 📅 Roadmap Schedule
- **Phase**: Phase 2 - AI Pipeline & Integration (Export)
- **Start Date**: 2026-01-02 (Thursday)
- **End Date**: 2026-01-03 (Friday)
- **Duration**: 2 days
- **Week**: Week 3
- **⚠️ Blocker**: Cannot start until #009 is complete

## 🎯 목적
생성된 사업계획서를 HWP(한글) 또는 PDF 형식으로 내보내는 기능을 구현합니다.

## 📝 설명
JSON 형태로 저장된 사업계획서 데이터를 HWP 또는 PDF 파일로 변환하여 다운로드할 수 있는 API를 구현합니다.

## ✅ 범위 (In-Scope)
- PDF 내보내기 API (`POST /api/v1/projects/{id}/export/pdf`)
- HWP 내보내기 API (`POST /api/v1/projects/{id}/export/hwp`)
- 파일 다운로드 API (`GET /api/v1/exports/{exportId}/download`)
- 템플릿 기반 문서 생성
- 비동기 파일 생성

## ❌ 제외 범위 (Out-of-Scope)
- 실시간 편집
- 클라우드 저장소 연동
- 워터마크 기능

## 🔨 구현 힌트
1. **Package Structure**:
   ```
   api/
     ├── ExportController
     └── dto/
         ├── ExportRequest
         └── ExportResponse
   business/
     ├── service/ExportService
     ├── service/PdfGeneratorService
     └── service/HwpGeneratorService
   infra/
     └── export/
         ├── PdfGenerator (iText)
         └── HwpGenerator (JHWP or LibreOffice)
   ```

2. **API Endpoints**:
   - `POST /api/v1/projects/{projectId}/export/pdf` - PDF 생성 요청
   - `POST /api/v1/projects/{projectId}/export/hwp` - HWP 생성 요청
   - `GET /api/v1/exports/{exportId}/download` - 파일 다운로드
   - `GET /api/v1/exports/{exportId}/status` - 생성 상태 조회

3. **Database Schema**:
   ```sql
   CREATE TABLE export_jobs (
     id BIGSERIAL PRIMARY KEY,
     project_id BIGINT NOT NULL REFERENCES projects(id),
     format VARCHAR(10), -- PDF, HWP
     status VARCHAR(50), -- PENDING, PROCESSING, COMPLETED, FAILED
     file_path VARCHAR(500),
     file_size BIGINT,
     created_at TIMESTAMP,
     completed_at TIMESTAMP
   );
   ```

4. **PDF Generation (iText)**:
   ```java
   @Service
   public class PdfGeneratorService {
       public byte[] generate(GeneratedDocument document) {
           // Use iText to create PDF
       }
   }
   ```

5. **HWP Generation**:
   - Option 1: JHWP Library (Native Korean)
   - Option 2: LibreOffice Headless (Convert from DOCX)

## ✅ 완료 조건
- [ ] API endpoints 구현 완료
- [ ] PDF 생성 라이브러리 연동 (iText)
- [ ] HWP 생성 라이브러리 연동 (JHWP or LibreOffice)
- [ ] 파일 저장 및 다운로드 로직 구현
- [ ] 비동기 처리 구현
- [ ] Unit Tests 작성 (Service Layer)
- [ ] Integration Tests 작성 (Controller Layer)
- [ ] Swagger API Documentation 작성
- [ ] Flyway Migration Script 작성

## 🔗 의존성
- **Depends on**: #009 (문서 생성 완료 필요)
- **Blocks**: None

## 🧪 테스트
### Unit Tests
```java
@ExtendWith(MockitoExtension.class)
class PdfGeneratorServiceTest {
    @Test
    void generate_ShouldCreatePdf_WhenValidDocument() {
        // Test PDF generation
    }
}
```

### Integration Tests
```java
@SpringBootTest
@AutoConfigureMockMvc
class ExportControllerTest {
    @Test
    void exportPdf_ShouldReturn202_WhenValidRequest() {
        // Test async export
    }
    
    @Test
    void downloadFile_ShouldReturnPdf_WhenExportCompleted() {
        // Test file download
    }
}
```

## 📅 로드맵
- **Phase**: Phase 3 (AI Pipeline - Export)
- **Parallel Group**: None (Sequential after #009)

## 🏷️ Labels
`epic-1`, `backend`, `api`, `export`, `feature`, `priority-must`

## 📋 Dependencies (Gradle)
```kotlin
dependencies {
    // PDF Generation
    implementation("com.itextpdf:itext7-core:8.0.2")
    
    // HWP Generation (Option 1)
    implementation("kr.dogfoot:hwplib:1.1.3")
    
    // File handling
    implementation("commons-io:commons-io:2.15.0")
}
```

## 📋 Checklist
- [ ] Build successful (Gradle build)
- [ ] Unit/Integration tests added and passing
- [ ] Code style checked (Checkstyle/Spotless)
- [ ] API Documentation updated (Swagger/OpenAPI)
- [ ] Database migrations tested
- [ ] PDF generation tested
- [ ] HWP generation tested
- [ ] File download tested

---
**Related Tasks**: #009
**Execution Order**: Phase 3 - Sequential (After #009)

