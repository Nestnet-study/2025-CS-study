# 스택

스택은 입력과 출력이 한 방향으로 제한되어 가장 나중에 들어온 데이터가 가장 먼저 나오는 LIFO 구조의 자료구조이다.

## 자바에서의 스택

### Stack Class

Stack 클래스는 레거시로 간주되며 권장되지 않는다. 그 이유는 **Vector를 상속받는 방식**으로 구현되어있기 때문이다.

잠시 벡터부터 알아보고 가자. 자바에서의 벡터는 동적 배열 구현체로, **모든 메소드가 synchronized로 선언되어 thread-safe**하다. 선언 방식은 다음과 같다.

```java
import java.util.Vector;

public class VectorExample {
    public static void main(String[] args) {
        // 기본 생성자
        Vector<String> vector1 = new Vector<>();

        // 초기 용량 지정
        Vector<String> vector2 = new Vector<>(10);

        // 초기 용량과 증분 용량 지정
        Vector<String> vector3 = new Vector<>(10, 5);

        // 다른 컬렉션으로부터 생성
        Vector<String> vector4 = new Vector<>(vector1);
    }
}
```

Vector는 불필요한 동기화로 인해 **단일 스레드 환경에서도 오버헤드가 발생**한다는 문제점이 존재한다. 또, Java Collections Framework 이전에 설계되어 API 설계가 다르다. 개별 클래스로 존재하고 일관된 인터페이스가 없기에 각 데이터 컬렉션 클래스가 독립적으로 설계되어 일관성이 없는 것이다.

- Java Collections Framework는 자바에서 데이터 컬렉션을 저장하고 조작하기 위해 통합된 아키텍쳐이다. 인터페이스, 구현 클래스, 알고리즘이 존재한다.
  - 인터페이스(Interfaces)
    - Collection: 모든 컬렉션의 최상위 인터페이스
    - List: 순서가 있는 컬렉션 (중복 허용)
    - Set: 중복을 허용하지 않는 컬렉션
    - Queue: FIFO(First-In-First-Out) 방식의 컬렉션
    - Deque: 양쪽 끝에서 요소 추가/제거가 가능한 컬렉션
    - Map: 키-값 쌍으로 이루어진 매핑을 저장하는 객체
  - 구현 클래스(Implementations)
    - ArrayList, LinkedList: List 인터페이스 구현
    - HashSet, TreeSet: Set 인터페이스 구현
    - PriorityQueue: Queue 인터페이스 구현
    - ArrayDeque: Deque 인터페이스 구현
    - HashMap, TreeMap: Map 인터페이스 구현

다시 돌아와서 Stack은 Vector를 상속받아 구현되었기에 레거시로 간주된다. 또한, Stack은 LIFO 구조이지만 Vector를 상속받기에 `get()`, `remove()` 등의 메소드로 스택의 중간 요소에 접근할 수 있어 스택의 추상화를 깨뜨린다.

Stack 클래스는 다음과 같이 구현되어 있다.

```java
public class Stack<E> extends Vector<E> {
    // 생성자
    public Stack() {
    }

    // 요소 추가
    public E push(E item) {
        addElement(item);
        return item;
    }

    // 요소 제거 및 반환
    public synchronized E pop() {
        E obj;
        int len = size();
        obj = peek();
        removeElementAt(len - 1);
        return obj;
    }

    // 맨 위 요소 반환 (제거 없음)
    public synchronized E peek() {
        int len = size();
        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }

    // 스택이 비어있는지 확인
    public boolean empty() {
        return size() == 0;
    }

    // 요소의 위치 검색
    public synchronized int search(Object o) {
        int i = lastIndexOf(o);
        if (i >= 0) {
            return size() - i;
        }
        return -1;
    }
}
```

Vector 클래스를 상속받기에 Vector 메소드를 활용하고 synchronized로 선언되었다는 부분만 알면 된다.

### ArrayDeque Class

자바에서 권장되는 스택 구현 방법은 ArrayDeque class를 사용하는 것이다. ArrayDeque class는 Deque 인터페이스의 구현이며 내부적으로 circular array를 사용하여 더 효율적인 메모리 사용과 빠른 연산을 제공한다.

- circular array는 일반적인 선형 배열을 원형처럼 사용하는 데이터 구조이다.

또, ArrayDeque는 동기화되지 않아 단일 스레드에서 더 빠르다. 필요하다면 다음과 같이 동기화할 수 있다.

```java
Deque<Integer> stack = Collections.synchronizedDeque(new ArrayDeque<>());
```

혹은 좀 더 안전하게 ConcurrentLinkedDeque를 사용할 수 있다.

```java
Deque<Integer> stack = new ConcurrentLinkedDeque<>();
```

ArrayDeque는 null을 허용하지 않아 NullPointerException을 방지한다. ArrayDeque의 기본 사용법은 다음과 같다.

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class ModernStackExample {
    public static void main(String[] args) {
        // Deque 인터페이스를 사용하여 스택 생성
        Deque<Integer> stack = new ArrayDeque<>();

        // 요소 추가 (push)
        stack.push(10);
        stack.push(20);
        stack.push(30);

        // 스택 내용 확인
        System.out.println("스택 내용: " + stack);  // 출력: [30, 20, 10]

        // 맨 위 요소 확인 (peek)
        System.out.println("맨 위 요소: " + stack.peek());  // 출력: 30

        // 요소 제거 (pop)
        System.out.println("Pop 결과: " + stack.pop());  // 출력: 30

        // pop 후 스택 내용
        System.out.println("Pop 후 스택: " + stack);  // 출력: [20, 10]

        // 스택 크기 확인
        System.out.println("스택 크기: " + stack.size());  // 출력: 2

        // 스택이 비어있는지 확인
        System.out.println("스택이 비어있는가? " + stack.isEmpty());  // 출력: false
    }
}
```

ArrayDeque는 Deque 인터페이스를 상속받는다. 그런데 Deque 인터페이스는 양쪽 끝에서 요소를 추가하고 제거할 수 있는 인터페이스이기에 스택과 큐를 모두 구현하는 데 사용될 수 있다. 스택으로 사용하려 해도 큐 관련 FIFO 메소드를 사용할 수 있는 것이다. 이 문제는 Deque를 래핑하는 별도의 스택 클래스를 만들어 스택 관련 메소드만 노출시키는 방식으로 해결할 수 있다.

```java
public class Stack<E> {
    private final Deque<E> deque = new ArrayDeque<>();

    public void push(E item) {
        deque.push(item);
    }

    public E pop() {
        return deque.pop();
    }

    public E peek() {
        return deque.peek();
    }

    public boolean isEmpty() {
        return deque.isEmpty();
    }

    public int size() {
        return deque.size();
    }
}
```

참고로 ArrayDeque는 내부적으로 Object 배열을 사용하여 요소를 저장한다. 이 배열은 처음에 지정된 크기로 초기화되며 배열이 꽉 찬다면 다음과 같은 과정을 통해 배열을 확장한다.

1. 용량 확장 조건 확인
2. 새 배열 할당

   현재 배열 크기의 2배 크기로 새 배열을 생성한다.

3. 원소 복사 및 재배치

   이때 중요한 것은 기존 배열의 논리적 순서를 유지하며 복사한다는 것이다. 기존 배열에서 head부터 시작하는 원소들을 새 배열의 0 index부터 재배치한다.

4. 포인터 재설정

## Stack 단골 문제

1. 괄호 유효성 검사

   ```java
   예: "(())()" → 유효
       "(()" → 유효하지 않음
       "{[()]}" → 유효
       "{[(])}" → 유효하지 않음
   ```

2. Postfix 계산

   중위 표기식을 후위 표기식으로 변환하거나 계산하는 문제이다.

   ```java
   중위: 3 + 4 * 2 / (1 - 5)
   후위: 3 4 2 * 1 5 - / +
   ```

---

# 큐

큐는 가장 먼저 추가된 원소가 가장 먼저 제거되는 FIFO 구조의 자료구조이다.

## 자바에서의 Queue

### Queue 인터페이스

Queue 인터페이스는 Stack 클래스와 다르게 레거시가 아니다. 우선 Queue는 인터페이스로 LinkedList, ArrayDeque 등으로 구현할 수 있다.

```java
public interface Queue<E> extends Collection<E> {
    // 기본 메소드들
    boolean add(E e);      // 요소 추가 (실패 시 예외 발생)
    boolean offer(E e);    // 요소 추가 (실패 시 false 반환)
    E remove();            // 맨 앞 요소 제거하고 반환 (큐가 비어있으면 예외 발생)
    E poll();              // 맨 앞 요소 제거하고 반환 (큐가 비어있으면 null 반환)
    E element();           // 맨 앞 요소 반환 (제거하지 않음, 큐가 비어있으면 예외 발생)
    E peek();              // 맨 앞 요소 반환 (제거하지 않음, 큐가 비어있으면 null 반환)
}
```

### LinkedList 구현체

LinkedList는 doubly linked list를 기반한 구현체로 List, Deque 인터페이스도 함께 구현한다. 삽입/삭제 효율이 O(1)로 좋지만 각 요소마다 이전/다음 노드 참조를 위한 추가 메모리가 필요하다. 크기 변동이 심하고 양끝에서 빈번한 삽입/삭제가 필요한 경우 사용하면 된다.

```java
import java.util.LinkedList;
import java.util.Queue;

public class LinkedListExample {
    public static void main(String[] args) {
        // 큐로 사용
        Queue<String> queue = new LinkedList<>();

        // 요소 추가
        queue.offer("첫번째 고객");
        queue.offer("두번째 고객");
        queue.offer("세번째 고객");

        System.out.println("대기열: " + queue);  // [첫번째 고객, 두번째 고객, 세번째 고객]

        // 순차적으로 처리
        while (!queue.isEmpty()) {
            String customer = queue.poll();
            System.out.println("현재 처리 중: " + customer);
            System.out.println("남은 대기열: " + queue);
        }

    }
}
```

### ArrayDeque 구현체

ArrayDeque는 circular array 기반으로 구현되어 있고, Deque 인터페이스를 구현하여 큐, 스택 기능을 모두 사용할 수 있다. LinkedList보다 노드당 오버헤드가 적고 null 요소를 허용하지 않는다. 또한 연속된 메모리 위치를 사용하여 캐시 성능이 우수하다. 대부분의 경우 LinkedList 보다 성능이 좋다.

ArrayDeque은 내부적으로 배열을 사용하므로 특정 인덱스에 O(1)로 접근할 수 있다. 하지만 설계상의 의도로 특정 인덱스에 접근하는 메서드를 제공하지 않는다.

```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Queue;

public class ArrayDequeExample {
    public static void main(String[] args) {
        // 큐로 사용
        Queue<Integer> queue = new ArrayDeque<>();

        // 요소 추가
        queue.offer(100);
        queue.offer(200);
        queue.offer(300);

        System.out.println("처리 대기 항목: " + queue);  // [100, 200, 300]

        // 순차 처리
        while (!queue.isEmpty()) {
            int current = queue.poll();
            System.out.println("처리 중: " + current);
        }


        // 양방향 큐(Deque)로 사용
        Deque<Character> deque = new ArrayDeque<>();

        // 양쪽에서 추가
        deque.addFirst('A');
        deque.addLast('B');
        deque.addFirst('C');
        deque.addLast('D');

        System.out.println("Deque 내용: " + deque);  // [C, A, B, D]

        // 양쪽에서 제거
        char first = deque.removeFirst();
        char last = deque.removeLast();

        System.out.println("제거된 첫 요소: " + first);  // C
        System.out.println("제거된 마지막 요소: " + last);  // D
        System.out.println("Deque 내용: " + deque);  // [A, B]
    }
 }
```

---

# 예상 문제

1. 메모리 관리에서 스택(Stack)과 힙(Heap)의 차이점을 설명하고, 재귀 함수를 반복문으로 변환할 때 스택을 어떻게 활용하는지 설명하시오.
