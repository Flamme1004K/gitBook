---
description: 이 글은 최범균 저자님의 DDD-START의 정리입니다.
---

# Chapter5 리포지터리의 조회 기능

* 스펙
* JPA 스펙 구현
* 정렬과 페이징
* 동적 인스턴스와 @SubSelect

## 검색을 위한 스펙

리포지터리는 애그리거트의 저장소이다.

애그리거트를 저장하고 찾고 삭제하는 것이 리포지터리의 기본 기능이다.

애그리거트를 찾을 때 식별자를 이용하는 것이 기본이지만 식별자 외에 여러 다양한 조건으로 애그리거트를 찾아야 할 때가 있다.

만약 검색 조건의 조합이 다양해지면 모든 조합별로 find 메서드를 정의할 수 없어 검색 조건이 많을 수록 스펙을 이용해서 문제를 풀어야 한다.

스펙\(Specification\)은 애그리거트가 특정 조건을 충족하는지 여부를 검사한다.

### 스펙 조합

스펙의 장점은 조합에 있다.

두 스펙을 AND 연산자나 OR 연산자로 조합해서 새로운 스펙을 만들 수 있고, 조합한 스펙을 다시 조합해서 더 복잡한 스펙을 만들 수 있다.

## JPA를 위한 스펙 구현

실제 구현에서는 쿼리의 where 절에 조건을 붙여서 필요한 데이터를 걸러야 한다.

이는 스펙 구현도 메모리에서 걸러내는 방식에서 쿼리의 where를 사용하는 방식으로 바꿔야 한다는 것을 뜻한다.

JPA는 다양한 검색 조건을 조합하기 위해 CriteriaBuilder와 Predicate를 사용하므로 JPA를 위한 스펙은 CriteriaBuilder와 Predicate를 이용해서 검색 조건을 구해야 한다.

### JPA 스펙 구현

### 스펙을 사용하는 JPA 리포지터리 구현

### 정렬 구현

### 페이징과 개수 구하기 구현

### 조회 전용 기능 구현

