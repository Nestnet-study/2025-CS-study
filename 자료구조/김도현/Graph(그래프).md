
## 1. Graph(그래프)

그래프는 노드(또는 정점)와 이들을 연결하는 간선으로 구성된 자료구조이다. 그래프는 네트워크 구조를 표현하는데 매우 유용하다. 또한 최소 신장 트리(MST), 깊이우선탐색(DFS), 너비 우선탐색(BFS)과 같은 알고리즘에서 기본적으로 사용하는 자료구조 중 하나이다.

그래프는 G = (V, E)는 노드(정점)과 간선으로 이루어져있다.

- V: 정점(Vertex) 집합
- E: 간선(Edge) 집합


그래프는 방향 그래프, 무방향 그래프, 가중치 그래프로 크게 3가지 유형이 존재한다.

![](https://i.imgur.com/OgAvjj1.png)

간선(Edge)에 방향의 존재 유무를 통해 방향그래프와 무방향 그래프로 나눌 수 있으며, 간선에 가중치가 있으면 가중치 그래프로 분류한다.

방향 그래프에 가중치가 있는 경우 방향 가중치 그래프이고, 무방향에도 마찬가지로 가중치가 존재한다면, 무방향 가중치 그래프이다. 


또한 그래프를 구현하는데 있어, **인접 행렬**과 **인접 리스트** 방식 2가지가 존재한다.
필자는 인접 리스트 방식으로 구현해보도록 하겠다. 


## 2. javasciprt 그래프 구현

### 1. 기본 구조 및 생성자

```js
class Graph {
  constructor(isDirected = false) {
    this.isDirected = isDirected;
    this.adjacencyList = new Map();
  }
}
```

`isDirected` 매개변수로 방향그래프 인지 아닌지 설정할 수 있도록 구현하였다.
실제로 방향 그래프인지, 무방향 그래프인지는 구현함에 있어 인접리스트인 value에 한쪽 노드에만 다른 노드를 표현하는것과 양쪽 노드에서 서로 노드를 적는것 밖에 차이가 없다.
이부분에 대해서는 간선 추가에서 추가설명하도록 하겠다. 

`adjacencyList` 는 JavaScript의 Map 객체를 사용하여 인접 리스트 구조를 구현하였다.
Map자료구조에 대해서는 [[Map(맵)]]을 참고하길 바란다.

Map자료구조에서 각 **정점을 Key**로하고, 해당 정점에 **연결된 정점들의 배열을 값(value)**로 저장한다. 

### 2. 정점 추가

```js
addVertex(vertex) {
  if (!this.adjacencyList.has(vertex)) {
    this.adjacencyList.set(vertex, []);
  }
}
```

인자로 받은 `vertex`가 존재하지 않는다면, 새 정점은 빈 배열(`[]`)과 함께 Map에 저장된다.

### 3. 간선 추가
```js
  addEdge(vertex1, vertex2) {
	// 정점이 존재하지 않으면 추가
    if (!this.adjacencyList.has(vertex1)) this.addVertex(vertex1);
    if (!this.adjacencyList.has(vertex2)) this.addVertex(vertex2);
	
	// vertex1 -> vertex2 간선 추가
    this.adjacencyList.get(vertex1).push(vertex2);
	
	// 무방향 그래프인 경우 양방향으로 간선 추가
    if (!this.isDirected) this.adjacencyList.get(vertex2).push(vertex1);
    
  }
```

먼저 `vertex1`과 `vertex2`가 그래프에 존재하는지 확인하고, 없으면 추가한다.
`vertex1`에서 `vertex2`로 가는 간선을 추가하고, 무방향인 경우 반대로도 추가해주면된다.

`this.adjacencyList.get(vertex1)`은 `vertex1`이라는 key값의 value를 가져오는것인데, 해당 vertex1의 value값은 인접 리스트인 배열이다. 해당 배열에 push로 vertex2를 넣기에, 그래프에 간선을 추가하는 동작인 것이다. 
 
### 4. 간선 제거 

```js
removeEdge(vertex1, vertex2) {
  if (this.adjacencyList.has(vertex1) && this.adjacencyList.has(vertex2)) {
    // vertex1 -> vertex2 간선 제거
    this.adjacencyList.set(
      vertex1, 
      this.adjacencyList.get(vertex1).filter(v => v !== vertex2)
    );

    // 무방향 그래프인 경우 양방향 간선 제거
    if (!this.isDirected) {
      this.adjacencyList.set(
        vertex2, 
        this.adjacencyList.get(vertex2).filter(v => v !== vertex1)
      );
    }
  }
}
```

두 정점이 모두 존재하는 경우에만 간선제거를 실행하기에 추후 정점제거에서 정점자체 삭제 보다 간선제거를 먼저 한다.
`filter(v => v !== vertex2)`는 vertex2를 제외한 모든 정점을 유지하는 새 배열을 만든다.
이를 통해 `vertex1`의 인접 리스트에서 `vertex2`를 필터링하여 제거할 수 있다. 

### 5. 정점 제거

```js
removeVertex(vertex) {
  if (!this.adjacencyList.has(vertex)) return;

  // 해당 정점과 연결된 다른 정점들의 간선 제거
  for (const adjacentVertex of this.adjacencyList.get(vertex)) {
    this.removeEdge(adjacentVertex, vertex);
  }

  // 정점 제거
  this.adjacencyList.delete(vertex);
}
```

인자로 받은 정점이 존재하지 않으면 아무 작업도 수행하지 않고 반환한다.
정점을 제거하기 전에, `vertex`의 인접 리스트에 있는 모든 정점에 대해 `removeEdge`를 호하여 해당 정점과 연결된 모든 간선을 제거한다. 마지막으로 Map에서 해당 정점 자체를 삭제한다.

### 6. 그래프 출력
```js
printGraph() {
  for (const [vertex, edges] of this.adjacencyList.entries()) {
    console.log(`${vertex} -> ${edges.join(', ')}`);
  }
}
```

Map의 `entries()` 메서드를 사용하여 각 정점과 그 인접 리스트를 순회한다. 
`entries()` 메서드는 Map에 저장된 모든 Key-value를 [key, value] 형태의 배열로 포함하는 반복 가능한(iterable) 객체를 반환해주는 메서드이다. 