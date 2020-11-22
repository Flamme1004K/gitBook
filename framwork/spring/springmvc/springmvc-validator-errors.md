# SpringMVC - validator와 Errors

요번 시간에는 validator와 Errors입니다.

내가 보고 있는 책에서는 이렇게 써져있다.

**웹 개발에서 입력 폼의 값이 올바른지 검증하는 것은 매우 중요한 작업이다.**

**오청 파라미터 값을 확인하지 않고 그대로 사용한다면 데이터베이스에 잘못된 데이터가 들어갈 가능성이 많다.**

**요청 파라미터 값을 검사할 떄에는 크게 두가지 방법을 이용한다고 한다.**

**웹 브라우저 : 자바 스크립트를 이용해서 데이터를 웹 서버에 전송하기 전에 미리 검사**

**웹 서버 : 전달받은 요청 파라미터의 값을 검사, 올바르지 않을 경우 에러 코드를 응답하거나 재입력을 위한 폼 화면을 웹 브라우저에 전송한다.**

일단 valid 메세지부터 보겠다.

valid 메세지를 출력하려면 

메세지를 읽어올 때 사용할 MessageSource를 스프링 설정에 등록한다.

**잠깐, 여기서 프로퍼티 설정, 메시지 설정에 대해서부터 알고 넘어가려고 한다.** 

이 책은 같이 설명해주면 좋을 것을... 프로퍼티설정 및 메시지 설정을 앞으로 빼놔서 책을 다시 앞으로 넘겨야됐다.

그리고 나는 지금 프로퍼티에 대한 설명이 부족하여 여기서 짧게 정리하자.

**어플리케이션을 개발하다보면 동일한 코드를 사용하면서 일부 정보만 변경하고 싶은 경우가 흔히 발생한다.** 

**이렇게 일부 정보만 변경하게 만드는 방법 중에 하나가 Environment이다.**

Enviroment는 프로퍼티 통합관리, 프로필을 이용해서 선택적으로 설정 정보를 사용할 수 있는 방법을 제공한다고 한다.

그 중 프로퍼티 값을 제공하는 기능에 대해 말해보려고 한다.

스프링은 시스템 프로퍼티와 한경 변수를 사용하는 두개의 PropertySource를 기본적으로 사용한다.

이 중에서 시스템 프로퍼티가 우선 순위가 가장 높다.

그러면 프로퍼티는 어떻게 설정하는가?

```text
<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
		<property name="basenames">
			<list>
				<value>classpath:/egovframework/message/message-common</value>
				<value>classpath:/egovframework/rte/fdl/idgnr/messages/idgnr</value>
				<value>classpath:/egovframework/rte/fdl/property/messages/properties</value>
			</list>
		</property>
		<property name="cacheSeconds">
			<value>60</value>
		</property>
	</bean>
```

-egovframework

```text
<bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource" 
		p:basenames="kr.or.flamme.msg.message"
	/>
```

-MyProject

properties 파일에

```text
NotBlank={0}누락
Length={0} 길이({1}~{2})확인
Email=이메일주소확인
NotNull={0}널을 허용하지 않음.
Min={0}대한 최소값 {1}
Pattern={0}형식{1} 확인
```

 써주고 &lt;form:errors path="Notblank"&gt; 써주면 된다.

이제 익셉션 처리이다.

Exception처리에서는 **@ExceptionHandler와 @ControllerAdvice 그리고 @ResponseStatus를 통한 익셉션 처리**가 있다.

**@ExceptionHandler**는 RequestMapping 메서드를 실행하는 과정에서 익셉션이 발생할 때 직접 그 익셉션 처리를 하고 싶으면 ExceptionHandler를 써주면 된다.

즉 @ExceptionHandler에 AritihmeticException를 써놨을때 이 클래스에서 ArithmeticException이 뜨면 ExcptionHandler를 등록해놓은 에러페이지로 이동한다.

그다음에는 **@ControllerAdvice**이다.

ControllerAdvice는 ExceptionHandler를 공통적으로 사용하기 위해서 만든 것이다.

코드는 이렇다.

```text
@ControllerAdvice("kr.or.flamme")
public class CommonExceptionHandler{
	@ExceptionHandeler(RuntimeException.class)
    public String handleRuntimeException(){
    	return "error/commonException"
    }
}
```

즉 kr.or.flamme 밑에 있는 하위 패키지들이 RuntimeException이 발생했을때 handleRuntimeException 클래스를 탄다는 것이다,

이제는 **@ResponseStatus**를 이용한 익셉션이다.

ResponseStatus는 익셉션 클래스에 직접 에러 응답코드를 설정하고 싶을 때 사용한다.

```text
throw new NoFileInfoException();

@ResponseStatus(HttpStatus.NOT_FOUND)
public class NoFileInfoException extends Exception{

}
```

이렇게 HttpStatus.NOT\_FOUND를 걸어준다.

이 놈은 꼭 컨트롤러 영역에서 발생시키는 익셉션 코드에만 사용해야 한다. \(UI영역에 의존하는 결과를 만들기 때문이다.\)

