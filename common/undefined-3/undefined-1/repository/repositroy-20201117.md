# Repositroy\(20201117\)

Repositroy 리뷰

현재 진행 사항 1. Repositroy 패키지에 ezTalk2 Table Entity 생성 2. MSSQL 연결 테스트.

테스트 사항

1. TestDB에 현재 테이블의 구조가 만들어지는지 확인.
2. ezTalk2 연결 테스트 \(User에 대한 간단한 정보 Insert\)

Entity Class 생성

1. 데이터 타입은 primitive와 Wrapper 중에 Wrapper를 사용.

    이유 : null 값 처리가 용이하기하여 SQL과 연동처리가 용이하기 때문.

2. @Column Annotation을 사용하여 MSSQL DB 데이터 형식 및 Null Check --&gt; DB에서 변경 해야하기 떄문에 관련 @ 삭제
3. compositeKey를 가진 클래스\(Device, Resigner, SvrCompany, Message, RoomMember, ConcurrentJob, GroupMember는

@IdClass와 @EmbeddedId 중에 EmbeddedId를 사용 선택 이유 :

@IdClass는 데이터베이스를 관점 @EmbeddedId는 객체지향 관점 이라고 하여 객체지향 관점으로 바라보기 위하여 EmbeddedId 선택.

1. DB dateTime은 모두 LocalDateTime 클래스 사용.
2. Message, Memo, Room에 대한 regDate 및 updateDate에 대한 date 추가 및 변경은 @CreationTimeestamp와 @UpdateTimestamp 사용.

   이유 : 

   date 추가 변경에 대한 전략은 auditing 설정에 대한 것과 

   CreationTimeestamp, UpdateTimestamp와 같은 hibernate Annotation이 있다.

   둘다 사용해도 상관 없고 설정이 덜 필요한 hibernate Annotation 사용

--&gt; UpdateTimestamp 사용 고려.

1. flag에 대한 처리는 아직 명확하지 않아 Boolean Type 혹은 Character로 데이터 타입으로 선언 s,Y -&gt; BOOLEAN 

   DeviceType인 경우 Android냐 IPHONE인가에 대한 처리가 필요하기 때문에 Enum으로 관리.

   추후에도 관리가 필요한 flag값은 enum으로 처리하려고 함.

혹여나 enum이 key value로 관리 그리고 숫자로 관리가 되어야하면 Enumurate.String, Enumrate.Ordinary 어노테이션 사용보다는 @Convert 전략을 사용하여 처리하려고함.

이유 : Ordinary는 서수로 표현, String은 문자열 자체가 저장되어 DB 공간 낭비가 발생하기 때문에 @Convert를 통하여 서수 및 직접적으로 들어가는 것을 피함.

1. JPA에서는 프록시 객체가 필요하므로 기본 생성자를 각 entity.class마다 생성

   @NoArgsConstructor\(access = AccessLevel.PROTECTED\)로 낮은 접근지시자 사용.

차후 진행 사항

1. Service 클래스 생성 및 Legacy 코드 분석 후 개선하여 구현
2. 단방향, 양방향 연관관계처리는 Service 클래스를 구현하며 진행
3. Log 처리 \(AOP, slf4j, LogBack\) spring log wrapper

   aop는 Debug에서도 가능한지 알아보기

User와 UserInfo, UserProfile의 관계

대상 테이블에 외래 키가 있는 경우.

주 테이블에 외래 키 : 외래 키를 객체 참조와 비슷하게 사용할 수 있어서 객체지향 개발자들이 선호. 대상 테이블에 외래 키 : 전통적인 데이터베이스 개발자가 선호. 장점 : 테이블 관계를 일대일에서 일대다로 변경할 때 테이블 구조를 그대로 유지

대상 테이블에 외래 키에 대한 단방향 연관관계를 JPA에서는 허용하지 않음. 그래서 양방향 연관관계를 줘야함.

