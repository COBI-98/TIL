# **일급 컬렉션 (First Class Collection)**

일급 컬렉션은 나뿐만 아니라 
대부분의 개발자들에게 쉽지 않은 개념이였던 것 같다. 

일급 컬렉션은 객체지향적으로, 
리팩토링하기 쉬운 코드를 만들기 위해 필요하다. 

먼저 이 일급 컬렉션의 목적을 상기시키고 시작해보자! <br>
일급 컬렉션이란 단어는 [소트웍스 앤솔로지](https://www.coupang.com/?weblogMode=production&landingType=UNKNOWN&mcid=UNKNOWN) 의 객체지향 생활체조 파트에서 언급되었다고 한다.
살펴보자.

- 콜렉션을 포함한 클래스는 반드시 다른 멤버 변수가 없어야 한다. 
- 각 콜렉션은 그 자체로 포장돼 있으므로 이제 콜렉션과 관련된 동작은 근거지가 마련된셈이다.
- 필터가 이 새 클래스의 일부가 됨을 알 수 있다.
- 필터는 또한 스스로 함수 객체가 될 수 있다.
- 또한 새 클래스는 두 그룹을 같이 묶는다든가 그룹의 각 원소에 규칙을 적용하는 등의 동작을 처리할 수 있다.
- 이는 인스턴스 변수에 대한 규칙의 확실한 확장이지만 그 자체를 위해서도 중요하다.
- 콜렉션은 실로 매우 유용한 원시 타입이다.
- 많은 동작이 있지만 후임 프로그래머나 유지보수 담당자에 의미적 의도나 단초는 거의 없다.

이로인한 장점은
- 불변성 유지
- 상태와 행위 분리
- 가독성 향상
- 중복 코드 감소
- 테스트 용이성
- 도메인 모델 개선 등이 존재한다.

<br>

## **그래서 일급 컬렉션이 무엇인가??**
Collection을 Wrapping하면서, 
Wrapping한 Collection 외 다른 멤버 변수가 없는 상태를 일급 컬렉션

Collection을 Wrapping한다의 의미는
```java
Map<String, String> map = new HashMap<>();
map.put("1", "A");
map.put("2", "B");
map.put("3", "C");
```
아래와 같이 wrapping 하는 것을 얘기한다.
```java
public class GameRanking {

    private Map<String, String> ranks;

    public GameRanking(Map<String, String> ranks) {
        this.ranks = ranks;
    }
}
```

<br>

## **왜 사용할까?**
일급 컬렉션을 사용하는 이유를 편의점 예제로 파악해보자.

GS편의점에 아이스크림을 팔고 있다.
```java
// GSConvenienceStore.class
public class GSConvenienceStore {
    // 편의점에는 여러 개의 아이스크림을 팔고 있을 것이다.
    private List<IceCream> iceCreams;
    
    public GSConvenienceStore(List<IceCream> iceCreams) {
        this.iceCreams = iceCreams;
    }
    ...
}

// IceCream.class
public class IceCream {
    private String name;
    ...
}
```

특이하게도 해당 편의점은 아이스크림의 종류를 10가지 이상 팔지 못한다고 한다.

그러면 우리는 List<IceCream> iceCreams의 size가 10이 넘으면 안되는 검증이 필요할 것이다.

```java
// GSConvenienceStore.class
public class GSConvenienceStore {
    private List<IceCream> iceCreams;
    
    public GSConvenienceStore(List<IceCream> iceCreams) {
        validateSize(iceCreams)
        this.iceCreams = iceCreams;
    }
    
    private void validateSize(List<IceCream> iceCreams) {
    	if (iceCreams.size() >= 10) {
            new throw IllegalArgumentException("아이스크림은 10개 이상의 종류를 팔지 않습니다.")
        }
    }
    // ...
}
```

1. **만약 아이스크림뿐만 아니라 과자, 라면 등 여러 가지가 있다고 가정해보자.**

- 모든 검증을 GSConvenienceStore class에서 할 것인가?
```java
validate아이스크림(아이스크림);
validate과자(과자);
validate라면(라면);
```

- 만약 CUConvenienceStore class에서도 동일한 것을 판다면 GSConvenienceStore class에서 했던 검증을 또 사용할 것인가?

```java
public class GSConvenienceStore {
    private List<IceCream> iceCreams;
    private List<Snack> snacks;
    private List<Noodle> Noobles;
    
    public GSConvenienceStore(List<IceCream> iceCreams ...) {
        validate아이스크림(아이스크림);
        validate과자(과자);
        validate라면(라면);
        // ...
    }
    // ...
}

// CUConvenienceStore.class
public class CUConvenienceStore {
    private List<IceCream> iceCreams;
    private List<Snack> snacks;
    private List<Noodle> Noobles;
    
    public CUConvenienceStore(List<IceCream> iceCreams ...) {
        validate아이스크림(아이스크림);
        validate과자(과자);
        validate라면(라면);
        // ...
    }
    // ...
}
```

<br>

2. **List<IceCream> iceCreams의 원소 중에서 하나를 find하는 메서드를 만든다고 가정해보자.**

GSConvenienceStore class와 CUConvenienceStore class 같은 메서드(find)를 두번 구현할 것인가?

```java
// GSConvenienceStore.class
public class GSConvenienceStore {
    private List<IceCream> iceCreams;
    // ...
    public IceCream find(String name) {
        return iceCreams.stream()
            .filter(iceCream::isSameName)
            .findFirst()
            .orElseThrow(RuntimeException::new)
    }
    // ...
}

// CUConvenienceStore.class
public class CUConvenienceStore {
    private List<IceCream> iceCreams;
    // ...
    public IceCream find(String name) {
        return iceCreams.stream()
            .filter(iceCream::isSameName)
            .findFirst()
            .orElseThrow(RuntimeException::new)
    }
    // ...
}
```

편의점 class의 역할이 무거워 지고, 중복코드가 많아진다.

이것을 상태와 행위을 각각 관리하도록 해결해주는 것이 일급컬렉션이다.

아이스크림을 일급 컬렉션으로 만들어 보자.
```java
// class
public class IceCreams {
    private List<IceCream> iceCreams;
    
    public IceCreams(List<IceCream> iceCreams) {
        validateSize(iceCreams)
        this.iceCreams = iceCreams
    }
    
    private void validateSize(List<IceCream> iceCreams) {
    	if (iceCreams.size() >= 10) {
            new throw IllegalArgumentException("아이스크림은 10개 이상의 종류를 팔지않습니다.")
        }
    }
    
    public IceCream find(String name) {
        return iceCreams.stream()
            .filter(iceCream::isSameName)
            .findFirst()
            .orElseThrow(RuntimeException::new)
    }
    // ...
}

// record 
public record IceCreams(List<IceCream> iceCreams) {
    public IceCreams(List<IceCream> iceCreams) {
        if (iceCreams.size() >= 10) {
            throw new IllegalArgumentException("아이스크림은 10개 이상의 종류를 팔지 않습니다.");
        }
    }

    public IceCream find(String name) {
        return iceCreams.stream()
                .filter(iceCream -> iceCream.isSameName(name))
                .findFirst()
                .orElseThrow(() -> new RuntimeException("아이스크림을 찾을 수 없습니다: " + name));
    }
    
    // 다른 유틸리티 메서드나 오버라이드할 메서드 등을 추가할 수 있습니다.
}
```

그렇다면 편의점 class는 어떻게 달라질까?

```java
// GSConvenienceStore.class
public class GSConvenienceStore {
    private IceCreams iceCreams;
    
    public GSConvenienceStore(IceCreams iceCreams) {
        this.iceCreams = iceCreams;
    }
    
    public IceCream find(String name) {
        return iceCreams.find(name);
    }
    // ...
}

// CUConvenienceStore.class
public class CUConvenienceStore {
    private IceCreams iceCreams;
    
    public CUConvenienceStore(IceCreams iceCreams) {
        this.iceCreams = iceCreams;
    }
    
    public IceCream find(String name) {
        return iceCreams.find(name);
    }
    // ...
}
```

과자랑 라면 등이 생겨도 검증은 

과자의 일급 컬렉션과 <br>
라면의 일급 컬렉션이 해줄 것이다.

그리고 편의점 class가 했던 역할을 

아이스크림, 과자, 라면 등 각각에게 위임하여 상태와 로직을 관리할 것이다.

이로인해 상태와 로직을 따로관리할 수 있고, <br>
로직이 사용되는 클래스의 부담을 줄이며, 중복코드를 줄일 수 있다.

<br>

## **컬렉션의 불변을 보장**

소프트웨어 규모가 커지고 있는 상황에서 불변 객체는 아주 중요하다.

각각의 객체들이 절대 값이 바뀔일이 없다는게 보장되면 그만큼 코드를 이해하고 수정하는데 사이드 이펙트가 최소화되기 때문이다.

여기서 일급 컬렉션은 컬렉션의 불변을 보장한다.

일급 컬렉션 클래스의 사용법은 새로 만들거나 값을 가져오는 것뿐이기 때문에,
List라는 컬렉션에 접근할 수 있는 방법이 없다.

그래서 일급컬렉션을 사용하면 불변 객체를 만들 수 있다.

