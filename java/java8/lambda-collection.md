# lambda - Collection



```text
final List<String> friends = Arrays.asList("Brian", "Nate", "Neal", "Raju", "Sara", "Scott");

for (int i = 0; i < friends.size(); i++) {
    System.out.println(friends.get(i));
}

System.out.println("============ for ============>");

for(String name : friends) {
    System.out.println(name);
}

System.out.println("============ foreach ============>");

friends.forEach(new Consumer<String>() {
    @Override
    public void accept(String name) {
        System.out.println(name);
    }
});

System.out.println("============ anonymous for(lambda)1 ============>");

friends.forEach((final String name ) -> System.out.println(name));

System.out.println("============ anonymous for(lambda)2 ============>");

friends.forEach((name) -> System.out.println(name));

System.out.println("============ anonymous for(lambda)3 ============>");

friends.forEach(name -> System.out.println(name));

System.out.println("============ anonymous for(lambda)4 ============>");

friends.forEach(System.out::println);

System.out.println("============ upperCaseList for ============>");

final List<String> uppercaseNames = new ArrayList<String>();

for(String name : friends) {
    uppercaseNames.add(name.toUpperCase());
}

System.out.println("============ upperCaseList for ============>");

final List<String> uppercaseName2 = new ArrayList<String>();
friends.forEach(name -> uppercaseName2.add(name.toUpperCase()));
System.out.println(uppercaseName2);

friends.stream()
        .map(name -> name.toUpperCase())
        .forEach(name -> System.out.println(name + " "));

System.out.println();

friends.stream()
        .map(name ->name.length())
        .forEach(name -> System.out.println(name + " "));
//람다 표현식 버전은 명시적 변경(explicit mutation)이 없으며 간결하다.
//더 이상 비어 있는 컬렉션의 초기화 작업이나 가비지 변수가 필요하지 않다.
//이러한 작업에 필요한 변수들은 내부 라이브러리에 맡겨두면 된다.

friends.stream()
        .map(String::toUpperCase)
        .forEach(System.out::println);
//자바는 String 클래스에서 제공하는 메서드인 toUpperCase()를 호출한다.
//이 메서드는 합성된 메서드에 전달된 파라미터이며 함수형 인터페이스의 추상(abstract) 메서드 구현에 해당된다.
//String::toUpperCase라는 파라미터 레퍼런스가 암묵적으로 제공된다.

//메서드 레퍼런스를 언젲 사용해야하는가?
//메서드 레퍼런스는 인스턴스 메서드를 위해서 사용된다.
//메서드 레퍼런스는 또한 정적 메서드를 참조하며 파라미터를 갖는다.
//람다 표현식을 사용하면 컬렉션을 나열하고 새로운 컬렉션으로 변경할 수 있다.
//또한 컬렉션에서 하나의 엘리먼트를 선택하는 기능도 간단하게 할 수 있다.

//엘리먼트 찾기
final List<String> startWithN = new ArrayList<String>();
for(String name : friends) {
    if(name.startsWith("N")){
        startWithN.add(name);
    }
}

final  List<String> startWithN2 = friends.stream()
        .filter(name -> name.startsWith("N"))
        .collect(Collectors.toList());

//filter() 메서드는 boolean 결과를 리턴하는 람다표현식이 필요하다.
//결과를 collect()메서드를 사용하면 리스트로 변경한다.

System.out.println(String.format("Found %d names", startWithN2.size()));

//filter() 메서드는 map() 메서드처럼 이터레이터를 리턴하지만 그 외에는 비슷한 부분이 거의 없다.
//map() 메서드가 입력과 같은 크기의 컬렉션을 리턴하는 반면에 filter()메서드는 그렇지 않다.
//0부터 최대 입력 컬렉션에 있는 엘리먼트의 수만큼의 결과 컬렉션을 리턴할 수 있다.
//map()과 달리 filter()가 리턴한 결과 컬렉션의 엘리먼트는 입력 컬렉션에 있는 엘리먼트의 서브셋이다.
//즉, filter() 메서드에 의해 처리된 결과 컬렉션의 엘리먼트들은 모두 입력 컬렉션의 엘리먼트들 중 하나라는 의미다.


```

