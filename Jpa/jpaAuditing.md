# JPA Auditing 정리 및 구현

JPA에서 'Audit'는 감시하다 라는 뜻으로 사용된다. <br>
 각 데이터 마다 '누가', '언제' 데이터를 생성하고 변경했는지 감시한다는 의미이다. 엔티티 클래스에는 공통적으로 들어가는 필드가 있다. 예를들어 '등록일자'와 '수정일자' 같은 것이다

즉, JPA에서 시간을 자동으로 값을 넣어주는 기능이다.

Entity를 영속성 컨텍스트에 저장하거나 조회를 수행한 후에 update를 하는 경우 매번 시간 데이터를 입력하여 주어야 하는데, JPA Auditing을 이용하면 자동으로 시간을 매핑하여 데이터베이스의 테이블에 넣어주게 된다.

`JPA Audit은 데이터의 변경 기록을 추적하고 데이터 변화에 대한 이력을 기록하는 것을 목적으로 한다.`

<br>

## 구현 예시
1. **가장 먼저 Auditing이 필요한 Entity에서 상속받을 BaseEntity를 생성해야 한다.**


[BaseEntity]
```java
@EntityListeners(value = {AuditingEntityListener.class})
@MappedSuperclass
@Getter
public abstract class BaseEntity {

    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdTime;

    @LastModifiedDate
    private LocalDateTime modifiedTime;
}
```

인스턴스 필요 없으므로 추상클래스로 선언한다.

- @EntityListener(value = AuditingEntiyListener.class)
어노테이션이 있으면 **Entity의 변화를 감지**해서 Audit을 확인한다.

- @MappedSuperclass
추상클래스에 사용할 수 있으며 엔티티가 될 수 없고 상속을 통해서 사용해야만 한다.

- @CreateDate
JPA 저장소가 save()할 때 자동으로 생성시간을 만듭니다. ( 수정시간도 똑같이 생성된다.)

- @LastModifiedDate
JPA 저장소가 수정할 때 자동으로 생성시간을 만듭니다

<br>
<br>

2. **Entity가 상속 받을 BaseEntity를 만들었다면 이제 @CreatedBy와 @ModifiedBy를 사용하기 위해 jpaConfig 설정클래스를 만든다.**


<br>

[JpaAuditingConfig]
```java
@Configuration
@EnableJpaAuditing
public class JpaAuditingConfig {
}
```

<br>

**별도의 config를 사용하여 @EnableJpaAuditing을 사용하는이유** <br> 
@WebMvcTest는 테스트 대상이 되는 컨트롤러와 관련된 빈들만 로드하므로, JPA Auditing과 관련된 설정이 충분하지 않다.

Application 메인 클래스에 전역적으로 올리면 Jpa관련된 빈을 찾지못해서 오류가 나타난다.

그렇기에 configuration을 분리하는 JpaAuditingConfig 클래스를 활용하여 JPA Auditing을 활성화하기 위한 구성 클래스로 사용될 수 있다.

<br>

3. **실제 사용하려는 엔티티에 상속하여 사용**

```java
public class Order extends BaseEntity
```

```java
public class Product extends BaseEntity
```

<img src="../ETC/imgs/jpaAuditing01.jpg" width="400" alt="JpaAuditing"></img>
<img src="../ETC/imgs/jpaAuditing02.jpg" width="400" alt="JpaAuditing"></img>
>주문 추가 시 변경감지로 자동 시간 업데이트 

<br>
<img src="../ETC/imgs/jpaAuditing03.jpg" width="400" alt="JpaAuditing"></img>

> 미리 선언해둔 상품예시는 null값으로 처리되어있다. 

<br>

이와 같이 주문 클래스와 같이 직접 CreatedBy, UpdatedBy를 설정하지 않아도 생성과 수정 시 자동으로 추가가 된다. 개발자는 엔티티 마다 해야하는 반복적인 일을 JPA Data를 통해 간단하게 만들 수 있다.