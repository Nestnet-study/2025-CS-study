## Map(맵)

맵은 **키(key)와 값(value)의 쌍**으로 데이터를 저장하는 추상 자료구조이다.

맵의 특징으로는 **'각 키는 맵 내에서 유일하게 존재.'** , **'각 키는 정확히 하나의 값과 연관'** 와 같이 **키의 유일성**이다. 하나의 맵 안에서 동일한 키를 가진 두 개의 항목은 존재할 수 없다. 만약 이미 존재하는 키로 새로운 값을 저장하면, 기존 값은 새로운 값으로 덮어쓰기 된다. 이러한 특성 때문에 맵은 고유 식별자를 기반으로 정보를 구성해야 하는 상황에 이상적이다.

맵의 기본 연산으로는 키-값 쌍의 삽입, 키를 이용한 값 검색, 키-값 쌍의 삭제, 특정 키의 존재 여부 확인 등이 있다. 맵 자료구조는 이러한 연산들을 효율적으로 수행할 수 있도록 설계되어 있다. 구현 방식에 따라 성능 특성이 달라지지만, 일반적으로 빠른 접근 시간을 제공한다.


맵의 구현방식으로 크게 2가지가 존재하는데, 해시 테이블을 사용한 해시맵 방법과 트리구조를 사용한 트리맵 방법이 존재한다.

해시테이블의 경우 평균적으로 O(1)로 연산을 수행할 수 있다.
간단히 예시로 우리가 Map자료구조 안에 'a'라는 key값의 value를 찾는다고 하자.
`a` 값을 해시 함수에 넣게되면, 해시함수는 `a` 를 어떤 숫자 값(해시코드)로 변환하고,
`index = hash('a') % array_size` 와 같이 배열의 특정 인덱스 위치로 직접이동하여 바로 해당 값을 찾을 수 있게 된다.

이처럼 해시맵 방식인 경우 삽입, 삭제, 검색, 존재여부 확인 **모두 O(1)의 시간복잡도**를 가진다. Map(맵)은 프로그래밍에서 개체 간의 관계를 표현하거나, 특정 키에 대한 값을 빠르게 찾아야 하는 상황에서 맵은 매우 유용한 도구이다.





## javasciprt Map(맵) 활용


### 기본 사용
```js
// Map 생성
const myMap = new Map();

// 값 추가
myMap.set('name', 'John');
myMap.set('age', 30);
myMap.set('city', 'Seoul');

// 값 조회
console.log(myMap.get('name')); // 'John'

// 값 존재 확인
console.log(myMap.has('age')); // true

// 값 삭제
myMap.delete('city');

// Map 크기
console.log(myMap.size); // 2
```

위 코드는 자바스크립트에서 Map 객체를 생성하고 기본적인 메서드들을 활용하는 예시이다. `new Map()`으로 빈 맵을 생성한 후, `set()` 메서드를 사용하여 키-값 쌍을 추가하고 있다. `get()` 메서드는 지정된 키에 해당하는 값을 반환하며, `has()` 메서드는 특정 키가 맵에 존재하는지 확인한다. `delete()` 메서드는 지정된 키-값 쌍을 제거하며, `size` 속성은 맵에 저장된 항목의 수를 반환한다. 
이러한 기본 연산들은 **모두 O(1)의 시간 복잡도**를 가진다.

### 배열 데이터 처리하기

```js
// 배열의 요소 빈도수 계산
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
const fruitCount = new Map();

for (const fruit of fruits) {
  // 이미 Map에 과일이 있으면 개수 증가, 없으면 1로 설정
  fruitCount.set(fruit, (fruitCount.get(fruit) || 0) + 1);
}

console.log(fruitCount.get('apple')); // 3
console.log(fruitCount.get('orange')); // 1
```

이 코드는 배열에서 각 요소의 출현 빈도를 계산하는 일반적인 Map 활용 사례이다. 배열을 순회하면서 각 과일을 키로, 해당 과일의 등장 횟수를 값으로 저장한다.

만약 과일이 이미 Map에 있다면 기존 값에 1을 더하고, 없다면 1로 설정한다. `fruitCount.get(fruit) || 0`은 해당 과일이 Map에 없는 경우 `undefined`가 반환되므로, 이를 0으로 처리하여 초기 카운트를 설정한다. 이러한 방식으로 Map은 요소 빈도 계산, 데이터 그룹화 등의 작업에 효율적으로 활용될 수 있다.

### 배열 요소 필터링/변환

```js
const numbers = [1, 2, 3, 4, 5, 1, 2, 3];

// 배열 요소 변환하기
const doubledMap = new Map();
numbers.forEach(num => {
  doubledMap.set(num, num * 2);
});

// 원래 숫자를 키로, 변환된 값을 가져올 수 있음
console.log(doubledMap.get(3)); // 6

// 모든 변환된 값을 배열로 가져오기
const transformedValues = Array.from(doubledMap.values());
console.log(transformedValues); // [2, 4, 6, 8, 10]
```

이 코드는 배열의 각 고유 요소를 키로 사용하고, 그 값을 변환한 결과를 저장하는 예시이다. 각 숫자를 키로 하고 그 두 배 값을 값으로 저장하여 변환 맵을 생성한다. 중복 숫자가 있더라도 **Map은 키의 유일성을 보장**하므로 최종적으로는 **고유한 숫자에 대한 변환 결과만 저장**된다.