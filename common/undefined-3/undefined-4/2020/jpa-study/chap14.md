---
description: 이것은 김영한님의 JPA책을 정리한 글입니다.
---

# Chap14

##

## JPA 14장 - 컬렉션과 부가 기능

14장에서 다루는 주제는 크게 컬렉션, 컨버터, 리스너, 엔티티 그래프 입니다.

### 컬렉션

JPA는 자바에서 기본으로 제공하는 Collection, List, Set, Map 컬렉션을 지원합니다.

* @OneToMany, @ManyToMany, @ElementCollection에 사용
* Collection : 자바가 제공하는 최상위 컬렉션.
  * 하이버네이트는 중복을 허용하고 순서를 보장하지 않는다고 가정한다.
* Set : 중복을 허용하지 않는 컬렉션. 순서를 보장하지 않음.
* List : 순서가 있는 컬렉션. 순서를 보장하고 중복을 허용한다.
*   Map : Key, Value 구조로 되어 있는 특수한 컬렉션

    **JPA와 컬렉션**

    하이버네이트는 엔티티를 영속 상태로 만들 때 컬렉션 필드를 하이버네이트에서 준비한 컬렉션으로 감싸 사용한다.



```

java @OneToMany @JoinColumn private Collection members = new ArrayList();
-->
Team team = new Team();
System.out.println("before persist =" + team.getMembers().getClass()); em.persist(parent); Systme.out.println("after persist = " + team.getMember().getClass);
-->
before persist = class java.util.ArrayList after persist = class org.hibernate.collection.internal.PersistnetBag
```

* ArrayList 타입이었던 컬렉션이 엔티티 영속 상태로 만든 직후 하이버네이트가 제공하는 PersistenetBag 타입으로 변경되었다.

즉 하이버네이트는 컬렉션의 효율적으로 관리하기 위해 엔티티를 영속 상태로 만들 때 원본 컬렉션을 감싸고 있는 내장 컬렉션을 생성해서 이 내장 컬렉션을 사용하도록 참조를 변경한다.

하이버네이트가 제공하는 내장 컬렉션은 원본 컬렉션을 감싸고 있어서 래퍼 컬렉션으로도 부른다.

`Collection, List, Set, Map 모두 래퍼 컬렉션이 다르다.`

결과적으로 이런 특징 때문에 컬렉션을 사용할 때 초기화해서 사용하는 것을 권장한다.

```
Collection<Member> members = new ArrayList<Member>();
```

****

**이제 Collection, List, Set, Map에 대한 래퍼 컬렉션에 대해서 알아보자.**

**Collection, List**

* 내장 컬렉션 : PersistenceBag
* 중복 허용 : O
* 순서 보관 : X
* ArrayList로 초기화

```java
@Entity
public class Parent {

    @Id @GeneratedValue
    private Long id;

    @OneToMany
    @JoinColumn
    private Collection<CollectionChild>  collection = new ArrayList<CollectionChild>();

    @OneToMany
    @JoinColumn
    private List<ListChild> list = new ArrayList<ListChild>();

}
```

```java
    List<Comment> comments = new ArrayList<Comment>();

    //단순히 추가만 한다. 결과는 항상 true다.
    boolean result = commtents.add(data)

    comments.contaions(comment);
    comments.remove(comment);
```

Collection, List는 엔티티를 추가할 때 중복된 엔티티가 있는지 비교하지 않고, 단순히 저장만 하면 된다. 따라서 엔티티를 추가해도 지연 로딩된 컬렉션을 초기화 하지 않는다.

Q1. 왜 지연 로딩된 컬렉션을 초기화 하지 않을까?

**Set**

* 내장 컬렉션 : PersistenceSet
* 중복 허용 : X
* 순서 보관 : X
*   HashSet으로 초기화

    ```java
     @Entity
     public class Parent {   

     @OneToMany
     @JoinColumn
     private Set<SetChild> set = new HashSet<SetChild>();
    }
    ```

```java
    Set<Comment> comments = new HashSet<Comment>();

    boolean result = comments.add(data) 
    comments.contains(comment);
    comments.remove(comment);
```

Set은 엔티티를 추가할 때 중복된 엔티티가 있는지 비교해야 한다. 따라서 엔티티를 추가할 때 지연 로딩된 컬렉션을 초기화한다.

**List + @OrderColumn**

* 내장 컬렉션 : PersistentList
* 중복 허용 : O
*   순서 보관 : O

    ```java
     @Entity
     public class Board {

         @Id @GeneratedValue
         private Long id;

         private String title;
         private String content;

         @OneToMany(mappedBy = "board")
         @OrderColumn(name = "POSITION")
         private List<Commnet> comments = new ArrayList<Comment>();

         ...
     }

     @Entity
     public class Comment {

         @Id @GeneratedValue
         private Long id;

         private String comment;

         @ManyToOne
         @JoinColumn(name = "BOARD_ID")
         private Board board;
     }
    ```

1. Board.comments에 List 인터페이스를 사용하고 @OrderColumn을 추가했다.
2. Board.comments는 순서가 있는 컬렉션으로 인식된다.

자바가 제공하는 List 컬렉션은 내부에 위치 값을 가지고 있다. 따라서 다음 코드처럼 List의 위치 값을 활용할 수 있다.

```java
    List.add(1,data1); // 1번 위치에 data1을 저장하라.
    List.get(10); // 10번 위치에 있는 값을 조회하라.
```

순서가 있는 컬렉션은 데이터베이스에 순서 값도 함께 관리한다.

즉 POSITION이라는 속성값이 바로 순서를 나타내는 순서값이다.

![](https://k.kakaocdn.net/dn/bG02et/btqEUFQ2vvb/3YBgaetEfWLTvieSN7krhk/img.png)

```java
Board board = new Board("제목1", "내용1");
em.persist(board);

Comment comment1 = new Comment("댓글1");
comment1.setBoard(board);
board.getComments().add(comment1); //POSITION 0
em.persist(comment1);

Comment comment2 = new Comment("댓글2");
comment2.setBoard(board);
board.getCOmments().add(comment2); // POSITION 1
em.persist(comment2);
```

@OrderColumn을 사용해서 List의 위치 값을 보관하면 편리할 것 같지만 실무에서 사용하기에는 단점이 많다.

* @OrderColumn을 Board 엔티티에서 매핑하므로 Comment는 POSITION의 값을 알 수 없다. 그래서 Comment를 INSERT할 때는 POSTION값이 저장되지 않는다.
  * POSITION은 Board.comments의 위치 값이므로, 이 값을 사용해서 POSITION의 값을 UPDATE 하는 SQL이 추가로 발생한다.
* List를 변경하면 연관된 많은 위치 값을 변경해야 한다.
  * 댓글2를 삭제하면 댓글3, 댓글 4의 POSITION 값을 각각 하나씩 줄이는 UPDATE SQL이 2번 추가로 실행된다.
*   중간에 POSITION값이 없으면 조회한 LIST에는 null이 보관된다.

    * NullPointerException이 발생한다.

    이러한 단점을 가지고 있기 때문에 @OrderColumn을 매핑하지 말고 개발자가 직접 POSITION 값을 관리하거나 다음에 설명하는 @OrderBhy를 사용하길 권장한다.

    **@OrderBy**

    @orderBy는 데이터베이스의 ORDER BY절을 사용해서 컬렉션을 정렬한다.
* 순서용 컬럼을 매핑하지 않아도 된다.
*   @OrderBy는 모든 컬렉션에 사용할 수 있다.

    ```java
     @Entity
     public class Team { 

         @Id @GeneratedValue
         private Long id;
         private String name;

         @OneToMany(mappedBy = "team")
         @OrderBy("username desc, id asc")
         private Set<Member> members = new HashSet<Member>();
     }
    ```

@OrderBy의 값으로 username desc, id asc를 사용해서 Member의 username 필드로 내림차순으로 정렬하고 id로 오름차순 정렬 했다.

@OrderBy의 값은 JPQL의 order by절처럼 엔티티의 필드를 대상으로 한다.

추가로 하이버네이트는 Set에 @OrderBy를 적용해서 결과를 조회하면 순서를 유지하기 위해 HashSet 대신에 LinkedHashSet을 내부에서 사용한다.

### @Converter

컨버터를 사용하면 엔티티의 데이터를 변환해서 데이터베이스에 저장한다.

데이터베이스에 0 또는 1인 숫자를 N이나 Y로 변경해서 저장할때 컨버터를이용하면 된다.

1 . @Converter를 적용해서 데이터베이스에 저장되기 직전에 BooleanToYNConverter 컨버터가 동작하도록 했다.

```java
    @Entity
    public class Member { 
        @Converter(converter=BooleanToYNConverter.class)
        private  boolean vip;
    }

    @Converter
    public class BooleanToYNConverter implements AttributeConverter<Boolean,String> { 

        @Override
        public String covertToDatabaseColumn(Boolean attribute) { 
            return (attribute != null && atrribute) ? "Y" : "N";    
        }   

        @Override
        public Boolean convertToEntityAttribute(String dbData) { 
            return "Y".equals(dbData);
        }   
    }
```

2 . 제네릭에 현재 타입과 변환할 타입을 지정해야 한다.

* 여기서는 Boolean, String을 지정해서 Boolean 타입을 String 타입으로 변환한다.

3 . 컨버터 클래스는 @Converter 어노테이션을 사용하고 AttributeConverter인터페이스를 구현해야 한다.

```java
    public interface AttributeConver<X,Y> { 
        public Y convertToDatabaseColumn (X attribute);
        public X convertToEntityAttribute (Y dbData);
    }
```

* converToDatabaseColumn() : 엔티티의 데이터를 데이터베이스 컬럼에 저장할 데이터로 변환한다.
  * ture면 Y를 false면 N을 반환하도록 한다.
*   convertToEntityAttribute() : 데이터베이스에서 조회한 컬럼 데이터를 엔티티의 데이터로 변환한다.

    * 문자 Y면 true를 아니면 false를 반환하도록 했다.

    4 . 이제부터 회원 엔티티를 저장하면 데이터베이스의 VIP 컬럼에는 Y 또는 N이 저장된다.

    같은 클래스 레벨에 설정할 수 있다.

    ```java
     @Entity
     @Convert(converter = BooleanToYNConverter.class, attributeName = "vip")
     public class Member { 
         @Id
         private String id;
         private String username;

         private boolean vip;
     }
    ```

#### 글로벌 설정

모든 Boolean 타입에 컨버터를 적용하려면 @Converter(autoApply = true)옵션을 적용하면 된다.

```java
    @Converter(autoApply = ture)
    public class BooleanToYNConverter implements AttributeConverter<Boolean, String> {
    ...
}
```

이렇게 글로벌 설정을 하면 @Convert를 지정하지 않아도 모든 Boolean 타입에 대해 자동으로 컨버터가 적용된다.

![](https://k.kakaocdn.net/dn/oCFBv/btqEVZgUqxf/OcsQWF6D106VxKKs4fPMyk/img.png)

### 리스너

모든 엔티티를 대상으로 언제 어떤 사용자가 삭제를 요청했는지 모두 로그로 남겨야 하는 요구사항이 있다.

JPA 리스터 기능을 사용하면 엔티티의 생명주기에 따른 이벤트를 처리할 수 있다.

#### 이벤트의 종류

![](https://k.kakaocdn.net/dn/tOvl4/btqEVXcxItQ/eomcVKxWEmkxFMJytldZz1/img.png)

1. PostLoad&#x20;
   * 엔티티가 영속성 컨텍스트에 조회된 직후 또는 refresh를 호출 한 후(2차 캐시에 저장되어 있어도 호출된다.)
2. PrePsersist&#x20;
   * persist() 메소드를 호출해서 엔티티를 영속성 컨텍스트에 관리하기 직전에 호출된다.
     * 식별자 생성 전략을 사용한 경우 엔티티에 식별자는 아직 존재하지 않는다.
     * 새로운 인스턴스를 merge할 때도 수행된다.
3. preUpdate&#x20;
   * flush나 commit 호출해서 엔티티를 데이터베이스에 수정하기 직전에 호출된다.
4. PreRemove&#x20;
   * remove() 메소드를 호출해서 엔티티를 영속성 컨텍스트에서 삭제하기 직전에 호출된다.
5. PostPersist&#x20;
   * flush나 commit을 호출해서 엔티티를 데이터베이스에 저장한 직후에 호출된다.  &#x20;
     * 식별자가 항상 존재한다.
6. PostUpdate&#x20;
   * flush나 commit을 호출해서 엔티티를 데이터베이스에 수정한 직후에 호출된다.
7. PostRemove&#x20;
   * flush나 commit을 호출해서 엔티티를 데이터베이스에 삭제한 직후에 호출 된다.

#### 이벤트 적용 위치

이벤트는 엔티티에서 직접 받거나 별도의 리스너를 등록해서 받을 수 있다.

* 엔티티에 직접 적용
* 별도의 리스너 등록
* 기본 리스너 사용

**엔티티에 직접 사용**

```java
    @Entity
    public class Duck { 
        @PostPersist
        public void prePersist(){ 
            System.out.println("Duck.prePersist id= " + id);
        }   
    }
```

엔티티에 이벤트가 발생할 때마다 어노테이션으로 지정한 메소드가 실행된다.

#### 별도의 리스너 등록

```java
    @Entity
    @EntityListners(DuckListener.class)
    public class Duck {...}

    public class DuckListener { 
        @PrePersist
        private void prePersist(Object obj) { 
            System.out.println("DuckListner.prePersist obj = [ " + obj + "]");
        }
    }
```

리스너는 대상 엔티티를 파라미터로 받을 수 있다 .반환타입은 void로 설정해야 한다.

#### 기본 리스너 사용

모든 엔티티의 이벤트를 처리하려면 META-INF/orm.xml에 기본 리스너로 등록하면 된다.

```markup
<?xml version="1.0" encoding="UTF-8" ?>
<entity-mappings xmlns="http://java.sun.com/xml/ns/persistence/orm" version="2.0">

    <persistence-unit-metadata>
        <persistence-unit-defaults>
            <entity-listeners>
                <entity-listener class="jpabook.jpashop.domain.test.listener.DefaultListener"/>
            </entity-listeners>
        </persistence-unit-defaults>
</persistence-unit-metadata>

</entity-mappings>
```

여러 리스너를 등록 했을 때 이벤트 호출 순서는 다음과 같다.

1.  기본리스너 -> 2. 부모 클래스 리스너 -> 3. 리스너 -> 4. 엔티티

    **더 세밀한 설정**

    더 세밀한 설정을 위한 어노테이션도 있다.

    * @ExcludeDefaultListners : 기본 리스너 무시
    * @ExcludeSuperclassListeners : 상위 클래스 이벤트 리스너 무시

    이벤트를 잘 활용하면 대부분의 엔티티에 공통으로 적용하는 등록 일자, 수정일자 처리와 해당 엔티티를 누가 등록하고 수정했는지에 대한 기록을 리스너 하나로 처리할 수 있다.

### 엔티티 그래프

엔티티를 조회할 때 연관된 엔티티를 함께 조회하려면 글로벌 fetch옵션을 FetchType.EAGER로 설정한다.

글로벌 fetch옵션은 애플리케이션 전체에 영향을 주고 변경할 수 없는 단점이 있다.

그래서 FetchType.LAZY를 사용하고, 엔티티를 조회할 때 연관된 엔티티를 함께 조회할 필요가 있으면 JPQL의 패치 조인을 사용한다.

그런데 페치 조인을 사용하면 JPQL을 중복해서 작성하는 경우가 많다.

그래서 이것을 해결하기 위해 나온 개념이 바로 JPA 2.1에 추가된 엔티티 그래프 기능이다.

즉 엔티티를 조회하는 시점에 함께 조회할 연관된 엔티티를 설정 할 수 있다.

`엔티티 그래프 기능은 엔티티 조회시점에 연관된 엔티티들을 함께 조회하는 기능이다.`

#### Named 엔티티 그래프

```java
    @NamedEntityGraph(name = "Order.withMember", attributeNodes = { 
        @NamedAttributeNode("member")
    })
    @Entity
    @Table(name = "ORDERS")
    public class Order { 
        ...
        @ManyToOne(fetch = FetchType.LAZY , optional = false) 
        @JoinColumn(name = "MEMBER_ID")
        private Member member;
    }
```

Namde 엔티티 그래프는 @NamedEntityGraph로 정의한다.

* name : 엔티티 그래프의 이름을 정의한다.
* attributeNodes : 함께 조회할 속성을 선택한다.
  * @NamedAttributeNode를 사용하고 그 값으로 함께 조회할 속성을 선택하면 된다.

Order.member가 지연 로딩으로 설정되어 있지만, 엔티티 그래프에서 함께 조회할 속성으로 member를 선택했으므로 이 엔티티 그래프를 사용하면 Order를 조회할 때 연관된 member도 함께 조회 할 수 있다.

둘 이상 정의하려면 @NamedEntityGraphs를 사용하면 된다.

#### em.find()에서 엔티티 그래프 사용

```java
    EntityGraph graph = em.getEntityGraph("Order.withMember")

    Map hints = new HashMap();
    hints.put("javax.persistence.fetchgraph", graph);

    Order order = em.find(Order.class, orderId, hints);
```

#### subgraph

```java
    @NamedEntityGraph(name = "Order.withAll", attributeNodes = { 
        @NamedAttributeNode("member"),
        @NamedAttribute(value = "orderItems", subgraph = "orderItems")
        },
        subgraphs = @NamedSubgraph(name = "orderItems", attributeNodes = { 
            @NamedAttributeNode("item")
        })
    )
    @Entity
    @Talbe(name = "ORDERS")
    public class Order {...}
```

Order.withAll이라는 Namde 엔티티 그래프를 정의했다.

이 엔티티 그래프는 Order -> Member, Order -> OrderItem, OrderItem -> Item의 객체 그래프를 함께 조회한다.

```java
    Map hints = new HashMap();
    hints.put("javax.persistence.fetchgrah", em.getEntityGraph("Order.withAll"));

    Order order = em.find(Order.class, orderId, hints);
```

이렇게 하면 Order.withAll을 실행 한다.

#### JPQL에서 엔티티 그래프 사용

JPQL에서 엔티티 그래프를 사용하는 방법은 em.find()와 동일하게 힌트만 추가함녀 된다.

```java
    List<Order> resultList = 
        em.createQuery("select o from Order o where o.id = :orderId", Order.class)
        .setParameter("OrderId", orderId)
        .setHint("javax.persistence.fetchgraph", em.getEnetiyGraph("Order.withAll"))
        .getResultList();
```

#### 동적 엔티티 그래프

엔티티 그래프를 동적으로 구성하려면 createEntityGraph() 메소드를 사용하면 된다.

```java
    public <T> EntityGraph<T> createEntityGraph(Class<T> rootType);

    EntityGraph(Order) graph = em.createEntityGraph(Order.class);
    graph.addAttributeNodes("member");

    Map hints = new HashMap();
    hints.put("javax.persistence.fetchgraph", graph);

    Order order = em.find(Order.class , orderId, hints)
```

em.createEntityGraph(Order.class)를 사용해서 동적으로 엔티티 그래프를 만들었다. 그리고 graph.addAttributeNodes("member)를 사용해서 orer.member 속성을 엔티티 그래프에 포함했다.

```java
EntityGraph<Order> graph = em.createEntityGraph(Order.class);
graph.addAttributeNodes("member");
Subgraph<OrderItem> orderItem = graph.addSubgraph("orderItems");
orderItems.addAttributeNodes("Item");

Map hints = new HashMap();
hints.put("javax.persistence.fetchgraph", graph);

Order order = em.find(Order.class, orderId, hints);
```

graph.addSubgraph("orderItems") 메소드를 사용해서 서브그래프를 만들었다. 그리고 서브 그래프가 item 속성을 포함하도록 했다.

#### 엔티티 그래프 정리

* ROOT에서 시작

엔티티 그래프는 항상 조회하는 엔티티의 ROOT에서 시작해야 한다.

* 이미 로딩된 엔티티

영속성 컨텍스트에 해당 엔티티가 이미 로딩되어 있으면 엔티티 그래프가 적용되지 않는다.

* fetchgraph, loadgraph의 차이

fetchgraph는 엔티티 그래프에 선택한 속성만 함께 조회한다.

loadgraph는 엔티티 그래프에 선택한 속성뿐만 아니라 글로벌 fetch 모드가 FetchType.EAGER로 설정된 연관관계도 포함해서 함께 조회한다.

## 정리

* JPA가 지원하는 컬렉션의 종류와 특징들을 알아보았다.
* 컨버터를 사용하면 엔티티의 데이터를 변환해서 데이터베이스에 저장할 수 있다.
* 리스너를 사용하면 엔티티에서 발생한 이벤트를 받아서 처리할 수 있다.
* 페치 조인은 객체지향 쿼리를 사용해야 하지만 엔티티 그래프를 사용하면 객체지향 쿼리를 사용하지 않아도 원하는 객체 그래프를 한 번에 조회할 수 있다.
