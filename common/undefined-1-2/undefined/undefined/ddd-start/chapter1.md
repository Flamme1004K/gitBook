---
description: 이 글은 최범균 저자님의 DDD-START의 정리입니다.
---

# Chapter1 도메인 모델의 시작

## 항목

* 도메인
* 도메인 모델
* 도메인 모델 패턴
* 도메인 모델 도출
* 엔티티와 밸류
* 도메인 용어

### 도메인

흔히 말해서 '온라인 서점' 이라는 큰 틀이 바로 도메인이다. 이 도메인 아래에는 주문, 회원, 배송, 결제, 정산 등으로 하위 도메인을 나눌 수 있다.

```java
 하위 도메인과 모델 
 도메인은 다수의 하위 도메인으로 구성된다. 각 하위 도메인이 다루는 영역은 서로 다르기 때문에 같은 용어라도 하위 도메인마다 의미가 달라질 수 있다.
 도메인에 따라 용어의 의미가 결정되므로, 여러 하위 도메인을 하나의 다이어그램에 모델링하면 안된다. 
 모델의 각 구성요소는 특정 도메인을 한정할 때 비로소 의미가 완전해지기 때문에, 각 하위 도메인마다 별도로 모델을 만들어야 한다. 즉 서로 다른 도메인 모델을 따로 만들어야 한다.
```

### 도메인 모델

도메인 모델에는 다양한 정의가 존재하는데 기본적으로 도메인 모델은 특정 도메인을 개념적으로 표현한 것이다.

주문 도메인은 상품 선택 --&gt; 배송지 입력 --&gt; 결제 순으로 흘러간다.

이 순서를 모델링 구성한 것이 바로 도메인 모델이다.

즉, 도메인 모델을 사용하면 여러 관계자들이 동일한 모습으로 도메인을 이해하고 도메인 지식을 공유하는 데 도움이 된다.

이러한 도메인을 이해하려면 도메인의 제공하는 기능과 도메인의 주요 데이터 구성을 파악해야 하는데, 이런 면에서 기능과 데이터를 함께 보여주는 객체 모델은 도메인을 모델링하기에 적합하다.

이러한 도메인 모델은 객체 모델로 만들 수도 있지만 상태 다이어그램, 클래스 다이어그램, UML 기법을 이용하여 모델링도 가능하다.

즉, 도메인 모델은 기본적으로 도메인 자체를 이해하기 위한 개념 모델이다. 개념 모델을 이용해서 바로 코드를 작성할 수 있는 것은 아니기에 구현 기술에 맞는 구현 모델이 따로 필요하다.

개념 모델과 구현 모델은 서로 다른 것이지만 구현 모델이 개념 모델을 최대한 따르도록 할 수 있다. 예를 들어 객체 기반 모델을 이용해서 도메인을 표현했다면 객체 지향 언어를 이용해서 개념 모델에 가깝게 구현할 수 있다.

```java
 개념 모델과 구현 모델
개념 모델은 순수하게 문제를 분석한 결과물이다. 개념모델은 데이터베이스, 트랜잭션 처리, 성능, 구현 기술과 같은 것들을 고려하고 있지 않기 때문에 실제 코드를 작성할 때 개념 모델을 있는 그대로 사용할 수 없다. 그래서 개념 모델을 구현 가능한 형태의 모델로 전환하는 과정을 거치게 된다.
 처음부터 완벽한 개념 모델을 만들기보다는 전반적인 개요를 알 수 있는 수준으로 개념모델을 작성해야 한다. 프로젝트 초기에는 개요 수준의 개념 모델로 도메인에 대한 전체 윤곽을 이해하는 데 집중하고, 구현하는 과정에서 개념 모델을 구현 모델로 점진적으로 발전시켜 나가야 한다.
```

### 도메인 모델 패턴

일반적인 애플리케이션의 아키텍쳐는 표현, 응용, 도메인, 인프라스트럭쳐로 네 개의 계층으로 구성 된다.

| 계층\(Layer\) | 설명 |
| :--- | :--- |
| 표현\(Presentation\) | 사용자의 요청을 처리하고 사용자에게 정보를 보여준다. 사용자는 소프트웨어를 사용하는 사람뿐만 아니라 외부 시스템도 사용자가 될 수 있다. |
| 응용\(Application\) | 사용자가 요청한 기능을 실행한다. 업무 로직을 직접 구현하지 않으면 도메인 계층을 조합해서 기능을 실행한다. |
| 도메인 | 시스템이 제공할 도메인의 규칙을 구현한다. |
| 인프라스트럭쳐\(infrastructure\) | 데이터베이스나 메시징 시스템과 같은 외부 시스템과의 연동을 처리한다. |

도메인 모델은 아키텍쳐상태의 도메인 계층을 객체 지향 기법으로 구현하는 패턴을 말한다.

도메인 계층은 도메인의 핵심 규칙을 구현한다.

예를들어 주문 도메인은 '출고 전에 배송지를 변경할 수 있다.'과 '주문취소는 배송 전에만 할 수 있다.' 라는 코드가 도메인 계층에 위치하게 된다. 이런 도메인 규칙을 객체 지향 기법으로 구현하는 패턴이 **도메인 모델 패턴**이다.

```java
 도메인 모델이란?
 도메인 자체를 표현하는 개념적인 모델을 의미하지만, 도메인 계층을 구현할 때 사용하는 객체 모델을 언급할 때에도 '도메인 모델'이란 용어를 사용한다.
 이 책에서도 도메인 계층의 객체 모델을 표현할 때 도메인 모델이라고 표현하고 있다.
```

### 도메인 모델 도출

도메인을 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요소, 규칙, 기능을 찾는 것이다.

```java
문서화
문서화를 하는 주된 이유는 지식을 공유하기 위함이다.
코드를 보면서 도메인을 깊게 이해하게 되므로 코드 자체도 문서화의 대상이 된다.
모데민 관점에서 코드가 도메인을 잘 표현해야 비로소 코드의 가독서잉 높아지며 문서로서 코드가 의미를 갖는다.
```

### 엔티티와 벨류

도출한 모델은 크게 엔티티\(Entity\)와 밸류\(Value\)로 구분된다.

#### 엔티티

엔티티의 가장 큰 특징은 식별자를 갖는다는 것이다. 1. 식별자는 엔티티 객체마다 고유해서 각 엔티티는 서로 다른 식별자를 갖는다. 2. 엔티티를 생성하고 엔티티의 속성을 바꾸고 엔티티를 삭제할 때까지 식별자는 유지된다. 3. 엔티티의 식별자는 바뀌지 않고 고유하기 때문에 두 엔티티 객체의 식별자가 같으면 두 엔티티는 같다고 판단할 수 있다.

**엔티티의 식별자 생성**

엔티티의 식별자를 생성하는 시점은 도메인의 특정과 사용하는 기술에 따라 달리진다. 1. 특정 규칙에 따라 생성 2. UUID 사용 3. 값을 직접 입력 4. 일련번호 사용\(시퀀스나 DB의 자동 증가 칼럼 사용\)

식별자를 생성할 떄 주의 점은 같은 시간에 동시에 식별자를 생성할 때 같은 식별자가 만들어지면 안된다.

#### 밸류

밸류 타입은 개념적으로 완전한 하나를 표현할 때 사용한다. 밸류타입을 불변으로 구현해야한다. 그 이유는 불변 탑입을 사용하면 보다 안전한 코드를 작성할 수 있기 때문이다.

엔티티 탑입의 두객체가 같은지 비교할 떄 주로 식별자를 사용한다면 두 밸류 객체가 같은지 비교할 때는 모든 속성이 같은지 비교해야 한다.

#### 엔티티 식별자와 밸류 타입

#### 도메인 모델에 set 메서드 넣지 않기

도메인 모델에 get/set 메서드를 무조건 추가하는 것은 좋지 않은 버릇이다.

도메인 객체가 불완전한 상태로 사용되는 것을 막으려면 생성 시점에 필요한 것을 전달해 주어야 한다. 즉, 생성자를 통해 필요한 데이터를 모두 받아야 한다.

생성자를 필요한 것을 모두 받으므로 생성자를 호출하는 시점에 필요한 데이터가 올바른지 검사할 수 있다.\( bottle neck 현상을 방지해야 한다.\)

불변 밸류 타입을 사용하면 자연스럽게 밸류 타입에는 set 메서드를 구현하지 않는다. set 메서드를 구현해야 할 특별한 이유가 없다면 불변 타입의 장점을 살릴 수 있도록 밸류 타입은 불변으로 구현한다.

### 도메인 용어

코드를 작성할 때 도메인에서 사용하는 용어는 매우 중요하다.

코드를 도메인 용어로 해석하거나 도메인 용어를 코드로 해석하는 과정이 줄어든다. 이는 코드의 가독성을 높여서 코드를 분석하고 이해하는 시간을 시간을 절약한다. 도메인 용어를 사용해서 최대한 도메인 규칙을 코드로 작성하게 되므로 버그도 줄어들게 된다.

