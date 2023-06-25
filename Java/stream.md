# Stream(스트림)

- 자바 8에서 추가한 스트림(Streams)은 람다를 활용할 수 있는 기술 중 하나
-  컬렉션(Collection)과 배열(Array) 같은 데이터 요소들의 연속된 시퀀스를 처리하는 기능을 제공하는 API이다.
- 데이터를 다루는 작업을 간결하고 가독성있게 표현할수 있게 된다.
- 스트림은 '데이터의 흐름’이다.
    - 배열과 컬렉션을 함수형으로 처리할 수 있다

<br>

####  Stream 장점

1. `선언형 프로그래밍` : 가독성 향상과 로직이 명확하게 드러난다.
2. `내부 반복` : 스트림 API가 내부에서 요소들을
3. `지연 연산` : 필요한 요소만 처리하고, 불필요한 작업을 피할 수 있다.
4. `병렬 처리` : 쓰레드를 이용해 많은 요소들을 빠르게 처리

<br />

## 1. Stream 특징

### 1-1. 생성

- 컬렉션으로부터 스트림 생성:
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Stream<Integer> stream = numbers.stream();
```

- 배열로부터 스트림 생성:

```java
int[] array = {1, 2, 3, 4, 5};
IntStream stream = Arrays.stream(array);
값을 직접 전달하여 스트림 생성:
```

- 값을 직접 전달하여 스트림 생성:
```java
Stream<String> stream = Stream.of("Java", "Stream", "Example");
```

<br>

### 1-2. 내부 반복

- 컬렉션은 반복자를 지정하지만 stream은 내부 반복 지원

- forEach를 사용하여 스트림 요소 출력:
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
names.stream()
     .forEach(System.out::println);
```
- filter를 사용하여 조건에 맞는 요소 필터링:
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
numbers.stream()
       .filter(n -> n % 2 == 0)
       .forEach(System.out::println); // 출력: 2, 4
```
<br />

## 2. Stream 연산
- Stream의 연산은 `중간 연산`, `최종 연산`으로 구분된다.

### 2-1. 중간 연산
1. 중간 연산은 다른 stream을 반환한다.
2. 중간 연산을 여러 개 연결해서 만들 수 있다.
3. `Lazy` : 스트림 파이프라인에 실행하기 전까지 아무 연산을 수행하지 않는다.
   1. `최종 연산`에서 한 번에 처리한다.

####  중간연산 예제
```java
public class Main {
	public static void main(String[] args) {
		List<String> names = menu.stream()
            .filter(product -> product.getCalories() > 300)
            .map(product -> product.getName())
            .limit(3) 
            .collect(toList());
	}
}
```
- `limit`을 이용하여 다 가져오는 것이 아닌 처음 3개만 선택했다. (`쇼트서킷`) 
- `filter` `map`을 같이 병합하여 사용했다. (`루프 퓨전`)

<br />

### 2-2. 최종 연산 
- 파이프라인에서 결과를 도출한다.
- Stream이 아닌 자료형을 반환하는 것들이 최종 연산이다.
```java
public class Main {
	public static void main(String[] args) {
		names.stream()
            .forEach(System.out::println);
	}
}
```

## 3. Stream 활용
### 3-1. 스트림 필터링 
1. `filter` : 조건에 맞는 요소만 선택
2. `distinct` : 중복 제거

```java
class Filtering {
public static void main(String[] args) {
    List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
    numbers.stream()
        .filter(i -> i % 3 == 0)  
        .distinct()                
        .forEach(System.out::println);
    }
}
```

### 3-2. 스트림 슬라이싱
1. `limit` : 처음 n개 요소 선택
2. `skip` : 처음 n개 요소 제외
3. `takeWhile` : 조건에 맞는 요소 선택
4. `dropWhile` : 조건에 맞는 요소 제외
 
```java
// 카페 주문 예제
class Manufacturing {
    public static void main(String[] args) {
        List<Product> menu = Arrays.asList(
            new Product("americano", false, 200, Product.Type.HANDMADE),
            new Product("latte", false, 350, Product.Type.HANDMADE),
            new Product("vanillaLatte", false, 400, Product.Type.HANDMADE));
            
        List<Product> productes = menu.stream()
            .takeWhile(product -> product.getCalories() < 320)
            .collect(toList());
        
        List<Product> productes2 = menu.stream()
            .dropWhile(product -> product.getCalories() < 320)
            .collect(toList());
    }
}
```

### 3-3. 매핑

####  map (매핑)
```java
// 카페 키오스크 응답 예제
public class Mapping {
	public static void main(String[] args) {
		List<ProductResponse> products = menu.stream()
			.map(Product::getName)      
			.collect(toList());     
	}
}
```

<br />

####  Stream FlatMap (스트림 평면화)
- flatMap은 각 배열을 스트림으로 변환한 다음에 모든 스트림을 하나의 스트림으로 연결하는 기능을 한다.

```java
// 2개의 숫자 리스트가 있을 때 모든 숫자 쌍의 리스트 반환
// [1, 2, 3] [3, 4] -> [(1, 3), (1, 4), (2, 3), (2, 4), (3, 3), (3, 4)]
public class FlatMap {
	public static void main(String[] args) {
		List<Integer> numbers1 = Arrays.asList(1, 2, 3);
		List<Integer> numbers2 = Arrays.asList(3, 4);
		List<int[]> pairs = numbers.stream()
            .flatMap(num1 -> numbers2.stream()
                .map(num2 -> new int[]{num1, num2})
			)
            .collect(toList());
	}
} 
```

<br />

### 3-4. 검색과 매칭
1. `anyMatch` : 적어도 조건에 하나라도 일치하는지 확인
2. `allMatch` : 모든 요소가 조건에 일치하는지 확인
3. `noneMatch` : 모든 요소가 조건에 일치하지 않는지 확인
4. `findFirst` : 첫 번째 요소 반환
5. `findAny` : 현재 스트림에서 임의의 요소 반환

<br />

####  특징
1. `anyMatch` `allMatch` `noneMatch`는 `쇼트서킷`을 지원한다.
   - 따라서 `findFirst` `findAny`보다 더 효율적이다.
2. `findFirst` `findAny`는 `쇼트서킷`을 지원하지 않는다.

<br />

####  그럼에도 불구하고 `findFirst` `findAny` 사용하는 이유
1. `병렬 스트림`에서는 `findFirst` `findAny`가 더 빠르다.
2. `병렬 스트림`에서는 `쇼트서킷`을 지원하지 않는다.
    - 병렬 실행에서는 첫 번째 요소를 찾기 어렵기 때문이다.
    - `쇼트서킷`을 지원하지 않는다는 것은 `모든 요소를 검사`한다는 것이다.
    - `병렬 스트림`에서는 `모든 요소를 검사`하는 것이 더 빠르다.

<br />

### 3-5. 리듀싱
- `reduce` : 스트림의 요소를 하나로 줄인다. 즉, 모든 스트림 요소를 처리해서 값으로 도출한다.
- `reduce`는 `쇼트서킷`을 지원한다.

```java
// reduce 예제
// reduce(초기값, BinaryOperator<T> 람다 표현식)
public class Reduce {
    public static void main(String[] args) {
        int sum = numbers.stream() 
                .reduce(0, (a, b) -> a + b);
    }
}
```

<br />

### 3-6. 숫자형 스트림
- `IntStream` `LongStream` `DoubleStream` : 기본형 특화 스트림
- `mapToInt` `mapToLong` `mapToDouble` : 스트림의 요소를 특화 스트림으로 매핑

```java
// 카페 음료가격 스트림 예제
public class NumericStream {
    public static void main(String[] args) {
        int totalPrice = menu.stream()
            .mapToInt(Beverage::getPrice)
            .sum();
    }
}
```

<br />

### 3-7. 스트림 만들기

#### 1) 값, 배열을 스트림으로 변환
1. `Stream.of` : 여러 값들을 스트림으로 변환
2. `Arrays.stream` : 배열을 스트림으로 변환
3. `Stream.empty` : 빈 스트림 생성
4. `Stream.builder` : 빈 스트림 생성
5. `Stream.concat` : 두 개의 스트림을 하나로 합침

<br />

#### 2) 함수로 무한 스트림 만들기
1. `Stream.iterate` : 무한 스트림 생성
2. `Stream.generate` : 무한 스트림 생성

- `iterate`, `generate` 메서드는 무한 스트림을 만들기 때문에 limit 메서드로 스트림의 크기를 제한해야 한다.

<br /> 

####  `iterate` `generate` 차이점
- `iterate` : 이전 값을 기반으로 다음 값을 계산
- `generate` : 이전 값을 기반으로 다음 값을 계산하지 않음

<br />

####  언제 `iterate`를 사용할까 ? 
- `iterate`는 이전 값을 기반으로 다음 값을 계산하기 때문에 `상태를 유지`해야 한다.
- `iterate`는 `상태를 유지`하기 때문에 `병렬 스트림`에서는 `순차적으로 처리`된다.
- 즉, 순서가 보장되어야할 때 사용

<br />

####  언제 `generate`를 사용할까 ?
- `generate`는 이전 값을 기반으로 다음 값을 계산하지 않기 때문에 `상태를 유지`할 필요가 없다.
- `generate`는 `상태를 유지`할 필요가 없기 때문에 `병렬 스트림`에서 `병렬로 처리`된다.
- 순서 보장보다는 `성능`이 중요할 때 사용