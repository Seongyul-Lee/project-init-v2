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

### 2단계: PRD 읽기 (Section 7-1만)
- PRD 파일을 찾는다:
  - 경로: `~/app-idea-lab/ideas/adopted/`
  - 파일명 패턴: `NNN-*-prd.md` (예: `001-데일리셀프-prd.md`)
  - 해당 패턴의 파일이 없으면 사용자에게 정확한 파일명을 확인하고 중단한다.
- PRD 파일에서 **Section 7-1 (기술 스택)만** 읽는다.
  - `## 7-1.` 또는 `### 7-1.` 헤딩부터 `## 7-2.` 또는 `### 7-2.` 헤딩 직전까지.
- 읽은 내용에서 CLAUDE.md의 "조건부 의존성 매핑" 테이블에 해당하는 기술을 식별한다.
  - 식별된 기술 목록을 기록해둔다 (7단계에서 사용).
- **미매핑 기술 감지**: Section 7-1에 언급된 모든 기술/라이브러리를 나열하고, CLAUDE.md의 "공통 의존성"과 "조건부 의존성 매핑" 테이블에 **모두 해당하지 않는** 기술이 있는지 확인한다.
  - 미매핑 기술이 발견되면 사용자에게 아래 형식으로 경고한다:
    ```
    ⚠️ PRD Section 7-1에 매핑되지 않는 기술이 포함되어 있습니다:
    - [기술명]: 자동 설치 불가. scaffold 완료 후 수동 설치가 필요합니다.
    ```
  - 사용자에게 "계속 진행할까요?"를 확인한 뒤, 승인 시 다음 단계로 진행한다.
  - 미매핑 기술 목록은 8단계 완료 보고에도 포함한다.

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

### 5단계: 폴더 구조 생성
- 프로젝트 루트: `~/[프로젝트명]`
- CLAUDE.md의 "표준 폴더 구조"를 참조하여 아래 디렉토리를 생성한다:
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

### 6단계: 기본 파일 생성
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
- **`.env.example`**:
  ```
  EXPO_PUBLIC_SUPABASE_URL=
  EXPO_PUBLIC_SUPABASE_ANON_KEY=
  ```
- 기존 `.gitignore`에 아래 항목을 추가한다 (이미 있으면 생략):
  ```
  .env
  .env.local
  ```
- **커스텀 명령어 복사**: `~/project-init/templates/commands/` 디렉토리의 파일을 프로젝트의 `.claude/commands/`로 복사한다.
  - `next.md` — Task Master 다음 작업 조회
  - `plan.md` — Plan Mode 진입 + 구현 계획 문서 생성

### 7단계: 의존성 설치
- 프로젝트 루트에서 실행한다: `~/[프로젝트명]`
- **공통 의존성** (CLAUDE.md의 "공통 의존성 > 필수 의존성" 목록):
  ```
  npx expo install @supabase/supabase-js react-native-paper react-native-safe-area-context zustand react-native-mmkv react-native-nitro-modules expo-router expo-linking expo-constants @expo/metro-runtime react-native-screens react-native-reanimated react-native-gesture-handler expo-status-bar expo-splash-screen expo-secure-store expo-font expo-system-ui expo-dev-client
  ```
- **조건부 의존성** (2단계에서 식별된 기술에 대응하는 패키지만):
  - CLAUDE.md의 "조건부 의존성 매핑" 테이블에서 패키지명과 **함께 설치할 피어**를 모두 찾아 설치한다.
  - 예: `Chart (Gifted Charts)` → `npx expo install react-native-gifted-charts react-native-svg`
  - 예: `Analytics (Firebase)` → `npx expo install @react-native-firebase/app @react-native-firebase/analytics`
  ```
  npx expo install [조건부 패키지 + 피어 패키지]
  ```
- **개발 의존성** (CLAUDE.md의 "공통 의존성 > 필수 개발 의존성" 목록):
  ```
  npm install -D @types/react typescript
  ```
- 설치 실패한 패키지가 있으면 해당 패키지명과 에러를 사용자에게 알려주고, 나머지 설치는 계속 진행한다.

### 8단계: 완료 보고
아래 내용을 사용자에게 보고한다:

```
✅ 프로젝트 scaffold 완료

📁 프로젝트 경로: ~/[프로젝트명]
📦 공통 의존성: [설치된 패키지 수]개
📦 조건부 의존성: [설치된 패키지 목록 또는 "없음"]
⚠️ 미매핑 기술 (수동 설치 필요): [미매핑 기술 목록 또는 "없음"]
📂 생성된 폴더: [폴더 목록]
📋 커스텀 명령어: next.md, plan.md

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
