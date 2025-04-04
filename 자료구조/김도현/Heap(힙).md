
# Heap
---

**힙**은 **완전이진트리 구조를 기반으로 하는 자료구조**로, 특정한 순서 속성을 만족한다. 주로 **최댓값이나 최솟값을 빠르게 찾아야 하는 경우**에 사용된다.

힙에는 **최대힙(MaxHeap)**과 **최소힙(MinHeap)** 2가지 방식이 존재한다.
최댓값 최솟값을 찾는 자료구조로써 우선순위가 높은 데이터를 빠르게 찾아야하는 경우에 효율적인 자료구조이다.

힙 자료구조는 우선순위 큐 구현, 다익스트라 알고리즘 등에 응용하여 사용되기도 한다.



#  Heap 작동 방식
---


힙의 주요 연산은 **push(삽입 연산), pop(최댓값/ 최솟값 추출), peek(최댓값/ 최솟값 확인), build Heap(힙 생성)** 총 4개로 구분지을 수 있다.

javascript로 기본적인 클래스를 생성하면 다음과 같다.
```js
class MinHeap {
  constructor() {
    this.heap = [];
  }
  size() {
    return this.heap.length;
  }
  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }
  getParentIndex(index) {
    return Math.floor((index - 1) / 2);
  }
  getLeftChild(index) {
    return 2 * index + 1;
  }
  getRightChild(index) {
    return 2 * index + 2;
  }
}
```

생성자를 통해 기본적으로 배열을 생성한다. size()는 힙의 크기를 반환한다.
swap은 힙 동작과정 중 배열에서 위치를 변경하는 메서드이다. 나머지 get~() 함수들은 완전 이진 트리에서 부모, 자식의 인덱스를 반환하는 메서드이다. 

마지막 레벨 노드들은 왼쪽부터 채워지기 때문에, 배열로 구현했을 때에 인덱스의 관계는 다음과 같다.
- 부모 노드: `(i-1) / 2` (정수 나눗셈)
- 왼쪽 자식 노드: `2*i + 1`
- 오른쪽 자식 노드: `2*i + 2`



### 1. push(삽입 연산)

```js
  push(value) {
    this.heap.push(value);
    this.bubbleUp();
  }
```

삽입 연산은  트리의 마지막 위치에 새 요소를 추가한 후 부모 노드와 비교하여 힙속성을 만족할 때까지 위로 이동한다. 예시를 보면서 설명하도록 하겠다. 

최소힙인 heap 구조인 배열 `[4, 8, 5, 10, 9]`이 있다고 하자.

```text
	  4
     / \
    8   5
   / \
  10  9
```

2를 push 하게 되면, 리프노드에 2가 추가하게 된다. 

```text
      4
     / \
    8   5
   / \  /
  10 9 2  ← 여기에 추가
```

부모(5)와 비교했을 때  2 < 5이므로 교환하게 된다.

```text
	  4
     / \
    8   2
   / \  /
  10 9 5
```

새 위치에서 부모(4)와 비교했을 때  2 < 4이므로 교환하게 된다.

```text
	  2
     / \
    8   4
   / \  /
  10 9 5
```

그러면 결국 최소힙에 만족하는 가장 작은 값의 2가 루트노드로 이동하는 것을 볼 수 있다.


이때 위로 이동하는 과정을 `heapify-up` 또는` bubble-up`이라고 한다.

```js 
  bubbleUp() {
    let index = this.size() - 1; //리프 노드
    let parentIndex = this.getParentIndex(index); //부모 노드

    while (index > 0 && this.heap[parentIndex] > this.heap[index]) {
      this.swap(index, parentIndex);
      index = parentIndex;
      parentIndex = this.getParentIndex(index);
    }
  }
```

bubble-up과정을 js로 구현 해보면, 리프 노드에서 `this.heap[parentIndex] > this.heap[index]` 와 같이 부모 노드의 값과 비교하여 부모보다 힙의 속성에 만족하면 `swap`을 하여 `index>0`인 조건을 통해 루트노드까지 반복하는 것을 알 수 있다. 


노드 수가`n`인 경우, 완전이진트리이기 때문에 트리의 높이는 약 `log2n`이다. 방금 삽입한 리프노드가 루트노드까지 이동하게 되는 최악의 경우 시간복잡도는 **O(log n)**인 것을 알 수 있다.




### 2. pop(최댓값/ 최솟값 추출)


```js
  pop() {
    if (this.size() === 0) return null;

    const min = this.heap[0];
    const last = this.heap.pop();

    if (this.size() !== 0) {
      this.heap[0] = last;
      this.bubbleDown();
    }

    return min;
  }
```

최댓값/ 최솟값을 추출하는 pop연산은 결국엔 heap[0]인 루트 노드를 노드 구조에서 추출하는 것이다. 

추출시에 사이즈가 0인 경우에는 `if (this.size() === 0) return null;`을 반환하고,
```js
const min = this.heap[0];
```
루트노드를 반환하게 된다.

하지만 루트노드를 추출한 이상 루트노드가 비어있는 트리 구조가 되기 때문에, 가장 마지막 노드를 최상단으로 올려서 다시트리를 구성하는 `heapify-down`연산을 한다.  `heapify-down`는 `bubble-down`이라고 불리기도한다. 

아래와 같은 트리구조가 있다고 하자. 
```text
	  2
     / \
    8   4
   / \  /
  10 9 5
```

pop연산을 하게 되면 루트(2)를 제거하고, 마지막 노드(5)를 루트로 이동하게 된다. 
```text
	  5
     / \
    8   4
   / \
  10 9
```

이후 정상적인 힙 구조를 만족하기 위해 Heapify-down연산을 시행한다. 
자식들(8, 4) 중 4가 작으므로 루트노드(5)와 교환한다.

```text
      4
     / \
    8   5
   / \
  10 9
```

이후 정상적인 힙 속성을 만족하는 트리구조로 변경된 것을 볼 수 있다.

heapify-dwon을 js코드로 작성하면 다음과 같다.

```js
bubbleDown() {
    let index = 0;
    let length = this.size();
    let element = this.heap[0];

    while (1) {
      const leftChildIndex = this.getLeftChild(index);
      const rightChildIndex = this.getRightChild(index);
      let leftChild, rightChild;
      let swapIndex = null;

      //현재 요소보다 왼쪽 자식이 더 작은지
      if (leftChildIndex < length) {
        leftChild = this.heap[leftChildIndex];
        if (leftChild < element) {
          swapIndex = leftChildIndex;
        }
      }

      //현재 요소보다 오른쪽 자식이 더 작은지
      if (rightChildIndex < length) {
        rightChild = this.heap[rightChildIndex];
        if (
          (swapIndex === null && rightChild < element) ||
          (swapIndex !== null && rightChild < leftChild)
        )
          swapIndex = rightChildIndex;
      }

      if (swapIndex === null) break;

      this.swap(index, swapIndex);
      index = swapIndex;
    }
  }
```

노드 수가`n`인 경우, 완전이진트리이기 때문에 트리의 높이는 약 `log2n`이다. 
이 과정도 push연산과 마찬가지로 최악의 경우 시간복잡도는 **O(log n)**인 것을 알 수 있다.


### 3.peek(최댓값/ 최솟값 확인)

**peek** 연산은 간단히 **루트 노드 값을 반환하는 연산**이다.
배열로 heap을 구현한 경우 결국 `heap[0]`이 루트노드 값을 반환한다.

항상 루트 노드가 최댓값/최솟값이므로 상수시간이 걸려 시간복잡도는 **O(1)**이다.

### 4. build Heap(힙 생성)


**힙 생성(Build Heap)** 은 **일반 배열을 힙 속성을 만족하는 자료구조로 변환하는 것**이다. 
이 과정에서는 배열을 완전 이진 트리로 간주하고, 마지막 비단말 노드부터 루트까지 역순으로 각 노드에 대해` heapify-down` 연산을 수행한다.

시간복잡도 분석에서는 각 노드에 대한 `heapify-down` 연산이 `O(log n)`이고 총 노드 수는` n`이므로, 직관적으로는 `O(n log n)`으로 보인다.

하지만 모든 노드가 동일한 비용의 `heapify-down` 연산을 수행하지 않기 때문에 실제 시간복잡도는 **O(n)**이다.






# javascript 힙 구현 전체 코드

```js
class MinHeap {
  constructor() {
    this.heap = [];
  }
  size() {
    return this.heap.length;
  }
  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }
  getParentIndex(index) {
    return Math.floor((index - 1) / 2);
  }
  getLeftChild(index) {
    return 2 * index + 1;
  }
  getRightChild(index) {
    return 2 * index + 2;
  }
  push(value) {
    this.heap.push(value);
    this.bubbleUp();
  }
  pop() {
    if (this.size() === 0) return null;

    const min = this.heap[0];
    const last = this.heap.pop();

    if (this.size() !== 0) {
      this.heap[0] = last;
      this.bubbleDown();
    }

    return min;
  }

  bubbleUp() {
    let index = this.size() - 1; //리프 노드
    let parentIndex = this.getParentIndex(index); //부모 노드

    while (index > 0 && this.heap[parentIndex] > this.heap[index]) {
      this.swap(index, parentIndex);
      index = parentIndex;
      parentIndex = this.getParentIndex(index);
    }
  }
  bubbleDown() {
    let index = 0;
    let length = this.size();
    let element = this.heap[0];

    while (1) {
      const leftChildIndex = this.getLeftChild(index);
      const rightChildIndex = this.getRightChild(index);
      let leftChild, rightChild;
      let swapIndex = null;

      //현재 요소보다 왼쪽 자식이 더 작은지
      if (leftChildIndex < length) {
        leftChild = this.heap[leftChildIndex];
        if (leftChild < element) {
          swapIndex = leftChildIndex;
        }
      }

      //현재 요소보다 오른쪽 자식이 더 작은지
      if (rightChildIndex < length) {
        rightChild = this.heap[rightChildIndex];
        if (
          (swapIndex === null && rightChild < element) ||
          (swapIndex !== null && rightChild < leftChild)
        )
          swapIndex = rightChildIndex;
      }

      if (swapIndex === null) break;

      this.swap(index, swapIndex);
      index = swapIndex;
    }
  }
}
const minHeap = new MinHeap();
minHeap.push(5);
minHeap.push(3);
minHeap.push(8);
minHeap.push(1);
console.log('Heap after pushing elements:', minHeap.heap); // [1, 3, 8, 5]

console.log('Popped:', minHeap.pop()); // 1
console.log('Heap after pop:', minHeap.heap); // [3, 5, 8]

console.log('Popped:', minHeap.pop()); // 3
console.log('Heap after pop:', minHeap.heap); // [5, 8]

```