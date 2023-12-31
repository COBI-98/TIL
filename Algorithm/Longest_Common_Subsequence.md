# LCS - 최장 공통 부분 수열
- 두 개의 문자열에서 공통으로 나타나는 가장 긴 부분을 찾는것
- 수열은 원래 문자열에서 원소의 순서는 유지되지만, 연속적으로 나타나지 않아야한다.

> ex) "ABCBDAB"와 "BDCAB" 두 개의 문자열이 주어졌을 때, <br>
가장 긴 공통 부분 수열은 "BCAB"이다.

LCS 알고리즘은 다이나믹 프로그래밍(Dynamic Programming)을 활용하여 문제를 해결한다. 

주어진 두 문자열을 기준으로 이차원 배열을 생성하고, 
<br>
각 위치에서의 LCS의 길이를 계산하여 배열을 채워나간다. 

이를 통해 `최장 공통 부분 수열의 길이`를 찾을 수 있게 된다.

<br>

## DP를 활용한 LCS 알고리즘 계산
```java
public class LCSDPExample {
    public static int lcs(String text1, String text2) {
        int m = text1.length();
        int n = text2.length();
        int[][] dp = new int[m + 1][n + 1];

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (text1.charAt(i - 1) == text2.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp[m][n];
    }

    public static void main(String[] args) {
        String text1 = "ABCBDAB";
        String text2 = "BDCAB";
        int lcsLength = lcs(text1, text2);
        System.out.println("LCS의 길이: " + lcsLength); // 출력: 4
    }
}
```

DP 테이블 그리기 
```lua
     |  yi  |  B  |  D  |  C  |  A  |  B  |
-----|-------|-----|-----|-----|-----|-----|
 xi  |   0   |  0  |  0  |  0  |  0  |  0  |
-----|-------|-----|-----|-----|-----|-----|
  A  |   0   |  0  |  0  |  0  |  1  |  1  |
-----|-------|-----|-----|-----|-----|-----|
  B  |   0   |  1  |  1  |  1  | "1" |  2  |
-----|-------|-----|-----|-----|-----|-----|
  C  |   0   |  1  |  1  |  2  |  2  | "2" |
-----|-------|-----|-----|-----|-----|-----|
  B  |   0   |  1  |  1  |  2  |  2  |  3  |
-----|-------|-----|-----|-----|-----|-----|
  D  |   0   |  1  |  2  |  2  |  2  |  3  |
-----|-------|-----|-----|-----|-----|-----|
  A  |   0   |  1  |  2  |  2  |  3  |  3  |
-----|-------|-----|-----|-----|-----|-----|
  B  |   0   |  1  |  2  |  2  |  3  |  4  |

```

- 위 테이블의 각 셀은 lcs의 길이를 나타낸다. <br>
ex) (3,5) 값은 `ABCBDAB`의 첫 3개문자 **ABC**와 `BDCAB`의 첫 5개문자 **BDCAB**를 고려했을 때,<br> 
2의 길이(`AB`)가 나오고 해당 값은 (2,4) 위치의 값에 1을 더한 값이 된다.

이러한 방식으로 DP 테이블을 채우면 마지막 행과 열의 값이 전체 문자열에 대한 LCS의 길이가 된다. <br> 이를 통해 최장 공통 부분 수열의 길이를 찾을 수 있다.

<br>

## LCS 장단점
장점 :
- 비교적 간단한 구현과 이해 가능성
- 다양한 응용 가능성
- 시간 복잡도 개선 가능 
    - 수행 시간을 개선하기위해 메모이제이션 사용 가능

단점:
- 모든 공통 부분 수열을 찾지 않는다.
    - 길이에 초점을 맞춘다.   
- 부분 수열의 순서만을 고려한다.
- 시간복잡도 증가 가능성
    - 메모리의 증가로 최적화 하는 방법을 찾아야한다.

<br>

## LCS 시간 복잡도
- 시간 복잡도는 O(mn) 
    - 문자열의 길이가 m과 n인 경우, <br> 이차원 배열을 채우는 과정에서 모든 원소를 한 번씩 방문