---
description: 이 글은 김영한님의 jpa책을 보고 공부한 흔적입니다.
---

# Chap3

## JPA Chap3 영속성 관리

JPA가 제공하는 기능은 크게 두가지로 나눌 수 있다.

* 엔티티와 테이블을 매핑하는 설계부분
*   엔티티를 실제 사용하는 부분

    `JPA는 엔티티를 사용하기 위해 엔티티 매니저를 사용한다. 엔티티 매니저는 엔티티를 저장하고, 수정하고, 삭제하고, 조회하는 등 엔티티와 관련된 모든 일을 처리한다.`

```markup
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" version="2.1">
    <persistence-unit name="jpabook">
        <properties>
            <!— 필수 속성 —>
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect" />
               ...
        </properties>
    </persistence-unit>
</persistence>
```

```java
EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");
```

하나의 데이터베이스는 하나의 엔티티 매니저 팩토리를 가진다.

* 엔티티 매니저 팩토리는 여러 스레드가 동시에 접근해도안전하므로 서로 다른 스레드 간에 공유해도 된다.

```java
EntityManager em = emf.createEntityManager();
```

엔티티 매니저 팩토리에서는 엔티티 매니저를 생성한다.

* 여러 스레드가 동시에 접근하면 동시성 문제가 발생하므로 스레드 간에 절대 공유하면 안된다.
* 트랜잭션을 시작할 때 데이터베이스을 연결하여 커넥션을 획득한다.

```java
Member member = em.find(Member.class, "member1")
member.setId("member1");
member.setUsername("회원1");

em.persist(member);
em.persist(member1);
```

엔티티 매니저는 영속성 컨텍스트에 엔티티를 보관하고 관리한다.

`영속성 컨텍스트란? 엔티티를 영구 저장하는 환경`

엔티티는 영속성 컨텍스트에서 4가지 상태로 존재한다.

* 비영속(new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 상태
* 영속(managed): 영속성 컨텍스트에 저장된 상태
* 준영속(detached): 영속성 컨텍스트에 저장되었다가 분리된 상태
* 삭제(removed): 삭제된 상태

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2\&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2FpnSeS%2FbtqDtACNUcY%2FH5PvtSlkLeD2gNaSKoPcY0%2Fimg.png) 엔티티의 생명주기

### 엔티티의 비영속 -> 영속 -> 삭제 상태 변화

\
&#x20;\
&#x20;영속성 컨텍스트는의 특징은 \
\


* 영속성 컨텍스트와 식별자 값 : 영속성 컨텍스트는 엔티티를 식별자 값으로 구분한다.
* 영속성 컨텍스트와 데이터베이스 저장 : 영속성 컨텍스트는 flush로 데이터베이스에 반영된다.
* 영속성 컨텍스트가 엔티티를 관리시 장점
  * 1차캐시
  * 동일성 보장
  * 트랜잭션을 지원하는 쓰기 지연
  * 변경 감지
  * 지연 로딩

```java
Member member = new Member();
member.setId("member1");
member.setUsername("회원1");

em.persist(member);
```

엔티티를 영속성 컨텍스트에 보관하면 엔티티 컨텍스트안에 있는 내부 캐시(1차 캐시)로 인하여 영속 상태의 엔티티가 모두 이곳에 저장 된다.

`아직 member는 데이터베이스에 저장되지 않았다.`

1차 캐시의 키는 식별자의 값이다. 식별자의 값은 데이터베이스 기본 키와 매핑되어 있다. 즉, 영속성 컨텍스트에 데이터를 저장하고 조회하는 모든 기준은 데이터베이스 기본 키 값이다.

```java
Member member = em.find(Member.class, "member1")

//member vo의 @id annotation이 붙어있는것이 primaryKey가 된다고 한다.

//EntityManager.find() 메소드 정의
public <T> T find(Class<T> entityClass, Object primaryKey)
```

여기서 member1은 처음 영속성 컨텍스트로 올라가 1차캐시로 저장된 엔티티이다.

만약에 member1이 영속성 컨텍스트로 올라가지 않았으면 find()메소드는 데이터베이스에서 값을 찾아 조회하고 1차 캐시로 저장한다.

1차 캐시로 항상 저장함으로써 엔티티를 조회할 때 1차 캐시로 불러옴으로써 성능상 이점을 누릴 수 있다.

Q. 1차 캐시를 장점으로 쓰려면 처음 조회할 때 해당 테이블의 데이터를 전부 불러와 캐시화 한다면 처음 사용할 때 느리겠지만 다음번 사용할 때는 성능이 올라가지 않을까요?

```java
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");

a == b; //true
```

Member1이 1차 캐시로 등록되어 있기 때문에 영속성 컨텍스트는 성능상 이점과 엔티티의 동일성을 보장한다.

```java
EntitiyManager em = emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();
//엔티티 매니저는 데이터 변경 시 트랜잭션을 시작해야 한다.
transaction.begin();

em.persist(memberA);
em.persist(memberB);
//여기까지 INSERT SQL을 데이터베이스에 보내지 않는다.

// 커밋하는 순간 데이터베이스에 INSERT SQL을 보낸다.
transaction.commit(); //트랜잭션 커밋
```

엔티티 매니저는 트랜잭션을 커밋하기 직전까지 데이터베이스에 엔티티를 저장하지 않고 내부 쿼리 저장소에 INSERT SQL을 차곡차곡 모아둔다.

트랜잭션을 커밋할 때 모아둔 쿼리를 데이터베이스에 보내서 등록한다.

`이때 memberA-> memberB 순으로 총 2건의 쿼리가 데이터베이스에 등록된다.`

![](https://k.kakaocdn.net/dn/bjRXgs/btqDtz4ZUdG/tCXQcERQ0mhDVQfXZg9dWk/img.png)

그림과 같이 commit()를 실행하면 엔티티 매니저는 우선 영속성 컨텍스트를 flush한다.

```
flush란? 

영속성 컨텍스트의 변경 내용을 데이터베이스에 동기화하는 작업. 등록, 수정, 삭제한 엔티티를 데이터베이스에 반영한다.``

1. 변경 감지가 동작해서 영속성 컨텍스트에 있는 모든 엔티티를 스냅샷과 비교해서 수정된 엔티티를 찾는다. 
  수정된 엔티티는 수정 쿼리를 만들어 쓰기지연 SQL 저장소에 등록한다.
2. 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 저장한다(등록, 수정, 삭제,쿼리)

flush 호출 방법
 - em.flush()로 직접호출 :
  테스트나 다른 프레임워크와 JPA를 함꼐 사용할 떄를 제외하고 거의 사용하지 않는다.

 - 트랜잭션 커밋시 자동 호출 :
  트랜잭션을 커밋하기 전에 플러시가 호출되어 데이터베이스에 반영

 - JPQL 쿼리 실행 시 자동 :
  JPQL 객체지향 쿼리를 호출할 때 영속성 컨텍스트로 선언되어있다면 선언된 영속성 컨텍스트도
  쿼리문에 포함되어 조회되어야 하므로 JPQL 호출할 때 flush를 호출한다.
```

이후 flush한 데이터를 데이터베이스에 보내 데이터베이스에 동기화한 후에 실제 데이터베이스 트랜잭션을 커밋한다.

`여기서 memberA, memberB가 commit()시에만 데이터베이스에 반영되기 때문에 쓰기 지연이 가능하다.`

```java
MYBATIS/IBATIS

UPDATE MEMBER
SET 
    NAME=?,
    AGE=?
WHERE 
    ID=?

memberDao.memberUpdate(member);

JPA
Member memberA= em.find(Member.class, "memberA");

memberA.setUsername("hi");
memberA.setAge(10);

transaction.commit();
```

MYBATIS/IBATIS 경우 SQL문을 작성하고 메소드를 호출하여 값을 넣어줘야 변경되지만.

JPA는 엔티티를 수정할 때 단순히 엔티티를 조회해서 데이터만 변경하면 된다.

이것을 바로 변경감지라고 말한다.

![](https://k.kakaocdn.net/dn/cmgrmB/btqDuqzXbxr/tD6TKyIxNe6KLMtcbNsuEk/img.png)

\
\
&#x20;JPA는 엔티티를 영속성 컨텍스트에 보관할 떄, 최초 상태를 복사해서 저장해두는데 이것을 스냅샷이라고 한다.

그리고 플러시 시점에 스냅샷과 엔티티를 비교해서 변경된 엔티티를 찾는다.

스냅샷과 엔티티를 비교하여 변경 감지가 일어나서 커밋시 내용이 변경되면 변경된 내용으로 데이터베이스에 반영된다.

`변경 감지는 영속성 컨텍스트가 관리하는 영속 상태의 엔티티에만 적용된다.`

변경감지로 인해서 업데이트 할때 JPA는 부분적으로 업데이트를 하는게 아니라 모든 필드를 업데이트 한다.

* 모든 필드를 사용하면 수정 쿼리가 항상 같다. 애플리케이션 로딩 시점에 수정 쿼리를 미리 생성해두고 재사용할 수 있다.
* 데이터베이스에 동일한 쿼리를 보내면 데이터베이스는 이전에 한 번 파싱된 쿼리를 재사용할 수 있다.

`필드가 많거나 저장되는 내용이 너무 크면 수정된 데이터만 사용해서 동적으로 UPDATE SQL을 생성하는 전략을 선택하면 된다. 이때 Hibernate의 확장 기능을 사용해야 한다.`

```java
Member memberA = em.find(Member.class,"memberA");
em.remove(memberA);
```

em.remover()에 삭제 대상 엔티티를 넘겨주면 엔티티를 삭제한다. 이때도 쓰기 지연 SQL 저장소에 등록하고 commit시 삭제 쿼리를 전달해 데이터베이스에서 해당 엔티티가 삭제 된다.

`이때 삭제된 엔티티는 재사용하지 말고 자연스럽게 가비지 컬렉션의 대상이 되도록 두는 것이 좋다.`

### 엔티티의 영속 -> 준영속

![](https://k.kakaocdn.net/dn/DQAXr/btqDqiJXBY9/kPoSzhBhGnEFDknLb1toHk/img.png)

위 그림과 같이 영속 상태의 엔티티가 영속성 컨텍스트에서 분리되는 것을 준영속 상태라 한다.

`쓰기지연에 해당하는 등록, 변경 감지에 대한 수정, 삭제도 모두 불가능하다.`

즉, 준영속 상태의 엔티티는 영속성 컨텍스트가 제공하는 기능을 사용할 수 없다.

엔티티의 준영속 상태로 만드는 방법은 3가지다.

* em.detach(entity) : 특정 엔티티만 중영속 상태로 전환
* em.clear() : 영속성 컨텍스트를 완전히 초기화
  * 해당 연속성 컨텍스트의 모든 엔티티를 준영속 상태로 만든다.
*   em.close() : 영속성 컨텍스트를 종료

    * 모든 쓰기지연 및 1차 캐시에 대한 정보들이 모두 준영속 상태가 된다.

    `em.clear와 em.close의 차이 : em.clear는 영속성 컨텍스트에 있는 모든 엔티티만 준영속 상태로 만들고 em.close는 영속성 컨텍스트에 있는 모든 것들 쓰기지연, 1차캐시, 엔티티가 다 준영속 상태로 변한다.`

    ```java
    Member mergeMember = em.merge(member);
    ```

    준영속 상태의 엔티티를 다시 영속 상태로 변경하려면 병합을 사용하면 된다.

    merge() 메소드는 준영속 상태의 엔티티를 받아서 그 정보로 새로운 영속 상태의 엔티티를 반환한다.

    ![](https://k.kakaocdn.net/dn/bcNJwX/btqDqiXvINx/auMJYVAfN3l7WkkvC0oBGk/img.png)

준영속성 병합이 일어날 때 사진과 같이 다시 해당 엔티티의 값을 1차캐시 -> DB조회 순으로 조회한다. 그 후 해당 엔티티는 member와 동일성을 가지는게아니라 mergeMember와 동일성을 가진다.

그 이유는 준영속 상태인 member 엔티티와 영속 상태인 mergeMember 엔티티가 서로 다른 인스턴스이기 때문이다.

```java
Member member = new Member();
Member newMember = em.merge(member); //비영속 병합
tx.commit();
```

`병합은 비영속 엔티티도 영속 상태로 만들 수 있다.`

병합은 파라미터로 넘어온 엔티티의 식별자 값으로 연속성 컨텍스트를 조회하고 찾는 엔티티가 없으면 데이터베이스에서 조회한다. 만약 데이터베이스에서도 발견하지 못하면 새로운 엔티티를 생성해서 병합한다.

즉 병합은 저장과 업데이트 기능을 수행한다.

3챕터를 짧게 정리하자면

엔티티 매니저 팩토리는 앤티티 매니저를 만들고 엔티티 매니저는 영속성 컨텍스트를 이용하여 엔티티를 관리한다. 그리고 엔티티 컨텍스트는 엔티티들을 수정하고 저장하고 삭제할 수 있으므로 3가지 행위를 할때는 commit()메소드로 플러시를 불러와 동기화하고 마지막으로 데이터베이스는 커밋을 이용하여 반영한다.

이다.
