# sensors
이번 프로젝트에서 사용한 센서들은 다음과 같다.
-  [TF-Luna LiDAR](#1introduction)
-  [HC-SR04](#2overview)
-  [SEN030101](#3Requirements)
## TF-Luna LiDAR
###  Overview

TF-Luna Lidar 센서를 이용해 전방의 장애물을 파악한 후 <br>모터를 정지시킴으로써 안전을 확보하기위해 사용했다. 

[사용한 모듈](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/EnvironmentSetting.md#sensor)

<img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/8df420d4-84a8-4380-9213-92c99ed8bbf2" width="500" height="300"/>



###  블럭도

![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/7e8cbdf0-776c-49e7-8437-fb83d36c03b1) 



### networking test
송신부<br>
<img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/16b8212a-9587-4f62-8fc1-529ca86a5a61" width="700" height="100"/>
<br>
수신부<br>
<img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/84de82f7-c670-4ec6-ade2-91480391ada0" width="700" height="100"/>
<br>

### demo
<img src="https://github.com/qkcvb110/Portfolio/assets/121782690/8e93a8c7-8ef5-458a-87c5-c8af9d7982b0" width="500" height="300"/>
<br>


## HC-SR04

### Overview

차량 후 측방에 초음파 센서를 부착시켜 3면의 거리를 체크해 <br>장애물이 있을 시 부저를 울리도록 사용했다.

[사용한 모듈](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/EnvironmentSetting.md#sensor)

<img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/d4a052f5-c18a-40e6-8d86-595c63562a7c" width="500" height="300"/>

### 블럭도
<img width="100%" img src="https://github.com/crasdok/capstone/assets/118472691/2afaa613-30f2-49bb-ad56-e9f89311bec4">

### networking test
송신부<br>
<img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/b6d26867-c123-4270-8fd3-ac7737b5b0c9" width="700" height="100"/>

수신부<br>
<img src="https://github.com/crasdok/capstone/assets/118472691/6d249b61-b789-4fc5-918b-05c1babc87f1" width="700" height="100"/>
### demo
<img src="https://github.com/qkcvb110/Portfolio/assets/121782690/95b63d8f-ebf4-4e7e-8e17-37cd82e69248" width="500" height="300"/> <br>
## SEN030101

### Overview

실제 차량에 들어가는 조도센서와 같이 빛의 세기의 따라 전조등이 켜지도록 만들었다.<br>canfd통신을 통해 조도센서의 값을 수신부로 보내주고 수신부에서 전조등이 켜진다

[사용한 모듈](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/EnvironmentSetting.md#sensor)

<img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/71ec1a47-9ec3-4fb0-8336-25c0cad98bcd" width="500" height="300"/>


### networking test
송신부<br>
<img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/8da1f8d6-a241-4587-8026-d39e9d89b0f5" width="700" height="100"/>
수신부<br>
<img src="https://github.com/crasdok/capstone/assets/118472691/f340aa68-cc4f-4742-81eb-9f2f1aee943e" width="700" height="100"/>


### demo
<img src="https://github.com/crasdok/capstone/assets/118472691/c5a6ff02-2a8e-413e-a355-f17a2049f019" width="500" height="300"/>
