<a id="readme-top"></a>

# Project Init v2

[app-idea-lab-v2](../app-idea-lab-v2)에서 채택된 PRD를 기반으로, Expo + Supabase 프로젝트를 자동 생성하고 개발 환경을 초기화하는 Claude Code 워크플로우 도구입니다. app-idea-lab-v2 파이프라인의 **Stage 4(채택) → MVP 개발** 사이에서 동작합니다.

<!-- TABLE OF CONTENTS -->
<details>
  <summary>목차</summary>
  <ol>
    <li><a href="#프로젝트-소개">프로젝트 소개</a></li>
    <li><a href="#파이프라인에서의-위치">파이프라인에서의 위치</a></li>
    <li><a href="#프로젝트-구조">프로젝트 구조</a></li>
    <li><a href="#시작하기">시작하기</a></li>
    <li><a href="#사전-설정">사전 설정</a></li>
    <li><a href="#사용법">사용법</a></li>
    <li><a href="#기술-스택">기술 스택</a></li>
    <li><a href="#생성되는-프로젝트-구조">생성되는 프로젝트 구조</a></li>
    <li><a href="#의존성-매핑">의존성 매핑</a></li>
    <li><a href="#개발-착수-taskmaster-ai">개발 착수: TaskMaster-AI</a></li>
    <li><a href="#연관-프로젝트">연관 프로젝트</a></li>
  </ol>
</details>

## 프로젝트 소개

이 프로젝트는 **코드 개발을 하지 않습니다.** PRD 기반 프로젝트 초기화 자동화 스킬만 제공합니다.

**하는 일:**
- PRD를 읽고 필요한 폴더 구조 생성
- Supabase 초기화 + DB 마이그레이션 + EAS Build 설정
- 공통 + 조건부 의존성 설치
- CLAUDE.md, KNOWLEDGE.md 등 개발 지침 문서 자동 생성
- Git 초기화

**하지 않는 일:**
- 앱 기능 코드 작성
- PRD 작성이나 평가 (app-idea-lab-v2의 역할)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 파이프라인에서의 위치

본 프로젝트는 app-idea-lab-v2의 전체 파이프라인 중 **Stage 4(채택) → MVP 개발** 사이에 위치합니다.

```
Stage 1 → Stage 1V → Stage 2 → Stage 3 → Stage 4 (채택)
                                                    ↓
                                            ★ project-init-v2 ★
                                            /init-scaffold → /init-docs
                                                    ↓
                                            TaskMaster-AI (태스크 분해)
                                                    ↓
                                              MVP 개발
                                                    ↓
                                            Stage 5 (출시) → Stage 6 (성장)
                                                    ↓
                                            /monthly-review (매월)
```

**입출력:**
- **입력**: `~/app-idea-lab-v2/ideas/adopted/NNN-아이디어명-prd.md` (Stage 4 채택 PRD)
- **출력**: 개발 착수 가능한 Expo + Supabase 프로젝트 (CLAUDE.md, KNOWLEDGE.md 포함)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 프로젝트 구조

```
project-init-v2/
├── .claude/
│   └── commands/
│       ├── init-scaffold.md    # 프로젝트 생성 커맨드
│       └── init-docs.md        # 문서 생성 커맨드
├── templates/
│   └── commands/
│       ├── next.md             # 생성 프로젝트에 복사할 /next 커맨드
│       └── plan.md             # 생성 프로젝트에 복사할 /plan 커맨드
├── CLAUDE.md                   # 프로젝트 지침 (스택, 의존성 매핑 등)
└── README.md
```

### 경로 상수

| 항목 | 경로 |
|---|---|
| app-idea-lab-v2 | `~/app-idea-lab-v2` |
| PRD 파일 위치 | `~/app-idea-lab-v2/ideas/adopted/NNN-아이디어명-prd.md` |
| 프로젝트 생성 기본 경로 | `~/` |
| 커맨드 템플릿 | `~/project-init-v2/templates/commands/` |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 시작하기

### 사전 요구사항

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI 설치 및 인증 완료
- [app-idea-lab-v2](../app-idea-lab-v2)에서 채택된 PRD 문서 (최소 1개)
- Node.js 18+ (Expo 프로젝트 생성에 필요)

### 설치

```sh
git clone <repo-url> ~/project-init-v2
cd ~/project-init-v2
```

별도의 의존성 설치는 필요 없습니다. Claude Code 세션에서 슬래시 커맨드를 실행하면 됩니다.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 사전 설정

이 프로젝트의 커맨드가 정상 동작하려면 아래 환경이 갖춰져 있어야 합니다.

### 필수 도구

| 도구 | 용도 | 비고 |
|---|---|---|
| **Claude Code** | 슬래시 커맨드 실행 환경 | CLI 설치 및 인증 필요 |
| **Node.js 18+** | Expo 프로젝트 생성 및 패키지 설치 | `npx expo install` 사용 |
| **Git** | 생성된 프로젝트 버전 관리 | `/init-docs`에서 자동 초기화 |

### 생성 프로젝트에 포함되는 커맨드

`/init-docs` 실행 시 새 프로젝트의 `.claude/commands/`에 아래 커맨드가 자동 복사됩니다.

| 커맨드 | 설명 | 필요 MCP |
|---|---|---|
| `/next` | TaskMaster-AI에서 다음 작업 조회 | `task-master-ai` |
| `/plan` | 구현 계획 수립 + Plan Mode 진입 | — |

### 생성 프로젝트에서 사용하는 MCP 서버

생성된 프로젝트에서 개발 시 아래 MCP 서버를 연동하면 워크플로우가 더 원활해집니다.

| MCP 서버 | 용도 | 필수 여부 |
|---|---|---|
| **task-master-ai** | PRD → 태스크 분해, 작업 관리 | 권장 (필수에 가까움) |
| **context7** | 외부 라이브러리 최신 문서 조회 | 선택 (권장) |

**TaskMaster-AI MCP 설정 예시:**

```json
{
  "mcpServers": {
    "task-master-ai": {
      "command": "npx",
      "args": ["-y", "task-master-ai"],
      "env": {
        "ANTHROPIC_API_KEY": "<Anthropic API 키>"
      }
    }
  }
}
```

> **참고:** TaskMaster-AI는 Claude API를 사용하므로 별도의 Anthropic API 키가 필요합니다.
> [Anthropic Console](https://console.anthropic.com/)에서 발급받을 수 있습니다.

### 슬래시 커맨드 목록

이 프로젝트에 포함된 커맨드입니다 (`.claude/commands/`):

| 커맨드 | 인자 | 설명 |
|---|---|---|
| `/init-scaffold` | `NNN-아이디어명 프로젝트명` | 프로젝트 생성 + 폴더 구조 + 의존성 설치 |
| `/init-docs` | `NNN-아이디어명 프로젝트명` | CLAUDE.md + KNOWLEDGE.md 생성 + Git 초기화 |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 사용법

Claude Code에서 project-init-v2 프로젝트를 열고 커맨드를 순서대로 실행합니다.

```sh
# project-init-v2 디렉토리에서 Claude Code 시작
cd ~/project-init-v2
claude
```

```
# 1. 프로젝트 스캐폴드 생성
/init-scaffold 009-복약케어 medi-care

# 2. 개발 문서 생성 + Git 초기화
/init-docs 009-복약케어 medi-care
```

실행 후 `~/medi-care/`에 개발 착수 가능한 프로젝트가 생성됩니다.

### /init-scaffold 상세 단계

| 순서 | 단계 | 설명 |
|:---:|---|---|
| 1 | Expo 프로젝트 생성 | `create-expo-app`으로 프로젝트 생성 |
| 2 | 표준 폴더 구조 생성 | `src/app`, `src/components`, `src/hooks`, `src/stores` 등 |
| 3 | Supabase 초기화 | `supabase init`으로 `supabase/` 디렉토리 구성 |
| 4 | DB 마이그레이션 | PRD Section 7-5의 CREATE TABLE SQL로 마이그레이션 파일 생성 |
| 5 | EAS Build 설정 | `eas.json` 프로파일 구성 (dev/preview/production) |
| 6 | 의존성 설치 | 공통 의존성 + PRD Section 7-1 기반 조건부 의존성 설치 |

### /init-docs 상세 단계

| 순서 | 단계 | 설명 |
|:---:|---|---|
| 1 | CLAUDE.md 생성 | PRD에서 개발 지침 추출 (아래 매핑표 참조) |
| 2 | KNOWLEDGE.md 생성 | PRD에서 도메인 지식 추출 (아래 매핑표 참조) |
| 3 | 커맨드 복사 | `templates/commands/`의 `/next`, `/plan`을 새 프로젝트에 복사 |
| 4 | Git 초기화 | `git init` + 초기 커밋 |

**CLAUDE.md PRD 섹션 매핑:**

| CLAUDE.md 섹션 | 추출 원본 (PRD Section) |
|---|---|
| 프로젝트 개요 | Section 1 (Executive Summary) |
| 기술 스택 | Section 7-1 |
| 설계 원칙 | Section 7-4 |
| 상태 관리 패턴 | Section 7-7 |
| DB 규칙 | Section 7-5 |
| API 규칙 | Section 7-6 |
| 오프라인/동기화 규칙 | Section 7-3 |
| 제약사항 | Section 12 |

**KNOWLEDGE.md PRD 섹션 매핑:**

| KNOWLEDGE.md 섹션 | 추출 원본 (PRD Section) |
|---|---|
| 비즈니스 컨텍스트 | Section 1, 2 |
| 타겟 사용자 | Section 3 |
| 핵심 기능 요약 | Section 5 |
| 용어 사전 | Section 16 |
| 수익 모델 | Section 10 |
| 경쟁 포지셔닝 | Section 9 |
| 성공 지표 | Section 15 |

상세 골격은 [CLAUDE.md](./CLAUDE.md)의 "생성할 CLAUDE.md 골격" / "생성할 KNOWLEDGE.md 골격" 섹션을 참조해 주세요.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 기술 스택

생성되는 프로젝트의 고정 기술 스택입니다. app-idea-lab-v2의 기술 스택 제약과 동기화됩니다.

| 레이어 | 기술 |
|---|---|
| 프레임워크 | Expo Managed Workflow (SDK 최신 안정 버전) |
| 언어 | TypeScript (strict mode) |
| 라우팅 | Expo Router (파일 기반) |
| UI | React Native Paper |
| 상태 관리 | Zustand |
| 백엔드 | Supabase (PostgreSQL + Auth + Edge Functions + Storage) |
| 로컬 KV | react-native-mmkv |
| 로컬 DB | op-sqlite (조건부) |
| 타겟 플랫폼 | iOS / Android |

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 생성되는 프로젝트 구조

`/init-scaffold`가 생성하는 Expo Router 기반 프로젝트의 표준 폴더 구조입니다.

```
[프로젝트루트]/
├── src/
│   ├── app/                    # Expo Router 파일 기반 라우팅
│   │   ├── (auth)/            # 인증 관련 화면 그룹
│   │   ├── (tabs)/            # 메인 탭 네비게이터
│   │   ├── _layout.tsx        # 루트 레이아웃
│   │   └── index.tsx          # 엔트리 포인트
│   ├── components/            # 재사용 컴포넌트
│   │   └── ui/               # 공통 UI
│   ├── hooks/                 # 커스텀 훅
│   ├── stores/                # Zustand 스토어
│   ├── services/              # 외부 서비스 연동
│   │   └── supabase.ts       # Supabase 클라이언트 초기화
│   ├── utils/                 # 유틸리티 함수
│   ├── types/                 # TypeScript 타입 정의
│   ├── constants/             # 상수
│   └── assets/                # 정적 리소스
├── .claude/
│   └── commands/              # /next, /plan 커맨드
├── supabase/
│   ├── migrations/            # DB 마이그레이션 SQL
│   └── functions/             # Edge Functions
├── .env.example
├── .gitignore
├── eas.json                   # EAS Build 프로파일 (dev/preview/production)
├── CLAUDE.md                  # 프로젝트별 개발 지침 (init-docs가 생성)
└── KNOWLEDGE.md               # 프로젝트별 도메인 지식 (init-docs가 생성)
```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 의존성 매핑

### 공통 의존성 (모든 프로젝트에 설치)

```bash
# 필수 의존성
npx expo install @supabase/supabase-js react-native-paper react-native-safe-area-context zustand react-native-mmkv react-native-nitro-modules expo-router expo-linking expo-constants @expo/metro-runtime react-native-screens react-native-reanimated react-native-gesture-handler expo-status-bar expo-splash-screen expo-secure-store expo-font expo-system-ui expo-dev-client

# 개발 의존성
npm install -D @types/react typescript
```

### 조건부 의존성 (PRD Section 7-1에 명시된 경우만)

| 카테고리 | PRD 기술명 | 패키지 |
|---|---|---|
| 로컬 DB / 오프라인 | op-sqlite | `@op-engineering/op-sqlite` |
| 인증 | Kakao OAuth | `@react-native-seoul/kakao-login` |
| 인증 | Google OAuth | `@react-native-google-signin/google-signin` |
| 인증 | Apple Auth | `expo-apple-authentication` |
| 인증 | Naver OAuth | `@react-native-seoul/naver-login` |
| 알림 | expo-notifications | `expo-notifications` |
| 분석 | Firebase Analytics | `@react-native-firebase/analytics` + `app` |
| 분석 | Aptabase | `@aptabase/react-native` |
| 결제 | react-native-iap | `react-native-iap` |
| 결제 | RevenueCat | `react-native-purchases` |
| 차트 | Gifted Charts (SVG 기반) | `react-native-gifted-charts` + `react-native-svg` |
| 차트 | Victory Native (Skia 기반) | `victory-native` + `@shopify/react-native-skia` |
| UI 컴포넌트 | react-native-calendars | `react-native-calendars` |
| UI 컴포넌트 | DateTimePicker | `@react-native-community/datetimepicker` |
| UI 컴포넌트 | react-native-svg | `react-native-svg` |
| 미디어 / 디바이스 | expo-image-picker | `expo-image-picker` |
| 미디어 / 디바이스 | expo-camera | `expo-camera` |
| 미디어 / 디바이스 | expo-haptics | `expo-haptics` |
| 애니메이션 | Lottie | `lottie-react-native` |

상세 피어 의존성, app.json plugin 설정, 주의사항은 [CLAUDE.md](./CLAUDE.md)를 참조해 주세요.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 개발 착수: TaskMaster-AI

프로젝트 초기화 완료 후 실제 개발에 착수할 때는 **[TaskMaster-AI](https://github.com/eyaltoledano/claude-task-master)를 적극 활용하는 것을 강력히 권장합니다.** PRD를 분석하여 개발 태스크를 자동 분해하고, 태스크 간 의존성과 우선순위를 관리해줍니다.

### TaskMaster-AI 설정

```sh
# 생성된 프로젝트 디렉토리로 이동
cd ~/medi-care

# TaskMaster-AI 초기화
task-master init

# PRD 기반 태스크 자동 생성
task-master parse-prd
```

### 개발 워크플로우

TaskMaster-AI 설정 후 Claude Code에서 아래 사이클을 반복합니다.

| 단계 | 커맨드/행동 | 설명 |
|:---:|---|---|
| 1 | `/next` | TaskMaster-AI에서 다음 작업 조회 |
| 2 | `/plan [기능명]` 또는 `/plan id:N` | PRD 참조 자동 추출 + Plan Mode 진입 |
| 3 | 구현 | 코드 작성 |
| 4 | 검증 | `npx tsc --noEmit` + lint |
| 5 | 완료 처리 | Task Master 상태 → done |
| 6 | KNOWLEDGE.md 검토 | 업데이트 필요 여부 확인 |

> `/next`는 TaskMaster-AI MCP 서버가 연동되어 있어야 동작합니다. `/plan`은 `tasks.json`에서 관련 태스크를 조회한 뒤 PRD 섹션을 참조하여 Plan Mode로 진입합니다.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 연관 프로젝트

### app-idea-lab-v2

본 프로젝트의 입력 소스입니다. 아이디어 발굴 → 검증 → 평가 → PRD 작성 → 검증의 파이프라인을 거쳐 채택된 PRD를 산출합니다. 출시 후에는 Stage 5/6/Monthly Review도 app-idea-lab-v2에서 수행합니다.

```
app-idea-lab-v2 (PRD 산출)
        ↓
project-init-v2 (프로젝트 초기화)
        ↓
TaskMaster-AI (태스크 분해 + 개발)
        ↓
app-idea-lab-v2 (출시/성장/월간 리뷰)
```

### 동기화 항목

아래 항목이 app-idea-lab-v2에서 변경되면 본 프로젝트의 대응 섹션도 함께 수정해야 합니다. `/sync-check` 커맨드(app-idea-lab-v2)로 동기화 상태를 검증할 수 있습니다.

| app-idea-lab-v2 | project-init-v2 |
|---|---|
| 기술 스택 제약 | 기술 스택, 공통/조건부 의존성 |
| 개발 환경 | 개발 환경, 경로 상수 |
| 타겟 플랫폼 | 타겟 플랫폼 |
| PRD 최종 목표 / Task Master 투입 요건 | CLAUDE.md 골격, KNOWLEDGE.md 골격 |

<p align="right">(<a href="#readme-top">back to top</a>)</p>
