# dashboard-service

홈 대시보드 데이터 수집 · 환율 · 코스피/코스닥/나스닥 · Redis TTL 캐시

## 포트: 8083

## 주요 기술
- @Scheduled — 10분 주기 외부 API 수집
- @Cacheable + Redis TTL 5분
- WebSocket (추후) — 실시간 업데이트

## 외부 데이터 소스
- 환율: 한국은행 Open API (BOK_API_KEY)
- 국내 지수: 한국거래소 API
- 해외 지수: Yahoo Finance / Alpha Vantage
