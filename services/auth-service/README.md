# auth-service

회원가입 · 로그인 · JWT 발급/갱신 · Redis 블랙리스트

## 포트: 8081

## 주요 기술
- Spring Security 6
- JWT (access: 1h / refresh: 7d)
- Redis — 로그아웃 블랙리스트

## 패키지 구조
```
auth-service/src/main/java/io/moka/auth/
├── domain/          User 엔티티, Repository 인터페이스
├── application/     SignupUseCase, LoginUseCase, TokenRefreshUseCase
├── infrastructure/  JPA, Redis, JwtProvider
└── presentation/    AuthController
```

## TDD 시작점
```bash
./gradlew :services:auth-service:test
```
