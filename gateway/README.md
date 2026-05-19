# gateway

Spring Cloud Gateway — 단일 진입점, JWT 검증, 라우팅

## 포트: 8080

## 라우팅 규칙
```
/api/auth/**      → auth-service:8081
/api/users/**     → user-service:8082
/api/dashboard/** → dashboard-service:8083
/api/ai/**        → ai-service:8084
/api/board/**     → board-service:8085
```
