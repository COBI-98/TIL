# 낙관적 락(Optimistic Lock)과 비관적 락(Pessimistic Lock)
JPA 에서 동시성 제어를 위해 락을 사용한다고 하는데
비관적 락, 낙관적 락 두개의 락킹 전략이 존재했다.

두 락의 개념과 차이를 정리하고
spring 에서 다뤄보면서 복습해보자.

<br>

## **등장 배경**

### 비관적 락
1. 동시에 여러 트랜잭션이 동일한 데이터를 수정하려고 할 때 데이터의 일관성과 동시성 문제가 발생
2. 이러한 문제를 해결하기 위해 트랜잭션이 데이터에 접근할 때 다른 트랜잭션으로부터의 변경을 방지
3. 데이터베이스 레벨에서 락을 설정하여 다른 트랜잭션이 해당 데이터를 변경하지 못하도록 보호
4. 트랜잭션이 해당 데이터를 독점적으로 사용하여 일관성을 유지할 수 있게 됩니다.

<br>

### 낙관적 락

1. 비관적 락은 동시성 제어를 위해 락을 설정하는 방식이기 때문에, 동시에 다수의 트랜잭션이 동일한 데이터에 접근하는 경우 성능 저하가 발생
2. 성능을 향상시키기 위해 동시에 다수의 트랜잭션이 동일한 데이터에 접근하는 것을 허용하고, 충돌이 발생하는지를 커밋 시점에서 확인하는 방식으로 등장
3. 낙관적 락은 트랜잭션의 시작과 끝 사이에 다른 트랜잭션에 의한 데이터 변경을 감지하는 메커니즘을 사용하여 충돌을 해결
4. 충돌이 발생하는 경우 트랜잭션을 롤백하거나 예외를 발생시켜 충돌을 처리

<br>

## **사용 용도와 주의 사항**

### 비관적 락
사용용도
- 데이터의 일관성을 유지해야 하는 상황에서 다른 트랜잭션으로부터의 변경을 피하고자 할 때
- 긴 처리 시간이 필요한 작업을 수행할 때, 해당 작업이 끝날 때까지 다른 트랜잭션이 해당 데이터를 수정하지 못하도록 보호할 때

❗ 주의사항:
- 비관적 락은 성능 저하를 초래할 수 있으며, 동시성이 낮아질 수 있다.
- 데이터베이스 종류에 따라 비관적 락을 지원하지 않는 경우도 있을 수 있다.

<br>

### 낙관적 락
사용 용도
- 충돌이 일어나는 빈도가 낮은 경우에 사용
- 동시성이 높은 환경에서 다수의 트랜잭션이 동시에 작업을 수행해야 할 때 사용

❗ 주의사항:
- 낙관적 락은 충돌이 발생할 가능성을 가정하고 예외 처리를 해야 하므로, 코드에서 충돌이 발생했을 때의 처리 방법을 고려해야 함
- 충돌이 발생하는 경우 롤백 및 재시도 또는 충돌을 해결하기 위한 로직이 필요

`비관적 락`은 데이터베이스의 락 기능을 사용하여 데이터 접근 시점에 다른 트랜잭션과의 충돌을 방지하는 반면,

`낙관적 락`은 데이터의 일관성을 커밋 시점에 검사하고, 충돌이 발생하면 예외를 발생시켜 처리한다.
 
  적절한 락 전략은 데이터의 일관성 요구사항과 동시성 요구사항에 따라 선택되어야 한다.

<br>

## **Spring 사용 예시**

### 비관적 락

```java
@Service
@Transactional
public class ProductService {

  @PersistenceContext
  private EntityManager entityManager;

  public Product purchaseProduct(Long productId) {
    Product product = entityManager.find(
            Product.class,
            productId, 
            LockModeType.PESSIMISTIC_WRITE
     );
    
    // 비관적 락을 통해 해당 상품을 다른 트랜잭션에서 변경하지 못하도록 보호
    
    // 상품 구매 로직 수행
    
    return product;
  }
}

```

`LockModeType.PESSIMISTIC_WRITE`를 사용하여, <br> find 메서드로 해당 상품을 조회할 때 비관적 락을 설정하고, <br> 다른 트랜잭션에서 해당 상품을 변경하지 못하도록 보호한다. 

이후 상품 구매 로직을 수행하고, 트랜잭션이 커밋되면 락이 해제되는 예시이다.

<br>

### 낙관적 락
```java
@Entity
@Getter
public class Product {

  @Id
  @GeneratedValue
  private Long id;

  private String name;

  @Version
  private int version;
  
}

@Service
@Transactional
public class ProductService {

  @Autowired
  private ProductRepository productRepository;

  public Product updateProductPrice(Long productId, BigDecimal newPrice) {
    Product product = productRepository.findById(productId).orElse(null);
    
    // 낙관적 락을 통해 동시 수정을 검사
    
    if (product != null) {
      product.setPrice(newPrice);
      return productRepository.save(product);
    }
    
    return null;
  }
}

```

 @Version 애노테이션을 사용하여 Product 엔티티에 낙관적 락을 설정한다.
>  엔티티의 값을 변경하면 버전이 하나씩 자동으로 증가한다.<br> 그리고 엔티티를 수정할 때 조회 시점의 버전과 수정 시점의 버전이 다르면 예외가 발생할 수 있다.


<br>
  updateProductPrice 메서드에서는 상품을 조회한 후, 상품의 가격을 업데이트하고 저장할 때 JPA가 버전을 검사하여 동시 수정 여부를 판단한다. <br> 동시에 다른 트랜잭션에서 수정된 경우 예외가 발생하고 롤백하는 방법으로 구현할 수 있다.

<br>

### **정리**
 Spring에서 이전 예시와 같이 동시성 제어를 수행할 수 있다. <br> 
 
이러한 방식으로 데이터베이스 트랜잭션에서 락을 설정하고 충돌을 감지하거나 예외를 처리하여 <br>
**데이터 일관성과 동시성을 보장하는 데 도움을 준다.**