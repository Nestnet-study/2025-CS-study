# Graph

그래프는 노드와 간선으로 이루어진 자료 구조이다. 다음과 같은 종류로 나뉜다.

- 무방향 그래프: 간선에 방향이 없음
- 방향 그래프: 간선에 방향이 있음
- 가중치 그래프: 간선에 가중치가 있음
- 트리: 사이클이 없는 연결된 그래프

---

## 자바에서의 그래프

자바에서는 그래프를 구현한 인터페이스/구조체가 존재하지 않는다. 하지만 그래프는 최단 경로, MST 등 사용할 일이 많기에 직접 구현하는 방법을 알아둬야 한다. 보통은 ArrayList나 Map을 사용하여 많이 구현한다.

먼저 ArrayList 기반의 무방향 그래프를 구현해보자.

```java
class Graph {
    ArrayList<Integer>[] adj;

    // 정점 개수를 받아 그래프 초기화
    public Graph(int n) {
        adj = new ArrayList[n];
        for (int i = 0; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
    }

    // 양방향 간선 추가
    void addEdge(int u, int v) {
        adj[u].add(v);
        adj[v].add(u);
    }

    // BFS
    void bfs(int start) {
        boolean[] visited = new boolean[adj.length];
        Queue<Integer> queue = new LinkedList<>();

        visited[start] = true;
        queue.add(start);

        while (!queue.isEmpty()) {
            int curr = queue.poll();
            System.out.print(curr + " ");

            for (int next : adj[curr]) {
                if (!visited[next]) {
                    visited[next] = true;
                    queue.add(next);
                }
            }
        }
    }
}
```

다음은 방향 그래프이다.

```java
class DirectedGraph {
    ArrayList<Integer>[] adj;

    public DirectedGraph(int n) {
        adj = new ArrayList[n + 1];
        for (int i = 0; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
    }

    // 단방향 간선 추가
    void addEdge(int u, int v) {
        adj[u].add(v);
    }
}
```

가중치 그래프도 보자.

```java
class WeightedGraph {
    // 간선 정보 클래스
    static class Edge {
        int to, weight;

        Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    ArrayList<Edge>[] adj;

    public WeightedGraph(int n) {
        adj = new ArrayList[n + 1];
        for (int i = 0; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
    }

    // 양방향 가중치 간선 추가
    void addEdge(int u, int v, int weight) {
        adj[u].add(new Edge(v, weight));
        adj[v].add(new Edge(u, weight));
    }
}
```

Map으로 그래프를 구현하는 경우는 희소 그래프일 때 효율적이다. 문제 풀이에서 사용되지는 않는다. 구현 방법은 vetex 정보들을 Map에 저장하고 Map의 key로 node, value로 key와 연결된 node들의 정보를 List로 담으면 된다.

```java
import java.util.*;

// Map 기반 무방향 그래프
class MapGraph {
    Map<Integer, List<Integer>> adj;

    // 그래프 초기화
    public MapGraph() {
        adj = new HashMap<>();
    }

    // 정점 추가
    void addVertex(int v) {
        adj.putIfAbsent(v, new ArrayList<>());
    }

    // 간선 추가
    void addEdge(int u, int v) {
        // 정점이 없으면 자동 추가
        addVertex(u);
        addVertex(v);

        // 양방향 연결
        adj.get(u).add(v);
        adj.get(v).add(u);
    }
}

// Map 기반 가중치 그래프
class MapWeightedGraph {
    // 간선 정보 클래스
    static class Edge {
        int to, weight;

        Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    Map<Integer, List<Edge>> adj;

    public MapWeightedGraph() {
        adj = new HashMap<>();
    }

    void addVertex(int v) {
        adj.putIfAbsent(v, new ArrayList<>());
    }

    // 양방향 가중치 간선 추가
    void addEdge(int u, int v, int weight) {
        addVertex(u);
        addVertex(v);

        adj.get(u).add(new Edge(v, weight));
        adj.get(v).add(new Edge(u, weight));
    }
}
```

---

<aside>
<img src="/icons/light-bulb_yellow.svg" alt="/icons/light-bulb_yellow.svg" width="40px" />

이번 주제는 딱히 예상 문제가 없습니다.

</aside>

# Graph

그래프는 노드와 간선으로 이루어진 자료 구조이다. 다음과 같은 종류로 나뉜다.

- 무방향 그래프: 간선에 방향이 없음
- 방향 그래프: 간선에 방향이 있음
- 가중치 그래프: 간선에 가중치가 있음
- 트리: 사이클이 없는 연결된 그래프

---

## 자바에서의 그래프

자바에서는 그래프를 구현한 인터페이스/구조체가 존재하지 않는다. 하지만 그래프는 최단 경로, MST 등 사용할 일이 많기에 직접 구현하는 방법을 알아둬야 한다. 보통은 ArrayList나 Map을 사용하여 많이 구현한다.

먼저 ArrayList 기반의 무방향 그래프를 구현해보자.

```java
class Graph {
    ArrayList<Integer>[] adj;

    // 정점 개수를 받아 그래프 초기화
    public Graph(int n) {
        adj = new ArrayList[n];
        for (int i = 0; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
    }

    // 양방향 간선 추가
    void addEdge(int u, int v) {
        adj[u].add(v);
        adj[v].add(u);
    }

    // BFS
    void bfs(int start) {
        boolean[] visited = new boolean[adj.length];
        Queue<Integer> queue = new LinkedList<>();

        visited[start] = true;
        queue.add(start);

        while (!queue.isEmpty()) {
            int curr = queue.poll();
            System.out.print(curr + " ");

            for (int next : adj[curr]) {
                if (!visited[next]) {
                    visited[next] = true;
                    queue.add(next);
                }
            }
        }
    }
}
```

다음은 방향 그래프이다.

```java
class DirectedGraph {
    ArrayList<Integer>[] adj;

    public DirectedGraph(int n) {
        adj = new ArrayList[n + 1];
        for (int i = 0; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
    }

    // 단방향 간선 추가
    void addEdge(int u, int v) {
        adj[u].add(v);
    }
}
```

가중치 그래프도 보자.

```java
class WeightedGraph {
    // 간선 정보 클래스
    static class Edge {
        int to, weight;

        Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    ArrayList<Edge>[] adj;

    public WeightedGraph(int n) {
        adj = new ArrayList[n + 1];
        for (int i = 0; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
    }

    // 양방향 가중치 간선 추가
    void addEdge(int u, int v, int weight) {
        adj[u].add(new Edge(v, weight));
        adj[v].add(new Edge(u, weight));
    }
}
```

Map으로 그래프를 구현하는 경우는 희소 그래프일 때 효율적이다. 문제 풀이에서 사용되지는 않는다. 구현 방법은 vetex 정보들을 Map에 저장하고 Map의 key로 node, value로 key와 연결된 node들의 정보를 List로 담으면 된다.

```java
import java.util.*;

// Map 기반 무방향 그래프
class MapGraph {
    Map<Integer, List<Integer>> adj;

    // 그래프 초기화
    public MapGraph() {
        adj = new HashMap<>();
    }

    // 정점 추가
    void addVertex(int v) {
        adj.putIfAbsent(v, new ArrayList<>());
    }

    // 간선 추가
    void addEdge(int u, int v) {
        // 정점이 없으면 자동 추가
        addVertex(u);
        addVertex(v);

        // 양방향 연결
        adj.get(u).add(v);
        adj.get(v).add(u);
    }
}

// Map 기반 가중치 그래프
class MapWeightedGraph {
    // 간선 정보 클래스
    static class Edge {
        int to, weight;

        Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    Map<Integer, List<Edge>> adj;

    public MapWeightedGraph() {
        adj = new HashMap<>();
    }

    void addVertex(int v) {
        adj.putIfAbsent(v, new ArrayList<>());
    }

    // 양방향 가중치 간선 추가
    void addEdge(int u, int v, int weight) {
        addVertex(u);
        addVertex(v);

        adj.get(u).add(new Edge(v, weight));
        adj.get(v).add(new Edge(u, weight));
    }
}
```
