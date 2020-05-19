# Spring MVC - ViewResolver

**■ InternalResourceViewResolver**

  ● Jsp나 Html 파일과 같이 웹 어플리케이션의 내부 자원을 이용하여 뷰를 생성하는 AbstractUrlBasedView 타입의 뷰 객체를 리턴

  ● 기본적으로 사용하는 View 클래스임

  ● prefix, suffix 프로퍼티를 사용

|  &lt;bean id="internalResource" class="org.springframework.web.servlet.view.InternalResourceViewResolver"&gt;   &lt;property name="viewClass" value="org.springframework.web.servlet.view.JstlView"&gt;&lt;/property&gt;   &lt;property name="prefix" value="jsp/"&gt;&lt;/property&gt;   &lt;property name="suffix" value=".jsp"&gt;&lt;/property&gt;  &lt;/bean&gt; |
| :--- |


  
**■ BeanNameViewResolver**

  ● 뷰 이름과 동일한 이름을 갖는 빈을 뷰 객체로 사용

  ● 주로 커스텀 View 클래스를 뷰로 사용해야 하는 경우에 사용

<table>
  <thead>
    <tr>
      <th style="text-align:left">
        <p>&lt;bean id=&quot;viewResolver&quot; class=&quot;org.springframework.web.servlet.view.BeanNameViewResolver&quot;/&gt;</p>
        <p>&lt;bean id=&quot;download&quot; class=&quot;sp.mvc.file.download.ExcelDown&quot;/&gt;</p>
      </th>
    </tr>
  </thead>
  <tbody></tbody>
</table>  ▶ 특정 Controller에서 뷰의 이름을 "download"라고 지정 시 위의 sp.mvc.file.download.ExcelDown 클래스가 처리

**■ XmlViewResolver**

  ● 뷰 이름과 동일한 이름을 갖는 빈을 뷰 객체로 사용

  ● 별도의 XML 설정 파일로부터 빈 객체를 검색

  ● Location 프로퍼티의 값을 지정하지 않을 경우 기본값은 "/WEB-INF/views.xml"임

|  &lt;bean id="viewResolver" class="org.springframework.web.servlet.view.XmlViewResolver"&gt;   &lt;property name="location" value="/WEB-INF/xml\_views.xml"&gt;&lt;/property&gt;  &lt;/bean&gt; |
| :--- |


**■ 다수의 ViewResolver 설정**

  ● 하나의 DispatcherServlet은 한 개 이상의 ViewResolver를 설정할 수 있음

  ● "order" 프로퍼티를 이용하여 뷰 이름을 검사할 ViewResolver의 순서를 결정

  ● "order" 프로퍼티의 값이 작을수록 우선 순위가 높으며 우선순위가 높은 ViewResolver가 null을 리턴하면, 다음 우선순위를 갖는 ViewResolver에 뷰을 요청

  ● 주의할 점은 InternalResourceViewResolver는 마지막 우선 순위를 갖도록 지정해야 함 &lt;= InternalResourceViewResolver는 항상 뷰 이름에 매핑되는 뷰 객체를 리턴하기 때문에 null을 리턴하지 않음. 따라서 InternalResourceViewResolver의 우선순위가 높을 경우 우선순위가 낮은 ViewResolver는 사용되지 않게 됨. 

|  &lt;bean id="beanNameViewResolver" class="org.springframework.web.servlet.view.BeanNameViewResolver"   **p:order="1"**/&gt;    &lt;bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver"&gt;   **&lt;property name="order" value="2"&gt;&lt;/property&gt;**   &lt;property name="prefix" value="jsp/"&gt;&lt;/property&gt;   &lt;property name="suffix" value=".jsp"&gt;&lt;/property&gt;  &lt;/bean&gt;  |
| :--- |


  
  
출처: [https://snoopy81.tistory.com/325](https://snoopy81.tistory.com/325) \[영혼과영혼의양식\]

