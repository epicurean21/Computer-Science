# Spring Cloud Config - Knowledge

## Spring Actuator

> Spring actuator란 스프링 부트에서 운영 중인 application을 HTTP 또는 JMX를 이용해서 모니터링하고 관리할 수 있는 기능을 제공하는 **라이브러리**. 
> 
> 웹의 상태 모니터링, metric, traffic 정보 그리고 db 상태 등을 알 수 있다.

### How to use

- [Spring Boot Actuator | Baeldung](https://www.baeldung.com/spring-boot-actuators)

- [(Spring Boot)Spring Boot Actuator 소개 | 기록은 재산이다](https://supawer0728.github.io/2018/05/12/spring-actuator/)



## Spring Cloud Bus

> *Spring Cloud Bus*는 분산 시스템에 존재하는 Node들을 경량 메시지 브로커 (ex. Kafka, RabbitMQ 등) 와 연결하는 역할을 한다.
> 
> 예를들어, Micro Service Architecture (MSA)에서 각 서비스가 config를 바라보고 있을 때, config의 수정내용을 최신값으로 각 서비스에서 가져오기 위해서는 POST 방식으로 http 통신을 해야한다. Config 서버가 각 micro service의 주소를 모두 관리해야하니 비 효율적이다.
> 
> Spring cloud bus는 **동적으로 config 변경을 적용하기 위한 Message Queue Handler이다.** 



Spring Cloud Config Server를 이용해서 application 구동에 필요한 정보 (ex. application.yml, application.properties) 를 한 곳에서 관리할 수 있다.



#### 참조:

- [[SC05] Spring Cloud Bus 란 ? :: 온달의 해피클라우드(Happy@Cloud)](https://happycloud-lee.tistory.com/211)

- [Spring Cloud Bus - guide](https://coe.gitbook.io/guide/config/springcloudconfigbus#:~:text=Spring%20Cloud%20Bus%20%EB%8A%94%20%EB%B6%84%EC%82%B0,%ED%95%98%EB%8A%94%EB%8D%B0%20%EC%82%AC%EC%9A%A9%EC%9D%B4%20%EA%B0%80%EB%8A%A5%ED%95%A9%EB%8B%88%EB%8B%A4.)
