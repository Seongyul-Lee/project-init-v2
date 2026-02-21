입력: $ARGUMENTS
형식: NNN-아이디어명 프로젝트명
예시: /init-scaffold 001-데일리셀프 dailyself-app

---

## 목적
채택된 PRD를 기반으로 Expo + TypeScript 프로젝트를 생성하고, 표준 폴더 구조와 의존성을 설치한다.

---

## 실행 절차

### 1단계: 인자 파싱
- `$ARGUMENTS`에서 두 인자를 분리한다.
  - 첫 번째: `NNN-아이디어명` (예: `001-데일리셀프`)
  - 두 번째: `프로젝트명` (예: `dailyself-app`)
- NNN은 아이디어 번호 (예: `001`), 프로젝트명은 생성할 디렉토리 이름.
- 인자가 2개가 아니면 사용 예시를 보여주고 중단한다.

### 2단계: PRD 읽기 (Section 7-1, 7-5)
- PRD 파일을 찾는다:
  - 경로: `~/app-idea-lab-v2/ideas/adopted/`
  - 파일명 패턴: `NNN-아이디어명-prd.md` (예: `001-데일리셀프-prd.md`)
  - 해당 패턴의 파일이 없으면 사용자에게 정확한 파일명을 확인하고 중단한다.
- PRD 파일에서 **Section 7-1 (기술 스택)**과 **Section 7-5 (DB 스키마)**를 읽는다.
  - **Section 7-1**: `## 7-1.` 또는 `### 7-1.` 헤딩부터 `## 7-2.` 또는 `### 7-2.` 헤딩 직전까지.
  - **Section 7-5**: `## 7-5.` 또는 `### 7-5.` 헤딩부터 `## 7-6.` 또는 `### 7-6.` 헤딩 직전까지.
    - `sql` 코드 블록(```sql ... ```)을 모두 추출하여 순서대로 기록해둔다 (8단계에서 사용).
    - Section 7-5가 PRD에 없으면 기록해두고, Supabase 마이그레이션 단계(8단계 A)를 스킵한다.
- 읽은 내용에서 CLAUDE.md의 "조건부 의존성 매핑" 테이블에 해당하는 기술을 식별한다.
  - 식별된 기술 목록을 기록해둔다 (7단계 A, 9단계에서 사용).
- **미매핑 기술 감지**: Section 7-1에 언급된 모든 기술/라이브러리를 나열하고, CLAUDE.md의 "공통 의존성"과 "조건부 의존성 매핑" 테이블에 **모두 해당하지 않는** 기술이 있는지 확인한다.
  - 미매핑 기술이 발견되면 사용자에게 아래 형식으로 경고한다:
    ```
    ⚠️ PRD Section 7-1에 매핑되지 않는 기술이 포함되어 있습니다:
    - [기술명]: 자동 설치 불가. scaffold 완료 후 수동 설치가 필요합니다.
    ```
  - 사용자에게 "계속 진행할까요?"를 확인한 뒤, 승인 시 다음 단계로 진행한다.
  - 미매핑 기술 목록은 10단계 완료 보고에도 포함한다.

### 3단계: 프로젝트 디렉토리 확인
- 프로젝트 경로: `~/[프로젝트명]`
- 디렉토리가 이미 존재하면 사용자에게 확인을 요청한다:
  - "해당 디렉토리가 이미 존재합니다. 덮어쓸까요?"
  - 거부 시 중단.
- 존재하지 않으면 다음 단계로 진행.

### 4단계: Expo 프로젝트 초기화
- 작업 디렉토리: `~/`
- 명령어:
  ```
  npx create-expo-app@latest [프로젝트명] --template blank-typescript
  ```
- 실행 후 `~/[프로젝트명]/package.json`이 존재하는지 확인한다.
  - 존재하지 않으면 에러 메시지를 사용자에게 보여주고 중단.

### 5단계: Supabase 초기화
- 프로젝트 루트에서 실행한다: `~/[프로젝트명]`
- 명령어:
  ```
  cd ~/[프로젝트명]
  npx supabase init
  ```
- 실행 결과 확인:
  - `supabase/config.toml`이 생성되었는지 확인한다.
  - 이미 존재하면 (3단계에서 기존 디렉토리 덮어쓰기를 선택한 경우) 스킵한다.
  - `npx supabase` 실행이 실패하면 (supabase CLI 미설치 등) 아래 경고를 출력하고 이 단계를 스킵한다. 나머지 scaffold는 계속 진행한다:
    ```
    ⚠️ supabase init 실행 실패: [에러 메시지]
       Supabase CLI가 설치되어 있지 않을 수 있습니다.
       scaffold 완료 후 수동으로 'npx supabase init'을 실행해주세요.
    ```
- **참고**: `supabase init`은 `supabase/` 폴더와 `config.toml`을 생성한다. 이후 6단계에서 `supabase/migrations/`와 `supabase/functions/`를 mkdir -p로 생성하므로 충돌하지 않는다.

### 6단계: 폴더 구조 생성
- 프로젝트 루트: `~/[프로젝트명]`
- CLAUDE.md의 "표준 폴더 구조"를 참조하여 아래 디렉토리를 생성한다 (`mkdir -p`):
  ```
  .claude/commands/
  docs/plans/
  src/app/(auth)/
  src/app/(tabs)/
  src/components/ui/
  src/hooks/
  src/stores/
  src/services/
  src/utils/
  src/types/
  src/constants/
  src/assets/
  supabase/migrations/
  supabase/functions/
  ```
- PRD Section 5 (Functional Requirements)에서 식별된 P0 기능의 영문명으로 `src/components/` 하위에 기능별 폴더를 추가한다.
  - PRD의 기능명이 한글이면 적절한 영문 kebab-case로 변환한다.
  - 예: "체크인" → `src/components/check-in/`, "리포트" → `src/components/report/`

### 7단계: 기본 파일 생성

**A. app.json 수정**
- `~/[프로젝트명]/app.json` 파일을 읽는다.
  - 파일이 없으면 `⚠️ app.json을 찾을 수 없습니다. 스킵합니다.` 경고를 출력하고 B 항목으로 넘어간다.
- 아래 필드를 추가/수정한다 (기존 값 보존):
  - `expo.scheme` ← 프로젝트명 (kebab-case 그대로 사용)
  - `expo.plugins` ← 기본 plugin 배열 + 2단계에서 식별된 조건부 plugin
  - `expo.web.bundler` ← `"metro"`
- **기본 plugins** (항상 포함):
  - `"expo-router"`
  - `"expo-secure-store"`
- **조건부 plugins**: CLAUDE.md "조건부 의존성 매핑" 테이블의 "app.json plugin" 컬럼에 값이 있는(`—`가 아닌) 패키지 중, 2단계에서 식별된 것만 추가한다.
- 예시 (프로젝트명이 `dailyself-app`이고 조건부 plugin이 없는 경우):
  ```json
  {
    "expo": {
      "scheme": "dailyself-app",
      "plugins": [
        "expo-router",
        "expo-secure-store"
      ],
      "web": {
        "bundler": "metro"
      }
    }
  }
  ```

**B. package.json 수정**
- `~/[프로젝트명]/package.json` 파일을 읽는다.
  - 파일이 없으면 `⚠️ package.json을 찾을 수 없습니다. 스킵합니다.` 경고를 출력하고 다음 항목으로 넘어간다.
- `"main"` 필드를 `"expo-router/entry"`로 설정한다.
- 기존 내용을 보존한다.

**C. 기본 소스 파일 생성**
- **`src/services/supabase.ts`**: Supabase 클라이언트 초기화 코드를 작성한다.
  ```typescript
  import { createClient } from '@supabase/supabase-js';
  import * as SecureStore from 'expo-secure-store';

  const supabaseUrl = process.env.EXPO_PUBLIC_SUPABASE_URL!;
  const supabaseAnonKey = process.env.EXPO_PUBLIC_SUPABASE_ANON_KEY!;

  export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
    auth: {
      storage: {
        getItem: SecureStore.getItemAsync,
        setItem: SecureStore.setItemAsync,
        removeItem: SecureStore.deleteItemAsync,
      },
      autoRefreshToken: true,
      persistSession: true,
      detectSessionInUrl: false,
    },
  });
  ```
- **D. `.env.example`**:
  ```
  EXPO_PUBLIC_SUPABASE_URL=
  EXPO_PUBLIC_SUPABASE_ANON_KEY=
  ```
- **E.** 기존 `.gitignore`에 아래 항목을 추가한다 (이미 있으면 생략):
  ```
  .env
  .env.local
  ```
- **F. 커스텀 명령어 복사**: `~/project-init-v2/templates/commands/` 디렉토리의 파일을 프로젝트의 `.claude/commands/`로 복사한다.
  - `next.md` — Task Master 다음 작업 조회
  - `plan.md` — Plan Mode 진입 + 구현 계획 문서 생성

### 8단계: Supabase 마이그레이션 + EAS 설정

**A. Supabase 마이그레이션 파일 생성**

2단계에서 추출한 Section 7-5의 SQL 코드 블록들을 마이그레이션 파일로 저장한다.

- 파일 경로: `supabase/migrations/00000000000000_initial_schema.sql`
  - 타임스탬프는 `00000000000000` (14자리 0) 고정으로 사용한다.
  - 이유: 초기 스키마이므로 항상 첫 번째 마이그레이션이어야 함.
- 파일 내용 구성:
  ```sql
  -- Initial schema generated from PRD Section 7-5
  -- Source: ~/app-idea-lab-v2/ideas/adopted/NNN-아이디어명-prd.md
  -- Generated at: YYYY-MM-DD HH:MM

  [Section 7-5에서 추출한 모든 SQL 코드 블록을 순서대로 이어붙인다]
  ```
- Section 7-5에서 추출할 SQL 범위:
  - CREATE TABLE 문
  - CREATE INDEX 문
  - RLS 정책 (ALTER TABLE ... ENABLE ROW LEVEL SECURITY, CREATE POLICY)
  - DB Function (CREATE OR REPLACE FUNCTION)
  - Trigger (CREATE TRIGGER)
  - INSERT 문 (시드 데이터, 예: 카테고리 초기값)
- Section 7-5의 SQL 코드 블록 중 **로컬 DB(op-sqlite)용 테이블**은 제외한다.
  - 판별 기준: 블록 직전 텍스트에 "로컬", "op-sqlite", "SQLite", "local" 등의 키워드가 있으면 제외.
  - Supabase(PostgreSQL)용 테이블만 마이그레이션에 포함.
- **Section 7-5가 PRD에 없거나 SQL 코드 블록이 없으면 이 단계(A)를 스킵**하고 아래 메시지를 출력한다:
  ```
  ℹ️ PRD에 Section 7-5(DB 스키마)가 없습니다. 마이그레이션 파일 생성을 건너뜁니다.
  ```
- 마이그레이션 파일 경로에 이미 파일이 존재하면 사용자에게 덮어쓸지 확인한다.

**B. eas.json 생성**

- 파일 경로: `~/[프로젝트명]/eas.json`
- 파일이 이미 존재하면 스킵한다.
- 내용:
  ```json
  {
    "cli": {
      "version": ">= 13.0.0",
      "appVersionSource": "remote"
    },
    "build": {
      "development": {
        "developmentClient": true,
        "distribution": "internal",
        "ios": {
          "simulator": false
        },
        "android": {
          "buildType": "apk"
        }
      },
      "preview": {
        "distribution": "internal",
        "ios": {
          "simulator": false
        }
      },
      "production": {
        "autoIncrement": true
      }
    },
    "submit": {
      "production": {
        "ios": {
          "appleId": "",
          "ascAppId": "",
          "appleTeamId": ""
        },
        "android": {
          "serviceAccountKeyPath": "",
          "track": "internal"
        }
      }
    }
  }
  ```
- **설계 의도**:
  - `development`: dev-client 빌드. Android는 apk(에뮬레이터/실기기 직접 설치), iOS는 실기기 대상.
  - `preview`: 내부 테스트 배포용 (TestFlight/내부 트랙).
  - `production`: 스토어 배포. autoIncrement로 빌드 번호 자동 관리.
  - `submit`: Apple/Google 스토어 제출 설정. 초기에는 비워두고 사용자가 채움.
  - `appVersionSource: "remote"`: EAS에서 버전을 중앙 관리.

### 9단계: 의존성 설치
- 프로젝트 루트에서 실행한다: `~/[프로젝트명]`
- **공통 의존성** (CLAUDE.md의 "공통 의존성 > 필수 의존성" 목록):
  ```
  npx expo install @supabase/supabase-js react-native-paper react-native-safe-area-context zustand react-native-mmkv react-native-nitro-modules expo-router expo-linking expo-constants @expo/metro-runtime react-native-screens react-native-reanimated react-native-gesture-handler expo-status-bar expo-splash-screen expo-secure-store expo-font expo-system-ui expo-dev-client
  ```
- **조건부 의존성** (2단계에서 식별된 기술에 대응하는 패키지만):
  - CLAUDE.md의 "조건부 의존성 매핑" 테이블에서 패키지명과 **함께 설치할 피어**를 모두 찾아 설치한다.
  - 예: `Gifted Charts (SVG 기반)` → `npx expo install react-native-gifted-charts react-native-svg`
  - 예: `Firebase Analytics` → `npx expo install @react-native-firebase/app @react-native-firebase/analytics`
  ```
  npx expo install [조건부 패키지 + 피어 패키지]
  ```
- **개발 의존성** (CLAUDE.md의 "공통 의존성 > 필수 개발 의존성" 목록):
  ```
  npm install -D @types/react typescript
  ```
- 설치 실패한 패키지가 있으면 해당 패키지명과 에러를 사용자에게 알려주고, 나머지 설치는 계속 진행한다.

### 10단계: 완료 보고
아래 내용을 사용자에게 보고한다:

```
✅ 프로젝트 scaffold 완료

📁 프로젝트 경로: ~/[프로젝트명]
📦 공통 의존성: [설치된 패키지 수]개
📦 조건부 의존성: [설치된 패키지 목록 또는 "없음"]
⚠️ 미매핑 기술 (수동 설치 필요): [미매핑 기술 목록 또는 "없음"]
📂 생성된 폴더: [폴더 목록]
📋 커스텀 명령어: next.md, plan.md

🔧 Supabase: [초기화 완료 / 스킵됨 (사유)]
🗃️ 마이그레이션: [생성 완료 (N개 테이블, N개 RLS, N개 Function) / 스킵됨 (Section 7-5 없음)]
📱 app.json: [scheme, plugins, web.bundler 설정 완료 / 스킵됨 (사유)]
📱 eas.json: [생성 완료 (development, preview, production) / 이미 존재하여 스킵]

👉 다음 단계: /init-docs [NNN-아이디어명] [프로젝트명]
```

---

## 오류 처리
| 상황 | 처리 |
|---|---|
| 인자 부족/초과 | 사용 예시 안내 후 중단 |
| PRD 파일 미발견 | 정확한 파일명을 사용자에게 확인 요청 |
| 디렉토리 이미 존재 | 사용자에게 덮어쓸지 확인 |
| `npx create-expo-app` 실패 | 에러 메시지 출력 후 중단 |
| `npx expo install` 일부 실패 | 실패 패키지 알려주고 나머지 계속 |
| PRD에 미매핑 기술 포함 | 경고 출력 후 사용자 승인 시 계속, 거부 시 중단 |
| `npx supabase init` 실패 | 경고 출력 후 스킵. 나머지 scaffold 계속 진행 |
| PRD Section 7-5 없음 | 마이그레이션 생성 스킵. 안내 메시지 출력 |
| Section 7-5에 SQL 코드 블록 없음 | 마이그레이션 생성 스킵. 안내 메시지 출력 |
| `app.json` 파일 미발견 | 경고 출력 후 스킵 |
| `package.json` 파일 미발견 | 경고 출력 후 스킵 |
| `eas.json` 이미 존재 | 스킵 (덮어쓰지 않음) |
| 마이그레이션 파일 경로에 이미 파일 존재 | 사용자에게 덮어쓸지 확인 |
