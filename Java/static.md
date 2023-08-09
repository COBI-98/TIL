# static

static은 자바 프로그래밍 언어에서 사용되는 키워드로, 해당 멤버(필드 또는 메서드)가 클래스에 속하며 인스턴스와는 독립적으로 사용됨을 나타낸다. <br>

그러므로 Java에서 Static 키워드를 사용한다는 것은 메모리에 한번 할당되어 프로그램이 종료될 때 해제되는 것을 의미한다.

개념을 정리하고 Spring에서 `static` 키워드를 사용하는 경우와 예시코드를 살펴보자.

<br>

## static 키워드 개념정리
- static 키워드는 클래스 레벨에 적용되며, 해당 클래스의 모든 인스턴스가 공유하는 멤버를 정의할 때 사용된다.
- static 필드와 메서드는 클래스가 로딩될 때 메모리에 할당되며, 인스턴스를 생성하지 않아도 사용할 수 있다.
- static 멤버는 클래스의 모든 인스턴스가 공유하기 때문에, 값을 변경하면 모든 인스턴스에 영향을 준다.
- static 멤버는 클래스 이름을 통해 접근하며, 인스턴스 변수나 메서드와는 다르게 this 키워드를 사용할 수 없다.

<br>

## Static 메모리

<p align="center">
  <img src="../ETC/imgs/static01.png" alt="static01.png" align ="center" width="55%">
  <img src="../ETC/imgs/static02.png" alt="static01.png" align ="center" width="44%">
</p>


일반적으로 우리가 만든 Class는 **Static 영역**에 생성되고, <br>
new 연산을 통해 생성한 객체는 **Heap영역**에 생성된다.

Static 영역
- 프로그램 종료 시까지 메모리가 할당된 채로 존재한다.
- 정적 변수와 클래스 정보가 저장된다.

Heap 영역
-  동적으로 생성된 객체와 배열이 저장한다.
- Garbage Collector를 통해 수시로 관리 받는다.


>Garbage Collector(GC란?) 
<br> 프로그램에서 사용하지 않는 메모리를 자동으로 탐지하고 회수하는 JVM의 일부 <br>
프로그램이 동적으로 메모리를 할당하면 사용하지 않는 메모리 영역이 발생할 수 있다. 
GC는 이러한 불필요한 메모리를 식별하고 회수하여 시스템 자원을 효율적으로 관리한다.

<br>

### JVM의 메모리영역
- Method Area: 클래스 정보, 상수, 정적 변수 등이 저장
- PC Register: 각 스레드의 현재 실행 주소를 저장
- Stack: 메서드 호출과 로컬 변수, 스레드 정보 등이 저장
- Heap: 동적으로 생성된 객체와 배열이 저장되며, Garbage Collector에 의해 관리
- Static Area: 정적 변수와 클래스 정보가 저장

<br>

## Spring 사용 예시
```java
public class MathUtils {
    public static final double PI = 3.14159;
    
    public static int add(int a, int b) {
        return a + b;
    }
}
```
- PI는 static 필드로, 모든 인스턴스가 공유하는 상수 값으로 사용가능
- add 메서드는 static 메서드로, 인스턴스 생성 없이 클래스 이름으로 호출 가능

<br>

```java
// Spring Bean에서 static 메서드 사용
@Service
public class MyService {
    
    public static void staticMethod() {
        System.out.println("static Method");
    }
}
```
staticMethod는 MyService 클래스의 static 메서드로, 스프링 빈으로 등록되지만 인스턴스를 생성하지 않고도 클래스 이름을 통해 호출 가능

 static 멤버를 사용하여 공통된 상태나 동작을 표현하거나 호출할 수 있음을 확인할 수 있다.

<br>

 ## static를 많이 사용하게 된다면
- static 멤버의 남용은 결합도를 높이고 테스트 용이성을 저해할 수 있으므로 신중한 사용이 필요하다.
- 상황과 요구사항을 고려하여 적절한 방식을 선택하는 것이 중요하다.
- 테스트가 어려울 수 있다. 특히 외부 의존성을 가지는 static 멤버의 테스트는 복잡하다.
- 상속과 오버라이딩이 어려울 수 있다.

- 멀티스레드 환경에서 주의가 필요
```java
// 스레드 예시
public class Visitor {
    static int visitCnt = 0;
    Visitor() {
        this.visitCnt++;
        System.out.print(this.visitCnt+" ");
    }

    public static void main(String[] args) {
        Visitor v1 = new Visitor();
        Visitor v2 = new Visitor();
        Visitor v3 = new Visitor();

        // 출력 값 1 2 3
    }
}
```

프로그램에서 공통으로 사용되는 데이터를 관리할 때 이용할 수 있으나<br>
thread-safe하지 않아 `1 2 3`이 된다


## reference
[[Java] static변수와 static 메소드](https://mangkyu.tistory.com/47)