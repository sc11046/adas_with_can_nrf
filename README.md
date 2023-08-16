# Smart Communication in ADAS-Equipped Electric Vehicle with CAN and NRF


-  [소개](#1introduction)
-  [개요](#2overview)
-  [요구사항](#3Requirements)
-  [파일 설명](#4File-Description)
-  [기능](#5Contents)

## 1.Introduction 
이 Repository 2023년도 1학기에 capstone 수업의 대한 졸업작품입니다.
우리는 전동자동차에 adas시스템에 들어가는 **실제 차량에 들어가는 센서**와 **유,무선 통신**을 구현해보았습니다.
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
>

## 2.Overview
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/2adb81d7-0ad7-4dd9-9e30-1acd12218d63)
프로젝트에는 크게 4가지로 구성되어있습니다.

1) stm32f411송신부에서 **핸들** 및 엑셀레이터의 저항값을 rf통신을 이용해 **원격으로 수신부로 전송**합니다.
2) 라즈베리파이의 카메라를 연결해 **차선인식의 변화값**을 실시간으로 **can통신을 사용해 수신부로 전송**합니다.
3) stm32h7a3zi(2,3)에서 각 센서의 값들을 **canfd통신**으로 **수신부로 전송**합니다.
4) stm32h7a3zi(1) 즉 수신부에서 여러가지 **can통신에 걸려있는 값들을 필터링에 필요한 값만 출력**해서 사용합니다.

**자세한 내용은 Contents를 통해 볼 수 있습니다.**

## 3.Requirements

본 프로젝트를 위해서는 다음과 같은 것들이 필요합니다.

1) **can통신**이 가능한 mcu보드
2) **센서류** : 조도,초음파,라이다센서
3) **nrf통신모듈**
4) **차선인식**을 위한 카메라

## 4.File Description
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/63862075-ed62-4683-b8c8-1eb82d0f53ff)

1) STM32F411_TX : **엑셀레이터** 및 **핸들**의 값을 보냄
2) STM32H7A3ZI_1_RX : TX보드에서 보내는 **센서값과 저항값**을 받아 출력
3) STM32H7A3ZI_2_TX : 조도센서와 초음파값을 **CANFD통신**을 통해 RX보드로 송신
4) STM32H7A3ZI_3_TX : **라이다값**을 **CANFD통신**을 통해 RX보드로 송신
5) Raspberry Pi_TX : **차선인식값**을 **CAN통신**을 통해 RX보드로 송신

## 5.Contents
0) [개발환경](https://github.com/sc11046/adas_with_can_nrf/blob/main/EnvironmentSetting.md)
1)  
