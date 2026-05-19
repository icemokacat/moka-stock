# board-service

자유게시판 CRUD · 댓글 · 조회수 Redis 카운터 · 커서 페이지네이션

## 포트: 8085

## 주요 기술
- Redis INCR — 조회수 (주기적으로 PostgreSQL sync)
- 커서 기반 페이지네이션
