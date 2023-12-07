# Java 메서드 네이밍 (Clean code)

좋은 코드를 작성하기 위한 Java 메서드 네이밍 규칙은  <br>
개발자들 사이에 일반적으로 수용되는 규칙 및 관행 중 하나이다.

코드 리뷰를 진행하면서 느낀 점은 다음과 같다.

- 메서드 이름은 의도와 목적이 명확해야 한다.
- 메서드의 반환 값을 나타내는 이름을 사용하자.
- 다른 사람이 쉽게 읽어질 수 있는 메서드 네이밍 규칙이 필요하다.

다른 사람이 내 코드를 이해하는데 들이는 시간을 최소화 하기위해 <br>
많이 사용되는 메서드 네이밍 규칙과 방법에 대해서 알아보자.

<br>

## **네이밍시 중요한 고려사항**
네이밍을 할 때 다음과 같은 질문을 통해 이름을 짓는 것이 필요하다.

- 왜 존재해야 하는가
- 무슨 작업을 하는가
- 어떻게 사용하는가
Example
```java
public List<Piece> findPiecesByColor(Color color){}
// 왜 존재해야 하는가(find) - color에 대해 존재하는 piece들을 알기 위해.
// 무슨 작업을 하는가 - color에 맞는 piece들을 가져온다.
// 어떻게 사용하는가 - 체스판에서 흑색(or 백색)의 piece들을 가져와서 점수를 계산.
```
이름만으로도 언제 이 메서드를 호출해야 하는지 의미를 파악할 수 있도록 구체적으로 작성하도록 해야 한다.

<br>

## **메서드 명명 규칙**
### **메서드 이름은 명확하고 의미 있게 지어야 한다.**
```java
// 나쁜 예
public void doStuff() {
    // ...
}

// 좋은 예
public void calculateTotalPrice() {
    // ...
}
```
다른 개발자가 코드를 읽을 때 <br>
메서드 이름만으로도 메서드의 역할과 의도를 파악할 수 있도록 축약하지 말자.

<br>


### **동사 형태로 시작하자.**
```java
// 나쁜 예
public void priceCalculation() {
    // ...
}

// 좋은 예
public void calculatePrice() {
    // ...
}
```
동사로 시작하여 해당 메서드가 어떤 동작을 수행하는지를 나타내자.

<br>


### **메서드 이름은 camelCase 표기법을 사용, 테스트는 lowerCamelCase**
```java
// 나쁜 예
public void calculate_total_price() {
    // ...
}

// 좋은 예
public void calculateTotalPrice() {
    // ...
}
```

하지만 Junit 테스트 메소드 이름은 언더스코어(_)가 표시되어, <br>
 이름의 논리 컴포넌트를 구분하고 각 컴포넌트는 lowerCamelCase로 작성한다.
```java
// Example
// 1. MethodName_StateUnderTest_ExpectedBehavior (메서드명_테스트상태_기대행위)
@Test
void isAdult_AgeLessThan18_False(){}

// 2. MethodName_ExpectedBehavior_StateUnderTest (메서드명_기대행위_테스트상태)
@Test
void isAdult_False_AgeLessThan18(){}

```

<br>

### **메서드의 반환 값을 나타내는 이름을 사용하자.**
```java
// 나쁜 예  검증 or 맞는지, 아닌지 boolean을 반환할 것 같은 선언
private void isDifferentCarName(List<Car> cars){

}

// 좋은 예 
public boolean isDifferentCarName(List<Car> cars){
    // ...
}

private void validateDifferentCarName(List<Car> cars){
    // ...
}
```

<br>

## **메서드 이름으로 자주 사용되는 동사 정리**
- get (가져오기):
```java
public int getAge() {
    return age;
}
```
- set (설정하기):
```java
public void setName(String name) {
    this.name = name;
}
```
- init (초기화):
```java
public void initializeData() {
    // 데이터 초기화 작업 수행
}
```
- is/has/can (상태 확인):
```java
// is (boolean 값 반환)
public boolean isAdult() {
    return age >= 18;
}
// has (특정 조건을 충족하는지 확인 - boolean 값 반환):
public boolean hasPermission(String permission) {
    // 권한 확인 로직
}

// can (어떤 동작을 수행할 수 있는지 확인 - boolean 값 반환):
public boolean canEditDocument(Document document) {
    // 문서 편집 가능 여부 확인
}
```

- create (새로운 객체 생성):
```java
public Person createPerson(String name, int age) {
    // 새로운 Person 객체 생성
}
```
- find (찾기 - 주로 데이터 검색에 사용):
```java
public List<Product> findProductsByCategory(String category) {
    // 카테고리로 제품 검색
}
```
- of (특정한 요소로 구성된 객체 생성):
```java
public static List<Integer> ofNumbers(int... numbers) {
    return Arrays.asList(numbers);
}
```
- from (특정한 소스에서 데이터 변환):
```java
public static List<User> fromDtoList(List<UserDto> userDtos) {
    return userDtos.stream()
                   .map(User::fromDto)
                   .collect(Collectors.toList());
}
```
- to (타입 변환):
```java
public String toString() {
    // 객체를 문자열로 변환
}
```
- A-By-B (A에 의해 B 생성):
```java
public List<Order> findOrdersByCustomer(Customer customer) {
    // 고객에 의해 주문 찾기
}
```

<br>

## 정리

이러한 규칙을 정하고 적용하였다고 한다면, <br> 
그보다 더 중요한 것은 <br>프로젝트 전체에서 동일한 네이밍 규칙을 적용하여 일관성을 유지하는 것이 가독성 향상에 있어서 가장 중요하다.



## reference
[좋은 코드를 위한 자바 메서드 네이밍](https://tecoble.techcourse.co.kr/post/2020-04-26-Method-Naming/)