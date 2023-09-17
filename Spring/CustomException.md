# Custom exception을 언제 써야할까? 무분별한 커스텀 예외처리 정리
서비스를 만들다보면 예외를 발생시키는 일이 많이 발생한다.

그에 따라 Java에서는 사용자가 상황에 따라 적절한 표준 예외를 처리할 수 있도록 기능을 제공하고 있다.

하지만 표준 예외 메시지 대신에 사용자 정의 예외 메시지를 제공하며, 일반적으로 특정 응용 프로그램 또는 라이브러리에서 발생하는 독자적인 예외 상황을 처리할 때 사용할 수 있는 `Custom Exception`는 의미 있는 예외처리가 가능하다.

표준 예외 처리, <br>
Custom exception으로 사용자가 직접 정의한 예외 <br>
두 가지를 비교하며 각각을 비교해보자.

<br>

## 표준 예외를 사용하면 안 될까?
다음 표는 사용자가 상황에 따라 자주 사용되는 표준 예외 예시이다.

| 예외 클래스                      | 발생 상황                                                                               |
| ------------------------------ | ------------------------------------------------------------------------------------ |
| `IllegalArgumentException`    | 인자의 값이 메소드의 요구 사항을 충족하지 않을 때 발생                             |
| `IllegalStateException`      | 객체의 상태가 메소드 호출을 처리하기에 적절하지 않을 때 발생                     |
| `NullPointerException`     | null을 허용하지 않는 메소드에 null 값이 전달되었을 때 발생                       |
| `IndexOutOfBoundsException` | 인덱스 범위를 넘어섰을 때 발생                                            |
| `ConcurrentModificationException` | 허용하지 않는 동시 수정이 감지될 때 발생                              |
| `UnsupportedOperationException` | 호출한 메소드를 객체가 지원하지 않을 때 발생                        |

<br>

### 충분한 의미 전달이 가능할 것이다.

>우리는 이와 같은 표준 예외를 사용하며 메시지만 예외사항에 맞게 재정의 해준다면 충분한 의미를 파악할 수 있다.

### 협업이나 다른 개발자가 읽기에 가독성이 더 좋을 것이다.

>낯선 예외보다는 오히려 평소에 많이 사용하는 IllegalArgumentException, IlegalStateException, NullPointerException 등의 자주 사용되는 표준 예외가 더 편한 상황이 있을 수 있다.


또한, Effective Java에서도 아래와 같은 이유로 표준 예외 사용을 권장하고 있다. (3판, Item 72)
- 배우기 쉽고 사용하기 편리한 API를 만들 수 있다.
- 표준 예외를 사용한 API는 가독성이 높다.
- 예외도 재사용하는 것이 좋다. 예외 클래스의 수가 적을수록 프로그램의 메모리 사용량이 줄고, 클래스를 적재 시간도 줄어든다.

그러나 Custom Exception이 나오게 된 이유는 무엇일까?

Custom Exception에 대해 알아보자.

<br>

## Custom Exception 등장 배경
모든 예외를 표준 예외로 사용할 수는 없는 다음과 같은 상황에서 사용자 표준 예외처리가 더 효과적으로 적용하는 것 같다.
- 메시지 만으로 예외 정보를 전달하는 것보다 더 심각한 예외의 경우
- 예외 안에 담아야만 하는 정보가 많은 경우
- 보다 구체적인 처리를 요구할 필요가 있는 경우

<br>

## Custom Exception 장점
1. **이름만으로 정보전달이 가능하다.**

사용자가 로그인할 때 잘못된 사용자 이름 또는 비밀번호를 제출하는 경우 <br>
`RuntimeException` 보다 `InvalidCredentialsException`을 활용하면,

Custom Exception은 이름을 통해 일차적으로 예외 발생 상황에 대해 유추할 수 있는 정보를 제공

<br>

2. **예외 상황을 더 명확하게 표현할 수 있다.**
```java
public class IllegalIndexException extends IndexOutOfBoundsException {
	private static final String message = "범위를 벗어났습니다.";

	public IllegalIndexException(List<?> target, int index) {
		super(message + " size: "  + target.size() + " index: " + index);
	}
}
```

Custom Exception 클래스는 여러 부분에서 재사용될 수있으며,
특정 상황을 더 명확하게 표현할 수 있다.

<br>

3. **중앙 집중화된 예외 처리**

Spring에서는 @ControllerAdvice 어노테이션을 사용하여 중앙 집중화된 예외 처리를 구현할 수 있다. 

이를 사용하면 애플리케이션의 여러 부분에서 발생하는 예외를 한 곳에서 처리할 수 있다
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(InvalidCredentialsException.class)
    public ResponseEntity<String> handleInvalidCredentialsException(InvalidCredentialsException ex) {
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body(ex.getMessage());
    }
}
```
예외는 상속 관계에 있기 때문에, Exception이나 RuntimeException을 잡아두면 프로그램 내에서 발생하는 거의 모든 예외에 대해 처리가 가능하다. 하지만 이는 프로그래머가 의도하지 않은 예외까지 모두 잡아내  발생 위치를 정확하게 파악하기 힘들다는 단점(혼란)을 야기할 수 있다.

위 코드에서 GlobalExceptionHandler는 InvalidCredentialsException을 처리하는데, 이 예외가 발생하면 클라이언트에게 HTTP 401 Unauthorized 응답을 반환한다.

예외에 대한 후처리는 각각의 사용자 정의 예외 내부에서 처리한 뒤 일관성있게 error.getMessage()등으로 처리한다.

<br>

## 정리

Custom Exception을 사용하면 코드의 가독성, 유지보수성 및 예외 처리의 일관성을 향상시킬 수 있다.

 Spring과 같은 프레임워크에서 Custom Exception을 적절하게 사용하면 개발자가 더 효과적으로 예외 처리를 관리하고 서비스의 신뢰성 또한 높아질 것이다.



## reference
[무분별한 커스텀 예외 정의](https://www.blog.ecsimsw.com/entry/%EC%98%88%EC%99%B8%EB%A5%BC-%EC%84%A0%ED%83%9D%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)

[custom exception을 언제 써야 할까?](https://tecoble.techcourse.co.kr/post/2020-08-17-custom-exception/)

[명쾌한 Custom Exception in Java](https://ssoco.tistory.com/69)