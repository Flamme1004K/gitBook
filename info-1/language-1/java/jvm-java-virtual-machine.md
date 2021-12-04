---
description: '2021-01-22'
---

# JVM(Java Virtual Machine)

## 좀 더 자세한 JVM의 원리를 알아보자. 기원알아보는게 좋다고한다.&#x20;

## JVM이란 ?&#x20;

&#x20;**** JRE(Java Runtime Environment)에서 실행되어 컴파일 된 Java ByteCode를 실행시켜주는 가상머신

**JRE 이란?**

{% embed url="https://www.redhat.com/ko/topics/cloud-native-apps/what-is-a-Java-runtime-environment" %}

## JVM 의 구조

&#x20;

![https://jeong-pro.tistory.com/148](<../../../.gitbook/assets/image (22).png>)

&#x20;PC register : Thread 명령과 address 저장

&#x20;Native Method Stack : Java 외 작성된 코드를 저장하기 위한 영역 (C/C++)

## JVM 의 실행 순서

### &#x20;JRE 실행 시 JVM Runtime Data Area 적재&#x20;

1. 자바 컴파일러가 .Java 파일을 컴파일하면 .class 파일이 생성된다.
2. .class 파일들은 Class Loader를 통하여 Runtime Data Area 형태로 메모리에 적재 된다.&#x20;
   1. &#x20;Runtime Data Area는 Class Loader에서 통하여 적재 Class 저장소이다.
3. Class에 있는 Method 및 Class Variable은 Method Area에 들어가게 된다.
   1. &#x20;Method인 경우 class -> library -> class path 순으로 찾아서 검증되어 검증 완료되면 Method Area로 들어가게 된다.
   2. Class Variable는 Reference Variable 만 저장되고 그 후 Heap에 선언되는 instance Variable의 연결고리가 된다.

### &#x20; Method 호출&#x20;

1. Thread가 할당되면 Stack Area는 각 Thread 마다 하나씩 존재하게 된다.
2. 해당 Thread에서 Method가 호출할 때마다 Stack Area에 쌓이게 된다.
   1. Method 안의 basic type variable는 Stack Area에 직접 값을 가지게 된다.
   2. primitive type variable는 Heap Area나 Method Area에 값을 가지게 된다.
3.  &#x20;이렇게 쌓여진 Method는 끝날 때 마다 하나씩 제거되며 Garbage Collection의 Mark and Sweep

    발생 시 Heap Area에 있는 객체가 Stack Area에서 참조 할 수 없는 경우에는 해당 객체를 제거.

&#x20;**Heap Area란 ?**&#x20;

&#x20;     JVM에서 런타임 시 동적 데이터를 저장하기 위한 영역이다. Instance Variable을 저장, Stack Area의 variable, diffrent Object field에 참조한다.

&#x20; Garbage Collection의 Mark and Sweep 시에 참조하는 variable이 없을 시 해당 variable, field는 삭제됨.&#x20;

## Garbage Collection 이란?

&#x20;사용되지 않는 오브젝트들을 삭제.&#x20;

&#x20;Garbage Collection은 Mark and Sweep을 하며 사용하는 Variable이나 field는 Mark하고 Mark가 안된 Variable이나 filed는 Sweep 한다. 즉 제거하는 것이다.

&#x20;Garbage Collection은 Eden -> survivor1 -> survivor2 -> new -> old 으로 나뉘며 해당 순서대로 Mark and Sweep(이때 Sweep는 Minor GC가 한다.) 하며 결국 끝까지 남는 variable이나 field는 Old로 들어가버린다.&#x20;

&#x20;Old에 들어간 variable이나 field가 계속적 적재되면 결국은 Major GC가 해당 객체들을 다 삭제합니다.

이때 모든 Thread(GC 제외)는 정지되어 프로그램의 응답속도가 느려진다.&#x20;

&#x20;Eden, survivor1,  survivor2, new는 Minor GC가 실행, old 는 Major GC를 실행

{% embed url="https://dzone.com/articles/memory-leak-andjava-code" %}



&#x20;
