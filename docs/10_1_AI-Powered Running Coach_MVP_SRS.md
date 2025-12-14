아래는 기존 문서를 **MVP 목적에 맞게 완전히 경량화한 “MVP 전용 SRS”**야.
속도, 구현 난이도, 비용 최소화를 기준으로 재설계했어.

---

# Software Requirements Specification (SRS) – MVP Version

**Document ID:** SRS-MVP-001
**Product Name:** Coached (MVP)
**Version:** 0.1
**Purpose:** 문제–해결 적합성(Problem–Solution Fit) 검증

---

## 1. Introduction

### 1.1 Purpose

본 문서는 러닝 자세 분석 기반 부상 예방 앱의 **최소기능제품(MVP)** 요구사항을 정의한다.
본 MVP의 목표는 **실제 사용자에게 가치 있는 피드백을 제공할 수 있는지**를 검증하는 것이다.

---

### 1.2 Scope

#### In-Scope (MVP 포함 기능)

* iOS 단일 플랫폼 기반 모바일 앱
* 10초 러닝 영상 촬영
* On-device 키포인트 추출
* 핵심 러닝 지표 계산(3~4개)
* 간단한 텍스트 기반 피드백 UI
* 로컬 저장 기능

#### Out-of-Scope (MVP 제외 기능)

* 서버 연동
* 사용자 계정 시스템
* 광고 및 결제
* 3D 시각화
* 푸시 알림(FCM)
* AI 모델 서버 업로드

---

## 2. System Overview

### 2.1 Target Platform

* iOS 15 이상
* iPhone 12 이상

### 2.2 Architecture

* 완전한 On-device 처리 구조
* 네트워크 의존성 없음
* 단일 모듈 앱 구조

---

## 3. Functional Requirements

| ID           | Priority | Description | Acceptance Criteria         |
| ------------ | -------- | ----------- | --------------------------- |
| MVP-FUNC-001 | Must     | 비디오 촬영 기능   | 10초 자동 녹화, 재촬영 가능           |
| MVP-FUNC-002 | Must     | 키포인트 추출     | Vision Framework로 17개 관절 추출 |
| MVP-FUNC-003 | Must     | 핵심 지표 계산    | 최소 3개 지표 산출                 |
| MVP-FUNC-004 | Must     | 결과 표시       | 텍스트 UI로 위험도 표시              |
| MVP-FUNC-005 | Must     | 결과 로컬 저장    | JSON 기반 파일 저장               |
| MVP-FUNC-006 | Should   | 단순 기록 조회    | 최근 5개 기록 리스트 출력             |

---

## 4. Non-Functional Requirements

| Metric    | Target       |
| --------- | ------------ |
| 분석 시간     | 평균 20초 이내    |
| AI 인식 성공률 | 70% 이상       |
| 앱 크래시율    | 1% 미만        |
| 데이터 저장    | 디바이스 내 저장 전용 |

---

## 5. Data Model

### 5.1 Local JSON Structure

```json
{
  "id": "UUID",
  "createdAt": "ISO8601 Date",
  "metrics": {
    "kneeAngle": 0.0,
    "cadence": 0.0,
    "upperBodyLean": 0.0
  },
  "riskLevel": "Safe | Caution | Danger"
}
```

---

## 6. Core Flow

```text
User → Video Capture → Keypoint Extraction → Metrics Calculation → Result Display → Local Save
```

---

## 7. Success Criteria (MVP Validation)

| 항목       | 기준     |
| -------- | ------ |
| 첫 분석 완료율 | 80% 이상 |
| 재촬영 비율   | 50% 이하 |
| 사용자 재방문율 | 30% 이상 |

---

## 8. Risks

| Risk         | Mitigation |
| ------------ | ---------- |
| 저사양 기기 성능 저하 | 분석 프레임 간소화 |
| Vision 인식 실패 | 가이드 UI 개선  |

---

## 9. References

* Apple Vision Framework Documentation
* Lean Startup MVP Principles

---

## 10. Versioning Plan

| Phase   | Scope                 |
| ------- | --------------------- |
| Phase 0 | 현재 MVP 기능             |
| Phase 1 | 히스토리 고도화, 알림, 클라우드 저장 |

---
