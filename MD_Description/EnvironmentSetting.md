# Environment Setting

개발 환경에 대한 설명

## Board
>- 사용 보드 : NUCLEO- stm32H7A3ZI-Q
>- 사용 이유 : 핀이 가장 많은 MCU나 고성능 MCU를 탑재해서 가장 많은 I/O와 성능등을 제공하는 제품
>- canfd통신 및 여러 기능을 추가하기 위해선 핀이 가장 많은 144개를 선택
>- 보드를 고르는 단계여서 가격과 성능을 동시에 잡을 수 있는 h7a3zi를 선택
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/a34e1f73-9a98-44f3-80a1-4ded33165c34)

>- 사용 보드 : 라즈베리파이 4B
>- 사용 이유 : 파이썬 및 리눅스 환경에 적응 할 수 있고 카메라 등 실시간으로 데이터를 사용하기에 성능이 뛰어나서 선택
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/7d8e896f-8d29-4b06-b062-2e476e9259bf" width="300" height="200"/>

>- 사용 보드 : STM32F411
>- 사용 이유 : 기존 STM보드와 호환성과 작고 배치하기 용이해서 선택
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/04c33751-9119-45b0-8f94-0360321c996d" width="200" height="200"/>

## Networking
### Can Networking
>- 사용 모듈 : SN65HVD230
>- 사용 이유 : stm 보드에 자체적으로 Can Tranceiver가 내장되어있지않아서 사용
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/16df0e02-51f9-4daf-b23b-9dd956ff949f)
>- 사용 모듈 : mcp2515
>- 사용 이유 : 라즈베리파이에서 CAN통신을 이용해 데이터를 전송하기 위해 사용
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/e480288e-cb16-491f-884e-e56887d7018b)

### RF Networking
>- 사용 모듈 : nRF24L01
>- 사용 이유 : 보드간 무선으로 rf통신을 하기 위해 사용
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/be361729-9dd6-4c46-9e30-8040d94f305a" width="200" height="200"/>
>- 장점 : 통신거리가 길고 안정적임
>- 단점 : 내구성이 약함

## Sensor
>- 사용 모듈 : HC-SR04
>- 사용 이유 : 측방 및 후방 거리에 따라 부저에서 신호 발생
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/7f8d8425-4d06-49fc-87b4-7475d89f8ab4" width="200" height="150"/>
>- 단점 : 가격이 저렴해서 초음파 값이 갑자기 튀는 현상이 있음

>- 사용 모듈 : TF-Luna LiDAR
>- 사용 이유 : 자동차 전방 거리에 따라 모터를 정지
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/718e6018-aab3-4ccd-ab8d-d5c3369b12f3" width="200" height="200"/>
>- 장점 : 거리값이 안정적으로 출력됨

>- 사용 모듈 : SEN030101
>- 사용 이유 : 조도 값에 따라 자동차 라이트의 세기 변경
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/04fa8599-2ae5-4778-ac45-be69599fd06f)

## Motor
### Motor
>- 사용 모터 : 12v 듀얼 모터(뒷바퀴),12v dc 모터(앞바퀴)
>- 사용 이유 : 뒷바퀴와 앞바퀴에 각각 다른 기어박스를 사용해 방향 전환 및 속도 제어
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/04010abf-20a1-4bcb-b626-02bbb5db957e" width="200" height="200"/>

### Motor Driver
>- 사용 모듈 : W314 DC 모터 드라이버
>- 사용 이유 : 뒷바퀴 속도 제어
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/4c1586dc-6f46-4f77-86f6-e48eb7534d3e" width="200" height="200"/>

>- 사용 모듈 : MD10C DC 모터 드라이버
>- 사용 이유 : 앞바퀴 방향 제어
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/8247a37c-755c-41f8-97a3-24b1b04ce0c8" width="200" height="200"/>

### bettery
>- 사용 배터리 : 12v 9ah 납배터리,12v 7ah 납배터리
>- 사용 이유 : 뒷바퀴에는 배터리소모가 크기때문에 9ah 사용, 앞바퀴에는 7ah 사용
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/653243bc-5d9e-4333-aaa3-8a1eed251d09" width="200" height="200"/>
### controler
>- 사용 부품 : 가변저항, 플라스틱 핸들, 나무 판자, 스위치<br>
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/0a884fa1-ae42-409f-87b6-922bd6c4e2c7)
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/29577ffa-f6ba-45b6-96b5-4b10a9b8fc22)


