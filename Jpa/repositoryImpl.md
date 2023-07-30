# QueryDSL을 RepositoryImpl로 관리해보자.

## Repository에서 QueryDSL 사용하기

<br>

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("select o from Order o join fetch o.orderItems")
    List<Order> findAllInnerFetchJoin();

    @Query("select distinct o from Order o join fetch o.orderItems")
    List<Order> findAllInnerFetchJoinWithDistinct();

    //...
}
```

현재 OrderRepository가 사용 중인 정적 쿼리(JPQL)들을 QueryDSL로 교체하려고한다.

Spring Data JPA는 JpaRepository를 상속한 Repository 클래스에서<br> Custom Repository 기능을 사용할 수 있도록 하는 기능을 제공한다.

이를 RepositoryImpl에 추가하여 사용하면 JPA와 Spring Data JPA의 편리함과 장점을 그대로 유지하면서도 쿼리 작성에 더 많은 유연성과 편의성을 추가해보자.

<br>

### **Custom Repository**
<br>

> OrderCustomRepository.java
```java
public interface OrderCustomRepository {

    List<Order> findAllInnerFetchJoin();

    List<Order> findAllInnerFetchJoinWithDistinct();
}
```

기존의 OrderRepository 인터페이스의 findAllInnerFetchJoin() 및 findAllInnerFetchJoinWithDistinct() 메서드와<br> 동일한 메서드를 가지는 새로운 커스텀 인터페이스에 정의한다.

<br>

### Repository Impl

<br>

> OrderRepositoryImpl.java
```java
import static jpabook.jpashop.domain.QMember.member;
import static jpabook.jpashop.domain.QOrder.order;
import javax.persistence.EntityManager;
import com.querydsl.jpa.impl.JPAQueryFactory;

@Repository
public class OrderRepositoryImpl implements OrderCustomRepository{

    private final EntityManager em;
   
    private final JPAQueryFactory query;

    public OrderRepositoryImpl(EntityManager em) {
        this.em = em;
        this.query = new JPAQueryFactory(em);
    }

    @Override
    public List<Order> findAllInnerFetchJoin() {
        return query.selectFrom(order)
            .innerJoin(Order.orderItems)
            .fetchJoin()
            .fetch();
    }

    @Override
    public List<Order> findAllInnerFetchJoinWithDistinct() {
        return query.selectFrom(order)
            .distinct()
            .innerJoin(order.orderItems)
            .fetchJoin()
            .fetch();    
    }
}
```

- EntityManager는 JPA를 사용하여 데이터베이스와 상호작용하는 데 사용되는 객체
- JPAQueryFactory는 QueryDSL을 사용하여 쿼리를 생성하고 실행하는데 사용되는 객체

이 클래스들은 Spring의 의존성 주입(Dependency Injection)을 통해 주입되며, 생성자에서 초기화한다.

커스텀 인터페이스를 구현하는 클래스에 QueryDSL 쿼리를 작성하면 복잡한 쿼리를 더욱 가독성 있게 작성하고, fetch join을 사용하여 성능을 최적화를 할 수 있다.


이 때, 해당 구현 클래스 이름을 `Impl`로 하는 이유
> "Impl"은 Spring Data JPA에서 Repository 인터페이스의 구현체를 나타내는 데 사용되며, <br> 사용자 정의 쿼리나 동작을 구현하기 위해 추가되는 클래스를 의미


<br>

## 변경된 OrderRepository

<br>

```java
public interface OrderRepository extends JpaRepository<Order, Long>, OrderCustomRepository {

    /*
    삭제된 JPQL 정적 쿼리 메서드
    @Query("select o from Order o join fetch o.orderItems")
    List<Order> findAllInnerFetchJoin();

    @Query("select distinct o from Order o join fetch o.orderItems")
    List<Order> findAllInnerFetchJoinWithDistinct();   
     */

    //...
}
```

JpaRepository를 상속하는 OrderRepository가 OrderCustomRepository 인터페이스를 상속하도록 한다.

OrderCustomRepositoryImpl에 작성된 QueryDSL 코드를 OrderRepository가 자동으로 사용할 수 있게 된다.

## 참고자료: <br>
[SpringBoot QueryDSL을 사용해보자](https://tecoble.techcourse.co.kr/Order/2021-08-08-basic-querydsl/)