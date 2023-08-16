# Environment Setting

개발 환경에 대한 설명

## Board
>- 구매 보드 : NUCLEO- stm32H7A3ZI-Q
>- 사용 이유 : 핀이 가장 많은 MCU나 고성능 MCU를 탑재해서 가장 많은 I/O와 성능등을 제공하는 제품
>- canfd통신 및 여러 기능을 추가하기 위해선 핀이 가장 많은 144개를 선택
>- 보드를 고르는 단계여서 가격과 성능을 동시에 잡을 수 있는 h7a3zi를 선택
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/a34e1f73-9a98-44f3-80a1-4ded33165c34)
>- 구매 보드 : 라즈베리파이 4B
>- 사용 이유 : 파이썬 및 리눅스 환경에 적응 할 수 있고 카메라 등 실시간으로 데이터를 사용하기에 성능이 뛰어나서 선택
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/39f7715d-8559-43f6-89a4-96eaed895c99)
>- 구매보드 : STM32F411
>- 사용 이유 : 기존 STM보드와 호환성과 작고 배치하기 용이해서 선택
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/04c33751-9119-45b0-8f94-0360321c996d" width="300" height="300"/>

## Networking
### Can Networking
>- 구매 모듈 : SN65HVD230
>- 사용 이유 : stm 보드에 자체적으로 Can Tranceiver가 내장되어있지않아서 사용
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/16df0e02-51f9-4daf-b23b-9dd956ff949f)
>- 구매 모듈 : mcp2515
>- 사용 이유 : 라즈베리파이에서 CAN통신을 이용해 데이터를 전송하기 위해 사용
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/e480288e-cb16-491f-884e-e56887d7018b)

### RF Networking
>- 구매 모듈 : nRF24L01
>- 사용 이유 : 보드간 무선으로 rf통신을 하기 위해 사용
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/be361729-9dd6-4c46-9e30-8040d94f305a" width="300" height="300"/>
>- 장점 : 통신거리가 길고 안정적임
>- 단점 : 내구성이 약함

## Sensor
>- 구매 모듈 : HC-SR04
>- 사용 이유 : 측방 및 후방 거리에 따라 부저에서 신호를 내기 위해 사용
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/7f8d8425-4d06-49fc-87b4-7475d89f8ab4" width="300" height="200"/>
>- 단점 : 가격이 저렴해서 초음파 값이 갑자기 튀는 현상이 있음
>-
>- 구매 모듈 : TF-Luna LiDAR
>- 사용 이유 : 자동차 전방 거리에 따라 모터를 정지시키기 위해 사용
>- <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/718e6018-aab3-4ccd-ab8d-d5c3369b12f3" width="300" height="300"/>
>- 장점 : 거리값이 안정적으로 출력됨
>
>- 구매 모듈 : SEN030101
>- 사용 이유 : 조도 값에 따라 자동차 라이트의 세기를 변경하기 위해 사용
>- ![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/04fa8599-2ae5-4778-ac45-be69599fd06f)


