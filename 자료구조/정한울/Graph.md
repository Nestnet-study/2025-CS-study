# Graph
그래프(Graph)는 <b>정점(Vertex)</b>과 <b>간선(Edge)</b>으로 이루어진 자료구조.
<img src="img/graph.png" style="width:50%;" />

### 용어 정리
- **정점(Vertex)**: 그래프의 노드(node)로, 그래프의 구성 요소.
- **간선(Edge)**: 정점과 정점을 연결하는 선으로, 그래프의 연결 관계를 나타냄.
- **인접(Adjacent)**: 두 정점이 간선으로 연결되어 있는 경우.
- **차수(Degree)**: 정점에 연결된 간선의 개수.
  - **진입 차수(In-Degree)**: 정점으로 들어오는 간선의 개수.
  - **진출 차수(Out-Degree)**: 정점에서 나가는 간선의 개수.
- **사이클(Cycle)**: 시작 정점과 끝 정점이 같은 경로.
<br>

## 그래프의 종류
### 뱡향성
- **방향 그래프(Directed Graph)**: 간선에 방향이 있는 그래프.
- **무방향 그래프(Undirected Graph)**: 간선에 방향이 없는 그래프.
### 가중치
- **가중치 그래프(Weighted Graph)**: 간선에 가중치가 있는 그래프.
- **비가중치 그래프(Unweighted Graph)**: 간선에 가중치가 없는 그래프.
### 순환성
- **순환 그래프(Cyclic Graph)**: 사이클이 있는 그래프.
- **비순환 그래프(Acyclic Graph)**: 사이클이 없는 그래프. DAG(Directed Acyclic Graph)라고도 함.
<br>

## 그래프의 구현
### 인접 행렬(Adjacency Matrix)
```java
int[][] graph = new int[n][n];  // n: 정점 수
```
`graph[i][j] = 1`이면 i -> j의 간선이 존재
- **장점** : 간선의 존재 여부를 O(1)로 확인 가능.
- **단점** : 메모리 낭비가 심함.
### 인접 리스트(Adjacency List)
```java
List<List<Integer>> graph = new ArrayList<>();
```
`graph.get(i)`는 i번 정점의 연결 리스트
- **장점** : 메모리 사용량이 적음.
- **단점** : 간선의 존재 여부를 O(V)로 확인해야 함.
<br>

---
### 문제
1. 소셜 네트워크에서 친구 추천 기능을 어떻게 설계할 건가요?