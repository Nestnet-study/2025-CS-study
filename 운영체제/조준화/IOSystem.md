## 1. I/O Device

디바이스는 OS 입장에서 크게 Block, Character로 나눌 수 있다.

**Block device**

- **fixed size block**로 정보를 기록하는 장치이다.
- block size는 장치마다 다르다.
- 각 블락별로 address를 가지고 있다.
- **read write가 블락마다 독립적으로 작동한다**는 것이 핵심적이다. 이 말은 랜덤한 seek가 가능하다는 것이다. 즉 110번 read, 120번 write, 29번 read 이런 식으로 가능하다. address가 존재하며 seek 가능하다는 게 가장 중요하다.
- **순서 없이 엑세스 해야 하는 경우에 사용**한다.

**Character device**

- **character의 스트림**으로 다루게 된다.
  - 스트림이라는건 키보드를 예로 들면 키보드를 누른 순서대로 데이터가 오고 가는 것을 말한다.
- **address, seek operation이 필요하지 않다. 따라서** A처리 → D 처리 → C처리 이런 게 불가능하다는 것이다
- **순서 있게 액세스 해야 하는 경우 사용**한다.

---

## 2. Device Controller & I/O Hardware

I/O 장치는 기계적인 부분과 eletronic 한 부분이 존재한다.

- 디스크 판이 돌고, 디스크 기계가 움직이고, 출력을 실행하는 기계적인 부분이 있다.
- device controller가 항상 붙어있다. 또, 해당 device를 위한 driver가 함께 있어야 한다.

**device controller가 하는 일**

- I/O는 디바이스의 버퍼와 메모리를 왔다 갔다 하는 것이다
- 그 과정에서 byte들을 읽고 쓰고 하는 게 I/O 장치인데, 그 byte를 block단위 혹은 stream으로 변환하는 게 컨트롤러의 일이다.
- 필요하다면 error correction도 device controller가 담당한다.

**디바이스는 결국 레지스터를 가지고 있다.**

- 디바이스 드라이버는 그 레지스터를 건드리는 일을 한다. 명령을 받아서 어느 레지스터에 어떤 명령을 수행하는 방식이다. 사전에 약속된 레지스터와 커맨드가 다 정해져 있다.
- 디바이스 드라이버가 특정한 레지스터(컨트롤러)에 명령을 주면 정해진대로 동작하는 게 디바이스인 것이다.
  - 프린터 레지스터에 1이라는 값을 주고 그 옆에 출력할 글자 a를 주면, 기계 장치가 그대로 읽어서 1을 보고 출력을 실행하고 뭘 출력할지 a를 보고 a를 출력한다. 이 명령과 주소, 데이터는 드라이버가 준다.
- 드라이버는 OS 회사에서 만드는 게 아니라, 디바이스를 만든 회사에서 제공한다.

정리하자면 device driver가 device controller에게 커널에서 받은 명령을 전달하고 device controller는 레지스터를 확인하고 명령어를 실행하는 실제 I/O 작업을 수행한다.

---

## 3. Direct I/O, Memory-mapped I/O

드라이버가 어떻게 컨트롤러의 레지스터에 데이터를 쓰는지, 즉 I/O 장치에게 명령을 전달하는 방식은 두 가지로 나뉜다.

**Direct I/O**

- 디바이스에게 명령을 직접 방식이다. 명령을 직접 주기 위해 instruction, port가 모두 존재한다.
  - CPU 만드는 사람이 I/O 장치를 사용할 때 필요한 명령어와 address를 정의하고, 해당 명령어에 맞춰서 디바이스 컨트롤러를 만들어야 한다.
- 정의되지 않은 디바이스가 새로 추가되면 CPU를 새로 설계해야 하므로 제한적이다.
- 어셈블리어를 기본적으로 사용해야 한다.

**Memory-mapped I/O**

- 확장성을 위해 사용하는 방식이다.
- device controller에 존재하는 레지스터를 address space의 특정 위치에 매핑시키는 방식이다. 레지스터를 마치 파일처럼 읽어서 매핑한다. 메모리에 1을 쓰면 I/O 장치의 컨트롤러에 반영되어 레지스터의 값을 변경한다.
- device driver를 전부 C로 작성할 수 있다는 것이다.
- protection도 가능하다. 페이지 테이블에 prot bit가 있으니까 그걸로 가능하다.
- 값을 읽거나 쓰는 것도 메모리에 쓰면 되므로 매우 쉬워서 테스트하기도 쉽다.
- 대부분의 운영체제는 디바이스를 file로 취급한다.

---

## 4. Polled I/O, Interrupt-driven I/O

디바이스에게 명령을 줬으면 그게 끝났는지 CPU에게 꼭 알려줘야 한다. 다시 두 가지 방식으로 나뉜다.

**Polled I/O**

- CPU가 디바이스한테 계속 물어보는 것이다. 레지스터에 있는 bit를 계속 체크하기를 while문을 돌면서 반복한다.
- 간단하다.
- 소프트웨어적으로 컨트롤하기도 쉽다.
- CPU가 I/O의 결과를 빨리 알 수 있으면 효율적이긴 한데 스핀락처럼 계속 결과가 안 나오면 CPU가 낭비될 수 있다.
- priority가 낮은 디바이스에 대해서는 서비스되지 않을 수 있다.

**Interrupt-driven I/O**

- I/O가 끝났거나 에러가 생겼거나 하면 인터럽트를 보내서 알려주게 된다.
- 각 디바이스에 맞는 특정한 ISR이 호출된다.
- 여러 디바이스가 동일한 인터럽트를 공유할 수 있다.
- CPU는 디바이스가 필요로 할 때만 사용된다.
- 일반적으로 폴링 I/O에 비해 효율적이다.
- 인터럽트를 처리하는 데에도 오버헤드가 생긴다. 몇 바이트 단위의 작은 데이터에 대해서도 인터럽트가 계속 발생하면 오버헤드가 더 커질 수 있다.
- 과도한 인터럽트는 CPU가 인터럽트를 처리하느라 다른 일을 못할 수 있다.

![](https://blog.kakaocdn.net/dn/NXXWH/btsHScREjgI/8blzqoK7IgVDHvDvKGfG60/img.png)

---

## 5. Programmed I/O, Direct Memory Access

Input, Output의 버퍼를 통해 데이터를 옮기는 과정, 즉 실제로 데이터를 주고받는 방식은 두 가지로 나뉜다.

- Input : 디스크에 있는걸 IO 장치의 버퍼에 써서 메인 메모리에 쓰는 과정
- Output : 메인 메모리에 있는걸 IO 장치의 버퍼에 써서 디스크로 쓰는 과정

**Programmed I/O**

- CPU가 I/O device와 memory 사이에서 데이터를 직접 옮기는 방식이다.
- 특별한 I/O instruction 혹은 memory-mapped I/O로 구현될 수 있다.

**DMA**

- 메모리 속도와 비슷한 속도의 I/O device에서 사용된다.
- I/O 장치가 명령을 받으면 디바이스 컨트롤러가 버퍼에 있는 데이터를 메모리로 직접 전송한다. CPU의 개입이 전혀 일어나지 않는다.
- 데이터 블록의 전송이 완료되면 인터럽트를 발생시키기에 블록 단위로만 인터럽트를 처리하여 CPU 오버헤드가 더 줄어든다.
- 대량의 데이터 전송을 위한 Programmed I/O를 피하기 위해 사용된다.
- 적은 양의 데이터는 Programmed I/O가 더 빠르다. DMA는 오버헤드가 존재하기 때문이다.

![](https://blog.kakaocdn.net/dn/ckzJxW/btsHSw3prbn/JMKtDkQTBshVUpusqgsRPK/img.png)

DMA의 작동 방식을 자세히 알아보자.

1. CPU가 DMA 컨트롤러의 레지스터를 설정한다.
   - 데이터를 전송할 메모리 주소, 전송할 데이터의 count(크기), 제어 정보가 포함된다.
2. DMA 컨트롤러가 메모리로 transfer request
3. data transfer, 이 과정에서 CPU 개입이 일어나지 않는다.
4. 전송 완료 및 ACK 전송, DMA에게 전송이 끝났다고 알려준다.
5. DMA 컨트롤러는 CPU에게 작업이 끝났음을 알리기 위해 인터럽트를 발생시킨다.

---

## 6. I/O System Layer 총 정리

![](https://blog.kakaocdn.net/dn/bMVtCk/btsHTijdJEa/MT37YVadxYPP9PeV7rEtF1/img.png)

1. User Processes가 I/O를 호출한다.
2. Device-independent software가 file을 기반으로 어떤 디바이스를 오픈했는지, blocking인지 character인지 판단하고, 권한이 있는지 확인한 뒤에 드라이버에 요청한다.
3. 드라이버는 레지스터를 세팅해서 하드웨어로 바로 보낸다. 여기서 Direct I/O, Memory Mapped I/O로 나뉜다.
4. 하드웨어가 I/O를 수행하는데, Programmed I/O 방식이면 CPU가 관여하고 DMA 방식이면 DMA Controller가 관여한다.
5. 작업이 끝났는지 확인하는 방식은 Polling I/O라면 CPU가 계속 체크하고, Interrupt-driven I/O라면 Interrupt를 발생시켜 Interrupt handler가 작동한다.
6. Interrupt handler는 다시 계층을 올라가면서 I/O가 끝나게 된다.
