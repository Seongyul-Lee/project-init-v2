# Project Init

## 목적
app-idea-lab에서 채택(adopted)된 PRD 문서를 기반으로, React Native + Supabase 프로젝트를 자동 생성하고 초기화하는 워크플로우 도구.

## 프로젝트 역할
이 프로젝트는 **코드 개발을 하지 않는다.** PRD 기반 프로젝트 초기화 자동화 스킬만 제공한다.

## 워크플로우
```
/init-scaffold NNN-아이디어명 프로젝트명  →  프로젝트 생성 + 폴더 구조 + 의존성 설치
/init-docs NNN-아이디어명 프로젝트명      →  CLAUDE.md + KNOWLEDGE.md 생성 + Git 초기화
```

## 연관 프로젝트: app-idea-lab
본 프로젝트는 `~/app-idea-lab`의 채택된 PRD를 입력으로 사용한다. app-idea-lab의 기술 스택 제약, 개발 환경, PRD 구조가 변경되면 본 프로젝트의 대응 섹션(기술 스택, 경로 상수, 의존성 매핑, 템플릿 골격)도 함께 수정해야 한다. 동기화 대상 상세는 app-idea-lab CLAUDE.md의 "연관 프로젝트: project-init" 섹션을 참조.

## 경로 상수
- **app-idea-lab 경로**: `~/app-idea-lab`
- **PRD 파일 위치**: `~/app-idea-lab/ideas/adopted/NNN-*-prd.md`
- **프로젝트 생성 기본 경로**: `~/`
- **커맨드 템플릿 경로**: `~/project-init/templates/commands/`

---

## 기술 스택 (고정)
- **프레임워크**: Expo Managed Workflow (SDK 최신 안정 버전)
- **언어**: TypeScript (strict mode)
- **라우팅**: Expo Router (파일 기반)
- **UI**: React Native Paper
- **상태 관리**: Zustand
- **백엔드**: Supabase (PostgreSQL + Auth + Edge Functions + Storage)
- **로컬 저장소**: react-native-mmkv (경량 키-값), op-sqlite (구조화 데이터, 필요 시)

---

## 표준 폴더 구조
Expo Router 기반 React Native + Supabase 프로젝트의 기본 구조. init-scaffold에서 이 구조를 생성한다.

```
[프로젝트루트]/
├── src/
│   ├── app/                    # Expo Router 파일 기반 라우팅
│   │   ├── (auth)/            # 인증 관련 화면 그룹
│   │   ├── (tabs)/            # 메인 탭 네비게이터
│   │   ├── _layout.tsx        # 루트 레이아웃
│   │   └── index.tsx          # 엔트리 포인트
│   ├── components/            # 재사용 컴포넌트
│   │   └── ui/               # 공통 UI (Button, Card, Input 등)
│   ├── hooks/                 # 커스텀 훅
│   ├── stores/                # Zustand 스토어
│   ├── services/              # 외부 서비스 연동
│   │   └── supabase.ts       # Supabase 클라이언트 초기화
│   ├── utils/                 # 유틸리티 함수
│   ├── types/                 # TypeScript 타입 정의
│   ├── constants/             # 상수 (색상, 크기, 설정값 등)
│   └── assets/                # 이미지, 폰트 등 정적 리소스
├── .claude/
│   └── commands/              # Claude Code 커스텀 명령어 (next, plan)
├── docs/
│   └── plans/                 # 구현 계획 문서 (/plan 명령어 산출물)
├── supabase/
│   ├── migrations/            # DB 마이그레이션 SQL 파일
│   └── functions/             # Supabase Edge Functions
├── .env.example               # 환경변수 템플릿
├── .gitignore
├── CLAUDE.md                  # 프로젝트별 개발 지침 (init-docs가 생성)
└── KNOWLEDGE.md               # 프로젝트별 도메인 지식 (init-docs가 생성)
```

### 폴더 확장 규칙
PRD Section 5의 P0 기능 목록에 따라 `src/components/` 하위에 기능별 폴더를 추가한다.
예: 체크인 기능 → `src/components/checkin/`, 리포트 기능 → `src/components/report/`

---

## 공통 의존성
모든 프로젝트에 설치하는 패키지. `npx expo install`로 설치하여 SDK 호환 버전을 자동 맞춘다.

### 필수 의존성 (dependencies)

| 패키지명 | 용도 | 비고 |
|---|---|---|
| `@supabase/supabase-js` | Supabase 클라이언트 | 순수 JS, 네이티브 코드 없음 |
| `react-native-paper` | Material Design UI 컴포넌트 | 피어: `react-native-safe-area-context` |
| `react-native-safe-area-context` | Safe Area 처리 | expo-router, react-native-paper 공통 피어 |
| `zustand` | 전역 상태 관리 | 순수 JS |
| `react-native-mmkv` | 로컬 키-값 저장소 | 피어: `react-native-nitro-modules` (v4+) |
| `react-native-nitro-modules` | MMKV v4+ 필수 피어 | react-native-mmkv와 함께 설치 |
| `expo-router` | 파일 기반 라우팅 | 피어 다수 (아래 참조) |
| `expo-linking` | 딥링크 처리 | expo-router 필수 피어 |
| `expo-constants` | 앱 상수 접근 | expo-router 필수 피어 |
| `@expo/metro-runtime` | Metro 번들러 런타임 | expo-router 필수 피어 |
| `react-native-screens` | 네이티브 화면 스택 | expo-router 필수 피어 |
| `react-native-reanimated` | 고성능 애니메이션 | expo-router 전환 애니메이션, 범용 사용 |
| `react-native-gesture-handler` | 제스처 처리 | 내비게이션 제스처, 스와이프 등 |
| `expo-status-bar` | 상태바 제어 | |
| `expo-splash-screen` | 스플래시 화면 제어 | |
| `expo-secure-store` | 보안 저장소 (토큰 등) | Supabase 세션 저장에 사용 |
| `expo-font` | 커스텀 폰트 로딩 | `@expo/vector-icons` 피어 |
| `expo-system-ui` | 시스템 UI 제어 (루트 배경색) | 다크모드 화면 깜빡임 방지 |
| `expo-dev-client` | 개발 빌드 클라이언트 | 네이티브 모듈 사용 시 필수 (MMKV 등) |

> **참고**: `@expo/vector-icons`는 `expo` 패키지에 기본 포함되어 있어 별도 설치 불필요.
> `react-native-vector-icons`는 Expo Managed Workflow에서 사용하지 않는다.

### 설치 명령어
```bash
npx expo install @supabase/supabase-js react-native-paper react-native-safe-area-context zustand react-native-mmkv react-native-nitro-modules expo-router expo-linking expo-constants @expo/metro-runtime react-native-screens react-native-reanimated react-native-gesture-handler expo-status-bar expo-splash-screen expo-secure-store expo-font expo-system-ui expo-dev-client
```

### 필수 개발 의존성 (devDependencies)
```bash
npm install -D @types/react typescript
```

---

## 조건부 의존성 매핑
PRD Section 7-1에 아래 기술이 명시된 경우에만 추가 설치한다.
모든 패키지는 `npx expo install`로 설치한다.

### 로컬 DB / 오프라인

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| op-sqlite | `@op-engineering/op-sqlite` | — | dev build 필요. `expo-updates`와 SQLite 충돌 가능, 주의 |
| WatermelonDB | `@nozbe/watermelondb` | — | RN 0.76+ 호환 이슈 있음. Expo용 커뮤니티 플러그인 필요: `@lovesworking/watermelondb-expo-plugin-sdk-52-plus` |
| AsyncStorage | `@react-native-async-storage/async-storage` | — | MMKV보다 느림. 레거시 호환 필요 시만 사용 |

### 인증

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| Kakao OAuth | `@react-native-seoul/kakao-login` | — | dev build 필요 |
| Google OAuth | `@react-native-google-signin/google-signin` | — | Expo config plugin 내장. dev build 필요 |
| Apple Auth | `expo-apple-authentication` | — | iOS 전용 |
| Naver OAuth | `@react-native-seoul/naver-login` | — | dev build 필요 |

### 알림

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| Push Notification | `expo-notifications` | — | 푸시 기능은 dev build 필요 |

### 분석

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| Analytics (Firebase) | `@react-native-firebase/analytics` | `@react-native-firebase/app` (동일 버전) | `expo-firebase-analytics`는 **폐기됨**. dev build 필요, app.json config plugin 설정 필요 |
| Analytics (Aptabase) | `@aptabase/react-native` | — | 순수 JS. 최신 버전(v0.4+)은 React 19/RN 0.81+ 요구 — Expo SDK 버전에 맞는 호환 버전 확인 필요 |

### 결제

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| In-App Purchase | `react-native-iap` | `react-native-nitro-modules` | dev build 필요. Expo config plugin 내장 |
| RevenueCat | `react-native-purchases` | — | IAP 래퍼. dev build 필요 |

### 차트 / 시각화

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| Chart (Gifted Charts) | `react-native-gifted-charts` | `react-native-svg` | SVG 기반. 그래디언트 사용 시 `expo-linear-gradient` 추가 |
| Chart (Victory Native) | `victory-native` | `@shopify/react-native-skia`, `react-native-reanimated`, `react-native-gesture-handler` | Skia 기반 (SVG 아님). dev build 필요 |

### UI 컴포넌트

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| Calendar | `react-native-calendars` | — | 주로 JS 기반 |
| DateTimePicker | `@react-native-community/datetimepicker` | — | Expo 공식 지원, config plugin 내장 |
| SVG | `react-native-svg` | — | 차트/아이콘 라이브러리의 공통 피어 |

### 미디어 / 디바이스

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| Image Picker | `expo-image-picker` | — | |
| Camera | `expo-camera` | — | dev build 필요 |
| Haptics | `expo-haptics` | — | |

### 애니메이션

| PRD 기술명 | npm 패키지명 | 함께 설치할 피어 | 비고 |
|---|---|---|---|
| Lottie | `lottie-react-native` | — | dev build 필요. `npx expo install`로 호환 버전 설치 |

---

## 생성할 CLAUDE.md 골격
init-docs 스킬이 새 프로젝트에 생성하는 CLAUDE.md의 구조. 각 섹션에 PRD의 어느 부분을 채워야 하는지 명시한다.

```markdown
# [프로젝트명]

## 프로젝트 개요
← PRD Section 1 (Executive Summary)에서 추출.
한 문단으로 프로젝트의 핵심 가치와 목표를 서술.

## 기술 스택
← PRD Section 7-1에서 추출.
레이어별(프론트엔드/상태관리/로컬저장소/백엔드/인증/결제/분석/빌드) 기술 선택과 선택 근거.

## 설계 원칙
← PRD Section 7-4 (핵심 설계 원칙)에서 추출.
번호 목록으로 3~6개 원칙을 나열. 각 원칙은 한 줄로 핵심만 서술.
개발 의사결정 시 판단 기준으로 활용.

## 프로젝트 구조
← 표준 폴더 구조 + PRD Section 5, 8 기반으로 기능별 폴더 확장.
실제 생성된 폴더 트리를 기록.

## 워크플로우

| 단계 | 행동 | 비고 |
|---|---|---|
| 1 | 다음 태스크 확인 | `/next` 또는 Task Master 직접 조회 |
| 2 | 계획 수립 (복잡한 작업 시) | `/plan [기능명]` |
| 3 | 외부 라이브러리 문서 조회 | Context7 활용 |
| 4 | 구현 | — |
| 5 | 검증 | lint + typecheck |
| 6 | 태스크 완료 처리 | Task Master 상태 업데이트 |

## 코딩 컨벤션

### 네이밍 규칙
- 컴포넌트 파일: PascalCase (예: CheckInScreen.tsx)
- 훅 파일: camelCase, use 접두사 (예: useCheckIn.ts)
- 스토어 파일: camelCase + Store 접미사 (예: checkInStore.ts)
- 타입 파일: PascalCase + 용도 접미사 (예: CheckInRequest, CheckInResponse)
- 상수: UPPER_SNAKE_CASE
- 유틸 파일: camelCase (예: formatDate.ts)
- 폴더: kebab-case (예: check-in/)

### 컴포넌트 패턴
- 함수형 컴포넌트만 사용. React.FC 미사용, props 타입 직접 선언
- 화면 컴포넌트: [Feature]Screen.tsx
- 재사용 컴포넌트: [Name].tsx
- 한 파일에 하나의 export default 컴포넌트

### 상태 관리 패턴
← PRD Section 7-7에서 추출.
Zustand 스토어 목록, 각 스토어의 역할과 주요 상태 필드.

### 에러 핸들링
- Supabase 호출: try-catch + 에러 타입 분기
- 사용자 대면 에러: Toast/Snackbar로 표시
- 네트워크 에러: 오프라인 큐에 적재 (해당 시)

## 검증 프로토콜

> **적용 범위**: 코드 변경이 포함된 모든 작업
> **제외**: 질문 응답, 파일 조회, 정보 검색만 하는 경우

### 사전 검사 (구현 전)

**체크리스트**
- [ ] 관련 코드를 모두 읽었는가?
- [ ] 기존 패턴을 이해했는가?
- [ ] 부작용 범위를 파악했는가?
- [ ] `KNOWLEDGE.md`에서 유사 사례 확인했는가?

**신뢰도 평가**

| 신뢰도 | 행동 |
|---|---|
| ≥90% | 바로 진행 |
| 70-89% | 대안 제시 후 조사 계속 |
| <70% | 중단, 사용자에게 질문 |

### 사후 검증 (구현 후)

**필수**: lint + typecheck 실행

**위험도별 추가 검증**:

| 위험도 | 조건 | 추가 검증 |
|---|---|---|
| 기본 | 모든 변경 | lint + typecheck |
| 중간 | UI 변경 | + 접근성 확인 |
| 높음 | 핵심 로직 | + 실제 기기 테스트 |

**완료 전 체크리스트**:
- [ ] typecheck 성공 (실제 출력 확인)
- [ ] 모든 요구사항 충족 (항목별 체크)
- [ ] 검증되지 않은 가정 없음
- [ ] `KNOWLEDGE.md` 업데이트 필요 여부 검토

### 환각 위험 신호

즉시 재검토 필요:
- 증거 없이 "정상 작동" 주장
- "아마도", "대부분" 같은 불확실한 언어
- 테스트 실패 무시
- 요약만 제공, 실제 출력 생략

### 커밋 규칙
- 메시지: 한국어
- 형식: `type: 설명` (feat, fix, refactor, docs, test 등)

## DB 규칙
← PRD Section 7-5에서 추출.
- 테이블 네이밍: snake_case, 복수형
- RLS 정책 요약
- 핵심 DB 함수 목록과 역할

## API 규칙
← PRD Section 7-6에서 추출.
- REST vs Edge Function 구분 기준
- 엔드포인트 네이밍 규칙
- TypeScript 인터페이스 패턴 (Request/Response)
- Edge Function 내부 비즈니스 로직 패턴

## 오프라인/동기화 규칙
← PRD Section 7-3에서 추출.
- 로컬 저장소 선택과 역할 분담
- 동기화 큐 로직 (재시도 횟수, 백오프)
- 충돌 해결 정책

## 주요 명령어
- `npx expo start` — 개발 서버 시작
- `npx expo start --clear` — 캐시 클리어 후 시작
- `npx eas build` — 프로덕션 빌드
- `npx supabase start` — 로컬 Supabase 시작
- `npx supabase db push` — 마이그레이션 적용
- `npx supabase functions serve` — Edge Function 로컬 실행

## Tooling

| 도구 | SSOT | 사용 정책 |
|---|---|---|
| Task Master | `.taskmaster/` | 태스크 관리 SSOT |
| Context7 | (런타임 조회) | 외부 라이브러리 문서 조회 시 Claude 판단에 따라 사용 |

## Safety (절대 규칙)
- `.env`/비밀키/API키: 읽거나 출력 금지
- 파괴적 명령: 실행 전 롤백 방안 제시 필수
- 대규모 리팩터링: 임의 제안 금지
- 외부 API 키: 서버/Edge Function에서만 사용, 클라이언트 번들 노출 금지

## 제약사항
← PRD Section 12에서 추출.
비즈니스 가정, 외부 의존성, 기술적 제약.

## PRD 참조
← init-docs가 PRD 파일의 물리적 경로와 주요 섹션 안내를 자동 기입.
CLAUDE.md에 요약하지 않은 상세 정보를 원문에서 조회할 때 사용.
```

---

## 생성할 KNOWLEDGE.md 골격
init-docs 스킬이 새 프로젝트에 생성하는 KNOWLEDGE.md의 구조.

```markdown
# [프로젝트명] 도메인 지식

## 비즈니스 컨텍스트
← PRD Section 1 (Executive Summary), Section 2 (Problem Statement)에서 추출.
이 앱이 해결하는 문제, 시장 배경, 핵심 가치.

## 타겟 사용자
← PRD Section 3 (Target Users & Personas)에서 추출.
사용자 세그먼트, 페르소나 요약, 핵심 니즈.

## 핵심 기능 요약
← PRD Section 5 (Functional Requirements)에서 추출.
P0/P1 기능 목록과 각 기능의 한 줄 설명. AC 번호 참조.

## 핵심 용어 사전
← PRD Section 16 (Glossary)에서 추출.
도메인 특화 용어와 정의 (예: "체크인" = 일일 루틴 완료 기록).

## 수익 모델
← PRD Section 10 (Monetization Strategy)에서 추출.
무료/유료 구분, 결제 구조 요약.

## 경쟁 포지셔닝
← PRD Section 9 (Competitive Differentiation)에서 추출.
주요 경쟁 앱과 차별점 요약.

## 성공 지표
← PRD Section 15 (Success Metrics)에서 추출.
MVP 단계 핵심 KPI.
```
