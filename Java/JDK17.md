# **JDK17**
팀 프로젝트 진행시 JDK11과 JDK17 버전 중 어떤 것을 사용할지
고민한 결과

그동안 많이 접해온 Java 11의 익숙함과
더 많은 정보 량을 가진 Java 11을 결정하기로 하였다.

고민하는 과정에서 공부한 Java 11, Java 17 의 변경내용과

JDK17의 중요 업데이트 항목 중 하나인 record 클래스를 정리해보려고한다.

<br>

## **JDK 17까지의 주요 업데이트**

### **JDK 11**
|Type|Features|Content|
|:---:|:---|:---|
|패키지|Jigsaw 모듈 시스템|모듈을 만들고 해당 모듈은 외부에서 호출할 수 있는 API를 제공하여, 언어레벨에선 직접적으로 해당 모듈에 접근이 불가능|
|패키지|**New Garbage Collector, ZGC 추가**|새로운 Garbage Collector 도입|
|패키지|Thread-Local Handshakes|GC 실행 전 우선 발생하는 "STOP-THE-WORLD" 발생 시 이전에는 모든 Thread가 동시에 중단이 되었다면, 이제는 Thread 개별로 중단 가능|
|패키지|JVM 힙 영역을 시스템 메모리가 아닌 다른 종류의 메모리 할당|HotSpot JVM 이 사용자가 지정한 대체 메모리 장치 또는 서로 다른 메모리장치를 이용해서 JVM Heap 영역의 메모리를 할당|
|패키지|Multi-Release JAR File|JAR파일 형식을 확장하여 여러 버전의 클래스 파일을 하나의 JAR안에 공존 가능|
|지원 도구|jlink|JRE를 생성해주는 도구|
|지원 도구|JShell|메인 Method 없이 자바 코드를 넣고 즉석에서 실행 가능한 도구|
|기능|**Collection Factory Method 기능 강화**|List, Set, Map 인터페이스에 immutable 생성을 할 수 있는 새로운 Method 추가|
|기능|Interface, Private Method 도입|인터페이스 내 private Method 사용 가능|
|기능|Optional ifPresentOrElse Method 추가|기존 ifPresent Method 경우 Optional 객체가 값을 담고 있는 경우만 처리를 하였으나, 추가 된 ifPresentOrElse Method는 해당 객체가 값이 없을 경우 처리할 내용까지 정의가 가능|
|기능|HTML5 Javadoc|javadoc 생성 시, 이전에는 HTML4 형식을 사용하였으나, JDK 9 부터는 HTML5 마크업으로 생성이 가능|
|기능|HTTP 2 Client|http2를 구현하는 신규 클라이언트 API 제공, 기존 HttpURLConnection API 대체 가능|
|기능|**Reactive Stream**|Non-Blocking Backpressure를 이용한 비동기 스트림 처리 지원 API 추가|
|기능|**로컬 변수 타입 추론 "var"**|로컬 변수 선언 시 "타입 추론"을 이용한 명시적 타입 선언이 없어도 변수 선언이 가능한 "var" 키워드 추가|
|기능|**신규 문자열 Method 추가**|isBlank, lines, strip, stripLeading, stripTrailing, repeat 등 신규 String Method 추가|

### **JDK 17**
|Type|Features|Content|
|:---:|:---|:---|
|패키지|향상된 의사 난수 생성기|의사 난수 생성기(Pseudo-Random Number Generator)를 위한 새로운 인터페이스 타입과 구현을 제공|
|패키지|신규 Mac OS 렌더링 파이프라인|Apple 메탈 API를 사용하는 Mac OS용 Java  파이프라인을 구현|
|기능|**텍스트 블록 기능 추가**|기존 String을 여러 줄 작성할 때 사용 가능한 기능, 가독성 있는 코드 지원|
|기능|Switch 표현식 기능 향상|Switch 문 이용 시 값을 반환하여 이용 가능 하며, 람다 스타일 구문을 사용 가능|
|기능|**Record Data class 추가**|immutable 객체를 생성하는 새로운 유형의 클래스로 기존 toString, equals, hashCode Method에 대한 구현을 자동 제공|
|기능|Instanceof 매칭|이전 버전 경우 Instanceof 내부에서 객체를 캐스팅 하는 과정이 필요하였으나, 캐스팅 과정을 내부에서 지원할 수 있도록 변경|
|기능|NumberFormat 클래스 기능 향상|기존 숫자 Format 클래스(NumberFormat) 내 Method 추가(getCompactNumberInstance)|
|기능|DateTimeFormatter 클래스 기능 향상|기존 날짜 Format 클래스(DateTimeFormatter) 내 패턴 Method 형식 추가("B")|
|기능|봉인(Sealed) 클래스|무분별한 상속을 막기 위한 목적으로 등장한 기능으로 지정한 클래스 외 상속을 허용하지 않으며, 지정한 클래스 외 상속 불가능|
|기능|Stream.toList() 기능 추가|기존, Stream을 List로 변환 시 Collectors에서 기능을 찾아 사용했다면 Java17 부터는 Collectors호출 없이 toList()만으로 변환이 가능|

java의 버전이 업데이트 될수록 자동화, 추가적으로 제공되는 기능들이 증가하고있다.

여기서 주목해야할 만한 JDK17의 record 클래스 및 장점을 코드를 보며 확인해보자.

<br>

## **record 클래스란?**
Java 17부터 도입된 record 클래스는 불변(immutable) 데이터를 표현하기 위한 간결한 방법을 제공한다. 

 record 클래스는 자동으로 필드를 private final로 만들고, 
<br> 해당 필드에 접근하기 위한 getter 메서드와 클래스의 구조적 비교를 위한 equals(), hashCode(), toString() 등의 메서드를 생성할 수 있다.
다음 코드를 확인해보자.

```java
// jdk11 dto class
public class SampleResponse {
    private String name;
    private int age;

    public SampleResponse() {
    }

    public SampleResponse(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        SampleResponse that = (SampleResponse) o;
        return age == that.age &&
                Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }

    @Override
    public String toString() {
        return "SampleResponse{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

```java
// jdk17 record class
public record SampleResponse(String name, int age) {}
```
record 클래스를 활용해서 위와 같은 설정을 해줄수 있다.

차이점을 살펴보면 record클래스는 생성자를 통해 데이터를 초기화하며 모든 필드에 대한 매개변수가 있는 생성자를 생성하기 때문에, <br>
Java 11과 같이 매개변수가 없는 생성자, 있는 생성자 두 개의 처리를 해줄 필요가없다.


또한 record 클래스를 사용하면 필드를 수정할 수 없기 때문에 setter 메서드가 필요하지 않다.  
따라서 불변한 데이터 객체를 표현할 때 record 클래스는 강한 강점을 가지고있다.

## **record 특징**

- 불변성
    - 필요한 경우에는 새로운 record 객체를 생성하는 방식으로 값을 업데이트해야 한다.

- setter의 부재
    - record 클래스에서는 없다. 따라서 필드 값을 수정하려면 새로운 record 객체를 생성하는 방식으로 처리해야 합니다.

- 데이터 일관성
    - 불변한 객체를 사용할 때 데이터 일관성을 유지하기가 더 쉽다. 
    - 객체의 상태가 변경되지 않기 때문에 예측 가능한 방식으로 동작하며, 멀티스레드 환경에서도 안전하다.

- **DTO와의 관련성**:
    - record 클래스는 DTO(데이터 전송 객체)를 정의하는 데 매우 적합
    - DTO는 데이터의 전송과 표현을 목적으로 하는 객체이므로 불변성을 가지는 것이 이점을 제공할 수 있다.

따라서 Java 17을 사용하게된다면 record 클래스를 사용하여 DTO(response, request)를 정의하는 것이 일반적으로 사용될 것이다.

 Setter 메서드를 사용하지 않아도 되며, 데이터 일관성과 예측 가능한 동작을 유지할 수 있기 때문이다.

 <br>

 ## JDK17의 다른 주요 업데이트
 우리가 많이 접하는 toList() 와 switch, case 문이 가독성 측면에서 많이 향상되었다.

코드로 살펴보자.

### toList()
```java
public class ToListWithJDK11 {

    public static void main(String[] args) {
        List<Integer> list = List.of(1, 2, 3, 4, 5);
        List<Integer> result = list.stream()
                .filter(i -> i > 3)
                .collect(Collectors.toList());
    }
}
```

```java
public class ToListWithJDK17 {

    public static void main(String[] args) {
        List<Integer> list = List.of(1, 2, 3, 4, 5);
        List<Integer> result = list.stream()
                .filter(i -> i > 3)
                .toList();
    }
}
```

### switch, case

```java
public class SwitchWithJDK11 {

    public static void main(String[] args) {
        String day = "Sunday";
        int result = 0;
        switch (day) {
            case "Monday":
                result = 1;
                break;
            case "Tuesday":
                result = 2;
                break;
            case "Wednesday":
                result = 3;
                break;
            case "Thursday":
                result = 4;
                break;
            case "Friday":
                result = 5;
                break;
            case "Saturday":
                result = 6;
                break;
            case "Sunday":
                result = 7;
                break;
        }
        System.out.println(result);
    }
}
```

```java
public class SwitchWithJDK17 {

    public static void main(String[] args) {
        String day = "Sunday";
        int result = switch (day) {
            case "Monday" -> 1;
            case "Tuesday" -> 2;
            case "Wednesday" -> 3;
            case "Thursday" -> 4;
            case "Friday" -> 5;
            case "Saturday" -> 6;
            case "Sunday" -> 7;
            default -> 0;
        };
        System.out.println(result);
    }
}
```



위와 같이 JDK 버전이 업데이트 될수록 성능이나 기능적인 부분이 계속 향상되고 편의성이 증가되고 있다는것을 알 수 있다.

버전이 업데이트 될수록 업데이트 버전을 조사해보고 사용해보는 경험이 필요할 것 같다.

<br>

## reference
[우리팀이 JDK 17을 도입한 이유
](https://techblog.gccompany.co.kr/%EC%9A%B0%EB%A6%AC%ED%8C%80%EC%9D%B4-jdk-17%EC%9D%84-%EB%8F%84%EC%9E%85%ED%95%9C-%EC%9D%B4%EC%9C%A0-ced2b754cd7)

[우리 프로젝트에서 java 17을 사용하게 된 이유](https://be-student.tistory.com/86#Java%2017%20%EA%B3%BC%20Java%2011%EC%9D%98%20%EC%A4%91%EC%9A%94%ED%95%9C%20%EC%B0%A8%EC%9D%B4%EB%93%A4-1)
