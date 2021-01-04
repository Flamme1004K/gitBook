---
description: 이 글은 김영한님의 jpa책을 보고 공부한 흔적입니다.
---

# Chap4

## JPA Chap4 엔티티 매핑

`JPA를 사용하는데 가장 중요한 일은 엔티티와 테이블을 정확히 매핑하는 것이다.`

Jpa에서 엔티티 매핑은 크게 4가지로 분류 할 수 있다.

* 객체와 테이블 매핑 : @Entity, @Table
* 기본 키 매핑 : @Id
* 필드와 컬럼 매핑 : @Column
* 연관관계 매핑 : @ManyToOne, @JoinColumn

Chap4에서는 객체와 테이블 매핑, 기본키 매핑, 필드와 컬럼 매핑에 대해서 정리되어있다.

연관관계 매핑은 5,6,7장에 걸쳐서 정리되어 있음.

### @Entity

`JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 어노테이션을 필수로 붙여야 한다.`

* @Entity : JPA가 클래스를 관리하겠다고 하는 어노테이션
  * name : JPA에서 사용할 엔티티 이름을 지정 

      \(설정하지 않으면 클래스 이름 그대로 사용\)

  * 주의사항 
    * 기본 생성자 필수\(public, protected\)
    * final 클래스, enum, interface, inner 클래스 사용 불가 
    * 저장할 필드에 final 사용 불가

### @Table

`엔티티와 매핑할 테이블을 지정한다.`

* @Table : 엔티티와 매핑할 테이블을 지정
  * name : 매핑할 테이블 이름\(기본값 : 엔티티 이름 사용\)
  * catalog : catalog 기능이 있는 데이터베이스에서 catalog 매핑
  * schema : schema 기능이 있는 데이터베이스에서 schema를 매핑
  * uniqueConstraints : DDL 생성 시에 유니크 제약 조건 만듬.

### 데이터 스키마 자동 생성 -&gt; DDL 제약조건 추가 -&gt; 기본 키 매핑

일단 스키마, DDL에 대한 개념을 잡기 위해서 둘의 개념을 알아보도록 하자.

스키마 : Table의 집합. schema를 모은 것이 database Server

DDL\(Data Definition Language\) : 데이터 정의어. 테이블과 같은 데이터 구조를 정의하는데 사용되는 명령어들로 데이터 구조와 관련된 명령어들을 말함.\(CREATE, ALTER, DROP, RENAME, TRUNCATE\)

```java
<property name="hibernate.hbm2ddl.auto" value="create" />
// 애플리케이션 실행 시점에 데이터베이스 테이블을 자동 생성

<property name="hibernate.show_sql" value="true"/>
//테이블 생성 DDL을 출력
```

`책에서 말하는 스키마 자동생성은 테이블 자동생성을 뜻하는듯하다.`

```java
@Entity
@Table(name="MEMBER")
public class Member {
    @Id
    @Column(name =" Id")
    private String id;

    @Column(name = "NAME", nullable = false, length = 10) //추가
    private String username;
}
```

perperty 생성과 함께 @Entity클래스를 생성하면 어플리케이션 실행 시 테이블이 자동으로 만들어진다.

`이때 property를 이용하여 테이블의 생성 및 삭제 설정 및 camel표기법을 수정, 등의 기능을 사용할 수 있다.`

`@Table에 uniqueConstraints를 사용하여 유니크 제약 조건 또한 추가 할 수 있다.`

### @Id

`JPA에서는 기본키를 직접 할당할 수도 있고, MySQL에서 흔히 말하는 AI 기능을 통하여 생성된 값을 기본키로 할당할 수도 있다.`

@Id = primary Key

JPA에서의 기본 키 생성 전략

* 직접 할당 : 기본키를 애플리케이션에서 직접 할당
  * em.persist\(\)로 엔티티를 저장하기 전에 애플리케이션에서 기본 키를 직접 할당하는 방법
* 자동 생성 : 대리키 사용 방식

  * IDENTITY : 기본키 생성을 데이터베이스에 위임
    * MySQL, PostgreSQL, SQL Server, DB2 사용
    * @GenerateValue\(strategy = GenerationType.IDENTITY\) 사용
    * 데이터베이스에 엔티티를 저장해서 식별자 값을 획득한 후 영속성 컨텍스트에 저장 \( 데이터를 저장해야 식별자 값을 획득\)
  * SEQUENCE : 데이터베이스 시퀀스를 사용해서 기본 키를 할당
    * Oracle, PostgreSQL, DB2, H2 사용
    * @GenerateValue\(strategy = GenerationType.SEQUENCE, generator = "BOARD\_SEQ\_GENERATOR""\)
      * @SequenceGenerator
        * name : 식별자 생성기 이름\(필수\)
        * sequenceName : 데이터베이스에 등록되어 있는 시퀀스 이름
        * initialValue : DDL 생성 시에만 사용
        * allocationSize : 시퀀스 호출당 증가하는 수 시퀀스값을 DB에서 가지고옴 50채울때까지 시퀀스 값을 안가지고옴.
        * catalog. schema : 데이터베이스 catalog, schema 이름
    * 데이터베이스 시퀀스에서 식별자 값을 획득한 후 역속성 컨텍스트에 저장한다.
  * TABLE : 키 생성 테이블 사용
    * 모든 데이터베이스에 적용
    * @TableGenerator
      * name : 식별자 생성기 이름\(필수\)
      * table : 키생성 테이블 명 
      * pkColumnName : 시퀀스 컬럼명
      * pkColumnValue : 키로 사용할 값 이름\(엔티티 이름\)
      * valueColumnName : 시퀀스 값 컬럼명
      * initialValue : 초기 값, 마지막으로 생성된 값
      * allocationSize
      * catalog, schema
      * uniqueConstraints\(DDL\) : 유니크 제약 조건 지정
    * 값을 조회하면서 SELECT 쿼리를 사용하고 다음 값으로 증가시키기 위해 UPDATE 쿼리를 사용하기 떄문에 SEQUENCE 전략과 같다.
    * 데이터베이스 시퀀스 생성용 테이블에서 식별자 값을 획득 한 후 영속성 컨텍스트에 저장한다.
  * AUTO : 데이터베이스 방언에 따라 IDENTITY, SEQUENCE, TABLE 전략 중 하나를 자동 선택

  `자동 생성 전략이 다양한 이유는 데이터베이스 밴더 마다 지원하는 방식이 다르기 때문이다.`

  권장하는 식별자 선택 전략 1. null값은 허용하지 않는다. 2. 유일해야 한다. 3. 변해선 안된다.

### 필드와 컬럼 매핑: 레퍼런스

종류 : @Column, @Enumerated, @Temporal, @Lob, @Transient, @Access

JPA책 145Page 참고바람.

JAVA8부터는 LocalData가 있기 때문에 @Temporal 대신 쓴다.

