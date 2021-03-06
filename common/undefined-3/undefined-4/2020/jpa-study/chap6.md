---
description: 이 글은 김영한님의 jpa책을 보고 공부한 흔적입니다.
---

# Chap6

## JPA-Cha6 다양한 연관관계 매핑

엔티티의 연관관계를 매핑 할 때

* 다중성 : 다대일, 일대다, 일대일, 다대다

  보통 다대일과 일대다 관계를 가장 많이 사용

* 단방향, 양방향

  객체 관계에서 한 쪽만 참조하는 것을 단뱡항 관계, 양쪽이 서로 참조하는 것을 양방향.

* 연관관계의 주인 

외래키를 가진 테이블과 매핑한 엔티티가 외래키를 관리하는 게 효율적이므로 보통 이곳을 연관관계의 주인을 선택한다. 주인이 아닌 방향은 외래 키를 변경할 수 없고 읽기만 가능하다.

연관관계의 주인은 mappedBy 속성을 사용하지 않는다. 연관관계의 주인이 아니면 mappedBy 속성을 사용하고 연관관계의 주인 필드, 이름을 값으로 입력해야 한다.

1. 두 엔티티가 일대일 관계인지 일대다 관계인지 다중성을 고려
2. 두 엔티티 중 한쪽만 참조하는 단방향 관계인지 서로 참조하는 양방향 관계인지 고려
3. 양방향 관계면 연관관계의 주인을 정해야 한다.

   **다대일**

데이터 베이스의 테이블의 일, 다 관계에서 외래키는 항상 다쪽에 있다.

객체 양방향 관계에서 연관관계의 주인은 항상 다쪽이다.

양방향은 외래 키가 있는 쪽이 연관관계의 주인이다.

양방향 연관관계는 항상 서로를 참조해야 한다.

### 일대다

일대다 관계는 다대일 관계의 반대 방향이다. 일대다 관계는 엔티티를 하나 이상 참조할 수 있으므로 자바 컬렉션인 Collection, List, Set, Map 중에 하나를 사용해야 한다.

일대다 단방향 매핑보다는 다대일 양방향 매필을 사용하는 것이 좋다.

일대다 양방향 매핑은 존재하지 않는다. 대신 다대일 양방향 매핑을 사용해야 한다.

### 일대일

일대일 관계는 양쪽이 서로 하나의 관계만 가진다.

* 일대일 관계는 그 반대도 일대일 관계다.
* 테이블 관계에서 일대다, 다대일은 항상 다쪽이 외래 키를 가진다. 반면에 일대일 관계는 주 테이블이나 대상 테이블 둘 중 어느 곳이나 외래 키를 가질 수 있다.

주 테이블에 외래키, 대상 테이블에 외래키로 둘 중에 누가 외래 키를 가질지 선택해야 한다.

* 주 테이블에 외래 키
  * 일대일 관계를 구성할 때 객체지향 개발자들은 주 테이블에 외래 키가 있는 것을 선호한다.
  * JPA도 주 테이블에 외래 키가 있으면 좀 더 편리하게 매핑할 수 있다.
* 대상 테이블에 외래 키

### 다대다

관계형 데이터베이스는 정규화된 테이블 2개로 다대다 관계를 표현할 수 없다. 그래서 보통 다대다 관계를 일대다, 다대일 관계로 풀어내는 연결 테이블을 사용한다.

[https://www.youtube.com/watch?v=brE0tYOV9jQ&t=137s](https://www.youtube.com/watch?v=brE0tYOV9jQ&t=137s)

?? 리스트와 셋의 차이? 기능? @orderBy

JPA @orderBy는 잘 안먹음

list set을 상황에 맞춰써야한다.

편의 메소드는 무엇인가?

