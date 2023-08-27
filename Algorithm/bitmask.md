# BitMask (비트마스킹)

## 개요

보통 알고리즘 문제를 풀 때 방문했다는 표시로 boolean visit 배열을 많이 사용한다.

BitMask은 해당 visit 배열처럼 이진 비트를 활용하여 각 비트가 특정 의미나 상태를 나타내도록 설계된다.

 각 비트는 0 또는 1의 값을 가지며, 이를 조합하여 다양한 상태나 정보를 나타낼 수 있습니다. 예를 들어, 특정 기능을 켜고 끄거나, 어떤 아이템을 가지고 있는지 등을 나타낼 수 있다.

<br>

## Java 비트마스크 처리

### **bit 표현**
```java
public class BitmaskExample {
    public static void main(String[] args) throws Exception {
		int n1 = 1 << 1;
		int n2 = 1 << 2;
		int n3 = 1 << 3;

		System.out.println(n1);
		System.out.println(Integer.toBinaryString(n1));

		System.out.println(n2);
		System.out.println(Integer.toBinaryString(n2));

		System.out.println(n3);
		System.out.println(Integer.toBinaryString(n3));
	
    }
}
```
> printResult <br>
2
<br>10
<br>4
<br>100
<br>8
<br>1000

bit 연산에서 left shift 를 하면 비트가 한 칸 왼쪽으로 이동한다.

이는 마치 x2 가 되는 것과 같고, 이진수에서는 한 칸 왼쪽으로 이동하는 것이 십진수에서는 x2 를 하는 것으로 방문처리를 표현할 수 있다.

<br>

### **visit 표현: 방문 처리**
먼저, 방문 처리를 위해 visit 배열의 특정 인덱스를 true로 설정하는 방법을 사용합니다. 이때, 해당 인덱스 위치에 해당하는 비트를 1로 설정한다.
```java
int visit = 0; // 초기 값은 0으로 설정

// 방문 처리
visit |= 1 << 1; // index 1을 방문 처리 
visit |= 1 << 2; // index 2를 방문 처리
```
> 이진수의 인덱스는 오른쪽부터 3, 2, 1, 0 <br> 
> visit 변수는 이진 비트로 표현되며, visit = 110

<br>

### **visit 표현: 방문 확인**
방문 확인을 할 때는 AND 연산을 사용하여 특정 비트가 1로 설정되어 있는지 확인할 수 있다. 
AND 연산 결과가 0이 아니라면 해당 비트가 1로 설정되어 있다는 의미이다.
```java
int check1 = visit & (1 << 1); // index 1 방문 확인
boolean hasVisited1 = check1 != 0; // 비트가 1로 설정되어 있는지 확인

int check2 = visit & (1 << 2); // index 2 방문 확인
boolean hasVisited2 = check2 != 0; // 비트가 1로 설정되어 있는지 확인
```

 만약 해당 비트가 1로 설정되어 있다면 check1 또는 check2의 값은 2의 제곱 형태의 수가 되며, 그렇지 않으면 0이다. 
 
 이를 통해 방문 여부를 빠르게 확인할 수 있다.

<br>

## 장,단점
장점:
- 공간 절약: 여러 개의 상태를 하나의 정수로 표현하기 때문에, 메모리 공간을 절약할 수 있다.
- 빠른 연산: 비트 연산을 사용하여 빠르게 상태를 검사하거나 설정할 수 있다.
- 가독성: 복잡한 상태를 간결하게 표현할 수 있어 코드의 가독성을 높여준다.

단점:
- 복잡성: 비트 연산을 다루는 것이 익숙하지 않은 개발자에게는 이해하기 어렵다.
- 한계: 일부 상황에서는 필요한 비트 수가 넘어가면 표현이 어려워진다.
- 디버깅 어려움: 비트 단위로 상태를 표현하기 때문에 디버깅이 어렵다

