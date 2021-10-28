---
description: Generic에 대해서 알아보자.
---

# Generic

제네릭(Generic)이란? \<?>라고 말합니다.

데이터의 타입(data type)을 일반화한다는 것을 의미한다.

제네릭은 클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정하는 방법이다.

컴파일 시에 미리 타입 검사(type check)를 수행하며 클래스나 메소드 내부에서 사용되는 객체의 타입 안정성을 높일 수 있고, 반환값에 대한 타입 변환 및 타입 검사에 들어가는 노력을 줄일 수 있다.

```
class MyArray<T> {

    T element;

    void setElement(T element) { this.element = element; }

    T getElement() { return element; }

}
```

T를 타입 변수(type variable)라고 하며, 임의의 참조형 타입을 의미합니다.

&#x20;제네릭은 여러 방법으로 쓸 수 있다.

&#x20;1\. **타입 변수의 제한**

&#x20;2\. **제네릭 메소드(generic method)**

** 3. 와일드카드의 사용**

****

** 1.타입 변수의 제한**

&#x20;제네릭은 'T'와 같은 타입 변수(type variable)를 사용하여 타입을 제한한다.

&#x20;extends를 사용하여 특정 타입만 사용하도록 제한 할 수 있다.

```
class userList<T extends auth> { ... }
```

** 2. 제네릭 메소드(generic method)**

&#x20;메소드의 선언부에 타입 변수를 사용한 메소드

&#x20;타입 변수의 선언은 메소드 선언부에서 반환 타입 바로 앞에 위치 한다.

```
public static <T> void add(...) {...}

public static <T> void sort(List<T> list, Comparator<? super T> comp) { ... }
```

&#x20; **3. 와일드카드의 사용**

&#x20;와일드카드(wild card)란 이름에 제한을 두지 않음을 표현하는 데 사용하는 기호.

&#x20;자바의 제네릭에서는 물음표(?) 기호를 사용하여 와일드 카드를 사용한다.

```
<?>           // 타입 변수에 모든 타입을 사용할 수 있음.

<? extends T> // T 타입과 T 타입을 상속받는 자손 클래스 타입만을 사용할 수 있음.

<? super T>   // T 타입과 T 타입이 상속받은 조상 클래스 타입만을 사용할 수 있음.
```

&#x20;

Ref : [http://tcpschool.com/java/java\_generic\_various](http://tcpschool.com/java/java\_generic\_various)
