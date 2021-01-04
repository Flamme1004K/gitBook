---
description: 2020/07/16 @Transactional에 대해 궁금증
---

# @Transactional

 1. @Transactional에서 예외가 발생했을 때 롤백 시킬 수 있을까?

```text
@Transactional(rollbackFor = Exception.class)

출처: https://offbyone.tistory.com/405 [쉬고 싶은 개발자]
```

rollbackFor을 쓰면 된다고 한다.



