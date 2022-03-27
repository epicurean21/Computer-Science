# JPA의 N+1 select 문제와 해결 방법

JPA의 대표적으로 발생하는 문제인 n+1 select 문제가 무엇인지 알아보고, 해결 방안을 생각해보자



##### Cat Entity

```java
public class Cat {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @ManyToOne
    private Owner owner;
  
  	...
}
```

##### Owner Entity

```java
public class Owner {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "owner", fetch = FetchType.EAGER)
    private Set<Cat> cats = new LinkedHashSet<>();

  	...
}
```

Cat은 ManyToOne 관계로 집사와 관계돼 있으며, Owner는 여러마리의 고양이를 소유한다.



#### N + 1 문제란?

> 연관 관계에서 발생하는 이슈로 연관 관계가 설정된 엔티티를 조회할 경우에 조회된 데이터 갯수(n) 만큼 연관관계의 조회 쿼리가 추가로 발생하여 데이터를 읽어오게 된다. 이를 N+1 문제라고 한다.
>
> 즉, 고양이 10마리를 소유한 집사 Owner를 조회 했을 때,  (FetchType.Eager, default 상태라 가정)
>
> 집사 조회 .... select 1회
>
> 집사의 고양이 [1] 조회 ... select 2회
>
> 집사의 고양이 [2] 조회 ... select 3회
>
> 집사의 고양이 [3] 조회 ... select 4회
>
> ...
>
> 집사의 고양이 [8] 조회 ... select 9회
>
> 집사의 고양이 [9] 조회 ... select 10회
>
> 집사의 고양이 [10] 조회 ... select 11회
>
> 이런식으로 총 1 + N 번의 select 문을 날리게 된다. 이는 더 거대한 데이터 베이스의 소유주를 조회했을 때 심각한 속도 저하를 야기한다. 



#### N + 1 Select Test Code

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class NPlusOneTest {

    @Autowired
    CatRepository catRepository;
    @Autowired
    OwnerRepository ownerRepository;
    @Autowired
    EntityManager entityManager;

    @Test
    void exmapleTest() {
        Set<Cat> cats = new LinkedHashSet<>();

        for (int i = 0; i < 10; i++) {
            cats.add(new Cat("cat: " + i));
        }

        List<Owner> owners = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            Owner owner = new Owner("owner" + i);
            owner.setCats(cats);
            owners.add(owner);
        }
        ownerRepository.saveAll(owners);
        entityManager.clear();

        List<Owner> everyOwners = ownerRepository.findAll();
        Assertions.assertFalse(everyOwners.isEmpty());
    }
}
```

#### 실제 결과 log를 확인해보자

* 고양이 집사 Owner를 조회하는 Query 생성
* 고양이 집사 Owner의 소유 고양이를 조회하는 query가 row 만큼 호출된다.

```sql
Hibernate: select owner0_.id as id1_2_, owner0_.name as name2_2_ from owner owner0_
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
Hibernate: select cats0_.owner_id as owner_id3_0_0_, cats0_.id as id1_0_0_, cats0_.id as id1_0_1_, cats0_.name as name2_0_1_, cats0_.owner_id as owner_id3_0_1_ from cat cats0_ where cats0_.owner_id=?
```



### FetchType.EAGER의 문제일까?

Owner의 entity에서 Cat의 FetchType을 LAZY로 변경한 뒤 실행 해 보았다.

```sql
Hibernate: select owner0_.id as id1_2_, owner0_.name as name2_2_ from owner owner0_
```

단 하나의 select 문만이 발생했다. 즉 Owner만을 찾았다.

Lazy와 Eager의 차이를 생각해보자. Lazy로 설정돼 있으면 Default인 Eager와 다르게 Transaction이 종료 (commit) 되기 전 까지 실제 사용되지 않는 연관관계에 대해서는 쿼리를 날리지 않는다.

즉, Test code에서는 Cat에 대한 정보를 필요로 하지 않기 때문에 Owner에 대한 정보만을 select 쿼리를 통해 가져온 것이다.  그렇기 때문에 N + 1 문제를 **해결한게 아닌** 문제의 발생점을 **뒤로 늦췄을 뿐이다.**

만약 Test 코드에서 Owner의 정보를 가져온 뒤, 각 Owner의 cat 정보를 필요로 하는 code가 존재한다면, JPA는 뒤늦게 N 번만큼의 select 문을 추가로 날릴 것이다. 즉 우리가 원하는 join 문을 통한 해결이 아닌, 실제 N + 1의 문제가 뒤늦게 나타나는 것이다.



### 해결방법(들)



##### 1. Fetch Join

- N + 1이 발생하는 상황에서, 우리가 원하는 결과물은 하나의 Query [select * from owner left join cat on cat.owner_id = owner.id] 이다. 
- 즉 JPA의 전적인 도움을 빌려 N+1문제가 발생했기에 우리가 직접 Query를 작성해서 Fetch Join을 이용해 정보를 가져오는 방법이다.
- 이 방법은 JpaRepository 를 이용하는게 아닌, **JPQL**로 작성해야 한다.

```java
@Query("select o from Owner o join fetch o.cats")
List<Owner> findAllJoinFetch();
```



##### 2. EntitfyGraph

* @EntityGraph 의 attributePaths에 쿼리 수행시 바로 가져올 필드명을 지정하면 Lazy가 아닌 Eager 조회로 가져오게 된다. Fetch join과 동일하게 JPQL을 사용하여 query 문을 작성하고 필요한 연관관계를 EntityGraph에 설정하면 된다. 그리고 Fetch join과는 다르게 join 문이 outer join으로 실행되는 것을 확인할 수 있다.

  ```java
  @EntityGraph(attributePaths = "cats")
  @Query("select o from Owner o")
  List<Owner> findAllEntityGraph();
  ```



##### 3. FetchMode.SUBSELECT - [subselect fetching]

* 1 + N의 쿼리 발생을 두 번의 쿼리로 해결하는 방법이다.
* 해당 Entity를 조회하는 쿼리는 그대로 둔 뒤, 연관관계 데이터를 조회할 때 SubQuery로 함께 조회하는 방법이다 !

```java
@Entity
@Getter
@Setter
@NoArgsConstructor
public class Owner {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;

    @Fetch(FetchMode.SUBSELECT)
    @OneToMany(mappedBy = "owner", fetch = FetchType.EAGER)
    private Set<Cat> cats = new LinkedHashSet<>();

}
```

그렇다면 로그를 확인해보자 기존처럼 N + 1번의 select문을 날릴까? FetchType.Lazy 처럼 필요없는걸 제외한 1번의 쿼리를 날릴까 ?

```sql
Hibernate: select owner0_.id as id1_2_, owner0_.name as name2_2_ from owner owner0_
Hibernate: select cats0_.owner_id as owner_id3_0_1_, cats0_.id as id1_0_1_, cats0_.id as id1_0_0_, cats0_.name as name2_0_0_, cats0_.owner_id as owner_id3_0_0_ from cat cats0_ where cats0_.owner_id in (select owner0_.id from owner owner0_)
```

보면 알다시피, 두 번의 쿼리만을 사용한다. 첫 번째는 엔티티 조회 (Owner 조회)를위한 쿼리이고, 두 번째가 바로 연관관계 데이터를 조회할때 사용된 SubQuery이다

```sql
select cats0_.owner_id as owner_id3_0_1_, cats0_.id as id1_0_1_, cats0_.id as id1_0_0_, cats0_.name as name2_0_0_, cats0_.owner_id as owner_id3_0_0_ from cat cats0_ where cats0_.owner_id in (select owner0_.id from owner owner0_)
```

Subquery (select 안에 select)를 이용했다.

즉시 로딩 (Eager)설정하면 조회 시점에, 지연 로딩 (Lazy)로 설정하면 엔티티를 사용하는 시점에 쿼리가 실행된다.

**모두 지연로딩으로 설정하고 성능 최적화가 필요한 곳에는 JPQL 페치 조인을 사용하는 것이 추천되는 전략이다.**



##### 4. Batch Fetching 

```java
public class Owner {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;

    @BatchSize(size=5)
    @OneToMany(mappedBy = "owner", fetch = FetchType.EAGER)
    private Set<Cat> cats = new LinkedHashSet<>();
}
```

@BatchSize를 사용한다.

하이버네이트가 제공하는 `org.hibernate.annotations.BatchSize` 어노테이션을 이용하면 연관된 엔티티를 조회할 때 지정된 size 만큼 SQL의 IN절을 사용해서 조회한다.

FetchType.EAGER에서 Owner를 조회할 때 Cat을 즉시 같이 조회한다. @BatchSize 어노테이션을 이용했기 때문에, Cat의 수 (row 수) 만큼의 select문을 날리게 아닌, 조회한 Owner의 id를 모아서 SQL IN 절을 날린다.

여기서 @BatchSize(**size** = ? )의 size는 In 절에 올수있는 최대 인자 개수이다.



### 문제점 정리

* Fetch Join이나 EntityGraph를 사용한다면 Join문을 이용하여 하나의 쿼리로 해결할 수 있지만 중복 데이터 관리가 필요하고 FetchType을 어떻게 사용할지에 따라 달라질 수 있다.

* SUBSELECT는 두번의 쿼리로 실행되지만 FethType을 EAGER로 설정해두어야 한다는 단점이 있다.

* BatchSize는 연관관계의 데이터 사이즈를 정확하게 알 수 있다면 최적화할 수 있는 size를 구할 수 있겠지만 사실상 연관 관계 데이터의 최적화 데이터 사이즈를 알기는 쉽지 않다.

