---
description: '[2021-01-07]'
---

# QueryDSL N+1 문제\(@OneToOne\)

 

![](../../../../.gitbook/assets/image%20%2820%29.png)



```text
public List<GroupMember> selectGroupMemberList() {
        return queryFactory
                .selectFrom(groupMember)
                .leftJoin(groupMember.userInfo, userInfo).fetchJoin()
                .join(groupMember.userProfile, userProfile).fetchJoin()
                .where(groupMember.groupMemberId.gId.eq(32L)).fetch();
    }
```

 

**User Entity와 UserProfiles, UserInfo는 양방향으로 되어있고, GroupMember와 UserProfiles, UserInfo는 단방향으로 되어있었다.**

**하지만 위에 같이 QueryDSL을 썻을 경우 계속 User에 대한 정보가 N+1 형태로 불러와졌다.**

 그 이유는 @OneToOne은 양방향일 경우 LazyLoading이 안된다는 것이였다.

 이것을 해결하기 위해서 일단 User Entity와 UserProfiles, UserInfo의 양방향을 단방향으로 고쳤다.

 @OneToOne 관계는 

* 부모-자식에서 `optional = false` 로 지정되어야 한다.
* 양방향이 아닌 단방향 관계여야한다.
  * **부모가 자식의 key를 가져야 한다**.
* [@PrimaryKeyJoin](https://github.com/PrimaryKeyJoin)이면 안된다.

며 OneToOne 관계는 잘 생각해봐야 한다.





