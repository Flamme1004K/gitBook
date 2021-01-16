---
description: 이 글은 손에 잡히는 10분 SQL을 공부하며 쓴 정리본 입니다.
---

# 정리 본

### 목차

* 테이블 생성과 조작
* 데이터형과 제약조건 
* 데이터 삽입하기
* 데이터 업데이트와 삭제
* 뷰 사용하기
* 인덱스 
* 트랜잭션 처리 관리하기
* 저장프로시저
* 커서 
* 트리거
* 데이터베이스 보안

### 테이블 생성과 조작

#### 테이블 생성

* DBMS가 지원하는 대화형 관리 툴
* SQL 문으로 테이블을 직접 생성

#### 기본 테이블 생성

```sql
CREATE TABLE [새로운 테이블 이름]
(
  [컬럼이름] [데이터형] [제약조건],
  [컬럼이름] [데이터형] [제약조건],
  ...
  [컬럼이름] [데이터형] [제약조건]
 );
```

테이블 생성 규칙 1. CREATE TABLE 문 뒤에 새로운 테이블 이름을 적는다. 2. 테이블 이름과 정의를 콤마\(,\)로 구분하여 적는다. 3. 일부 DBMS에서는 테이블의 위치도 명시

이러한 테이블 생성 규칙을 이용하여 테이블을 생성하면 된다.

**하지만 테이블을 생성하기 전에 우리는 데이터형과 제약조건에 대해서 우선 알아야한다.**

### 데이터형과 제약조건

#### 데이터형

데이터형을 알아야하는 이유는 무엇일까?

* 저장할 수 있는 데이터를 제한한다.
* 데이터를 바르게 정렬할 수 있도록 한다.
* 내부적으로 저장 공간을 효율적으로 사용할 수 있게 한다.

이렇게 함으로써 디스크 사용량을 최적화하는 데 중요한 역할을 한다.

데이터형의 종류는 **문자열 데이터형, 수치 데이터형, 날짜와 시간 데이터형, 바이너리 데이터형**으로 나뉜다.

#### 제약조건

제약조건이란 일단 데이터베이스 데이터를 어떻게 삽입하고 조작할 것인지 통제하는 규칙이다.

그렇기 때문에 DBMS마다 지원하는 수준이 다르다.

제약조건이 필요한 이유는

클라이언트 레벨에서 데이터 무결성 규칙을 확인하기 어렵기 때문에 미리 사전에 DBMS에 제약조건을 주는 것이다.

여기서 어떤 사람은 지나가고 어떤 사람은 알고 싶은 내용이 있을 것이다.

바로 **무결성**이다.

**무결성이란 무엇일까?**

무결성이란 여러가지 제한을 통하여 데이터에 대한 신뢰를 보장하게 하여 일관성을 유지 시켜주는 것이다.

데이터 무결성의 종류는 대략적으로

**널 무결성, 고유 무결성, 참조 무결성, 도메인 무결성, 키 무결성**이 있다.

[https://coding-factory.tistory.com/221](https://coding-factory.tistory.com/221) [https://jwprogramming.tistory.com/53](https://jwprogramming.tistory.com/53)

#### 제약 조건의 종류

* Null 값 사용하기
* 기본값 지정하기
* 기본키
* 왜래키
* 고유 키 무결성 제약 조건
* 체크 무결성 제약 조건

**Null값 사용하기**

```sql
CREATE TABLE Products 
(
    prod_id CHAR(10) NOT NULL,
    vend_id CHAR(10) NOT NULL,
    prod_name CHAR(254) NOT NULL,
    prod_price DECIMAL(8,2) NOT NULL,
    prod_desc VARCHAR(1000) NULL
);
```

NULL

* 열에 아무런 값을 넣지 않아도 행 삽입이 허용된다.

NOT NULL

* 값이 없는 행을 허용하지 않는다.
* NULL 값을 허용하지 않는 열만 기본 키로 사용할 수 있다.

NULL 값과 빈 문자열을 혼동해서는 안 된다. NULL은 아직 값이 없는 것이지 빈 문자열이 아니다. 즉, DBMS에서 빈 문자열은 유효한 값이므로 값이 없다는 의미는 아니다.

#### 기본값 지정하기

SQL에서 행을 삽입할 때 값이 없으면 자동으로 들어가는 기본값을 지정할 수 있다.

```sql
CREATE TABLE OrderItems 
(
    order_num  INTEGER      NOT NULL,
    order_item INTEGER      NOT NULL,
    prod_id    CHAR(10)     NOT NULL,
    quantity   INTEGER      NOT NULL DEFAULT 1,
    item_price DECIMAL(8,2) NOT NULL
);
```

날짜나 시간, 그리고 기본 값에 대한 설정을 할 수 있다.

#### 기본키

```sql
CREATE TABLE OrderItems 
(
    order_num  INTEGER      NOT NULL PRIMARY KEY,
    order_item INTEGER      NOT NULL,
    prod_id    CHAR(10)     NOT NULL,
    quantity   INTEGER      NOT NULL DEFAULT 1,
    item_price DECIMAL(8,2) NOT NULL
);
```

```sql
ALTER TABLE Vendors ADD CONSTRAINT PRIMARY KEY(order_num);
```

[http://tcpschool.com/mysql/mysql\_constraint\_primaryKey](http://tcpschool.com/mysql/mysql_constraint_primaryKey)

기본키 **\(PRIMARY KEY\)** 는 특별한 제약 조건으로서 열에 있는 값이 고유하면서 절대 변하지 않는다는 것을 보장하기 위해 사용한다.

즉, 테이블의 열로 테이블에 있는 행을 고유하게 구별할 수 있는 값을 가지게 하고 특정한 행을 매우 쉽게 조작할 수 있게 한다.

기본키가 없다면 다른 행에 영향을 주지 않고 특정 행만 안전하게 업데이트 하거나 삭제하는 것이 매우 힘들다.

**기본키의 조건**

* 기본키는 하나이다.
* 기본키는 NULL을 허용하지 않는다.
* 기본 키 값을 가진 열은 변경하거나 업데이트를 하면 안된다.
* 기본 키 값은 절대 다시 사용되어서는 안 된다.

#### 외래 키

```sql
CREATE TABLE Orders 
(
    order_num    INTEGER        NOT NULL PRIMARY KEY,
    order_date     DATETIME     NOT NULL DEFAULT now(),
    cust_id        CHAR(10)    NOT NULL REFERENCES Customers(cust_id)
);
```

```sql
ALTER TABLE Orders
ADD CONSTRAINT
FOREIGN KEY(cust_id) REFERENCES Customers(cust_id);
```

외래 키 **\(FOREGIN KEY\)** 는 테이블에 있는 열이면서 그 값이 다른 테이블의 기본 키 값 중에 꼭 존재해야하는 열이다.

외래 키는 참조 무결성을 보장하는 데 대단히 중요한 역할을 한다.

`참조 무결성이란? 기본 키와 참조 키 간의 관계가 항상 유지됨을 보장한다.`

이 역할로 외래 키는 실수로 데이터를 삭제하는 것을 막아준다.

외래키가 정의되면 DBMS는 다른 테이블과 연결된 행을 삭제할 수 없기 때문이다.

`하지만 계단식 삭제라는 기능을 사용하면 한 테이블에서 행이 삭제되면 관련된 행을 모두 삭제가 가능하다.`

#### 고유 키 무결성 제약 조건

열\(또는 열 집합\)에 있는 모든 데이터가 동일한 값을 가질 수 없음을 정의하는 제약 조건이다.

* 테이블은 여러 고유 키 무결성 제약 조건을 가질 수 있지만, 기본 키는 한 테이블에 하나만 정의 되어야 한다.
* 열은 NULL을 허용
* 변경 및 업데이트가 가능
* 열의 값을 재사용 할 수 있다.
* 외래 키로 정의되어 사용할 수 있다.

Unique, Constraint를 사용하면 된다.

#### 체크 무결성 제약 조건

```sql
CREATE TABLE OrderItems 
(
    order_num    INTEGER        NOT NULL,
    order_item    INTEGER        NOT    NULL,
    prod_id        CHAR(10)    NOT NULL,
    quantity    INTEGER        NOT    NULL CHECK (quantity > 0),
    item_price    MONEY        NOT NULL
);
```

```sql
ADD CONSTRAINT CHECK (gender LIKE '[MF]')
```

열에서 허용 가능한 데이터의 범위나 조건을 지정하기 위한 제약 조건

* 최솟값이나 최댓값 확인
* 범위 지정
* 특정 값만 사용

### 데이터 업데이트와 삭제

#### 테이블 변경하기

테이블 정의를 업데이트할 때 ALTER TABLE 문을 사용한다.

* 기본적으로 데이터가 있는 테이블은 변경해서는 안 된다.
* 모든 DBMS는 존재하는 테이블에 열을 추가하는 것은 허용하지만 추가하는 열의 데이터 형에 몇가지 제약을 둔다.
* 다수의 DBMS는 테이블에 있는 열을 제거하거나 변경하는 것을 허용하지 않는다.
* 대부분의 DBMS는 열 이름의 변경을 허용한다.
* 다수의 DBMS는 데이터가 있는 열을 변경하는 것을 제한하고, 데이터가 없는 열을 변경하는 것에는 제한 사항을 많이 두지 앟는다.

ALTER TABLE 문 뒤에 변경할 테이블 이름을 적은 후 변경할 사항을 나열한다.

```sql
ALTER TABLE Vendors
ADD vend_phone    CHAR(20);
```

```sql
ALTER TABLE Vendors 
DROP COLUMN vend_phone
```

#### 테이블 삭제

```sql
DROP TABLE CustCopy;
```

테이블 삭제는 Drop를 사용하면 된다. 하지만 삭제하지 못하도록 규칙을 설정하는게 좋다.

### 뷰 사용하기

```sql
CREATE VIEW VendorLocations AS
SELECT RTRIM(vend_name) || ' (' || RTRIM(vend_country) || ')' AS vend_title
FROM Vendors;
```

뷰는 가상 테이블이다.

뷰는 데이터를 가진 테이블과는 달리, 뷰는 사용될 때, 동적으로 데이터를 가져오는 쿼리들을 담고 있다.

#### 사용목적

* SQL 문을 재사용
* 복잡한 SQL 작업을 단순화
* 테이블의 일부만을 노출
* 데이터를 보호
* 데이터 형식을 변경하기 위해

#### 뷰의 여러 사용

* 복잡한 조인을 단순화하기 위한 뷰를 생성,
* 가져온 데이터의 형식을 변경하기 위해 뷰 사용
* 원하지 않는 데이터를 필터링하기 위해 뷰 사용
* 계산 필드와 함께 뷰 사용하기

#### 뷰 규칙과 제한

* 테이블처럼 뷰는 고유한 이름을 가져야 한다.
* 생성할 수 있는 뷰의 수에는 제한이 없다.
* 뷰를 생성하기 위해 보안권한을 가져야한다.
* 뷰는 뷰를 포함할 수 있다.
* 많은 DBMS에서 뷰 쿼리에서 ORDER BY 절의 사용을 금지한다.
* 일부 DBMS에서 가져오는 모든 열에 이름을 필히 부여해야 한다.
* 뷰는 인덱스, 트리거, 기본값을 사용할 수 없다.
* SQLite와 같은 일부 DBMS는 뷰를 읽기 전용 쿼리로 처리한다.
* 일부 DBMS에서는 삽입 또는 업데이트한 데이터가 뷰의 범위를 벗어나는 경우는 삽입과 업데이트를 불허하는 뷰를 만들 수 있다.
* 뷰는 데이터를 갖고 있지 않기 때문에 뷰를 사용할 때마다 쿼리가 실행된다.

### 인덱스

인덱스는 데이터를 논리적으로 정렬해 검색과 정렬 작업 시 속도를 높이는데 사용한다.

**인덱스의 원리**

1. 기본 키 데이터는 항상 정렬되어 있다.
2. 기본 키로 특정 데이터를 가져오는 것은 언제나 빠르면서 동시에 효율적인 작업이다.
3. 하지만 다른 열로 값을 찾는 것은 보통 기본 키로 찾는 것만큼 효율적이지 않다.
4. 하나 이상의 열을 인덱스로 정의할 수 있다.
5. 인덱스로 정의한 열은 DBMS가 내용을 정렬해서 저장해놓는다.
6. 인덱스를 정의하면, DBMS는 특정 데이터형으로만 데이터를 찾는다.

**인덱스 주의점**

* 인덱스는 검색 성능을 개선하지만, 데이터 삽입, 수정, 삭제 성능은 저하된다.
  * 삽입, 수정, 삭제 작업이 수행할 때마다 DBMS는 인덱스를 동적으로 업데이트해야한다.
* 인덱스 데이터는 저장 공간을 많이 차지한다.
* 모든 데이터가 인덱스에 적합한 것은 아니다.
* 인덱스는 데이터 필터링과 정렬에 사용된다.
  * 특정 순서로 데이터를 자주 정렬한다면, 그 데이터는 인덱싱 후보가 될 수 있다.
* 여러 열을 하나의 인덱스로 정의할 수 있다.

### 트랜잭션 처리 관리하기

#### 트랜잭션 처리 이해하기

트랜잭션 처리는 여러 개의 SQL 작업을 일괄적으로 실행하거나 아니면 아예 실행하지 않도록 하여 데이터베이스의 무결성을 보장하는 처리 방식이다.

데이터베이스가 부분적으로 작업을 수행하는 것을 막기 위해 여러 SQL 작업을 일괄적으로 처리하는 메커니즘을 가지고 있다.

트랜잭션 처리를 이요하면, 작업이 중간에 중단되지 않도록 할 수 있다.

트랜잭션 처리 시 에러가 발생하지 않으면, SQL 문 전체가 데이터베이스 테이블에 커밋된다.

에러가 발생하면, 롤백되어 데이터베이스가 안전한 상태로 복구된다.

**트랜잭션 키워드**

* 트랜잭션\(Transaction\) - 일괄 처리할 SQL 명령어들을 묶은 블록\(block\)
* 롤백\(Rollback\) - 변경된 작업 내용을 모두 취소하는 전략
* 커밋\(Commit\) - 변경된 작업 내용을 데이터 베이스에 저장
* 저장점\(Savepoint\) - 부분적으로 롤백하기 위한 임시 저장

#### 트랜잭션 통제하기

트랜잭션의 시작과 끝을 지정할 수 있다.

```sql
BEGIN TRANSATION
...
COMMIT TRANSACTION
```

이 명령어 또한 각 DBMS마다 다른 명령어를 사용해야 한다. MariaDB, MYSQL은 START TRANSACTION, Oracle은 SET TRANSACTION으로 시작한다.

**롤백 사용하기**

```sql
DELETE FROM Orders;
ROLLBACK;
```

**커밋 사용하기**

보통 SQL 문은 실행되면서 데이터베이스 테이블을 바로 변경한다.

이렇게 커밋이 자동으로 일어나는 것을 자동 커밋이라 부른다.

하지만 트랜잭션은 자동으로 커밋되지 않는다.

**저장점 사용하기**

ROLLBACK과 COMMIT 문은 단순히 전체 트랜잭션을 저장하거나 되돌리는 작업만 한다.

간단한 트랜잭션을 사용할 때는 문제가 없지만, 좀 더 복잡한 트랜잭션은 부분적인 커밋이나 롤백이 필요할 수도 있다.

이때 필요한 것이 바로 저장점이다.

**저장점이란?**

```sql
SAVEPOINT delete1;
```

```sql
ROLLBACK TRANSACTION delete1;
```

트랜잭션을 부분적으로 롤백하려면, 트랜잭션에서 전략상 중요한 위치들을 임시 지점으로 정해놓고, 롤백이 필요할 때 임시 지점 중 하나로 되돌리는 것.

### 저장 프로시저

#### 저장 프로시저 이해하기

SQL문은 한개 이상의 테이블에 대해 단일한 구문을 상요한다는 점에서 간단하다고 말할 수 있다. 하지만 모든 작업이 다 그렇게 쉬운 것은 아니고 다수의 구문을 사용해야 할 필요가 있다.

이때 필요한 것이 바로 저장 프로시저이고, 이 저장 프로시저는 나중에 사용하기 위해 만들어둔 하나 이상의 SQL 명령문 집합을 의마하는데, 일종의 배치 파일로 생각해도 된다. 실제로는 배치 파일 이사으이 기능을 제공하기도 한다.

#### 저장 프로시저를 사용하는 이유

* 여러 단계로 이루어진 과정을 사용하기 쉬운 하나의 단위로 캡슐화하여 복잡한 작업을 단순화 한다.
* 여러 단계를 반복해서 만들 필요가 없어서 데이터 일관성을 보장한다.
* 오류 방지에도 도움을 준다.
* 변경 관리를 단순화한다.
* 보안성을 높인다.
* 저장 프로시저는 컴파일된 형태로 저장하기 때문에, 명령을 처리하기 위해 DBMS가 해야 하는 일이 줄어들고, 그 결과 성능이 향상된다.
* SQL 언어 요소와 기능 중에 하나의 요청 안에서만 사용해야 하는 것이 있다.

결과적으로 단순성, 보안성, 성능을 준다고 한다.

#### 저장 프로시저 단점

* 저장 프로시저 문법은 DBMS마다 매우 달라서, 다른 DBMS로 이식할 수 있는 저장 프로시저를 작성하는 것은 거의 불가능하다.
* 저장 프로시저는 기본 SQL 문을 작성하는 것보다는 좀 더 복잡해서, 저장 프로시저를 작성하려면 고수준의 기술과 경험이 필요하다.

### 커서

커서는 DBMS 서버에 저장된 쿼리로서 SELECT 문은 아니지만, SELECT 문으로 가져온 결과 집합이다. 커서는 한 번 저장되면, 프로그램에서 필요할 때마다 데이터를 상하로 탐색하면서 검색 결과를 가져올 수 있다.

* 커서에 읽기 전용으로 표시하여, 데이터를 일을 수는 있지만 업데이트나 삭제는 못하게 하는 기능
* 방향과 위치를 제어할 수 있는 기능
* 특정한 열만 수정할 수 있게 표시하고, 그 외의 열은 수정하지 못하기하는 기능
* 커서를 특정한 요청이나 모든 요청에 접근할 수 있게 하는 범위 지정 기능
* DBMS에서 가져온 데이터를 복사하여, 커서가 연\(open\) 후 데이터를 사용하는 사이에 데이턱타 변경되지 않게 하는 기능

### 트리거

트리거는 특정한 데이터베이스 작업이 발생하면 자동으로 수행되는 특별한 저장 프로시저로서, 특정한 테이블에 INSERT, UPDATE, DELETE와 같은 작업이 일어나면 자동으로 실행되는 코드이다.

트리거의 일반적인 용도

* 데이터 일관성 보장
* 테이블의 변화를 감지하여 특정한 작업을 수행
* 추가적인 데이터 유효성 검사나 데이터 롤백 수행
* 다른 열들의 값을 기초로 어떠한 계산을 하건거나 타입스탬프를 갱신

### 데이터베이스 보안

대부분 DBMS는 관리자에게 데이터 접근 권한을 부여하거나 제재하는 메커니즘을 제공한다.

보안 시스템의 기본은 사용자 승인과 인증이다.

보안이 적용되어야할 작업은 일반적으로 다음과 같다.

* 테이블 생성, 변경, 삭제와 같은 데이터베이스 관리 기능에 대한 접근
* 특정 데이터베이스나 테이블에 대한 접근
* '읽기 전용', '특정 열에만 접근'과 같은 접근 유형
* 뷰나 저장 프로시저를 통해서만 접근할 수 있는 테이블 지정
* 로그인한 계정에 따라 접근과 제어 권한을 다양하게 부여하는 다단계 보안 레벨 생성
* 사용자 계정 관리 권한

보안은 SQL GRANT와 REVOKE 문으로 관리할 수 있는데, 대부분의 DBMS가 대화형 관리자 유틸리티를 제공한다.



## [https://nesoy.github.io/articles/2018-02/Database-Partitioning
)

## [https://cheese10yun.github.io/mysql-explian/](https://cheese10yun.github.io/mysql-explian/)


