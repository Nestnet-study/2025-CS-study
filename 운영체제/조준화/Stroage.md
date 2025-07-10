## 1. HDDs

![](https://blog.kakaocdn.net/dn/c9m02Z/btsHR826ehS/XaemhuLE8B4BsV6CXQEyfK/img.png)

- 하드디스크는 여러 개의 트랙으로 이루어져 있고, 트랙은 여러 개의 섹션으로 나뉜다.
- 회전 속도는 7200 rpm 정도로 상당히 빠르다. 한 트랙 안에서 섹션은 빠르게 찾는다.
- 적절한 트랙을 찾는 속도인 seek time은 read는 8.5ms, wirte는 9.5ms 정도로 굉장히 오래 걸린다.
- 내부 캐시 버퍼를 32MB정도 가지고 있다.
- 암과 디스크 사이의 거리도 매우 가깝고 회전 속도는 굉장히 빠른데, 디스크 두께도 굉장히 얇다.

---

## 2. Managing Disk

디스크는 결국 기계적인 장치를 포함하기에 항상 에러의 가능성을 가지고 있다. bad block이 생길 수 있고 seek가 사라질 수 있다.

운영체제 입장에서 디스크는 굉장히 불안하다. 따라서 high-level 소프트웨어 측면에서 뭔가 mess를 숨겨줄 필요가 생긴다. 즉, file, database 등의 레벨에서 디스크의 mess는 신경 쓰지 않아야 한다.

**OS는 disk access를 어떻게 다룰까?**

![](https://blog.kakaocdn.net/dn/dvJLhi/btsHSwia580/pekkvKfKdQ8kgPbMsTUTA1/img.png)

- OS는 a.txt같은 logical 파일을 유저 레벨에서 유저 라이브러리를 통해 제공한다. bytes 단위가 된다.
- logical file이 파일 시스템으로 내려오면 disk logical block이 된다. 커널 레벨이고 Blcok 단위로 다룬다.
- blcok layer로 내려오면 디스크의 어떤 섹터에 네가 원하는 정보가 있어 이런 식으로 변환하여 실제 디스크를 다루게 된다. surface, cylinder, secotr 등 피지컬한 disk block을 다루게 되는 것이다.

디스크의 성능은 seek, rotation, transfer에 달려있다. seek은 매우 오래 걸린다. rotation은 내가 원하는 섹터까지 회전해서 움직이는건데 그렇게 빠른 편은 아니다. transfer는 빠르다.

![](https://blog.kakaocdn.net/dn/IMft3/btsHTDAFVV7/Zd54IZRS5IkPoC8LRl0he0/img.png)

- 즉, seek은 트랙을 맞추는 시간, rotation은 디스크를 회전시켜서 섹션을 맞추는 시간, transfer은 읽어들이는 시간이다.

---

## 3. Disk Scheduling

디스크는 많은 요청이 들어오면 굉장히 바쁠 것이다.

따라서 디스크 내부에 큐를 가지고 운영체제가 block device를 디스크에 요청하게 되면 큐에 달아두고 연산을 다 수행했다면 운영체제에게 알려주는 방식으로 작동하게 된다.

![](https://blog.kakaocdn.net/dn/yLswO/btsHRbTT3Xs/5N2FfuVgroOolekvngDkKK/img.png)

이 과정에서 스케줄링 문제가 생긴다. 큐에 존재하는 job들을 어떤 순서로 접근하는 게 가장 좋은가이다.

A B C D E F로 들어왔다면 FIFO로 처리하는게 합리적이긴 한데 seek 횟수가 너무 크다.

순서를 C F D E B A로 바꾼다면 seek이 왔다갔다하지는 않고 가장 바깥에서 안쪽으로 훑으면 되므로 seek 횟수가 줄어든다. 즉, 스케줄링에 따라 seek time이 바뀌게 된다.

### FCFS(FIFO)

![](https://blog.kakaocdn.net/dn/dvL9HT/btsHR22ZZCB/qy83pRhgDIH1rtojeyPOP1/img.png)

- 꽤 합리적이다.
- request가 굉장히 많아지면 waiting time이 늘어질 수 있다.
- 왔다 갔다 왔다 갔다 하면서 seek가 일어난다.

### SSTF : Shortest Seek Time First

![](https://blog.kakaocdn.net/dn/u9m0M/btsHS1hH9q0/HvbrsPsJdtdKiBJTOtX2K1/img.png)

- seek time이 가장 짧은걸 먼저 골라서 액세스 하는 방식이다.
- 헤드가 53에서 가장 먼저 시작했다면 그다음 가장 가까운 65 67 37 14 … 이런 방식이다.
- arm의 움직임을 최소화시키는 알고리즘이다. 그럼 seek time이 가장 적어지고 따라서 io 성능이 좋아진다.

하지만 두 가지 문제가 생긴다.

1. 가운데에 배치되어 있는 blocks가 선호된다. 선호되는 block이 있어서는 안 된다.
2. starvation의 문제도 생길 수 있다.

### SCAN (엘리베이터 알고리즘)

![](https://blog.kakaocdn.net/dn/beevzv/btsHSsmGp5N/X0NXkuIhokYDj3UHxqlkFk/img.png)

- 마치 엘리베이터가 동작하듯이 한 방향으로 쭉 가면서 다 처리하고 끝에 도달하면 반대 방향으로 쭉 다 이동하면서 다 처리하는 방식이다.
- starvation은 존재하지 않지만 waiting time이 uniform 하지 않다는 문제가 있다.
- 데이터가 어디에 존재하고 엘리베이터 방향이 어떠냐에 따라 waiting time이 너무 길어질 수 있다.

### Circular SCAN

![](https://blog.kakaocdn.net/dn/dLJcxb/btsHQ8JC6Re/p1Yfh0Ji1XYcU9b3Bf4rF0/img.png)

- scan인데 한 방향으로만 처리하겠다는 것이다.
- 53에서 오른쪽으로 쭉 처리하고 끝까지 도달하면 다시 0으로 와서 오른쪽으로 쭉 가면서 처리하는 방식이다.
- waiting time이 조금 더 uniform 해진다.

### LOOK/C-LOOK

![](https://blog.kakaocdn.net/dn/AoTXg/btsHRuFIaTw/KFkbtrKC1n3d6M2MktHQsK/img.png)

- arm이 final request가 존재하는 곳까지만 간다.
- 끝을 찍지 않고 183에서 반대 방향으로 진행(LOOK)하거나 다음 최솟값 요청으로 가는 것(C-LOOK)이다.
- 조금 더 빠를 것이다.

**어떤 스케줄링 알고리즘이 가장 좋을까?**

- SSTF는 자연스럽고 seek time을 최소화시킬 수 있다.
- SCAN, C-SCAN는 디스크에 가해지는 load가 클 때 성능이 좋다.
- SSTF, LOOK, C-LOOK이 합리적이고 디폴트 알고리즘으로 많이 사용한다.
- 성능은 request의 수와 타입에 달려있다.
- 사실 더 중요한 건 파일을 어떻게 디스크에 배치했는지에 따라 성능이 굉장히 달라지게 된다. 예를 들어 a.txt를 4개의 섹터에 배치해야 한다면 당연히 한 트랙에 4개 섹터로 연달아서 배치하는 게 좋다.
