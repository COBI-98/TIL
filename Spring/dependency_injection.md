# Spring IoC와 DI 정리

스프링(Spring)에서의 DI(Dependency Injection)와 IoC(Inversion of Control)는 <br>의존성 관리와 객체 생성을 관리하는 중요한 개념이다.

각각의 용어를 정리하고 spring 코드를 예시로 더 자세히 알아보자.

<br>

## IoC 개념

IoC(Inversion Of Control, 제어의 역전)는 간단히 프로그램의 제어 흐름 구조가 뒤바뀌는 것이라고 할 수 있다.

제어권을 상위 템플릿 메소드에 넘기고 자신은 필요할 때 호출되어 사용되도록 한다는 개념이다.

IoC가 적용된 대표적인 기술은 프레임워크이다.

기존에는 객체를 클레스 내부에서 생성했다면,
제어권을 스프링에게 위임하여 스프링이 만들어놓은 객체를 필요한곳에 주입시켜줌으로써, 싱글턴 패턴의 특징을 가지며
스프링에게 작업을 처리하게 만든다.

<br>

> 프레임워크는 내가 작성한 코드를 제어하고 대신 실행 <br>
내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그건 라이브러리

<br>

## DI 개념

스프링의 DI(Dependency Injection)는 의존성 주입을 통해 객체 간의 결합도를 낮추고<br> 유연한 애플리케이션을 만들기 위한 핵심 개념이다.


- DI는 객체가 자신이 사용하는 의존성을 직접 생성하거나 관리하지 않고 외부로부터 주입받는 패턴을 의미
- 이로써 객체 간의 결합도가 낮아지며 코드의 재사용성과 유지보수성이 향상됩니다.
- 스프링에서는 빈(Bean)이라고 불리는 객체들을 스프링 컨테이너가 생성하고 주입

<br>

## 스프링에서 DI와 IoC가 어떻게 작동하는가?

1. 인터페이스 정의
```java
public interface MessageService {
    String getMessage();
}
```

2. 구현 클래스 생성
```java
@Service // 스프링에서 관리되는 빈으로 등록
public class EmailService implements MessageService {
    @Override
    public String getMessage() {
        return "This is an email message";
    }
}
```

3. 의존성 주입(DI)을 받을 클래스 생성
```java
@Component
public class MessagePrinter {

    private final MessageService messageService;

    // 생성자를 통한 의존성 주입
    @Autowired
    public MessagePrinter(MessageService messageService) {
        this.messageService = messageService;
    }

    public void printMessage() {
        System.out.println(messageService.getMessage());
    }
}
```

4. 메인 애플리케이션 클래스
```java
@SpringBootApplication
public class SpringDIExampleApplication implements CommandLineRunner {

    @Autowired
    private MessagePrinter messagePrinter;

    public static void main(String[] args) {
        SpringApplication.run(SpringDIExampleApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        messagePrinter.printMessage();
    }
}
```
### DI
MessagePrinter 클래스는 MessageService 인터페이스에 의존하며, 이 의존성은 생성자를 통해 주입된다. <br> @Autowired 어노테이션은 스프링에게 해당 타입의 빈을 찾아서 자동으로 주입하도록 지시한다.

<br>

### IoC
위의 예시에서 SpringApplication.run() <br> 
스프링 애플리케이션 컨텍스트를 초기화하고 모든 빈들을 생성, 관리한다. <br>이렇게 개발자가 객체를 직접 생성하고 관리하는 것이 아니라 스프링 프레임워크가 그 역할을 대신하게 된다.


<br>

## 정리
`개발자의 제어권이 역전` <br>
Controller, Service 같은 객체들의 동작을 우리가 직접 구현하기는 하지만, 해당 객체들이 어느 시점에 호출될 지는 개발자가 제어하지 않는다.<br>
스프링 프레임워크가 요구하는대로 객체를 생성하면, 프레임워크가 해당 객체들을 가져다 생성하고 호출하고 소멸시킨다.


의존성 주입과 IOC는 스프링의 핵심 개념으로, 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하면서, 코드의 모듈화와 유지 보수성을 향상시켜주는 역할을 한다.

 개발자는 객체 간의 관계에 집중하는 대신 비즈니스 로직을 구현하는 데 더 집중할 수 있게된다.