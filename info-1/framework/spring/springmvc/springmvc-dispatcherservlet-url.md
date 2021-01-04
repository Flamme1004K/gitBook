# SpringMVC - DispatcherServlet의 URL

저번 시간에 아래와 같은 문구와 끝났다.

**dispatcherServlet을 /\*을 안 해주면 html, css가 안 먹을 수 있으므로 /로만 표기해준다.**

**이제 dispatcherServlet의 기본적인 설정을 해줬으니, handlerMapping, handlerAdapter, ViewResolver 구현 객체를 빈으로 등록해주어야 한다.**

예고했던 대로 문구를 해석하고, 기본 설정에 대해 다시 써보겠습니다.

servlet mapping에 따른 controller mapping과 defaultServlet 설정이다.

```text
<servlet-mapping>
	<servlet-name>dispatcher</servlet-name>
	<url-pattern>*.do</url-pattern>
<servlet-mapping>
```

이 문구는 전자정부프레임워크에서 봐왔을 것이고, 다른 스프링 설정에서도 많이 봐왔을 것이다.

맞다. \*.do는 나중에 controller를 탔을 때 주소가 끝에. do가 붙어야 된다는 것이다.

url-pattern은 어디서 작동하냐? 바로 RequestMappingHandlerMapping에서 작동한다. 

그러면 RequestMappingHandlerMapping에게 \*.do를 없애주고 싶으면 어떻게 해야 하나?

바로 URL 패턴을 "/"으로 주면 된다.

그리고 &lt;mvc:default-servlet-handler/&gt;를 추가하면 된다.

이 설정을 해주면 jsp 요청을 제외한 나머지 모든 요청을 DispatcherServlet이 받게 된다.

그리고 요청 URL에 매핑되는 컨트롤러를 검색, 디폴트 서블릿 핸들러가 등록되어 있지 않다면 404 응답 에러, 등록되어 있으면 디폴트 서블릿 핸들러에 요청을 전달한다.

```text
@Configuration
@EnableWebMvc
public Class WebConfig extends WebMvcConfigurerAdapter{
	@Override
    public void configureDefaultServletHandling(
    				DefaultServletHandlerConfigurer configurer){
    	configurer.enable();
    }
    
}
```

-java 설정

다음 시간에는 Validator와 Erros/BindingResult이다.

