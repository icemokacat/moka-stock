# ai-service

Claude API 스트리밍 · 자연어 스크리닝 · 종목 파싱 · DB 저장

## 포트: 8084

## 주요 기술
- Claude API (claude-sonnet-4) — SSE 스트리밍
- Web Search 툴 연동
- X-Anthropic-Key 헤더 포워딩 (사용자 개인 키)
- Redis — 스크리닝 결과 캐시 (TTL 1h)

## API Key 처리 방식
사용자가 프론트 /settings 에서 등록한 개인 Anthropic API Key를
X-Anthropic-Key 헤더로 전달받아 Claude API 호출 시 사용.
서버는 키를 저장하지 않는다.

## 패키지 구조
```
ai-service/src/main/java/io/moka/ai/
├── domain/          ScreeningReport, SavedStock, Category 엔티티
├── application/     ScreeningUseCase, StockSaveUseCase
├── infrastructure/  ClaudeApiClient, StockRepository
└── presentation/    ScreeningController
```

## 스크리닝 플로우
```
POST /api/ai/screen
  Header: X-Anthropic-Key: sk-ant-...
  Body: { "query": "나스닥 100 중 PER 15 이하..." }
  →  Claude API 스트리밍
  →  응답 JSON 블록 파싱
  →  SavedStock + Category 저장
  →  SSE 스트림 반환
```
