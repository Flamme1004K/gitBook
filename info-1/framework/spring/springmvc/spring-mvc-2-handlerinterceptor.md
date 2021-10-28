# Spring MVC - 기본설정(2) 및 HandlerInterceptor

**뷰 전용 컨트롤러 설정**

\- 단순경로 요청을 위함.

defaultServlet \<mvc: view-controller path="/경로" view-name="경로/">

**디폴트 서블릿 설정과 동장박식**

web-xml에서 DispatcherServelet에 대한 경로 매핑을 '/'로 하면 CSS/JS/HTML/JSP 등에 대한 요청이 DispatcherServlet으로 전달된다.

이 요청은 was가 기본으로 제공하는 디폴트 서블릿이 처리하게 되어 있기 때문에 디폴트 서블렛이 처리를 한다.

그러므로 처리를 할 때 \<mvc:default-servlet-handler>를 추가시키면 된다.

default-servlet-handler는&#x20;

&#x20;\- 요청 경로와 일치하는 컨트롤러를 찾는다.

&#x20;\- 컨트롤러가 존재하지 않으면, 디폴트 서블릿 핸들러에 전달한다.

&#x20;\- 디폴트 서블릿 핸들러는 WAS의 디폴트 서블릿에 처리를 위임한다.

&#x20;\- 디폴트 서블릿읭 처리 결과를 응답으로 전송한다.

**정적 자원 설정하기**

CSS, JS, 이미지 등의 자원은 거의 변하지 않기 때문에, 웹 브라우저에 캐시를 하면 네트워크 사용량, 서버 사용량, 웹 브라우저의 반응 속도 등을 개선할 수 있다.

이렇게 반응 속도를 개선하기 위해서 사용하는 것이 \<mvc:resources> 태그이다.

```
<mvc:resources mapping="/images/**" location="/img/, /WEB-INF/resources/" cache-period="60"/>
```

**HandlerInterceptor를 이용한 인터셉터 구현**

&#x20;요청 경로마다 접근 제어를 다르게 해야 한닥거나 사용자가 특정 URL을 요청할 때마다 접근 내역을 기록하고 싶다면 매 클래스마다 중복된 코드를 쓸 수 밖에 없다. 하지만 이 중복되는 코드를 없애주기 위한 방법이 바로 AOP이지만 AOP는 범용적인 방법이다. 그래서 스프링MVC에서는 여러 컨트롤러에 공통으로 적용되는 기능을 구현하는 방법인 HandlerInterceptor를 제공하고 있으며, 이를 사용하면 스프링 MVC에 맞게 공통 기능을 다수의 URL에 적용 할 수 있게 된다.\
\


HandlerInterceptor는 공통기능으로 컨트롤러 실행전, 컨트롤러 실행 후, 뷰를 실행한 이후로 기능을 넣을 수 있다.

```
<mvc:interceptors>
	<bean id="measuringInterceptor" class="인터셉터클래스 이름"/>
</mvc:interceptors>
```

기본적인 설정.

```
<mvc:interceptors>
	<mvc:interceptor>
    	<mvc:mapping path="/event/**"/>
        <mvc:mapping path="/folders/**"/>
	<bean id="measuringInterceptor" class="인터셉터클래스 이름"/>
    </mvc:interceptor>
</mvc:interceptors>
```

이렇게 요청경로 패턴을 따로 지정 할 수 있다.

**핸들러 인터셉터는 실행 시간 측정, 로그인 한 사용자만 접근 허용, 여러 뷰에서 함께 사용될 모델 설정**

**나는 jwtToken, cookie, session로그인 정보를 전부 HandlerInterceptor를 extends해줘서 구현했다.**

```
<mvc:interceptors>
	<mvc:interceptor>
    	<mvc:mapping path="/acl/**"/>
        <bean class="kr.or.flamme.auth"/>
    </mvc:interceptor>
    <bean class="Measure"/>
    <mvc:interceptor>
          <mvc:mapping path="1"/>
          <mvc:mapping path="2"/>
          <mvc:mapping path="3"/>
          <bean class="test1"/>
    </mvc:interceptor >
</mvc:interceptors>
```

\- 많은 인터셉터를 넣어 차례대로 진행하도록 만들었다.

또한 제외하고 싶으면 \<mvc:exclude-mapping paht="명확한경로"/>를 써주면 된다.좋아요1공유하기통계글 요소\
