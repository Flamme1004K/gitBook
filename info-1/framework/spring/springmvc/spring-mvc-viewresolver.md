# Spring MVC - ViewResolver

**■ InternalResourceViewResolver**

&#x20; ● Jsp나 Html 파일과 같이 웹 어플리케이션의 내부 자원을 이용하여 뷰를 생성하는 AbstractUrlBasedView 타입의 뷰 객체를 리턴

&#x20; ● 기본적으로 사용하는 View 클래스임

&#x20; ● prefix, suffix 프로퍼티를 사용

| <p> &#x3C;bean id="internalResource" class="org.springframework.web.servlet.view.InternalResourceViewResolver"><br>  &#x3C;property name="viewClass" value="org.springframework.web.servlet.view.JstlView">&#x3C;/property><br>  &#x3C;property name="prefix" value="jsp/">&#x3C;/property><br>  &#x3C;property name="suffix" value=".jsp">&#x3C;/property><br> &#x3C;/bean></p> |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

\
**■ BeanNameViewResolver**

&#x20; ● 뷰 이름과 동일한 이름을 갖는 빈을 뷰 객체로 사용

&#x20; ● 주로 커스텀 View 클래스를 뷰로 사용해야 하는 경우에 사용

| <p> &#x3C;bean id="viewResolver" class="org.springframework.web.servlet.view.BeanNameViewResolver"/></p><p> &#x3C;bean id="download" class="sp.mvc.file.download.ExcelDown"/></p> |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

&#x20; ▶ 특정 Controller에서 뷰의 이름을 "download"라고 지정 시 위의 sp.mvc.file.download.ExcelDown 클래스가 처리

**■ XmlViewResolver**

&#x20; ● 뷰 이름과 동일한 이름을 갖는 빈을 뷰 객체로 사용

&#x20; ● 별도의 XML 설정 파일로부터 빈 객체를 검색

&#x20; ● Location 프로퍼티의 값을 지정하지 않을 경우 기본값은 "/WEB-INF/views.xml"임

| <p> &#x3C;bean id="viewResolver" class="org.springframework.web.servlet.view.XmlViewResolver"><br>  &#x3C;property name="location" value="/WEB-INF/xml_views.xml">&#x3C;/property><br> &#x3C;/bean></p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

**■ 다수의 ViewResolver 설정**

&#x20; ● 하나의 DispatcherServlet은 한 개 이상의 ViewResolver를 설정할 수 있음

&#x20; ● "order" 프로퍼티를 이용하여 뷰 이름을 검사할 ViewResolver의 순서를 결정

&#x20; ● "order" 프로퍼티의 값이 작을수록 우선 순위가 높으며 우선순위가 높은 ViewResolver가 null을 리턴하면, 다음 우선순위를 갖는 ViewResolver에 뷰을 요청

&#x20; ● 주의할 점은 InternalResourceViewResolver는 마지막 우선 순위를 갖도록 지정해야 함 <= InternalResourceViewResolver는 항상 뷰 이름에 매핑되는 뷰 객체를 리턴하기 때문에 null을 리턴하지 않음. 따라서 InternalResourceViewResolver의 우선순위가 높을 경우 우선순위가 낮은 ViewResolver는 사용되지 않게 됨.&#x20;

| <p> &#x3C;bean id="beanNameViewResolver" class="org.springframework.web.servlet.view.BeanNameViewResolver"<br>  <strong>p:order="1"</strong>/><br> <br> &#x3C;bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"><br> <strong> &#x3C;property name="order" value="2">&#x3C;/property></strong><br>  &#x3C;property name="prefix" value="jsp/">&#x3C;/property><br>  &#x3C;property name="suffix" value=".jsp">&#x3C;/property><br> &#x3C;/bean> </p> |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |

\
\
출처: [https://snoopy81.tistory.com/325](https://snoopy81.tistory.com/325) \[영혼과영혼의양식]
