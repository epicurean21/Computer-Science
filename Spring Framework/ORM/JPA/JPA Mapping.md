# JPA 연관관계 매핑



### 양방향 연관관계

<img src="./img/JPA Mapping/object.png" alt="image" style="zoom:50%;" />

객체 연관관계는 다음과 같다.

- 회원 ➡️ 팀 (Member.team)
- 팀 ➡️ 회원 (Team.members)

<img src="./img/JPA Mapping/table.png" alt="table" style="zoom:50%;" />

테이블 연관관계는 다음과 같다.

내가 소속된 팀 이름을 알고 싶으면 TEAM 테이블의 TEAM_ID로 MEMBER 테이블에 조인하면 된다.
팀에 소속된 멤버들을 알고 싶으면 MEMBER의 TEAM_ID로 TEAM 테이블에 join하면 된다..
따라서 **데이터베이스 테이블은 외래 키 하나로 양방향으로 조회할 수 있다.**



##### Member class

```java
@Entity
public class Member {
 @Id
 @Column(name = "MEMBER_ID")
 private String id;
 
 private String username;
 
 @ManyToOne
 @JoinColumn(name="TEAM_ID")
 private Team team;
 
 // 연관관계 설정
 public void setTeam(Team team) {
   this.team = team;
 }
 
 // Getter, Setter ...
}
```



##### Team class

```java
@Entity
public class Team {
  @Id
  @Column(name = "TEAM_ID")
  private String id;
  
  private String name;
  
  @OneToMany(mappedBy = "team")
  private List<Member> members = new ArrayList<Member>();
  
  // Getter, Setter ...
}
```

팀과 회원은 일대다 관계입니다. 따라서 팀 엔티티에 `List<Member> members`를 추가했음.

그리고 일대다 관계를 매핑하기 위해 `@OneToMany` 매핑 정보를 사용했다. 

`@OneToMany`의 mappedBy 속성은 <u>양방향 매핑일 때 사용하는데, **반대쪽 매핑의 필드 이름을** 값으로 주면 된다</u>. 반대쪽 매핑이 Member.team이므로 team을 값으로 설정하였음. mappedBy는 뒤에서 더 알아보도록..



### 연관관계 주인

엄밀히 이야기하면 객체에는 <u>**양방향**</u> <u>연관관계라는 것이 없다</u>. 

서로 다른 **단방향 연관관계 2개를 애플리케이션 로직으로** 잘 묶어서 양방향인 것처럼 보이게 할 뿐.

반면에 데이터베이스 테이블은 외래 키 하나로 양쪽이 서로 조인할 수 있습니다. 따라서 **테이블은 외래 키 하나만으로 양방향 연관관계를 맺는다.**



**객체 연관관계 = 2개**

- 회원 ➡️ 팀 : 연관관계 1개(단방향)
- 팀 ➡️ 회원 : 연관관계 1개(단방향)

**테이블 연관관계 = 1개**

- 회원 ↔️ 팀 : 연관관계 1개(양방향)

**엔티티를 양방향 연관관계로 설정하면 객체의 참조는 둘인데 외래 키는 하나. 따라서 둘 사이에 차이가 발생한다.** 그렇다면 둘 중 어떤 관계를 사용해서 외래 키를 관리해야 할까?

##### 이런 차이로 인해 JPA에서는 **두 객체 연관관계 중 하나를 정해서 테이블의 외래키를 관리해야 하는데 이것을 연관관계의 주인**이라 한다.



양방향 연관관계 매핑 시 지켜야할 규칙이 있는데 두 연관관계 중 하나를 연관관계의 주인으로 정해야 한다. **연관관계의 주인만이 데이터베이스 연관관계와 매핑되고 외래 키를 관리(등록, 수정, 삭제)할 수 있습니다. 반면에 주인이 아닌 쪽은 읽기만 할 수 있습니다.**

- 주인은 `mappedBy` 속성을 사용하지 않는다.
- 주인이 아니면 mappedBy 속성을 사용해서 속성의 값으로 연관관계의 주인을 지정해야 한다.



<img src="./img/JPA Mapping/relationship.png" alt="relationship" style="zoom:50%;" />

##### 그렇다면 Member.team, Team.members 둘 중 어떤게 연관관계의 주인일까?

연관관계의 주인을 정하다는 것 == 외래 키 (foreign key) 관리자를 선택하는 것이다.

> 그림을 보면, 여기서는 회원 테이블에 있는 TEAM_ID 외래 키를 관리할 관리자를 선택해야 합니다. 만약 회원 엔티티에 있는 Member.team을 주인으로 선택하면 자기 테이블에 있는 외래 키를 관리하면 됩니다. 하지만 팀 엔티티에 있는 Team.members를 주인으로 선택하면 물리적으로 전혀 다른 테이블의 외래 키를 관리해야 합니다. 왜냐하면 이 경우 Team.members가 있는 Team 엔티티는 TEAM 테이블에 매핑되어 있는데 관리해야할 외래 키는 MEMBER에 있기 때문입니다.



### 연관관계의 주인은 외래 키가 있는 곳

**연관관계의 주인은 테이블에 외래 키가 있는 곳으로 정해야 한다.** 여기서는 회원 테이블이 외래 키를 가지고 있으므로 Member.team이 주인이 됩니다. 주인이 아닌 Team.members에는 `mappedBy="team"` 속성을 사용해서 주인이 아님을 설정해야 합니다. 여기서 `mappedBy`의 값으로 사용된 team은 연관관계의 주인인 Member 엔티티의 team 필드를 말합니다.

<img src="./img/JPA Mapping/realMapping.png" alt="realMapping" style="zoom:50%;" />

> 데이터베이스 테이블의 다대일, 일대다 관계에서는 항상 **다** 쪽이 외래 키를 가집니다. 다 쪽인 `@ManyToOne`은 항상 연관관계의 주인이 되므로 `mappedBy`를 설정할 수 없습니다. 따라서 `@ManyToOne`에는 `mappedBy` 속성이 없습니다.




### References

- [[jpa] 연관관계 매핑 기초](https://joont92.github.io/jpa/%EC%97%B0%EA%B4%80%EA%B4%80%EA%B3%84-%EB%A7%A4%ED%95%91-%EA%B8%B0%EC%B4%88/)