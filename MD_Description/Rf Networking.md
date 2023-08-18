# Rf networking
## Overview
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/4bae7996-b104-43c5-9c62-4b32cce08fd0)
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/17cafb2a-9fba-4543-b76b-0874108ec1c0)

## NRF24_init
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/d2a28c50-753c-4cd9-ae07-682b0b0665c2)

>- 위 코드는 nrf모듈을 초기화를 시킨후 tx/rx address,데이터크기,ack모드,데이터 속도 등 레지스터 값을 설정하는 코드 이다
>- 5byes의 데이터 크기와 2Mbps로 속도를 설정했다

## 데이터 주소값 설정
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/17d38788-42e7-4767-b12b-b93272dd2c09)

위 코드는 **adrress의 주소값**을 서로 맞춰주는 코드이다

## 레지스터 및 모드 설정
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/58cd5544-a15a-427b-b75f-3a605dc6d380)

>- 위 코드는 각 tx모드,rx모드의 설정이다
>- tx모드 : 주어진 주소와 채널을 사용하여 모듈을 설정하고 송신 기능을 활성화시킨다

>- rx모드 : 레지스터의 상태를 초기화 시킨 후 채널을 설정한다
>- 이후 rx주소를 설정하고 모듈의 데이터를 파이프 2번에 수신되도록 설정한다
>- 수신 기능을 활성화시켜 수신 받을 준비를 완료한다

## fifo 버퍼 데이터 초기화 및 송,수신함수
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/f48f02ea-040f-418e-b860-ae3e2a0a170c)

>- **최초 1회는 fifo가 비워져 있는 상태이므로 초기화 되지않고 넘어간다**
 
>- 위 코드는 버퍼데이터 초기화가 포함된 코드이다
>- NRF24_Transmit : 초기값을 0으로 설정하고 HAL_SPI_Transmit함수를 호출해 nrf모듈에 32바이트의 데이터를 송신한다
>- fifostatus를 확인해 tx fifo가 비어져있는지 확인하고 cmdtosend = FLUSH_TX로 fifo를 비운다
>- 이후 리셋을 활용해 fifo를 초기화 시키고 return 1,0을 사용해 송신이 되면 1 안되면 0을 리턴한다

>- NRF24_Receive : 초기값을 0으로 설정하고 cs를 활성화 시킨다
>- 수신된 페이로드 데이터를 읽고 HAL_SPI_Transmit함수를 호출한다
>- 32바이트의 데이터를 "data" 배열로 받아온다 딜레이를 넣어주어 완료를 기다리게 세팅한다
>- 이후  cmdtosend = FLUSH_TX로 fifo를 비운다

## 송신 수신 과정
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/607fcdb7-3bbd-4dff-ab2c-dd96da9688cf)

>- tx : "if (NRF24_Transmit(TxData) == 1)"를 while안에 배치시켜 지속적으로 송신하게끔 설정했다
>- 리턴 값을 이용해 1이 되었을 때 송신하게끔 설정했다

>- rx : "if (isDataAvailable(2) == 1)
>  {NRF24_Receive(RxData)}"를 while안에 배치시켜 지속적으로 수신하게끔 설정했다
>- 파이프데이터 2번안에 데이터가 수신되었을때  NRF24_Receive(RxData)가 동작되게 설정했다

## 테스트 영상
![NRF영상_AdobeExpress_AdobeExpress](https://github.com/crasdok/capstone/assets/118472691/0e8da015-865e-457a-8531-79db8783a3ea)

>- 데이터 값에 핸들과 엑셀레이터 가변저항값을 전송시켜 무선으로 통신이 되도록 만들었다
