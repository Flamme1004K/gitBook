# Functional Programming

 Funcional Programming이란? 

 어떤 문제를 해결하는데 있어서 그 과정이나 공식에 매몰되기보다는 그냥 이미 만들어진 함수를 활용하는 방식 

 Functional Programming은 순수 함수로 접근하는 것으로 공유 상태\(pure functions\) , 불변 데이터\(mutable data\) , 사이드 임펙트\(side impact\) 를 피할 수 있다. 

 Functional Programing은 선언형 프로그램\(Imperative Programming\)이다.

 OOP\(Object Oriented Programming\)과 대비 된다.

##  Functional Programming에 대해서 알아둬야할 것 

1. Pure functions
2. Function composition
3. Avoid shared state
4. Avoid mutating state
5. Avoid side effects

###  Pure functions 

* Input과 Output이 항상 같아야 한다.
* side effects가 없어야 한다.

###  Function composition

 

함수 자체가 숨겨진 input과 output이 없도록 설계되어야 하는 것이 전제조건이다.

 input : parameter , output : return 

 filter, map, reduce와 같은 메서드를 활용하는 것이 대표적인 Functional Programming의 예

##  Input과 Output이 없도록 설계하는 것은 무엇일까? 

 1. 순수한 함수\(pure function\) 부작용\(side-effect\)이 없는 함수, 

 함수의 실행이 외부에 영향을 끼치지 않는 함수를 뜻한다.

 2. 익명 함수\(anonymous function\)

 이름이 없는 함수.  x -&gt; x \* x

 3. 고계 함수\(higer-order function\)

 함수를 다루는 함

