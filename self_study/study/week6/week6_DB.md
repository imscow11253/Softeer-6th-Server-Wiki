## SQL vs NOSQL의 차이와 예시 들어보기

전통적 관계형 데이터베이스(SQL)는 미리 정의된 스키마에 따라 테이블에 데이터를 저장하고, 테이블 간 JOIN 으로 관계를 표현한다. 
예컨대 “고객” 과 “주문” 이라는 두 테이블을 만들고 `customer_id` 외래키로 연결하는 식이다. 
반면 NoSQL(문서·키값·그래프·Wide-column 등)은 스키마가 느슨하거나 없으며, 문서(JSON) 에 고객과 주문을 한 덩어리로 넣을 수도 있다. 
몽고DB로 구현하면 `orders` 컬렉션 안 문서 한 개에 고객 정보와 주문 품목 배열이 모두 존재하는 식이다. 

- SQL: 정합성(ACID)·복잡한 JOIN에 강점, 수직적 확장 주도.
- NoSQL: 구조 변동이 잦은 데이터·대용량 수평 확장에 유리. 

## ACID / CAP에 대해서 설명하기

`ACID` 는 트랜잭션이 지켜야 할 네 가지 속성이다.
- (Atomicity) 하나면 전부, 실패면 전부 롤백.
- (Consistency) 제약조건을 깨지 않는다.
- (Isolation) 동시 실행돼도 서로 간섭 없음.
- (Durability) 커밋되면 전원 꺼져도 남는다. 은행 계좌 이체(출금+입금)가 대표 예다. 

CAP 이론은 분산 시스템에서 일관성(C), 가용성(A), 분할 허용성(P) 중 두 가지 특성만 완벽히 달성할 수 있다는 원리입니다.

1. Consistency & Partition Tolerance (CP) 예시￼

MongoDB
MongoDB는 네트워크 분할(Partition)이 발생하면, 데이터의 일관성(Consistency)을 유지하기 위해 일부 노드의 가용성(Availability)을 희생합니다.
예를 들어, 여러 서버에 데이터가 분산되어 있는데 네트워크 장애로 일부 서버가 서로 통신하지 못할 때, MongoDB는 일관성을 보장하기 위해 장애가 발생한 서버에서의 쓰기 작업을 중단시킵니다.
- 장점: 항상 최신 데이터만 읽을 수 있음
- 단점: 일부 서버가 일시적으로 응답하지 않을 수 있음

2. Availability & Partition Tolerance (AP) 예시

Cassandra
Cassandra는 네트워크 분할이 발생해도 모든 노드가 계속해서 요청에 응답할 수 있도록 설계되어 있습니다.
예를 들어, 서버 간 연결이 끊겨도 각 서버는 자신이 가진 데이터로 요청에 응답합니다.
- 장점: 항상 응답을 받을 수 있음
- 단점: 분할된 동안에는 최신 데이터가 아닐 수 있음(일관성 저하)

3. Consistency & Availability (CA) 예시

전통적 관계형 데이터베이스 (단일 서버에서 동작할 때)
예를 들어, 단일 서버에서 동작하는 MySQL은 네트워크 분할이 없으므로 일관성과 가용성을 모두 만족시킬 수 있습니다.
- 장점: 항상 최신 데이터, 항상 응답
- 단점: 분산 환경에서는 Partition Tolerance가 불가능

실제 상황 예시
- 은행 시스템:

은행은 일관성(Consistency)이 매우 중요합니다. 네트워크 장애가 발생하면 일부 서비스가 중단되더라도 데이터의 정확성을 우선시합니다. (CP)

- SNS 서비스:

소셜 미디어는 가용성(Availability)을 우선시합니다. 네트워크 장애가 발생해도 사용자는 계속해서 글을 읽고 쓸 수 있지만, 최신 데이터가 아닐 수 있습니다. (AP)

## 쿼리의 논리적인 실행순서에 대해서 학습

우리가 `SELECT ... FROM ... WHERE ...` 로 작성해도 DB 엔진은 다음 순서로 처리한다.
`FROM` → `JOIN` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `DISTINCT` → `ORDER BY` → `LIMIT`.
따라서 `WHERE` 절에서는 별칭이 아직 ‘탄생’하지 않았기에 사용할 수 없지만, `ORDER BY` 에서는 가능하다.￼

## 인덱스의 개념과 종류에 대해서 학습

인덱스는 책의 목차처럼 데이터 위치를 빠르게 찾아주는 자료구조(B-Tree, Hash 등) 다.
- Clustered Index: 테이블 자체가 키 순서로 정렬된다. PK 에 자동 생성되는 경우가 많아 테이블당 하나만 존재.
- Non-clustered Index: 데이터와 별도 공간에 키+포인터 저장. 하나의 테이블에 여러 개 가능.
- Hash Index: 단일 값 탐색(=)에 특화, 범위 검색에는 부적합.
- Composite Index: 두 개 이상 컬럼을 묶어 선택도를 높인다.

인덱스가 넘 많으면 optimizer가 어떤 인덱스를 써야하는지 혼란을 겪는다. 

## 인덱스가 필요한 상황에 대해서 설명

- WHERE 절에 자주 등장하고, 선택도(고유값 비율)가 높은 컬럼
- JOIN/ORDER BY/GROUP BY에서 자주 사용되는 키
- 대용량 테이블에서 소수 행만 조회
- 반대로 자주 수정되는 낮은 선택도의 컬럼은 오히려 인덱스 유지 비용이 더 클 수 있다. 

## 실행 쿼리 Explain의 컬럼에 나오는 항목에 대해서 학습
`EXPLAIN SELECT …` 를 수행하면 실행 계획이 표로 나타난다. 핵심 열은 아래와 같다.

- type: 접근 방법(ALL = 풀스캔, index, range, ref 등).
- possible_keys / key: 사용 가능 인덱스와 실제 선택된 인덱스.
- rows: 예상 탐색 행 수(작을수록 좋다).
- Extra `Using filesort`, `Using temporary` 등이 보이면 튜닝 후보.

예를 들어 `type = ALL`·`rows = 1,000,000`·`Extra = Using filesort` 라면 인덱스 추가를 고려해야 한다.￼

## 트랜잭션에 대해서 학습하고 설명하기

트랜잭션은 논리적으로 하나의 작업 단위를 말하며 ￼BEGIN/COMMIT/ROLLBACK구문으로 제어한다. 
쇼핑몰 결제를 예를 들면: 재고 감소, 주문 레코드 삽입, 결제 로그 기록이 모두 성공해야만 확정된다. 
중간 실패 시 `ROLLBACK` 으로 이전 상태로 복구되어 데이터 무결성이 유지된다. ACID 속성이 뒷받침되어야 신뢰할 수 있다.￼

격리수준
- read uncommited : commit 되지 않은 것을 read 할 수 있다. 
- read commited : commit 된 것만 read 할 수 있다. 
- repeatable read : 
- Serializable : 

https://mangkyu.tistory.com/299