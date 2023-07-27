# Error와 Exception

## Throwable 클래스
Trowable 클래스는 예외 처리를 할수 있는 최상위 클래스이다. 

그 중, Exception과 Error는 Throwable의 상속을 받는다.

<img src="../ETC/imgs/exception01.png" width="500" alt="exception01"></img>

Error와 Exception은 둘 다 프로그램 실행 중 발생하는 문제를 나타내는 개념이지만, 각각 다른 종류의 문제를 나타낸다. <br> Java에서는 Error와 Exception이 모두 Throwable 클래스의 하위 클래스이며,<br> 프로그램 실행 도중 이러한 문제를 처리하는 데 사용된다.

<br>

## Error
Error는 프로그램 실행 중 발생하는 심각한 오류로, 대부분의 경우 개발자가 직접 처리하지 않는다.

일반적으로 시스템 레벨에서 발생하는 문제를 의미하며, 애플리케이션 코드의 오류가 아니라 JVM이나 하드웨어 등의 문제로 발생한다. <br>
일반적인 에러 예시로는 OutOfMemoryError(메모리 부족), StackOverflowError(스택 오버플로우) 등이 있다.

- StackOverflowError: 호출의 깊이가 깊어지거나 재귀가 지속되어 stack overflow 발생 시 던져지는 오류


- OutOfMemoryError: JVM이 할당된 메모리의 부족으로 더이상 객체를 할당할 수 없을 때 던져지는 오류

<br>

```java
public class ErrorExample {
    public static void main(String[] args) {
        // OutOfMemoryError 발생 예시
        int[] arr = new int[Integer.MAX_VALUE];
    }
}
```

<br>

## Exception
Exception은 프로그램 실행 중 발생하는 예외 상황으로, 일반적으로 프로그램의 로직이나 사용자 입력 등으로 인해 발생한다.

예외는 예상 가능하며, 개발자가 적절히 처리해야 한다는 점이 다른 점이라고 할 수 있다.
 
 Java에서는 예외를 체크 예외(checked exception)와 언체크 예외(unchecked exception)로 구분한다.
- 체크 예외는 반드시 try-catch 블록이나 throws 문을 사용하여 처리해야 한다.
- 언체크 예외는 개발자가 선택적으로 처리할 수 있다.

<br>

```java
public class ExceptionExample {
    public static void main(String[] args) {
        try {
            // 체크 예외 발생 예시
            FileReader fileReader = new FileReader("file.txt");
            // 파일이 없어 IOException이 발생하므로 catch 블록에서 처리해야 함
        } catch (IOException e) {
            System.out.println("파일을 찾을 수 없습니다.");
        }

        // 언체크 예외 발생 예시
        int[] arr = {1, 2, 3};
        System.out.println(arr[5]); // ArrayIndexOutOfBoundsException 발생 (배열 인덱스 범위 초과)
        // 개발자가 선택적으로 처리할 수 있음
    }
}

```

<br>

### Exception의 종류
에외는 Checked Exception, Unchecked Exception으로 두 개의 종류로 나누어진다.

- Checked Exception: 예외처리가 필수이며, 처리하지 않으면 컴파일이 되지 않습니다. JVM 외부와 통신(네트워크, 파일시스템 등)할 때 주로 쓰인다.

  - `RuntimeException` 이외에 있는 모든 예외


  - `IOException`, `SQLException` 등


- Unchecked Exception: 컴파일 때 체크되지 않고, Runtime에 발생하는 Exception을 말한다.
  - `RuntimeException` 하위의 모든 예외

  
  - `NullPointerException`, `IndexOutBoundException` 등

<br>

### 대표적인 Exception Class 정리

- `NullPointerException` : Null 레퍼런스를 참조할때 발생, 뭔가 동작시킬 때 발생한다.

- `IndexOutOfBoundsException` : 배열과 유사한 자료구조(문자열, 배열, 자료구조)에서 범위를 벗어난 인덱스 번호 사용으로 발생한다.

- `FormatException` : 문자열, 숫자, 날짜 변환 시 잘못된 데이터(ex. "123A" -> 123 으로 변환 시)로 발생하며, 보통 사용자의 입력, 외부 데이터 로딩, 결과 데이터의 변환 처리에서 자주 발생한다.

- `ArthmeticException` : 정수를 0으로 나눌때 발생한다.

- `ClassCastException` : 변환할 수 없는 타입으로 객체를 변환할 때 발생한다.

- `IllegalArgumentException` : 잘못된 인자 전달 시 발생한다.

- `IOException` : 입출력 동작 실패 또는 인터럽트 시 발생한다.

- `IllegalStateException` : 객체의 상태가 매소드 호출에는 부적절한 경우에 발생한다.

- `ConcurrentModificationException` : 금지된 곳에서 객체를 동시에 수정하는것이 감지될 경우 발생한다.

- `UnsupportedOperationException` : 객체가 메소드를 지원하지 않는 경우 발생한다.


<br>

# 참고자료
- [예외 처리](https://gyoogle.dev/blog/computer-language/Java/Error%20&%20Exception.html)


