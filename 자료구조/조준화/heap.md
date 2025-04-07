# 힙

힙은 완전 이진 트리 기반의 자료구조이다. 완전 이진 트리이기에 배열로 힙을 표현할 수 있고 삽입, 삭제의 시간 복잡도가 O(log n)으로 보장된다는 장점이 있다.

Max Heap은 부모 노드의 값이 자식 노드의 값보다 크거나 같고 Min Heap은 부모 노드의 값이 자식 노드의 값보다 작거나 같다.

---

## 자바에서의 힙 - Priority Queue

PriorityQueue는 우선순위 힙(기본적으로 최소 힙)을 기반으로 구현되어 원소들이 우선순위에 따라 정렬된다. 완전 이진 트리 구조를 배열로 표현하여 부모 노드는 항상 자식보다 우선순위가 높게 구현되어 있다.

기본 사용법은 다음과 같다.

```java
// 기본 선언 - 초기 용량 11, 자연 순서(오름차순) 정렬
PriorityQueue<Integer> pq = new PriorityQueue<>();

// 초기 용량 지정
PriorityQueue<Integer> pq2 = new PriorityQueue<>(20);
```

내부적으로 완전 이진 트리를 배열로 표현하여 최소 힙을 생성한다. 디폴트값으로 선언하게 되면 큐에 추가되는 객체의 자료형의 자연 순서를 사용하도록 설정된다. 여기서 자연 순서란 해당 클래스가 Comparable 인터페이스를 구현할 때 정의한 compareTo 메소드의 결과에 따른 순서를 의미한다. 예를 들어 기본 타입의 래퍼 클래스 `Integer`, `Long` 등은 오름차순이고 `String`은 사전식 순서로 정렬된다.

우선순위 큐의 정렬 기준을 설정하는 방법은 Comparable, reverseOrder, 람다식이 존재한다. 하나씩 보자.

### Comparable

사용자 정의 클래스에서 Comparable을 구현하는 방법은 다음과 같다.

```java
public class Student implements Comparable<Student> {
    private String name;
    private int grade;

    @Override
    public int compareTo(Student other) {
        // 성적 기준 오름차순 정렬
        return Integer.compare(this.grade, other.grade);
    }
}
```

- 여기서 `compare` 함수는 x가 y보다 작으면 음수를 반환하고 x가 y보다 크면 양수를 반환한다.
- 반환 값이 음수이면 첫 번째 객체가 앞에 와야 하고, 양수이면 첫 번째 객체가 뒤에 와야하는 것을 의미한다.

내림차순 comparable은 다음과 같이 구현한다.

```java
public class Student implements Comparable<Student> {
    private String name;
    private int grade;

    // 생성자, getter, setter 등

    @Override
    public int compareTo(Student other) {
        // 성적 기준 내림차순 정렬 (높은 점수가 먼저)
        return Integer.compare(other.grade, this.grade);
    }
}
```

- 코드를 해석해보면, other.grade가 더 작으면 더 앞에 와야 한다는 뜻이다.

### reversoeOrder

자연 순서의 반대로 정렬하는 방법이다.

```java
// 내림차순 정렬 (가장 큰 수가 먼저 나옴)
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());

// String도 마찬가지로 역순 정렬 가능
PriorityQueue<String> reverseStringQueue = new PriorityQueue<>(Comparator.reverseOrder());
```

### 람다식 또는 Comparator 사용

특정 필드나 조건에 따라 정렬하는 방법이다.

```java
// 람다식으로 나이 기준 정렬
PriorityQueue<Person> ageQueue = new PriorityQueue<>(
    (p1, p2) -> Integer.compare(p1.getAge(), p2.getAge())
);

// 학생을 먼저 이름순으로, 이름이 같으면 성적 내림차순으로 정렬
PriorityQueue<Student> pq = new PriorityQueue<>((s1, s2) -> {
    // 이름 비교
    int nameCompare = s1.getName().compareTo(s2.getName());

    // 이름이 다르면 이름 순으로 정렬
    if (nameCompare != 0) {
        return nameCompare;
    }

    // 이름이 같으면 성적 내림차순으로 정렬
    return Integer.compare(s2.getGrade(), s1.getGrade());
});

// 짝수를 홀수보다 먼저, 같은 종류 내에서는 오름차순
PriorityQueue<Integer> customQueue = new PriorityQueue<>((a, b) -> {
    boolean aEven = a % 2 == 0;
    boolean bEven = b % 2 == 0;

    if (aEven && !bEven) {
        return -1;  // a만 짝수면 a가 먼저
    }
    if (!aEven && bEven) {
        return 1;   // b만 짝수면 b가 먼저
    }
    // 둘 다 짝수이거나 둘 다 홀수면 값으로 비교
    return Integer.compare(a, b);
});
```

람다식은 Comparable을 정의하는 것과 똑같다고 생각할 수 있는데, 중요한 차이점이 존재한다. Comparable 인터페이스 구현은 클래스 자체에 자연적인 순서를 정의하여 한 클래스는 하나의 기본 정렬 방식만 가질 수 있다. 하지만 람다식은 외부에서 정의하여 같은 객체에 대해 여러 정렬 방식을 동시에 사용할 수 있다.

Comparator 메소드 체이닝은 여러 정렬 기준을 연결하여 정렬 로직을 구성하는 방법인데, 가독성이 상당히 좋다.

```java
PriorityQueue<Employee> pq = new PriorityQueue<>(
    Comparator.comparing(Employee::getDepartment) // 부서 오름차순
              .thenComparing(Employee::getSalary, Comparator.reverseOrder()) // 급여 내림차순
              .thenComparing(Employee::getHireDate) // 입사일 오름차순
);
```

람다식과도 조합하여 사용할 수 있다.

```java
PriorityQueue<Product> pq = new PriorityQueue<>(
    Comparator.comparing(Product::getCategory)
              .thenComparing(p -> p.getPrice() * (1 - p.getDiscountRate())) // 할인가 기준
              .thenComparing(Product::getName)
);
```

null 값은 다음과 같이 처리한다.

```java
PriorityQueue<Task> pq = new PriorityQueue<>(
    Comparator.comparing(Task::getDueDate,
                         Comparator.nullsLast(Comparator.naturalOrder()))
              .thenComparing(Task::getPriority)
);
```

---

## 예상 문제

1. Java에서 Priority Queue의 우선 순위 기준을 설정할 때 람다식을 이용하는 방식과 Comparable 인터페이스를 정의하는 방식의 차이점은?
