# WSN Power Consumption

WSN을 구성하는 노드들은 대부분 배터리를 통해 에너지를 공급받기 때문에 사용할 수 있는 에너지가 한정되어 있다.

에너지를 모두 소비한 노드들은 더이상 사용하지 못하게 되는데, 만약 하나의 노드가 죽어버리면 이후에 그 이웃 노드들은 더 멀리 데이터를 보내게 되므로 금방 에너지를 소비하게 되서 빠르게 죽어버린다. 즉, **노드들의 에너지 소비를 균등하게 관리하는 것이 WSN 전체의 Lifetime에 큰 영향**을 미친다.

따라서 노드들의 에너지 소비를 최대한 효율적으로 줄이는 것이 굉장히 중요하다.

## 노드가 하는 일

WSN을 구성하는 센서 노드는 다음과 같은 3가지 작업을 하며 에너지를 소비한다.

- Data Originator: 데이터 수집(sensing)
- Data Router: 다른 노드의 데이터 수신 및 전달(communication)
- Data Processor: CPU, RAM 등 사용해서 데이터 수집 및 통신에 필요한 연산 수행(computation)

각 작업들에 평균적으로 사용되는 에너지 양 차이를 나타낸 그래프를 살펴보자.

<img src="https://i.imgur.com/NtmY6Jp.png" style="zoom:67%;" />

- 데이터 수집(sensor)과 연산(MCU)에 사용되는 에너지보다 radio를 통해 데이터를 보내고(TX) 받는데(RX) 사용되는 에너지가 훨씬 많다.
- 데이터를 보내는 거리가 길어질수록 저 차이는 더 커진다.
- Idle은 현재 radio 상에서 통신되고 있는 데이터 있는지 계속 체크하고 있는 상태이고, sleep은 노드가 아무 작업도 안하고 있는 상태이다. 이때에도 적은 양이지만 에너지가 계속 소비된다.

결론적으로 **데이터 통신에 소비되는 에너지를 절약**하는 것이 노드 및 네트워크 수명에 있어서 굉장히 중요하고, 이와 관련된 프로토콜이 많이 연구된다.

## Sensing 관련 에너지 소비

- 센싱 주기가 sporadic vs constant -> Application과 관련
- Detection 복잡도나 주변 상황 등에 의해 사용 에너지 결정

<img src="https://i.imgur.com/ZdBlIML.png" style="zoom:67%;" />

센서가 소비하는 에너지(P)는 센싱 주기(frequency)에 비례한다.

> ENOB는 detection complexibility와 ambient noise levels에 관련

결론은 센서가 소비하는 에너지는 Application에 영향을 받게 되는데, 이는 컨트롤할 수 있는게 별로 없음. 정해져있는 거니까.

## Computation 관련 에너지 소비

<img src="https://i.imgur.com/TRFSRw3.png" style="zoom:50%;" />

- 프로세서가 소비하는 에너지는 두가지로 나눌 수 있다.

  1. computation에 소비하는 에너지 (dutycycle)
  2. energy leakage (sleep mode)

- CPU가 할일이 많은 데스크탑 같은 경우 duty cycle이 거의 99%이므로, 1번에서 줄일 수 있는 voltage를 줄이는 게 의미가 있다. 하지만 센서 노드 같은 경우 duty cycle이 거의 50% 밖에 안되고 이런 경우 사실상 1번을 줄이기 위한 노력이 의미가 크게 없다.

- communication에 비해 소비하는 에너지가 적다.
- Local data processing: 2개의 노드에서 데이터를 받았을 때, 이 데이터들을 비교하고 중복이면 하나만 보내는 기능 -> 이게 computation 오버헤드가 있음에도 불구하고 반드시 필요하다. 왜? -> 데이터를 전송하는 데 소비되는 에너지가 훨씬 더 크기 때문에. 최대한 에너지를 아끼기 위해서는 보내는 데이터를 줄여야 하기 때문!

## Communication 관련 에너지 소비

- 거리가 짧으면 tx = rx 거의 이렇지만, 거리 길어질수록 tx에 소비하는 에너지가 점점 더 커진다.
- TX는 active 상태와 start-up(sleep->active) 상태에서 에너지 소비 필요
- 데이터 통신을 위한 에너지 = [보낼때 필요한 에너지] + [받을때 필요한 에너지]
  - [[start-up에 필요한 에너지], [tx와 rx를 전환하는데 필요한 에너지] 포함
- Start-up 에 필요한 에너지는 오버헤드. 낭비 -> wasted energy
- 한번 active 되었을 때 데이터를 많이 보내는게 좋아. -> 너무 자주 on/off 전환하면 안좋아.
- energy vs packet size

## 결론

노드는 데이터를 수집하고, 연산을 수행하고, 데이터 통신을 담당한다.

- Sensing에 소비되는 에너지는 해당 WSN의 Application에 직접적으로 영향받기 때문에 거의 컨트롤 할 수 없다.
- Computation에 소비되는 에너지는 DVS 등을 통해 적은 voltage를 사용하여 절약할 수 있으나, duty cycle이 낮은 센서 노드 특성상 이런 절약이 큰 의미는 없다.

- Communication에 노드의 에너지가 가장 많이 소모가 되므로, 이를 절약하는 것이 중요하다.

  - E = `start-up에 사용되는 E` + `rx-tx간 전환하는 데 사용되는 E` + `rx에 사용되는 E` + `tx에 사용되는 E`

  - E(st), E(sw)은 많을수록 오버헤드다. 이는 절대적인 양을 줄일 수는 없으나, on/off 빈도수를 낮추어서 여기에 사용되는 에너지보다 active 상태에서 통신을 위해 사용되는 에너지가 더 많도록 해야 한다.
  - E(tx)는 데이터 증폭에 필요한 에너지와 비례하고, 이는 데이터 전송 거리와 전송 속도에 비례한다. 즉 더 멀리, 더 빠르게 데이터를 전송할수록 에너지를 많이 소비한다.

- 에너지 절약하기 위한 방법

  - 작업을 하고 있지 않는 노드는 에너지를 거의 안쓰는 단계인 sleep mode에 진입한다.

  - sleep 모드 진입을 통해 절약한 에너지가 모드 전환 과정인 start-up에 사용되는 에너지보다 많아야 한다. (그렇지 않으면 overhead)

  - 수신 및 송신에 사용되는 에너지를 줄이기 위해 저속으로 통신하고, multi-hop 기법 등을 통해 전송 거리를 줄인다.

  - 통신에 사용되는 에너지를 최대한 아끼기 위한 local data(in-network) processing 역시 중요하다. 중복 데이터를 제거하고, 데이터 크기를 압축하는 등 여러 기법을 통해 최대한 적은 데이터를 전송하도록 한다.

  - 특정 이웃 노드에게만 계속 데이터 전송하면, 해당 노드가 에너지 소비를 많이 하게 되어 죽을 확률이 높아진다. 따라서 랜덤으로 노드를 뽑거나 에너지를 가장 많이 보유하고 있는 노드에게 데이터를 보낸다.
