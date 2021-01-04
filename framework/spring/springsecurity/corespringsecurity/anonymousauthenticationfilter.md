# AnonymousAuthenticationFilter



1. 익명사용자 인증 필터
2. 익명사용자와 인증 사용자를 구분해서 처리하기 위한 용도로 사용
3. 화면에서 인증 여부를 구현할 때 isAnonymous\(\)와 isAuthenticated\(\)로 구분해서 사용
4. 인증객체를 세션에 저장하지 않는다.

로그인을 하지않고 사이트에 접속하면 일단 익명사용자 인지 아닌지에 대한 체크를 한다.

들어오면 AnonymousAuthenticationFilter를 들어가는데

여기서 바로 익명사용자에 대한 체크가 시작된다.

여기서 처음에 null 체크를 한다.

인증과정을 처리안했으면 SecurityContenxtHolder.getContext\(\).getAuthentication\(\)은 null이된다.

그 후 securityContext에 role\_anonymouse 등 세가지로 객체를 만든다.

 

