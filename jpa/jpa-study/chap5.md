---
description: 이 글은 김영한님의 jpa책을 보고 공부한 흔적입니다.
---

# Chap5

## JPA-Cha5 연관관계매핑기초

객체는 참조\(주소\)를 사용해서 관계를 맺고 테이블은 외래 키를 사용해서 관계를 맺는다.

`객체 관계 매핑(ORM)에서 가장 어려운 부분이 바로 객체 연관관계와 테이블 연관관계를 매핑하는 일이다.`

객체의 참조와 테이블의 외래 키를 매핑하는 것이 이 장의 목표이다.

연관관계 매핑 핵심 키워드

* 방향 
  * 단방향 
    * 회원 -&gt; 팀 or 팀 -&gt; 회원 둘 중 하나만 참고 하는 것
  * 양방향
    * 회원 -&gt; 팀, 팀 -&gt; 회원 모두 서로 참조하는 것
* 다중성
  * 다대일\(N:1\), 일대다\(1:N\), 일대일\(1:1\), 다대다\(N:M\)
  * 회원과 팀의 관계는 다대일
  * 팀과 회원은 일대다 관계
* 연관관계의 주인
  * 객체를 양방향 연관관계로 만들면 연관관계의 주인을 정해야 한다.

### 단방향 연관관계

`연관관계 중에선 다대일(N:1) 단방향 관계를 우선 이해해야 한다.`

예.

* 회원과 팀이 있다.
* 회원은 하나의 팀에만 소속될 수 있다.
* 회원과 팀은 다대일 관계다.

!객체 연관관계

* 회원 객체로 팀 객체와 연관관계를 맺는다.
* 회원 객체와 팀 객체는 단방향 관계다.
  * 회원은 팀을 알 수 있지만, 팀은 회원을 알 수 없다.

테이블 연관관계

* 회원 테이블은 Team\_ID를 외래키로 팀 테이블과 연관관계를 맺는다.
* 회원 테이블과 팀 테이블은 양방향 관계다.

객체 연관관계 vs 테이블 연관관계 정리

* 객체는 참조\(주소\)로 연관관계를 맺는다.
* 테이블은 외래 키로 연관관계를 맺는다.

`즉 객체는 참조를 사용하지만 테이블은 조인을 사용한다.`

* 참조를 사용하면 연관관계는 단방향이다.
* 외래 키를 사용하는 테이블의 연관관계는 양방향이다.

  순수한 객체 연관관계: 168페이지 참고

  테이블 연관관계: 169페이지 참고

  객체 관계 매핑

```text
@Entity 
public class Member {
    @Id 
    @Column(name= "MEMBER_ID") 
    private String id;
    private String username;
    
    //연관관계 매핑 
    @ManyToOne // 다대일 관계라는 매핑정보 
    @JoinColumn(name="TEAM_ID") // 조인 컬럼은 외래 키를 매핑할 떄 사용한다. 
    private Team team;

public void setTeam(Team team) { this.team = team; } 
}

@Entity 
public class Team { @Id @Column(name = "TEAM_ID") private String id;
    private String name;
}
```

* 객체 연관관계 : 회원 객체의 Member.team 필드 사용
* 테이블 연관관계 : 회원 테이브르이 MEMBER.TEAM\_ID 외래 키 컬럼을 사용

Member.team과 MEMBER.TEAM\_ID를 매핑하는 것이 연관관계 매핑이다.

#### @JoinColumn

* name : 매핑할 외래 키 이름
* referencedColumnName : 외래 키가 참조하는 대상 테이블의 컬럼명
* foreignKey\(DDL\) : 외래 키 제약조건을 직접 지정할 수 있다.
* unique, nullable, isertable, updateble, columnDefinition, table

#### @ManyToOne

* optional : false로 설정하면 연관된 엔티티가 항상 있어야 한다.
* fetch : 글로벌 패치 전략
* cascade : 영속성 전이 기능
* targetEntity : 연관된 엔티티의 타입 정보를 설정한다.

`targetEntity속성 : 제네릭으로 타입 정보를 알 수 있다. targetEntity=Member.class인 경우 List에 <>를 안써도 된다.`

### 연관관계 사용

연관관계를 등록, 조회, 수정, 삭제 하는 예제 P173 참고

* 등록 
* 조회
  * 객체 그래프 탐색\(객체 연관관계를 사용한 조회\)
    * member.getTeam\(\)을 사용해서 member와 관련된 team 엔티티 조회
  * 객체지향 쿼리 사용\(JPQL\)
    * SQL과 JPQL을 비교하면 JQL은 객체\(엔티티\)를 대상으로 하고 SQL보다 간결하다.
    * 10장에서 다룬다.
* 수정
* 삭제
  * setTeam\(null\); //연관관계 제거

연관된 엔티티 삭제

`연관된 엔티티를 삭제하려면 기존에 있던 연관관계를 먼저 제거하고 삭제해야 한다. 그렇지 않으면 외래 키 제약조건으로 인해 데이터베이스에서 오류가 발생한다.`

```java
Team team = member.getTeam(); // 객체 그래프 탐색

member1.setTeam(null); //회원1 연관관계 제거
member2.setTeam(null); //회원2 연관관계 제거
em.remove(team); //팀삭제
```

#### 양방향 연관관계

회원에서 팀으로 접근하고 반대 방향인 팀에서 회원으로 접근할 수 있도록 양방향 연관관계에 대해서 말해보자.

회원과 팀은 다대일, 팀에서 회원은 일대다 관계다.

객체 연관관계 :

* 회원 -&gt; 팀 \(Member.team\)
* 팀 -&gt; 회원 \(Team.members\)

테이블의 관계 :

* 외래 키 하나로 양방향으로 조회

```java
@Entity
public class Member {
    @Id
    @Column(name= "MEMBER_ID")
    private String id;

    private String username;

    @ManyToOne
    @JoinColumn(name="TEAM_ID") 
    private Team team;

    public void setTeam(Team team) {
        this.team = team;
    }
}

@Entity
public class Team {
    @Id
    @Column(name = "TEAM_ID")
    private String id;

    private String name;

    @OneToMany(mappedBy = "team")
    //mappedBy 속성 양방향 매핑일 떄 사용하는데 반대쪽 매핑의 필드 이름을 갑으로 주면 된다.
    //Member.team이므로 team을 값으로 주었다.
    private List<Member> members = new ArrayList<Member>();


}
```

#### 일대다 컬렉션 조회

```java
public void biDirection() {
    Team team = em.find(Team.class,"team1");
    List<Member> members = team.getMember();

    for (Member member : members) {
        System.out.println("member.username = " + member.getUsername()); 
    }   

}
```

### 연관관계의 주인

객체에는 양방향 연관관계라는 것이 없다.

서로 다른 단방향 연관관계 2개를 애플리케이션 로직으로 잘 묶어서 양방향 인것처럼 보이게 할뿐이다.

* 테이블은 외래 키 하나로 두 테이블의 연관관계를 관리한다.
* 엔티티를 단방향으로 매핑하면 참조를 하나만 사용
* 엔티티를 양방향 연관관계로 설정하면 객체의 참조는 둘인데 외래 키는 하나다.

  따라서 둘 사이에 차이가 발생한다. -&gt; 이러한 차이로 JPA에서는 두 객체 연관관계 중 하나를 정해서 테이블의 외래키를 관리해야 하는데 이것을 연관관계의 주인\(Owner\)이라고 한다.

#### 양방향 매핑의 규칙 : 연관관계의 주인

양방향 연관관계에서 두 연관관계 중 하나를 연관관계의 주인으로 정해야 한다.

연관관계의 주인만이 데이터베이스 연관관계와 매핑되고 외래 키를 관리\(등록, 수정, 삭제\) 할 수 있다.

반면에 주인이 아닌 쪽은 읽기만 할 수 있다.

여기서 사용되는 개념이 바로 mappedBy 속성이다.

`mappedBy : 주인을 결정하는 방법` `mappedBy를 사용하면 주인이 아님을 설정한다.`

* 주인은 mappedBy 속성을 사용하지 않는다.
* 주인이 아니면 mappedBy 속성을 사용해서 속성의 값으로 연관관계의 주인을 지정해야 한다.   

`연관관계의 주인을 정한다는 것은 사실 외래 키 관리자를 선택하는 것이다.`

#### 연관관계의 주인은 외래 키가 있는 곳

연관관계의 주인은 테이블에 외래 키가 있는 곳으로 정해야 한다.

예제에서 회원테이블이 외래 키를 가지고 있으므로 Member.team이 주인이 된다.

주인이 아닌 Team.member에는 mappedBy="team" 속성을 사용해서 주인이 아님을 설정한다.

즉 연관관계의 주인만 데이터베이스 연관관계와 매핑되고 외래 키를 관리 할 수 있다.

주인이 아닌 반대편은 읽기만 가능하고 외래키를 변경하지 못한다.

`데이터베이스 테이블의 다대일, 일대다 관계에서는 항상 다 쪽이 외래 키를 가진다.`

`다 쪽인 @ManyToOne은 항상 연관관계의 주인이 되므로 mappedBy를 설정할 수 없다. 따라서 @ManyToOne에는 mappedBy 속성이 없다.`

### 양방향 연관관계 저장

```java
public void testSave() {

    Team team1 = new Team("team", "팀1");
    em.persist(team1);

    Member member1 = new Member("member1", "회원1");
    member1.setTeam(team1);
    em.persist(member1);

    Member member2 = new Member("member2", "회원2");
    member2.setTeam(team1);
    em.persist(member2);




}
```

### 양방향 연관관계의 주의점

연관관계의 주인에는 값을 입력하지 않고, 주인이 아닌 곳에만 값을 입력하는 것

#### 순수한 객체까지 고려한 양방향 연관관계

객체 관점에서 양쪽 방향에 모두 값을 입력해주는 것이 가장 안전한다.

```java
public void test순수한객체_양방향() {
    Team team1 = new Team("team1", "팀1");
    em.persist(team1);

    Member member1 = new Member("member1", "회원1");
    member1.setTeam(team1);
    team1.getMembers().add(member1); 
    em.persist(member1);

    Member member2 = new Member("member2", "회원2");
    member2.setTeam(team1);
    team1.getMember().add(member2);
    em.persist(member2);

    List<Member> members = team1.getMembers();
    System.out.println("members.size = " + members.size());
}
```

`member1.setTeam(team1); // 연관관계의 주인` `team1.getMembers().add(member1); // 주인이 아니다. 저장 시 사용되지 않는다.`

-&gt; 객체까지 고려해서 주인이 아닌 곳에도 값을 입력하자.

결론 : 객체의 양방향 연관관계는 양쪽 모두 관계를 맺어주자.

```java
public class Member { 
    private Team team;


    public void setTeam(Team team) {
        //삭제되지 않는 관계가 있기 때문에 연관관계를 변경할 떄는 기존 팀이 있으면 기존 팀과 회원의 연관관계를 삭제하는 코드를 추가해야 한다.
        if (this.team != null) {
            this.team.getMembers().remove(this);
        }
        this.team = team;
        team.getMember().add(this);
    }   
}

public void test순수한객체_양방향2() {
    Team team1 = new Team("team1", "팀1");
    em.persist(team1);

    Member member1 = new Member("member1", "회원1");
    member1.setTeam(team1);
    em.persist(member1);

    Member member2 = new Member("member2", "회원2");
    member2.setTeam(team1);
    em.persist(member2);

    List<Member> members = team1.getMembers();
    System.out.println("members.size = " + members.size());
}
```

`객체에서 양방향 연관관계를 사용하려면 로직을 견고하게 작성해야 한다.`

양방향의 장점 : 반대방향으로 객체 그래프 탐색 기능이 추가된 것.

`주인의 반대편은 항상 mappedBy로 주인을 지정해야 한다.`

* 단방향 매핑만으로 테이블과 객체의 연관관계 매핑은 이미 완료되었다.
* 단방향을 양방향으로 만들면 반대방향으로 객체 그래프 기능이 추가된다.
* 양방향 연관관계를 매핑하려면 객체에서 양쪽 방향을 모두 관리해야 한다.

`연관관계의 주인 : 외래 키의 위치와 관련해서 정해야지 비즈니스 중요도로 접근하면 안 된다.`

