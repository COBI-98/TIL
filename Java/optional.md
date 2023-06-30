# Optional

## Optional 이란?
Spring Data JPA를 사용하며 CrudRepository의 findById 메서드 리턴 타입인 **Optional** 클래스를 살펴보자.

DB 연동 시 많이 겪게되는 예외 중 하나가 바로 NPE(NullPointerException)이다.<br>
Optional은 Java 8에 추가된 새로운 API이며 'null일 수도 있는 객체'를 감싸는 일종의 Wrapper 클래스로, <br> 여러 if 로직 대신 언어 차원에서 NPE를 방지할 수 있도록 도와준다.

```java
Optional<T> optional = findById(Id id);
```

<br>

## Optional 활용하기 

### **1. Optional 객체 생성하기**

<br>

🟢 Optional.empty() :

비어있는 Optional 객체로 생성한다. <br>
Wrapper 클래스이기에 내부에 있는 Product 객체는 값이 없을수도 있고, <br> 객체는 Optional 내부적으로 미리 생성해놓은 싱글톤 인스턴스이다.
```java
Optional<Product> optionalProduct = Optional.empty();
 ```

 <br>

🟢 Optional.of(value) :

인자 value를 담고 있는 Optional 객체로 생성한다. 이 객체는 미리 만들어 놓은 null이 아닌 객체를 넘기면 된다. <br> 만약 인자로 넘긴 객체가 null이라면 NullPointerException이 발생할 수 있다.

```java
Optional<Product> optionalProduct = Optional.value(product);
```

 <br>

🟢 Optional.ofNullable(value) :

null일 수도 있고 아닐 수도 있는 객체를 담아 Optional 객체를 생성한다. <br> 만약 Product가 null인지 null이 아닌지 확신이 서지 않는다면 이 방법으로 Optional 객체를 생성하면 된다.

```java
Optional<Product> optionalProduct = Optional.ofNullable(product);
 ```

 <br>

### **2. Optional 객체 접근**


🟢 get() :

Optional 내부에 담긴 객체를 반환하며 null인 객체라면 NoSuchElementException이 발생한다.<br> 따라서, isPresent() 로 체크한 후에 이 get 메서드를 사용한다.

```java
Product product = optionalProduct.get();
```

 <br>

 🟢 orElseThrow() :

Optional 내부에 담긴 객체가 null이 아니라면 담겨 있는 객체를 반환하고,<br> null이라면 인자로 넘겨준 함수형 인자를 통해 생성된 예외를 발생시킨다.
```java
Product product = optionalProduct.orElseThrow(NullPointerException::new);
 ```

 <br>

🟢 orElse(T other) :

Optional 내부에 담긴 객체가 null이 아니라면 담겨있는 객체를 반환하고, null이라면 인자로 넘겨준 product라는 객체를 반환한다.
```java
Product product = optionalProduct.orElse(product);
 ```

  <br>

 🟢 orElseGet(Supplier<? extends T> other) :

Optional 내부에 담긴 객체가 null이 아니라면 담겨있는 객체를 반환하고, null이라면 인자로 넘겨준 함수형 인자를 통해 생성된 객체를 반환한다.

 <br>

## Optional 정리

  * Optional은 `null 또는 실제 값을 value로 갖는 wrapper로 감싸서 NPE(NullPointerException)로부터 자유로워지기 위해 나온 Wrapper 클래스`이다. 
  * 따라서 Optional을 반환하는 메소드는 절대 null을 갖는 value를 반환해서는 안된다. 
  * 또한 `Optional은 값을 Wrapping하고 다시 풀고, null일 경우에는 대체하는 함수를 호출하는 등의 오버헤드가 있으므로 성능이 저하될 수 있다.` 
  * 그렇기 때문에 `메소드의 반환 값이 절대 null이 아니라면 Optional을 사용하지 않는 것이 성능저하가 적다.` 
  * 즉, Optional은 `메소드의 결과가 null이 될 수 있으며, 클라이언트가 이 상황을 처리해야 할 때` 사용하는 것이 좋다.

<br>

## Optional의 orElse와 orElseGet 차이

### orElse VS orElseGet
Optional API의 단말 연산에는 orElse와 orElseGet 함수가 있다. <br> 
 Optional 객체가 비어 있을 때 대체 값을 제공하는 데 사용되지만, <br> 두 메서드 사이에는 동작 방식에서의 차이가 있다.


🟢 orElse() 메서드:
- orElse() 메서드는 Optional 객체가 **비어 있을 경우에만** 대체 값을 반환한다.
- 항상 대체 값을 판단하고 반환된다.
- 대체 값은 Optional이 아니라 실제 값이기 때문에 항상 평가된다.

```java
ex)
Optional<String> optionalValue = Optional.empty();
String result = optionalValue.orElse(getDefaultValue());
```

optionalValue가 비어있는 경우, <br> `getDefaultValue() 메서드는 항상 호출되어 대체 값을 반환한다.`

<br>

🟢 orElseGet() 메서드:
- orElseGet() 메서드는 Optional 객체가 **비어 있을 때만** 대체 값을 생성하고 반환한다.
- 대체 값을 생성하는 람다식 또는 메서드 참조를 인자로 받는다.
- 대체 값은 실제로 필요한 경우에만 평가된다.

```java
ex)
Optional<String> optionalValue = Optional.empty();
String result = optionalValue.orElseGet(() -> getDefaultValue());
```

optionalValue가 비어있는 경우에만 getDefaultValue() 메서드가 호출되어 대체 값을 생성한다. <br>
`optionalValue가 비어있지 않은 경우, getDefaultValue()는 호출되지 않는다.`

<br>

orElse() 메서드와 orElseGet() 메서드는 대체 값을 제공하는 점에서는 유사하지만, 대체 값의 평가 시점에서 차이가 있다.  <br> orElse()는 항상 대체 값을 평가하고 반환하지만, orElseGet()은 대체 값이 실제로 필요한 경우에만 평가된다. <br> 따라서 대체 값 생성에 비용이 많이 드는 연산이 있을 경우, **orElseGet()을 사용하여 성능을 개선할 수 있다.**
