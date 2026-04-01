# 📝 Day 3 (04/01) — 서브쿼리 + UNION

> SQL 기본편 섹션 4 + 섹션 5 | 서브쿼리 전체 + UNION

---

## 🔥 오늘 배운 핵심 한 줄 요약

> **서브쿼리 = 쿼리 안에 쿼리, UNION = 결과를 위아래로 합치기**

---

## 1. 서브쿼리란?

- SQL 안에 또 다른 SQL이 들어가는 것
- 복잡한 질문을 단계별로 쪼개서 답할 수 있음
- 위치에 따라 이름이 다름

---

## 2. 서브쿼리 종류

### 스칼라 서브쿼리 (SELECT절)

> 결과가 **딱 1개 값**인 서브쿼리

```sql
SELECT 이름,
       (SELECT AVG(급여) FROM 직원) AS 평균급여
FROM 직원;
```

- 모든 행에 같은 값이 반복됨
- 비교용으로 자주 사용

### 다중 행 서브쿼리 (WHERE절 + IN, ANY, ALL)

> 결과가 **여러 행**인 서브쿼리

```sql
-- IN: 목록 중 하나라도 매칭
SELECT * FROM 직원
WHERE 부서ID IN (SELECT 부서ID FROM 부서 WHERE 지역 = '서울');

-- ANY: 하나라도 만족하면 TRUE
WHERE 급여 > ANY (SELECT 급여 FROM 직원 WHERE 부서 = '영업');

-- ALL: 전부 만족해야 TRUE
WHERE 급여 > ALL (SELECT 급여 FROM 직원 WHERE 부서 = '영업');
```

### 다중 컬럼 서브쿼리

> 여러 컬럼을 동시에 비교

```sql
WHERE (부서ID, 직급) IN (SELECT 부서ID, 직급 FROM 조건테이블);
```

### 상관 서브쿼리 ⚠️ 핵심!

> **바깥 쿼리의 값을 안쪽에서 참조**하는 서브쿼리

```sql
-- 자기 부서 평균보다 급여가 높은 직원
SELECT *
FROM 직원 e
WHERE 급여 > (
    SELECT AVG(급여)
    FROM 직원
    WHERE 부서ID = e.부서ID
);
```

- 행마다 서브쿼리가 실행됨
- EXISTS와 자주 함께 사용

```sql
-- 주문이 있는 고객만
SELECT *
FROM 고객 c
WHERE EXISTS (
    SELECT 1 FROM 주문 o WHERE o.고객ID = c.고객ID
);
```

### SELECT 서브쿼리 (FROM절)

> 서브쿼리 결과를 **임시 테이블처럼** 사용

```sql
SELECT *
FROM (
    SELECT 부서ID, AVG(급여) AS 평균급여
    FROM 직원
    GROUP BY 부서ID
) AS 부서별급여
WHERE 평균급여 >= 5000;
```

- 반드시 별칭(AS) 필요!

### 테이블 서브쿼리

> INSERT, CREATE 등에서 서브쿼리로 데이터 넣기

```sql
INSERT INTO 백업테이블
SELECT * FROM 원본테이블 WHERE 조건;
```

---

## 3. 서브쿼리 vs 조인 🔥 면접 단골!

| 비교 | 서브쿼리 | 조인 |
|------|---------|------|
| 구조 | 쿼리 안에 쿼리 | 테이블을 옆으로 합침 |
| 가독성 | 단계별로 읽기 쉬움 | 한눈에 관계 파악 |
| 성능 | 상관 서브쿼리는 느릴 수 있음 | 일반적으로 더 빠름 |
| 사용 시점 | 단일 값 비교, EXISTS | 여러 테이블 데이터 동시 필요 |

> 💡 **실무 팁**: 같은 결과라면 조인이 성능상 유리한 경우가 많음. 하지만 가독성이 더 좋은 쪽을 선택하기도 함.

---

## 4. UNION

### UNION

> 두 쿼리 결과를 **위아래로 합치기** (중복 제거)

```sql
SELECT 이름, '정규직' AS 유형 FROM 정규직
UNION
SELECT 이름, '계약직' AS 유형 FROM 계약직;
```

- 컬럼 수와 데이터 타입이 같아야 함
- **중복 행 자동 제거**

### UNION ALL

> 중복 제거 없이 **전부 합치기**

```sql
SELECT 이름 FROM 정규직
UNION ALL
SELECT 이름 FROM 계약직;
```

- 중복 제거 안 해서 **UNION보다 빠름**
- 중복이 없는 게 확실하면 UNION ALL 사용

### UNION 정렬

```sql
SELECT 이름, 급여 FROM 정규직
UNION
SELECT 이름, 급여 FROM 계약직
ORDER BY 급여 DESC;
```

- ORDER BY는 **마지막에 한 번만** 사용

---

## 5. UNION vs JOIN 비교

| 비교 | UNION | JOIN |
|------|-------|------|
| 방향 | 위아래로 합침 (행 추가) | 옆으로 합침 (열 추가) |
| 조건 | 컬럼 수/타입 일치 필요 | ON 조건으로 연결 |
| 용도 | 같은 구조의 데이터 합칠 때 | 다른 테이블 정보 연결할 때 |

---

## 📌 오늘의 핵심 포인트

1. **상관 서브쿼리** = 바깥 쿼리를 참조하는 서브쿼리 (행마다 실행)
2. **EXISTS** = "있는지 없는지"만 체크할 때 (상관 서브쿼리와 세트)
3. **서브쿼리 vs 조인** = 면접 단골! 성능은 조인이 대체로 유리
4. **UNION** = 위아래로 합치기 (중복 제거)
5. **UNION ALL** = 중복 포함 합치기 (더 빠름)


> 💡 **내일 Day 4 예고**: CASE문 + 뷰 + 인덱스 + 트랜잭션
> 인덱스가 DE한테 제일 중요한 파트! 컨디션 회복해서 가보자!
