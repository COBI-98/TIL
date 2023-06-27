# Assertions

Junit 자체에는 `assert` 키워드를 사용하여, **assertion**을 작성할 수 있다. <br>

그중에는 기본적으로 제공하는 어설션 라이브러리 <br> `Junit Assertion` 와 `AssertJ`등의 외부 라이브러리 등을 사용할 수 있다.

> Assertion 이란? <br>
프로그래머 자신이 전개하고 있는 코드 내용에서 프로그래머가 생각하고 있는 움직임과 <br> 특정 지점에서의 프로그램상의 설정 값들이 일치하고 있는지를 검사할수 있도록 하는것

<br>

##  JUnit Assertions (JUnit5)
org.junit.jupiter.api.Assertions 클래스에 포함되어 있으며,<br> 다양한 static 메서드를 통해 다양한 어설션 기능을 사용할 수 있다.
 
- assertEquals() : 두 값을 비교하여 일치 여부 판단
- assertTrue() : Assertion 조건이 true 인지 false 인지 확인
- assertNotNull() : 객체가 null 인지 판별 <br>
등과 같이 `JUnit Assertions`는 간단하고 직관적인 문법을 가지고 있어 사용하기 쉽다. <br>
 하지만 단순한 비교와 널 체크 같은 기본적인 어설션 기능에는 한계가 있다.

<br>

 ## **AssertJ**

 ### Java에서 사용할 수 있는 강력하고 풍부한 어설션 라이브러리
tdd 개발에서 가장 많이 사용되고있는 assertJ에 대해서 알아보자.

org.assertj.core.api.Assertions 클래스에 포함되어 있으며, <br> **체인 형식의 문법**을 사용하여 다양한 어설션 기능을 제공한다. <br> AssertJ는 JUnit Assertions보다 풍부한 기능을 가지고 있으며,  가독성이 좋은 문장 형태의 어설션을 작성할수 있다.

<br>

> 체인 형식의 문법이란 <br>
메서드 체인(Method chaining)이라고도 불리며, <br>메서드 호출을 연속적으로 이어서 작성하는 방식을 말한다. <br>
이를 통해 코드를 좀 더 읽기 쉽고 유지보수하기 쉽게 만든다

```java
assertEquals(expected, actual); // Junit Assertions 
assertThat(actual).isEqualTo(expected); // AssertJ
```

<br>

## AssertJ 메서드 정리 
동등성 어설션:
- isEqualTo(expected): 값이 기대하는 값과 동일한지 확인
- isNotEqualTo(expected): 값이 기대하는 값과 다른지 확인

참/거짓 어설션:
- isTrue(): 값이 참인지 확인
- isFalse(): 값이 거짓인지 확인

널(NuLL) 어설션:
- isNull(): 값이 널인지 확인
- isNotNull(): 값이 널이 아닌지 확인

비교 어설션:
- isGreaterThan(value): 값이 주어진 값보다 큰지 확인
- isGreaterThanOrEqualTo(value): 값이 주어진 값보다 크거나 같은지 확인
- isLessThan(value): 값이 주어진 값보다 작은지 확인
- isLessThanOrEqualTo(value): 값이 주어진 값보다 작거나 같은지 확인
- arrayEquals(): 배열 deeply 비교 

- iterableEquals(): Iterable deeply 비교
- assertLinesMatch(): List<String> 비교
- assertNotSame(): 같지 않은지 비교
- assertSame(): 같은지 비교

문자열 어설션:
- contains(substring): 문자열이 주어진 부분 문자열을 포함하는지 
- startsWith(prefix): 문자열이 주어진 접두사(prefix)로 시작하는지 
- endsWith(suffix): 문자열이 주어진 접미사(suffix)로 끝나는지

파라미터를 확인하거나 예외, 실패 확인 어설션
- assertAll(): 주어진 파라미터들이 전부 실행 확인
- assertThrows(): throw 발생하는지
- assertTimeout(): 시간 초과 전에 실행 완료하는지
- assertTimeoutPreemptively(): 시간 초과 전에 실행 완료하는지
- fail(): 테스트 실패 시키기

추가적인 참고자료 <br>
https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/Assertions.html