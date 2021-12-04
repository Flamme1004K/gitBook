---
description: 최범균님의 스프링 4.0 책을 쓰고 정리한 글입니다.
---

# SpringAOP

Spring에 대한 두 번째 포스팅이다. 요번에는 AOP에 대하여 써보려고 한다.

**AOP는 무엇일까? 일단 AOP는 Aspect Oriented Programming의 약자이다.**

항상 비즈니스 로직을 구현하다 보면 공통 기능이 하나씩 나올 것이다. 이 공통기능을 한 곳에다가 빼내어 필요한 코드에 넣는 작업을 쉽게 하기 위하여 그리고 코드의 중복을 지우기 위하여 사용하는 것이 바로 AOP이다.

**AOP 용어**

&#x20;\- JoinPoint : Advice를 적용 가능한 지점을 의미, 메서드 호출, 필드 값 변경&#x20;

&#x20;\- Pointcut  : Joinpoint의 부분 집합으로서 실제로 Advice가 적용되는 Joinpoint를 나타냄. Spring에서는 AspectJ문법을 사용하여 정의

&#x20;\- Advice  : 공통 관심 기능을 핵심 로직에 적용 할 지 정의 ex) 트랜잭션

&#x20;\- Weaving : Advice를 핵심 로직 코드에 적용하는 것&#x20;

&#x20;\- Aspect : 여러 객체에 공통으로 적용되는 기능, 트랜잭션이나 보안등이 Aspect의 예

**Weaving 방식**&#x20;

\- 컴파일 시 weaving

\- 클래스 로딩 시 weaving

\- 런타임 시 weaving

**Spirng에서 AOP**

&#x20;스프링에서 AOP는 어떻게 쓰일까? 스프링 자체에서는 Proxy기반의 AOP를 지원하고 있다. 즉 JointPoint만 사용할 수 있고, 우리는 이것을 커스텀하기 위하여 AspectJ를 써야 한다고 한다. (스프링은 XML, AspectJ에서의 @Aspect 어노테이션, 스프링 API를 이용한 AOP 구현을 할 수 있다.)

&#x20;여기서 일반적으로 AspectJ나 xml을 사용하고 프록시 기반이기 때문에 CGLIB 클래스를 사용하여 객체를 만든다고 한다. 하지만 클래스가 final인 경우 프록시를 생성하지 못하여 AOP를 적용하지 못한다고 한다.

스프링에서 구현 가능한 Advice 종류&#x20;

Before Advice(대상 객체의 메서드 호출 전에 공통 기능 실행)

After Returning Advice(대상 객체의 메서드가 익셉션 없이 실행된 이후 공통기능 수행)

After Throwing Advice(익셉션이 발생한 경우에 공통기능 실행)

After Advice( 익셉션 발생 여부 없이 공통기능 실행 )

Around Advice(실행 전 후 익셉션 발생 시점에 공통기능 실행)

&#x20;이 중에서 Around Advice를 가장 범용적으로 쓰이는데 이 이유는 캐시 기능 ,성능 모니터링 기능을 추가하여 실행 전/후에 동작 할 수 있기 때문이라고 한다.

**XML AOP 태그 정보**&#x20;

&#x20;\- aop:config : AOP 설정 정보임

&#x20;\- aop:aspect : aspect를 설정 (id와 ref(빈Id))

&#x20;\- aop:pointcut : pointcut 설정

&#x20;\- aop:around : Around Advice 설정(aop:before, aop:after-returning, aop:after-throwing, aop:after, aop:around)

**JAVA AOP**

@Aspect 어노테이션 사용시 xml에는 항상 \<aop:aspectj-autoproxy/> 설정을 하거나 자바 설정을 이용하다면 @EnableAspectJAutoProxy를 적어준다.
