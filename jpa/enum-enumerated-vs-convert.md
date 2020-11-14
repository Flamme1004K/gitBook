# Enum\(@enumerated vs @convert\)

{% embed url="https://thorben-janssen.com/jpa-21-type-converter-better-way-to/" %}

Persisting enums with JPA 2.0 is possible, but there is no nice way to do it. Using the @Enumerated annotation, you can use EnumType.ORDINAL or EnumType.STRING to map the enum value to its database representation. But both options have their drawbacks. The ordinal of an Enum depends on the ordering of its values and can create problems, if we need to add new ones. The String representation of an Enum is often quite verbose and renaming a value will break the database mapping. These drawbacks can be avoided by using an Attribute Converter.

ORDINAL로 설정 후 Gender enum 타입이 변경된다면 예기치 못한 문제가 발생할 수 있다.

ORDINAL은 서수로 표현되기 때문이다 

```text
enum test {
    male, female // 0,1 로 각각 저장됨. 
}
```

STRING 설정은 문자열 자체가 저장되기 때문에 DB 공간 낭비가 발생한다.

 male, female 자체로 저장이 된다.

 그래서 서수와 String을 직접적으로 들어가는 것을 피하기 위해 @Convert를 사용한다.

{% embed url="https://woowabros.github.io/experience/2019/01/09/enum-converter.html" %}

 이걸 이용하면 될 듯하다.



