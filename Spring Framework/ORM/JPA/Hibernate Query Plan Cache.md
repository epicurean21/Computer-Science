# Hibernate Query Plan Cache

Hibernate의 Query Plan Cache란 무엇일까

> 모든 JPQL query 또는 Criteria query 들은, 실제 수행되기 전 compile 되어야한다 (정확히는 AST [Abstract Syntax Tree, 추상 구문 트리]를 선탐색한다. 이를 통해 SQL Statement 가 생성된다. 
> 
> 이러한 과정 (query compilation)은 꽤나 많은 자원 및 소요 시간을 요구하는데, Hibernate에서는 이를 위해 *Query Plan Cache* 를 제공한다.
> 
> - 즉, 빠른 수행을 위해 (컴파일 시간을 단축시키기 위해) 사용된다.
> 
> 모든 query 수행 전 Hibernate은 첫 번째로 plan cache를 탐색한다. 만약 존재하지 않는다면 새로운 plan 을 생성하고 이를 execution plan 에 저장하여 이후에도 사용할 수 있도록한다.

##### Abstract Syntax Tree in Hibernate

Hibernate 내부에서는 어떤식으로 추상 구문 트리를 검색하는지 알아보자.

- 못찾겠다 누군가는 알려줄 수 있을까



<img title="" src="./img/queryPlanCache/queryPlanCache.png" alt="" width="592">

## Configuration

Query Plan cache 설정은 두 가지 Property로 조작할 수 있다.

1. *hibernate.query.plan_cache_max_size*
   
   - plan cache의 최대 query plan 수를 설정한다 default 는 2048

2. *hibernate.query.plan_parameter_metadata_max_size*
   
   - cache 에 저장할 parameter metadata 인스턴스 수를 지정한다 default는 128 개 

여기서 주의해야 할 설정은, 만약 애플리케이션이 설정 된 query plan cache size보다 많은 양의 query를 수행한다면, hibernate이 추가적으로 query compile하는 시간이 소요되면서 결과적으로 query 수행시간이 증가하게된다.

- 즉, 쿼리의 개수가 cache 허용 개수보다 많으면 성능상 issue 가 발생한다.

### Explanation

실제 서비스를 바탕으로 query plan cache가 어떤 역할을 할 수 있는지, 또 추가적인 설정으로 성능 향상을 시키는 방법을 생각해보자.

JPA를 통해 
