# 데이터베이스 스키마

![ERD diagram](../assets/diagrams/erd-diagram.png)

### 1. AUTHOR (지은이)

인덱스:

- PRIMARY KEY: author_id

### 2. CATEGORY (카테고리)

인덱스:

- PRIMARY KEY: category_id

### 3. BOOK (도서)

인덱스:

- PRIMARY KEY: book_id

제약조건:

- fk_book_author: author_id → author.author_id

### 4. BOOK_CATEGORY (도서-카테고리)

인덱스:

- PRIMARY KEY: book_category_id
- UNIQUE KEY: (book_id, category_id) - 동일 도서-카테고리 조합 중복 방지
- KEY: book_id (특정 도서의 카테고리 조회)
- KEY: category_id (특정 카테고리의 도서 조회)

제약조건:

- fk_book_category_book: book_id → book.book_id
- fk_book_category_category: category_id → category.category_id

### 5. OWNED_BOOK (보유도서)

인덱스:

- PRIMARY KEY: owned_book_id
- UNIQUE KEY: barcode (바코드 중복 방지)
- KEY: book_id (특정 도서의 보유도서 조회)
- KEY: book_status (상태별 조회)
- KEY: (book_id, book_status) - 복합 인덱스 (도서별 상태 통계)

제약조건:

- fk_owned_book_book: book_id → book.book_id

### 🔒 비즈니스 제약조건

#### 애플리케이션 레벨 제약조건

#### BR-001: 신규 도서 카테고리 필수
```markdown
book_category 테이블에 최소 1개 레코드 생성 보장
애플리케이션에서 처리: 도서 생성 시 반드시 1개 이상 카테고리 연결
```

#### BR-003: 도서 카테고리 최소 개수 유지

```sql
-- 애플리케이션에서 처리: 카테고리 삭제 시 개수 확인
DELETE FROM book_category 실행 전 COUNT 검증
```

#### BR-007, BR-008: 도서 상태 전이 규칙
```sql 
-- 애플리케이션에서 처리: 상태 변경 전 현재 상태 확인
 UPDATE owned_book SET book_status = ? 실행 전 비즈니스 로직 검증
``` 