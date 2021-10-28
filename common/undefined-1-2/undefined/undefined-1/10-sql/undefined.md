# 인덱스

## 인덱스란?

* 데이터를 논리적으로 정렬해 검색과 정렬 작업 시 속도를 높이는데 사용한다.
  * 즉, 데이터베이스 분야에 있어서 테이블에 대한 동작의 속도를 높여주는 자료 구조이다.
  * 이러므로써 응답시간을 빠르게하고,  서버의 부하량을 최소화 할 수 있다.
* DBMS가 특정 데이터형으로 데이터를 찾는 행위 입니다.
* 어떤 데이터를 인덱스로 만드느냐에 따라 방대한 데이터의 경우 성능에 큰 영향을 미칠 수 있다.
* SELECT-FROM-WHERE에서 WHERE절에 사용하는 컬럼에 대해 효율적이라고 합니다.
* WHERE 절을 사용하지 않고, 인덱스가 걸린 컬럼을 조회하는 것은 성능에 영향이 없다라고 합니다.

## 인덱스의 종류

{% embed url="http://blog.naver.com/PostView.nhn?blogId=xxsaintxx&logNo=220604493277&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView" %}

{% embed url="https://mozi.tistory.com/199" %}

## **무조건 많이 설정하는 것이 좋냐? **

인덱스는 데이터베이스 메모리를 사용하여 테이블 형태로 저장되므로 개수와 저장 공간은 비례하기 때문에 무조건 많이 설정하는 것은 좋지 않습니다.

## **그러면 어떻게 설정해야 좋은 인덱스일까요?**

1. 짧게 말해서 인덱스는 조회시 자주 사용하고 고유한 값 위주로 인덱스를 설정하는게 좋다.

## **인덱스의 주의점 **

* 인덱스는 SELECT 쿼리에서 성능이 잘 나오지만, INSERT, UPDATE, DELETE 쿼리에 성능이 나빠진다.

&#x20;UPDATE, DELETE는 WHERE 절에 잘 설정된 인덱스로 조건을 붙여주면 조회할 때 성능은 크게 조회되지 않는다.

&#x20;하지만 INSERT의 경우 새로운 데이터가 추가되면서 기존에 인덱스 페이지에 저장되어 있던 탐색 위치가 수정되어야 하므로 효율이 좋지 않다.

* 인덱스 데이터는 저장 공간을 많이 차지한다.
* 모든 데이터가 인덱스에 적합한 것은 아니다.
* 인덱스는 데이터 필터링과 정렬에 사용된다.
  * 특정 순서로 데이터를 자주 정렬한다면, 그 데이터는 인덱싱 후보가 될 수 있다.
* 여러 열을 하나의 인덱스로 정의할 수 있다.

## **어떤 컬럼에 인덱스를 설정하는게 좋을까?**

&#x20;인덱스는 한 테이블당 보통 3\~5개가 적당하다.

&#x20;효율적 인덱스 설정에는

* 카디널리티(Cardinality)  : 한 컬럼이 갖고 있는 값의 중복 정도가 낮을 수록 좋다.
* 선택도(Selectivity) : 선택도가 낮을 수록 인덱스 설정에 좋다.
* 활용도 : 활용도가 높을 수록 인덱스 설정에 좋다.
* 중복도 : 중복도가 없을 수록 인덱스 설정에 좋다.

가 필요하다.

### **참고 블로그**

{% embed url="https://yurimkoo.github.io/db/2020/03/14/db-index.html" %}

{% embed url="https://m.blog.naver.com/PostView.nhn?blogId=thwlstmxk&logNo=221062514161&proxyReferer=https:%2F%2Fwww.google.com%2F" %}

{% embed url="https://m.blog.naver.com/PostView.nhn?blogId=weekamp&logNo=221603471094&proxyReferer=https:%2F%2Fwww.google.com%2F" %}

{% embed url="https://12bme.tistory.com/138" %}

{% embed url="https://teraphonia.tistory.com/681" %}

{% embed url="https://estenpark.tistory.com/384" %}

{% embed url="https://webmonkeyuk.wordpress.com/2010/09/22/what-makes-a-good-mysql-index-part-1-column-size/" %}

{% embed url="https://jojoldu.tistory.com/243" %}

{% embed url="https://isstory83.tistory.com/131" %}

{% embed url="https://zorba91.tistory.com/293" %}

****

****

