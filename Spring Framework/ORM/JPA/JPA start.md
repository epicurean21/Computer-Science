# JPA [Java Persistence API]

> 김영한 저자의 '자바 ORM 표준 JPA 프로그래밍' 및 강의를 바탕으로 
> Spring JPA를 공부한다.
>
> <img src="./img/jpa.jpg" alt="jpa" style="zoom:33%;" /> 

### Server Spectiication
- IntelliJ (Mac OS X)
- Java 11
- H2 Database
- JPA



### JPA (Java Persistence API)

##### 왜 JPA를 사용할까?

> Database는 아직까지 관계형 DB - [Orable, MySQL, MariaDB] 등을 사용한다.
>
> * 지금 시대는 **객체를 관계형 DB에** 관리한다.
>
> 
>
> 관계형 DB는 SQL을 계속해서 작성해야한다.



1. SQL 중심적인 개발의 문제점

- CRUD의 무한 반복

  > 무한반복하며 지루한 코드의 연속

  ```java
  public class Member {
    private String memberId;
    private String name;
  
    ...
      
  }
  ```
  
  ```sql
  INSERT INTO Member (Member_ID, NAME) VALUES
  SELECT MEMBER_ID, NAME FROM MEMBER M
  UPDATE MEMBER SET ...
  ```

* 여기서 만약 Member의 '연락처'가 추가된다면? - [필드 추가]
  * 연락처 tel 변수가 추가되며 **모든 쿼리를 수정해주어야한다**.
  * 쿼리수정시 실수가 발생하면 정상작동하지 않는다.

* 엔티티 신뢰 문제

  ```java
  class MemberService {
  	  ...
      
      private void process(String id) {
        Member member = memberDAO.find(id);
        member.getTeam(); // ??
        member.getOrder().getDelivery(); // ???
      }
  }
  ```

  객체지향적인 코드에서 Member가 어떤 Team에 속한다면,

  MemberDAO에서 find해서 member 변수를 받아오고, 그 멤버 객체에서 .getTeam()으로 팀을 가져온다.

  1. Member가 소속된 팀 객체를 가져오는것
  2. Member가 주문된 정보, 주문의 배송지 정보를 가져오는 것

  이것이 불가능하다.

  Member DAO에서 Member 객체를 가져오는데, Team과 Order, Delivery의 연관에 대한 모든것을 가져와야한다.

  즉, MemberDAO에서 query가 Team, Order, Delivery를 다 가져와야한다. 

  보장이 되지 않는다면 이러한 코드 작성이 불가능하다.

> 계층형 아키텍처 - [Controller, Service, Repository, DAO ... ]
>
> 진정한 의미의 **계층 분할이** 어렵다.



* 패러다임의 불일치 - 객체 vs. 관계형 데이터베이스

> 객체지향과 관계형 데이터베이스는 사상이 다르다.
>
> 관계형 데이터베이스는 철저하게 데이터를 잘 저장할지에 Focus 되어있다.
>
> 이 두 가지를 억지로 Mapping해서 작업을 해야한다는 번거로움이 있다.
>
> '객체 지향 프로그래밍은 추상화, 캡슐화, 정보은닉, 상속, 다향성 등 시스템의 복잡성을 제어할 수 있는 다양한 장치들을 제공한다'



객체를 영구 보관하는 다양한 저장소 - [Object] 

- RDB
- NoSQL
- File
- OODB

> 현실적으로 RDB, NoSQL을 사용하기에 현실적으로는 관계형 DB를 사용한다.

<img src="./img/JPA2.png" alt="jpa" style="zoom:33%;" /> 

- 객체를 SQL로 **우리가** 바꾸고, 저장을 해야한다.
- 즉, 개발자 == SQL 매퍼 역할이었다.



##### 그렇다면 근본적으로 객체와 관계형 데이터베이스는 무슨 차이가 있을까?

1. 상속

   <img src="./img/inheritance.png" alt="jpa" style="zoom:33%;" /> 

   - 관계형 데이터베이스에는 상속 관계가 있을까?
     - 있는 데이터베이스도 있지만, 당연히 없다.
     - 객체는 있는데 이거를 어떻게 풀어낼까?
   - Album을 저장한다 해보자.
     - 객체 분해
     - INSERT INTO ITEM ...
     - INSERT INTO ALBUM ... 
     - 최소 두 개의 Query 작성을 해야한다. 
   - 조회는 어떻게 할것인가?
     - Item과 Album을 JOIN해서 가져온다. Select ~ Item Join Album
     - **문제는** Movie, Book은??? 
       - **그때마다 새로운 join 쿼리를 작성한다**
     - 그래서 DB는 상속관계 없이, Super ITEM DTO를 만들어서 다 들어있는걸 사용한다...



2. 연관관계

   - 객체는 **참조** 를 사용한다: member.getTeam()

   - 테이블을 **외래 키** 를 사용한다: JOIN ON M.TEAM_ID = T.TEAM_ID

   - 이 둘은 매우 다르다

     - 객체
       - Member.getTeam()으로 Team으로 갈 수 있음
       - Team만 조회했을때 member로 갈수있나? **없다**
     - 테이블
       - Member를 조회, Team Join해서 갈 수 있음
       - Team을 조회 Member로 갈 수 있음 join 하면된다.
     - 객체 Reference를 통한 연관관계는 **방향성이 있음**
     - DB에서 Foreign Key는 **방향성이 없음**. 하나만 있으면 반대로 왔다갔다 가능
     - 양방향 연관관계 Mapping이 매우 중요하지만 어려움 (JPA의 포인터 같은 놈)

     

   - **객체를 테이블에 맞추어 모델링**

     * ```java
       class Member {
         private String id;	// MEMBER_ID 컬럼 사용
         private Long teamId; // TEAM_ID FK 컬럼 사용
         private String username; // USERNAME 컬럼 사용
       }
       ```

     * ```java
       class Team {
         private Long id; // TEAM_ID PK 사용
         private String name; // NAME 컬럼 사용
       }
       ```

     * Member Class에 teamId라는 외래키 값을 그대로 넣는다.즉 외래키 자체를 그대로 Mapping 한다.

       * 객체지향적으로는 Member에 Team 객체가 있는게 더 객체지향적 (Id 값만 있는게 아닌)

     * 근데 이러한 설계를 객체지향적이지 않다는 딴지를 걸며 객체다운 모델링으로 바꾸면??
       * ```java
         class Member {
           private String id;	// MEMBER_ID 컬럼 사용
         	private Team team; // 참조로 연관관례를 맺는다.
           private String username; // USERNAME 컬럼 사용
           
           Team getTeam() {
             return team;
           }
         }
         ```

       * ```java
         class Team {
         	private Long id;
           private String name;
         }
         ```

         Member 객체가 Team 객체를 갖고있음 

         TEAM_ID는 member.getTeam().getId(); 로 넣는다 !?

         INSERT INTO MEMBER (MEMBER_ID, TEAM_ID, USERNAME) VALUES ...

       * 이럴경우 조회하려면 ... HELL 시작

         ```sql
         SELECT M.*, T.*
         FROM MEMBER M
         JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID
         ```

         ...

         ```java
         public Member find(String memberId) {
           // SQL 실행 ...
           
           Member member = new Member();
           
           // 데이터베이스에서 조회한 회원 관련 정보를 모두 입력...
           
           Team team = new Team();
           
           // 데이터베이스에서 조회한 팀 관련 정보를 모두 입력...
           
           member.setTeam(team); // **
           return member;
         }
         ```

         많이 길다... 코드가 너무 길어도 너무 길다 ...

       * 이렇게 반환하면..? 뭔가 이상하다... 기존에 한방 쿼리로.. Member_Team 이란 DTO를 갖고있었는데... 이렇게 객체지향적으로 하니까 너무 길다... 복잡하다...

3. 데이터 타입

4. 데이터 식별 방법

 

**객체 지향적으로 모델링 할수록 매핑 작업만 늘어난다**

**객체를 자바 컬렉션에 저장하듯이 DB에 저장할 수 없을까 ?**

##### JPA - Java Persistence API

- Java 진영의 **ORM **기술 표준

##### ORM ?

- Object-Relational Mapping (객체 관계 매핑)
- 객체는 객체대로 설계
- 관계형 데이터베이스는 관계형 데이터베이스대로 설계
- ORM 프레임워크가 중간에서 Mapping
- 대중적인 언어에는 대부분 ORM 기술이 존재한다.



##### JPA 동작 - 저장

<img src="./img/JPAMove.png" alt="jpa" style="zoom:40%;" /> 

* MemberDAO에 회원 객체를 넘기면 JPA **Persist** 라는 명령어가 Entity를 분석해서 Insert Query를 만들어준다.
* 객체를 DB로 바꿀 뿐 아니라 **패러다임 불일치를 해결한다**
  * JPA가 알아서 Insert 쿼리를 두 번 만들어서 넣는다. 즉 Member Class, Album Class에 다 넣는다.
  * JPA를 통해 Album 객체를 조회하면, **JPA가 알아서 Album 객체와 Join을 해서 Album 객체를 가져온다.**

##### 

##### JPA 동작 - 조회

<img src="./img/JPASearch.png" alt="jpa" style="zoom:40%;" /> 

* JPA가 find(식별자)를 하면, 패러다임 불일치를 해결한 적절한 SQL을 생성한다.



##### JPA 소개

- EJB - 엔티티 빈 (자바 표준)
- 하이버네이트 (Hibernate, 오픈소스) 
- JPA (자바 표준) - **JPA는 인터페이스, 실질적인 구현체는 Hibernate 를 사용한다.**



##### **JPA 생산성** - JPA와 CRUD

- 저장: jpa.persist(member)

- 조회: Member member = jpa.find(memberId)

- 수정: member.setName("name")

  - 이러면 끝난다고 !?
  - update, save 호출이 필요없다. 
  - **Transaction이 commit되는 시점에 JPA가 알아서 변경된 내용을 찾아서 DB에 Query를 날린다**

- 삭제: jpa.remove(member)

  

##### JPA와 연관관계, 객체 그래프 탐색

```java
// 연관관계 저장
member.setTeam(team);
jpa.persist(member);

// 객체 그래프 탐색
Member member = jpa.find(Member.class, memberId);
Team team = member.getTeam();
```

* Member를 가져왔는데 어떻게 Team 객체가 Nullpoint Exception 발생없이 가져올까 ?
  * JPA가 Member와 관련된 객체를 한번에 다 미리 가져오는가 ? 테이블이 50개면... 어마어마한 양 
    * 이렇게 동작하지 않고 보다 **우아하게** 동작한다
  * **member.getTeam()** 시점에, Team 조회가 안되있으면 **JPA가 이때 Team만 조회하는 쿼리를 날린다.**
    * Lazy Loading
  * 원하면 한번에 미리 가져오도록 할 수 있기도 하다. 



##### 신뢰할 수 있는 엔티티, 계층

```java
class MemberService {
	  ...
		public void process() {
      Member member = memberDAO.find(memberId);
      member.getTeam(); // 자유로운 객체 그래프 탐색
      member.getOrder().getDelivery();
    } 
}
```

* JPA를 통해 맴버 객체를 가져오면 이러한 자유로운 객체를 가져올 수 있다. 
* **물론 성능적인 측면은 생각을 해야한다**



**동일한 트랜잭션에서 조회한 엔티티는 같음을 보장한다**

```java
class MemberService {
	  ...
		public void compare() {
      String memberId = "100";
      Member member1 = jpa.find(Member.class, memberId);
      Member member2 = jpa.find(Member.class, memberId);
      
      member1 == member2; // 같다
    } 
}
```

* 마치 자바 collection에 넣고 빼는 것 처럼, JPA collection에 넣었다 빼고, 나머지 DB와의 연결부분은 JPA가 다 해준다.



##### JPA의 성능 최적화 기능

1. 1차 캐시와 동일성 (identity) 보장

   - 같은 **Transaction 안에서** 조회를 하면, 두 번째꺼는 db에서 가져오는게 아닌, cache에서 가져온다.
   - 즉 member1, member2를 조회하면 member1에서는 쿼리가 나가지만, member2는 캐쉬에서 가져온다. 
     - **물론 같은 Transaction에서만이다**
   - JPA라는 중간 Layer가 존재하기에 가능

2. 트랜잭션을 지원하는 쓰기 지연 (transactional write-bound)

   - 트랜잭션을 커밋할 때까지 INSERT SQL을 모은다

   - **JDBC BATCH SQL** 기능을 사용해 한번에 SQL을 전송

     ```java
     transaction.begin(); // [트랜잭션] 시작
     
     em.persist(memberA);
     em.persist(memberB);
     em.persist(memberC);
     // 여기까지 INSERT SQL을 DB에 보내지 않는다
     
     // 커밋하는 순간 데이터베이스에 INSERT SQL을 모아서 보낸다
     transaction.commit(); // [트랜잭션] 커밋
     ```

     

3. 지연 로딩 (Lazy Loading)



##### 지연 로딩과 즉시 로딩

> 지연 로딩: 객체가 실제 사용될 때 로딩
>
> 즉시 로딩: JOIN SQL로 한번에 연관된 객체까지 미리 조회

```java
// 지연 로딩

Member member = memberDAO.find(memberId); // SELECT * FROM MEMBER 

Team team = member.getTeam();
String teamName = team.getName(); // SELECT * FROM TEAM
```

```java
// 즉시 로딩

Member member = memberDAO.find(memberId); // SELECT M.*, T.* FROM MEMBER JOIN TEAM ... 

Team team = member.getTeam();
String teamName = team.getName();
```

* 지연 로딩은 team.getName() 등 실제 team 객체의 내용을 사용 할 때 query를 날린다.
* 지연 로딩은 쿼리를 두 번 날리네 ? 성능을 위해 즉시로딩을 쓸수도 있다. Member랑 Team이 만약 항상 같이 쓰인다면, 즉시 로딩..



