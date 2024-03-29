# effective78



## Item79 과도한 동기화는 피하라

응답 불가와 안전 실패를 피하려면 동기화 메서드나 동기화 블록 안에서는 제어를 절대로 클라이언트에 양도하면 안 된다.

#### 여기서 제어란 ?

* 재정의할 수 있는 메서드를 호출
  * 클라이언트가 넘겨준 함수 객체를 호출

--&gt; 이러한 동기화 메서나 및 동기화 블록에 대한 제어는 과도한 동기화를 일으킴.

* 성능을 떨어트림.
* 교착상태에 빠트림.
* 예측할 수 없는 동작을 낳기도 한다.
* 데이터를 훼손 할 수 있음.

## 어떻게 동기화를 해야하는 걸까?

1. 클래스를 동시에 사용해야 하는 클래스가 외부에서 알아서 동기화하게 하자. 
2. 동기화를 내부에서 수행해 스레드 안전한 클래스로 만들자.

   **첫번 째 방식**은 java.util\(Vector와 HashTable을 제외\) 택하였고, **두번 째 방식**은 Java.util.concurrent를 택하였다. 단, 두번 째 방식은 클라이언트가 외부에서 객체 전체에 락을 거는 것보다 동시성을 월등히 개선할 수 있을 때 선택해야한다.

자바는 초창기에는 이 지침을 따르지 않았다고 한다.

StringBuffer 인스턴스는 거의 항상 단일 쓰레드에서 쓰였음에도 내부적으로 동기화를 수행하였다. 이로 인해, 동기화를 하지 않는 StringBuilder가 만들어 졌다.

비슷한 방법으로 java.util.Random은 동기화하지 않는 버전인 java.util.concurrent.ThreadLocalRandom으로 대체되었다.

만약 두개가 힘들다면, 스레드 안전하지 않다고 명기하자.

1. 여러 스레드가 호출할 가능성이 있는 메서드가 정적 필드를 수정한다면 그 필드를 사용하기 전에 반드시 동기화를 해야 한다.

   정적 필드가 심지어 pirvate라도 서로 관련 없는 스레드들이 동시에 읽고 수정할 수 있게 되면, 사실상 전역 변수와 같아지기 때문이다.

### 결론

* 교착상태와 데이터 훼손을 피하려면 동기화 영역 안에서 외계인 메서드를 절대 클라이언트에게 양도하면 안된다.
* 동기화 영역 안에서의 작업은 최소한으로 줄이자. 가변 클래스를 설계할 때는 스스로 동기화해야 할지 고민하자.
  * 멀티코어 세상인 지금은 과도한 동기화를 피하는 게 과거 어느 때보다 중요하다.
* 합당한 이유가 있을 때만 내부에서 동기화하고, 동기화했는지 여부를 문서에 명확히 밝히자.

