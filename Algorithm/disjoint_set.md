# Disjoint Set 분리집합


## 배경
분리집합은 주로 컴퓨터 과학 및 알고리즘 문제에서 그래프 관련 문제를 해결하기 위해 개발되었으며, <br> 다음과 같은 상황에 등장하게 되었다.

- 그래프 알고리즘, 최소 신장 트리, 최단 경로, 네트워크 플로우 등 그래프 알고리즘에서 연결 요소와 관련된 문제를 해결하기 위해

- 동등한 원소, 서로 다른 그룹에 속하는 원소를 효과적으로 분류하고, 같은 그룹에 속한 원소를 찾기 위해

- 사이클 탐지, 그래프에서 사이클을 탐지하거나, 특정 연산이 사이클을 생성하는지 여부를 확인하기 위해

<br>

Disjoint set은 서로 중복되지 않는 집합들로 나누어진 원소들에 대한 정보를 저장하고 조작하는 자료구조로, <br> 서로소 집합에 관련된 문제를 더 효율적으로 해결할 수 있게 되었다.

<br>

## 성능 향상
Disjoint Set을 사용하면 다음과 같은 성능 향상을 이룰 수 있다.

- 서로소 집합 관리
    - 그래프의 연결 요소를 찾거나 사이클을 검출하는 데 사용할 수 있다.
- 효율적인 합치기 및 검색
    - Disjoint Set을 효율적으로 합치기(Union)와 원소 검색(Find)할 수 있다.<br> 경로 압축(Path Compression) 및 크기 기반 합치기(Size Union) 기술을 사용하여 연산 시간을 최적화한다.

- 효율적인 그래프 알고리즘
    - 각 원소가 어떤 그룹 또는 카테고리에 속하는지를 효과적으로 관리할 수 있다.
    - 최소 신장 트리(Minimum Spanning Tree)를 찾는 Kruskal 알고리즘에서 사용된다.
- 원소 분류 및 검색
    - 특정 원소의 그룹 또는 카테고리를 검색하는 데 효율적으로 활용할 수 있다.

<br>

## 분리 집합을 구현한 간단한 예제 코드
```java
class DisjointSet {
    private int[] parent;

    public DisjointSet(int size) {
        parent = new int[size];
        for (int i = 0; i < size; i++) {
            parent[i] = i; // 각 원소는 자신을 대표로 가진다.
        }
    }

    // 원소가 속해있는 집합의 부모를 찾는 연산
    public int find(int x) {
        if (parent[x] == x) {
            return x;
        }
        return parent[x] = find(parent[x]); // 경로 압축 기법
    }

    // 하나의 부모만을 가지도록 작은 그룹을 큰 그룹에 합치는 방식
    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            parent[rootX] = rootY; // 두 그룹을 합친다.
        }
    }
}
```

서로소 집합을 효과적으로 관리할 수 있으며, 그래프 알고리즘과 같은 여러 문제를 해결할 때 도움이 될 것이다.
