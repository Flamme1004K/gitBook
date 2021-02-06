# Annotation

 Annotation은 정적이다. Annotation에 들어가야하는 값들은 전부 정적인 값이어야 한다. static final

 @Retention은 source, class, runtime이 있다.

source는 컴파일 했을 때 경우 남아있지가 않는다.

 예\) Override

 class 파일까지 남아있다면 리플렉션이 안된다.

 Runtime 에서 리플렉션을 쓸 수 있다.

 어노테이션을 만들때 필요한 만큼만 만들면 된다.



@Inherited : 상속 받은 클래스도 어노테이션 영향을 받는다.

  getdeclared 선언되어 있는 것들을 모두 가지고온다.



