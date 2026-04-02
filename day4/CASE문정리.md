# 📝 Day 4 (04/02) — CASE문 완전 정복


## 🔥 오늘 배운 핵심 한 줄 요약

> **CASE문 = SQL 안에서 IF/ELSE를 쓰는 방법**

---

## 1. CASE문이란?

- SQL에는 IF문이 없는 대신 **CASE문**으로 조건 분기를 처리합니다
- SELECT, WHERE, ORDER BY, GROUP BY 등 거의 모든 곳에서 사용 가능합니다

---

## 2. CASE문 기본 문법

### 단순 CASE문
```sql
SELECT 이름,
    CASE 등급
        WHEN 'VIP' THEN '최우수'
        WHEN 'GOLD' THEN '우수'
        WHEN 'SILVER' THEN '일반'
        ELSE '없음'
    END AS 등급명
FROM 고객;
```

### 검색 CASE문 (더 자주 사용!)
```sql
SELECT 이름, 급여,
    CASE
        WHEN 급여 >= 8000 THEN '상'
        WHEN 급여 >= 5000 THEN '중'
        ELSE '하'
    END AS 급여등급
FROM 직원;
```

> 💡 **단순 CASE** = 하나의 컬럼 값을 비교
> 💡 **검색 CASE** = 다양한 조건식 사용 가능 (더 유연함!)

---

## 3. CASE문 + GROUP BY (그룹핑)

카테고리를 직접 만들어서 그룹핑할 수 있습니다.

```sql
-- 나이대별 고객 수
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

> ⚠️ SELECT의 CASE문과 GROUP BY의 CASE문이 **동일**해야 합니다

---

## 4. CASE문 + 조건부 집계 

### 행 → 열 변환 (피벗)

```sql
-- 부서별 남/여 인원수를 열로 표시
SELECT 부서,
    COUNT(CASE WHEN 성별 = '남' THEN 1 END) AS 남자,
    COUNT(CASE WHEN 성별 = '여' THEN 1 END) AS 여자
FROM 직원
GROUP BY 부서;
```

### 결과

| 부서 | 남자 | 여자 |
|------|------|------|
| 개발 | 5 | 3 |
| 영업 | 4 | 6 |
| 인사 | 2 | 4 |

### SUM + CASE (조건부 합계)

```sql
-- 상태별 주문 금액 합계
SELECT
    SUM(CASE WHEN 상태 = '완료' THEN 금액 ELSE 0 END) AS 완료_금액,
    SUM(CASE WHEN 상태 = '취소' THEN 금액 ELSE 0 END) AS 취소_금액,
    SUM(CASE WHEN 상태 = '대기' THEN 금액 ELSE 0 END) AS 대기_금액
FROM 주문;
```

### AVG + CASE (조건부 평균)

```sql
-- 부서별 정규직만의 평균 급여
SELECT 부서,
    AVG(CASE WHEN 고용유형 = '정규직' THEN 급여 END) AS 정규직_평균급여
FROM 직원
GROUP BY 부서;
```

> 💡 CASE에서 ELSE를 안 쓰면 조건에 안 맞는 행은 **NULL**이 됩니다
> 💡 COUNT는 NULL을 세지 않고, AVG도 NULL을 제외하고 계산합니다

---

## 5. CASE문 활용 꿀팁

### ORDER BY에서 사용 (커스텀 정렬)
```sql
-- VIP를 먼저, 그 다음 GOLD, SILVER 순
SELECT * FROM 고객
ORDER BY
    CASE 등급
        WHEN 'VIP' THEN 1
        WHEN 'GOLD' THEN 2
        WHEN 'SILVER' THEN 3
        ELSE 4
    END;
```

### WHERE에서 사용
```sql
-- 조건에 따라 다른 필터링
SELECT * FROM 주문
WHERE
    CASE
        WHEN 회원유형 = 'VIP' THEN 금액 >= 10000
        ELSE 금액 >= 50000
    END;
```

---

## 📌 오늘의 핵심 포인트

1. **CASE문** = SQL의 IF/ELSE (조건에 따라 다른 값 반환)
2. **검색 CASE** (WHEN 조건식)가 단순 CASE보다 더 유연하고 자주 쓰임
3. **COUNT/SUM/AVG + CASE** = 조건부 집계 → 행을 열로 변환하는 피벗에 핵심!
4. **GROUP BY + CASE** = 직접 만든 카테고리로 그룹핑 가능
5. **ELSE 생략 시 NULL** → COUNT는 NULL 제외, 이걸 이용해서 조건부 카운팅

---
