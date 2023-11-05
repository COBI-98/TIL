# Sealed Class

Java 17 에서 추가된 Sealed Class/Interface 를 사용하여 <br>
상속하거나(extends), 구현(implements) 할 클래스를 지정해두고, <br> 해당클래스들만 상속/구현이 가능하도록 제한하여 구현한 코드를 보았다.

해당 코드의 장점으로는 어떤 클래스가 해당클래스를 상속받는지를 가독성이높아지고 <br>
불 필요한 확장을 방지, 제어할 수 있다는 장점을 가지고 있었다.

그래서 Sealed Class가 어떠한 기능을 구현하는 데 유용한지 알아보려고 한다.

<br>

## Sealed Class 정의 방법
```java
sealed class Shape permits Circle, Rectangle, Triangle {
    abstract double area();
}
```

super-class 에 sealed 키워드를 사용하고, <br>
permits 키워드 뒤에 해당 클래스를 상속받을 sub-class 를 선언한다.

```java
final class Circle extends Shape {
    private final double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    double area() {
        return Math.PI * radius * radius;
    }
}

final class Rectangle extends Shape {
    private final double width;
    private final double height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    double area() {
        return width * height;
    }
}

final class Triangle extends Shape {
    private final double base;
    private final double height;

    public Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }

    @Override
    double area() {
        return 0.5 * base * height;
    }
}
```

그런 다음, 각 하위 클래스인 Circle, Rectangle, Triangle 클래스를 <br> 
final로 선언하여 더 이상 확장되지 않도록 할 수 있다.

<br>

## **non-sealed vs final 차이점?**

Sealed Class (sealed)가 상속 또는 구현을 제한할 수 있다면, <br>
Non-sealed 클래스는 상속이나 구현을 제한하지 않는다. <br> 즉, 누구든지 이 클래스를 상속하거나 구현할 수 있다.

```java
non-sealed class NonSealedShape {
    // 클래스 내용
}
```

Final 클래스는 더 이상 상속을 허용하지 않는다. <br> 즉, final 클래스를 상속한 클래스를 만들 수 없다.
```java
final class FinalShape {
    private String name;

    public FinalShape(String name) {
        this.name = name;
    }

    public void describe() {
        System.out.println("This is a " + name);
    }
}

// 컴파일 오류
/*
class CustomShape extends FinalShape {
    public CustomShape(String name) {
        super(name);
    }
}
*/
```

<br>

## 정리
이러한 구조를 사용함으로써 필요한 클래스의 확장을 엄격하게 제어하거나 필요에 따라 확장을 허용할 수 있다.

 Sealed 클래스를 사용함으로써 코드의 안정성을 높일 수 있고, <br> 가독성 향상이 특징인 것 같다.