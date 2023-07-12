# 정수론


## 정수론이란?
정수와 정수 간의 관계, 소수와 약수, 모듈러 연산 등을 다루는 수학 분야로써, 암호학과 알고리즘 분야에서는 정수론의 이론들이 다양한 암호화 알고리즘과 키 관리에 사용된다.

정수론에 사용되는 이론과 구현방법을 연결지어 정리해보자.

<br>

**소수와 소인수분해**

- 소수(Prime number)
     - 1과 자기 자신만을 약수로 갖는 수를 의미 소수는 암호화에서 중요한 역할
     - 대표적인 소수 생성 알고리즘 중 하나는 에라토스테네스의 체(Sieve of Eratosthenes)이다.
- 소인수분해(Factorization): 
    - 주어진 수를 소수의 곱으로 분해하는 과정을 의미
    - 대표적인 소인수분해 알고리즘으로는 페르마의 소인수분해 알고리즘(Fermat's Factorization Method)이 있습니다.

**최대공약수와 최소공배수**

- 최대공약수(GCD, Greatest Common Divisor)
    - 두 수의 공통된 가장 큰 약수를 의미
    - 유클리드 호제법(Euclidean Algorithm)은 두 수의 최대공약수를 구하는데 사용됩니다.
- 최소공배수(LCM, Least Common Multiple)
    - 두 수의 공통된 가장 작은 배수를 의미


**모듈러 연산과 역원**

- 모듈러 연산(Modular Arithmetic)
    - 두 수의 나눗셈을 특정한 수로 나눈 나머지를 계산하는 연산
    - 모듈러 연산은 대칭키 암호와 공개키 암호에서 사용
- 역원(Inverse)

    - 모듈러 연산에서 특정한 수의 곱셈에 대한 역원을 찾는 것을 의미
    - 역원은 RSA와 같은 공개키 암호화에서 중요한 개념

<br>

## 주요 이론 활용 코드 정리

### 1. 소수 생성 예시 (에라토스테네스의 체 알고리즘)
```java
public class PrimeNumberExample {

    public static void main(String[] args) {
        int n = 100;
        boolean[] isPrime = new boolean[n + 1];

        // 초기화
        Arrays.fill(isPrime, true);
        isPrime[0] = isPrime[1] = false;

        // 소수 판별 에라토스테네스의 체
        // 2부터  ~ i*i <= n
		// 각각의 배수들을 지워간다.
        for (int i = 2; i * i <= n; i++) {
            if (isPrime[i]) {
                for (int j = i * i; j <= n; j += i) {
                    isPrime[j] = false;
                }
            }
        }

        // 소수 출력
        for (int i = 2; i <= n; i++) {
            if (isPrime[i]) {
                System.out.print(i + " ");
            }
        }
    }
}
```

- 에라토스테네스의 체 알고리즘을 사용하여 2부터 n까지의 소수를 찾는다. 
- 배열 isPrime을 사용하여 각 수가 소수인지를 표시하고, 반복문을 통해 소수를 출력한다.
- 시간 복잡도: O(n log log n)

<br>

### 2. 최대공약수와 최소공배수 예시 (유클리드 호제법):
```java
public class GCDAndLCMExample {

    public static void main(String[] args) {
        int number1 = 24;
        int number2 = 36;

        // 최대공약수 계산
        int gcd = calculateGCD(number1, number2);
        System.out.println("GCD: " + gcd);

        // 최소공배수 계산
        int lcm = calculateLCM(number1, number2);
        System.out.println("LCM: " + lcm);
    }

// 1071과 1029의 최대공약수를 구하면,
// 1071은 1029로 나누어 떨어지지 않기 때문에, 1071을 1029로 나눈 나머지를 구한다. ≫ 42
// 1029는 42로 나누어 떨어지지 않기 때문에, 1029를 42로 나눈 나머지를 구한다. ≫ 21
// 42는 21로 나누어 떨어진다. == 최대공약수 21
    private static int calculateGCD(int a, int b) {
        while (b != 0) {
            int temp = b;
            b = a % b;
            a = temp;
        }
        return a;
    }

    private static int calculateLCM(int a, int b) {
        return a * (b / calculateGCD(a, b));
    }
}
```
- calculateGCD() 메소드는 최대공약수를 계산
- calculateLCM() 메소드는 최소공배수를 계산
- 시간 복잡도: O(log min(a, b))


### 3. 모듈러 연산과 역원 예시
```java
public class ModularArithmeticExample {

    public static void main(String[] args) {
        int number = 7;
        int modulus = 26;

        // 모듈러 덧셈
        int result = (number + 5) % modulus;
        System.out.println("Modular Addition: " + result);

        // 모듈러 곱셈
        result = (number * 10) % modulus;
        System.out.println("Modular Multiplication: " + result);

        // 역원 계산
        int inverse = calculateInverse(number, modulus);
        System.out.println("Inverse: " + inverse);
    }

    private static int calculateInverse(int a, int m) {
        for (int x = 1; x < m; x++) {
            if ((a * x) % m == 1) {
                return x;
            }
        }
        return -1;
    }
}

```

- 모듈러 덧셈과 모듈러 곱셈을 수행
-  calculateInverse() 메소드를 사용하여 역원 계산
 - calculateInverse() 메소드는 가능한 x 값을 1부터 m-1까지 반복하여 (a * x) % m이 1이 되는 값을 찾아 반환
 - 시간 복잡도: O(log m)

 정수론이 필요한 알고리즘 문제에서 고려하여 활용한다면 연산과정의 성능 향상이 보장될 것 같다.