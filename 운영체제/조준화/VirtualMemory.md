# 1. 페이징에 관하여

## 1. 페이징

운영체제는 메모리를 가상의 페이지로 관리한다.

- 실제 물리 메모리는 frames라 불리는 고정된 크기의 block으로 분할된다. 이때 유저 입장에서 메모리는 연속적인 페이지를 원한다. 하지만 물리 메모리는 연속적으로 할당하는 것은 단편화라는 큰 문제점을 가진다.
- 따라서 가상의 페이지로 메모리를 관리하여 사용자로 하여금 연속적인 메모리를 사용하는 것 처럼 느끼게한다.

주소를 번역하는 과정은 어떻게 될까?

- 가상 주소는 두 부분으로 이루어져 있다.
  - **<virtual page number :: offset>**
- 가상 주소를 번역하려고 페이지 테이블에 접근할 때 VPN을 인덱스로 사용한다.
- offset은 페이지 테이블을 거치지 않고, 번역 없이 그대로 물리 주소로 사용할 수 있다. 하나의 페이지는 똑같은 크기의 프레임으로 매핑되므로 가상 주소의 offset은 물리 주소로 변환되어도 전혀 달라지지 않기 때문이다.
- 즉, **<VPN::offset> → <PFN::offset>, VPN → PFN** 과정으로 주소가 번역되는 것이다.

**Page Tables**

- 페이지 테이블은 OS에 의해 관리된다.
- VPN을 PFN으로 바꾸는 역할을 한다.
- 가상 address space에서 페이지 하나당 한 페이지 테이블 엔트리(PTE)를 가진다.
  - **one PTE per VPN**
- 페이지 테이블은 배열로 사용하고, index로 VPN을 사용하기에 빠르게 PFN를 찾을 수 있다.
- CPU에 **32bit address가 들어오면 VPN 값을 가지고 page table의 VPN index (위에서 VPN번째)를 찾아보면 PFN을 얻을 수 있다. 즉, PFN 값을 통해 피지컬 메모리에 접근하게 되고, 피지컬한 메모리에서 특정 프레임 PFN의 오프셋만큼** 가면 내가 접근하려는 메모리 조각이 존재하는 것이다.

Virtual address, Physical address, Page size가 주어진다면 페이지 테이블의 전체 형태를 예측할 수 있다.

- Virtual address : 32 bits (4G)
- Physical address : 20 bits (1M)
- Page size : 4 KB

이 상황에서 피지컬한 메모리가 얼마나 꽂혀있는지, VPN, offset, PFN, page table size, page table entry를 계산해보자.

피지컬한 어드레스는 20bit만 사용한다고 했으므로 이 컴퓨터에는 피지컬한 메모리가 1MB 꽂혀있다는 뜻이다.

- 참고로 가상 메모리는 OS에서 정하기에 제한이 없지만 피지컬한 메모리는 얼마나 꽂느냐에 달려있다.
- 30bit를 꽂으면 1GB, 31bit를 꽂으면 2GB, 33bit를 꽂으면 8GB인데, 가상 주소가 32bit이기 때문에 33bit는 꽂을 필요가 없다.

페이지 size를 4KB로 정했다고 했다. 페이지 size가 4KB라는 말은 하나의 페이지가 2^12 byte의 데이터를 가지고 있다는 뜻이다.

- 여기서 주의해야 할 점이 32bit 머신의 어드레스 스페이스 한 칸의 크기가 1byte이다. 페이지가 1byte 단위로 데이터가 끊긴다는 것이다.

페이지 size가 2^12 byte이므로 offset은 12bit가 된다. 12bit의 offset으로 2^12개의 바이트를 식별할 수 있기 때문이다.

virtusl address가 32bit이므로 VPN은 20bit가 될 것이다.

- offset이 정해지면서 VPN bit가 20bit로 정해졌다. 그렇다는 말은 가상 주소가 2^20개로 표현된다는 것이다. 따라서 페이지 테이블은 2^20개의 엔트리를 가지게 된다.

또, 정해진 offset을 물리주소에서도 똑같이 사용하므로 PFN도 정해진다. 물리주소가 20bit이므로 8bit가 되는 것이다.

PFN이 정해졌으므로 페이지 테이블 엔트리 size도 8bit가 된다.

---

## 2. Page Table Entries (PTEs)

페이지 테이블에는 프레임 넘버뿐 아니라 몇 가지 정보를 더 가지고 있다.

![](https://blog.kakaocdn.net/dn/erHSkW/btsHGC4z5Gw/sY21RFLSHLK6EgExkvjgE1/img.png)

**Valid**

- 이 페이지 **테이블 엔트리가 사용되고 있는지 아닌지**를 나타낸다.
- OS는 프로세스가 해당 페이지를 사용하던 안 하던 해당 프로세스가 쓸 수 있는 만큼 다 만들어놓는다. 따라서 해당 페이지 테이블 엔트리가 사용되는지 아닌지, 혹은 유효한지 아닌지를 나타낸다.
- **Valid가 1이라면 관련된 페이지는 유효하게 피지컬한 메모리에 들어있다**는 의미가 된다.
- **0이라면 페이지 테이블에 들어있는 프레임이 메모리에 없다거나 빈 공간이라는 등의 방식으로 유효하지 않은 걸 나타낸다**. 보통 스와핑 때문에 메모리에 존재하지 않는 경우이다. **아직 사용된 적 없거나, 빈 공간 확보를 위해 페이지를 메모리로 내려보내고 빈 페이지로 두고 Valid bit를 0으로 세팅할 수도 있다.**

**Reference**

- 참조가 일어났는지 아닌지를 나타낸다. 어떤 페이지에 대해 read 혹은 write를 하면 R값이 1이 된다.
- 초기에는 0으로 설정되고 프로세스가 해당 페이지에 액세스 하면 R이 1이 된다.
- 주기적으로 R값을 전체 0으로 세팅하기도 한다.

**Modify**

- R과 비슷한데 write가 일어났을 때만 체크된다.
- 해당 페이지를 읽기만 하면 R만 1이 되고 M은 여전히 0이다. 해당 페이지를 쓰면 둘 다 1이 된다.
- 페이지의 내용이 바뀌어서 dirty 한 상태인지 아닌지 알기 위해서 사용한다. 나중에 페이지 replacement algorithm에서 사용된다.

**Protection (Prot bit)**

- 이 페이지가 Read, Write, Execute가 가능한지 불가능한지 나타내기 위해 사용한다.

---

## 3. 페이징 장단점

**페이징 장점**

- **피지컬한 메모리를 할당하기 굉장히 쉽다.**
  - 프로세스가 필요로 하는 메모리 전체를 다 피지컬한 메모리로 할당하는 방식이 아니라 페이지 테이블에 프레임 넘버만 적어주면 된다.
  - OS는 사실 빈 프레임의 리스트를 유지하기에 거기서 하나 빼서 프로세스 페이지 테이블에 적어주면 끝이다.
- 고정된 크기로 프레임을 자르기에 **external fragmentation이 생기지 않는다**.
- 어떤 프로그램을 **Page out 시키기 쉽다.**
  - 페이지 사이즈가 모두 동일하다. 따라서 메모리가 부족하다면 페이지 테이블에서 필요 없는 엔트리의 **valid bit를 0으로 만들면**, replacement 알고리즘에 의해 삭제된다.
  - 페이지의 크기는 보통 disk block size의 배수로 설정된다.
- **프로텍션이 굉장히 쉽다**. 코드 부분에 있는 실행 코드를 업데이트시키거나 스택 영역에 코드를 써서 실행시키는 등의 접근을 막을 수 있다.
- **페이지를 공유하기 쉽다.** 여러 프로세스들끼리 공유하려는 프레임을 페이지 테이블 엔트리에 적어주면 끝이다.

**페이징 단점**

- **internal fragmentation 문제를 가지고 있다.**
  - 페이지의 일부만 사용해서 나머지는 사용하지 않는 문제가 생겨도 페이지가 4KB라 그렇게 큰 문제가 되지는 않는다.
- **메모리를 액세스 하는데 시간적 오버헤드가 성능상 꽤 크다.**
  - 메모리에 있는 i라는 값을 액세스 하기 위해 페이지 테이블을 가서 i값의 프레임 넘버를 찾고, 피지컬 메모리에 가서 액세스해야 한다. 따라서 메모리에 두 번 접근해야 한다는 오버헤드가 발생한다. 레지스터에 액세스 하는 거에 비해 메모리에 액세스 하는 건 굉장히 느리므로 꽤나 큰 오버헤드이다.
  - 이 문제는 TLB 하드웨어의 지원으로 해결할 수 있다.
- **굉장히 큰 페이지 테이블을 유지하는 메모리가 요구된다. 즉, 스페이스 오버헤드가 발생한다.**
  - 페이지 테이블을 프로세스가 쓰던 안 쓰던 많은 양의 엔트리를 준비해놔야 한다.
  - 예를 들어, 32bit address space, 4KB page는 2^20의 PTE를 준비해놓아야 한다.
  - 페이지당 4 bytes를 사용한다면 페이지 테이블의 크기는 4MB가 된다.
  - 25개의 프로세스를 생각해 보면 100MB를 페이지 테이블로 사용해야 한다.
  - 이렇게 **미리 준비하는 이유는 배열의 형태를 사용하기 위해서**이다. 배열의 **index로 O(1)에 페이지 테이블에 접근할 수 있어**서 매우 빠르기 때문이다. linked list로 구현한다면 space overhead는 해결되지만 index를 사용할 수 없어서 데이터를 찾는데 O(n) 시간으로 오래 걸린다.
  - **page the page tables, multi-level page tables, inverted page tables**로 해결할 수 있다.

---

## 4. Demand Paging

**Demand가 있을 때 Paging을 시키겠다**는 개념이다. 메모리가 정말로 필요한 순간에 가상 페이지를 피지컬한 메모리에 할당한다.

- 피지컬한 메모리와 디스크를 왔다갔다하는 **I/O 횟수가 줄어든다**.
- 피지컬한 **메모리의 필요량이 줄어**든다.
- **응답이 빨라**진다.
- **좀 더 많은 프로세스를 수용**할 수 있다.

OS가 **메인 메모리를 마치 캐시처럼 사용**한다.

- 메모리를 디스크의 캐시처럼 사용한다.
- 프레임 단위로 디스크에 내리거나 필요한 게 있으면 메모리로 올려서 사용하는 방식이다.
- 피지컬한 메모리가 다 차게 되면, 메모리가 부족해진 것이므로 적절한 프레임을 찾아서 디스크로 내린다.
- 디스크로 내리는 과정을 Eviction이라 하고, 반대의 과정 메모리로 올리는 것을 loading이라 한다.

**디스크로 내리는 과정**을 보자.

- 디스크로 내려서 eviction 시켜야 할 때 해당 프레임이 읽기만 해서 clean 상태인지, 뭔가가 write가 일어나서 dirty 상태인지 **modify bit**로 알 수 있다.
  - 만약 **dirty 상태라면 디스크에 반드시 써야한다**. 메모리와 디스크의 내용이 다르기 때문이다.
  - **clean 상태라면** 굳이 다시 쓸 이유는 없다. 그냥 **페이지 테이블과의 매핑을 끊어**버리면 된다. 이렇게 **evited된 PTE는 디스크를 가리키고 있다**.
  - 따라서 dirty인지 아닌지 확인하는 것이 중요하다. 디스크에 쓰는 것은 굉장히 오래걸리기 때문이다.
- 운영체제는 알아서 메모리에서 디스크를 왔다갔다하면서 eviction, loading을 수행한다.
- **Transparent to the application** : 어플리케이션은 이 페이지 교체 작업을 전혀 몰라도 된다.

---

## 5. Page faults

evicted page virtual address로 요청이 왔을 때 즉, **valid bit가 0인 페이지에 요청이 들어오면 해당 페이지는 피지컬한 메모리에 존재하지 않는다. 그런 페이지에 대해 요청이 있는 상황을 page faults**라 한다.

- ecvition이 일어날 때 valid bit를 0으로 만들고 해당 페이지가 디스크의 어디에 있는지 적어놓는다.
- 그리고 이 페이지에 접근하면 exception이 발생한다.

그리고 운영체제에 있는 코드인 **page fault handler가 작동**한다.

- page fault handler는 **디스크(swap file)에서 요청이 들어온 데이터가 어디있는지 찾는다**.
  그리고 **읽어서 메모리에 적는다**. 이 과정에서 **disk I/O**가 발생한다.
- 그리고 **페이지 테이블을 업데이트** 시킨다. **valid bit를 1로도 만들고** 기존에 어딘가를 가리키던 페이지를 새로 메모리로 바꾸는 과정이 일어나는 것이다.
- 위의 핸들러 과정이 다 끝나면 **처음부터 다시 요청**한다.

페이지를 다시 올릴 땐, 보통은 메모리가 꽉차서 해당 페이지가 내려가 있었던 것이다.

따라서 다시 페이지를 올릴 때 어떤 프레임을 빼서 새로운 프레임을 올릴것인지 문제가 된다.

- **앞으로 사용되지 않을 프레임을 빼면 된다. 이를 replacement algorithm**이라 한다.
- 기본적으로 운영체제는 빈 프레임을 유지한다. 사실은 메모리가 꽉 차서 내리는건 아니고 빈 프레임을 유지하기 위해 내리는 것이다.
- 그럼 운영체제가 적절한 빈 프레임을 얼마나 유지할까? 보통은 빈 공간을 10% 정도로 유지하는데, 이것도 고민이 된다.

![](https://blog.kakaocdn.net/dn/kzax0/btsHGiFFyR8/4O2b0rMRAtaoWeLjB3M2Rk/img.png)

**페이지 폴트가 일어나는 과정을** 다시 정리해보자.

1. **프로세스가 가상 주소로 요청한다.**
2. 가상 주소로 페이지 테이블에 엑세스했더니 **해당 페이지의 vaild bit가 0**이다. 그렇다면 **trap, exception이 발생**한다.
3. 운영 체제 코드로 넘어오고 운영체제는 **page fault handler 함수를 호출**한다.
4. 내가 원하는 **페이지가 디스크의 어디에 있는지 찾고, 찾은 파일을 읽어서 빈 프레임에 쓴다. (I/O)**
5. **페이지 테이블을 다시 세팅하고 valid bit를 1로 만든다.**
6. 아까 요청한 주소를 **다시 요청**한다.

---

## 6. Segmentation

**코드, 스택, 힙, 라이브러리 등의 논리적으로 연관된 데이터 유닛을 묶어서 하나의 세그먼트로 할당하는 것**이다.

- 스택 세그먼트 따로, 메인 프로그램 세그먼트 따로 그런식이다.

메모리를 **variable-sized segments의 컬렉션**으로 보는 방식이다.

- Varaible-size partitions와 유사하지만 한 프로세스에 한 세그먼트가 아닌, **한 프로세스에 여러 세그먼트를 사용**하는 것이다.
- **세그먼트들을 ordering 할 필요는 없다**.
- 세그먼트를 사용한 가상 주소는 **<Segment # :: Offset>**가 된다.

**세그먼트는 사이즈가 늘었다 줄었다 변할 수 있다**.

![](https://blog.kakaocdn.net/dn/b29iN3/btsHHckxXGz/eTrtCGY4v0VFknVTBCk9tk/img.png)

세그먼트를 위해서는 **segment table**이 필요하고, 그 테이블에는 **세그먼트의 수만큼 엔트리**가 필요하다.

세그먼트 1번을 보면 6300 ~ 6700 이런식으로 할당하는데, 이를 세그먼트 테이블로 관리한다.

**base가 시작주소이고 limit는 세그먼트의 크**기이다.

limit을 조정하면서 세그먼트의 사이즈를 늘리고 줄일 수 있다.

### **세그먼트 장점**

- **사이즈가 변하는 데이터 스트럭쳐를 관리하기 용이**하다. 스택같이 계속 변하는 자료 구조도 limit 값만 변경하면 된다.
- 세그먼트를 **프로텍션**하기 굉장히 좋다.
  - 페이징에서 data 영역은 r/w, code는 ex로 세팅할 수 있는데 **code + data가 섞인 페이지는 굉장히 곤란하다.**
  - 그런데 세그먼트 단위는 이런 **섞이는 부분이 없어**서 좋다. **스와핑도 세그먼트 단위**로 하면 좋다.
- 세그먼트 단위로 **공유**하기 좋다.
  - 외부 라이브러리처럼 공유하는 데이터는 base/limit register를 똑같이 주면 공유된다.

### **세그먼트 단점**

- **cross-segment address**
  - 여러 프로세스가 동일한 세그먼트를 공유해서 포인터를 사용하려면 해당 세그먼트의 번호가 동일해야 한다. 따라서 포인터의 사용이 어렵다.
  - 따라서 indirect addrssing만 사용해야 한다
  - 따라서 **세그먼트 내에서만 포인터를 쓸 수 있다**.
- **external 단편화**
  - 빈 공간이 조각조각 나있으면 그 부분은 사용할 수 없다.
- 세그먼트 테이블을 프로세스마다 유지, 관리해야 한다. 페이징에 비하면 굉장히 작긴 하다. TLB처럼 하드웨어적 서포터를 받으면 된다.

---

## 7. Paging vs Segmentation

![](https://blog.kakaocdn.net/dn/c65aBZ/btsHIzFGXa5/QOuEs686ZkkLr905HejlYk/img.png)

- paging은 block size가 정해져있다. 보통 4KB를 쓴다. segmentation은 block size가 변할 수 있다.
- Linear address space : Paging에서는 하나이다. Segmentation에서는 여러개가 된다. 세그먼트끼리 연속된 메모리 주소를 갖지 않기 때문이다.
- Replacement, Disk traffic : Paging은 좀 더 쉽고 페이지 단위로 효율적으로 replace하기 쉽다. **Segmentation은 세그먼트마다 사이즈가 다르므로 어렵고 비효율적**이다.
- Transparent to the programmers : Paging은 연속적인 주소를 가지므로 프로그래머는 신경쓰지 않아도 된다. Segmentation은 포인터라던지 조심해야할 부분이 있으므로 프로그래머가 신경써야 하는 부분이 존재한다.

![](https://blog.kakaocdn.net/dn/3Endg/btsHGYmGQbZ/efZSVQcbXMcUqr8kPdJYf0/img.png)

- 피지컬한 메모리보다 더 큰 공간을 프로세스가 사용할 수 있을까? : 둘 다 swapping하면서 사용하면 되므로 사용할 수 있다.
- 코드와 데이터가 구별되고 분리되어서 protected 될 수 있을까? : Paging에서는 그러지 못할 가능성이 있다.
- 크기가 변동하는 테이블을 쉽게 조정할 수 있을까? : Segmentation은 base, limit register만 잘 옮겨주면 된다. Paging은 가능은 하다. 페이지를 더 할당해주면 되지만 애초에 그런 목적으로 설계된 기법은 아니기에 불편하다.
- 코드의 share가 쉬운가? : Paging에서는 잘 안될 가능성이 높고 Segmentation은 편하다.
- 왜 해당 기술이 고안되었는가? : **Paging은 하나의 긴 연속된 어드레스 스페이스를 제공하기 위해서 고안**되었다. **Segmentation은 논리적으로 독립된 어드레스 스페이스를 sharing과 protection이 용이하게끔 제공**하기 위해 고안되었다.

물론 여기에 나온 개념들이 맞다/틀리다로 항상 나눠질 순 없고, 각 방식의 특징을 잘 기억하고 고안된 목적을 생각하면 구분하기 쉽다!

---

## 8. Segmentation with Paging

실제로는 이 두 방식을 hybrid 하는 방식을 많이 사용한다.

**Segmentation with Paging**이라는 기법을 거의 다 사용한다. **Segment를 페이지로 쪼개서 관리**하는 기법이다. 페이지 사이즈는 CPU 아키텍쳐마다 여러 개의 사이즈를 제공하기도 하고, 하나의 사이즈만 사용하기도 한다.

![](https://blog.kakaocdn.net/dn/brrGQA/btsHH73UCZm/DrrF3TNzmrPdaNNupDREIk/img.png)

기본적으로 Segmentation을 사용한다.

- 코드, 데이터, 힙을 분리해서 사용한다.
- 사이즈 또한 늘렸다 줄였다 하면서 사용한다. (multiple pages)

**Segment 하나를 페이지 단위로 쪼개서 사용한다.**

- 페이지들을 프레임으로 할당해서 여러 군데로 흩어서 배치하게 된다.
- 이렇게 만들면 **demand paging 하듯이 일부는 디스크에 있을 수 있고 일부는 메모리에 있을 수 있다.**
- Segments를 메모리로 넣었다가 뺐다가 하지 않고 그냥 페이지만 옮기면 된다.
- **external fragmentation이 존재하지 않는다.**
  - segmentation이 고정된 크기로 분할되기에 존재하지 않는 것이다.
- **segmentation table이 필요하고, 각 segmentation마다 page table이 필요하다.**

---

# 2. 페이지 테이블의 구현

## 1. Two-level Page Tables

![](https://blog.kakaocdn.net/dn/coj3Ly/btsHKjwTrts/0A3rO3AGzElMIN7rZAvNZ0/img.png)

Two-level page table은 다음과 같은 주소와 테이블을 가진다.

- **[Master page #, Secondary Page #, Offset]**
  - Master page table : master page number → secondary page table
  - Secondary page table : secondary page number → page frame number

![](https://blog.kakaocdn.net/dn/bfQ3hw/btsHKbFNO8K/WmdjhqaSQb7csVCu8Vsb60/img.png)

Two level 구성의 가장 큰 이득은 **한 세컨더리 페이지 테이블 안에서 프로그램이 다 돌아간다면 나머지 세컨더리 페이지 테이블들은 미리 만들 필요가 없다는 것이다.**

따라서 세컨더리 페이지 테이블을 이용한 Two level Page table을 사용하면 메모리를 많이 아낄 수 있다.

하지만 **페이지 테이블에서 무조건 두 번의 액세스 발생**한다는 문제점이 존재한다. 따라서 **성능적으로 오버헤드가 더 증가한다.**

### Multi-level Page Tables

멀티 레벨 페이지 테이블도 있다. 레벨 1이 master page table과 유사한 테이블이고 master page table을 이용해서 레벨 2, 레벨 3에 접근할 수 있고 레벨 3에서 피지컬 한 어드레스에 접근할 수 있다. 이 방식은 메모리는 굉장히 많이 아낄 수 있지만 **오버헤드는 4배이다.**

심지어 Multi-level page table 앞에 segment도 있어서 segment table 한 번 더 뒤져봐야 한다.

---

## 2. Hashed Page Tables

![](https://blog.kakaocdn.net/dn/b4eKKd/btsHKnMLB49/kCbRc6RjCTXAviK3NapufK/img.png)

- virtual address의 **virtual page number를 hash function(모듈러 등)을 돌리는 방법**이다.
- 해시 테이블 엔트리는 충돌 문제 때문에 linked list로 연결된다. **vpn과 일치하는지 linked list를 따라가면서 체크**하게 된다.
- 해시 테이블을 사용하면 페이지 테이블의 사이즈가 줄어든다. 페이지 테이블의 엔트리 수가 해시 펑션에 따라 정할 수 있는 것이다.
  - **1024로 모듈러 연산을 쓴다면 1024개의 엔트리를 가진다.**
- 이 방식은 linked list를 따라서 **search 해야 한다는 문제**를 가지고 있다.

### Clustered page tables

해시 테이블을 개선하기 위해 Clustered page tables를 고안되었다.

- **해시 결과가 같은 노드 여러 개를 묶어서 한 클러스터화 시키고 이들을 linked list**로 만드는 방식이다.
- 그리고 VPN의 일부를 Block offset으로 정한다.
  - **노드 안에서 Block offset을 이용해서 검색**하면 linked list를 이전 방식보다 조금만 검색해도 된다. 예를 들어 block offset이 3이면 VPBN 2를 바로 보고 VPBN이 일치하지 않으면 다음 linked list를 뒤져본다.

사실 노드를 채우는 방식이 순차적으로 채우는 게 아니라 **VPBN에 따라 Block offset에 따라서 값을 또 정하는 것이므로 메모리 낭비가 좀 더 있을 수 있다**.

Clustered page tables가 더 좋은 이유는 search 횟수가 적어진다는 것이다.

![](https://blog.kakaocdn.net/dn/bfZvLA/btsHLfmWek4/bz8WnGYBMSrwwwfDSZmqdk/img.png)

정리하자면 VPN을 VPBN과 Block offset으로 나누고 VPBN을 해시함수를 돌려서 해시 테이블에 접근한다. 해시 펑션의 결과가 같으면 linked list로 달아두는데, Block offset을 이용해서 달아놓는다. Block offset이 2라면 PPN2만 linked list를 따라가면서 확인하면 된다.

가상 주소로 접근하면, VPBN bit 만큼을 해시 펑션을 돌리고, Block offset bit 만큼을 index로 사용해서 그 부분만 딱 딱 search하면 된다.

---

## 3. Inverted Page Table

원래는 가상 주소로 페이지 테이블을 만들었다. Inverted Page Table은 반대로 **피지컬한 메모리를 기준으로 페이지 테이블을 만드는 방식**이다. **페이지 테이블 엔트리 하나하나가 프레임 하나하나에 매핑된다**.

![](https://blog.kakaocdn.net/dn/bZ6eJq/btsHLcjAbNI/uVjyvuXdK7YjoNzxk8xRF0/img.png)

- 하나의 엔트리가 메모리의 real page에 매핑된다.
- 엔트리는 real memory에 저장된 **페이지의 가상 주소와 해당 페이지를 소유하는 프로세스의 정보**로 이루어져 있다.
- **PID를 관리해야만 한다.**

**주소를 번역하는 과정은 다음과 같다.**

1. CPU는 32bit의 **page number, offset, pid 주소를 받아서 페이지 테이블을 search** 하기 시작한다.
2. 페이지 테이블의 위에서부터 search를 진행하다가 **pid, p가 일치하는 i+1번째 페이지**를 찾았다면
3. **i번째 프레임과 매핑된 것이다. i, d가 물리 주소**가 된다. i가 offset처럼 작동하지만 실제 offset은 아니라는 것을 주의하자!

**장점**

- **페이지 테이블의 사이즈가 피지컬한 메모리의 사이즈에 의해 결정된다.**
  - 만약 피지컬한 메모리가 4GB라면 페이지 테이블은 2^22이 될 것이다. 엔트리 하나의 사이즈가 **pid 4byte, page number 4byte라면 8\*2^22**이다. 프로세스 하나라면 비효율적이다.
  - 기존의 방식에서는 프로세스의 개수만큼 페이지 테이블이 필요하다. 그러나 이 방식에서는 **페이지 테이블이 global 하게 딱 하나**만 있으면 된다. 프로세스가 여러 개라면 pid를 가지고 찾는 것이므로 페이지 테이블 하나의 크기는 좀 크더라도 전체적으로 따지면 아낄 수 있다.
- pid와 p만 알면 프레임에 따라 페이지 테이블을 정한 것이므로 프레임 넘버 i를 바로 알 수 있다.

**단점**

- 최악에는 **엔트리 수만큼 search** 해야 한다.
  - 앞에 해시를 쓰거나 서치 트리를 만들거나 하면 search 수를 줄일 수 있지만 어쨌든 간에 search 자체가 오버헤드가 된다.
- 또 하나의 오버헤드는 **PID를 유지 관리**해야 한다는 것이다.
  - 만약 3번 프로세스가 100개 정도의 페이지를 사용했다면 페이지 테이블 엔트리 100개 정도를 3번이 쓰고 있었을 테고, 3번이 죽는다면 그만큼 페이지 테이블에서 찾아서 지워야 한다.
  - 즉, 페이지 테이블 전체를 search 하는 일이 꽤 빈번하게 일어난다.

이런 오버헤드를 제외하면 페이지 테이블 사이즈는 확실히 줄일 수 있다**. Inverted가 아마도 페이지 테이블 메모리를 많이 아낄 수 있겠지만 serach 오버헤드는 가장 클 것이다.**

실제로는 Multi level Page Tables를 많이 사용한다. 임베디드에서 딱 하나의 프로세스만 가능하다면 Inverted도 나쁘지 않을 수 있다. 임베디드에서는 피지컬 한 메모리가 매우 작을 것이기 때문이다.

---

## 4. TLBs

페이지 테이블을 거쳐서 메모리에 액세스 하면 시간이 많이 걸린다. **original page table은 doubled the cost of memory lookups 하다. Two-level에서는 triple the cost 하다!**

시간적인 오버헤드를 줄이기 위해서 도입된 게 TLB이다. **TLB의 목적은 메모리 액세스 한 번의 수준으로 물리 주소를 얻는 것**이고, 해결책은 캐싱밖에 없다.

페이지 테이블의 일부 내용을 캐싱하고 있는 하드웨어가 **Translation Lookaside Buffer** TLB이다. MMU안에 TLB가 들어있다.

**TLB는 어떻게 구성되어 있을까?**

- 캐시 안에는 페이지 테이블과 상당히 유사한 데이터를 가지고 있다.
- 그림에는 없지만 ref가 있을 것이다.
- **태그로 사용하기 위해 virtual page도 가지고 있다.**

![](https://blog.kakaocdn.net/dn/cIUTB3/btsHKI37rg0/5gRt9gwsD0MWj47EUkRNs0/img.png)

- TLB는 하드웨어이고, 일반적으로 **Fully associative cache**이다.
- 캐시를 쓸 때 보통 태그를 쓴다. 내가 원하는 데이터가 캐싱된 데이터가 맞는지 확인하기 위해 태그를 쓰고, 여기서는 page number가 된다.
- 캐시에 들어있는 **값들은 페이지 테이블 엔트리들**이 될 것이다.
- PTE와 offset을 잘 조합하면 MMU가 피지컬 어드레스를 계산할 수 있다.

**TLB는 캐시이기 때문에 로컬리티를 역시 사용한다.**

- TLB는 로컬리티가 굉장히 좋을 것이다. 페이지 테이블은 한 번 액세스 하면 자주 액세스하기 때문이다. hit율이 굉장히 높다.

---

## **5. TLB hit 총 정리**

![](https://blog.kakaocdn.net/dn/YbiIM/btsHKF0OmPK/7YMGsaWkVyMlc8Enii7mDk/img.png)

1. **TLB hit**
   - 대부분 이 케이스이다.
   - **엔트리를 찾아낼 수 있고 엔트리 자체도 피지컬 한 프레임에 할당되어 있는 경우**
   - **메모리 액세스가 한 번 일어난다.**
   - 그림으로 따지면 p로 f를 찾았는데 f가 메모리에 할당된 경우이다.
   - 드물게 **TLB에는 있는데 그게 할당되지 않아서 page fault가 일어난 상황도 존재한다.**
     - 이게 왜 엄청나게 드무냐면 보통의 상황에서는 TLB hit인데 page fault 일 수 없다.
     - PTE가 디스크로 내려간다면 TLB도 업데이트하기 때문인데, 이 케이스는 테이블 자체가 내려가면 가능하긴 하다.
     - 이 경우에도 **메모리** **액세스는 한 번** 일어난다.
2. **TLB miss -** **page table is in memory**
   - 메모리에 존재하는 페이지 테이블에서 찾아서 TLB를 업데이트하고 다시 시작한다.
   - frame이 메모리에 있으면 **두 번의 메모리 액세스가 일어나지만**, frame이 없다면 **page fault가 발생해 세 번의 메모리 엑세스와 한 번의 디스크 엑세스가 일어난다.**
3. **TLB miss - page table is page out**
   - page table을 위한 page fault handler를 실행한다. -> **메모리 한 번, 디스크 한 번 엑세스**
   - 페이지 테이블을 올리고 TLB을 업데이트 한 뒤 restart 한다. -> **메모리 한 번 엑세스**
   - 여기서도 또 프레임에 대해 page fault가 일어날 수 있고 안 일어날 수 있다. → reculsive page fault
   - 프레임에 대한 pf가 일어나지 않았다면 메모리 **액세스가 총 세 번, 디스크 엑세스가 한 번 일어나고**, pf가 일어났다면 메모리 엑세스 **네 번, 디스크 엑세스가 두 번 일어난다**.
