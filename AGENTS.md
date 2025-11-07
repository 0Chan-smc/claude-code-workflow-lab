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

현재 기본 설정 사용 중.

### TypeScript Configuration

**파일**: `tsconfig.json`

- Strict mode 활성화
- Path alias: `@/*` → `./*`

### Tailwind CSS Configuration

**파일**: `app/globals.css`, `tailwind.config.ts`

플러그인: `tailwindcss-animate`, `@tailwindcss/typography`

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

- Formatter: 활성화 (indent 2 spaces, line width 80)
- Linter: 활성화 (style, a11y, correctness 규칙)
- JavaScript: single quotes, semicolons, trailing commas
- JSON: comments 허용
- Import 정리: 자동 활성화

#### 제외된 파일/디렉토리

`node_modules/`, `.next/`, `public/`, `components/ui/`, `playwright-report/` 등

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

## Task Management Workflow

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

### Git Hooks (Husky)

Husky `^9.1.7` 사용. Git hooks는 `.husky/` 디렉토리에 저장됩니다.

```bash
# Husky 초기화
pnpm husky init
```

## 추가 워크플로우 팁

### 주요 라이브러리

- **날짜 처리**: `date-fns`, `dayjs`
- **React 훅**: `usehooks-ts`
- **폼 관리**: `react-hook-form`, `zod`
- **스타일링**: `tailwindcss-animate`, `tailwind-merge`, `clsx`

### 컴포넌트 개발

- `components/ui/`: shadcn/ui 컴포넌트
- `components/`: 프로젝트 커스텀 컴포넌트

---

> **참고**: 이 문서는 프로젝트가 발전함에 따라 지속적으로 업데이트됩니다.
