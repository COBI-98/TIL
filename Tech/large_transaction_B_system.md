# 대규모 트랜잭션을 처리하는 배민 주문시스템 세션 후기


## **배달의민족 주문 시스템**

특정 시간에 주문율이 높은 커머스와 수많은 시스템의 존재하는 시스템
- 느슨한 결합이 중요

방대한 데이터를 어떻게 저장해야할까?
- 조회 성능 고려

대규모 트랙잭션과 이벤트 기간으로 통신(이벤트 아키텍쳐 단순화, 일관성) 

<br>

## **성장하는 배민 주문 시스템**

## **성장통**
    - 단일 장애 포인트
    > 하나의 시스템의 장애는 <br>
전체 시스템의 장애로 이어졌어요.
    - 대용량 데이터
    > RDBMS 조인 연산으로 <br>
    조회 성능이 좋지 않았어요.

    
    - 대규모 트랜잭션
    > 주문수 증가로 저장소의 쓰기 처리량 한계에 도달했어요.
    - 복잡한 이벤트 아키텍처
    > 규칙 없는 이벤트 발행으로
    서비스 복잡도가 높아졌어요.

## reference
[[우아한테크세미나] 1부: 대규모 트랜잭션을 처리하는 배민 주문시스템 규모에 따른 진화](https://www.youtube.com/watch?time_continue=2321&v=WCwPSVu8mH8&embeds_referring_euri=https%3A%2F%2Fjonghoonpark.com%2F&source_ve_path=MTM5MTE3LDIzODUx&feature=emb_title)

## 정리
```java
[이커머스 도메인 지식이 왜 필요해?]
https://brunch.co.kr/@windydog/371

[필수 지식]
https://www.mobiinside.co.kr/2022/11/04/ecommerce-domain/
```