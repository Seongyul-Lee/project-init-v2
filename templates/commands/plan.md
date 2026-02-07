---
description: Plan Mode 진입 + 구현 계획 문서 생성
argument-hint: [feature-name] 또는 id:N
allowed-tools: Read, Grep, Glob, Write, EnterPlanMode
---

$ARGUMENTS 의 구현 계획을 수립하고 문서화한다.

## 인자 형식

| 형식 | 예시 | 동작 |
|------|------|------|
| `id:N` | `/plan id:2` | Task ID로 직접 조회 |
| `[기능명]` | `/plan Supabase DB 스키마` | title/description 매칭 검색 |

## 실행 순서

1. **tasks.json에서 관련 태스크 조회**
   - `.taskmaster/tasks/tasks.json` 읽기
   - `$ARGUMENTS`가 `id:N` 형식이면 → 해당 ID의 태스크 직접 조회
   - `$ARGUMENTS`가 기능명이면 → title/description 매칭 검색
   - 해당 태스크의 `id`, `title`, `details`, `testStrategy` 추출

2. **PRD 참조 섹션 확인**
   - 태스크 `details`에서 "PRD 섹션 X-X" 패턴 추출
   - `.taskmaster/docs/prd.md`에서 해당 섹션 읽기
   - 관련 FR(Functional Requirements), AC(Acceptance Criteria) 확인

3. **Plan Mode 진입** (EnterPlanMode 도구 사용)
   - 수집한 컨텍스트(Task ID, PRD 섹션, AC)를 기반으로 계획 수립

4. 관련 코드베이스 분석

5. 영향 범위 파악

6. 사용자 승인 (ExitPlanMode)

## 중요

- 탐색 과정, 의사결정 근거, 검토한 대안 등 맥락 정보 보존
- **PRD의 AC(수용 기준)를 구현 검증 기준으로 활용**

## 참고

- tasks.json에 매칭되는 태스크가 없으면 PRD 전체에서 관련 섹션 검색
