---
description: 최범균님의 스프링 4.0 책을 쓰고 정리한 글입니다.
---

# SpringDI

대전 D 개발원을 나오고 나서 다시 한번 스프링에 대한 기초를 잡아보려 회사에 굴러다니는 스프링 4.0 책을 펼쳐보았다.

오늘은 이 책의 두번째 챕터인 Spring DI에 대해서 포스팅을 하려고 한다.

**DI란 Dependecy Injection의 약자이고 우리말로는 '의존 주입'이라고 한다.**

Spring FrameWork는 DI기반으로 돌아가기 때문에 DI에 대한 개념이 일단 필요하다.

JAVA를 공부했을 때 필자는** 'NEW 뭐시기뭐시기'** 를 많이 썼을 것이다.

맞다. 우리는 NEW를 붙이면서 의존하는 타입의 객체를 직접 생성을 했던 것이다.

이것이 바로 의존 주입이라는 것이다.

이렇게 의존 주입을 직접 생성하면서 해주면 뭐가 안 좋을까?

이렇게 직접 생성하는 것에는 단점이 있다. 필자는 두 가지만 적겠다.

만약에 더 다른 단점이 있고 이러한 장점을 Spring DI가 해결해주는 게 있다면 덧글을 달아주길 바란다.

**1.  코드량이 늘어날 것이다.**

**2.  다른 클래스를 완성하기 전까지는 우리는 이 클래스에 해당되는 테스트는 사용하지 못한다.**

이 둘을 어떻게 해결할까?&#x20;

1번은 java class를 이용한 의존 주입, xml을 이용한 의존주입 마지막으로 annotation을 이용한 의존 주입으로

코드를 짧게 만들어주고 정리도 해준다.

2번은 완성하기 전에 더미 데이터를 넣어서 테스트할 수 있도록 도와준다.

**그럼 DI설정은 어떻게 해줄까?**

Spring을 처음 접하면 bean이라는 용어를 Spring이라는 단어 다음으로 많이 들을 것이다. 그리고 많이 보았을 것이다.

그렇다. Bean을 이용하여 의존성 주입을 할 수도 있고, 의존성 주입을 제안할 수도 있다.

또한 의존성 주입으로  API 사용 그리고 SpringFrameWork의 기능들을 대부분 쓸 수가 있다.

그리고 아무도 이런 짓은 하지 않겠지만, 한 XML 파일이나 Class파일에 절대 의존성 주입을 다 모아놓지 않았으면 한다.

필자는 개발할 때 가장 필요한 것은 바로 정리라고 생각한다. 즉 의존성을 한 곳에다가 모아놓지 말고, 연관된 것끼리&#x20;

모아서 정리해놓길 바란다.

**이제 DI를 공부하면서 다시 한번 알게 된 정보들이다.**

**1. xml에서 DI를 설정한 java class를 주입하려면 \<context:annotation-config>를 사용해야 된다는 것**

그리고 configuration이 적용된 빈을 또한 등록해야 된다는 것이다.

그러면 반대로 java class에 xml을 등록시키려면 어떻게 해야 하냐면 class위에 @ImportResource("classPath:xml이름")을 써야 된다는 것이었다

즉 @Configuration @Import\~가 된다는 점이다.

두 개 일 때는 객체 형태로 쓰면 된다.

**2. 어노테이션 기반 의존 자동연결 설정**

\<context:annotation-config/> 설정하기이다.

ex) @Autowired, @Resource, @PostConstruct, @Qualifier 어노테이션을 쓸 수 있게 된다.

그럼 @Inject는요?

Inject는 pom.xml을 이용하여 추가해주면 된다.

**3. 컴포넌트(@Component) 설정하기**

**컴포넌트(@Component)란?**

스프링 빈으로 자동으로 등록하고 의존을 자동 설정함으로써 각 빈 간의 의존 처리가 가능하게 한다.

**컴포넌트를 사용하려면? **

기본적으로 컴포넌트 어노테이션을 사용하면 \<context:component-scan> 태그를 이용하여 빈을 등록한다.

ex)  xml : \<context-component-scan base-package="클래스 주소">

&#x20;    java : @ComponentScan(basePackages = "클래스 주소")

**Component의 종류는 Component, Service, Repository, Controller가 있다.**

나는 지금까지 Component를 xml에 설정했지 클래스 위에 어노테이션을 써본 적이 없었다.

다음 프로젝트부터 적용해봐야 되겠다.

그 외 현재 나는  Service는 서비스단, Repository는 dao, Controller는 jsp를 연결해주기 위해서 사용하고 있다.

스캔 대상 클래스 범위 지정

항상 전자정부 프레임워크, 그리고 스프링 Component설정을 보면 아래와 같은 설정이 있다.

```
<context:component-scan base-package="egovframework">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service"/>
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
 </context:component-scan>
```

이 설정은 바로 Controller, Service, Repository를 어느 정도 범위 내에서 자동 스캔할 것인지에 대한 설정이다.

type값에 들어갈 수 있는 것들 종류가 꽤 있다.

annotation, assignable, regex, aspectj가 있다고 한다.

글 쓰면서 느낀 점은 빈을 사용하면 클린 한 코드를 사용할 수 있을 뿐 아니라 비즈니스 로직을 절차적으로 구현할 수 있을 것 같다.

**4. 스프링 컨테이너의 생명 주기**

컨테이너 생성 -> 빈 메타 정보를 이용한 빈 객체 생성 -> 컨테이너 사용 -> 컨테이너 종료

**5. 스프링 컨테이너의 계층 구조**

컨테이너는 부모-자식 관계의 계층 구조를 가질 수 있다.

즉, 자식에 속하는 컨테이너는 부모 컨테이너에 속한 빈을 참조할 수 있다. 반대로 부모 컨테이너는 자식 컨테이너를 참조할 수 없다.

부모/자식 구조를 만들고 싶다면 set을 이용하자.

이상이다.
