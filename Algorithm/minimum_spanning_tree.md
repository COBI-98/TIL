# 최소 신장 트리(MST, Minimim Spanning Tree)

MST는 'Minimum Spanning Tree'의 약자로, 최소 신장 트리를 의미한다.

그래프는 정점과 그 정점들을 연결하는 간선으로 구성되어 있는데, <br>
신장 트리(spanning tree)는 원래 그래프의 모든 정점을 포함하면서 사이클이 없는 부분 그래프이다.

여기서 핵심은 간선에 비용이 할당되어있는 그래프의 `모든 정점을 최소한의 비용`으로 연결하는 것에 있다.

<br>

## **등장 배경**
MST, 최소신장 트리는 <br>
네트워크 디자인, 도로 건설, 전기회로 설계 등 <br>
인접한 리스트, 분야에서 최소 비용 문제를 해결하기 위해 등장했다.

여러 도시를 도로로 연결 할 때 가능한 가장 적은 비용으로 <br>
모든 도시를 연결하려면 어떻게 해야할까?

MST 알고리즘이 그 답을 도와줄 수 있다. 

<br>

## MST 알고리즘의 동작과정

### **크루스칼 알고리즘**
Union-Find 자료구조를 사용하여 간선들을 비용의 오름차순으로 정렬한 후, <br>
사이클을 형성하지 않는 간선들을 선택하여 구성한다.

```java
import java.util.*;

class Edge implements Comparable<Edge> {
    int src, dest, cost;

    public Edge(int src, int dest, int cost) {
        this.src = src;
        this.dest = dest;
        this.cost = cost;
    }

    public int compareTo(Edge o) {
        return this.cost - o.cost;
    }
}

class Graph {
    int vertices, edges;
    Edge[] edge;

    Graph(int v, int e) {
        vertices = v;
        edges = e;
        edge = new Edge[e];
        for (int i = 0; i < e; ++i)
            edge[i] = new Edge(0, 0, 0);
    }

    int find(int[] parent, int i) {
        if (parent[i] == i)
            return i;
        return find(parent, parent[i]);
    }

    void union(int[] parent, int x, int y) {
        int xroot = find(parent, x);
        int yroot = find(parent, y);
        parent[xroot] = yroot;
    }

    void KruskalMST() {
        Edge[] result = new Edge[vertices];
        int e = 0;
        int i = 0;
        for (i = 0; i < vertices; ++i)
            result[i] = new Edge(0, 0, 0);

        Arrays.sort(edge);

        int[] parent = new int[vertices];
        for (i = 0; i < vertices; ++i)
            parent[i] = i;

        i = 0;
        while (e < vertices - 1) {
            Edge next_edge = edge[i++];

            int x = find(parent, next_edge.src);
            int y = find(parent, next_edge.dest);

            if (x != y) {
                result[e++] = next_edge;
                union(parent, x, y);
            }
        }

        // Print the MST
        for (i = 0; i < e; ++i)
            System.out.println(result[i].src + " - " + result[i].dest + ": " + result[i].cost);
    }
}
```

<br>

### 프림 알고리즘
프림 알고리즘은 시작 정점에서 출발하여, <br>스패닝 트리 집합을 단계적으로 확장해나가는 방식이다.
```java
public class PrimAlgorithm {
    private static final int INF = Integer.MAX_VALUE;

    public static void primMST(int graph[][]) {
        int V = graph.length;
        int[] parent = new int[V]; // 최소 스패닝 트리
        int[] key = new int[V]; // 가중치를 저장할 배열
        boolean[] mstSet = new boolean[V]; // MST에 포함된 정점들

        Arrays.fill(key, INF);
        key[0] = 0; // 첫 번째 정점을 시작점으로 설정
        parent[0] = -1; // 첫 번째 정점은 부모가 없음

        for (int count = 0; count < V - 1; count++) {
            int u = minKey(key, mstSet);
            mstSet[u] = true;

            for (int v = 0; v < V; v++) {
                if (graph[u][v] != 0 && !mstSet[v] && graph[u][v] < key[v]) {
                    parent[v] = u;
                    key[v] = graph[u][v];
                }
            }
        }

        printMST(parent, graph);
    }

    private static int minKey(int[] key, boolean[] mstSet) {
        int min = INF, minIndex = -1;

        for (int v = 0; v < key.length; v++) {
            if (!mstSet[v] && key[v] < min) {
                min = key[v];
                minIndex = v;
            }
        }

        return minIndex;
    }

    private static void printMST(int[] parent, int graph[][]) {
        System.out.println("Edge \tWeight");
        for (int i = 1; i < graph.length; i++) {
            System.out.println(parent[i] + " - " + i + "\t" + graph[i][parent[i]]);
        }
    }
```

<br>

## **장, 단점**
### 크루스칼 알고리즘 

장점
- 그리디 알고리즘의 한 예로, 상대적으로 이해하고 구현하기 쉽다.
- 간선의 수가 적은 스파스 그래프에서 효율적으로 동작
- 유니온 파인드를 사용하여 사이클을 효율적으로 체크할 수 있어, 대규모 데이터를 다루는 데 적합하다.

단점
- 모든 간선을 가중치에 따라 정렬해야 하므로, 정렬 알고리즘의 시간 복잡도에 영향을 미친다.
- 간선의 수가 많은 밀집 그래프에서는 프림 알고리즘에 비해 비효율적일 수 있습니다

### 프림 알고리즘

장점
- 간선의 수가 많은 그래프에서 좋다
- 우선순위 큐 사용, 최소 힙 등을 이용하여 다음에 연결할 가장 가벼운 간선을 효율적으로 선택

단점
- 프림 알고리즘은 크루스칼 알고리즘보다 구현이 복잡하다.
- 간선이 적은 그래프에서는 크루스칼 알고리즘에 비해 비효율적이다.

<br>

## **시간복잡도**
크루스칼(Kruskal) 알고리즘
- O(ElogE) 또는 O(ElogV) (E는 간선의 수, V는 정점의 수)

프림(Prim) 알고리즘
- 인접 리스트와 최소 힙을 사용할 경우 O(E + VlogV)
- 인접 행렬을 사용할 경우 O(V^2)

<br>

## **사용 가능한 분야**
네트워크 설계
- 인터넷, 전화망 등 최소 비용으로 네트워크를 구축하는 데 사용 

도로, 전기망 설계
-  도시나 지역을 연결하는 도로망, 전기망을 구축할 때 최소 비용으로 모든 지역을 연결

클러스터링 알고리즘
- 데이터를 클러스터링할 때 MST를 사용하여 유사한 데이터 포인트를 연결

이미지 처리
- 이미지의 세그멘테이션(segmentation)에서 MST를 활용하여 픽셀을 효과적으로 그룹화

<br>

## 정리
MST 문제는 종종 "모든 요소를 최소 비용으로 연결하라"와 같은 형태로 제시되므로 <br> Java로 MST 알고리즘을 구현할 때는 우선순위 큐, 연결 리스트, 배열 등을 효과적으로 사용해야한다.