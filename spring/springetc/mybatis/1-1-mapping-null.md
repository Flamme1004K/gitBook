---
description: 2020/07/17
---

# 1:1 Mapping시 Null

```text
<resultMap id=”get-product-result” class=”com.ibatis.example.Product”>
<result property=”id” column=”PRD_ID”/>
<result property=”description” column=”PRD_DESCRIPTION”/>
<result property=”category.id” column=”CAT_ID” />
<result property=”category.description” column=”CAT_DESCRIPTION” />
</resultMap>
<statement id=”getProduct” parameterClass=”int” resultMap=”get-product-result”>
select * from PRODUCT, CATEGORY where PRD_CAT_ID=CAT_ID and PRD_ID = #value#
</statement>
```

category.id를 넣었는데 null값이 들어가는 이유는 뭘까?

```text
public class Product{
    private String id;
    private String description;
    private Category category;
    ...
}
```

여기서 문제가 있다.

바로 Category에 대한 새로운 생성자를 만들어 주지 않았기 때문이다.

```text
public class Product{
    private String id;
    private String description;
    private Category category = new Category();
    ...
}
```

 이렇게 되어야한다.

 내 2시간..

Ref. [http://ibatis.apache.org/docs/java/pdf/iBATIS-SqlMaps-2\_ko.pdf](http://ibatis.apache.org/docs/java/pdf/iBATIS-SqlMaps-2_ko.pdf)

