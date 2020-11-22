# Spring MVC - Field Injection & Constructor Injection



```text
@Component
public  class FieldInjection {
    @Autowired
    private BService AService;
    @Autowired
    private AService BService;
}
```

잘보면 Autowired를 필드에 해주는 경우가 많다.

하지만 Spring에서는 필드에다가 injection을 해주는 것을 그렇게 썩... 좋게 생각하지 않는다고 한다.

그러면 어떻게 의존성을 주입할까?

바로 Constructor Injection을 하면 된다.

```text
@Component
public class ConstructorInjection {
     private final AService AService;
     private final BService BService;

    @Autowired
    public ConstructorInjection(AService AService,
                BService BService) {
         this.AService = AService;
         this.BService = BService;
    }
}
```

또한 4.3 이상이고 단일 생성자이면 자동적으로 맵핑이 된다고 한다.

그러면 Contructor Injection의 장점은 무엇일까?

그 이유는

**1. 단일 책임의 원칙**

생성자의 인자가 많을 경우 코드량도 많아지고, 의존관계도 많아져 단일 책임의 원칙에 위배된다. 그래서 Constructor Injection을 사용함으로써 의존관계, 복잡성을 쉽게 알수 있어 리팩토링의 단초를 제공하게 된다.

**2. 테스트 용이성**

DI 컨테이너에서 관리되는 클래스는 특정 DI 컨테이너에 의존하지 않고 POJO여야 한다. DI 컨테이너를 사용하지 않고도 인스턴스화 할 수 있고, 단위 테스트도 가능하며, 다른 DI 프레임 워크로 전환할 수도 있게 된다.

**3. Immutability**

Constructor Injection에서는 필드는 final로 선언할 수 있다. 불변 객체가 가능한데 비해 Field Injection은 final는 선언할 수 없기 때문에 객체가 변경 가능한 상태가 된다.

**4. 순환 의존성**

Constructor Injection에서는 멤버 객체가 순환 의존성을 가질 경우 BeanCurrentlyInCreationException이 발생해서 순환 의존성을 알 수 있게 된다.

**5. 의존성 명시**

의존 객체 중 필수는 Constructor Injection을 옵션인 경우는 Setter Injection을 활용할 수 있다.

이라고 [https://www.mimul.com/blog/di-constructor-injection/](https://www.mimul.com/blog/di-constructor-injection/)에 써져있다.

앞으로 Contructor Injection을 사용해봐야되겟다.

추가로 여기 블로그에는 Lombok으로 생성자주입 설명도 있다.

```text
1. Spring 4.3 이상

@RequiredArgsConstructor
@Component
public class ConstructorInjection {
    @NonNull
    private final LoginService loginService;
    @NonNull
    private final SignupService signupService;
}
2. Spring 4.3 이전

@RequiredArgsConstructor(onConstructor = @__(@Autowired))
@Component
public class ConstructorInjection {
    @NonNull
    private final LoginService loginService;
    @NonNull
    private final SignupService signupService;
}
```

