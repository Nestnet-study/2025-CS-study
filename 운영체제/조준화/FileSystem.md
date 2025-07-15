# 1. 파일 시스템

## 1. Basic Concepts

**file system**

- storage에 있는 파일을 abstraction으로 구현한 시스템이다.
- 디렉터리라는 logical 개념으로 파일을 관리한다.
- 파일시스템이 파일을 다루면서 프로세스나 사람들이나 머신들이 공유할 수 있어야 한다. 내가 만든 파일을 다른 기기가 볼 수 있어야 하고 protection도 필요하다. 다른 사람은 쓸 수 없고 read만 가능하다는 방식처럼 말이다.

storage는 블락 단위로 구성되어 있다. 3번 블락부터 몇 개의 섹터로 read, write 할 수 있고 블락 번호로 식별할 수 있다.

이제 파일이 뭔지, 파일 시스템이 뭔지 자세히 알아보자.

---

## 2. 파일 시스템 기초

파일은 다음의 요소들을 가져야 한다.

- 파일 이름
- 파일 속성(메타데이터)
  - 파일 사이즈
  - 소유자
  - 생성 시간, 최근 수정된 시간 등등
- 파일 contents (data)
  - 결국 파일은 0101로 되어있는데 그 데이터가 storage 어딘가에 저장되어있어야 한다.

파일 시스템은 매핑의 문제를 가지고 있다. 파일은 <filename, metadata, data>의 구조를 가지는데 그걸 디스크에 어떤 블락에 어떻게 매치할 것인가이다.

a.out, dog.jpg를 어떻게 디스크에 저장할 수 있을까? 만약 디스크의 블락 크기 하나가 512byte라면 파일을 512byte로 잘라서 저장한다. 파일의 metadata, file name 이런 식으로 블락 단위로 저장한다.

![](https://blog.kakaocdn.net/dn/b3Psna/btsHR2Pyyua/Eu3RuY9XbSsJbHEa9owRRK/img.png)

이렇게 저장되어 있고 a.out을 찾는다면 디스크에서 a.out을 찾아서 파일 이름과 metadata를 확인해서 파일을 읽을 수 있는지 등을 확인한  열어서 보여주게 된다.

---

## 3. Directories

**디렉터리의 역할**

- 사용자에게는, 파일을 조직화할 수 있는 구조적인 방법을 제공한다.
- 파일 시스템에게는, 편리한 naming 인터페이스를 제공한다.
  - logical file organization을 디스크의 피지컬한 파일 배치화 분리할 수 있도록 한다.

**Hierarchical directory system**

- 대부분의 파일 시스템은 멀티 레벨 디렉터리를 제공한다. 하나의 디렉토리 안에 여러 하위 디렉터리와 파일을 가질 수 있는 것이다.
- 대부분의 파일 시스템은 현재 디렉터리(작업 디렉토리)의 개념을 지원한다. 현재 작업 중인 디렉토리를 기준으로 파일을 참조하고 상대 경로를 지정할 수 있는 것이다.
- 상대 경로 : 현재 디렉토리를 기준으로 파일이나 디렉토리를 지정한다.
- 절대 경로 : 디렉토리 트리의 루트에서 시작하는 경로를 지정한다.

**디렉토리 내부**

- 디렉터리는 사실 특별한 metatdata를 포함하는 파일이다.
- (file name, file attributes) 구조의 list가 디렉터리이다.
  - Attribute는 Size, protection, creation time, access time, Location on dist 등 다양한 속성을 포함한다.

---

## 5. 마운팅

USB를 꽂으면 파일 시스템에 usb가 추가되는데 이게 마운팅이다.

운영체제가 어떤 storage가 붙었고 거기에는 어떤 파일 시스템이 있고 이런 걸 인식하는 과정이다.

- 윈도우에서는 C:\, D:\ 이런 식으로 드라이브 문자를 하나 잡는다.
- 유닉스에서는 마운트 포인트를 잡을 수 있다.

![](https://blog.kakaocdn.net/dn/OoV3K/btsHR1iVHX4/FQqjjDxq8eIHk8fWU0kEg1/img.png)

여기서 포인트 밑에 붙은 피라미드가 마운트 포인트이다. 빈 디렉터리를 계속 추가해 간다.

---

# 2. 파일 시스템 내부

## 1. 파일 시스템 개요

운영체제는 다음의 요소들을 중점적으로 파일 시스템을 관리한다.

- 파일과 디렉터리를 디스크 block에 저장하는 방식
- 디스크의 빈 공간 관리

디스크에는 OS가 사용하는 첫 번째 블락이 반드시 존재해야 한다.

하나의 디스크를 파티션으로 나누어 관리할 수 있고 보통 아래와 같은 구조를 가진다.

![](https://blog.kakaocdn.net/dn/EBKze/btsHSS6BCAW/LbB5kgcKphPbmjzhjPmFTK/img.png)

![](https://blog.kakaocdn.net/dn/ywiz1/btsHSBquvK7/qaN1TS4oMu8kvkEHjZa0S1/img.png)

하나의 운영체제가 여러 파일 시스템과 여러 스토리지를 지원해야 하기에 하나의 통합된 VFS가 중간에 존재한다. 그리고 그 밑에 device driver가 동작하는 구조이다.

![](https://blog.kakaocdn.net/dn/YGmfp/btsHSymYbXJ/nmPA7nNDUxNEAP8aaOjht1/img.png)

메모리 안에서는 PCB를 따로 가지는 것 처럼 프로세스마다 file descriptor table이 있다.

OS는 descripor table을 보면서 파일이 뭐가 열려있고 해당 파일은 누가 사용한다는 정보를 확인한다.

그들을 위해서 디렉터리 캐시, 버퍼 캐시가 있어서 캐싱할 수 있다.

---

## 2. VFS : virtual file system

- 말 그대로 가상의 파일 시스템이다.
- 모든 파일 시스템에게 하나의 포맷을 제공하기 위해서 제공되는 시스템이다.
- 내가 오픈하고 싶은건 a.txt이지 a.txt가 ext4에 저장되어 있는지 nfs에 저장되어 있는지 그런 건 알고 싶지 않다. 따라서 a.txt를 open system call을 호출하면 system call interface가 받고 그 명령을 VFS로 보내고 마운트 포인터를 보고 해당 파일의 위치를 확인한 뒤 디스크에서 읽게 된다.

---

## 3. Directory Implementation

a 디렉터리에 b, c, d 파일이 존재하면, 해당 디렉터리는 파일 이름과 파일 속성을 가지고 있다. 어떻게 metadata를 저장하는지는 주로 세 가지 방식이 존재한다.

**directory entry**

![](https://blog.kakaocdn.net/dn/bj1sAS/btsHSDBO6Ry/pI12OgZGWyH6b5BHHeTWK1/img.png)

- fixed length entries
  - 엔트리 수를 100, 파일 이름 200 이런 식으로 설계할 수 있다.

**separate data structure**

![](https://blog.kakaocdn.net/dn/dJQ8dn/btsHTfNV9mP/6OAlP44jtlWy4DJG1fIQJ0/img.png)

- linked list처럼 테이블을 늘리면서 만들 수 있다.
- search 하는 문제가 있을 수 있다.
- b tree나 해시 테이블을 사용하면 search 타임을 줄일 수 있다.
  - hash의 장단점을 그대로 가진다. 해시 펑션에 의존적이라던지 충돌이 나면 노드를 쭉 달아야 하는 등의 문제이다.

**hybird**

![](https://blog.kakaocdn.net/dn/elRo4m/btsHSurx7o4/GpugGG6B6PyaA8wWNvU3t1/img.png)

한 디렉터리에 많은 파일이 들어가야 한다면 hash table이 유리하다. 일반적으로 linear list를 많이 사용하긴 하는데 선택의 문제이다.

![](https://blog.kakaocdn.net/dn/oOU2V/btsHS9mJz9h/WPLHFJW3O50xSgD2QIRdVK/img.png)

메타데이터의 배치도 고민이 된다. 디렉터리 엔트리에 같이 저장할 수도 있고 분리시킬 수도 있다. 분리시키는 경우에는 파일 이름과 linked list로 어느 block에 있는지 링크해 둘 수 있다. 하이브리드도 존재한다.

자주 쓰이는 건 디렉터리 엔트리에 같이 저장하고 자주 쓰이지 않는 건 다른 block에 저장해서 링크해 놓는 방식이다.

(a) 방식은 디렉터리에 넣는 방식이다. 해당 방식은 사이즈가 고정되어서 파일의 이름의 길이가 제한된다. 따라서 파일의 이름을 길게 만들고 싶으면 엔트리 하나에 대해 파일의 이름 길이를 정해놓고 파일마다 저장하기 할 수 있다.

(b) 방식은 파일의 이름은 Heap에 모아두고 포인터로 링크해두는 방식이다. 엔트리를 고정 크기로 구성할 수 있다. (b) 방식이 좀 더 파일의 이름을 길게 만들 수 있다.

---

## 4. Allocation

디스크 block에 대한 allocation의 문제이다. 하나의 파일은 <file name, metadata, file data>로 구성된다. 이걸 어떻게 block에 배치할까?

### Contiguous allocation

![](https://blog.kakaocdn.net/dn/6ldew/btsHRvyalwY/6qKBOf0qR4g50q2WVKaLi1/img.png)

- 파일별로 필요한 길이만큼 연속된 blcok으로 할당
- 파일의 이름과 시작 blcok 번호, 길이 저장

장점

- 디스크에서 같은 트랙에 배치되어 있을 확률이 높으므로 seek time이 좋음

단점

- 파일의 사이즈가 변해서 예측하기가 굉장히 힘들다.
- 외부 단편화 문제가 생긴다.

### Linked allocation

![](https://blog.kakaocdn.net/dn/cmGpsJ/btsHSZq2jWv/GY79R7FKgx3jhrkrdcKVH1/img.png)

- 정보와 다음 block 포인터를 함께 저장한다.
- 디렉토리 엔트리에는 start, end 포인터만 저장한다.

장점

- 연속적으로 할당하지 않아도 돼서 external 단편화 X
- free block만 있으면 파일 사이즈를 계속 늘리 수 있다.

단점

- 단점은 트랙을 왔다 갔다 해야 하기에 seek overhead가 커진다.
- 포인터를 유지하는 스페이스 오버헤드 존재
- dead block이 나서 포인터가 죽어버리면 파일 전체를 읽을 수 없음

### Linked allocation using a FAT

![](https://blog.kakaocdn.net/dn/niEcC/btsHTHXBJkR/8ZCbJsrngqZY41oh2PKPA1/img.png)

- 디렉토리 엔트리에 start block을 저장한다.
  - start block은 FAT (File Allocation Table) 의 주소를 link하고 있으며, FAT를 따라서 다음 block을 찾는 방식이다.
- FAT이 캐싱만 잘 되어있으면 seek를 꽤나 줄일 수 있다.
- 메모리에 FAT을 올려야 하므로 메모리 부담이 존재한다.

### Indexed allocation

![](https://blog.kakaocdn.net/dn/VhZcr/btsHTCPFKaa/xhVlbKJeTIa23kASEJkbf1/img.png)

- 디렉토리 엔트리에 index block만 저장한다.
- index block(inode)을 통해 block을 순서대로 엑세스할 수 있다.
- 각 파일은 각자의 index block을 가진다.

장점

- direct access 지원
  - index로 특정한 블록에 바로바로 접근 가능
- index block을 메모리에 올려서 캐싱하면 정말 빠르다.

단점

- index block을 위한 메모리 오버헤드

![](https://blog.kakaocdn.net/dn/b3wgGg/btsHTWf5nLY/HKStoYypWuK2rGKi9dknU1/img.png)

리눅스의 inode는 멀티 레벨로 구성되어 있다.

- direct block으로 바로 접근할 수 있고, dircect block으로 부족하면 single indirect가 있어서 single에 접근, 그 다음은 double → single → data와 같은 방식이다.

이렇게 하면 굉장히 큰 파일까지 커버할 수 있다. 대부분은 direct block에서 끝나고 아무리 커도 얼마든지 확장할 수는 있지만 그에 따라 direct block이 좀 늘어날 수 있다.
