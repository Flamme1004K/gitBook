# SpringData-JPA에서 Save, SaveAll

&#x20;**SpringData JPA에서 다중 Insert를 할때 각각에 대해 Save()를 하거나 SaveAll()을 한다.**

{% hint style="warning" %}
Spring Data-JPA에서 저장 방식에 대해서 Save와 SaveALL이 있다.

다중 Insert를 할때 Save를 각각 하게되면 각각에 트랜잭션이 넘어간다. 하지만 SaveALL은 하나의 트랜잭션에서 이루어 진다.
{% endhint %}

Save()와 SaveAll()을 할때 간혹 select를 하고 Insert를 할때가 있다.&#x20;

Save(), SaveAll()을 할때 PersistsTable에서 구현 엔티티가 신규인지 아닌지에 대해서 검증을 한다. 이때  검증을 하기 위해서 isNew() 함수가 실행이 되는데, 이때 false면 새로운 데이터에 대하여 select를 해온다.

하지만 대용량 데이터에 관하여 100건 1000건 새로운 데이터를 saveAll()을 통하여 넣게 된다면, 그만큼 신규라고 인식하고 100건, 1000건 select를 할 것 이다.&#x20;

**그렇다면 select를 없애는 방향은 무엇이 있을까? **

간단하다. isNew()함수를 항상 True로 반환하면 된다.

&#x20;방법은 아래와 같다.

&#x20;예제는 저번에 쓴 GroupMember이다.

```
public class GroupMember implements Persistable<GroupMemberId>{

    @EmbeddedId
    private GroupMemberId groupMemberId
    
    @ManyToOne(fetch = FetchType.LAZY)
    @MapsId("gid")
    @JoinColumn("id")
    private Group group
    
    @OneToOne
    @JoinColumn(name="g_id", updateable = false, insertable = false)
    private UserProfile userProfile
    
    @OneToOne
    @JoinColumn(name="g_id", updateable = false, insertable = false)
    private UserInfo userInfo
    
    @Override
    public boolean isNew() {
        return true;
    }

    @Override
    public GroupMemberId getId() {
        return this.groupMemberId;
    }

}
```

&#x20; 1\. implements PersistTable\<PK> 를 상속 받는다.

&#x20; 2\. isNew() 함수와 getId() 함수가 @override가 된다.&#x20;

&#x20; 3\. 여기서 getId의 return은 PK를 바라보게 만들면되고, isNew() 함수의 return값을 true로 주면 된다.

&#x20;  이렇게 해준다면 새로운 값에 대한 save(), saveAll()에 대하여 Insert시 select를 안하고 transaction이 끝나면 Insert를 할 것이다.
