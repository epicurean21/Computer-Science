# Chapter 1: 도메인 모델 시작하기

## 도메인 이란?

도메인이란, 소프트웨어로 해결하고자 하는 문제 영역이다.

- 한 도메인은 하위 도메인으로 나눌 수 있다.

- 도메인은 다른 하위 도메인과 연동하여 완전한 기능을 제공한다
  
  - 물건구매 - 주문, 결제, 배송, 혜택 엮임
  - 모든 기능을 직접 구현하는 것이 아님
    - 타 서비스 연결
    - 배송 - 외부 배송업체 시스템
    - 결제 - 결제 대행업체
  - 하위 도메인 구성 방식은 상황에 따라 다름
    - 몇몇 대형고객만 관리하는 곳은 - 카탈로그 제공, 주문서 받는 정도만
      - **모든 도메인을 다 구현하려 하지 말고 필요에 따라 구현**
    - 많은 일반 고객 받는 곳 - 리뷰, 결제, 배송, 회원기능등 더 필요
