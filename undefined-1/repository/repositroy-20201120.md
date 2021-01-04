# Repositroy\(20201120\)

Repositroy 리뷰

WebAPI Query GETROOMMESSAGEBYID를 JDBCTemplate를 이용하여 구현

사용 :

JDBCTemplate, Kotlin or Groovy

Kotlin or Groovy 사용 이유 :

1.Query를 xml이 아닌 class 파일로 관리하기 위하여 사용.

1. Java의 String type의 길이가 길어졌을 때 라인 변경에 대한 지저분함을 처리하기 위하여 사용
2. xml으로 사용 시 mybatis를 사용했을 경우 한 쿼리 내에서 조건에 대한 처리가 가능하나

JDBCTemplate는 조건에 대한 처리가 한 쿼리에서 불가능하여, 하나의 Query에 대해서 여러 조건이 있으면

그 여러 조건 만큼 Query를 다 적어줘야하기 때문에 중복코드가 많이 생김.

그 처리를 class로 관리하기 위하여 사용.

사용 후 느낀점 :

1. JDBCTemplate를 접했을 때 걱정했던 부분은 중복 된 Query가 많아져 Sql을 보관하는 xml 또는 class 파일이 지저분할 것 같은 걱정을 하였으나, Kotlin이나 Groovy를 사용하면 어느정도 처리가 가능하다는 것을 느낌.
2. QueryDSL은 Select 절에 서브쿼리가 없는 것과 join이 많은 쿼리문에 한해서 연관관계를 설정하여 사용하면 될 듯함.
3. QueryDSL을 사용하냐 vs JDBCtemplate 사용하냐

queryDSL -&gt; 클래스\(object\)로 query를 짜는 라이브러리 JDBCTemplate -&gt; NativeQuery를 사용하여 DB와 연결하는것

