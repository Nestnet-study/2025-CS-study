# Linked List

Linked List는 각 노드가 데이터와 다음 노드를 가리키는 포인터로 구성된 자료 구조이다. 다음 세 종류로 나뉜다.

1. Singly Linked List
   - 각 노드는 데이터와 다음 노드를 가리키는 포인터만 가진다.
   - 한 방향으로만 탐색이 가능하다.
2. Doubly Linked List
   - 각 노드는 데이터, 이전 노드 포인터, 다음 노드 포인터를 가진다.
   - 양방향 탐색이 가능하다.
3. Circular Linked List
   - 마지막 노드가 첫 번째 노드를 가리켜 원형 구조를 형성한다.

### 장점

- 크기가 동적으로 변할 수 있다.
- 삽입 및 삭제 연산이 효율적이다. (O(1))

### 단점

- 임의 접근에 Linear 시간이 든다.
- 추가적인 메모리가 포인터 저장에 필요하다.
- cache locality가 배열보다 좋지 않다.

---

## 자바에서의 Linked List

자바에서 LinkedList는 구현체이다. 예를 들어 LinkedList를 구현체로 사용하고, 인터페이스는 Queue를 사용한다면 큐를 Linked List로 구현한 것이 된다.

```java
Queue<String> queue = new LinkedList<>();
queue.offer("Task 1");
queue.offer("Task 2");
String nextTask = queue.poll();  // "Task 1" 반환 및 제거
```

LinkedList 클래스가 구현하는 주요 인터페이스를 정리해보자.

1. List 인터페이스

   순서가 있는 컬렉션으로, 인덱스 기반 접근을 제공한다. 데이터를 순서대로 저장하면서 인덱스로 접근이 필요할 때 사용한다.

   ```java
   List<String> list = new LinkedList<>();
   list.add("Apple");
   list.add(1, "Banana");  // 인덱스 1에 삽입
   String item = list.get(0);  // "Apple" 반환
   ```

2. Queue 인터페이스
3. Deque 인터페이스

   양방향 삽입/삭제가 빈번히 일어나는 경우 사용한다.

   ```java
   Deque<String> deque = new LinkedList<>();
   deque.offerFirst("First");
   deque.offerLast("Last");
   String first = deque.pollFirst();  // "First" 반환 및 제거
   String last = deque.pollLast();    // "Last" 반환 및 제거

   // 스택으로 사용
   deque.push("A");  // 앞에 추가
   deque.push("B");  // 앞에 추가
   String top = deque.pop();  // "B" 반환 및 제거 (LIFO)
   ```

---

## 예상 질문

1. List 인터페이스를 구현할 때 LinkedList와 ArrayList 중 어떤 상황에서 어떤 구현체가 더 유리한지 메모리 단편화와 캐시 locality 관점에서 서술하시오.
