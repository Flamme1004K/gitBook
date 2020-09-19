# 데이터베이스 식별자 선택 전략

## 데이터베이스 식별자 선택 전략

데이터베이스 기본 키는 3가지 조건을 모두 만족해야 한다. 

1. null값은 허용하지 않는다. 

2. 유일해야한다. 

3. 변해선 안 된다.

테이블의 기본 키를 선택하는 전략은 2가지가 있다.

* 자연키 \(natural key\)
  * 비즈니스에 의미가 있는 키
    * 예\) 주민등록번호, 이메일, 전화번호
* 대리 키\(surrogate key\)
  * 비즈니스와 관련 없는 임의로 만들어진 키, 대체 키로도 불린다.
  * 예 : 오라클 시퀀스, auto\_increment, 키 생성 테이블 사용

### 자연키 vs 대리키

* [https://stackoverflow.com/questions/337503/whats-the-best-practice-for-primary-keys-in-tables](https://stackoverflow.com/questions/337503/whats-the-best-practice-for-primary-keys-in-tables)

자연키와 대리키의 논쟁이 항상 있었지만, 많은 사람들이 대리키를 만드는 것을 더욱 추구하는 듯하다.

그 이유에 대표적인 사례가 바로 주민등록번호를 자연키로 했을 경우이다. 예전의 주민등록번호는 null값을 허용하지 않고, 유일하고 변화하지도 않는 숫자였다. 하지만 정부 정책이 변화되면서 주민등록번호를 저장할 수 없게 되었다. 즉, 기본 키의 3가지 조건이 없어진 것이다. 그 결과 주민등록번호를 자연키를 둔 곳은 주민등록과 연관된 테이블을 모두 수정해야하는 어려움을 겪게 되었다고 한다. 하지만 처음부터 이것을 대리키, 즉 AutoIncrement, Sequence, 키 생성 테이블로 관리를 했으면 어떻게 되었을까? 바로 수정할 필요도 없었을 것이다. 즉 자연키로 하여금 비즈니스 환경이 변경되었을때 매우 치명적인 것을 미리 대리키로 해소했어야 했다.

그래도 자연키를 써야되겟다면 해당 자연키가 작고 변화되지 않는 것을 골라야 할 것이다.

### GUID\(UUID\) VS 시퀀스 , AutoIncrement

* [https://dba.stackexchange.com/questions/264/guid-vs-int-which-is-better-as-a-primary-key](https://dba.stackexchange.com/questions/264/guid-vs-int-which-is-better-as-a-primary-key)
* [https://nesoy.github.io/articles/2020-04/Guid](https://nesoy.github.io/articles/2020-04/Guid)
* [https://medium.com/@FranckPachot/uuid-aka-guid-vs-oracle-sequence-number-ab11aa7dbfe7](https://medium.com/@FranckPachot/uuid-aka-guid-vs-oracle-sequence-number-ab11aa7dbfe7)

위에서 자연키 vs 대리키를 찾다가 대부분의 사람들은 대리키를 선택했다. 하지만 대리키 선택에서도 GUID\(UUID\)를 쓸 것인가? 시퀀스, AutoIncrement를 쓸 것인가에 대한 논쟁이 있었다.

위 3가지의 글을 읽고 느낀점은 GUID\(UUID\)가 아무리 유일성에 대한 장점이 있겠지만 나중에 많은 데이터값으로 인한 성능 이슈가 일어나기 때문에 AutoIncrement나 시퀀스 전략을 사용하는 것이 좋다고 생각한다.

