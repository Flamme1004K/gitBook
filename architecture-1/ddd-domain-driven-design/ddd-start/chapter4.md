---
description: 이 글은 최범균 저자님의 DDD-START의 정리입니다.
---

# Chapter4 리포지터리와 모델 구현

## 리포지터리와 모델구현\(JPA 중심\)

* JPA를 이용한 리포지터리 기능 구현
* 엔티티와 밸류 매핑
* 밸류 컬렉션 매핑
* 애그리거트 로딩전략과 영속성 전파
* 식별자 생성기능

### JPA를 이용한 리포지터리 기능 구현

#### 레포지터리 기본 기능 구현

* 아이디로 애그리거트 조회하기
* 애그리거트 저장하기

### 매핑 구현

애그리거트와 JPA 매핑을 위한 기본 규칙은 다음과 같다.

* 애그리거트 루트는 엔티티이므로 @Entity로 매핑 설정한다.
* 한 테이블에 엔티티와 밸류 데이터가 같이 있다면
  * 밸류는 @Embeddable로 매핑 설정한다.
  * 밸류 타입 프로퍼티는 @Embedded로 매핑 설정 한다.

#### 기본 생성자

JPA의 @Entity와 @Embeddable로 클래스를 매핑하려면 기본 생성자를 제공해야 한다.

하이버네이트와 같은 JPA 프로바이더는 DB에서 데이터를 읽어와 매핑된 객체를 생성할 때 기본 생성자를 사용해서 객체를 생성한다.

이런 기술적인 제약으로 Receiver와 같은 불변 타입은 기본 생성자가 필요 없음에도 불구하고 다음과 같은 기본 생성자를 추가해야 한다.

즉 기본 생성자는 JPA 프로바이더가 객체를 생성할 때만 사용한다.

또한 기본 생성자를 다른 코드에서 사용하면 값이 없는 온전하지 못한 객체를 만들기 때문에 다른 코드에서 기본 생성자를 사용하지 못하도록 protected로 선언해야 한다.

#### 필드 접근 방식 사용

JPA는 필드와 메서드 두가지 방식으로 매핑 처리를 할 수 있다.

메서드 방식을 사용하려면 get/set 메서드를 구현하면 된다.

엔티티에 프로피터를 위한 공개 get/set 메서드를 추가하면 도메인의 의도가 사리지고 객체가 아닌 데이터 기반으로 엔티티를 구현할 가능성이 높아진다. set 메서드는 내부 데이터를 외부에서 변경할 수 있는 수단이 되기 때문에 캡슐화를 꺠는 원인이 될 수 있다.

엔티티가 객체로서 제 역할을 하려면 외부에 set 메서드 대신 의도가 잘 드러나는 기능을 제공해야 한다.

상태 변경을 위한 setState\(\) 메서드보다 주문 취소를 위한 cancel\(\) 메서드가 도메인을 더 잘 표현하고, setShoppingInfo\(\) 메서드보다 배송지를 변경한다는 의미를 갖는 changeShippingInfo\(\)가 도메인을 더 잘 표현한다.

밸류 타입을 불변으로 구현하고 싶은 경우 set 메서드 자체가 필요 없는데 JPA의 구현 방식 때문에 공개 set 메서드를 추가하는 것도 좋지 않다.

엔티티를 객체가 제공할 기능 중심으로 구현하도록 유도하려면 JPA 매핑 처리를 프로퍼티 방식이 아닌 필드 방식으로 선택해서 불필요한 get/set 메서드를 구현하지 말아야 한다.

#### AttributeConverter를 이용한 밸류 매핑 처리

int, long, String, LocalDate와 같은 타입은 DB 테이블의 한 개 컬럼과 매핑된다. 이와 비슷하게 밸류 타입의 프로퍼티를 한 개 칼럼에 매핑해야 할 때도 있다.

#### 밸류 컬렉션 : 별도 테이블 매핑

P.119

#### 밸류 컬렉션: 한 개 칼럼 매핑

P.122

#### 밸류를 이용한 아이디 매핑

식별자는 최종적으로 문자열이나 숫자와 같은 기본 타입이기 때문에 String 이나 Long타입을 이용해서 식별자를 매핑한다.

JPA에서 식별자 타입은 Serializable 타입이어야 하므로 식별자로 사용될 밸류 타입은 Serializable 인터페이스를 상속받아야 한다.

#### 별도 테이블에 저장하는 밸류 매핑

애그리거트에서 루트 엔티티를 뺀 나머지 구성요소는 대부분 밸류이다.

#### 밸류 컬렉션을 @Entity로 매핑하기

개념적으로 밸류인데 구현 기술의 한계나 팀 표준 때문에 @Entity를 사용해야 할 때도 있다.

상속 구조를 갖는 밸류 타입을 사용하려면 @Embeddable 대신 @Entity를 이용한 상속 매핑으로 처리해야 한다.

#### ID 참조와 테이블을 이용한 단방향 M:N aovld

134P

#### 애그리거트 로딩전략

JPA 매핑을 설정할 때 항상 기억해야 할 점은 애그리거트에 속한 객체가 모두 모여야 완전한 하나가 된다는 것이다.

애거리거트는 개념적으로 하나여야 한다.

1. 상태를 변경하는 기능을 실행할 때 애그리거트 상태가 완전해야 한다.
2. 표현 영역에서 애그리거트의 상태 정보를 보여줄 때 필요하기 때문이다.

### 애그리거트의 영속성 전파

애그리거트가 완전한 상태여야 한다는 것은 애그리거트 루트를 조회할 떄뿐만 아니라 저장하고 삭제할 때도 하나로 처리해야 함을 의미한다.

* 저장 메서드는 애그리거트 루트만 저장하면 안되고 애그리거트에 속한 모든 객체를 저장해야 한다.
* 삭제 메서드는 애그리거트 루트뿐만 아니라 애그리거트에 속한 모든 객체를 삭제해야 한다.

### 식별자 생성 기능

* 사용자 직접 생성
* 도메인 로직으로 생성
* DB를 이용한 일련번호 사용

