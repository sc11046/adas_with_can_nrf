# Can,CanFD Networking

## Overview

Can,CanFD Networking이란? <br>고속 데이터 전송과 유연성을 제공하기 위해 <br>개발된 자동차 및 산업 분야에서 사용되는 통신 프로토콜이다

실제 차량에 필수적인 통신인 Can,FDCan통신을 활용하여 <br>전동차량에 ECU간 통신을 통해 네트워킹 환경을 구축하고자 하였다.<br>

> Can 통신의 특징은 다음과 같다.
> 1. **신뢰성과 내구성**: CAN은 신뢰성 높은 통신을 제공하며, 잡음과 간섭에 강함.
> 2. **멀티노드 통신**: CAN은 멀티노드 네트워크 구성이 가능. 여러 장치나 센서들이 하나의 CAN 네트워크에 연결되어 통신할 수 있으며, 이를 통해 다양한 시스템을 효율적으로 관리 가능.
> 3. **실시간 통신**: CAN은 실시간 통신을 지원하여 실시간 제어와 데이터 전송이 필요한 시스템에 적합 자동차에서는 ABS 나 엔진 제어와 같은 시스템에서 실시간 데이터 통신이 중요.
> 4. **높은 효율성**: CAN 프로토콜은 통신에 사용되는 데이터 양을 최소화하여 효율적인 통신이 가능.
> 5. **저전력 소모**: CAN은 저전력 통신을 지원하므로, 자동차나 배터리 기반의 장치에서 효율적으로 사용 가능.

![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/ad48d701-9126-4824-8d04-85c47254781d)<br>
위 그림과 같이 여러 ECU끼리 통신이 **원하는 신호만 필터링 가능하다**는 점이 가장 큰 장점이다<br>
또한 우리는 MCU에 CAN Transceiver와 CAN Bus Module를 연결시켜 Can bus형식를 만들었다.<br>

사용 모듈 : **CAN Transceiver,CAN Bus Module**<br>
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/e207109e-1e66-4549-8c14-1447e6517dea)

## Flow Chart
