# Project Init

## 목적
app-idia-lab에서 채택(adopted)된 PRD 문서를 기반으로, React Native + Supabase 프로젝트를 자동 생성하고 초기화하는 워크플로우 도구.

## 프로젝트 역할
이 프로젝트는 **코드 개발을 하지 않는다.** PRD 기반 프로젝트 초기화 자동화 스킬만 제공한다.

## 워크플로우
```
/init-scaffold NNN-아이디어명 프로젝트명  →  프로젝트 생성 + 폴더 구조 + 의존성 설치
/init-docs NNN-아이디어명 프로젝트명      →  CLAUDE.md + KNOWLEDGE.md 생성 + Git 초기화
```

## 경로 상수
- **app-idia-lab 경로**: `C:\Users\lsy\app-idia-lab`
- **PRD 파일 위치**: `C:\Users\lsy\app-idia-lab\ideas\adopted\NNN-*-prd.md`
- **프로젝트 생성 기본 경로**: `C:\Users\lsy\`

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
모든 프로젝트에 설치하는 패키지.

### 필수 의존성 (dependencies)
```
@supabase/supabase-js
react-native-paper
react-native-safe-area-context
react-native-vector-icons
zustand
react-native-mmkv
expo-router
expo-linking
expo-constants
expo-status-bar
expo-splash-screen
expo-secure-store
```

### 필수 개발 의존성 (devDependencies)
```
@types/react
typescript
```

---

## 조건부 의존성 매핑
PRD Section 7-1에 아래 기술이 명시된 경우에만 추가 설치한다.

| PRD 기술명 | npm 패키지명 | 용도 |
|---|---|---|
| op-sqlite | `@op-engineering/op-sqlite` | 구조화 로컬 DB |
| WatermelonDB | `@nozbe/watermelondb` | 오프라인 퍼스트 DB |
| Kakao OAuth | `@react-native-seoul/kakao-login` | 카카오 로그인 |
| Google OAuth | `@react-native-google-signin/google-signin` | 구글 로그인 |
| Apple Auth | `expo-apple-authentication` | 애플 로그인 |
| Push Notification | `expo-notifications` | 푸시 알림 |
| Analytics (Firebase) | `expo-firebase-analytics` | Firebase 분석 |
| Analytics (Aptabase) | `@aptabase/react-native` | 프라이버시 중심 분석 |
| In-App Purchase | `react-native-iap` | 인앱 결제 |
| Chart/Graph (Gifted) | `react-native-gifted-charts` | 차트 시각화 |
| Chart/Graph (Victory) | `victory-native` | 차트 시각화 |
| Calendar | `react-native-calendars` | 캘린더 UI |
| Image Picker | `expo-image-picker` | 이미지 선택 |
| Camera | `expo-camera` | 카메라 |
| Haptics | `expo-haptics` | 햅틱 피드백 |
| Lottie | `lottie-react-native` | 애니메이션 |
| DateTimePicker | `@react-native-community/datetimepicker` | 날짜/시간 선택 |
| AsyncStorage | `@react-native-async-storage/async-storage` | 비동기 키-값 저장소 |

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

## 프로젝트 구조
← 표준 폴더 구조 + PRD Section 5, 8 기반으로 기능별 폴더 확장.
실제 생성된 폴더 트리를 기록.

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

## 제약사항
← PRD Section 12에서 추출.
비즈니스 가정, 외부 의존성, 기술적 제약.
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
← PRD 전반에서 추출.
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
