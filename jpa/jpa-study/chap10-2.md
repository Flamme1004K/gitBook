# Chap10-2

### JPQL

QueryDSL, 네이티브 SQL, JDBC, Creteria등을 사용하는 방법의 기초는 모두 JPQL\(Java Persistence Query Language\)로 부터 시작한다.

* JPQL은 객체지향 쿼리 언어다. 따라서 테이블을 대상으로 쿼리하는 것이 아니라 엔티티 객체를 대상으로 쿼리한다.
* JPQL은 SQL을 추상화해서 특정 데이터베이스 SQL을 의존하지 않는다.
* JPQL은 결국 SQL로 변환된다.

  JPQL도 SQL과 비슷하게 SELECT, UPDATE, DELETE문을 사용할 수 있다. EntityManager.persist\(\) 메소드를 사용하면 되므로 INSERT 문은 없다.

**SELECT 문**

```java
SELECT m FROM Member AS m WHERE m.username = 'Hello'
```

대소문자 구분

* 엔티티와 속성은 대소문자를 구분한다.

  * Member, username은 대소문자를 구분한다.
  * SELECT, FROM, AS 같은 JPQL 키워드는 대소문자를 구분하지 않는다.

  엔티티 이름

* JPQL에 사용한 Member는 클래스 명이 아니라 엔티티 명이다.

  * @Entity\(name="Member"\)로 지정할 수 있다.
  * 지정하지 않으면 클래스 명을 기본값으로 사용한다.
  * 기본값이 클래스 명을 엔티티 명으로 사용하는 것을 추천한다.

  별칭은 필수

* Member AS m 처럼 m이라는 별칭을 주어야 한다.



**TypeQuery, Query**

* 작성한 JPQL을 실행하려면 쿼리 객체를 만들어야 한다.
* 쿼리 객체는 TypeQuery와 Query가 있는데 반환할 타입을 명확하게 지정할 수 있으면 TypeQuery 객체를 사용한다.
* 반환 타입을 명확하게 지정할 수 없으면 Query 객체를 사용한다.

  ```java
  //TypeQuery 사용

  TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class );

  List<Member> resultList = query.getResultList();
  for (Member member : resultList) {
   System.out.println("member = " + member);
  }
  ```

  ```java
  //Query 사용

  Query query = em.createQuery("SELECT m.username, m.age FROM Member m", Member.class );

  List resultList = query.getResultList();
  for (Object o : resultList) {
   System.out.println("username = " + result[0]);
   System.out.println("age = " + result[1]);
  }
  ```

  **결과 조회**

실제 쿼리를 실행해서 데이터베이스를 조회한다.

* query.getResultList\(\) : 결과를 예제로 반환한다. 만약 결과가 없으면 빈 컬렉션을 반환한다.
* query.getStringResult\(\) : 결과가 정확히 하나일 때 사용한다.
  * 결과가 없으면 javax.persistence.NoResultException 예외가 발생한다.
  * 결과과 1개보다 많으면 javax.persistence.NonUniqueResultException 예외가 발한다.

#### 파라미터 바인딩

JDBC는 위치 기준 파라미터 바인딩만 지원하지만 JPQL은 이름 기준 파라미터 바인딩도 지원한다.

**이름 기준 파라미터**

* 이름 기준 파라미터는 파라미터를 이름으로 구분하는 방법이다.

  `이름 기준 파라미터는 앞에 :를 사용한다.`

  \`\`\`java String usernameParam = "User1";

  TypedQuery query = em.createQuery\("SELECT m FROM Member m where m.username = :username", Member.class\)

  query.setParameter\("username", usernameParam\);

  List resultList = query.getResultList\(\);

```text
 **위치 기준 파라미터**

 ``위치 기준 파라미터를 사용하려면 ? 다음에 위치 값을 주면 된다. 위치 값은 1부터 시작한다.``

 ```java
    List<Member> members = em.createQuery("SELECT m FROM Member m where m.username = ?1", Member.class)
    .setParameter(1, usernameParam)
    .getResultList();
```

**위치 기준 파라미터방식보다는 이름 기준 파라미터 바인딩 방식을 사용하는 것이 더 명확하다.**

#### 프로젝션

SELCT 절에 조회할 대상을 지정하는 것을 프로젝션\(projection\)이라 한다.

* \[SELECT {프로젝션 대상} FROM\]으로 대상을 선택한다.
* 엔티티, 엠비디드 타입, 스칼라 타입이 있다.
* 스칼라 타입은 숫자, 문자 등 기본 데이터 타입을 뜻한다.

  **엔티티 프로젝션**

  ```java
  SELECT m FROM Member m //회원

  SELECT m.team FROM Member m // 팀
  ```

처음은 회원을 조회했고 두 번째는 회원과 연관되 팀을 조회했는데 둘 다 엔티티를 프로젝션 대상으로 사용했다.

원하는 객체를 바로 조회한 것인데 컬럼을 하나하나 나열해서 조회하는 SQL과는 차이가 있다.

조회한 엔티티는 연속성 컨텍스에서 관리된다.

**임베디드 타입 프로젝션**

JPQL에서 임베디드 타입은 엔티티와 거의 비슷하게 사용된다.

임베디드 타입은 조회의 시작점이 될 수 없다는 제약이 있다.

```java
    String query = "SELECT o.address FROM Order o";
    List<Address> addresses = em.createQuery(query, Address.class).getResultList();
```

임베디드 타입은 엔티티 타입이 아닌 값 타입이다. 따라서 이렇게 직접 조회한 임베디드 타입은 영속성 컨텍스트에서 관리되지 않는다.

**스칼라 타입 프로젝션**

숫자, 문자, 날짜와 같은 기본 데이터 타입들을 스칼라 타입이라 한다.

```java
List<String> usernames = em.createQuery("SELECT username FROM Member m". String.class).getResultList();
//SELECT DISTINCT username FROM Member m

Double orderAmoutAvg = em.createQuery("SELECT AVG(o.orderAmount) FROM Order o", Double.class).getSingleResult();
```

**여러 값 조회**

엔티티를 대상으로 조회하면 편리하겠지만, 꼭 필요한 엔티티들만 선택해서 조회해야 할 떄도 있다.

프로젝션에 여러 값을 선택하면 TypeQuery를 사용할 수 없고, 대신에 Query를 사용해야 한다.

```java
 Query query = em.createQuery("SELECT m.username, m.age FROM Member m");

 List resultList = query.getResultList();

 Iterator iterator = resultList.iterator();
 while (iterator.hasNext()) {
    Object[] row = (Object[]) iterator.next();
    String username = (String) row[0]; 
    Integer age = (Integer) row[1];
}
```

제네릭 Object\[\] 사용하여 코드를 더 간결하게 만들 수 있다.

```java
    List<Object[]> resultList = em.createQuery("SELECT m.username, m.age FROM Member m").getResultList();

    for (Object[] row : resultList) {
        String username = (String) row[0];
        Integer age = (Integer) row[1];
    }
```

스칼라 타입뿐만 아니라 엔티티 타입도 여러 값을 함꼐 조회할 수 있다.

```java
    List<Object[]> resultList = em.createQuery("SELECT o.member, o.product, o.orderAmount FROM Order o").getResultList();

    for(Object[] row : resultList) {
        Member member = (Member) row[0];    //엔티티
        Product product = (Product) row[1]; //엔티티
        int orderAmount = (Integer) row[2]; //스칼라
    }
```

**NEW 명령어**

NEW 명령어 사용 전

```java
     List<Object[]> resultList = em.createQuery("SELECT o.member, o.product, o.orderAmount FROM Order o").getResultList();

    //객체 변환작업
     List<UserDTO> userDTOs = new ArrayList<UserDTO>();

     for(Object[] row : resultList) {
        UserDTO userDTO = new UserDTO((String) row[0], (Integer)row[1]);
        userDTOs.add(userDTO);
     }

     return userDTOs;

     public class UserDTO {

        private String username;
        private int age;

        public UserDTO(String username, int age) {
            this.username = username;
            this.age = age;
        }           
    }
```

NEW 명령어 사용 후

```java
    TypedQuery<UserDTO> query = em.createQuery("SELECT new jpabook.jpql.UserDTO(m.username, m.age) FROM Member m", UserDTO.class);

    List<UserDTO> resultList = query.getResultList();
```

SELECT 다음에 NEW 명령어를 사용하면 반환받을 클래스를 지정할 수 있는데 이 클래스의 생성자에 JPQL 조회 결과를 넘겨줄 수 있다.

NEW 명령어를 사용한 클래스로 TypeQuery 사용할 수 있어서 지루한 객체 변환작업을 줄일 수 있다.

주의할 점

* 패키지 명을 포함한 전체 클래스 명을 입력해야한다.
* 순서와 타입이 일치하는 생성자가 필요하다.

  **페이징 API**

  페이징 처리용 SQL을 작성하는 일은 지루하고 반복적이다.

  데이터베이스마다 페이징을 처리하는 SQL 문법이 다르다는 점이다.

  JPA 페이징은 두 API로 추상화 했다.

* setFirestREsult\(int startPosition\) : 조회 시작 위치\(0부터 시작한다.\)
* setMaxResults \(int maxResult\) : 조회할 데이터 수

  ```java
   TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m ORDER BY m.username DESC", Member.class);

   query.setFirstResult(10);
   query.setMaxResults(20);
   query.getResultList();
  ```

  \*\*\*\*

**집합과 정렬**

집합은 집합함수와 함께 통계 정보를 구할 때 사용한다.

* COUNT : 결과 수를 구한다. 반환 타입 : Long
* MAX, MIN : 최대, 최소 값을 구한다. 문자, 숫자, 날짜 등에 사용한다.
* AVG : 평균값을 구한다. 숫자타입만 사용할 수 있다. 반환 타입 : Double
* SUM : 합을 구한다. 숫자타입만 사용할 수 있다. 
  * 반환타입 : 정수합 Long, 소수합 Double, BigInteger합 : BigInteger, BigDecimal합 : BigDecimal

집합 함수 사용 시 참고사항

* NULL 값은 무시하므로 통계에 잡히지 않는다. \(DISTINCT가 정의되어 있어도 무시된다.\)
* 값이 없는데 SUM, AVG, MAX, MIN 함수를 사용하면 NULL 값이 된다. COUNT는 0이 된다.
* DISTINCT를 집합 함수 안에 사용해서 중복된 값을 제거하고 나서 집합을 구할 수 있다.
  * select COUNT\(DISTINCT m.age\) from Member m
* DISTINCT를 COUNT에서 사용할 때 임베디드 타입은 지원하지 않는다.

\*\*\*\*

**GROUP BY, HAVING**

GROUP BY는 통계 데이터를 구할 떄 특정 그룹끼리 묶어준다.

HAVING 은 GROUP BY와 함꼐 사용하는데 GROUP BY로 그룹화한 통계 데이터 기준으로 필터링 한다.

`통계 쿼리에서 결과가 아주 많다면 통계 결과만 저장하는 테이블을 별도로 만들어 두고 사용자가 적은 새벽에 통계 쿼리를 실행해서 그 결과를 보관하는 것이 좋다.`

\*\*\*\*

**정렬\(ORDER BY\)**

ORDER BY는 결과를 정렬할 떄 사용한다.

* ASC : 오름차순\(기본값\)
* DESC : 내림차순

**JPQL 조인**

JPQL도 조인을 지원하는데 SQL 조인과 기능은 같고 문법만 약간 다르다.

**내부조인**

내부 조인은 INNER JOIN을 사용한다. 참고로 INNER는 생략 할 수 있다.

```java
 String teamName = "팀A";
 String query = "SELECT m FROM Member m INNER JOIN m.team t WHERE t.name = :teamName";

 List<Member> members = em.createQuery(query, Member.class).setParameter("teamName", teamName).getResultList();
```

JPQL 조인의 가장 큰 특징은 연관 필드를 사용한다는 것이다.

여기서 m.team이 연관 필드인데 연관필드는 다른 엔티티와 연관관계를 가지기 위해 사용하는 필드를 말한다.

* FROM Member m : 회원을 선택하고 m이라는 별칭을 주었다.
* Member m JOIN m.team t : 회원이 가지고 있는 연관 필드로 팀과 조인한다. 조인한 팀에는 t라는 별칭을 주었다.

`JPQL 조인을 SQL 조인처럼 사용하면 문법 오류가 발생한다.`

```java
    FROM Member m JOIN Team t // 조인오류
```

만약 조인한 두 개의 엔티티를 조회하려면 다음과 같이 작성하면 된다.

```java
    SELECT m, t FROM Member m JOIN m.team t
```

서로 다른 타입의 두 엔티티를 조회했으므로 TypeQuery를 사용할 수 없다.

따라서 다음처럼 조회해야 한다.

```java
    List<Object[]> result = em.createQuery(query).getResultList();

    for(Object[] row : result) {
        Member member = (Member) row[0];
        Team team = (Team) row[1];
    }
```

**외부 조인**

```java
SELECT m FROM Member m LEFT [OUTER] JOIN m.team t
```

외부 조인 가능상 SQL의 외부 조인과 같다.

OUTER는 생략 가능해서 보통 LEFT JOIN으로 사용한다.

**컬렉션 조인**

일대다 관계나 다대다 관계처럼 컬렉션을 사용하는 곳에 조인하는 것을 컬렉션 조인이라 한다.

* \[회원 -&gt; 팀\]으로의 조인은 다대일 조인이면서 단일 값 연관 필드\(m.team\)를 사용한다.
* \[팀 -&gt; 회원\]은 반대로 일대다 조인이면서 컬렉션 값 연관 필드\(m.members\)를 사용한다.

  ```java
  SELECT t, m FROM TEAM t LEFT JOIN t.members m
  ```

t LEFT JOIN t.members는 팀과 팀이 보유한 회원목록을 컬렉션 값 연관 필드로 외부 조인 했다.

**세타 조인**

WHERE 절을 사용해서 세타 조인을 할 수 있다.

참고로 세타 조인은 내부 조인만 지원한다.

```java
    //JPQL
    select count(m) from Member m, Team t
    where m.username = t.name

    //SQL
    SELECT COUNT(M.ID)
    FROM
        MEMBER M CROSS JOIN TEAM T
    WHERE 
        M.USERNAME = T.NAME
```

**JOIN ON 절**

```java
    //JPQL
    select m,t from Member m
    left join m.team t on t.name = 'A'

    //SQL
    SELECT m.*, t.* FROM Member m
    LEFT JOIN Team t ON m.TEAM_ID = t.id and t.name = 'A'
```

SQL 결과를 보면 and t.name='A'로 조인 시점에 조인 대상을 필터링한다.

#### 페치 조인

페치 조인은 SQL에서 이야기하는 조인의 종류는 아니고 JPQL에서 성능 최적화를 위해 제공하는 기능이다.

연관된 엔티티나 컬렉션을 한 번에 같이 조회하는 기능인데 join fetch 명령어로 사용할 수 있다.

JPA 표준 명세에 정의된 페치 조인 문법은 다음과 같다.

* 페치 조인 ::= \[ LEFT \[OUTER\] \| INNER \] JOIn FETCH 조인경로

  **엔티티 페치 조인**

  ```java
   select m from Member m join fetch m.team
  ```

  연관된 엔티티나 컬렉션을 함께 조회를 회원\(m\)과 팀\(m.team\)을 함께 조회한다.

  페치 조인은 별칭을 사용할 수 없다.

  회원과 팀 객체가 객체 그래프를 유지하면서 조회 된다.

  ```java
   String jpql = "select m from Member m join fetch m.team";

   List<Member> members = em.createQuery(jpql, Member.class).getResultList();

   for (Member member : members) {
       //페치 조인으로 회원과 팀을 함께 조회해서 지연 로딩 발생 안 함
       System.out.println("username = " + member.getUsername() + ", teamname = " + member.getTeam().name() );
   }
  ```

회원을 조회할 때 페치 조인을 사용해서 팀도 함께 조회했으므로 연관된 팀 엔티티는 프로깃가 아닌 실제 엔티티다.

따라서 연관된 팀을 사용해도 지연 로딩이 일어나지 않는다.

프록시가 아닌 실제 엔티티이므로 회원 엔티티가 영속성 컨텍스트에서 분리되어 준영속성 사태가 되어도 연관된 팀을 조회할 수 있다.

**컬렉션 페치 조인**

```java
    select t from Team t join fetch t.members
    where t.name = '팀A'
```

**페치 조인과 DISTINCT**

JPQL의 DISTINCT 명령어는 SQL에 DISTINCT를 추가하는 것은 물론이고 애플리케이션에서 한번 더 중복을 제거한다.

```java
    select distinct t
    from Team t join fetch t.members
    where t.name = '팀A'
```

**페치 조인과 일반 조인의 차이**

```java
    //내부 조인 JPQL
    select t
    from Team t join t.members m 
    where t.name = '팀A'

    //실행된 SQL
    SELECT 
        T.*
    FROM TEAM T
    INNER JOIN MEMBER M ON T.ID=M.TEAM_ID
    WHERE T.NAME = '팀A'
```

JPQL은 결과를 반환할 때 연관관계까지 고려하지 않는다. 단지 SELECT 절에 지정한 엔티티만 조회할 뿐이다.

지연 로딩으로 설정하면 프록시나 아직 초기화하지 않은 컬렉션 래퍼를 반환한다.

즉시 로딩으로 설정하면 회원 컬렉션을 즉시 로딩하기 위해 쿼리를 한 번 더 실행한다.

반면에 페치 조인을 사용하면 연관된 엔티티도 함께 조회한다.

```java
    //컬렉션 패치 조인 JPQL
    select t 
    from Team t join fetch t.members
    where t.name = '팀A'

    //실행된 SQL
    SELECT
        T.*, M.*
    FROM TEAM T
    INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
    WHERE T.NAME = '팀A'
```

**페치 조인의 특징과 한계**

페치 조인의 특징

* 페치 조인을 사용하면 SQL 한 번으로 연관된 엔티티들을 함께 조회할 수 있어서 SQL 호출 횟수를 줄여 성능을 최적화할 수 있다.
* 엔티티에 직접 적용하는 로딩 전략\(LAZY, EAGER\)은 애플리케이션 전체에 영향을 미치므로 글로벌 로딩 전략이라 부른다.
* 페치 조인은 글로벌 로딩 전략보다 우선한다.
* 글로벌 로딩 전략을 지연 로딩으로 설정해도 JPQL에서 페치 조인을 사용하면 페치 조인을 적용해서 함께 조회한다.
* 즉시 로딩을 실행하면 어플리케이션 전체에서 항상 즉시 로딩이 일어난다.
* 일부는 빠를 수는 있지만 전체로 보면 사용하지 않는 엔티티를 자주 로딩하므로 오히려 성능에 악영향을 미칠 수 있다.
* 따라서 글로벌 로딩 전략을 될 수 있으면 지연 로딩을 사용하고 최적화가 필요한 페치 조인을 적용하는 것이 효과 적이다.
* 페치 조인을 사용하면 연관된 엔티티를 쿼리 시점에 조회하므로 지연 로딩이 발생하지 않는다.
* 즉 준영속 상태에서도 객체 그래프 탐색할 수 있다.

페치 조인의 한계

* 페치 조인 대상에는 별칭을 줄 수 없다.
* 둘 이상의 컬렉션을 페치할 수 없다.
* 컬렉션을 페치 조인하면 페이징 API\(setFirstResult, setMaxResults\)를 사용할 수 없다.
  * 컬렉션\(일대다\)이 아닌 단일 값 연관 필드\(일대일, 다대일\)들은 페치 조인을 사용해도 페이징 API를 사용할 수 있다.
  * 하이버네이트에서 컬렉션을 페치 조인하고 페이징 API를 사용하면 경고 로그를 남기면서 메모리에서 페이징 처리를 한다.
    * 데이터가 적으면 상관없겠지만 데이터가 많으면 성능 이슈와 메모리 초과 예외가 발생할 수 있어서 위험하다.

페치 조인은 객체 그래프를 유지할 때 사용하면 효과적이다.

여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야 한다면 억지로 페치 조인을 사용하기보다는 여러 테이블에서 필요한 필드들만 조회해서 DTO로 반환하는 것이 더 효과적일 수 있다.

#### 경로 표현식

경로표현식은 .\(점\)을 찍어 객체 그래프를 탐색는 것이다.

```java
    select m.username
    from Member m 
        join m.team t
        join m.orders o
    where t.name = '팀A'
```

m.username, m.team, m.orders, t.name이 모두 경로 표현식을 사용한 예다.

**경로 표현식의 용어 정리**

* 상태 필드\(state field\) : 단순히 값을 저장하기 위한 필드\(필드 or 프로퍼티\)
* 연관 필드\(association field\) : 연관관계를 위한 필드, 임베디드 타입 포함\(필드 or 프로퍼티\)
  * 단일 값 연관 필드 : @ManyToOne, @OneToOne, 대상이 엔티티
  * 컬렉션 값 연관 필드 : @OneToMany, @ManyToMany, 대상이 컬렉션

```java
    @Entity
    public class Member { 

        @Id @GeneratedValue
        private Long id;

        @Column(name = "name")
        private String username; // 상태 필드
        private Integer age; // 상태 필드

        @ManyToOne(..)
        private Team team; // 연관 필드(단일 값 연관 필드)

        @OneToMany(..)
        private List<Order> orders; //연관 필드(컬렉션 값 연관 필드)
    }
```

* 상태 필드 : t.username, t.age
* 단일 값 연관 필드 : m.team
* 컬렉션 값 연관 필드 : m.orders

  **경로 표현식과 특징**

  JPQL에서 경로 표현식을 사용해서 경로 탐색을 하려면 다음 3가지 경로에 따라 어떤 특징이 있는지 이해해야 한다.

  **상태 필드 경로**

  경로 탐색의 끝이다. 더는 탐색할 수 없다.

  * 다음 JPQL의 m.username, m.age는 상태 필드 경로 탐색이다.
  * select m.username, m.age from Member m

  **단일 값 연관 경로**

  묵시적으로 내부 조인이 일어난다.

  단일 값 연관 경로는 계속 탐색할 수 있다.

  select o.member from Order o

  select m.\* from Orders o inner join Member m on o.member\_id = m.id

  * 단일 값 연관 필드로 경로 탐색을 하면 SQL에서 내부 조인이 일어나는데 이것을 묵시적 조인이라 한다.
  * 묵시적 조인은 모두 내부 조인이다.
  * 명시적 조인 : JOIN을 직접 적어주는 것 ex\) SELECT m FROM Member m JOIN m.team t
  * 묵시적 조인 : 경로 표현식에 의해 묵시적으로 조인이 일어나는 것, 내부 조인\(INNER JOIN\)만 할 수 있다. ex\) SELECT m.team FROM Member m

  \`\`\`java //JPQL select o.member.team

  from Order o

  where o.product.name = 'productA' and o.address.city = 'JINJU'

```text
//실행된 SQL
select t.*
from Order o
inner join Member m on o.member_id = m.id
inner join Team t on m.team_id = t.id
inner join Product p on o.product_id = p.id
where p.name = 'productA' and o.city='JINJU'
```

```text
   **컬렉션 값 연관 경로**

   묵시적으로 내부 조인이 일어난다. 더는 탐색 할 수 없다. 단 FROM 절에서 조인을 통해 별칭을 얻으면 별칭으로 탐색할 수 있다.

   JPQL을 다루면서 많이 하는 실수 중 하나는 컬렉션 값에서 경로 탐색을 시도하는 것이다.

   ```java

    select t.members from Team t //성공
    select t.members.username from Team t //실패
```

t.members처럼 컬렉션까지는 경로 탐색이 가능하다.

t.members.username처럼 컬렉션에서 경로 탐색을 시작하는 것은 허락하지 않는다.

만약 컬렉션에서 경로 탐색을 하고 싶으면 다음 코드처럼 조인을 사용해서 새로운 별칭을 획득해야 한다.

```java
    select m.username from Team t join t.members m

    // join t.members m으로 컬렉션에 새로운 별칭을 얻었다.
    // 이제 별칭 m부터 다시 경로 탐색을 할 수 있다.
```

참고로 컬렉션은 컬렉션의 크기를 구할 수 있는 size라는 특별한 기능을 사용할 수 있다.

size를 사용하면 COUNT 함수를 사용하는 SQL로 적절히 변환된다.

```java
    select t.members.size from Team t
```

**경로 탐색을 사용한 묵시적 조인 시 주의사항**

경로 탐색을 사용하면 묵시적 조인이 발생해서 SQL에서 내부 조인이 일어날 수 있다.

* 항상 내부 조인이다.
* 컬렉션은 경로 탐색의 끝이다. 컬렉션에서 경로 탐색을 하려면 명시적으로 조인해서 별칭을 얻어야 한다.
* 경로 탐색은 주로 SELECT, WHERE 절에서 사용하지만 묵시적 조인으로 인해 SQL의 FROM 절에 영향을 준다.

  성능이 중요하면 분석하기 쉽도록 묵시적 조인보다는 명시적 조인을 사용하자.

#### 서브 쿼리

JPQL도 SQL처럼 서브쿼리를 지원한다.

서브쿼리는 WHERE, HAVING 절에서만 사용할 수 있고, SELECT, FROM 절에서는 사용할 수 없다.

```java
    //JQPL

    select m from Member m
    where m.age > (select avg(m2.age) from Member m2)
    //나이가 평균보다 많은 회원을 찾는다.

    select m from Member m
    where (select count(0) from Order o where m = o.member) > 0
    //다음 한 건이라도 주문한 고객을 찾는다.

    //JPQL Size 기능

    select m from Member m where m.orders.size > 0
```

**서브 쿼리 함수**

* \[NOT\] EXISTS \(subquery\)
  * 서브쿼리에 결과가 존재하면 참이다. NOT은 반대
* \[ALL \| ANY \| SOME\] \(sebquery\)
  * 비교 연산자와 같이 사용한다. { = \| &gt; \| &gt;= \| &lt; ..}
  * ALL 조건을 모두 만족하면 참이다.
  * ANY 혹은 SOME : 둘은 같은 의미다. 조건을 하나라도 만족하면 참이다.
* \[NOT\] IN \(subquery\)

  * 서브쿼리의 결과 중 하나라도 같은 것이 있으면 참이다.
  * 참고로 IN은 서브쿼리가 아닌 곳에서도 사용한다.

  **조건식**

  **타입 표현**

  문자

* 작은 따옴표 사이에 표현
* 작은 따옴표를 표현하고 싶으면 작은 따옴펴 연속 두 개\(''\)사용
* 'She''s'

  숫자

* L, D, F

  날짜

* DATA {d 'yyy-mm-dd'}
* TIME {t 'hh-mm-ss'}
* DATETIME{ts 'yyy-mm-dd hh:mm:ss.f'}

  Boolean

* TRUE, FALSE

  Enum

* 패키지명을 포함한 전체 이름을 사용해야 한다.
* jpabook.MemberType.Admin

  엔티티 타입

* 엔티티의 타입을 표현한다. 주로 상속과 관련해서 사용한다.
* TYPE\(m\) = Member

**Between, IN, Like, NULL 비교**

**컬렉션 식**

컬렉션 식은 컬렉션에만 사용하는 특별한 기능이다.

빈 컬렉션 비교 식

* 문법 : {컬렉션 값 연관경로 } Is \[NOT\] EMPTY
* 설명 : 컬렉션에 값이 비었으면 참

  ```java
  //JPQL : 주문이 하나라도 있는 회원 조회
  select m from Member m
  where m.orders is not empty

  //실행된 SQL
  select m.* from Member m
  where 
     exists {
         select o.id
         from Orders o
         where m.id= o.member_id
     }
  ```

  **컬렉션의 멤버 식**

* 문법 : {엔티티나 값} \[NOT\] MEMBER \[OF\] {컬렉션 값 연관 경로}
* 설명 : 에티티나 값이 컬렉션에 포함되어 있으면 참

  ```java
  select t from Team t
  where :memberParam member of t.members
  ```

  **스칼라 식**

  스칼라는 숫자, 문자, 날짜, case, 엔티티 타입 같은 가장 기본적인 타입들을 말한다.

* 수학 식
* 문자 함수
* 수학 함수
* 날짜 함수



**CASE 식**

특정 조건에 따라 분기할 때 CASE 식을 사용한다.

* 기본 CASE
* 심플 CASE
* COALESCE
* NULLIF

  \*\*\*\*

**다형성 쿼리**

JPQL는 부모 엔티티를 조회하면 그 자식 엔티티도 함께 조회한다.

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscrimiantorColumn(name = "DTYPE")
public abstract class Item {...}

@Entity
@DiscriminatorValue("B")
public class Book extends Item { 
   private String author;
}

//Album, Movie 생략

List resultList = em.createQuery("select i from Item i").getResultList();
```

\*\*\*\*

**TYPE**

TYPE은 엔티티의 상속 구조에서 조회 대상을 특정 자식타입으로 한정할 떄 주로 사용한다.

\*\*\*\*

**TREAT\(JPA2.1\)**

자바의 타입 캐스팅과 비슷하다.

상속구조에서 부모 타입을 특정 자식 타입으로 다룰 때 사용한다.

**사용자 정의 함수** 

**JPQL**

QueryDSL, 네이티브 SQL, JDBC, Creteria등을 사용하는 방법의 기초는 모두 JPQL\(Java Persistence Query Language\)로 부터 시작한다.

* JPQL은 객체지향 쿼리 언어다. 따라서 테이블을 대상으로 쿼리하는 것이 아니라 엔티티 객체를 대상으로 쿼리한다.
* JPQL은 SQL을 추상화해서 특정 데이터베이스 SQL을 의존하지 않는다.
* JPQL은 결국 SQL로 변환된다.

  JPQL도 SQL과 비슷하게 SELECT, UPDATE, DELETE문을 사용할 수 있다. EntityManager.persist\(\) 메소드를 사용하면 되므로 INSERT 문은 없다.

  \*\*\*\*

**SELECT 문**

```java
SELECT m FROM Member AS m WHERE m.username = 'Hello'
```

대소문자 구분

* 엔티티와 속성은 대소문자를 구분한다.

  * Member, username은 대소문자를 구분한다.
  * SELECT, FROM, AS 같은 JPQL 키워드는 대소문자를 구분하지 않는다.

  엔티티 이름

* JPQL에 사용한 Member는 클래스 명이 아니라 엔티티 명이다.

  * @Entity\(name="Member"\)로 지정할 수 있다.
  * 지정하지 않으면 클래스 명을 기본값으로 사용한다.
  * 기본값이 클래스 명을 엔티티 명으로 사용하는 것을 추천한다.

  별칭은 필수

* Member AS m 처럼 m이라는 별칭을 주어야 한다.

  \*\*\*\*

**TypeQuery, Query**

* 작성한 JPQL을 실행하려면 쿼리 객체를 만들어야 한다.
* 쿼리 객체는 TypeQuery와 Query가 있는데 반환할 타입을 명확하게 지정할 수 있으면 TypeQuery 객체를 사용한다.
* 반환 타입을 명확하게 지정할 수 없으면 Query 객체를 사용한다.

  ```java
  //TypeQuery 사용

  TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class );

  List<Member> resultList = query.getResultList();
  for (Member member : resultList) {
   System.out.println("member = " + member);
  }
  ```

  ```java
  //Query 사용

  Query query = em.createQuery("SELECT m.username, m.age FROM Member m", Member.class );

  List resultList = query.getResultList();
  for (Object o : resultList) {
   System.out.println("username = " + result[0]);
   System.out.println("age = " + result[1]);
  }
  ```

  \*\*\*\*

**결과 조회**

실제 쿼리를 실행해서 데이터베이스를 조회한다.

* query.getResultList\(\) : 결과를 예제로 반환한다. 만약 결과가 없으면 빈 컬렉션을 반환한다.
* query.getStringResult\(\) : 결과가 정확히 하나일 때 사용한다.
  * 결과가 없으면 javax.persistence.NoResultException 예외가 발생한다.
  * 결과과 1개보다 많으면 javax.persistence.NonUniqueResultException 예외가 발한다.

**파라미터 바인딩**

JDBC는 위치 기준 파라미터 바인딩만 지원하지만 JPQL은 이름 기준 파라미터 바인딩도 지원한다.

**이름 기준 파라미터**

* 이름 기준 파라미터는 파라미터를 이름으로 구분하는 방법이다.

  `이름 기준 파라미터는 앞에 :를 사용한다.`

  ```text
  TypedQuery query = em.createQuery("SELECT m FROM Member m where m.username = :username", Member.class)
  query.setParameter("username", usernameParam);
  List resultList = query.getResultList();
  ```

\*\*위치 기준 파라미터\*\*



```text
 **위치 기준 파라미터**

 ``위치 기준 파라미터를 사용하려면 ? 다음에 위치 값을 주면 된다. 위치 값은 1부터 시작한다.``
    List<Member> members = em.createQuery("SELECT m FROM Member m where m.username = ?1", Member.class)
    .setParameter(1, usernameParam)
    .getResultList();
```

**위치 기준 파라미터방식보다는 이름 기준 파라미터 바인딩 방식을 사용하는 것이 더 명확하다.**

**프로젝션**

SELCT 절에 조회할 대상을 지정하는 것을 프로젝션\(projection\)이라 한다.

* \[SELECT {프로젝션 대상} FROM\]으로 대상을 선택한다.
* 엔티티, 엠비디드 타입, 스칼라 타입이 있다.
* 스칼라 타입은 숫자, 문자 등 기본 데이터 타입을 뜻한다.



**엔티티 프로젝션**

```java
SELECT m FROM Member m //회원

SELECT m.team FROM Member m // 팀
```

처음은 회원을 조회했고 두 번째는 회원과 연관되 팀을 조회했는데 둘 다 엔티티를 프로젝션 대상으로 사용했다.

원하는 객체를 바로 조회한 것인데 컬럼을 하나하나 나열해서 조회하는 SQL과는 차이가 있다.

조회한 엔티티는 연속성 컨텍스에서 관리된다.

**임베디드 타입 프로젝션**

JPQL에서 임베디드 타입은 엔티티와 거의 비슷하게 사용된다.

임베디드 타입은 조회의 시작점이 될 수 없다는 제약이 있다.

```java
    String query = "SELECT o.address FROM Order o";
    List<Address> addresses = em.createQuery(query, Address.class).getResultList();
```

임베디드 타입은 엔티티 타입이 아닌 값 타입이다. 따라서 이렇게 직접 조회한 임베디드 타입은 영속성 컨텍스트에서 관리되지 않는다.

**스칼라 타입 프로젝션**

숫자, 문자, 날짜와 같은 기본 데이터 타입들을 스칼라 타입이라 한다.

```java
List<String> usernames = em.createQuery("SELECT username FROM Member m". String.class).getResultList();
//SELECT DISTINCT username FROM Member m

Double orderAmoutAvg = em.createQuery("SELECT AVG(o.orderAmount) FROM Order o", Double.class).getSingleResult();
```

**여러 값 조회**

엔티티를 대상으로 조회하면 편리하겠지만, 꼭 필요한 엔티티들만 선택해서 조회해야 할 떄도 있다.

프로젝션에 여러 값을 선택하면 TypeQuery를 사용할 수 없고, 대신에 Query를 사용해야 한다.

```java
 Query query = em.createQuery("SELECT m.username, m.age FROM Member m");

 List resultList = query.getResultList();

 Iterator iterator = resultList.iterator();
 while (iterator.hasNext()) {
    Object[] row = (Object[]) iterator.next();
    String username = (String) row[0]; 
    Integer age = (Integer) row[1];
}
```

제네릭 Object\[\] 사용하여 코드를 더 간결하게 만들 수 있다.

```java
    List<Object[]> resultList = em.createQuery("SELECT m.username, m.age FROM Member m").getResultList();

    for (Object[] row : resultList) {
        String username = (String) row[0];
        Integer age = (Integer) row[1];
    }
```

스칼라 타입뿐만 아니라 엔티티 타입도 여러 값을 함꼐 조회할 수 있다.

```java
    List<Object[]> resultList = em.createQuery("SELECT o.member, o.product, o.orderAmount FROM Order o").getResultList();

    for(Object[] row : resultList) {
        Member member = (Member) row[0];    //엔티티
        Product product = (Product) row[1]; //엔티티
        int orderAmount = (Integer) row[2]; //스칼라
    }
```

**NEW 명령어**

NEW 명령어 사용 전

```java
     List<Object[]> resultList = em.createQuery("SELECT o.member, o.product, o.orderAmount FROM Order o").getResultList();

    //객체 변환작업
     List<UserDTO> userDTOs = new ArrayList<UserDTO>();

     for(Object[] row : resultList) {
        UserDTO userDTO = new UserDTO((String) row[0], (Integer)row[1]);
        userDTOs.add(userDTO);
     }

     return userDTOs;

     public class UserDTO {

        private String username;
        private int age;

        public UserDTO(String username, int age) {
            this.username = username;
            this.age = age;
        }           
    }
```

NEW 명령어 사용 후

```java
    TypedQuery<UserDTO> query = em.createQuery("SELECT new jpabook.jpql.UserDTO(m.username, m.age) FROM Member m", UserDTO.class);

    List<UserDTO> resultList = query.getResultList();
```

SELECT 다음에 NEW 명령어를 사용하면 반환받을 클래스를 지정할 수 있는데 이 클래스의 생성자에 JPQL 조회 결과를 넘겨줄 수 있다.

NEW 명령어를 사용한 클래스로 TypeQuery 사용할 수 있어서 지루한 객체 변환작업을 줄일 수 있다.

주의할 점

* 패키지 명을 포함한 전체 클래스 명을 입력해야한다.
* 순서와 타입이 일치하는 생성자가 필요하다.

  \*\*\*\*

**페이징 API**

페이징 처리용 SQL을 작성하는 일은 지루하고 반복적이다.

데이터베이스마다 페이징을 처리하는 SQL 문법이 다르다는 점이다.

JPA 페이징은 두 API로 추상화 했다.

* setFirestREsult\(int startPosition\) : 조회 시작 위치\(0부터 시작한다.\)
* setMaxResults \(int maxResult\) : 조회할 데이터 수



```java
 TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m ORDER BY m.username DESC", Member.class);

 query.setFirstResult(10);
 query.setMaxResults(20);
 query.getResultList();
```

\*\*\*\*

**집합과 정렬**

집합은 집합함수와 함께 통계 정보를 구할 때 사용한다.

* COUNT : 결과 수를 구한다. 반환 타입 : Long
* MAX, MIN : 최대, 최소 값을 구한다. 문자, 숫자, 날짜 등에 사용한다.
* AVG : 평균값을 구한다. 숫자타입만 사용할 수 있다. 반환 타입 : Double
* SUM : 합을 구한다. 숫자타입만 사용할 수 있다. 
  * 반환타입 : 정수합 Long, 소수합 Double, BigInteger합 : BigInteger, BigDecimal합 : BigDecimal

집합 함수 사용 시 참고사항

* NULL 값은 무시하므로 통계에 잡히지 않는다. \(DISTINCT가 정의되어 있어도 무시된다.\)
* 값이 없는데 SUM, AVG, MAX, MIN 함수를 사용하면 NULL 값이 된다. COUNT는 0이 된다.
* DISTINCT를 집합 함수 안에 사용해서 중복된 값을 제거하고 나서 집합을 구할 수 있다.
  * select COUNT\(DISTINCT m.age\) from Member m
* DISTINCT를 COUNT에서 사용할 때 임베디드 타입은 지원하지 않는다.

데이터베이스에서의 사칙연산에 NULL이 들어가면 NULL이 나온다.

NULL값 공포!

**GROUP BY, HAVING**

GROUP BY는 통계 데이터를 구할 떄 특정 그룹끼리 묶어준다.

HAVING 은 GROUP BY와 함꼐 사용하는데 GROUP BY로 그룹화한 통계 데이터 기준으로 필터링 한다.

`통계 쿼리에서 결과가 아주 많다면 통계 결과만 저장하는 테이블을 별도로 만들어 두고 사용자가 적은 새벽에 통계 쿼리를 실행해서 그 결과를 보관하는 것이 좋다.`

**정렬\(ORDER BY\)**

ORDER BY는 결과를 정렬할 떄 사용한다.

* ASC : 오름차순\(기본값\)
* DESC : 내림차순

**JPQL 조인**

JPQL도 조인을 지원하는데 SQL 조인과 기능은 같고 문법만 약간 다르다.

**내부조인**

내부 조인은 INNER JOIN을 사용한다. 참고로 INNER는 생략 할 수 있다.

```java
 String teamName = "팀A";
 String query = "SELECT m FROM Member m INNER JOIN m.team t WHERE t.name = :teamName";

 List<Member> members = em.createQuery(query, Member.class).setParameter("teamName", teamName).getResultList();
```

JPQL 조인의 가장 큰 특징은 연관 필드를 사용한다는 것이다.

여기서 m.team이 연관 필드인데 연관필드는 다른 엔티티와 연관관계를 가지기 위해 사용하는 필드를 말한다.

* FROM Member m : 회원을 선택하고 m이라는 별칭을 주었다.
* Member m JOIN m.team t : 회원이 가지고 있는 연관 필드로 팀과 조인한다. 조인한 팀에는 t라는 별칭을 주었다.

`JPQL 조인을 SQL 조인처럼 사용하면 문법 오류가 발생한다.`

```java
    FROM Member m JOIN Team t // 조인오류
```

만약 조인한 두 개의 엔티티를 조회하려면 다음과 같이 작성하면 된다.

```java
    SELECT m, t FROM Member m JOIN m.team t
```

서로 다른 타입의 두 엔티티를 조회했으므로 TypeQuery를 사용할 수 없다.

따라서 다음처럼 조회해야 한다.

```java
    List<Object[]> result = em.createQuery(query).getResultList();

    for(Object[] row : result) {
        Member member = (Member) row[0];
        Team team = (Team) row[1];
    }
```

**외부 조인**

```java
SELECT m FROM Member m LEFT [OUTER] JOIN m.team t
```

외부 조인 가능상 SQL의 외부 조인과 같다.

OUTER는 생략 가능해서 보통 LEFT JOIN으로 사용한다.

**컬렉션 조인**

일대다 관계나 다대다 관계처럼 컬렉션을 사용하는 곳에 조인하는 것을 컬렉션 조인이라 한다.

* \[회원 -&gt; 팀\]으로의 조인은 다대일 조인이면서 단일 값 연관 필드\(m.team\)를 사용한다.
* \[팀 -&gt; 회원\]은 반대로 일대다 조인이면서 컬렉션 값 연관 필드\(m.members\)를 사용한다.

  ```java
  SELECT t, m FROM TEAM t LEFT JOIN t.members m
  ```

t LEFT JOIN t.members는 팀과 팀이 보유한 회원목록을 컬렉션 값 연관 필드로 외부 조인 했다.

**세타 조인**

WHERE 절을 사용해서 세타 조인을 할 수 있다.

참고로 세타 조인은 내부 조인만 지원한다.

```java
    //JPQL
    select count(m) from Member m, Team t
    where m.username = t.name

    //SQL
    SELECT COUNT(M.ID)
    FROM
        MEMBER M CROSS JOIN TEAM T
    WHERE 
        M.USERNAME = T.NAME
```

**JOIN ON 절**

```java
    //JPQL
    select m,t from Member m
    left join m.team t on t.name = 'A'

    //SQL
    SELECT m.*, t.* FROM Member m
    LEFT JOIN Team t ON m.TEAM_ID = t.id and t.name = 'A'
```

SQL 결과를 보면 and t.name='A'로 조인 시점에 조인 대상을 필터링한다.

**페치 조인**

페치 조인은 SQL에서 이야기하는 조인의 종류는 아니고 JPQL에서 성능 최적화를 위해 제공하는 기능이다.

연관된 엔티티나 컬렉션을 한 번에 같이 조회하는 기능인데 join fetch 명령어로 사용할 수 있다.

JPA 표준 명세에 정의된 페치 조인 문법은 다음과 같다.

* 페치 조인 ::= \[ LEFT \[OUTER\] \| INNER \] JOIn FETCH 조인경로

  \*\*\*\*

**엔티티 페치 조인**

```java
 select m from Member m join fetch m.team
```

연관된 엔티티나 컬렉션을 함께 조회를 회원\(m\)과 팀\(m.team\)을 함께 조회한다.

페치 조인은 별칭을 사용할 수 없다.

회원과 팀 객체가 객체 그래프를 유지하면서 조회 된다.

```java
 String jpql = "select m from Member m join fetch m.team";

 List<Member> members = em.createQuery(jpql, Member.class).getResultList();

 for (Member member : members) {
     //페치 조인으로 회원과 팀을 함께 조회해서 지연 로딩 발생 안 함
     System.out.println("username = " + member.getUsername() + ", teamname = " + member.getTeam().name() );
 }
```

회원을 조회할 때 페치 조인을 사용해서 팀도 함께 조회했으므로 연관된 팀 엔티티는 프로깃가 아닌 실제 엔티티다.

따라서 연관된 팀을 사용해도 지연 로딩이 일어나지 않는다.

프록시가 아닌 실제 엔티티이므로 회원 엔티티가 영속성 컨텍스트에서 분리되어 준영속성 사태가 되어도 연관된 팀을 조회할 수 있다.

**컬렉션 페치 조인**

```java
    select t from Team t join fetch t.members
    where t.name = '팀A'
```

**페치 조인과 DISTINCT**

JPQL의 DISTINCT 명령어는 SQL에 DISTINCT를 추가하는 것은 물론이고 애플리케이션에서 한번 더 중복을 제거한다.

```java
    select distinct t
    from Team t join fetch t.members
    where t.name = '팀A'
```

**페치 조인과 일반 조인의 차이**

```java
    //내부 조인 JPQL
    select t
    from Team t join t.members m 
    where t.name = '팀A'

    //실행된 SQL
    SELECT 
        T.*
    FROM TEAM T
    INNER JOIN MEMBER M ON T.ID=M.TEAM_ID
    WHERE T.NAME = '팀A'
```

JPQL은 결과를 반환할 때 연관관계까지 고려하지 않는다. 단지 SELECT 절에 지정한 엔티티만 조회할 뿐이다.

지연 로딩으로 설정하면 프록시나 아직 초기화하지 않은 컬렉션 래퍼를 반환한다.

즉시 로딩으로 설정하면 회원 컬렉션을 즉시 로딩하기 위해 쿼리를 한 번 더 실행한다.

반면에 페치 조인을 사용하면 연관된 엔티티도 함께 조회한다.

```java
    //컬렉션 패치 조인 JPQL
    select t 
    from Team t join fetch t.members
    where t.name = '팀A'

    //실행된 SQL
    SELECT
        T.*, M.*
    FROM TEAM T
    INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
    WHERE T.NAME = '팀A'
```

**페치 조인의 특징과 한계**

페치 조인의 특징

* 페치 조인을 사용하면 SQL 한 번으로 연관된 엔티티들을 함께 조회할 수 있어서 SQL 호출 횟수를 줄여 성능을 최적화할 수 있다.
* 엔티티에 직접 적용하는 로딩 전략\(LAZY, EAGER\)은 애플리케이션 전체에 영향을 미치므로 글로벌 로딩 전략이라 부른다.
* 페치 조인은 글로벌 로딩 전략보다 우선한다.
* 글로벌 로딩 전략을 지연 로딩으로 설정해도 JPQL에서 페치 조인을 사용하면 페치 조인을 적용해서 함께 조회한다.
* 즉시 로딩을 실행하면 어플리케이션 전체에서 항상 즉시 로딩이 일어난다.
* 일부는 빠를 수는 있지만 전체로 보면 사용하지 않는 엔티티를 자주 로딩하므로 오히려 성능에 악영향을 미칠 수 있다.
* 따라서 글로벌 로딩 전략을 될 수 있으면 지연 로딩을 사용하고 최적화가 필요한 페치 조인을 적용하는 것이 효과 적이다.
* 페치 조인을 사용하면 연관된 엔티티를 쿼리 시점에 조회하므로 지연 로딩이 발생하지 않는다.
* 즉 준영속 상태에서도 객체 그래프 탐색할 수 있다.

페치 조인의 한계

* 페치 조인 대상에는 별칭을 줄 수 없다.
* 둘 이상의 컬렉션을 페치할 수 없다.
* 컬렉션을 페치 조인하면 페이징 API\(setFirstResult, setMaxResults\)를 사용할 수 없다.
  * 컬렉션\(일대다\)이 아닌 단일 값 연관 필드\(일대일, 다대일\)들은 페치 조인을 사용해도 페이징 API를 사용할 수 있다.
  * 하이버네이트에서 컬렉션을 페치 조인하고 페이징 API를 사용하면 경고 로그를 남기면서 메모리에서 페이징 처리를 한다.
    * 데이터가 적으면 상관없겠지만 데이터가 많으면 성능 이슈와 메모리 초과 예외가 발생할 수 있어서 위험하다.

페치 조인은 객체 그래프를 유지할 때 사용하면 효과적이다.

여러 테이블을 조인해서 엔티티가 가진 모양이 아닌 전혀 다른 결과를 내야 한다면 억지로 페치 조인을 사용하기보다는 여러 테이블에서 필요한 필드들만 조회해서 DTO로 반환하는 것이 더 효과적일 수 있다.

**경로 표현식**

경로표현식은 .\(점\)을 찍어 객체 그래프를 탐색는 것이다.

```java
    select m.username
    from Member m 
        join m.team t
        join m.orders o
    where t.name = '팀A'
```

m.username, m.team, m.orders, t.name이 모두 경로 표현식을 사용한 예다.

**경로 표현식의 용어 정리**

* 상태 필드\(state field\) : 단순히 값을 저장하기 위한 필드\(필드 or 프로퍼티\)
* 연관 필드\(association field\) : 연관관계를 위한 필드, 임베디드 타입 포함\(필드 or 프로퍼티\)
  * 단일 값 연관 필드 : @ManyToOne, @OneToOne, 대상이 엔티티
  * 컬렉션 값 연관 필드 : @OneToMany, @ManyToMany, 대상이 컬렉션

```java
    @Entity
    public class Member { 

        @Id @GeneratedValue
        private Long id;

        @Column(name = "name")
        private String username; // 상태 필드
        private Integer age; // 상태 필드

        @ManyToOne(..)
        private Team team; // 연관 필드(단일 값 연관 필드)

        @OneToMany(..)
        private List<Order> orders; //연관 필드(컬렉션 값 연관 필드)
    }
```

* 상태 필드 : t.username, t.age
* 단일 값 연관 필드 : m.team
* 컬렉션 값 연관 필드 : m.orders

  \*\*\*\*

**경로 표현식과 특징**

JPQL에서 경로 표현식을 사용해서 경로 탐색을 하려면 다음 3가지 경로에 따라 어떤 특징이 있는지 이해해야 한다.

**상태 필드 경로**

경로 탐색의 끝이다. 더는 탐색할 수 없다.

* 다음 JPQL의 m.username, m.age는 상태 필드 경로 탐색이다.
* select m.username, m.age from Member m

**단일 값 연관 경로**

묵시적으로 내부 조인이 일어난다.

단일 값 연관 경로는 계속 탐색할 수 있다.

select o.member from Order o

select m.\* from Orders o inner join Member m on o.member\_id = m.id

* 단일 값 연관 필드로 경로 탐색을 하면 SQL에서 내부 조인이 일어나는데 이것을 묵시적 조인이라 한다.
* 묵시적 조인은 모두 내부 조인이다.
* 명시적 조인 : JOIN을 직접 적어주는 것 ex\) SELECT m FROM Member m JOIN m.team t
* 묵시적 조인 : 경로 표현식에 의해 묵시적으로 조인이 일어나는 것, 내부 조인\(INNER JOIN\)만 할 수 있다. ex\) SELECT m.team FROM Member m

```java
//JPQL select o.member.team
from Order o
where o.product.name = 'productA' and o.address.city = 'JINJU'
```

```text
//실행된 SQL
select t.*
from Order o
inner join Member m on o.member_id = m.id
inner join Team t on m.team_id = t.id
inner join Product p on o.product_id = p.id
where p.name = 'productA' and o.city='JINJU'
```

```text
   **컬렉션 값 연관 경로**

   묵시적으로 내부 조인이 일어난다. 더는 탐색 할 수 없다. 단 FROM 절에서 조인을 통해 별칭을 얻으면 별칭으로 탐색할 수 있다.

   JPQL을 다루면서 많이 하는 실수 중 하나는 컬렉션 값에서 경로 탐색을 시도하는 것이다.

   ```java

    select t.members from Team t //성공
    select t.members.username from Team t //실패
```

t.members처럼 컬렉션까지는 경로 탐색이 가능하다.

t.members.username처럼 컬렉션에서 경로 탐색을 시작하는 것은 허락하지 않는다.

만약 컬렉션에서 경로 탐색을 하고 싶으면 다음 코드처럼 조인을 사용해서 새로운 별칭을 획득해야 한다.

```java
    select m.username from Team t join t.members m

    // join t.members m으로 컬렉션에 새로운 별칭을 얻었다.
    // 이제 별칭 m부터 다시 경로 탐색을 할 수 있다.
```

참고로 컬렉션은 컬렉션의 크기를 구할 수 있는 size라는 특별한 기능을 사용할 수 있다.

size를 사용하면 COUNT 함수를 사용하는 SQL로 적절히 변환된다.

```java
    select t.members.size from Team t
```

**경로 탐색을 사용한 묵시적 조인 시 주의사항**

경로 탐색을 사용하면 묵시적 조인이 발생해서 SQL에서 내부 조인이 일어날 수 있다.

* 항상 내부 조인이다.
* 컬렉션은 경로 탐색의 끝이다. 컬렉션에서 경로 탐색을 하려면 명시적으로 조인해서 별칭을 얻어야 한다.
* 경로 탐색은 주로 SELECT, WHERE 절에서 사용하지만 묵시적 조인으로 인해 SQL의 FROM 절에 영향을 준다.

  성능이 중요하면 분석하기 쉽도록 묵시적 조인보다는 명시적 조인을 사용하자.

**서브 쿼리**

JPQL도 SQL처럼 서브쿼리를 지원한다.

서브쿼리는 WHERE, HAVING 절에서만 사용할 수 있고, SELECT, FROM 절에서는 사용할 수 없다.

```java
    //JQPL

    select m from Member m
    where m.age > (select avg(m2.age) from Member m2)
    //나이가 평균보다 많은 회원을 찾는다.

    select m from Member m
    where (select count(0) from Order o where m = o.member) > 0
    //다음 한 건이라도 주문한 고객을 찾는다.

    //JPQL Size 기능

    select m from Member m where m.orders.size > 0
```

**서브 쿼리 함수**

* \[NOT\] EXISTS \(subquery\)
  * 서브쿼리에 결과가 존재하면 참이다. NOT은 반대
* \[ALL \| ANY \| SOME\] \(sebquery\)
  * 비교 연산자와 같이 사용한다. { = \| &gt; \| &gt;= \| &lt; ..}
  * ALL 조건을 모두 만족하면 참이다.
  * ANY 혹은 SOME : 둘은 같은 의미다. 조건을 하나라도 만족하면 참이다.
* \[NOT\] IN \(subquery\)

  * 서브쿼리의 결과 중 하나라도 같은 것이 있으면 참이다.
  * 참고로 IN은 서브쿼리가 아닌 곳에서도 사용한다.

  \*\*\*\*

**조건식**

**타입 표현**

문자

* 작은 따옴표 사이에 표현
* 작은 따옴표를 표현하고 싶으면 작은 따옴펴 연속 두 개\(''\)사용
* 'She''s'

숫자

* L, D, F

날짜

* DATA {d 'yyy-mm-dd'}
* TIME {t 'hh-mm-ss'}
* DATETIME{ts 'yyy-mm-dd hh:mm:ss.f'}

Boolean

* TRUE, FALSE

Enum

* 패키지명을 포함한 전체 이름을 사용해야 한다.
* jpabook.MemberType.Admin

엔티티 타입

* 엔티티의 타입을 표현한다. 주로 상속과 관련해서 사용한다.
* TYPE\(m\) = Member

**Between, IN, Like, NULL 비교**

**컬렉션 식**

컬렉션 식은 컬렉션에만 사용하는 특별한 기능이다.

빈 컬렉션 비교 식

* 문법 : {컬렉션 값 연관경로 } Is \[NOT\] EMPTY
* 설명 : 컬렉션에 값이 비었으면 참

```java
//JPQL : 주문이 하나라도 있는 회원 조회
select m from Member m
where m.orders is not empty

//실행된 SQL
select m.* from Member m
where 
   exists {
       select o.id
       from Orders o
       where m.id= o.member_id
   }
```

**컬렉션의 멤버 식**

* 문법 : {엔티티나 값} \[NOT\] MEMBER \[OF\] {컬렉션 값 연관 경로}
* 설명 : 에티티나 값이 컬렉션에 포함되어 있으면 참

  ```java
  select t from Team t
  where :memberParam member of t.members
  ```

  \*\*\*\*

**스칼라 식**

스칼라는 숫자, 문자, 날짜, case, 엔티티 타입 같은 가장 기본적인 타입들을 말한다.

* 수학 식
* 문자 함수
* 수학 함수
* 날짜 함수

  \*\*\*\*

**CASE 식**

특정 조건에 따라 분기할 때 CASE 식을 사용한다.

* 기본 CASE
* 심플 CASE
* COALESCE
* NULLIF

  \*\*\*\*

**다형성 쿼리**

JPQL는 부모 엔티티를 조회하면 그 자식 엔티티도 함께 조회한다.

```java
@Entity
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscrimiantorColumn(name = "DTYPE")
public abstract class Item {...}

@Entity
@DiscriminatorValue("B")
public class Book extends Item { 
   private String author;
}

//Album, Movie 생략

List resultList = em.createQuery("select i from Item i").getResultList();
```

**TYPE**

TYPE은 엔티티의 상속 구조에서 조회 대상을 특정 자식타입으로 한정할 떄 주로 사용한다.

**TREAT\(JPA2.1\)**

자바의 타입 캐스팅과 비슷하다.

상속구조에서 부모 타입을 특정 자식 타입으로 다룰 때 사용한다.

**사용자 정의 함수 호출\(JPA2.1\)**

JPA 2.1부터 사용자 정의 함수를 지원한다.

* 문법 : function\_invocation:: = FUNCTION\(function\_name {, function\_arg\)\*\)
* 예 : select function\('group\_concat', i.name\) from Item i

```java
public class MyH2Dialect extends H2Dialect { 
   public MyH2Dialect() {
       registerFunction("group_concat", new StandardSQLFunction("group_concat", StandardBasicTypes.STRING))
   }
}
```

hibernate.dialect에 해당 방언을 등록해야 한다.

select group concat\(i.name\) from Item i

\*\*\*\*

**기타 정리**

* enum을 = 비교 연산만 지원한다.
* 임베디드 타입은 비교를 지원하지 않는다.

  \*\*\*\*

**EMPTY STRING**

**NULL 정의**

**엔티티 직접 사용**

**외래 키 값**

**Named 쿼리 : 정적 쿼리**

**호출\(JPA2.1\)**

JPA 2.1부터 사용자 정의 함수를 지원한다.

* 문법 : function\_invocation:: = FUNCTION\(function\_name {, function\_arg\)\*\)
* 예 : select function\('group\_concat', i.name\) from Item i

```java
public class MyH2Dialect extends H2Dialect { 
   public MyH2Dialect() {
       registerFunction("group_concat", new StandardSQLFunction("group_concat", StandardBasicTypes.STRING))
   }
}
```

hibernate.dialect에 해당 방언을 등록해야 한다.

select group concat\(i.name\) from Item i

\*\*\*\*

**기타 정리**

* enum을 = 비교 연산만 지원한다.
* 임베디드 타입은 비교를 지원하지 않는다.

  \*\*\*\*

**EMPTY STRING**

**NULL 정의**

**엔티티 직접 사용**

**외래 키 값**

**Named 쿼리 : 정적 쿼리**

