# QueryDSL

JPA Crietria를 통해 문자가 아닌 코드로 JPQL을 작성하면 문법 오류를 컴파일 단계에서 잡을 수 있는 등 여러 장점이 있다. 

하지만 JPA Creiteria는 너무 장황하고 복잡하다 ! 그렇기에 비슷한 역할을 하는 QueryDSL은 코드 기반의 장점을 포함하면서 비교적 단순하다.

QueryDSL도 Criteria와 같이 **JPQL 빌더 역할을 한다.**

## Q Class

QueryDSL 을 사용하기 위해서는 Criteria의 'Meta Model' 처럼 Entity 기반으로 쿼리 타입이라는 쿼리용 클래스 (Q Class)를 사용한다. 이는 maven (혹은 gradle)에서 빌드를 통해 생성할 수 있는데, Q로 시작하는 새로운 쿼리 타입들이 생성된다

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String login;

    private Boolean disabled;

    @OneToMany(cascade = CascadeType.PERSIST, mappedBy = "user")
    private Set<BlogPost> blogPosts = new HashSet<>(0);

    // getters and setters

}
```

위와같은 User 엔티티가 있을 때 QueryDsl에서 사용되는 쿼리타입 클래스 'QUser.class'가 자동 생성된다.

```java
@Generated("com.querydsl.codegen.EntitySerializer")
public class QAdgroup extends EntityPathBase<User> {

    private static final long serialVersionUID = 40215328711L;

}
```


