# LIS - 최장 증가 부분 수열
- 이전의 원소보다 다음 원소가 큰 원소들로만 이루어진 수열 을 (`증가 부분 수열`)이라고한다.
- 이러한 수열에서 가장 긴 증가하는 부분 수열을 찾아야 할때 사용한다.
- 부분 수열은 주어진 수열에서 연속되지 않아도 되며, 순서는 유지되어야 한다. 

즉 ,
`주어진 수열에서 순서를 유지하면서 원소들을 선택하여 구성된 부분 수열 중에서 가장 긴 길이를 갖는 부분 수열을 찾는 것이 목표`

<br>

## LIS 알고리즘
- LIS는 DP와 이분탐색 두가지 방법으로 해결할 수 있다.

> 주어진 수열이 [3, 2, 6, 4, 5, 1]인 경우,<br> 가장 긴 증가하는 부분 수열은 [2, 4, 5], 즉 길이는 3이다. <br>
해당 요구사항으로 알고리즘을 파악해보자.

<br>

### DP를 활용한 LIS 알고리즘 계산
```java
public class LISDPExample {
    public static int lis(int[] nums) {
        int n = nums.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }

        int maxLength = 0;
        for (int length : dp) {
            maxLength = Math.max(maxLength, length);
        }

        return maxLength;
    }

    public static void main(String[] args) {
        int[] nums = {3, 2, 6, 4, 5, 1};
        int lisLength = lis(nums);
        System.out.println("LIS의 길이: " + lisLength);
    }
}
```
- 처음에는 자기 자신만 포함될 것이므로 크기는 1로 초기화
-  수열의 처음부터 현재 인덱스(i) 전까지 탐색을 진행하며 저장된 길이와 최장 부분수열에 값에 1을 더한 길이를 비교하며 그 중 가장 큰 값을 찾아 반환하며 저장해나간다.

<br>

### 이분 탐색을 활용한 LIS 알고리즘 계산
```java
import java.util.ArrayList;
import java.util.List;

public class LISBinarySearchExample {
    public static int lis(int[] nums) {
        List<Integer> lis = new ArrayList<>();

        for (int num : nums) {
            if (lis.isEmpty() || num > lis.get(lis.size() - 1)) {
                lis.add(num);
            } else {
                int left = 0;
                int right = lis.size() - 1;

                while (left < right) {
                    int mid = left + (right - left) / 2;
                    if (lis.get(mid) >= num) {
                        right = mid;
                    } else {
                        left = mid + 1;
                    }
                }

                lis.set(right, num);
            }
        }

        return lis.size();
    }

    public static void main(String[] args) {
        int[] nums = {3, 2, 6, 4, 5, 1};
        int lisLength = lis(nums);
        System.out.println("LIS의 길이: " + lisLength); // 출력: 3
    }
}
```

- 이분 탐색을 사용하여 증가하는 부분 수열을 구성하는 중간 결과를 유지하고, 새로운 원소가 들어오면 이분 탐색을 수행하여 적절한 위치에 삽입한다. 
- 이렇게 유지되는 부분 수열은 원래 수열과 같은 길이를 갖는 LIS가 된다.

<br>

## LIS 장단점
장점 :
- 간결하고 직관적인 구현
- 다양한 응용 가능성
- 시간 복잡도 개선 가능 
    - 두개의 알고리즘 풀이로 시간복잡도 개선 가능성도 가지고있다. 

단점:
- LIS의 실제 원소들을 복원하려면 추가적인 작업이 필요
    - 부분 수열의 원소 순서만을 고려하기 때문에 위치 복원 작업필요   
- 메모리 사용량 증가 가능성
    - 메모리의 증가로 공간복잡도 개선 방법을 고려해야한다.

<br>

## LIS 시간복잡도

DP : O(n^2)

이분탐색 :  O(n log n)

수열의 크기가 매우 클 경우 이분탐색 이용이 시간복잡도 면에서 효율적이다.