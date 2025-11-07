# Project-Specific Agent Guide

이 문서는 이 프로젝트에 특화된 워크플로우와 설정 정보를 담고 있습니다.

## Quick Commands

### Development

```bash
# 개발 서버 시작 (Turbopack 사용)
pnpm dev

# 프로덕션 빌드
pnpm build

# 프로덕션 서버 시작
pnpm start

# 린트 실행
pnpm lint

# Biome 포맷팅 및 린트
pnpm format
pnpm check:fix
```

## Service-Specific Configuration

### Next.js Configuration

**파일**: `next.config.ts`

현재 기본 설정이지만, 다음 옵션들을 고려할 수 있습니다:

```typescript
const nextConfig: NextConfig = {
  // 환경 변수 설정
  env: {
    CUSTOM_KEY: process.env.CUSTOM_KEY,
  },

  // 이미지 최적화 설정
  images: {
    domains: ['example.com'],
  },

  // 리다이렉트 설정
  async redirects() {
    return [
      {
        source: '/old-path',
        destination: '/new-path',
        permanent: true,
      },
    ]
  },

  // 리라이트 설정
  async rewrites() {
    return [
      {
        source: '/api/proxy/:path*',
        destination: 'https://api.example.com/:path*',
      },
    ]
  },
}
```

### TypeScript Configuration

**파일**: `tsconfig.json`

현재 설정:

- Strict mode 활성화
- Path alias: `@/*` → `./*`
- ES2017 타겟
- Next.js 플러그인 사용

### Tailwind CSS Configuration

**파일**: `app/globals.css`

shadcn/ui 스타일이 포함되어 있으며, CSS 변수를 통해 테마를 관리합니다.

### shadcn/ui Configuration

**파일**: `components.json`

- Style: `new-york`
- Base color: `neutral`
- CSS variables 사용
- Icon library: `lucide`

### Biome Configuration

**파일**: `biome.json`

이 프로젝트는 Biome을 linter와 formatter로 사용합니다.

#### 주요 설정

- **Formatter**: 활성화됨

  - Indent: 2 spaces
  - Line width: 80
  - Line ending: LF
  - Attribute position: auto

- **Linter**: 활성화됨

  - Style 규칙: 엄격한 코딩 스타일 적용
  - A11y 규칙: 접근성 경고 활성화
  - Correctness 규칙: 정확성 검사
  - Nursery 규칙: 실험적 규칙 포함

- **JavaScript 설정**:

  - JSX Runtime: `reactClassic`
  - Quote style: `single`
  - JSX Quote style: `double`
  - Semicolons: `always`
  - Trailing commas: `all`

- **JSON 설정**:

  - Comments 허용: `true`
  - Trailing commas: `false`

- **CSS**: Formatter 및 Linter 비활성화

- **Import 정리**: 자동 활성화 (`organizeImports: on`)

#### 제외된 파일/디렉토리

다음 항목들은 Biome 검사에서 제외됩니다:

- `node_modules/`
- `.next/`
- `public/`
- `.vercel/`
- `playwright-report/`
- `components/ui/` (shadcn/ui 컴포넌트)
- `types/api.ts`
- `packages/**/*`
- `terraform/`
- `pnpm-lock.yaml`
- `lib/db/migrations`
- `lib/editor/react-renderer.tsx`

#### Biome 명령어

**package.json 스크립트 사용 (권장)**:

```bash
# 코드 포맷팅 (자동 수정)
pnpm format

# 코드 포맷팅 검사만 (수정 안 함)
pnpm format:check

# 린트 검사 (수정 안 함)
pnpm check

# 린트 검사 및 자동 수정
pnpm check:fix
```

**직접 명령어 사용**:

```bash
# 특정 파일만 검사
pnpm biome check app/page.tsx

# 설정 검증
pnpm biome check --config-path=biome.json biome.json
```

#### Override 설정

Playwright 테스트 파일에 대해서는 특별한 규칙이 적용됩니다:

- `**/playwright/**` 경로의 파일들
- `noEmptyPattern` 규칙 비활성화 (Playwright 요구사항)

#### 주의사항

- Biome 설정 파일(`biome.json`) 자체는 JSON 주석을 지원하지 않습니다.
- 다른 JSON 파일들은 `json.parser.allowComments: true` 설정으로 주석을 사용할 수 있습니다.
- CSS 파일은 Biome의 포맷팅/린팅 대상에서 제외됩니다.

## Task Management Workflow

### Development Documentation System

프로젝트의 개발 문서화 워크플로우:

1. **문서 작성 위치**

   - 일반 가이드: `CLAUDE.md`
   - 프로젝트 특화 정보: `AGENTS.md` (이 파일)
   - 코드 주석: JSDoc 형식 사용

2. **문서 업데이트 프로세스**

   ```
   코드 변경 → 관련 문서 업데이트 → 커밋
   ```

3. **주요 문서 구조**
   - `CLAUDE.md`: 프로젝트 기본 정보, 요구사항, 기본 명령어
   - `AGENTS.md`: 프로젝트 특화 워크플로우, 설정, 테스트 가이드
   - `README.md`: 프로젝트 소개 및 시작 가이드

### Git Workflow

```bash
# 기능 브랜치 생성
git checkout -b feature/your-feature-name

# 변경사항 커밋
git add .
git commit -m "feat: your feature description"

# 메인 브랜치로 병합 전 테스트
pnpm test
pnpm lint
pnpm check
pnpm build
```

## Testing Authenticated Routes

### API Route 테스트

Next.js App Router에서 인증이 필요한 API 라우트를 테스트하는 방법:

1. **API Route 생성 예시**

   ```typescript
   // app/api/protected/route.ts
   import { NextRequest, NextResponse } from 'next/server'

   export async function GET(request: NextRequest) {
     const token = request.headers.get('authorization')

     if (!token || !isValidToken(token)) {
       return NextResponse.json({ error: 'Unauthorized' }, { status: 401 })
     }

     return NextResponse.json({ data: 'Protected data' })
   }
   ```

2. **테스트 스크립트**

   ```bash
   # 인증 토큰과 함께 API 호출 테스트
   curl -H "Authorization: Bearer YOUR_TOKEN" http://localhost:3000/api/protected
   ```

3. **환경 변수 설정**
   `.env.local` 파일에 테스트용 토큰 설정:
   ```env
   TEST_AUTH_TOKEN=your-test-token-here
   ```

### 인증 미들웨어 테스트

```typescript
// middleware.ts (프로젝트 루트)
import { NextResponse } from 'next/server'
import type { NextRequest } from 'next/server'

export function middleware(request: NextRequest) {
  const token = request.cookies.get('auth-token')

  if (!token && request.nextUrl.pathname.startsWith('/protected')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }

  return NextResponse.next()
}

export const config = {
  matcher: ['/protected/:path*'],
}
```

## Workflow Dry-Run Mode

### 빌드 검증 (Dry-Run)

실제 배포 전에 빌드가 성공하는지 확인:

```bash
# 빌드만 실행 (서버 시작 안 함)
pnpm build

# 빌드 결과 확인
ls -la .next/

# 타입 체크만 실행
pnpm tsc --noEmit
```

### 린트 검증

```bash
# Next.js 린트 실행 (자동 수정 안 함)
pnpm lint

# Biome 포맷팅 검사 (수정 안 함)
pnpm format:check

# Biome 포맷팅 실행 (자동 수정)
pnpm format

# Biome 린트 검사 (수정 안 함)
pnpm check

# Biome 린트 검사 및 자동 수정
pnpm check:fix
```

### 환경 변수 검증

```bash
# 환경 변수 파일 확인 (실제 로드 안 함)
cat .env.local
cat .env.example

# Next.js 환경 변수 검증
pnpm build  # 빌드 시 환경 변수 검증됨
```

## Browser Tools Configuration

### Chrome DevTools 설정

1. **Network 탭**

   - Preserve log 활성화
   - Disable cache 활성화 (개발 중)

2. **Console 설정**

   - Show timestamps 활성화
   - Log XMLHttpRequests 활성화

3. **Application 탭**
   - Local Storage 확인
   - Session Storage 확인
   - Cookies 확인

### 개발 도구 확장 프로그램

권장 Chrome 확장 프로그램:

- React Developer Tools
- Redux DevTools (Redux 사용 시)
- Vue.js devtools (Vue 사용 시)

### 브라우저 호환성

이 프로젝트는 다음 브라우저를 지원합니다:

- Chrome (최신 2개 버전)
- Firefox (최신 2개 버전)
- Safari (최신 2개 버전)
- Edge (최신 2개 버전)

### 모바일 테스트

```bash
# 개발 서버를 네트워크에 노출
pnpm dev --hostname 0.0.0.0

# 로컬 IP로 접속 (예: http://192.168.1.100:3000)
```

## 추가 워크플로우 팁

### 컴포넌트 개발

1. 커스텀 컴포넌트 위치:
   - `components/ui/`: shadcn/ui 컴포넌트
   - `components/`: 프로젝트 커스텀 컴포넌트

### 환경 변수 관리

`.env.local` 파일 사용 (Git에 커밋하지 않음):

```env
NEXT_PUBLIC_API_URL=http://localhost:3000/api
DATABASE_URL=your-database-url
```

### 성능 최적화

1. 이미지 최적화: `next/image` 사용
2. 폰트 최적화: `next/font` 사용 (이미 적용됨)
3. 코드 스플리팅: Next.js 자동 처리

### 디버깅

```bash
# 개발 서버 디버그 모드
NODE_OPTIONS="--inspect" pnpm dev

# Chrome에서 chrome://inspect 접속하여 디버깅
```

---

> **참고**: 이 문서는 프로젝트가 발전함에 따라 지속적으로 업데이트됩니다.
