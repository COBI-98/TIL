# Tree

트리는 스택이나 큐와 같은 선형 구조가 아닌 비선형 구조이며, <br> 계층적 관계(Hierarchical Relationship)을 표현할 때 사용되며 각각 다른 용도와 장단점을 가지고 있다.

<br>

## 등장배경
트리는 계층적인 데이터를 효율적으로 표현하고 관리하기 위해 등장했다.

예를 들어 파일 시스템은 디렉토리와 파일을 트리구조로 표현하여 사용자가 쉽게 탐색하고 조작할 수 있도록한다. 
이처럼, db의 인덱스를 구현하거나
계층적 데이터를 표현할 때 트리구조의 장점이 드러난다.

<br>

## 구성 요소

- 노드(Node): 트리의 기본 요소로 데이터를 저장하며,  각 노드는 부모 노드와 자식 노드로 연결

- 간선(Edge): 노드들을 연결하는 선으로, 부모 노드와 자식 노드 사이의 관계를 나타냄

- 루트(Root): 트리의 가장 상위 노드로, 모든 노드는 루트로부터 시작하는 경로가 있어야 한다.

- 자식(Child): 어떤 노드의 하위에 연결된 노드를 자식이라고 한다.

- 부모(Parent): 어떤 노드의 상위에 연결된 노드를 부모라고 한다.

- 리프(Leaf) == (Terminal): 자식이 없는 노드로, 가장 하위에 있는 노드를 가리킨다.

- 서브 트리(Subtree): 트리 내에서 노드와 그 자손 노드들로 이루어진 부분 트리를 의미한다.

<br>

## 장,단점
장점
- 빠른 검색 및 삽입
    - 특정 노드에 접근하거나 새로운 노드를 삽입조직적인 구조를 표현하는 데 효과적
    - 이진 탐색 트리(Binary Search Tree)와 같은 트리 구조는 빠른 검색 연산을 지원
- 자동 정렬
    - 이진 탐색 트리와 같은 트리는 데이터를 정렬된 상태로 유지하므로 정렬된 데이터에 접근이 필요한 경우 유용

단점
- 메모리 소비
    -  포인터를 사용하므로 많은 메모리를 소비
- 삽입 및 삭제 복잡성
    - 트리의 균형을 유지하기 위해 회전 연산이 필요해 삽입 및 삭제 연산의 복잡성이 증가
- 순회 비용
    - 선형 데이터 구조에 비해 느릴 수 있다.

<br>

## 다양한 트리 기반 알고리즘

### **이진 트리 (Binary Tree)**
각 노드가 최대 두 개의 자식 노드를 가지는 트리 구조로, <br>
다양한 문제를 해결하기 위한 기본적인 자료 구조이다.

트리에서는 각 층별로 숫자를 매겨서 이를 트리의 Level(레벨)이라고 하며,<br>
레벨의 값은 0 부터 시작하고 따라서 루트 노드의 레벨은 0 이다. <br> 그리고 트리의 최고 레벨을 가리켜 해당 트리의 height(높이)라고 한다.


모든 레벨이 꽉 찬 이진 트리를 가리켜 `포화 이진 트리`, <br>
위에서 아래로, 왼쪽에서 오른쪽으로 순서대로 차곡차곡 채워진 이진 트리를 가리켜 `완전 이진 트리`, <br>
모든 노드가 0개 혹은 2개의 자식 노드만을 갖는 이진 트리를 가리켜 `정 이진 트리` <br> 
등 다양한 이진 트리의 종류는 각각 다른 용도와 장 단점을 가질 수 있다.

<br>

### **이진 탐색 트리 (Binary Search Tree, BST)**
이진 탐색 트리는 이진 트리의 일종이다. <br>단, 이진 탐색 트리에는 데이터를 저장하는 규칙이 있다. <br> 그리고 그 규칙은 특정 데이터의 위치를 찾는데 사용할 수 있다.

- 규칙 1. 이진 탐색 트리의 노드에 저장된 키는 유일하다.
- 규칙 2. 부모의 키가 왼쪽 자식 노드의 키보다 크다.
- 규칙 3. 부모의 키가 오른쪽 자식 노드의 키보다 작다.
- 규칙 4. 왼쪽과 오른쪽 서브트리도 이진 탐색 트리이다.


규칙에 맞추어 BST 예시로 구조를 자세히 알아보자.
```java
class TreeNode {
    int data;
    TreeNode left;
    TreeNode right;

    public TreeNode(int data) {
        this.data = data;
        this.left = null;
        this.right = null;
    }
}

class BinarySearchTree {
    TreeNode root;

    public BinarySearchTree() {
        this.root = null;
    }

    // 삽입 메서드
    public void insert(int data) {
        root = insertRec(root, data);
    }

    // 삽입을 재귀적으로 수행하는 도우미 메서드
    private TreeNode insertRec(TreeNode root, int data) {
        if (root == null) {
            root = new TreeNode(data);
            return root;
        }

        if (data < root.data) {
            root.left = insertRec(root.left, data);
        } else if (data > root.data) {
            root.right = insertRec(root.right, data);
        }

        return root;
    }

    // 검색 메서드
    public boolean search(int data) {
        return searchRec(root, data);
    }

    // 검색을 재귀적으로 수행하는 도우미 메서드
    private boolean searchRec(TreeNode root, int data) {
        if (root == null) {
            return false;
        }

        if (root.data == data) {
            return true;
        } else if (data < root.data) {
            return searchRec(root.left, data);
        } else {
            return searchRec(root.right, data);
        }
    }

    // 중위 순회 메서드
    public void inorderTraversal() {
        inorderTraversalRec(root);
    }

    // 중위 순회를 재귀적으로 수행하는 도우미 메서드
    private void inorderTraversalRec(TreeNode root) {
        if (root != null) {
            inorderTraversalRec(root.left);
            System.out.print(root.data + " ");
            inorderTraversalRec(root.right);
        }
    }
}
```

배열보다 많은 메모리를 사용하며 데이터를 저장했지만 탐색에 필요한 시간 복잡도가 같게 되는 비효율적인 상황이 발생할 수 있다.
이는 다른 기법으로 트리 구조의 재조정(Rebalancing)을 할 수 있다. 

<br>

### **자식 수가 정해지지 않은 트리 (General Tree)**
자식 수가 정해지지 않은 트리는 각 노드가 임의의 수의 자식 노드를 가질 수 있는 트리이다.

General Tree 예시로 트리 구조를 자세히 알아보자.
```java
import java.util.ArrayList;
import java.util.List;

// 자식 수가 정해지지 않은 트리의 노드를 나타내는 클래스
// 각 노드는 데이터 값과 자식 노드 목록을 가지게됨 
class TreeNode {
    int data;
    List<TreeNode> children;

    public TreeNode(int data) {
        this.data = data;
        this.children = new ArrayList<>();
    }
}

//자식 수가 정해지지 않은 트리 자체를 관리하는 클래스
class GeneralTree {
    TreeNode root;

    public GeneralTree(int data) {
        this.root = new TreeNode(data);
    }

    public void addChild(TreeNode parent, int data) {
        TreeNode child = new TreeNode(data);
        parent.children.add(child);
    }
}
```

<br>

### **세그먼트 트리 (Segment Tree)**
세그먼트 트리는 배열 또는 리스트와 같은 데이터 구조에서 구간 `합, 최솟값, 최댓값` 등을 효율적으로 구하는 데 사용되는 자료 구조이다.

세그먼트 트리 구현 예시로 트리 구조를 자세히 알아보자.
```java
class SegmentTree {
    int[] tree;
    int n;

    public SegmentTree(int[] nums) {
        n = nums.length;
        tree = new int[2 * n];
        buildTree(nums);
    }

    // 세그먼트 트리를 구축
    private void buildTree(int[] nums) {
        for (int i = n, j = 0; i < 2 * n; i++, j++) {
            tree[i] = nums[j];
        }
        for (int i = n - 1; i > 0; i--) {
            tree[i] = tree[2 * i] + tree[2 * i + 1];
        }
    }

    //  세그먼트 트리를 업데이트하는 메서드
    public void update(int index, int val) {
        index += n;
        tree[index] = val;
        while (index > 0) {
            int left = index;
            int right = index;
            if (index % 2 == 0) {
                right = index + 1;
            } else {
                left = index - 1;
            }
            tree[index / 2] = tree[left] + tree[right];
            index /= 2;
        }
    }

    // 구간 합 쿼리를 수행하는 메서드
    public int query(int left, int right) {
        left += n;
        right += n;
        int sum = 0;
        while (left <= right) {
            if ((left % 2) == 1) {
                sum += tree[left];
                left++;
            }
            if ((right % 2) == 0) {
                sum += tree[right];
                right--;
            }
            left /= 2;
            right /= 2;
        }
        return sum;
    }
}
```

<br>

### **인덱스 트리 (Binary Indexed Tree 또는 Fenwick Tree)**
인덱스 트리(Binary Indexed Tree, BIT) 또는 Fenwick Tree는 주로 구간 합을 빠르게 계산하기 위한 자료 구조이다.

 이러한 자료 구조는 특히 배열의 부분 합을 효율적으로 관리하고 업데이트하는 데 사용될 수 있다.

 다음은 BIT 구현 예시이다.
 ```java
 class BITree {
    int[] tree;
    int n;

    public BITree(int size) {
        n = size;
        tree = new int[n + 1]; // 1-based indexing를 사용하기 위해 크기를 n + 1로 설정
    }

    // 인덱스 i에 해당하는 값을 반환 (부분 합 계산)
    public int query(int index) {
        index++; // 1-based indexing로 변환
        int sum = 0;
        while (index > 0) {
            sum += tree[index];
            index -= index & -index; // 마지막 비트를 지우는 방식으로 상위 노드로 이동
        }
        return sum;
    }

    // 인덱스 i에 해당하는 값을 val만큼 증가시킴
    public void update(int index, int val) {
        index++; // 1-based indexing로 변환
        while (index <= n) {
            tree[index] += val;
            index += index & -index; // 마지막 비트를 더하는 방식으로 상위 노드로 이동
        }
    }
}
 ```

 인덱스 트리는 구간 합을 빠르게 계산하는 데 사용되며, 특히 대용량 데이터에서 유용하다.

<br>

 ## 정리
트리의 장점은 특정 종류의 작업과 알고리즘에 매우 유용하게 사용될 수 있으며, <br> 데이터 구조 선택 시 고려해야 하는 중요한 요소 중 하나이다.

그러나 트리 또한 일부 상황에서는 단점을 가질 수 있으며, 이러한 단점에 대한 이해와 적절한 트리 구현 선택이 중요할 것이다.

<br>

## reference
[Tree DataStructure
](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/DataStructure)