# Java 함수형 프로그래밍 (Functional Programming) 


자바 함수형 프로그래밍은 함수를 일급 객체로 다루고, 함수들을 조합하여 프로그래밍하는 패러다임을 의미한다.

객체지향프로그래밍이 객체간 메세지와 협력 관계의 정의로 이루어졌다면,<br> 함수형 프로그래밍은 단순히 함수들의 조합으로 이루어진다. 


그 함수들은 외부와의 관계는 없고 단지 함수 자신만으로 존재하기에 <br>
이러한 스타일의 프로그래밍은 불변성과 순수성을 강조하며,<br> 데이터의 상태를 변경하는 대신 함수의 조합으로 데이터를 처리하는 방식을 강조하게된다.


자바 함수형 프로그래밍은 자바 8에서 도입된 람다식(Lambda Expression)과 스트림(Stream) API를 기반으로 한다.

<br>

## Java 함수형 프로그래밍의 특징
객체지향 프로그래밍 4대요소나 SOLID의 원칙과 같이
자바 함수형 프로그래밍도 중요한특징들이 있다
그 중 불변성, 순수성, 일급함수, 고차함수에 대한 개념정리와
spring에서 활용하며 정리해보자.

<br> 

<p align="center">
<img src="../ETC/imgs/functional_programming01.png" width="400" alt="functional_programming01"></img>
</p>

<br>

- **불변성(Immutability)**: 함수형 프로그래밍에서는 데이터가 불변성을 갖는 것을 선호한다. 
    -  데이터가 생성된 후에는 변경되지 않고 새로운 데이터를 생성하여 반환한다.

- **순수성(Purity)**: 순수 함수를 사용하여 부작용(side effect)를 최소화하거나 없애는 것을 지향한다.
    - 함수의 실행이 외부 상태를 변경하지 않고 동일한 입력에는 항상 동일한 출력을 반환해야 한다.

    <br>

`순수성과 불변성 spring 예시`

```java
import org.springframework.stereotype.Service;

@Service
public class UserService {
    // 상태 변경을 최소화하고, 불변성을 지향하여 final로 선언(불변성)
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    // 순수 함수로서 외부 상태를 변경하지 않음 (순수성)
    public User createUser(String username, String email) {
        User newUser = new User(username, email);
        return userRepository.save(newUser);
    }

    public User getUserById(long id) {
        return userRepository.findById(id);
    }
}
```
> userRepository는 생성자를 통해 주입되며, 런타임 중에 변경되지 않음<br>
createUser 메서드에서 새로운 사용자를 생성할 때, 외부 상태를 변경하지 않고 새로운 사용자 객체 반환


<br>

- **일급 함수(First-class Functions)**: 함수를 변수에 할당하거나 다른 함수의 인자로 전달할 수 있으며, 함수를 반환값으로 사용할 수 있는 일급 함수의 개념이 적용된다.

`일급 함수 예시`
```java
import org.springframework.stereotype.Service;

@Service
public class UserService {
    // ... (생략)

    public User processUserInformation(String username, String email, UserProcessor userProcessor) {
        User newUser = new User(username, email);
        return userProcessor.process(newUser);
    }
}
```

```java
@FunctionalInterface
public interface UserProcessor {
    User process(User user);
}
```
> UserService의 processUserInformation 메서드는 <br> UserProcessor라는 함수형 인터페이스를 인자로 받음으로써,<br> 런타임에 다양한 처리로직을 사용할수 있다.

<br>

- **고차 함수(Higher-order Functions)**: 함수를 다른 함수의 인자로 전달하거나 함수를 반환하는 함수를 고차 함수라고 한다.
    -  이것을 활용하여 함수를 조합하고 추상화할 수 있다.

`고차함수 예시`
```java
import org.springframework.stereotype.Service;

@Service
public class UserService {
    // ... (생략)

    public User processUserInformation(String username, String email, UserProcessor userProcessor) {
        User newUser = new User(username, email);
        return userProcessor.process(newUser);
    }

    public void performBatchProcessing(List<User> users, UserBatchProcessor batchProcessor) {
        batchProcessor.process(users);
    }
}
```

```java
@FunctionalInterface
public interface UserBatchProcessor {
    void process(List<User> users);
}
```

> 함수형 인터페이스를 인자로 받으며,  <br>런타임에 다양한 배치 처리 로직을 적용할수 있다.

<br>

## 정리
Spring의 빈을 사용하여 간단한 사용자 정보 처리 서비스를 구현하면서, 함수형 프로그래밍의 특징들을 적용해보았다.

순수성과 불변성으로 외부 상태 변경을 최소화하고, 일급 함수와 고차 함수를 활용하여 함수를 인자로 전달하거나 반환하는 기능을 구현하였다. 

이를 통해 코드의 유연성과 확장성을 높일 수 있으며, 함수형 프로그래밍의 장점을 살릴수있지만 이는 말은 쉽지만 실제로 함수형 코딩을 잘 하는데는 높은 수준의 추상화를 요구하기에 
람다식과 함수형 인터페이스를 적절히 사용하며 이해를 높여야 할것이다.
