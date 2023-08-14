# Smart Communication in ADAS-Equipped Electric Vehicle with CAN and NRF
## 1.Introduction 
이 Repository 2023년도 1학기에 capstone 수업의 대한 졸업작품입니다.
우리는 전동자동차에 adas시스템에 들어가는 **실제 차량에 들어가는 센서**와 **유,무선 통신**을 구현해보았습니다.

## 2. Overview
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/2adb81d7-0ad7-4dd9-9e30-1acd12218d63)
프로젝트에는 크게 4가지로 구성되어있습니다.

1) stm32f411송신부에서 핸들 및 엑셀레이터의 저항값을 rf통신을 이용해 원격으로 수신부로 전송합니다.
2) 라즈베리파이의 카메라를 연결해 차선인식의 변화값을 실시간으로 can통신을 사용해 수신부로 전송합니다.
3) stm32h7a3zi(2,3)에서 각 센서의 값들을 canfd통신으로 수신부로 전송합니다.
4) stm32h7a3zi(1) 즉 수신부에서 여러가지 can통신에 걸려있는 값들을 필터링에 필요한 값만 출력해서 사용합니다.
자세한 내용은 Contents를 통해 볼 수 있습니다.
## 3. Requirements

STM3
