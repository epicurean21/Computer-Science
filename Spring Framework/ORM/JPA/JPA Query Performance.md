# JPA Query Performance

Spring Data JPA를 사용하면 내부적으로 구현해주는 query가 편리할 수 있지만, 해당 쿼리의 실행계획 등이 예상치 못하게 작동하여 성능적 이슈를 맞닥드릴 수 있다.

MySQL에 친숙한 경우 존재하는 여러 쿼리들과 Jpql 에서 지원되는 쿼리의 차이로 인한 이슈가 발생할 수 있으니 이에대해 알아보자.

## JPA Exists 성능 개선

많은 경우 DB에 데이터가 존재하는지 (조건에 해당되는 row가 존재하는지) 확인하기 위해 `exists` query를 사용한다.

간단한 경우에는 다음과 같이 사용할 수 있다.

> 이름에 맞는 user가 존재하는지

```java
boolean existsUserByName(String name);
```

조금 복잡해 진다면 보통 `@Query` 애너테이션을 이용하여 jpql을 직접 작성할 것이다.

하지만 JPQL에서는 **<u>select의 exists를 지원하지 않는다.</u>**

즉,

```sql
SELECT EXISTS (
    SELECT u.id
    FROM User u
    WHERE u.name = 'foo'
) as success;
```

와 같은 쿼리를 지원하지 않는다.

다만 **where의 exists는 지원한다 !**

그렇기에 exists를 우회하고자 **count 쿼리를 사용할 수 있다.**

- select exists 에러

```java
// 에러 !! - Validation failed for query for method public abstract boolean
@Query("SELECT EXIST (SELECT u.id FROM User u WHERE u.name =:name")
boolean exist(@Param("name") String name);
```

- count 쿼리 사용

```java
@Query("SELECT COUNT(u.id) > 0 FROM User u WHERE u.name =:name")
boolean exist(@Param("name") String name);
```

그러나 이런 방식의 count 쿼리는 **<u>성능상 이슈가 존재한다.</u>**
