---
description: 이 글은 김영한님의 jpa책을 보고 공부한 흔적입니다.
---

# Chap9

## JPA-Cha9 값 타입

JPA의 데이터 타입을 가장 크게 분류하면 엔티티 타입과 값 타입으로 나눌 수 있다.

값 타입은 다음 3가지로 나눌 수 있다.

* 기본값 타입
  * 자바 기본 타입\(int, double\)
  * 래퍼 클래스 \(Integer\)
  * String
* 임베디드 타입\(embedded type\)
* 컬렉션 값 타입\(collection value type\)

### 기본값 타입

```java
@Entity
@Data
public class Member {
    @Id @GeneratedValue
    private Long id;
    private String name;
    private int age;
}
```

Member에서의 String, int는 값 타입이다.

### 임베디드 타입\(복합 값 타입\)

임베디드 타입이란?

JPA에서는 새로운 값 타입을 직접 정의해서 사용하는 것.

@Embaddable과 @Embedded가 있다. 둘다 기본 생성자가 필수이다.

* @Embeddable : 값 타입을 정의하는 곳에 표시
* @Embedded : 값 타입을 사용하는 곳에 표시

```java
@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;
    private String name;

    @Temporal(TemporalType.DATE) java.util.Date startDate;
    @Temporal(TemporalType.DATE) java.util.Date endDate;

    private String city;
    private String street;
    private String zipcode;


}
```

```java
@Entity
public class Member { 

    @Id @GenratedValue
    private Long id;
    private String name;

    @Embedded Period workPerod;
    @Embedded Address homeAddress;


}


@Embeddable
public class Period { 

    @Temporal(TemporalType.DATE) java.util.Date startDate;
    @Temporal(TemporalType.DATE) java.util.Date endDate;

}

@Embeddable
public class Address { 

    @Column(name="city")
    private String city;
    private String street;
    private String zipcode;
}
```

임베디드 타입은 엔티티의 값일 뿐이다. 값이 속한 엔티티의 테이블에 매핑한다.

임베디드 타입을 사용하기 전과 후에 매핑하는 테이블이 같다.

즉, 임베디드 타입 덕분에 객체와 테이블을 아주 세밀하게 매핑하는 것이 가능하다.

결론적으로 잘 설계한 ORM 애플리케이션은 매핑한 테이블의 수보다 클래스의 수가 더 많다.

```java
@Entity
public class Member {

    @Embedded Address address;
    @Embedded PhoneNumber phoneNumber;

}

@Embeddable
public class Address { 

    private String city;
    private String street;
    private String zipcode;
    @Embedded Zipcode zipcode;

}

@Embbedable
public class Zipcode { 

    String zip;
    String plusFour;

}

@Embbedable
public class PhoneNumber { 

    String areaCode;
    String localNumber;
    @ManyToOne PhoneServiceProvider provider

}

@Entity
public class PhoneServiceProvider {

    @Id String name;
}
```

### @AttributeOverride : 속성 재정의

```java
@Entity
public class Member { 

    @Id @GeneratedValue
    private Long id;
    private String name;

    @Embedded Address homeAddress;
    @Embedded Address companyAddress;

}
```

```java
@Entity
public class Member { 

    @Id @GeneratedValue
    private Long id;
    private String name;

    @Embedded Address homeAddress;


    @Embedded 
    @AttributeOverrides({
        @AttributeOverride(name="city", column=@Column(name = "COMPANY_CITY")),
        @AttributeOverride(name="street", column=@Column(name = "COMPANY_STREET")),
        @AttributeOverride(name="zipcode", column=@Column(name = "COMPANY_ZIPCODE"))
})
    Address companyAddress;

}
```

임베디드 타입에 정희한 매핑정보를 재정의할 때 @AttributeOverride를 쓴다.

하지만 너무 많이 사용하면 엔티티코드가 지저분해진다는 단점이 있다.

### 임베디드 타입과 null

임베디드 타입이 null이면 메ㅐ핑한 컬럼 값은 모두 null이 된다.

```java
    member.setAddress(null);
    em.persist(member);
```

회원 테이블의 주소와 관련된 CITY, STREET, ZIPCODE 컬럼 값이 모두 null이 된다.

### 값 타입과 불변 객체

#### 값 타입의 공유 참조

임베디드 타입 같은 값 타입을 여러 엔티티에서 공유하면 위험하다.

```java
    member1.setHomeAddress(new Address("oldCity"));
    Address address = member1.getHomeAddress();

    address.setCity("NewCity");
    member2.setHomeAddress(address);
```

member1과 member2의 도시는 모두 NewCity로 들어간다.

이것은 바로 공유 참조에 의해 일어난 것이다.

자바의 기본타입은 값을 대입하는 것만으로도 값이 복사되지만, 임베디트 타입처럼 직접 정의한 값 타입은 객체 타입이다.

객체 타입은 값을 대입하면 항상 참조 값을 전달하기 때문에 `address.setCity("NewCity")` 처럼 회원1의 address 값을 공유해서 사용 했을때 결과로 둘다 City가 NewCity로 되는것을 볼 수 있다.

어떻게 해결해야할까?

책에서는 두가지 방법을 추천한다.

1. Clone을 만드는 방법
2. setter메소드를 모두 제거해서 객체의 값을 수정하지 못하게 만드는 방법, 즉 불변 객체를 만드는 방법이다.

#### Clone을 만드는 방법

#### 불변 객체

불변 객체란? 한번 만들면 절대 변경할 수 없는 객체.

왜 만들까? 객체를 불변하게 만들면 값을 수정할 수 없으므로 부작용을 원천 차단할 수 있기 떄문이다.

어떻게 만들까?

```java
@Embeddable
public class Address { 

    private String city;

    protected Address() {}

    public Address(String city){this.city=city}

    public String getCity() {
        return city;
    }

    //setter는 만들지 않는다.

}
```

간단한 방법으로 생성자로만 값을 설정하고 수정자를 만들지 않으면 된다.

#### 값 타입의 비교

* 동일성\(Identity\) 비교 : 인스턴스의 참조 값을 비교, == 사용
* 동등성\(Equivalence\) 비교 : 인스턴스의 값을 비교, equals\(\)사용

### 값 타입 컬렉션

@ElementCollection, @CollectionTable을 쓰면 된다.

```java
@Entity
public class Member { 

    @Id @GenratedValue
    private Long id;

    @Embedded Address homeAddress;

    @ElementCollection
    @CollectionTalbe(name = "FAVORITE_FOODS", 
      joinColumns = @JoinColumn(name = "MEMBER_ID"))
    @Column(name ="FOOD_NAME")
    private Set<String> favoriteFoods = new HashSet<String>;

    @ElementCollection
    @CollectionTalbe(name = "ADDRESS", 
       joinColumns = @JoinColumn(name = "MEMBER_ID"))
    private List<Address> addressHistory = new ArrayList<Address>();


}


@Embeddable
public class Address { 

    @Column
    private String city;
    private String street;
    private String zipcode;
}
```

```java
Member member = new Member();

member.setHomeAddress(new Address("통영", "몽돌해수욕장", "660-123"));

member.getFavoriteFoods().add("짬뽕");
member.getFavoriteFoods().add("짜장");
member.getFavoriteFoods().add("탕수육");

member.getAddressHistory().add(new Address("서울", "강남", "660-123"));
member.getAddressHistory().add(new Address("서울", "강북", "660-123"));

em.persist(member);
```

```java
Member member = em.find(Member.class, 1L);

Address homeAddress = member.getHomeAddress();

Set<String> favoriteFoods = member.getFavoriteFppds();

for (String favoriteFood : favoriteFoods) {
    System.out.println("favoriteFood = " + favoriteFood );
}

List<Address> addressHistory = member.getAddressHistory();

addressHistory.get(0);
```

```java
    Member member = em.find(Member.class, 1L);

    member.setHomeAddress(new Address("새로운도시","신도시", "123456"));

    Set<String> favoritFoods = member.getFavoritFoods();
    favoriteFoods.remove("탕수육");
    favoriteFoods.add("치킨");

    List<Address> addressHistory = member.getAddressHistory();
    addressHistory.remove(new Address("서울","기존주소", "123456"));
    addressHistory.setHomeAddress(new Address("새로운도시","신도시", "123456"));
```

### 값 타입 컬렉션의 제약사항

엔티티는 식별자가 있으므로 엔티티의 값을 변경해도 식별자로 데이터베이스에 저장된 원본 데이터를 쉽게 찾아서 변경할 수있다.

반면에 값 타입은 식별자라는 개념이 없고 단순한 값들의 모음이므로 값을 변경해버리면 데이터베이스에 저장된 원본 데이터를 찾기 어렵다.

값이 변경되면 컬렉션이 남아있는 상황이 많은데 이것을 해결하기 위해서는

값 타입 컬렉션이 매핑된 테이블에 데이터가 많다면 값 타입 컬렉션 대신에 일대다 관계를 고려하기도 한다.

일대다 매핑에 영속성 전이\(Cascade\) + 고아 객체 제거\(ORPHAN REMOVE\) 기능을 적용하여 쓸 수도 있다.

### 정리

엔티티 타입과 값 타입의 특징은 다음과 같다.

엔티티 타입의 특징

* 식별자가 있다.
  * 엔티티 타입은 식별자가 있고 식별자로 구별할 수 있다.
* 생명 주기가 있다.
  * 생성하고, 영속화하고, 소멸하는 생명 주기가 있다.
  * em.persist\(entity\)로 영속화 한다.
  * em.remove\(entity\)로 제거한다.
* 공유할 수 있다.
  * 참조 값을 공유할 수 있따. 이것을 공유 참조라 한다.
  * 예를 들어 회원 엔티티가 있다면 다른 엔티티에서 얼마든지 회원 엔티티를 참조 할 수 있다.

값 타입의 특징

* 식별자가 없다.
* 생명 주익를 엔티티에 의존한다.
  * 스스로 생명주기를 가지지 않고 엔티티에 의존한다. 의존하는 엔티티를 제거하면 같이 제거된다.
* 공유하지 않는 것이 안전하다.
  * 엔티티 타입과는 다르게 공유하지 않는 것이 안전하다. 대신에 값을 복사해서 사용해야 한다.
  * 오직 하나의 주인만이 관리해야 한다.
  * 불변 객체로 만드는 것이 안전하다.

