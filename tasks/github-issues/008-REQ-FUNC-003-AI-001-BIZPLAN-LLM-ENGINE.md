# [#008] REQ-FUNC-003-AI-001: 사업계획서 생성 LLM 엔진 (FastAPI)

## 📋 Issue Metadata
- **Issue Number**: #008
- **Epic**: EPIC 1 - 과제 통과 Job (To pass the test)
- **Type**: `feature`
- **Component**: `ai`, `fastapi`, `llm`
- **Priority**: `Must`
- **Estimated Effort**: L
- **Parallelizable**: ✅ Yes (Can develop independently, integrates with #009)

## 🎯 목적
사용자 답변을 기반으로 사업계획서를 생성하는 LLM 기반 엔진을 FastAPI로 구현합니다.

## 📝 설명
Wizard에서 수집한 답변 데이터를 입력받아 LLM(GPT-4, Claude 등)을 활용하여 구조화된 사업계획서를 생성하는 AI 서비스를 구축합니다.

## ✅ 범위 (In-Scope)
- FastAPI 기반 LLM 서비스 구축
- LLM Prompt Engineering
- 문서 구조화 (섹션별 생성)
- API Endpoint (`POST /api/v1/generate-bizplan`)
- 비동기 처리 (Celery/RQ)

## ❌ 제외 범위 (Out-of-Scope)
- 실시간 스트리밍 (MVP 단계)
- 다국어 지원
- 커스텀 LLM 파인튜닝

## 🔨 구현 힌트
1. **Project Structure**:
   ```
   fastapi_llm_service/
   ├── main.py
   ├── routers/
   │   └── bizplan.py
   ├── services/
   │   ├── llm_service.py
   │   └── prompt_templates.py
   ├── models/
   │   ├── request_models.py
   │   └── response_models.py
   └── config/
       └── settings.py
   ```

2. **API Endpoint**:
   ```python
   @router.post("/api/v1/generate-bizplan")
   async def generate_bizplan(request: BizPlanRequest):
       # LLM 호출 및 문서 생성
       pass
   ```

3. **Prompt Template Example**:
   ```python
   BIZPLAN_PROMPT = """
   다음 정보를 기반으로 사업계획서를 작성해주세요:
   - 사업 아이디어: {idea}
   - 타겟 시장: {target_market}
   - 예상 매출: {revenue}
   ...
   """
   ```

4. **LLM Integration**:
   - OpenAI API 또는 Anthropic API 사용
   - 섹션별 생성 (Executive Summary, Market Analysis, etc.)
   - JSON 응답 구조화

## ✅ 완료 조건
- [ ] FastAPI 서비스 구축 완료
- [ ] LLM API 연동 완료
- [ ] Prompt Template 작성 및 테스트
- [ ] 문서 구조화 로직 구현
- [ ] 비동기 처리 구현 (Celery/RQ)
- [ ] Unit Tests 작성
- [ ] Integration Tests 작성
- [ ] API Documentation (Swagger/ReDoc)
- [ ] 환경 변수 설정 (.env)

## 🔗 의존성
- **Depends on**: #007 (답변 데이터 구조 파악 필요)
- **Blocks**: #009 (Spring Boot 오케스트레이션)

## 🧪 테스트
### Unit Tests
```python
def test_generate_bizplan_success():
    # Given
    mock_answers = {...}
    # When
    result = llm_service.generate(mock_answers)
    # Then
    assert result['sections'] is not None
```

### Integration Tests
```python
@pytest.mark.asyncio
async def test_bizplan_api_endpoint():
    response = await client.post("/api/v1/generate-bizplan", json=payload)
    assert response.status_code == 200
```

## 📅 로드맵
- **Phase**: Phase 3 (AI Pipeline)
- **Parallel Group**: AI Development (Can develop independently)

## 🏷️ Labels
`epic-1`, `ai`, `fastapi`, `llm`, `feature`, `priority-must`

## 📋 Environment Variables
```env
OPENAI_API_KEY=your_api_key
LLM_MODEL=gpt-4
LLM_TEMPERATURE=0.7
MAX_TOKENS=4000
```

## 🔧 Dependencies
```txt
fastapi==0.109.0
uvicorn==0.27.0
openai==1.10.0
pydantic==2.5.3
celery==5.3.4
redis==5.0.1
```

---
**Related Tasks**: #007, #009
**Execution Order**: Phase 3 - Parallel (AI Development)

