---
description: QueryDsl
---

# Chap10-3



````
#QueryDSL

##QueryDSL이란?

- 오픈소스 프로젝트이다.
- 데이터를 조회하는데 기능이 특화되어 있다.

QueryDSL을 사용하려면 우선 com.mysema.query.jpa.impl.JPAQuery 객체를 생성해야 하는데 이때 엔티티 매니저를 생성자에 넘겨준다.

다음으로 생성하는 생성자에게 별칭을 주면 된다.

```java
public void queryDSL() {
    
    EntityManager em = emf.createEntityManager();
    
    JPAQuery query = new JPAQuery(em);
    QMember qMember = new QMember("m");
    List<Member> members = 
        query.from(qMember)
            .where(qMember.name.eq("회원1"))
            .orderBy(qMember.name.desc())
            .list(qMember);
    
}

select m from Member m
where m.name = ?1
order by m.name desc 
//m --> 별칭이다.
```

````

 
