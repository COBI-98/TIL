# AOP (Aspect-Oriented Programming)

## 등장배경
1. **횡단 관심사** 분리
     - 기능의 핵심 로직과 분리하여 중복을 피하고 유지보수성을 높일 필요가 있었다.
2. OOP의 한계
    - OOP(Object-Oriented Programming)은 객체의 상태와 행위를 캡슐화하여 코드의 구조를 개선하는 방법으로 중복을 효과적으로 처리하기 어렵다.
    - 횡단 관심사는 여러 클래스와 메소드에 걸쳐 존재하며, OOP로만 해결하기 어려운 코드의 중복과 복잡성이 발생할 수 있다.


AOP(Aspect-Oriented Programming)는 OOP(Object-Oriented Programming)와는 다른 보완적인 개념이다. <br>
 OOP의 한계를 극복하고 횡단 관심사를 모듈화하고 핵심적인 비즈니스 로직에서 분리하여 재사용하겠다는 것이 AOP의 등장배경이라고 할 수 있다.

 > 횡단 관심사 (Cross-cutting Concerns)<br>
 소프트웨어 개발에서 횡단 관심사는 여러 모듈이나 클래스에 공통적으로 적용되는 부분

 <br>

 ## AOP 주요 용어
- Aspect : 
    - 흩어진 관심사를 모듈화 한 것. 주로 부가기능을 모듈화함.
    - 핵심 로직이 진행 될 때 실행해야할 공통 로직
    - Advice 를 언제(join-point) 누구에게(point-cut) 실행 하는 것을 설정
- Target : 
    - Aspect를 적용하는 곳 (클래스, 메서드 등)
- Advice : 
    - 실질적으로 어떤 일을 해야할 지에 대한 것, 실질적인 부가기능을 담은 구현체
- JointPoint : 
    - 공통로직을(advice) 핵심로직(point-cut)에 언제 실행할 것인지
    1) Before          : 핵심로직 실행 전
	2) AfterReturning  : 핵심로직 실행 후, 정상적인 종료일 때만
	3) AfterThowing    : 핵심로직 실행 후, 예외가 발생했을 때만
	4) After           : AfterReturning + AfterThowing
	5) Around          : Before + After
- PointCut : 
    - JointPoint의 상세한 스펙을 정의한 것. 
    - 'A란 메서드의 진입 시점에 호출할 것'과 같이 더욱 구체적으로 Advice가 실행될 지점을 정할 수 있음
-  Weaving
	- Advice를 point-cut에 적용하는 행위

<br>


## Spring AOP 동작 원리
Spring AOP는 프록시 패턴(Proxy Pattern)을 사용하여 AOP를 구현한다. <br> 프록시 패턴은 객체의 대리자(proxy)를 통해 원본 객체의 동작을 제어하고, 횡단 관심사를 추가하는 방식이다.

> 프록시 패턴의 특징<br>
원래 하려던 기능을 수행하며 그 외의 부가적인 작업(로깅, 인증, 트랜잭션 등)을 별도로 수행할 수 있다.<br>
비용이 많이 드는 연산(DB 쿼리, 대용량 텍스트 파일 등)을 실제로 필요한 시점까지 미룰 수 있다.

<br>

Spring AOP는 JDK Dynamic Proxy와 CGLIB를 조합하여 프록시를 생성한다. <br> 기본적으로 인터페이스를 구현한 경우에는 `JDK Dynamic Proxy`를 사용하고, 인터페이스를 구현하지 않은 경우에는 `CGLIB`(default)를 사용한다. 

#### JDK Dynamic Proxy
- Java의 리플렉션 패키지에 존재하는 Proxy라는 클래스를 통해 생성된 프록시 객체를 의미한다.<br> 리플렉션의 Proxy 클래스가 동적으로 프록시 객체를 생성해 주므로 JDK Dynamic Proxy라는 이름이 붙여졌다

- 장점
    - 개발자가 직접 프록시 객체를 만들 필요가 없다.
- 단점
    - 프록시하려는 클래스는 반드시 인터페이스의 구현체여야한다.
리플렉션을 활용하므로 성능이 떨어진다.

#### CGLIB
CGLIB는 Code Generator Libray의 약자로, 클래스의 바이트 코드를 조작하여 프록시 객체를 생성해 주는 라이브러리다. <br> CGLIB를 사용하면 인터페이스가 아닌 타겟 클래스에 대해서도 프록시 객체를 만들어 줄 수 있고, 이 과정에서 Enhancer라는 클래스를 활용한다.

<br>


 ## Execution
execution(접근지정자(생략가능) 리턴타입 패키지명.클래스명.메서드명(매개변수타입) )

```javascript
- execution(public *.*)         : 모든 public 메서드
- execution( * set*())          : 모든 set으로 시작하는 메서드들 중 매개변수가 없는것
- execution( * set*(*))         : 모든 set으로 시작하는 메서드들 중 매개변수가 한개 선언한것
- execution( * set*(*,*))       : 모든 set으로 시작하는 메서드들 중 매개변수가 두개 선언한것
- execution( * set*(Integer, *)): 모든 set으로 시작하는 메서드들 중 매개변수가 두개 선언한것, 첫번째는 Integer
- execution( * set*(..))        : 모든 set으로 시작하는 메서드들 중 매개변수가 0개 이상 상관 X
- execution( * com.example.cobi.service.*.*()) : 
- execution( * com.example.cobi.api.service.*.*()) :  
- execution( * set*() || execution(* set*(*))) : 
- execution( * set*() && execution(* set*(*))) 
```

<br>


## Spring AOP 적용 예시

Spring AOP에서 프록시 패턴은 다음과 같은 동작 원리로 동작한다. 
대상 객체 식별

-  프록시 객체 생성
     - AOP를 적용할 대상 객체를 식별
-  프록시 객체의 메소드 호출 
    - 대상 객체의 메소드를 호출하기 전, 후 또는 예외 발생 시에 어드바이스(Advice)를 실행
- 어드바이스 실행 
    -  프록시 객체는 실제 대상 객체의 메소드를 호출(Before Advice, After Advice)
- 대상 객체의 메소드 호출
    -  어드바이스 실행 후, 프록시 객체는 실제 대상 객체의 메소드를 호출 
- 어드바이스 후속 작업  
    - 대상 객체의 메소드 호출이 완료되면, 프록시 객체는 추가적인 로직(Exception)을 실행

Spring에서 AOP를 적용하는 코드를 예시로 확인해보자.

1. 의존성 추가
```xml
<!-- Maven -->
<dependencies>
    <!-- Spring AOP -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
</dependencies>
```

<br>

2. Aspect 클래스 작성
```java
@Slf4j
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.cobi.service.*.*(..))")
    public void beforeMethodExecution(JoinPoint joinPoint) {
        log.info("Before executing method: {}", joinPoint.getSignature().toShortString());
    }

    @AfterReturning(pointcut = "execution(* com.example.cobi.api.service.*.*(..))", returning = "result")
    public void afterMethodExecution(JoinPoint joinPoint, Object result) {
        log.info("After executing method: {}. Result: {}", joinPoint.getSignature().toShortString(), result);
    }
}
```
- @Before 어노테이션은 메소드 실행 전에 실행될 Advice를 정의
- @AfterReturning 어노테이션은 메소드 실행 후에 실행될 Advice를 정의
- execution 포인트컷 표현식을 사용하여 Advice가 적용될 대상 메소드를 선택

<br>

3. Spring 설정
```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
    // 추가적인 설정 내용...
}
```
- @EnableAspectJAutoProxy 어노테이션을 추가하여 AOP를 활성화

<br>

4. 메소드가 적용될 서비스 클래스 작성
```java
@Service
public class UserService {

    public void addUser(String username) {
        // 사용자 추가 로직...
    }

    public void deleteUser(String username) {
        // 사용자 삭제 로직...
    }
}
```
- AOP를 적용할 대상 서비스 클래스를 작성

<br>

## 정리

프록시 패턴을 사용하여 AOP를 구현함으로써 <br> 클라이언트는 프록시 객체를 호출하면서 횡단 관심사를 추가할 수 있고, 대상 객체는 그대로 유지되면서 프록시 객체에 의해 제어된다. <br> 이를 통해 횡단 관심사의 분리와 재사용성을 달성할 수 있다.<br> Spring AOP는 이러한 프록시 패턴을 내부적으로 사용하여 AOP 기능을 제공한다.
