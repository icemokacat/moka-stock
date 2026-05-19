# moka-stock — Project Context

AI 기반 주식 스크리닝 & 종목 저장 플랫폼.
자연어로 스크리닝 조건을 입력하면 Claude API가 분석하고, 결과를 카테고리별로 DB에 저장한다.
실거래 기능 없음. 데이터 조회 · 분석 · 기록에 집중.

## 레포 구조

```
moka-stock/
├── common/                  공통 DTO, 예외, 응답 포맷
├── gateway/                 Spring Cloud Gateway
├── services/
│   ├── auth-service/        JWT · Spring Security · Redis 블랙리스트
│   ├── user-service/        프로필 · 설정 · Anthropic API Key 관리
│   ├── dashboard-service/   환율 · 코스피 · Redis TTL 캐시
│   ├── ai-service/          Claude API 스트리밍 · 스크리닝 · 종목 저장
│   └── board-service/       자유게시판 · Redis 조회수 카운터
├── frontend/                React + TypeScript (Vite) — Claude Code 담당
└── infra/                   docker-compose, prometheus, grafana
```

## 기술 스택

- Backend: Java 21 / Spring Boot 3.x / Gradle multi-module
- DB: PostgreSQL 16 (서비스별 독립 DB)
- Cache: Redis 7
- AI: Claude API — claude-sonnet-4 (스트리밍 + 웹서치 툴)
- 모니터링: Micrometer + Prometheus + Grafana
- Frontend: React 18 + TypeScript + Vite + Tailwind CSS

## Anthropic API Key 정책

사용자가 각자 Anthropic API Key를 발급받아 등록한다. 서버는 키를 저장하지 않고 요청 헤더로 포워딩한다.

```
사용자 → 프론트에서 키 입력 → X-Anthropic-Key 헤더로 전달
                                        ↓
                          ai-service (키 포워딩만)
                                        ↓
                                  Claude API
```

- 키는 프론트 `localStorage`에만 저장 (HTTPS 필수)
- ai-service는 `@RequestHeader("X-Anthropic-Key")`로 수신 후 그대로 사용
- 서버 DB에 키 저장 안 함

## 서비스 포트

| 서비스            | 포트 |
|-------------------|------|
| API Gateway       | 8080 |
| auth-service      | 8081 |
| user-service      | 8082 |
| dashboard-service | 8083 |
| ai-service        | 8084 |
| board-service     | 8085 |
| Frontend dev      | 5173 |
| Redis             | 6379 |
| Prometheus        | 9090 |
| Grafana           | 3000 |

## 공통 API 응답 포맷

```json
{
  "success": true,
  "data": { ... },
  "error": null,
  "timestamp": "ISO-8601"
}
```

## 종목 카테고리 구조

SavedStock에 다중 카테고리 태깅.

- 자산유형: STOCK / ETF / BOND / REIT / CRYPTO
- 국가/시장: US / KR / JP / EU / EM
- 섹터: TECH / HEALTHCARE / FINANCE / ENERGY / CONSUMER / INDUSTRIAL / MATERIALS / UTILITIES / REAL_ESTATE / COMM_SERVICES
- 전략: VALUE / GROWTH / DIVIDEND / MOMENTUM

## Redis 키 네임스페이스

```
auth:blacklist:{jti}           JWT 블랙리스트 — TTL = 토큰 만료까지
dashboard:exchange:{currency}  환율 캐시 — TTL 5분
dashboard:index:{market}       지수 캐시 — TTL 5분
board:views:{postId}           조회수 카운터 — 영구, 주기적 DB sync
ai:screen:{queryHash}          스크리닝 결과 캐시 — TTL 1시간
```

## 코드 컨벤션

- 패키지 구조: `domain / application / infrastructure / presentation` (헥사고날)
- 테스트: JUnit 5 + Mockito, Testcontainers (통합 테스트)
- TDD: 테스트 먼저 작성 후 구현
- 예외: 커스텀 예외 클래스 사용 (`MokaException` 상속)
- 로그: `@Slf4j`, 레벨은 INFO (운영) / DEBUG (개발)

## 개발 명령어

```bash
# 로컬 인프라 실행
docker-compose -f infra/docker-compose.yml up -d

# 전체 빌드
./gradlew build

# 특정 서비스 실행
./gradlew :services:auth-service:bootRun

# 특정 서비스 테스트
./gradlew :services:auth-service:test

# 프론트엔드
cd frontend && npm run dev
```

## 현재 개발 단계

Phase 1 — 도메인 설계 & 로컬 환경 세팅 중.
순서: auth-service → dashboard-service → ai-service → board-service → gateway 통합 → 모니터링 → Docker 완성
