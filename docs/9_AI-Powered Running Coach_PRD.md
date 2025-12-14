# Product Requirements Document (PRD) – MVP Version

**Document ID:** PRD-MVP-001  
**Product Name:** Coached (MVP)  
**Version:** 0.1  
**Last Updated:** 2024-12-07  
**Owner:** Product & Engineering Team

---

## 1. Executive Summary

### 1.1 Purpose
본 MVP는 **러닝 자세 분석 기반 부상 예방 앱의 핵심 가치 제안을 검증**하기 위한 최소기능제품입니다.

### 1.2 MVP Goal
**"10초 촬영만으로 부상 위험을 객관적으로 진단받을 수 있는가?"**를 검증

### 1.3 Success Metrics (3개월 내)
| Metric | Target |
|--------|--------|
| 첫 분석 완료율 | ≥ 80% |
| 재촬영 비율 (사용자 재방문) | ≥ 30% |
| 앱 평점 (iOS) | ≥ 4.0 |
| AI 인식 성공률 | ≥ 70% |

---

## 2. Problem Statement (MVP Focus)

### 2.1 Core Problem
러너의 **65%가 부상을 경험**하지만, 자세 문제를 객관적으로 진단받을 방법이 없습니다.

**Pain Points (MVP에서 해결할 문제)**
- 정형외과 진단: 1회 ₩30,000~50,000 + 2~3시간 소요
- 주관적 조언: 커뮤니티/지인의 경험담은 과학적 근거 부족
- 접근성: 전문가 코칭은 비용 부담 (1회 ₩100,000)

### 2.2 Target User (Primary Persona Only)
**민준 (32세, 부상 경험 러너)**
- 러닝 경력: 3년차
- 과거 부상: 장경인대염 재발
- 핵심 니즈: "내 자세가 다시 나빠지고 있는지 확인하고 싶다"

---

## 3. Solution (MVP Scope)

### 3.1 Core Value Proposition
**"스마트폰 하나로 30초 만에 부상 위험을 확인하세요"**

### 3.2 Key Features (Must-Have Only)

#### Feature 1: 10초 영상 촬영
**What:** 사용자가 측면에서 러닝하는 모습을 10초간 촬영
**Why:** 전신 동작 캡처 최소 시간 (Vision AI 인식 기준)
**Acceptance Criteria:**
- 자동 10초 녹화 완료
- 재촬영 버튼 제공
- 촬영 가이드 UI (측면 2~3m 거리)

#### Feature 2: AI 자세 분석
**What:** Apple Vision Framework로 17개 관절 추출 → 3~4개 핵심 지표 계산
**Why:** On-device 처리로 개인정보 보호 + 빠른 결과 제공
**Acceptance Criteria:**
- 분석 완료 시간 ≤ 30초 (평균)
- 무릎 각도, 케이던스, 상체 기울기 계산
- 위험도 3단계 표시 (Safe/Caution/Danger)

#### Feature 3: 결과 표시
**What:** 텍스트 기반 간단한 피드백 UI
**Why:** 3D 시각화는 Phase 2로 연기 (개발 시간 단축)
**Acceptance Criteria:**
- 지표별 수치 + 정상 범위 대비 표시
- 위험 항목에 대한 간단한 설명 (2~3문장)
- 색상 코드 (🔴 위험 / 🟡 주의 / 🟢 안전)

#### Feature 4: 로컬 저장
**What:** 분석 결과를 디바이스 내 JSON으로 저장
**Why:** 서버 비용 제로 + 개인정보 보호
**Acceptance Criteria:**
- 최근 5개 기록 리스트 조회 가능
- 각 기록 클릭 시 상세 결과 재표시

### 3.3 Explicitly Excluded (Out of Scope)
❌ 서버 연동 (사용자 계정, 클라우드 저장)  
❌ 3D 스켈레톤 시각화  
❌ 푸시 알림  
❌ 광고 및 결제  
❌ 비교 리포트 (이전 분석 대비)  
❌ Android 앱  

---

## 4. User Flow (MVP)

```
[앱 실행] 
   ↓
[촬영 가이드 화면]
- "측면 2~3m에서 10초간 촬영하세요"
- 예시 이미지 제공
   ↓
[촬영 시작] (자동 10초 녹화)
   ↓
[분석 중...] (20~30초 대기)
- 진행 표시: "관절 인식 중..."
   ↓
[결과 화면]
- 핵심 지표 3개 카드
- 위험도 표시 (Safe/Caution/Danger)
- "재촬영" / "기록 보기" 버튼
   ↓
[기록 탭]
- 최근 5개 분석 결과 리스트
```

---

## 5. Technical Requirements (MVP)

### 5.1 Platform
- iOS 15+ (iPhone 12 이상)
- 단일 플랫폼 앱

### 5.2 Architecture
- **100% On-device 처리**
- 네트워크 의존성: 없음
- 데이터 저장: CoreData (로컬)

### 5.3 Performance Targets
| Metric | Target |
|--------|--------|
| 분석 시간 (평균) | ≤ 20초 |
| AI 인식 성공률 | ≥ 70% |
| 앱 크래시율 | ≤ 1% |
| 메모리 사용량 | ≤ 200MB |

### 5.4 Core Data Model (Simplified)

```json
{
  "id": "UUID",
  "createdAt": "2024-12-07T10:30:00Z",
  "metrics": {
    "kneeAngle": 125.5,
    "cadence": 170,
    "upperBodyLean": 5.2
  },
  "riskLevel": "Caution"
}
```

---

## 6. Success Criteria & Validation

### 6.1 MVP Validation Framework

**Hypothesis:**  
"러너들은 10초 촬영만으로 자세 피드백을 받을 수 있다면, 정기적으로 앱을 사용할 것이다."

**Validation Method:**
- 베타 테스트 50명 (2주간)
- 측정 지표:
  - 첫 분석 완료율 ≥ 80%
  - 재촬영 비율 ≥ 30% (1주일 내)
  - 앱 평점 ≥ 4.0/5.0

**Success Criteria:**
- 3개 지표 모두 목표 달성 시 → Phase 1 진행
- 1개 이상 미달 시 → 원인 분석 후 Pivot

### 6.2 Key Risks & Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| Vision AI 인식 실패율 > 30% | 사용자 이탈 | 촬영 가이드 강화, 실패 시 구체적 피드백 |
| 분석 시간 > 45초 | 대기 중 이탈 | 프레임 간소화, 진행 상태 표시 |
| 결과 이해도 낮음 | 재사용 의지 하락 | 전문 용어 최소화, 시각적 색상 코드 |

---

## 7. Go-to-Market (MVP Launch)

### 7.1 Launch Strategy
**Soft Launch (Beta)**
- 타겟: 러닝 커뮤니티 50명 (초대제)
- 기간: 2주
- 목표: 피드백 수집 + 크리티컬 버그 수정

**Public Launch**
- iOS App Store 출시
- 완전 무료 (광고/결제 없음)

### 7.2 Marketing Message
**"30초면 충분합니다. 부상 위험, 지금 확인하세요."**

- 채널: 러닝 커뮤니티 (인스타그램, 러닝 크루)
- 콘텐츠: 촬영 → 결과 확인 시연 영상 (15초)

---

## 8. Timeline & Resources

### 8.1 Development Timeline (6주)

| Week | Milestone |
|------|-----------|
| 1-2 | 비디오 촬영 UI + Vision Framework 통합 |
| 3-4 | 지표 계산 로직 + 결과 화면 UI |
| 5 | 로컬 저장 + 기록 조회 기능 |
| 6 | 버그 수정 + 베타 테스트 준비 |

### 8.2 Team
- iOS 개발자: 1명 (풀타임)
- 디자이너: 0.5명 (UI 가이드만)
- PM: 0.3명 (요구사항 정리 + 테스트)

### 8.3 Budget
- 개발 인력: 내부 리소스 활용
- Apple Developer 계정: $99/년
- 서버 비용: $0 (On-device)
- **총 비용: $99**

---

## 9. Post-MVP Roadmap (Phase 1 Preview)

**If MVP succeeds (목표 달성 시):**
- 3D 스켈레톤 시각화
- 비교 리포트 (이전 분석 대비)
- 푸시 알림 (재촬영 리마인더)
- 광고 모델 도입

**If MVP fails (목표 미달 시):**
- Pivot 1: 타겟 사용자 변경 (입문자 → 부상 러너)
- Pivot 2: 촬영 방식 변경 (10초 → 3초 스냅샷)
- Pivot 3: 서비스 중단 및 학습 정리

---

## 10. Appendix

### 10.1 Related Documents
- `SRS-MVP-001`: 기술 상세 요구사항
- `8_2_JTBD 인터뷰 결과`: 사용자 니즈 검증 데이터

### 10.2 Decision Log
| Date | Decision | Rationale |
|------|----------|-----------|
| 2024-12-06 | 3D 시각화 제외 | 개발 시간 3주 단축 |
| 2024-12-06 | 서버 연동 제외 | 비용 제로 + 개인정보 보호 |
| 2024-12-06 | iOS 우선 출시 | 타겟 사용자의 80%가 iPhone 사용 |

---

**Document Status:** Draft  
**Next Review:** Post-Beta Test (2주 후)  
**Approval Required:** Engineering Lead, Product Owner