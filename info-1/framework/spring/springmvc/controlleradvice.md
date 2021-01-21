---
description: 'https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc'
---

# @ControllerAdvice

 @ControllerAdvice 란?

 동일한 예외 처리를 개별 컨트롤러가 아닌 전체 애플리케이션에 적용할 수 있다.

* Exception handling methods annotated with `@ExceptionHandler`.
* Model enhancement methods \(for adding additional data to the model\) annotated with `@ModelAttribute`. Note that these attributes are _not_ available to the exception handling views.
* Binder initialization methods \(used for configuring form-handling\) annotated with `@InitBinder`.



 @ControllerAdvice가 없기전의 Exception 처리

* Error 페이지
* Controller기반 @ExceptionHandler 사
* Exception에 따른 Class 생성



