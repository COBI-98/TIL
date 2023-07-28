# JPQL(Java Persistence Query Language)

## 등장배경
1. 객체지향적인 개발에서 RDBMS를 사용하는 경우,
객체와 DB간의 매핑과 관리가 어려워졌다.
2. 객체지향 프로그래밍과 관계형 데이터베이스간의 매핑을 자동화해주는 ORM 프레임워크의 처리가 필요해졌다.
3. JPA의 등장으로 개발자가 SQL을 직접 작성하지 않고도 객체를 DB에 저장하고 조회할수 있게됐다.
4. JPA는 객체를 DB에 저장하고 조회하는 기능을 제공하나,
복잡한 조회나 필터링을 위해선 SQL을 사용해야한다.

이러한 요구에 언어 차원에서 쿼리를 작성하는 필요성이 높아졌고, 
<br>JPA에서 사용하는 객체 지향 쿼리 언어로서, 객체를 기반으로 데이터베이스와 상호작용할 수 있도록  JPQL이 등장했다. <br>
JPQL을 사용하면 SQL과 유사한 문법으로 객체 그래프를 쿼리할 수 있고, ORM 프레임워크가 JPQL을 적절한 SQL로 변환하여 데이터베이스와 통신한다.


<br>

## 개념
- JPQL은 SQL과 유사한 쿼리 언어로, 엔티티 객체를 기반으로 쿼리를 작성
- 데이터베이스의 특정 SQL과 무관하며, JPA가 이를 적절한 SQL로 변환하여 데이터베이스와 통신
- JPQL은 영속성 컨텍스트를 활용하여 캐싱과 같은 성능 향상을 제공

<br>

> 간략 (n+1) 트러블슈팅 개념 정리<br>
JPQL이 실행되며 JPA는 이것을 분석해서 SQL을 생성한다. <br> 
JPQL 입장에서는 즉시 로딩, 지연 로딩과 같은 글로벌 패치 전략을 무시하고 JPQL만 사용해서 SQL을 생성하기에 DB의 패치 전략과는 별도로 동작한다.
<BR> 이에 N+1 (추가 query)를 사용케 한다.

<br>

## JPQL의 기본 문법
JPQL의 기본 문법은 sql(CUD)과 상이하게 사용된다.<br>
persist()로 insert는 존재하지 않는다.
```s
# JPQL 문법
# select문
select_
from_
[where_ ]
[group by_ ]
[having_ ]
[orderby_ ]

# update문
update_
[where_ ]

#delete
delete_
[where_ ]
```

<br>

> select jpql 예시
```java
SELECT e FROM Employee e WHERE e.department = :dept AND e.salary > :minSalary ORDER BY e.salary DESC
```

<br>

> join jpql 예시
```java
SELECT e FROM Employee e JOIN e.department d WHERE d.name = :deptName AND e.salary > :minSalary ORDER BY e.salary DESC
```
Employee 엔티티와 Department 엔티티는 일대다 관계 매핑이다.

<br>

## Spring Java 코드 예시

**@Query** 어노테이션을 사용하여 jpql 쿼리를 직접 작성
```java 
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long> {

    @Query("SELECT e FROM Employee e WHERE e.department = :dept AND e.salary > :minSalary ORDER BY e.salary DESC")
    List<Employee> findEmployeesByDepartmentAndSalary(String department, int minSalary);
}
```

- @Query 어노테이션은 EntityManager를 주입받아 JPQL을 실행하는 것보다 더 간단하게 JPQL을 사용할 수 있으며, 메소드 내에 복잡한 쿼리를 작성할 때 유용 
- 레포지토리 인터페이스에 정의한 메소드에 @Query 어노테이션을 사용하면 해당 메소드가 자동으로 JPQL 쿼리로 매핑되어 실행