---
description: 이 글은 스프링 5.0 마이크로 서비스  책(라제시 RV)을 보고 쓴 흔적입니다.
---

# SpringWebFlux란?

## Spring WebFlux?

Spring WebFlux를 알기전에 미리 reactive Programming에 대하여 알아야 한다.

### Reactive Programming이란?

Reactive Stream 명세에 바탕을 두고 있다.

Reactive Stream 명세는 따로 떨어져 있는 컴포넌트 사이의 async stream 처리나 event Flow을 Non-Blocking 방식으로 처리하기 위한 문법을 정리한다.

#### Non-Blocking 방식이란 ?

**I/O \(Input / Output\) 작업이 진행되는 동안 user process의 작업을 중단시키지 않는 방법이다. 이 경우 I/O의 진행시관과 관계가 없기 때문에 어플리케이션에서 작업을 오랜 시간 중지하지 않고도 I/O 작업을 진행할 수 있다. 그러나 반복적으로 시스템 호출이 발생하기 때문에 자원 낭비가 될 수 있다.**

다시 원래대로 돌아와 Reactive Stream에는 sequence의 처리, 완료 알림, 실패 시 배압\(backpressure\) 적용 등이 추가 된다.

#### Backpressure이란?

**배압이 지원되면 받는 쪽에서는 보내는 쪽에 얼마만큼의 데이터를 소화할 수 있는지 알릴 수 있다. 받는 쪽에서는 데이터가 처리될 준비가 됐을 때만 데이터를 받을 수 있다. Reactive Stream은 서로 다른 컴포넌트 사이에 서로 다른 스레드 풀을 다뤄야 할 때나 느린 컴포넌트와 빠른 컴포넌트를 통합할 때 많은 도움을 준다.**

이렇게 Reactive Programming 원칙은 Spring framework 5의 webFlux에 들어있다.

**즉, webFlux에서 callBack Method 를 사용하지 않고도 async, Non-Blocking Http server와 완전한 backpressure를 사용할 수 있다.**

그리고 reactive programming의 진정한 힘을 제대로 사용하려면 reactive 구조체가 클라이언트에서 뒷단의 repository까지 전 구간을 흐를 수 있어야 한다.

### Spring WebFlux로 SpringBoot Application 구현

1. @Controller Annotation 사용.
2. Java8 Functional Programming 사용

### Reactive Stream의 이해

#### 발행자\(Publisher\)

데이터의 소스를 갖고 있으며, 구독자\(Subscriber\)의 요청이 오면 데이터를 발행한다. --&gt; 이때 subscribe는 등록만을 담당할 뿐이며, 어떤 결과도 반환하지 않는다.

#### 구독자\(Subscriber\)

데이터 스트림을 소비하기 위해 발행자를 구독한다. 여러가지 이벤트에 대응하는 여러 메소드를 가지고 있다.

구독자의 모든 메소드는 콜백 등록, 메소드 그 자체는 데이터에 대해 아무런 반응도 하지 않는다.

#### 구독\(Subscription\)

단 하나의 발행자와 단 하나의 구독자를 연결해주며, 그 둘 사에어사만 공유되고, 그 둘 사이의 데이터를 교환을 중재한다.

#### 프로세서\(Processor\)

처리 단계를 나타내며 발행자 인터페이스와 구독자 인터페이스를 모두 상속한다. 프로세서는 발행자와 구독자 사이의 계약을 반드시 준수해야하며 발행자와 구독자를 연결해서 체이닝\(chaining\) 할 수 있다.

#### Mono, Flux

리액터에는 발행자 프로세서로 사용되는 두 개의 구현체\(Mono, Flux\)가 있다.

* Mono : 0 또는 1개의 이벤트만 발행
* Flux : 0 또는 N개의 이벤트를 발행
  * 플럭스는 다수의 데이터 요소 또는 값의 리스트를 스트림으로 전송할 때 사용한다.

**RabbitMQ, Kafka**

발행자와 구독자 사이에 메시지 queue에 대한 처리를 하기 위함.

{% embed url="https://devahea.github.io/2019/04/21/Spring-WebFlux%EB%8A%94-%EC%96%B4%EB%96%BB%EA%B2%8C-%EC%A0%81%EC%9D%80-%EB%A6%AC%EC%86%8C%EC%8A%A4%EB%A1%9C-%EB%A7%8E%EC%9D%80-%ED%8A%B8%EB%9E%98%ED%94%BD%EC%9D%84-%EA%B0%90%EB%8B%B9%ED%95%A0%EA%B9%8C/" %}

[https://www.slideshare.net/arawnkr/reactive-web-servlet-async-nonblocking-io-73838876](https://www.slideshare.net/arawnkr/reactive-web-servlet-async-nonblocking-io-73838876)



