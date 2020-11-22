# AuthenticationManager

![](../../../../.gitbook/assets/2020-10-25-8.44.25.png)

1. 인증처리 Filter로부터 처음으로 지시 받는 첫번째 클래스이다.
2. AuthenticationManager는 interface로 적용되고 인터페이스로 작성된 클래스가 바로 ProviderManager이다.
3. 즉 AuthenticationProvider에게 인증처리를 위임한다.
4. ProviderManager는 AuthenticationProvider에게 인증처리를 위임하는 역할까지한다.
5. Oauth인증에 ProviderManager는 현재 AuthenticationProvider에 DAO, RememberMe밖에 없기 때문에 Parent 속성의 ProviderManager를 저장하고. 이 Parent속성의 ProviderManager는 Oauth AuthenticationProvider라는 인증 객체에 위임하는 역할을 한다.
6. 인증이 성공 AuthenticationProvider는 ProviderManager에게 다시 반환하고 자기 자신이 성공한 Filter에다가 전달해 준다.

![](../../../../.gitbook/assets/2020-10-25-8.44.30.png)

