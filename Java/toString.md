# toString

toString() 메서드를 많이 사용하지 않고 생략 가능한 메서드로만 생각하였다.

하지만 이팩티브 자바 글과 toString()을 사용하는 올바른 예시를 보고 난 뒤, <BR>
toString() 메서드의 개념과 사용 방법을 코드를 살펴보며 정리하면 좋다고 생각했다.


<br>

## toString 메서드의 역할

toString() 메서드는 모든 클래스가 상속받는 Object 클래스의 메서드 중 하나로 정의는 다음과 같다. <br> 
- 객체를 문자열로 표현하는 역할
- 주로 객체의 내용을 읽기 쉬운 형태로 출력하거나 로깅, 디버깅 등에 활용

Person 클래스의 기본 예시로 메서드가 정의하는 바가 이루어지는지 코드로 살펴보자.

```java
public class Person {
    private String name;
    private int age;

    // ... 생성자, getter, setter 등의 코드 ...
}

// ..생략
Person person = new Person("cobi", 20);
System.out.println(person);
System.out.println(person.toString()); // toString() 생략가능
```


> 위 코드의 출력 결과
```java
Main.Person@6a5fc7f7
Main.Person@6a5fc7f7

// 클래스이름@16진수로_표시된_해시코드
```

toString() 메서드의 기본 구현은 **해당 객체의 클래스명과 해시 코드를 문자열로 반환하는 것**이다. 

하지만 이 기본 동작을 그대로 사용한다면, 객체의 실제 내용을 파악하기 어렵게 만들 것이다.

<br>

## toString() 항상 재정의하라. 

```java
public class Person {
    private String name;
    private int age;

    // ... 생성자, getter, setter 등의 코드 ...

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + "}";
    }
}
```


```java
Person person = new Person("cobi", 20);
System.out.println(person); // 출력: Person{name='cobi', age=20}
```

위 코드와 같이 개발자는 필요에 따라 이 메서드를 오버라이딩하여 <br> 의미 있는 **문자열 형태로 객체 정보를 반환**하도록 만들 수 있다.

<br>

toString()을 재정의 하는 이유를 정리하자면 다음과 같다.

`이팩티브 자바 3판 ` <br>
- toString 이 잘 구현된 클래스는 사용하기 편하고, 디버깅이 쉽다. 
- 객체를 출력하기만 하면, 객체가 가지고 있는 모든 정보를 확인할 수 있다. 
- 우리가 직접 호출하지 않아도, 로깅을 하거나 에러메세지를 출력할 때에도 유용하게 사용할 수 있다.

<br>

## 정리

toString() 메서드는 디버깅이나 로깅을 할 때 객체의 내용을 쉽게 파악하기 위해 사용된다. <br> 객체의 상태를 문자열로 표현함으로써 프로그래머들이 코드를 분석하고 디버깅하는 데 도움을 주는 역할을 할 것이다.

<br>

## reference
[Java 에서 toString 메소드의 올바른 사용 용도에 대하여](https://hudi.blog/java-correct-purpose-of-tostring/)