# frontend — React + TypeScript

moka-stock 프론트엔드. Claude Code가 전담 개발한다.
루트 컨텍스트 참조: @../CLAUDE.md

## 스택

- React 18 + TypeScript (strict mode)
- Vite
- Tailwind CSS
- React Query (서버 상태)
- Zustand (클라이언트 상태)
- React Router v6
- Axios (API 클라이언트)

## 폴더 구조

```
frontend/src/
├── api/               API 호출 함수 (서비스별 파일로 분리)
├── components/
│   ├── ui/            재사용 가능한 기본 컴포넌트 (Button, Input, Card ...)
│   └── features/      기능별 컴포넌트 (Screener, Dashboard, Board ...)
├── hooks/             커스텀 훅
├── pages/             라우트별 페이지 컴포넌트
├── stores/            Zustand 스토어 (auth, apiKey, ui)
├── types/             TypeScript 타입 정의
└── utils/             공통 유틸
```

## 페이지 구조

```
/                  홈 대시보드 — 환율, 코스피/코스닥, 나스닥
/screener          AI 스크리닝 — 자연어 입력, 스트리밍 응답, 결과 저장
/stocks            저장된 종목 목록 — 카테고리 필터링
/stocks/:id        종목 상세
/board             자유게시판 목록
/board/:id         게시글 상세
/board/write       게시글 작성
/login             로그인
/signup            회원가입
/settings          API Key 등록 및 계정 설정
```

## Anthropic API Key 처리

사용자가 `/settings`에서 키를 입력하면 `localStorage`에 저장.
스크리닝 요청 시 `X-Anthropic-Key` 헤더로 전달.

```typescript
// stores/apiKeyStore.ts
const useApiKeyStore = create((set) => ({
  apiKey: localStorage.getItem('anthropic_api_key') ?? '',
  setApiKey: (key: string) => {
    localStorage.setItem('anthropic_api_key', key);
    set({ apiKey: key });
  },
}));

// api/client.ts — 요청 인터셉터
client.interceptors.request.use((config) => {
  const apiKey = useApiKeyStore.getState().apiKey;
  if (apiKey) config.headers['X-Anthropic-Key'] = apiKey;
  return config;
});
```

## API 연동

모든 요청은 API Gateway(`:8080`)를 통한다.

```typescript
// api/client.ts
const client = axios.create({ baseURL: 'http://localhost:8080' });
// 인터셉터: JWT 자동 첨부, 401 시 refresh 처리
```

## 스크리닝 스트리밍 구현

ai-service SSE 스트리밍을 `fetch` + `ReadableStream`으로 처리.
응답 끝의 JSON 블록을 파싱해서 종목 테이블 렌더링.

```typescript
const parseStocks = (text: string) => {
  const match = text.match(/```json\n([\s\S]*?)\n```/);
  return match ? JSON.parse(match[1]) : null;
};
```

## 카테고리 타입 정의

```typescript
type AssetType = 'STOCK' | 'ETF' | 'BOND' | 'REIT' | 'CRYPTO';
type Market    = 'US' | 'KR' | 'JP' | 'EU' | 'EM';
type Sector    = 'TECH' | 'HEALTHCARE' | 'FINANCE' | 'ENERGY' | 'CONSUMER'
               | 'INDUSTRIAL' | 'MATERIALS' | 'UTILITIES' | 'REAL_ESTATE' | 'COMM_SERVICES';
type Strategy  = 'VALUE' | 'GROWTH' | 'DIVIDEND' | 'MOMENTUM';

interface SavedStock {
  id: string;
  ticker: string;
  name: string;
  per?: number;
  roe?: number;
  market: Market;
  assetType: AssetType;
  sector?: Sector;
  strategies: Strategy[];
  highlight?: string;
  savedAt: string;
}
```

## 컴포넌트 규칙

- 함수형 컴포넌트만 사용
- Props 타입은 `interface`로 정의, `type`은 유니온·유틸리티 타입에만
- 컴포넌트 파일명: PascalCase (`StockCard.tsx`)
- 훅 파일명: camelCase (`useScreener.ts`)
- `export default` 사용 (named export는 utils, types에만)

## 상태 관리 규칙

- 서버 상태: React Query (`useQuery`, `useMutation`)
- 전역 클라이언트 상태: Zustand (인증 토큰, API Key, UI 테마)
- 로컬 상태: `useState` / `useReducer`

## 스타일 규칙

- Tailwind 유틸리티 클래스 우선
- 커스텀 CSS는 `*.module.css`로 분리 (전역 스타일 지양)
- 다크모드 지원: `dark:` prefix 사용
- 반응형: mobile-first (`sm:`, `md:`, `lg:`)

## 개발 명령어

```bash
npm run dev       # 개발 서버 (localhost:5173)
npm run build     # 프로덕션 빌드
npm run preview   # 빌드 결과 미리보기
npm run lint      # ESLint
npm run typecheck # tsc --noEmit
```
