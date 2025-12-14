# [#011] REQ-FUNC-008-AI-001: PMF 진단 및 리포트 생성 LLM 엔진

## 📋 Issue Metadata
- **Issue Number**: #011
- **Epic**: EPIC 2 - 실패 회피 Job (To avoid failure)
- **Type**: `feature`
- **Component**: `ai`, `fastapi`, `pmf`
- **Priority**: `Should`
- **Estimated Effort**: M
- **Parallelizable**: ✅ Yes (Can develop with #012)

## 📅 Roadmap Schedule
- **Phase**: Phase 3 - Special Features
- **Start Date**: 2026-01-06 (Monday)
- **End Date**: 2026-01-08 (Wednesday)
- **Duration**: 3 days
- **Week**: Week 4
- **✅ Parallel Development**: Can run simultaneously with #012
- **⚠️ Blocker**: Cannot start until #008 is complete (shares LLM infrastructure)

## 🎯 목적
Product-Market Fit(PMF) 진단을 위한 설문 분석 및 리포트 생성 LLM 엔진을 구현합니다.

## 📝 설명
사용자의 PMF 설문 응답을 분석하여 시장 적합성을 평가하고, AI 기반 인사이트와 개선 제안이 포함된 리포트를 생성합니다.

## ✅ 범위 (In-Scope)
- PMF 설문 분석 API (`POST /api/v1/pmf/analyze`)
- PMF 점수 계산 알고리즘
- AI 기반 인사이트 생성
- 개선 제안 생성
- 리포트 구조화 (JSON)

## ❌ 제외 범위 (Out-of-Scope)
- 설문 문항 관리 (고정 문항 사용)
- 통계적 벤치마킹
- 산업별 커스터마이징

## 🔨 구현 힌트
1. **API Endpoint**:
   ```python
   @router.post("/api/v1/pmf/analyze")
   async def analyze_pmf(request: PMFAnalysisRequest):
       # PMF 점수 계산 및 인사이트 생성
       pass
   ```

2. **PMF Score Calculation**:
   ```python
   def calculate_pmf_score(responses: dict) -> float:
       # Sean Ellis Test 기반 점수 계산
       # "매우 실망할 것" 응답 비율 계산
       pass
   ```

3. **Prompt Template**:
   ```python
   PMF_ANALYSIS_PROMPT = """
   다음 PMF 설문 결과를 분석하여 리포트를 작성해주세요:
   
   설문 응답:
   {responses}
   
   PMF 점수: {pmf_score}/100
   
   다음 내용을 포함해주세요:
   1. 현재 PMF 수준 평가
   2. 주요 강점과 약점
   3. 구체적인 개선 제안
   4. 우선순위별 액션 아이템
   """
   ```

4. **Response Structure**:
   ```json
   {
     "pmf_score": 68.5,
     "level": "MODERATE",
     "insights": {
       "strengths": ["..."],
       "weaknesses": ["..."]
     },
     "recommendations": [
       {
         "priority": "HIGH",
         "category": "PRODUCT",
         "action": "..."
       }
     ]
   }
   ```

## ✅ 완료 조건
- [ ] FastAPI 엔드포인트 구현
- [ ] PMF 점수 계산 알고리즘 구현
- [ ] LLM 기반 인사이트 생성 구현
- [ ] 리포트 구조화 로직 구현
- [ ] Unit Tests 작성
- [ ] Integration Tests 작성
- [ ] API Documentation 작성

## 🔗 의존성
- **Depends on**: #008 (LLM 인프라 공유)
- **Blocks**: None

## 🧪 테스트
### Unit Tests
```python
def test_calculate_pmf_score():
    responses = {
        "disappointment": "very_disappointed",
        "alternatives": "none"
    }
    score = calculate_pmf_score(responses)
    assert 0 <= score <= 100
```

### Integration Tests
```python
@pytest.mark.asyncio
async def test_pmf_analysis_endpoint():
    payload = {"responses": {...}}
    response = await client.post("/api/v1/pmf/analyze", json=payload)
    assert response.status_code == 200
    assert "pmf_score" in response.json()
```

## 📅 로드맵
- **Phase**: Phase 4 (Special Features)
- **Parallel Group**: Special Features (Can run with #012)

## 🏷️ Labels
`epic-2`, `ai`, `fastapi`, `pmf`, `feature`, `priority-should`

## 📋 PMF Survey Questions
```yaml
questions:
  - id: disappointment
    text: "이 제품을 더 이상 사용할 수 없다면 어떤 느낌이 드시나요?"
    options: ["very_disappointed", "somewhat_disappointed", "not_disappointed"]
  
  - id: benefit
    text: "이 제품의 주요 혜택은 무엇인가요?"
    type: "text"
  
  - id: alternatives
    text: "대체 솔루션을 사용하신다면 무엇인가요?"
    type: "text"
```

---
**Related Tasks**: #008, #012
**Execution Order**: Phase 4 - Parallel (Special Features)

