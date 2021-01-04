---
description: 이 글은 최범균 저자님의 DDD-START의 정리입니다.
---

# Chapter3 애그리거트

## 항목

* 애그리거트
* 애그리거트 루트와 역할
* 애그리거트와 리포지토리
* ID를 이용한 애그리거트 참조

### 애그리거트

도메인 객체 모델의 복잡도 : 개별 구성요소 위주로 모델을 이해하게 되고 전반적인 구조나 큰 수준에서 도메인 간의 관계를 파악하기 어려워진다.

이 문제점은 코드를 변경하고 확장하는 것이 어려워진다는 것을 의미한다. 그리고 추가 요구사항에 대해서 회피하는 쪽으로 요구사항을 협의하게 된다.

복잡한 도메인을 이해하고 관리하기 쉬운 단위로 만들려면 상위 수준에서 모델을 조망할 수 있는 방법이 필요한데 그것이 바로 애그리거트이다.

즉, 수많은 객체를 애그리거트로 묶어서 바라보면 좀 더 상위 수준에서 도메인 모델 간의 관계를 파악할 수 있다.

애그리거트는 모델을 이해하는 데 도움을 줄 뿐만 아니라 일관성을 관리하는 기준이 된다.

모델을 보다 잘 이해할 수 있고 애그리거트 단위로 일관성을 관리하기 때문에 애그리거트 단위로 일관성을 관리하기 때문에 애그리거트는 복잡한 도메인을 단순한 구조로 만들어 준다. 복잡도가 낮아지는 만큼 도메인 기능을 확장하고 변경하는 데 필요한 노력\(개발 시간\)도 줄어든다.

애그리거트는 관련된 모델을 하나로 모은 것이기 때문에 한 애그리거트에 속한 객체는 유사하거나 동일한 라이프사이클을 갖는다.

그리고 한 애그리거트에 속한 객체는 다른 애그리거트에 속하지 않는다. 애그리거트는 독립된 객체군이며, 각 애그리거트는 자기 자신을 관리할 뿐 다른 애그리거트를 관리하지 않는다.

경계를 설정할 때 기본이 되는 것은 도메인 규칙과 요구사항이다. 도메인 규칙에 따라 함께 생성되는 구성요소는 한 애그리거트에 속할 가능성이 높다. 함께 변경되는 빈도가 높은 객체는 한 애그리거트에 속할 가능성이 높다.

처음 도메인 모델을 만들기 시작하면 큰 애그리거트로 보이는 것들이 많지만 도메인에 대한 경험이 생기고 도메인 규칙을 제대로 이해할수록 실제 애그리거트의 크기는 줄어들게 된다.

**애그리거트가 한 개의 엔티티 객체만 갖는 경우가 많으며 두개 이상의 엔티티로 구성되는 애그리거트는 드물게 존재한다.**

### 애그리거트 루트

애거리거트에 속한 모든 객체가 일관된 상태를 유지하려면 애그리거트 전체를 관리할 주체가 필요한데 이 책임을 지는 것이 바로 애그리거트의 루트 엔티티이다.

애그리거트 루트 엔티티는 애그리거트의 대표 엔티티로 애그리거트에 속한 객체는 애그리거트 루트 엔티티에 직접 또는 간접적으로 속한다.

### 도메인 규칙과 일관성

애그리거트 루트가 단순히 애그리거트에 속한 객체를 포함하는 것으로 끝나는 것은 아니다. 애그리거트 루트의 핵심 역할은 애그리거트의 일관성을 깨지지 않도록 하는 것이다.

이를 위해 애그리거트 루트는 애그리거트가 제공해야 할 도메인 기능을 구현한다.

애그리거트 루트가 아닌 다른 객체가 애그리거트에 속한 객체를 직접 변경하면 안된다. 이는 애그리거트 루트가 강제하는 규칙을 적용할 수 없어 모델의 일관성을 깨는 원인이 된다.

애그리거트 루트를 통해서만 도메인 로직을 구현하게 만들려면 도메인 모델에 대해 다음의 두가지를 습관적으로 적용해야한다. 1. 단순히 필드를 변경하는 set 메서드를 공개\(public\) 범위로 만들지 않는다. 2. 밸류 타입은 불변으로 구현한다.

#### 공개\(public\) SET

* 공개 set메서드는 중요 도메인의 의미나 의도를 포현하지 못하고 도메인 로직이 도메인 객체가 아닌 응용 영역이나 포현 영역으로 분산되게 만드는 원인이 된다.
* 도메인 로직이 한곳에 응집되어 있지 않게 되므로 코드를 유지보수할 때에도 분석하고 수정하는 데 더 많은 시간을 들이게 된다.
* 도메인 모델의 엔티티나 밸류에 공개 set 메서드만 넣지 않아도 일관성이 깨질가능성이 줄어든다.
* 공개 set 메서드만 사용하지 않아도 메서드를 사용해서 구현할 가능성이 높아진다.

#### 밸류 타입을 불변으로 구현

* 밸류 객체의 값을 변경할 수 없으면 애그리거트 루트에서 밸류 객체를 구해도 값을 변경할 수 없기 때문에 애그리거트 외부에서 밸류 객체의 상태를 변경할 수 없게 된다.
* 애그리거트 외부에서 내부 상태를 바꾸지 못하므로 애그리거트의 일관성이 깨질 가능성이 줄어든다.
* 밸류 객체가 불변이면 밸류 객체의 값을 변경하는 방법은 새로운 밸류 객체를 생성하는 것이다.
* 밸류 타입의 내부 상태를 변경하려면 애그리거트 루트를 통해서만 가능하다.

#### 결과

애그리거트 루트가 도메인 규칙을 올바르게만 구현하면 애그리거트 전체의 일관성을 올바르게 유지할 수 있다.

### 애그리거트 루트의 기능 구현

애그리거트 루트는 애그리거트 내부의 다른 객체를 조합해서 기능을 완성한다.

### 트랜잭션 범위

트랜잭션 범위는 작을수록 좋다.

DB테이블을 기준으로 한 트랜잭션이 한 개테이블을 수정하는 것과 세 개의 테이블을 수정하는 것은 성능에서 차이가 발생한다.

동일하게 한 트랜잭션에서는 한 개의 애그리거트만 수정해야 한다.

한 트랜잭션에서 두 개 이상의 애그리거트를 수정하면 트랜잭션 충돌이 발생할 가능성이 더 높아지기 때문에 한번에 수정하는 애그리거트 개수가 많아질수록 전체 처리량이 떨어지게 된다.

한 트랜잭션에서 한 애그리거트만 수정한다는 것은 애그리거트에서 다른 애그리거트를 변경하지 않는다는 것을 뜻한다.

애그리거트는 서로 최대한 독립적이어야 하는데 한 애그리거트가 다른 애그리거트의 기능에 의존하기 시작하면 애그리거트 간 결합도가 높아지게 된다.

결합도가 높아지면 높아질수록 향후 수정 비용이 증가하므로 애그리거트에서 다른 애그리거트의 상태를 변경하지 말아야 한다.

두개 이상의 애그리거트를 수정해야 한다면 애그리거트에서 다른 애그리거트를 직접 수정하지 말고 응용 서비스에서 두 애그리거트를 수정핟록 구현해야 한다.

도메인 이벤트를 사용하면 한 트랜잭션에서 한 개의 애그리거트를 수정하면서도 동기나 비동기로 다른 애그리거트의 상태를 변경하는 코드를 작성할 수 있다.

#### 한 트랜잭션에서 두 개 이상의 애그리거트를 변경하는 것을 고려할 수 있는 상황

* 팀 표준 : 팀이나 조직의 표준에 따라 사용자 유스케이스와 관련된 응용서비스의 기능을 한 트랜잭션으로 실행해야 하는 경우
* 기술 제약 : 한 트랜잭션에서 두 개 이상의 애그리거트를 수정하는 대신 도메인 이벤트와 비동기를 사용하는 방식을 사용하는데, 기술적으로 이벤트 방식을 도입할 수 없는 경우
* UI 구현의 편리 : 운영자의 편리함을 위해 주문 목록 화면에서 여러 주문의 상태를 한 번에 변경하고 싶을때

### 리포지터리와 애그리거트

애그리거트는 개념상 완전한 한 개의 도메인 모델을 표현하므로 객체의 영속성을 처리하는 리포지터리는 애그리거트 단위로 존재한다.

새로운 애그리거트를 만들면 저장소에 애그리거트를 영속화하고 애그리거트를 사용하려면 저장소에서 애그리거트를 읽어야 하므로 리포지터리는 적어도 다음 두 메서드를 제공해야 한다.

* save : 애그리거트저장
* findById : ID로 애그리거트를 구함

애그리거트는 개념적으로 하나이므로 리포지터리는 애그리거트 전체를 저장소에 영속화해야 한다.

### ID를 이용한 애그리거트 참조

한 객체가 다른 객체를 참조하는 것처럼 애그리거트도 다른 애그리거트를 참조한다. 애그리거트의 관리 주체가 애그리거트 루트이므로 애그리거트에서다른 애그리거트를 참조한다는 것은 애그리거트의 루트를 참조한다는 것과 같다.

애그리거트 간의 참조는 필드를 통해 쉽게 구현할 수 있다.

1. 애그리거트를 직접 참조할 때 발생할 수 있는 가장 큰 문제는 편리함을 오용할 수 있다는 것이다. 한 애그리거트 내부에서 다른 애그리거트 객체에 접근할 수 있으면 다른 애그리거트의 상태를 쉽게 변경할 수 있게 된다.
2. 애그리거트를 직접 참조하면 성능과 관련된 여러가지 고민을 해야한다.
3. 확장의 문제점이 있다.

이런 세가지 문제를 완화할 때 사용할 수 있는 것이 ID를 이용해서 다른 애그리거트를 참조하는 것이다.

ID를 이용한 참조는 DB테이블에서의 외래키를 사용해서 참조하는 것과 비슷하게 다른 애그리거트를 참조할 때 ID 참조를 사용한다는 점이다.

ID 참조를 사용하면 모든 객체가 참조로 연결되지 않고 한 애그리거트에 속한 객체들만 참조로 연결된다. 이는 애그리거트의 경계를 명확히 하고 애그리거트 간 물리적인 연결을 제거하기 때문에 모델의 복잡도를 낮춰준다. 또한 애그리거트 간의 의존을 제거하므로 응집도를 높여주는 효과도 있다.

구현 복잡도가 낮아진다. 다른 애그리거트를 직접 참조하지 않으므로 애그리거트간 참조를 지연 로딩으로 할지 즉시 로딩으로 할지 고민하지 않아도 된다. 참조하는 애그리거트가 필요하면 응용서비스에서 아이디를 이용해서 로딩하면 된다.

#### ID를 이용한 참조와 조회 성능

다른 애그리거트를 ID로 참조하면 참조하는 여러 애그리거트를 읽어야 할 때 조회 속도가 문제될 수 있다. 이때 N+1 문제가 발생할 수 있는데 이를 해결하려면 JPQL 및 지연로딩을 사용하면 된다.

### 애그리거트 간 집합 연관

애그리거트 간 1:N 관계는 Set과 같은 컬렉션을 이용해서 표현할 수 있을 것이다.

### 애그리거트를 팩토리로 사용하기
