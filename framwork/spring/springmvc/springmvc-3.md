# SpringMVC-3

저번시간에는 HandlerInterceptor에 대해서 알아보았다.

**WebApplicationContext 계층부터 알아보려고 한다.**

DispatcherServlet은 그 자체가 서블릿이기 때문에 한 개 이상의 DispatcherServlet을 설정하는 것이 가능하다.

이 책의 예제에서는 front와 rest의 dispatcherServlet으로 나누었다.

```text
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/service.xml, /WEB-INF/persistence.xml</param-value>
</context-param>

<listner>
    <listner-class>
     org.springframework.web.context.ContextLoaderListener
    </listner-class>
</listner>

<servlet>
    <servlet-name>front</servlet-name>
    <servlet-class>
    	org.springframework.web.servlet.DispactcherServlet
    <servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/front.xml</param-value>
    </init-param>
</servlet>

<servlet>
    <servlet-name>rest</servlet-name>
    <servlet-class>
    	org.springframework.web.servlet.DispactcherServlet
    <servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/rest.xml</param-value>
    </init-param>
</servlet>
```

DelegatingFilterProxy에 대한 이야기도 나왔지만 이건 springsecurity의 영역이니 다음에 다시 설명하려고 한다.

**이제 핸들러, 핸들러맵핑, 핸들러어뎁터에 대한 이야기다.**

dispatcherServlet은 컨트롤러를 직접 수행하지 않는다.

항상 HandlerAdapter를 이용하여 간접적으로 실행하고 있다.

이 책에서는 이렇게 말한다.

 **DispatcherServlet은 웹 요청을 실제로 처리하는 객체의 타입을 @Controller 애노테이션을 구현한 클래스로 제한하지 않는다. 실제로 거의 모든 종류의 객체로 웹 요청을 처리 할 수 있다. 그래서 웹 요청을 처리하는 객체를 좀 더 범용적인 의미로 핸들러 라고 부른다.**

즉 modelAndView만 리턴하면 올바르게 동작한다고 한다.

하지만 String으로 변환하면? 아마 DispacherServlet은 modelAndView가 없어서 안될 것이다. 이 String 타입을 변환시켜주는 녀석이 바로 HandlerAdapter이다.

그.. 우리가 지금까지

&lt;mvc:annotation-driven/&gt; 그리고 &lt;mvc:default-servlet-handler/&gt; 을 항상 넣었을 것이다.

mvc:annoation은 handlerMapping과 HandlerAdapter를 등록한다.

defaultServletHandler는 defaultServletHttpRequestHandler, SimpleUrlHandlerMapping을 등록한다.

