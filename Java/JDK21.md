# **JDK 21** 
현재까지 jdk 8, 11, 17 버전을 사용해봤고 버전 업에 따라 향상되고 편의성이 증가함을 경험했다. <br>

이번에 jdk 21을 사용해 볼 수 있는 기회가 생겨서 jdk 21 버전의 주요기능들을 살펴보려고한다.

주요 내용은 이전 버전에서 제공되지 않았던, <br> 
생산성을 높이는 기능과 <br>
코드의 단순화를 돕는 기능에 대해서 정리해보려고한다.

<br>

# JDK 17 VS JDK 21

| **기능/업데이트**              | **JDK 17**                       | **JDK 21**                       |
|--------------------------------|----------------------------------|----------------------------------|
| **장기 지원 (LTS)**            | LTS (Long-Term Support)         | LTS (Long-Term Support)         |
| **Pattern Matching for switch**| Preview 기능                    | 공식 출시                       |
| **Virtual Threads (Project Loom)** | 사용 불가                     | 정식 지원                       |
| **Scoped Values**              | 없음                            | 신규 추가                       |
| **String 기능**            | lines(), strip(), repeat() 등 제공                   | String Templates 도입 <br>(문자열 템플릿)                       |
| **Unnamed Patterns 및 Variables** | 없음                          | 신규 추가                       |

<br>

이번 주요 기능은 다음과 같은 표로 정리할 수 있을 것이고 각 특징은 다음과 같다.

<br>

# JDK 21 주요 업데이트 정리

## Virtual Threads
동시성(Concurrency)이 필요한 경우, 가상 스레드로 코드 복잡성을 줄일 수 있고, <br>
기존의 Thread Pool 대신 가상 스레드로 간단히 처리해 비동기 작업을 더욱 쉽게 구현가능해졌다.
```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    executor.submit(() -> System.out.println("가상 스레드 처리 !"));
}
```

게임이나 가상의 번호를 생성하는 로직 등 비동기적 작업이 필요하다면, <br>
가상스레드를 활용해 가볍고 효율적인 구현이 가능할것이다.


<br>

## String Templates
 결과를 출력하는 메시지를 작성할 때, 템플릿 기능을 사용해 코드가 더 간결하고 읽기 쉬워진다.
```java
String player = "COBI-98";
int score = 100;
String message = STR."Player {player} 는 {score} 점을 얻었어요 !";
System.out.println(message);  

```
<br>

## Pattern Matching 
레코드와 패턴 매칭하여 처리할 때 더 간결하게 코드 작성이 가능할 것이다.

```java
record Guess(int number, String hint) {}

void checkGuess(Object obj) {
    if (obj instanceof Guess(int number, String hint)) {
        System.out.println("Guess: " + number + ", Hint: " + hint);
    }
}
```


<br>

## Scoped Values
게임의 상태나 전역 변수처럼 **스레드 내에서 한정된 값**을 사용할 때 <br>
JDK 21의 Scoped Values를 활용하면 관리가 쉬워질 것이다.

```java
ScopedValue<String> CURRENT_PLAYER = ScopedValue.newInstance();

ScopedValue.where(CURRENT_PLAYER, "COBI-98").run(() -> {
    System.out.println("현재 플레이어는: " + CURRENT_PLAYER.get());
});
```


<br>

# JDK 21 정리
간단하게 jdk 21를 정리하면, 다음과 같은 이점을 볼 수 있을 것이고,
- 코드 간결화: String Templates와 Pattern Matching으로 가독성이 높은 코드 작성 가능.
- 동시성 처리 간소화: 가상 스레드를 활용해 멀티스레딩 작업을 쉽게 구현.
- 더 나은 성능: ZGC와 Start-Up 속도 개선으로 메모리 최적화.
- 관리 편의성: Scoped Values로 전역 데이터 관리 간소화.

버전 업에따라 더 좋은 코드 품질과 유지보수성을 증가시키기 위해 도입되었다는 것을 알 수 있다.
