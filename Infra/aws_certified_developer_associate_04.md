# EC2 Instance Storage Section

# EBS Volume ?
Elastic Block Store (EBS) == 네트워크 USB stick

pree tier : 30 GB of free EBS storage
-> gp2, gp3

# EBS - Delete on Termination arrtibute
Delete on Termination
-> 기본적으로 활성화상태
-> 인스턴스 종료와 함께 삭제되도록(EBS)

인스턴스가 종료될 때 루트 볼륨을 유지하고자 하는 경우
데이터를 저장하고 하는 경우
루트볼륨의 종료시 삭제 속성을 비활성화

# AMI Overview
## AMI = Amazon Machine Image
각자의 sw구성에 os 정의 및 모니터링 도구 생성

부팅과 구성에 시간 단축

ami를 통해 사전 패키징

aws의 ami로는 linux ami 2 사용하거나 직접사용

## AMI Process 
1. 인스턴스 시작 사용자 지정
2. 인스턴스 중지 데이터 무결성 확보
3. AMI 구축, EBS 스냅샷 생성
4. 다른 AMI에서 인스턴스 실행가능

# EC2 Instance Store
장기 스트로지 = EBS

EB2 인스턴스 저장소는 장기적 데이터 보관 X
-> 버나 캐시, 스크래치 데이터, 임시콘텐츠 등으로 사용적합
데이터 손실 우려

데이터 백업 복제 필요.

# EBS Volume Types ( 6 types )
- gp2 / gp3 (SSD) : 다양항 워크로드에 대해 가격과 성능의 균형을 맞추는 범용 SSD 볼륨
- io 1 / io2 block express (ssd) - Highest-performance SSD
- st 1 (HDD) - Low cost HDD volume
- sc 1 (HDD) - Lowest cost HDD volume 

EC2 볼륨은 GP2, GP3, io1, io2만 부팅 볼륨으로 사용가능
OS의 루트가 실행되는 위치

## 일반적인 SSD EBS Volume
gp3: 최신 세대 볼륨
    - 기본적 3,000 IOP와 초당 125MB의 처리량 제공
    - IOP는 최대 16,000 처리량은 초당 최대 1,000MB 까지 증가가능

gp2: 이전 버전
    - 최대 3,000 IOP

gp2와 gp3는 비용 효율적인 스토리지와 낮은 지연 시간 용도
> GP3: IOP와 처리량 독립적 <br> GP2: 서로 연결

볼륨의 크기와 IOP는 연관되어있음

## Provisioned IOPS SSD
DB작업이 스토리지성능과 일관성에 매우 민감한 경우
    - 프로비저닝된 볼륨
    - `io1 유형 볼륨` 4TB ~ 16TB 지원 최대 IOP를 프로비저닝 

> 최대 IOP는 Nitro EC2 Instance 약 64,000 `다른종류의 인스턴스 32,000`

`io2 block express`
- 최대 64TB의 데이터 사용 ( 4TB ~ 64TB )
- 최대 256,000 IOP 제공, IOP 대 기가바이트 비율 1,000:1
- 매우높은 성능의 볼륨
 
> io1/ io2 , EBS 다중 연결 기능 사용 가능 <br>

## Hard Disk Drives (HDD)
### Throughput Optimized HDD(st1)
- 최대 처리량 500 MB, 최대 IOPS 500

### Cold Hdd (sc1)
- 최대 처리량 250 MB, 최대 IOPS 250

# EBS 다중 연결 기능 (io 1 / io 2)
하나의 볼륨은 한 번에 `최대 16개`의 EC2 인스턴스에 부착 가능

# Amazon EFS - Elastic File System
- 관리형 NFS 네트워크 파일 
- 가용성이 높고 확정성 높음
- security group을 통한 동일한 네트워크 파일 시스템에 동시에 연결 가능 (EC2 Instance 다중 연결)

사용 사례
- 콘텐츠 관리
- 웹서핑
- 데이터 공유
- Wordpress

linux 기반 AMI와만 호환 (윈도우 호환 x)
Posix 파일 시스템 file api

파일 시스템은 자동으로 확장되며 EFS에서 사용되는 데이터 GB 사용량에따라 비용 지불

## EFS - Performance & Storage Classes
### EFS Scale

### 성능 모드

### 처리량 모드

### Storage Classes
- 스토리지 티어
    - 스탠다드
        - 자주 액세스하는 파일 계층
    - EFS-IA (Infrequent access)
        - 자주 액세스하지않는 파일 계층
    - 아카이브 스토리지
        - 1년에 몇번만 데이터 엑세스 (저렴)

# EBS vs EFS - Elastic Block Storage
## EBS Volume
gp2 : 디스크 크기에 비례하여 상승
gp3 & io 1 : 디스크 크기 무관하게 IO 증가

- az 간 ebs 볼륨을 옮기려면 스냅샷 사용하여 저장 및 복원
- EBS 볼륨 백업이 성능에 영향이 갈 수 있으므로 주의
- 인스턴스 종류시 ebs 볼륨또한 삭제

## EFS
- 여러 인스턴스가 하나의 파일시스템 공유
- wo

<br>

# 정리
DB가 필요한경우 범용 SSD 와 프로비저닝 된 IOP SSD

높은 처리량과 가장 낮은 비용이 필요하다면 st1 or sc1

