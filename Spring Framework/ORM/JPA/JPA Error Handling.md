# JPA Error Handling


### JPA Dirty Checking
> Dirty Checking (더티 체킹) 은 Transaction 안에서 **Entity의 변경이 일어나면, 변경 내용을 자동으로 Database에 반영하는** JPA 특징이다.

##### Database 변경 데이터 저장 시점
1. Transaction Commit
2. EntityManager Flush
3. JPQL

EntityManager Method내에는

- 저장 (persist)
- 조회 (find)
- 삭제 (delete)

에 대한 method는 존재하지만,***수정*** 에 대한 method가 존재하지 않는다. 대신에 수정에 해당하는 *Dirty Checking* 을 지원한다.

영속성 컨택스트 (Persistence Context) 안에 있는 엔티티를 대상으로 더티 체킹이 일어난다.

더티 체킹이 일어나는 환경은 아래 두 가지 조건이 충족되어야 한다

- 영속 상태(Managed) 안에 있는 엔티티인 경우
- Transaction 안에서 엔티티를 변경하는 경우

*예를 들어보자*

Service Layer에서 @Transactional을 사용한 경우

```java
@Service
public class ExampleService {

     @Transactional
     public void updateInfo(Long id, String name) {
          User user = userRepository.findById(id);
          user.setName(name); // user의 이름 변경
     }
}
```

위와 같은 경우 Hibernate의 Dirty Checking이 일어난다면 다음과 같은 Query 문을 날린다. 

***User의 update에 대한 method가 호출되지 않았지만 Query가 발생 (더티 체킹)***


```sql
Hibernate:
	update
		user
	set
		name=?
	where
		id=?
```


다음과 같은 경우 어떻게 동작하는지 봐보자.

``` java
public void updateInfo() { 
    User user = userRepository.findById(2L)
        .orElseThrow(() -> new ErrorCodeException(ErrorType.USER_IS_NOT_EXISTING));
    user.setEmail("hello@gmail.com");
    System.out.println(userRepository.existsByEmail("hello@gmail.com"));
}
```

이메일 주소를 변경하고, 해당 변경된 이메일을 다시 database에서 확인하는 메소드이다.
그곳에 @Transactional annotation의 활용 유무에 대해서 차이가 발생한다.

##### @Transactional를 사용한 경우

1. Table: User에서 PK id가 2인 user 엔티티 객체 조회
2. user의 email을 hello@gmail.com로 수정 **(Dirty Checking 발동)**
3. hello@gmail.com를 email로 가진 user 엔티티 유무 확인, 

2번 과정에서 수정되었기에 true 출력

##### @Transactional를 사용하지 않은 경우

1. Table: User에서 PK id가 2인 user 엔티티 객체 조회
2. user의 email을 hello@gmail.com로 수정
3. hello@gmail.com를 email로 가진 user 엔티티 유무 확인,

2번 과정에서 수정되지 못해서 false 출력

Transaction을 사용하지 않아서 반영되지 못한 내용을 반영하고 싶은 경우,
 원하는 시점에 save, saveAndFlush를 사용한다.

