---
description: Mybatis에 대해서 궁금한 점
---

# Mybatis

 2020/07/16 

Mybatis에서 auto\_Increment selectKey를 사용했을 때 동일한 데이터가 들어가서 충돌이 나지 않을까?

```text
<insert id="insertSurveyInfo" parameterType="Board">
    INSERT INTO board(boardID, title, content)
    VALUES(#{boarID}, #{title}, #{content})
    <selectKey resultType="int" keyProperty="iq" order="AFTER">
        SELECT LAST_INSERT_ID()
    </selectKey>        
</insert>
```

 SELECT LAST\_INSERT\_ID\(\)를 쓰면 되지만 이게 쓰레드 환경에서 과연 안정할까?

{% embed url="https://stackoverflow.com/questions/30959678/thread-safety-of-mysqls-select-last-insert-id" %}

여기에 답변이 나와있다. 

'The ID that was generated is maintained in the server on a _per-connection basis_. This means that the value returned by the function to a given client is the first AUTO\_INCREMENT value generated for most recent statement affecting an AUTO\_INCREMENT column _by that client_. This value cannot be affected by other clients, even if they generate AUTO\_INCREMENT values of their own. This behavior ensures that each client can retrieve its own ID without concern for the activity of other clients, and without the need for locks or transactions.

[https://dev.mysql.com/doc/refman/5.5/en/information-functions.html\#function\_last-insert-id](https://dev.mysql.com/doc/refman/5.5/en/information-functions.html#function_last-insert-id):

생성된 ID는 접속별로 서버에서 유지 관리된다. 즉, 기능에 의해 주어진 클라이언트에 반환된 값이 해당 클라이언트에 의해 AUTO\_INCREMENT 열에 영향을 미치는 가장 최근의 문장에 대해 생성된 첫 번째 AUTO\_INCREMENT 값임을 의미한다. 이 값은 다른 클라이언트가 자체 AUTO\_INGment 값을 생성하더라도 영향을 받을 수 없다. 이러한 행동은 각 클라이언트가 다른 클라이언트의 활동에 대해 염려하지 않고, 잠금이나 거래의 필요 없이 자신의 ID를 검색할 수 있도록 보장한다.

--&gt; 즉, ID를 생성해서 유지하는 것은 컨넥션 별로 따로 관리되며, 각각의 클라이언트가 정확한 수치를 가져오기 위해 테이블에 락을 걸거나 트랜잭션을 사용 할 필요가 없다는 것이다. 고로, 그냥 연달아 쿼리 날려주면 된다.

라 다른 클라이언트는 영향을 받지 않는다고 한다. 걱정 말고 쓰도록 하자!

