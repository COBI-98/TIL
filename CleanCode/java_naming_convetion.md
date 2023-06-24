# Java 명명 규칙

- [의도를 분명히 밝혀라](#의도를-분명히-밝혀라)
- [의미있게 구분하라](#의미있게-구분하라)
- [검색하기 쉬운 이름](#검색하기-쉬운-이름)
- [카멜 표기법](#카멜-표기법)
- [클래스 명명](#클래스-명명)
- [메서드 명명](#메서드-명명)
- [변수와 상수 명명](#변수와-상수-명명)
- [주석의 활용](#주석의-활용)

<br>
<br>

## 의도를 분명히 밝혀라
변수의 존재 이유, 기능, 사용법 등이 변수/함수/클래스명에 드러나야 한다. 따로 주석이 필요하지 않을 정도로.

의미를 함축하거나 독자(코드를 읽는 사람)가 사전지식을 가지고 있다고 가정하지 말자.

- 예시 1
    - Bad
        - int d; // elapsed time in days
    - Good
        - int elapsedTimeInDays;
        - int daysSinceCreation;
        - int daysSinceModification;
        - int fileAgeInDays; 

- 예시 2
```java
// Bad
public List<int[]> test() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x : theList) {
        if (x[0] == 4) {
            list1.add(x);
        }
    }
    return list1;
}
// Good
public List<int[]> getFlaggedCells() {
    List<int[]> flaggedCells = new ArrayList<int[]>();
    for (int[] cell : gameBoard) {
        if (cell[STATUS_VALUE] == FLAGGED) {
            flaggedCells.add(cell);
        }
    }
    return flaggedCells;
}
```
<br>


## 의미있게 구분하라
- 말이 안되는 단어(한 글자만 바꾼다던지 한 단어), [a1, a2, …]과 같이 숫자로 구분하는 경우 주의
- 클래스 이름에 Info, Data와 같은 불용어를 붙이지 말자. 정확한 개념 구분이 되지 않음

#### 예시
- Name VS NameString
- getActiveAccount() VS getActiveAccounts() VS getActiveAccountInfo() (이들이 혼재할 경우 서로의 역할을 정확히 구분하기 어렵다.)
- money VS moneyAmount
- message VS theMessage


<br>

## 검색하기 쉬운 이름
- 상수는 static final과 같이 정의해 쓰자.
- 변수 이름의 길이는 변수의 범위에 비례해서 길어진다

<br>


## 카멜 표기법
- 카멜 표기법은 식별자의 각 단어를 대문자로 시작하되, 첫 단어는 소문자로 작성하는 방식이다.

- 카멜 표기법은 변수와 메소드의 이름을 작성하는 데 자주 사용된다.
> ex) myVariable, calculateSum, getUserDat  => O<br>
> camel_case, CamelCase => X

<br>

## 클래스 명명
- 클래스이름은 명사 또는 명사구로 작성
- 첫 글자는 대문자로 표기
- 클래스의 역할과 의미가 파악되는 이름 선택
- 단수형 명사를 사용하고, 필요한 경우에는 단어를 결합하여 의미를 명확하게 전달
> Car, UserService, DatabaseConnection

<br>

## 메서드 명명
- 메소드의 이름은 동사나 동사구로 작성
- 첫 글자는 소문자 표기
- 메소드의 역할과 수행하는 동작이 파악되도록 이름 선택
- 코드를 읽는 사람이 메소드의 역할을 이해할 수 있도록 명확하고 간결한 이름을 사용
> calculateSum(), sendMessage(), getUserData()

<br>

## 변수와 상수 명명
- 변수와 상수의 이름은 명사 또는 명사구로 작성
- 첫 글자는 소문자 표기
- 변수는 해당 값을 잘 설명하는 이름으로 선택하고, 상수는 대문자와 언더스코어(_) 표기
- 변수와 상수의 역할과 의미를 이해하기 쉽게 작명
> count, totalAmount, MAX_RETRY

<br>

## 주석의 활용
주석은 코드의 가독성을 높이기 위해 사용된다.

코드의 의도나 작동 방식을 설명하거나, 네이밍 규칙에 대한 추가적인 정보를 제공할수 있다. 

주석은 다음을 유의해서 활용한다.
- 주석은 나쁜 코드를 보완하지 못한다
- 코드로 의도를 표현하라


####  좋은 주석이란 
- 의미를 설명하는 주석
- 의미를 명료하게 밝히는 주석
- 결과를 경고하는 주석
- TODO 주석
- 중요성을 강조하는 주석

#### 나쁜 주석
- 같은 이야기를 중복하는 주석
- 오해할 여지가 있는 주석
-함수나 변수로 표현할 수 있다면 주석을 달지 마라
- 위치를 표시하는 주석
- 전역 정보
- 너무 많은 정보
- 모호한 관계


참고자료

[클린 코드 저장소](https://github.com/Yooii-Studios/Clean-Code)
