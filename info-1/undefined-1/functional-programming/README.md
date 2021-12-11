---
description: >-
  https://medium.com/javascript-scene/master-the-javascript-interview-what-is-functional-programming-7f218c68b3a0
---

# Functional Programming

&#x20;Funcional Programming이란?&#x20;

&#x20;Functional Programming은 순수 함수로 접근하는 것으로 공유 상태(pure functions) , 불변 데이터(mutable data) , 사이드 임펙트(side impact)를 피할 수 있다.&#x20;

&#x20;Functional Programing은 선언형 프로그램(Imperative Programming)이다.

&#x20;OOP(Object Oriented Programming)과 대비 된다.

filter, map, reduce와 같은 메서드를 활용하는 것이 대표적인 Functional Programming의 예



## &#x20;Functional Programming에 대해서 알아둬야할 것&#x20;

1. Pure functions
2. Function composition
3. Avoid shared state
4. Avoid mutating state
5. Avoid side effects

### &#x20;Pure functions(순수 함수)

* Input과 Output이 항상 같아야 한다.
* side effects가 없어야 한다.

### &#x20;Function composition(함수 합성)

* &#x20;[http://tutorials.jenkov.com/java-functional-programming/functional-composition.html](http://tutorials.jenkov.com/java-functional-programming/functional-composition.html)
* &#x20;하나의 함수에 여러 함수를 결합하는 기술.

### Avoid Shared state

&#x20;공유 되는 데이터를 피하라.

&#x20; 꼭 ㅣ지켜야한다.

### Avoid Mutating state

&#x20;생성 후 그 상태를 바꿀 수 있는 것을 피하라.&#x20;

### Avoid Side effects

&#x20;사이드 임팩트를 피하라.

### &#x20;함수의 종류&#x20;

&#x20;1\. 순수한 함수(pure function) 부작용(side-effect)이 없는 함수,&#x20;

&#x20;함수의 실행이 외부에 영향을 끼치지 않는 함수를 뜻한다.

&#x20;2\. 익명 함수(anonymous function)

&#x20;이름이 없는 함수.  x -> x \* x

&#x20;3\. 고계 함수(higer-order function)

&#x20;함수를 다루는 함
