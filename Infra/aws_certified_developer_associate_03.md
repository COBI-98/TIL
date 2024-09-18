# AWS Certified Developer - Associate

# EC2 Instance Types - General Purpose
- t2.micro

# EC2 Instance Types - Computer Optimized
- Great for computer intensive tasks that require high performance processors
    - hpc 
    - batch processors
    - machine learning


- Fast performance for workloads that process large data sets in memory(RAM)
    - 비정형 정형 데이터베이스
    -  일래스틱 캐쉬
    - BI
    - 실시간 데이터베이스
    - R*, ~ R*

# EC2 Instance Types - Storage Optimized
- Great for storage-intensive tasks that require high, sequential read and write access to large data sets on local storage
    - storage 최적화 
    - OLTP systems
    - Relational & NoSql databases
    - Redis -> 캐시나 데이터 웨어하우징 애플리케이션
    - 분산파일시스템


# Referencing other security groups Diagram


# SSH Summary Table


# Instance Connect 
- 임시 ssh 키 생성 연결

# EC2 Intance 구매 옵션
- EC2 Instances Purchasing Options
- 온 디맨드 인스턴스
- 예약 인스턴스 ( 1 & 3 years)
    - 장기 인스턴스
    - 유연성있는 인스턴스 -> 전환형 예약인스턴스
- Savings Plans ( 1 & 3 years)
    - 장기 인스턴스
    - 현대적인 요금 모델 (특정사용량에 따라 지불)
- spot 인스턴스
    - 단기 워크로드 인스턴스
    - 언제든 인스턴스 잃을 수 있다.
    - 신뢰성 낮음
- 전용호스트 인스턴스 배치 제어 
    - 하드웨어 공유 x

# EC2 On Demand
- 사용량 만큼 지불
- 초당 비용 청구
- 가격 높지만 선결제 x
- 정기약정 x

- 요금 할인
리전예약인스턴스 saving plan 과 결합하여 요금할인

# 정리
## On Demand
- 언제든지 리조트 원할때 전체요금을 지불

## Reserved Instance
- 미리계획 1년~3년 할인율 높음

## Saving Plans
- 일정금액 지출
- 매월 12개월동안 지불
- 시간에따라 방 변경

## Spot 인스턴스
- 빈 객실에 대해 가격 입찰 방 예약 할인율 높음
- 언제든지 방 뺄수있음

## 전용 호스트
- 리조트 건물 전체 예약 자신만의 하드웨어
- 방 언제든지 사용가능
- 숙박여부와 관계없이 돈 지불