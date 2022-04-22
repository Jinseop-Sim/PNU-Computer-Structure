# External Memory
---
## Disk
> Disk란?  
> 기본적으로 자화 가능한 물질을 원판에 코팅해 놓은 것.  

- 원래는 알루미늄 재질을 사용했지만, 현재는 유리를 사용한다.
  - 안정성이 증가한다.
  - 표면의 결함이 감소한다.
  - Lower Flight Heights(Head가 조금만 떠서 움직여도 된다.)
 
## Magnetic Disk
> Disk의 종류 중 가장 대표적이고 널리 사용되는 Magnetic Disk.  

![image](https://user-images.githubusercontent.com/71700079/164605975-4ee66869-0d0b-4d37-81b4-caf8fd118d1a.png)  
- 기본 원리는 0또는 1로 이루어진 전기적 신호로 디스크 내의 물질을 자화시킨다.(Write)
  - 읽을 때는 __MR Sensor__ 를 통해서 자화된 상태를 읽는다.
  - 읽고 쓰는 장치를 총칭해서 Head라고 부른다.(ARM에 부착되어 있다.)

### Feature
![image](https://user-images.githubusercontent.com/71700079/164606113-7cc615c3-ec9c-400d-a0d6-e524c865ce0a.png)  

- Platter : 원판. 보통 3개 이상의 Platter을 사용하며, 윗면 아랫면을 모두 사용한다.
  - Platter가 3개일 경우, Head는 총 6개가 필요하다.
- Cylinder : Data가 저장되는 Track을 세로로 죽 이어서 만든 가상의 원통
  - Cylinder 개념의 장점은, Head의 움직임을 줄여주고 Transfer Rate를 올려주는 것.\
- Concentric Rings : Track.
  - Track과 Track 사이에는 반드시 Gap이 존재한다.
  - 겹쳐있으면 자화과정에서 문제가 생길 수도 있기 때문이다.
- 피자 조각처럼 잘린 한 구역을 Sector(Block)이라고 한다.
  - Sector도 각각 Gap으로 구분이 되어 있다.
- CAV(Constant Angular Velocity) : 일정한 각속도를 말한다. (Disk 방식)
  - Magnetic Disk는 LV(Linear Velocity), 선속도는 안 쪽 Track으로 갈 수록 빨라진다.
  - CD는 CLV(Constant Linear Velocity)이다.
![image](https://user-images.githubusercontent.com/71700079/164606692-20533a27-d2ee-48d6-92fd-c4c48f851674.png)  
- CAV로 인해, 각속도가 모두 동일하기 때문에 (a)와 같이 설계하면, 안 쪽은 적은 용량 밖에 쓸 수 없다.
  - 따라서 (b)와 같이 Multiple-zone Recording을 통해 안쪽도 용량을 보장받을 수 있다.

### How to find Sector?
> 그럼 Disk는 읽을 때 현재 내 Sector과 내가 가려는 Sector을 어떻게 구분할까?  

- 현재 내가 읽고 있는 자리가 몇 번 Sector인지 계속 기록을 해야한다. (Formatting)
- 515 Byte로 하나의 Sector Data를 구성한다. (Synch Byte + Data + CRC)
  - ID Field에 Synch Byte, Track #, Head #, Sector #, CRC가 저장된다.
    - 이는 곧 내가 현재 어느 Sector에 위치하는지? 를 말한다.
  - 아래와 같은 구성을 갖는다.  
  ![image](https://user-images.githubusercontent.com/71700079/164607066-143ecaed-941f-43ad-a1ab-abe2fb2f0a13.png)  
- 내가 원하는 Sector가 나올 때 까지 Track을 돌아야 한다.(Direct Access 방식)
  - 이 일에 걸리는 시간이 바로 Access Time이 된다.

### Speed(Rate)
- Seek Time : Head를 정확한 Track에 가져다 놓는데에 걸리는 시간. (보통 10ms 안이다.)
- Rotational Latency : Head 아래에 해당 Sector가 위치하는 데에 걸리는 시간.
- Access Time = Seek time + Rotational Latency
- Transfer Time = b / rN
  - b : # of bytes to be transferred
  - N : # of bytes on Track
- Example
  - Average Seek time : 4ms, Rotate 7200RPM (Average Rotation Delay : 4ms)
    - 7200RPM이면, 1초에 120바퀴, 즉 1바퀴 도는데에 8ms가 걸린다는 말이다. 
    - 하지만, 가까이 있으면 반바퀴도 안돌아도 되고 멀리 있으면 최대 한바퀴를 도므로, 그 평균인 반바퀴를 기준으로 4ms로 정한다.
  - 이 때, 512Byte / Sector이고 500Sector / Track이라고 가정하자.
  - 그럼 2500 Sector을 읽을 때의 Best Case와 Worst Case는?
    - Best Case : 4ms(Seek) + 4ms(Rot) + 8ms(500 Sector을 읽는 한 바퀴) + 4*(4+8)(나머지 2000sector을 읽는 4바퀴) = 64ms
    - Worst Case : 2500 * (4ms(Seek Time) + 4ms(Rot Late) + 0.016ms(1sector transfer)) = 8.016ms * 2500 = 20.04sec
      - 모든 Sector이 다 흩어져 있어서, 싹 다 뒤져봐야 하는 경우가 Worst Case.
 
## RAID(Redundancy Array of DISK)
> Redundancy Array of Independant Disks 또는, Redundancy Array of Inexpensive Disks.  
> 독립된 물리적 디스크들을 묶어서, 논리적으로 하나의 디스크 처럼 사용하는 기술이다.  

- RAID는 총 6개의 Level이 있으며, 계층 구조는 아니다.
- 6개의 Level을 어떤 구성으로 포함 시키느냐에 따라, 용량 및 성능 등이 결정된다.
  - 현재 Standard RAID Controller에서는 RAID 0, 1, 5, 6만 지원을 한다.

### RAID 0  
![image](https://user-images.githubusercontent.com/71700079/164610687-b97ce32d-a1dc-43ad-82db-0d422b921c4b.png)  

- Redundancy는 없다.
- Striping이라고도 부른다.
- 최소 2개의 Disk를 사용하며, 각 디스크에 번갈아가면서 Data를 저장한다.
  - 용량과 성능이 단일 Disk의 N배가 된다.
  - 하지만 하나의 Disk가 고장이 나면, 전체를 쓸 수가 없기 때문에, 안정성이 1/N이 된다.  

### RAID 1
![image](https://user-images.githubusercontent.com/71700079/164610906-564fead7-4ea0-4a92-b667-f2b1b506c713.png)  

- Mirroring이라고도 부른다.
- RAID 0과 동일하게 최소 2개의 Disk가 필요하다.
  - 현재 디스크의 Data를 그대로 복제해서 넣어야하기 때문이다.
  - 그대로 복제해서만 넣기 때문에, Disk가 늘어나도 사용 가능한 용량은 단일 Disk와 동일하다.
  - Write를 할 시에, 복제까지 해야하기 때문에 단일 Disk보다 성능이 안 나올 수도 있다.
  - Read시에는, 전체 Disk에서 읽어오기 때문에 성능이 N배가 된다.
- 복제를 해 놓았기 때문에, 안정성이 매우 높다! 
  - 하지만 비용이 배우 비싸다.

### RAID 2
![image](https://user-images.githubusercontent.com/71700079/164611272-158554a2-cff4-4235-a2a7-0345e985098d.png)  

- 개념적으로만 존재하며, 현실적으로 실현이 불가능한 RAID Level이다.
- M+1개의 Data Disk와 M개의 Parity Disk로 구성이 된다.
  - M개의 Parity Disk에는 Hamming Error Correction Code를 넣어서, 오류가 발생한 Disk를 고칠 수 있도록 한다.
  - 2개 이상의 Disk가 고장나는 경우에는 고칠 수 없다.

### RAID 3
![image](https://user-images.githubusercontent.com/71700079/164611605-5c06a5a2-d8ee-45bd-9cd7-5da62532ddb6.png)  

- RAID 2와 유사하며, 현재에는 쓰이지 않는 Level이다.
- Hamming Code를 넣는 것이 아닌, 하나의 추가 Parity Disk만 사용한다.
  - 따라서 성능은 N-1배가 된다.
- Byte 단위로 Striping을 하기 때문에, 너무 잘게 쪼개어 현재는 쓰이지 않는다고 한다.

### RAID 4
![image](https://user-images.githubusercontent.com/71700079/164611629-bd09aa9c-285f-42cf-b459-a759905ce210.png)  

- 현재는 쓰이지 않는 Level이다.
- RAID 3과 동일하게 하나의 Parity Disk만 사용하지만, Block 단위로 Striping한다.
- Parity Disk의 의존도가 높아서 잘 쓰지 않는다고 한다.
  - Disk 4개가 있으면, 읽을 때 마다 매번 Parity Disk도 읽어주어야함 ==> 수명 단축 초래

### RAID 5
![image](https://user-images.githubusercontent.com/71700079/164611644-429352ef-bf02-411b-857a-3230068bc779.png)  

- RAID 4를 개선시킨 방식이 RAID 5이다.
- 이 방식을 제일 많이 사용한다고 한다.
- Parity Disk를 따로 두는 것이 아닌, 사용중인 Disk에 Random하게 Parity bit를 하나씩 삽입한다.
  - 1개의 오류가 발생하면 수정이 가능, 2개 이상의 오류는 수정할 수 없다.
  - RAID 4의 신뢰성 문제가 해결이 가능해지며, 성능 또한 Disk 수에 따라 증가한다.
- RAID 0에 비해서 성능, 용량은 모자라지만 안정성을 확보한 방식이다.

### RAID 6
![image](https://user-images.githubusercontent.com/71700079/164611668-9e1fc299-65c9-472a-a39f-b133eb178b83.png)  

- RAID 5에서 성능, 용량을 좀 더 줄이고 안정성을 더 높인 방식이다.
- Error Correction을 위해서 2개의 bit를 하나의 Disk에 저장하는 방식인데.
  - RAID 5와 동일하게 저장되는 Disk가 고정되어 있지 않다.
  - RAID 5는 1개의 bit였지만, RAID 6은 2개의 bit.
    - 이러면, 용량 및 성능은 N-2배가 되기 때문에 줄은 거지만, 안정성이 높아졌다!
  - 2개의 Error 까지 수정 가능. 3개부터는 불가능하다.
- 안정성이 필요한 서버에 사용이 된다.

## RAID 0 + 1
![image](https://user-images.githubusercontent.com/71700079/164612738-61425e41-1348-4df3-bad7-b10eeacb5bef.png)  

- RAID 0의 Striping과 RAID 1의 Mirroring을 합친 방식이다.
  - Mirroring을 하는데, Striping 방식으로 Mirroring을 한다.

## SSD(Solid State Drive) vs HDD
- HDD보다 훨씬 빠른 속도 (높은 I/O Operation per Second)
- 수명(Durability)이 HDD가 더 짧다. (거의 절반 이하) 
- 배터리가 장착된 RAM과 함께 SSD가 나왔었는데, 시간이 지나며 NVRAM이 등장해 사장되었다.
- 기계적인 진동이나 충격에 강하다.
- 저전력으로 구동이 가능하다.
