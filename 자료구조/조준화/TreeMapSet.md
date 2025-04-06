## 트리

트리는 node와 node를 연결하는 edge로 구성되는 자료구조이다. 트리의 정의는 무방향이면서 사이클이 없는 연결 그래프이다. V개의 정점에 대해 V-1개의 간선을 가지는 사이클이 없는 연결 그래프라고 생각하면 된다. 다시 트리의 정의를 짚고 넘어가자.

1. 트리는 하나의 루트 노드를 갖는다.
2. 사이클이 존재하지 않는다.
3. 모든 노드는 딱 하나의 부모 노드를 갖는다.
4. 임의의 두 노드 사이에는 정확히 하나의 경로만 존재한다.

위의 조건을 모두 만족해야 트리이다. 트리는 다음과 같은 종류로 나뉜다.

- 이진 트리(Binary Tree): 각 노드가 최대 2개의 자식을 가질 수 있는 트리
- 이진 탐색 트리(Binary Search Tree): 왼쪽 자식 노드 < 부모 노드 < 오른쪽 자식 노드 규칙을 따르는 이진 트리
- 균형 트리(Balanced Tree): 왼쪽과 오른쪽 서브트리의 높이 차이가 일정 수준 이하인 트리
- 완전 이진 트리(Complete Binary Tree): 마지막 레벨을 제외한 모든 레벨이 완전히 채워져 있고, 마지막 레벨은 왼쪽부터 채워진 이진 트리
- 포화 이진 트리(Full Binary Tree): 모든 내부 노드가 2개의 자식을 가지고, 모든 리프 노드가 같은 레벨에 있는 이진 트리

---

## 자바에서의 트리

자바에서는 트리를 직접적으로 구현한 인터페이스나 클래스가 존재하지 않는다. 대신 트리 기반 자료구조인 TreeMap 등 인터페이스와 구현체가 존재한다.

---

## 자바에서의 맵

맵은 값을 키와 값의 쌍으로 저장하는 자료구조이다. 맵과 관련된 인터페이스와 구현체는 다양하다. 일반 Map 인터페이스를 구현하는 구현체는 HashMap, LinkedHashMap, TreeMap 등이 존재한다.

### Map 인터페이스 구현체

1. HashMap

   ```java
   Map<String, Integer> map = new HashMap<>();
   ```

   해시 테이블을 기반으로 구현한 Map이다. 키와 값에 null을 허용하고 순서를 보장하지 않는다. 즉, HashMap에 저장된 요소들이 특정 순서를 유지하지 않는다.

   검색, 삽입, 삭제에 O(1)을 보장하므로 굉장히 빠르다.

2. LinkedHashMap

   ```java
   Map<String, Integer> map = new LinkedHashMap<>();
   ```

   HashMap에 이중 연결 리스트를 결합하여 삽입 순서를 유지한다. 키와 값에 null을 허용한다.

   HashMap 보다는 약간 느리지만 검색, 삽입, 삭제 연산에 O(1)을 보장한다.

   또, LinkedHashMap은 삽입 순서 말고 접근 순서를 유지할 수도 있다. LRU 캐시를 구현할 때 유용하다.

   ```java
   Map<String, Integer> map = new LinkedHashMap<>(16, 0.75f, true);
   ```

3. TreeMap

   ```java
   Map<String, Integer> map = new TreeMap<>();
   ```

   레드-블랙 트리를 기반으로 구현한 Map이다. 키에 null을 허용하지 않고 키를 기준으로 정렬된 상태를 유지한다.

   삽입, 삭제, 검색에 O(log n) 시간이 든다.

### SrotedMap 인터페이스 구현체

SortedMap은 키가 정렬된 순서로 유지되는 맵을 정의하며 TreeMap 구현체를 사용한다.

```java
import java.util.SortedMap;
import java.util.TreeMap;

public class SortedMapExample {
    public static void main(String[] args) {
        // TreeMap을 SortedMap 인터페이스로 참조
        SortedMap<Integer, String> studentRanks = new TreeMap<>();

        // 데이터 추가 (키: 등수, 값: 학생 이름)
        studentRanks.put(3, "김철수");
        studentRanks.put(1, "이영희");

        System.out.println("첫 번째 키: " + studentRanks.firstKey());
        System.out.println("마지막 키: " + studentRanks.lastKey());
    }
}
```

SortedMap을 확장한 NavigableMap 인터페이스도 존재한다. `lowerEntry`, `florrEntry` 등 탐색 관련 메서드가 추가되어 주어진 키와 가장 가까운 키를 찾는 메서드를 제공한다.

TreeMap 구현체를 사용한다.

```java
import java.util.NavigableMap;
import java.util.TreeMap;
import java.util.Map;

public class NavigableMapExample {
    public static void main(String[] args) {
        // TreeMap을 NavigableMap 인터페이스로 참조
        NavigableMap<Double, String> temperatureWarnings = new TreeMap<>();

        // 데이터 추가 (키: 온도(°C), 값: 경고 메시지)
        temperatureWarnings.put(0.0, "결빙 위험");
        temperatureWarnings.put(10.0, "저온 주의");
        temperatureWarnings.put(25.0, "적정 온도");
        temperatureWarnings.put(30.0, "고온 주의");
        temperatureWarnings.put(35.0, "폭염 경보");

        // NavigableMap 탐색 메서드 사용
        double currentTemp = 27.5;

        // 현재 온도보다 낮은 최근접 온도
        Map.Entry<Double, String> lowerWarning = temperatureWarnings.lowerEntry(currentTemp);
        System.out.println("현재 온도보다 낮은 최근접 경고: " + lowerWarning);  // 25.0=적정 온도

        // 현재 온도보다 높은 최근접 온도
        Map.Entry<Double, String> higherWarning = temperatureWarnings.higherEntry(currentTemp);
        System.out.println("현재 온도보다 높은 최근접 경고: " + higherWarning);  // 30.0=고온 주의

        // 현재 온도 이하의 최근접 온도 (현재 온도가 정확히 있으면 그 값 반환)
        Map.Entry<Double, String> floorWarning = temperatureWarnings.floorEntry(currentTemp);
        System.out.println("현재 온도 이하 최근접 경고: " + floorWarning);  // 25.0=적정 온도

        // 역순으로 정렬된 맵 얻기
        NavigableMap<Double, String> descendingMap = temperatureWarnings.descendingMap();
        System.out.println("온도 내림차순: " + descendingMap.keySet());  // [35.0, 30.0, 25.0, 10.0, 0.0]
    }
}
```

---

## 자바에서의 Set

Set은 중복을 허용하지 않는 값들의 집합을 나타내는 자료 구조이다.

### Set 인터페이스 구현체

1. HashSet
2. LinkedHashSet
3. TreeSet

### SrotedSet 구현체

요소가 정렬된 순서로 유지되는 집합을 정의한다.

TreeSet 구현체를 사용한다.

```java
import java.util.SortedSet;
import java.util.TreeSet;

public class SortedSetExample {
    public static void main(String[] args) {
        // TreeSet을 SortedSet 인터페이스로 참조
        SortedSet<String> programmingLanguages = new TreeSet<>();

        // 데이터 추가 (자동으로 알파벳 순 정렬됨)
        programmingLanguages.add("Java");
        programmingLanguages.add("Python");

        // SortedSet의 메서드 사용
        System.out.println("모든 언어: " + programmingLanguages);

        System.out.println("첫 번째 언어: " + programmingLanguages.first());
        System.out.println("마지막 언어: " + programmingLanguages.last());

        // J로 시작하는 언어까지 (J 포함하지 않음)
        SortedSet<String> beforeJ = programmingLanguages.headSet("J");
        System.out.println("J 이전 언어: " + beforeJ);

        // Kotlin부터 끝까지 (Kotlin 포함)
        SortedSet<String> fromKotlin = programmingLanguages.tailSet("Kotlin");
        System.out.println("Kotlin부터: " + fromKotlin);

        // Java부터 Kotlin까지 (Java 포함, Kotlin 포함하지 않음)
        SortedSet<String> subset = programmingLanguages.subSet("Java", "Kotlin");
        System.out.println("Java~Kotlin 사이: " + subset);
}
```

NavigableSet은 SortedSet을 확장한 인터페이스이다. `lower`, `floor` 등 주어진 요소에 가장 가까운 요소를 찾는 메서드를 제공한다.TreeSet 구현체를 사용한다.

```java
import java.util.NavigableSet;
import java.util.TreeSet;

public class NavigableSetExample {
    public static void main(String[] args) {
        // TreeSet을 NavigableSet 인터페이스로 참조
        NavigableSet<Integer> scores = new TreeSet<>();

        // 데이터 추가 (자동으로 오름차순 정렬됨)
        scores.add(85);
        scores.add(95);

	     // NavigableSet 탐색 메서드 사용
        int currentScore = 82;

        // 현재 점수보다 낮은 최근접 점수
        Integer lowerScore = scores.lower(currentScore);
        System.out.println("현재 점수보다 낮은 최근접 점수: " + lowerScore);  // 75

        // 현재 점수보다 높은 최근접 점수
        Integer higherScore = scores.higher(currentScore);
        System.out.println("현재 점수보다 높은 최근접 점수: " + higherScore);  // 85

        // 현재 점수 이하의 최근접 점수
        Integer floorScore = scores.floor(currentScore);
        System.out.println("현재 점수 이하 최근접 점수: " + floorScore);  // 75

        // 현재 점수 이상의 최근접 점수
        Integer ceilingScore = scores.ceiling(currentScore);
        System.out.println("현재 점수 이상 최근접 점수: " + ceilingScore);  // 85

        // 가장 낮은 점수 조회 및 삭제
        Integer lowestScore = scores.pollFirst();
        System.out.println("제거된 가장 낮은 점수: " + lowestScore);  // 65
        System.out.println("남은 점수: " + scores);  // [75, 85, 90, 95]

        // 역순으로 정렬된 세트 얻기
        NavigableSet<Integer> descendingScores = scores.descendingSet();
        System.out.println("점수 내림차순: " + descendingScores);  // [95, 90, 85, 75]
    }
}
```

---

## 예상 질문

1. TreeMap과 LinkedHashMap을 모두 사용할 수 있을 때 어느 쪽을 선택해야 하는지에 대한 기준을 설명하시오.
