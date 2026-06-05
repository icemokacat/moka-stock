# moka-stock ☕

> AI 기반 주식 스크리닝 & 종목 저장 플랫폼
> MSA + TDD + Redis + Prometheus/Grafana 포트폴리오 프로젝트

---

## 프로젝트 개요

자연어로 주식 스크리닝 조건을 입력하면 AI가 분석하고, 결과 종목을 카테고리별로 저장·관리할 수 있는 플랫폼.
실거래 기능 없이 데이터 조회·분석·기록에 집중한다.

```
"나스닥 100 구성 종목 중 PER 15 이하, ROE 20% 이상인 종목 찾아줘"
→ AI 분석 → 종목 파싱 → 카테고리 태깅 → DB 저장
```

---

## 기술 스택

| 영역       | 기술                                              |
|------------|---------------------------------------------------|
| Backend    | Java 21 / Spring Boot 3.x                         |
| API Gateway| Spring Cloud Gateway                              |
| 인증       | Spring Security + JWT + OAuth2                    |
| DB         | PostgreSQL 16 (서비스별 DB 분리)                  |
| Cache      | Redis 7                                           |
| AI 연동    | Claude API (Anthropic) — claude-sonnet-4          |
| 외부 데이터| 환율/지수 공공 API                                |
| 모니터링   | Micrometer + Prometheus + Grafana                 |
| 컨테이너   | Docker / Docker Compose                           |
| CI/CD      | GitHub Actions                                    |
| Frontend   | React 18 + TypeScript + Vite + Tailwind CSS       |
| 테스트     | JUnit 5 + Mockito + Testcontainers                |

---

## Anthropic API Key 정책

사용자가 각자 [Anthropic Console](https://console.anthropic.com)에서 API Key를 발급받아 `/settings`에서 등록한다.
서버는 키를 저장하지 않으며 `X-Anthropic-Key` 헤더로 Claude API에 포워딩만 한다.

---

## 서비스 구조 (MSA)

```
클라이언트 (React)
      │
      ▼
API Gateway (8080) ── JWT 검증, 라우팅
      │
      ├─ auth-service      (8081)  회원가입 · 로그인 · JWT · Redis 블랙리스트
      ├─ user-service      (8082)  프로필 · 설정
      ├─ dashboard-service (8083)  환율 · 코스피/코스닥 · Redis TTL 캐시
      ├─ ai-service        (8084)  Claude API 스트리밍 · 스크리닝 · 종목 저장
      └─ board-service     (8085)  자유게시판 · 조회수 Redis 카운터
```

### 레포 구조 (Gradle Multi-module 모노레포)

```
moka-stock/
├── common/
├── gateway/
├── services/
│   ├── auth-service/
│   ├── user-service/
│   ├── dashboard-service/
│   ├── ai-service/
│   └── board-service/
├── infra/
│   ├── docker-compose.yml
│   ├── prometheus/
│   └── grafana/
├── .env.example
├── .gitignore
├── CLAUDE.md
└── README.md
```

---

## 종목 저장 & 카테고리 구조

| 대분류     | 값                                                                    |
|------------|-----------------------------------------------------------------------|
| 자산유형   | STOCK / ETF / BOND / REIT / CRYPTO                                    |
| 국가/시장  | US / KR / JP / EU / EM                                                |
| 섹터       | TECH / HEALTHCARE / FINANCE / ENERGY / CONSUMER / INDUSTRIAL / ...   |
| 전략       | VALUE / GROWTH / DIVIDEND / MOMENTUM                                  |

---

## 개발 단계 로드맵

| Phase | 내용                                      | 상태    |
|-------|-------------------------------------------|---------|
| 1     | 도메인 설계 & docker-compose 환경         | 진행 중 |
| 2     | auth-service TDD                          | 예정    |
| 3     | dashboard-service (캐시 패턴)             | 예정    |
| 4     | ai-service (스크리닝 + 종목 저장)         | 예정    |
| 5     | board-service                             | 예정    |
| 6     | API Gateway + 서비스 통합                 | 예정    |
| 7     | 모니터링 (Prometheus + Grafana)           | 예정    |
| 8     | Docker 완성 & CI/CD                       | 예정    |

---

## 로컬 개발 시작

```bash
# 환경변수 설정
cp .env.example .env
# .env 파일 편집 후

# 인프라 실행
docker-compose -f infra/docker-compose.yml up -d

# 공통 모듈 빌드
./gradlew :common:build

# 서비스 실행 (예: auth)
./gradlew :services:auth-service:bootRun

```
## 프론트엔드
[https://github.com/icemokacat/moka-stock-front](dd)

---

*moka-stock — built with ☕ and TDD*
