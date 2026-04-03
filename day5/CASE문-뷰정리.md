# 📝 Day 5 (04/03) — CASE문 + 뷰(View)

## 🔥 오늘 배운 핵심 한 줄 요약

> **CASE문 = SQL의 IF/ELSE, 뷰 = 저장된 SELECT문**

---

## 1. CASE문

### 기본 문법

```sql
-- 검색 CASE (가장 많이 사용)
SELECT 이름, 급여,
    CASE
        WHEN 급여 >= 8000 THEN '상'
        WHEN 급여 >= 5000 THEN '중'
        ELSE '하'
    END AS 급여등급
FROM 직원;
```

### CASE + GROUP BY (카테고리 만들어서 그룹핑)

```sql
SELECT
    CASE
        WHEN 나이 < 20 THEN '10대'
        WHEN 나이 < 30 THEN '20대'
        WHEN 나이 < 40 THEN '30대'
        ELSE '40대 이상'
    END AS 나이대,
    COUNT(*) AS 인원수
FROM 고객
GROUP BY
    CASE
        WHEN 나이 < 20 THEN '10대'
        WHEN 나이 < 30 THEN '20대'
        WHEN 나이 < 40 THEN '30대'
        ELSE '40대 이상'
    END;
```

### CASE + 조건부 집계 (행→열 변환, 피벗) ⭐

```sql
-- 부서별 남/여 인원수를 열로 표시
SELECT 부서,
    COUNT(CASE WHEN 성별 = '남' THEN 1 END) AS 남자,
    COUNT(CASE WHEN 성별 = '여' THEN 1 END) AS 여자
FROM 직원
GROUP BY 부서;
```

```sql
-- 조건부 합계
SELECT
    SUM(CASE WHEN 상태 = '완료' THEN 금액 ELSE 0 END) AS 완료_금액,
    SUM(CASE WHEN 상태 = '취소' THEN 금액 ELSE 0 END) AS 취소_금액
FROM 주문;
```

### 핵심 포인트
- **COUNT + CASE**: ELSE 안 쓰면 NULL → COUNT가 세지 않음 (이걸 이용!)
- **SUM + CASE**: ELSE 0 으로 해야 정확한 합계
- **AVG + CASE**: NULL 자동 제외되므로 ELSE 안 써도 OK

---

## 2. 뷰(View)

### 뷰란?

- **자주 사용하는 SELECT문을 저장**해놓은 가상 테이블
- 실제 데이터를 갖고 있지 않음 (쿼리만 저장)

### 뷰 생성

```sql
CREATE VIEW 부서별_인원수 AS
SELECT 부서, COUNT(*) AS 인원수
FROM 직원
GROUP BY 부서;
```

### 뷰 사용 (일반 테이블처럼!)

```sql
SELECT * FROM 부서별_인원수;
SELECT * FROM 부서별_인원수 WHERE 인원수 >= 5;
```

### 뷰 수정

```sql
ALTER VIEW 부서별_인원수 AS
SELECT 부서, COUNT(*) AS 인원수, AVG(급여) AS 평균급여
FROM 직원
GROUP BY 부서;
```

### 뷰 삭제

```sql
DROP VIEW 부서별_인원수;
```

### 뷰의 장점
- **편의성**: 복잡한 쿼리를 간단하게 재사용
- **보안성**: 특정 컬럼만 노출 가능 (급여 같은 민감 정보 숨기기)
- **독립성**: 테이블 구조가 바뀌어도 뷰만 수정하면 됨

### 뷰의 단점
- 뷰에 INSERT/UPDATE/DELETE가 제한될 수 있음
- 뷰 위에 뷰를 쌓으면 성능 저하 가능
- 인덱스를 직접 걸 수 없음

> 💡 **내일 Day 6 예고**: 인덱스 (섹션 8 + 9)
> DE한테 가장 중요한 파트! "왜 쿼리가 느린지" 이해하는 핵심!
