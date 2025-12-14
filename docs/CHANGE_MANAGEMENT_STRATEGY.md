# Change Management Strategy: PRD-SRS-Task

## 1. 3-Layer Traceability Structure

변경 관리는 다음 3단계 계층의 추적성(Traceability)을 기반으로 수행됩니다.

1. **PRD (Product Requirements)**: `docs/9_AI-Powered Running Coach_PRD.md`
   - 비즈니스 목표, 사용자 니즈, 성공 지표 정의
   - Version: 0.1 (MVP Focus)

2. **SRS (System Requirements)**: `docs/10_1_AI-Powered Running Coach_MVP_SRS.md`
   - 기능 및 비기능 요구사항 상세 명세
   - REQ ID 체계: `MVP-FUNC-001`, `MVP-NF-PERF-001` 등

3. **Task Definitions (Implementation Spec)**: `tasks/**/*.md`
   - 구현 가능한 단위 작업 정의 (YAML 포함)
   - Task ID 체계: `MVP-FUNC-001-CAMERA`, `MVP-FUNC-002-VISION` 등

**Traceability Map Example:**
```
PRD Feature 2 (AI 자세 분석)
  └─> SRS MVP-FUNC-002 (Vision Framework로 17개 관절 추출)
        └─> Task MVP-FUNC-002-VISION (On-Device Vision 기반 Keypoint 추출)
```

---

## 2. Change Scenarios & Workflows

### Scenario A: PRD (Business Goal) 변경 시
- **Trigger**: 시장 상황 변화, 이해관계자 요구 변경 (예: "보폭(Stride Length) 지표 추가해줘")
- **Action**:
  1. PRD 업데이트 및 버전 올림 (`v0.1` → `v0.2`).
  2. 영향을 받는 SRS REQ 식별 및 수정 (`MVP-FUNC-003`).
  3. 해당 SRS ID를 `req_ids`로 가지는 Task(`MVP-FUNC-003-METRICS`)를 찾아 `summary`, `outputs`, `steps_hint` 업데이트.
  4. WBS/DAG에서 영향도 파악 후 일정 재산정.
- **Git Commit**: `[PRD-Update] Add stride length metric (v0.2)`

### Scenario B: SRS (Requirement Details) 변경 시
- **Trigger**: 기술적 제약 발견, 상세 기획 변경 (예: "녹화 영상을 60fps → 30fps로 제한 필요")
- **Action**:
  1. SRS REQ 업데이트 (`MVP-FUNC-001` Acceptance Criteria 수정).
  2. 매핑된 Task(`MVP-FUNC-001-CAMERA`)의 `inputs.config`, `preconditions`, `exceptions` 수정.
  3. Task 상태를 `Pending`으로 되돌리고 에이전트 재할당 (필요 시).
  4. 관련 Task 영향도 분석 (`MVP-FUNC-002-VISION` 등).
- **Git Commit**: `[SRS-Sync] Limit camera to 30fps (MVP-FUNC-001)`

### Scenario C: Task (Implementation) 수준 변경 시
- **Trigger**: 리팩토링, 버그 수정, 최적화 (예: "Vision 프레임 처리 시 autoreleasepool 미사용으로 메모리 누수")
- **Action**:
  1. Task 정의서 내 `steps_hint` 또는 `exceptions` 수정.
  2. 상위 SRS/PRD 변경이 불필요하다면, Task 버전만 올리고 작업 수행.
  3. 코드 수정 후 Unit Test 통과 확인.
- **Git Commit**: `[Task-Update] Optimize Vision memory usage (MVP-FUNC-002)`

---

## 3. Version Control & Sync

- **Version Tags**:
  - PRD: `v{Major}.{Minor}` (예: `v0.1`, `v0.2`)
  - SRS: `v{Major}.{Minor}` (PRD Major 버전과 동기화 권장)
  - Task: `v{Major}.{Minor}` (선택, 구현 방식 변경 시)
- **Change Log**:
  - Git Commit Message에 `[PRD-Update]`, `[SRS-Sync]`, `[Task-Update]` 태그 사용.
  - 선택 사항: `docs/CHANGE_LOG.md`에 중요한 변경 사항 기록.
- **Sync Check**:
  - 정기적으로 SRS의 REQ ID와 Task 파일의 `req_ids`를 대조하여, 누락된 REQ나 삭제된 Feature의 Task가 남아있는지 점검한다.
  - 매 Sprint 종료 시 (2주마다) Traceability 점검 수행.

