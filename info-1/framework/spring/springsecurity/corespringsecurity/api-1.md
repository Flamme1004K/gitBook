# 인증 API - 사용자 정의 보안 기능 구현

스프링 시큐리티의 웹 보안 기능 초기화 및 설정 --&gt; 문제점 발생

* 문제점
  * 계정 추가, 권한 추가, DB 연동 등
  * 기본적인 보안 기능 외에 시스템에서 필요로 하는 더 세부적이고 추가적인 보안기능이 필요

현재 보안옵션, 권한, DB가 없어 보안 수준이 단순하다.

더 안전하고 보완된 기능을 갖추려면 보안 기능을 추가해야한다.

![](../../../../../.gitbook/assets/image%20%286%29.png)

#### SecurityConfig

사용자 정의 보안 설정 클래스 WebSecurityConfigurerAdapter를 상속 받는다.

#### WebSecurityConfigurerAdapter

스프링 시큐리티의 핵심적인 클래스이다. 스프링 시큐리티의 웹 보안 기능 초기화 및 설정을 한다.

#### HttpSecurity

WebSecurityConfigurerAdapter가 생성한다. 세부정인 보안 기능을 설정할 수 있는 API 제공한다.

**WebSecurityConfigurerAdapter와 HttpSecurity가 웹 보안성의 기본 활성화하고 작동화 시킨다.**

#### 인증 API

* http.formLogin\(\)
* http.logout\(\)
* http.csrf\(\)
* http.httpBasic\(\)
* http.SessionManagement\(\)
* http.RememberMe\(\)
* http.ExceptionHandling\(\)
* http.addFilter\(\)

#### 인가 API

* http.authorizeRequests\(\)
* http.antMatchers\(/admin\)
* http.hasRole\(USER\)
* http.permitAll\(\)
* http.authenticated\(\)
* http.fullyAuthentication\(\)
* http.acess\(hasRole\(USER\)\)
* http.denyAll\(\)



#### WebSecurityConfigurerAdapter

1. HTTP security 요청 
2.  11가지정도의 API를 호출
3.  configure Method 호출 \(추가적인 설정\) 
4.  authorizeRequests\(인증\) 
5.  anyRequest, authenticated --&gt; and --&gt; formLogin을 하여 로그인이 된다.
6.  바로 로그인 과정이 여기서 진행되는 것이고 나중에 configure을 상속받아 인증인가를 구현하면 된다.

![](../../../../../.gitbook/assets/image%20%285%29.png)

