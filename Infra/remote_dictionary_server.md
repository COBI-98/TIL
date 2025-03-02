# Remote DIctionary Server (Redis)

## 1. Redis의 탄생 배경

Redis는 REmote DIctionary Server의 약자로, Salvatore Sanfilippo에 의해 2009년에 처음 개발되었다. 

초기 목적은 데이터 저장과 조회를 극도로 빠르게 처리할 수 있는 메모리 기반의 데이터 구조 서버를 제공하는 것이었습니다. 

관계형 데이터베이스(RDBMS)의 복잡한 쿼리 및 저장소 성능의 병목 현상을 극복하고자 많이 사용했고, 현재는 NoSQL 분산 시스템, 실시간 애플리케이션, 캐싱 및 메시지 큐와 같은 다양한 시나리오에서 널리 채택되고 있다.

즉, 여러 서버(A서버, B서버, C서버)에서 데이터를 공유하고 싶을 때 사용되고

Redis 주요 활용 사례는 다음과 같다.
- 인증 토큰 저장 (Strings 또는 Hash) ★
- 랭킹 보드 (Sorted Set 활용) ★
- 사용자 API 요청 제한 (Rate Limit)
- 작업 큐 (List 활용) ★
- Persistence 옵션
    - AOF(Append Only File)와 RDB(Snapshot) 방식을 통해 데이터 영속성을 제공
- 복제 및 고가용성
    - Master-Slave 복제 및 Redis Sentinel을 통해 장애 복구와 고가용성을 지원
- Redis Cluster를 통한 분산 및 확장성 
- Pub/Sub 모델을 활용한 메시지 브로커

<br>

## 2. 추상적인 웹 서비스 구조
###  CPU Cache 계층
캐시는 속도와 용량 간의 트레이드오프가 존재하며 아래와 같은 계층 구조를 가진다:

- Core (최소 용량, 가장 빠름)
- L1 Cache
- L2 Cache
- L3 Cache
- Memory (RAM)
- Disk (최대 용량, 가장 느림)

→ 위 계층으로 갈수록 용량은 증가하지만 속도는 저하됨

```java
Client → Web Server → DB
```
추상적인 웹 서비스 구조는 다음과 같이 DB에는 많은 데이터가 저장되며, <br> 보통 디스크에 저장되는데 이럴경우
디스크는 속도가 느리므로 캐시를 활용하여 성능 최적화가 가능하다.

### **Cache 전략** 
#### Look-aside Cache (Lazy Loading)
```java
Client → Web Server → (Cache 확인) → DB
```
- 웹 서버는 데이터를 가져올 때 먼저 Cache를 조회
- Cache에 데이터가 없으면 DB에서 조회 후 Cache에 저장
- 데이터 갱신이 필요하면 Cache를 삭제 후 갱신
>장점: DB 부하 감소, 요청 속도 향상<br>
>단점: 최초 요청 시 Cache가 없어 느릴 수 있음 (Cache Miss 발생)

#### Write-back Cache
```java
Client → Web Server → Cache → DB
```
- 모든 데이터를 먼저 Cache에 저장 후 일정 시간 후 DB에 반영
- Cache 내에서 처리된 데이터가 일정 시간이 지나면 DB에 반영
> 장점: 빠른 응답 속도 제공 <br>
> 단점: Cache 장애 시 데이터 유실 가능

insert 문 백 번 vs insert 쿼리 백개 붙인것에 대한 속도 차이 (`Write Back`)

<br>

## 3. 개발 시 난이도 (Race Condition)

#### 랭킹서버의 구현
Redis의 Sorted Set을 이용하면 랭킹을 구현할 수 있음
덤으로, Replication도 가능
다만 가져다 쓰면 한계에 종속적이 됨
- 랭킹에 저장해야할 id가 1개당 100byte라고 했을 때

#### 친구 리스트 관리
친구 리스트를 Key/Value 형태로 저장할 때의 문제점
- 사용자 123의 친구 리스트를 friends:123 키로 관리한다고 가정
- 동시 요청이 발생할 경우 Race Condition 발생 가능

> ex Race Condition 발생 시 최종 상태

| 시간 | T1 (친구 B 추가)     | T2 (친구 C 추가)     | 최종 데이터        |
| -- | ---------------- | ---------------- | ------------- |
| 1  | `friends:123` 읽기 | `friends:123` 읽기 | A             |
| 2  | 친구 B 추가          | 친구 C 추가          | A             |
| 3  | `friends:123` 저장 | `friends:123` 저장 | A, C (B가 사라짐) |

→ **해결 방법:** Redis의 `SETNX`, `WATCH`, `MULTI/EXEC` 활용하여 동시성 문제 방지

<br>

## 4. Redis 운영 및 메모리 관리
### 메모리 관리
- Redis는 메모리 파편화가 발생할 수 있음.
- 4.x 버전부터 jemalloc에 힌트를 주는 기능이 추가되었으나, jemalloc 버전에 따라 동작 방식이 다를 수 있음.

- 3.x 버전의 경우
    - used memory는 2GB로 보고되지만, 실제 RSS는 11GB까지 사용할 수도 있음.
- 효율적인 메모리 관리 방법
    - 다양한 크기의 데이터보다 유사한 크기의 데이터를 저장하는 것이 유리함. 
    - 24 instance 보다 8피트 여러개가 더 안전함.

###  Redis 운영 시 고려 사항
- 메모리 관리를 철저히 해야 함. ★
- O(N) 복잡도를 가지는 명령어 사용에 주의할 것. ★
- Replication(복제) 설정을 활용하여 데이터 안정성 확보.
- 성능 최적화를 위한 권장 설정(Tip) 적용.

<br>

## 5. Spring Boot 및 Java 개발에서의 Redis 활용

Spring Boot 및 Java 애플리케이션에서 Redis는 주로 다음과 같은 방식으로 활용할 수 있다.

### 5.1. Redis를 활용한 단순 DB 조회

Redis 도입 전:
```java
@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public Product getProductById(Long id) {
        // 데이터베이스에서 직접 조회
        return productRepository.findById(id).orElseThrow(() -> new RuntimeException("Product not found"));
    }
}
```
Redis 도입 후 (캐싱 적용):
```java
@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    @Autowired
    private RedisTemplate<String, Product> redisTemplate;

    private static final String CACHE_KEY = "product:";

    public Product getProductById(Long id) {
        // Redis에서 캐시 조회
        Product product = redisTemplate.opsForValue().get(CACHE_KEY + id);
        if (product == null) {
            // 캐시에 없으면 데이터베이스에서 조회 후 캐시에 저장
            product = productRepository.findById(id).orElseThrow(() -> new RuntimeException("Product not found"));
            redisTemplate.opsForValue().set(CACHE_KEY + id, product);
        }
        return product;
    }
}
```

- 데이터베이스에 반복적으로 접근하지 않고, 캐싱을 통해 성능 향상.
- Redis를 사용한 캐시는 응답 속도를 개선하고, DB 부하를 줄임.

### 5.2. Redis를 활용한 세션 관리 예시

Redis 도입 전 (기본 세션 관리):

```properties
server.servlet.session.store-type=none
```

세션 정보가 애플리케이션 서버 메모리에 저장되므로, 분산 환경에서는 세션 공유가 어렵지만

Redis 도입 후 (Redis를 세션 저장소로 사용):
```properties
server.servlet.session.store-type=redis
spring.session.redis.namespace=session
spring.redis.host=localhost
spring.redis.port=6379
```

- 세션 정보가 Redis에 저장되므로, 여러 서버 간 세션을 공유할 수 있어 분산 환경에서 유리해질 수 있다.

<br>

## 6. Elasticsearch와 Redis 도입으로 검색성능 향상시키기
Elasticsearch와 Redis는 각자의 강점을 가진 데이터 처리 기술로, 함께 사용하면 강력한 검색 성능과 빠른 응답 속도를 확보할 수 있을 것이다.

### 목표
- 데이터 동기화: Redis를 캐싱 계층으로 활용하고 Elasticsearch를 영구 저장소 및 검색 엔진으로 사용
- 실시간 검색 성능 향상: Elasticsearch의 검색 성능을 보완하기 위해 Redis에 자주 검색되는 데이터를 캐싱
- 로그 분석: Redis는 실시간 데이터 처리를, Elasticsearch는 로그 저장 및 고급 쿼리를 처리
- 분산 아키텍처: 두 기술을 결합해 확장성과 고성능이 필요한 분산 애플리케이션을 구현


## 정리
Redis는 Spring Boot와 Java 개발에서 없어서는 안 될 도구로 자리 잡았으며, 특히 실시간 성능과 확장성이 중요한 현대 애플리케이션에서 그 가치는 더욱 크다. Elasticsearch와의 조합을 통해 효율적인 검색 및 데이터 처리가 가능하며, 이를 통해 전체적인 애플리케이션 성능과 사용자 경험을 크게 개선할 수 있습니다.

## Reference
- [우아한 Redis](https://www.youtube.com/watch?v=mPB2CZiAkKM)
- [우아한 Redis 발표 슬라이스](https://www.slideshare.net/slideshow/redis-196314086/196314086)
- [Sptring Redis](https://spring.io/projects/spring-data-redis)