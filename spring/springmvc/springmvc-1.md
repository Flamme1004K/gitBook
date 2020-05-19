# SpringMVC-1

요번 포스팅은 Spring mvc에 대해서 써보려고 한다.

일단 Spring은 대표적인 java framework이다. 이쯤이면 framework라는 설명이 필요하다.

framework란? 어플레이케이션 개발에 바탕이 되는 클래스와 인터페이스들의 집합이다.

이 스프링 프레임워크에서는 기본적으로 MVC 패턴을 많이 쓴다.

그리고 스프링 MVC 설정은 프로젝트 시작 시점에 설정하면 대부분 완료가 된다.  그럼 나머지는?

나머지는 5layer구조를 구현하는 데에 시간을 쓴다.

**MVC 패턴이란?**

**MVC 패턴은 Model View Controller의 약자이다.**

**즉 이름에 맞게 시스템의 구조를 각각 Model, View, Controller로 나누고 구조에 맞게 설계를 하는 것이다.**

이제부터 MVC 패턴과 스프링 프레임워크를 어떻게 조합했는지에 대한 기본적인 동작 방식과 구성 요소에 대해 살펴볼 것이다.

**스프링 MVC 기본 설정**

**1. web.xml에 DispatcherServlet설정**

**2. web.xml에 캐릭터 인코딩 처리 위한 필터 설정**

**3. 스프링 MVC 설정 \( HandlerMapping, HandlerAdapter설정, ViewResolver 설정\)**

일단 pom.xml은 뒤로 미루고, web.xml을 봐보자.

```text
<servlet>
	<servlet-name>dispatcher</servlet-name>
    <servlet-class>
    	org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
	<init-param>
    	<parma-anme>contextConfigLocation</param-name>
        <param-value>
        	/WEB-INF/dispatcher-servlet.xml
        </param-value>
    </init-param>
	<load-on-startup>1</load-on-startup>
</servlet>

<!--DispatcherServlet을 등록 -->

<servlet-mapping>
	<servlet-name>dispatcher</servlet-name>
    <url-partten>*.do</url-pattern>
</servlet-mapping>

<!--DispatcherServlet에 대한 매핑을 *.do로 적용-->

<filter>
	<filter-name>encodingFilter</filter-name>
    <filter-class>
    	org.springframework.web.filter.CharacterEncodingFilter
    </filter-class>
    <init-param>
    	<param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
	<filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

<!--요청 파라미터를 UTF-8로 처리하기 위한 필터를 설정-->
```

- 현재 읽고 있는 책의 web.xml예제 

- SpringBoot에서 web.xml 안 보이는 이유는 boot안에 내부 톰캣이 있기 때문에 안 보이는 거라고 한다.\(?\)

Web.xml의 첫번째 주석을 보면 DispatcherServlet 등록이라고 써져있을 것이다.

이제부터 DispatcherServlet이 어떻게 구동되는가에 대해서 써보려고 한다.

참고로 SpringMVC의 첫 관문은 바로 DispatcherServlet이다.

**dispatcherServlet이란?**

**Serlvet Container에서 HTTP 프로토콜을 통해 들어오는 모든 요청을 프레젠테이션 계층의 제일 앞에 둬서 중앙집중식으로 처리해주는 프론트 컨트롤러이다.**

[**출처 : http://mangkyu.tistory.com/18**](https://mangkyu.tistory.com/18)

즉, 클라이언트 요청이나 서버를 구동시킬때 가장 먼저 마주치고 먼저 실행되는 녀석이다.

![](https://k.kakaocdn.net/dn/cq4gAa/btqCoQA2qxy/IpardUiRlksZnsbdkGROa0/img.png)

DispatcherServlet은 이렇게 돌아간다.

DispatcherServlet - 클라이언트의 요청을 받고, 리턴 값을 view에 전달한다.

HandlerMapping - 클라이언트의 요청 URL을 어떤 컨트롤러가 처리할지를 결정

HandlerAdapter - DispatcherServlet의 처리 요청을 변환해서 컨트롤러에 전달하고 컨트롤러의 응답 결과를 DispatcherServlet이 요구하는 형식으로 변환한다. 웹 브라우저 캐시 등의 설정도 담당한다.

Controller - 클라이언트의 요청을 처리한 뒤 결과를 리턴한다.

ViewResolver - 컨트롤러의 처리 결과를 보여줄 뷰를 결정한다.

View -  컨트롤러의 처리 결과 화면을 생성한다.

여기서 Controller와 View와 연결되는 JSP는 직접 구현을 해줘야 한다.

일단 DispatcherServlet 설정부터 시작한다.

DispatcherServlet은 spring mvc의 대표적인 서블렛 클래스이다.

```text
    <servlet>
          <servlet-name>dispatcherServlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <!-- 기본적인 dispatcherServlet설정 -->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>/WEB-INF/*.xml</param-value>
          </init-param>
          <!-- 추가 설정이 필요할 시에는 contextConfigLocation을 더해준다. -->
          <load-on-startup>1</load-on-startup>
    </servlet>
```

- xml을 이용하여 dispatcherServlet 등록

기본 서블렛 설정 시 servlet-name, servlet-class만 써주면 된다. 

하지만 설정 파일이 많아진다면 init-param 속에 contextConfigLocation을 넣고 param-value에 결로를 설정해주면 된다.

```text
    <servlet>
          <servlet-name>dispatcherServlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <init-param>
              <param-name>contextClass</param-name>
              <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
          </init-param>
          <init-param>
          	  <param-name>contextConfigLocation</param-name>
              <param-value>
              <!-- 클래스 이름 -->
              </param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
    </servlet>
```

- javaConfiguration을 이용하여 dispatcherServlet 등록

```text
<filter>
	<filter-name>encodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-param>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
	</init-param>
</filter>

<filter-mapping>
	<filter-name>encodingFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
	
<servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>


```

그 후에 dispatcher parameter를 받아주기 위하여 밑에 encoding 설정을 해준다. 

우리나라인 경우에는 대부분 UTF-8을 쓰기 때문이다.

url pattern은 /\*로 해줘서 모든 경로가 UTF-8로 인코딩하여 받을 수 있게 해주자.

dispatcherServlet을 /\*을 안 해주면 html, css가 안 먹을 수 있으므로 /로만 표기해 준다.

이제 dispatcherServlet의 기본적인 설정을 해줬으니, handlerMapping, handlerAdapter, ViewResolver 구현 객체를 빈으로 등록해주어야 한다.

mvc-config을 사용해보자

```text
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- Uncomment and your base-package here:
         <context:component-scan
            base-package="org.springframework.samples.web"/>  -->


    <mvc:annotation-driven />

	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	        <!-- Example: a logical view name of 'showMessage' is mapped to '/WEB-INF/jsp/showMessage.jsp' -->
	        <property name="prefix" value="/WEB-INF/view/"/>
	        <property name="suffix" value=".jsp"/>
	</bean>

</beans>
```

-xml방식

기존 Spring을 만들었을 때 기본적으로 생기는 config 파일이다.

&lt;mvc:annotation-driven /&gt; 태그는 RequestMappingHandlerMapping, RequestMappingHandlerAdapter를 등록해준다.

mvc:annotation 

 - @Controller 애노테이션이 적용된 클래스를 컨트롤러로 쓰게 해준다.

 - JSON이나 XML 등 요청/ 응답 처리에 필요한 ConversionService등을 빈으로 등록해준다.

ViewResolver는 컨트롤러의 처리 결과를 jsp의 경로를 생성할 때 사용된다.

```text
@Configurattion
@EnableWebMvc
public class MvcQuickStartConfig {
	@Bean
    public InternalResourceViewResolver viewResolver(){
    	InternalResourceViewResolver viewResolver = new InternalResouceViewResolver();
        viewResolver.setPrefix("/WEB-INF/view/");
        viewResolver.setSuffix(".jsp");
        return viewResolver;
    }
}
```

-javaConfig방식

요번 시간은 이걸로 끝이다. 다음 시간에 신경 쓰이던 부분인

**dispatcherServlet을 /\*을 안 해주면 html, css가 안 먹을 수 있으므로 /로만 표기해준다.**

**이제 dispatcherServlet의 기본적인 설정을 해줬으니, handlerMapping, handlerAdapter, ViewResolver 구현 객체를 빈으로 등록해주어야 한다.**

이 부분을 설명하고 mvc 기본 설정에 대한 설명을 이어서 설명하겠습니다.

 

