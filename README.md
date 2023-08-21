# Smart Communication in ADAS-Equipped Electric Vehicle with CAN and NRF



-  [개요](#1overview)
-  [소개](#2introduction)
-  [구상도](#3구상도)
-  [요구사항](#4requirements)
-  [파일 설명](#5file-description)
-  [기능](#6contents)


## 1.Overview
이 Repository 2023년도 1학기에 capstone 수업의 대한 졸업작품입니다. 
<br>
본 프로젝트는 실제 자동차에 쓰이는 필수적인 **Can,CanFD,RF통신**과 **센서,차선인식**을 <br>
전동자동차에 활용하여 효과적으로 쓰이기 위한 방법을 제시하기 위한 목적으로 개발하였습니다. <br>
<br>
여러가지 ECU의 역할을 해주는 MCU에 센서를 활용해 유선으로 CANFD통신을 하였고 <br>
카메라부터 취득한 데이터를 인식하고,이를 기반으로 차선의 상태를 CAN통신으로 명령을 보내는 제어 프로그램을 구축했습니다.<br>
또한 RF모듈을 사용해 MCU간 무선통신을 구축했습니다.
CanFD통신과 Can통신을 나눠서 사용한 이유는 8byte이상의 데이터를 보내기 위해 센서쪽에는 CanFD를 사용했고
영상처리에는 8byte이상 필요하지않아 Can통신을 사용했습니다.
<br>
## 2.Introduction 
>
**프로젝트:** Smart Communication in ADAS-Equipped Electric Vehicle with CAN and NRF
>
**기획 및 제작:** **오승찬**,강민성,최원진
>
**팀 내 역할:** **CANFD통신,RF통신,모터제어**
>
**제작 기간:** 2023.01 ~ 2023.08
>
**주요 기능:** CANFD통신,RF통신,차선인식,모터,라이다,조도,초음파
>
**사용 언어:** C,python

## 3.구상도

 ![KakaoTalk_20230819_182954222](https://github.com/qkcvb110/Portfolio/assets/121782690/12019a38-dc8a-4656-ba84-139fd4a13c36)

 <img src="https://github.com/qkcvb110/Portfolio/assets/121782690/b1a28858-fab1-49f8-bfe5-f44c19cbf8f6" width="800" height="350"/> 


프로젝트에는 크게 4가지로 구성되어있습니다.
1) stm32f411송신부에서 **핸들** 및 엑셀레이터의 저항값을 rf통신을 이용해 **원격으로 수신부로 전송**합니다.
2) 라즈베리파이의 카메라를 연결해 **차선인식의 변화값**을 실시간으로 **can통신을 사용해 수신부로 전송**합니다.
3) stm32h7a3zi(2,3)에서 각 센서의 값들을 **canfd통신**으로 **수신부로 전송**합니다.
4) stm32h7a3zi(1) 즉 수신부에서 여러가지 **can통신에 걸려있는 값들을 필터링에 필요한 값만 출력**해서 사용합니다.

**자세한 내용은 Contents를 통해 볼 수 있습니다.**

## 4.Requirements

본 프로젝트를 위해서는 다음과 같은 것들이 필요합니다.

1) **can통신**이 가능한 mcu보드
2) **센서류** : 조도,초음파,라이다센서
3) **nrf통신모듈**
4) **차선인식**을 위한 카메라

## 5.File Description
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/63862075-ed62-4683-b8c8-1eb82d0f53ff)

1) STM32F411_TX : **엑셀레이터** 및 **핸들**의 값을 보냄
2) STM32H7A3ZI_1_RX : 송신부에서 보내는 **센서값과 저항값**을 받아 출력
3) STM32H7A3ZI_2_TX : 조도센서와 초음파값을 **CANFD통신**을 통해 RX보드로 송신
4) STM32H7A3ZI_3_TX : **라이다값**을 **CANFD통신**을 통해 RX보드로 송신
5) Raspberry Pi_TX : **차선인식값**을 **CAN통신**을 통해 RX보드로 송신

## 6.Contents
0) [개발환경](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/EnvironmentSetting.md)
1) [Sensors](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/Sensors.md)
2) [Rf Networking](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/Rf%20Networking.md)
3) [Can Networking](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/Can%20Networking.md)
4) [Motor Control](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/Motor%20Control.md)
5) [[Lane Keeping Assist]





