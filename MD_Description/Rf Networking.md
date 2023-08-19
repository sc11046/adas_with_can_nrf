# Rf Networking

## Overview

Rf Networking이란?  라디오 주파수를 사용해 네트워크를 구축하는 것

사용 배경 : 처음에는 블루투스를 이용해 사용해봤으나 통신 속도 및 통신 거리가 너무 약해 RF 통신을 사용하게 되었다

사용 이유 : 무선 네트워킹을 사용하고싶었고 안정적인 신호 전달을 위해서 사용하였다

사용 모듈 : nRF24L01

![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/a0c50c7d-8a97-40db-b810-52ee3818ed14)

## Flow Chart
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/e61b41f0-ca0e-4b92-82f7-91e78e73f6d7)
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/17cafb2a-9fba-4543-b76b-0874108ec1c0)

## NRF24_init

```
void NRF24_Init (void)
{
	// disable the chip before configuring the device
	CE_Disable();


	// reset everything
	nrf24_reset (0);
	
	nrf24_WriteReg(CONFIG, 0);  // will be configured later
	
	nrf24_WriteReg(EN_AA, 0);  // No Auto ACK
	
	nrf24_WriteReg (EN_RXADDR, 0);  // Not Enabling any data pipe right now
	
	nrf24_WriteReg (SETUP_AW, 0x03);  // 5 Bytes for the TX/RX address
	
	nrf24_WriteReg (SETUP_RETR, 0);   // No retransmission
	
	nrf24_WriteReg (RF_CH, 0);  // will be setup during Tx or RX
	
	nrf24_WriteReg (RF_SETUP, 0x0E);   // Power= 0db, data rate = 2Mbps
	
	// Enable the chip after configuring the device
	CE_Enable();

}
```

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

void nrf24_reset(uint8_t REG)
{
	if (REG == STATUS)
	{
		nrf24_WriteReg(STATUS, 0x00);
	}

	else if (REG == FIFO_STATUS)
	{
		nrf24_WriteReg(FIFO_STATUS, 0x11);
	}
	
	else {
	nrf24_WriteReg(CONFIG, 0x08);
	nrf24_WriteReg(EN_AA, 0x3F);
	nrf24_WriteReg(EN_RXADDR, 0x03);
	nrf24_WriteReg(SETUP_AW, 0x03);
	nrf24_WriteReg(SETUP_RETR, 0x03);
	nrf24_WriteReg(RF_CH, 0x02);
	nrf24_WriteReg(RF_SETUP, 0x0E);
	nrf24_WriteReg(STATUS, 0x00);
	nrf24_WriteReg(OBSERVE_TX, 0x00);
	nrf24_WriteReg(CD, 0x00);
	uint8_t rx_addr_p0_def[5] = {0xE7, 0xE7, 0xE7, 0xE7, 0xE7};
	nrf24_WriteRegMulti(RX_ADDR_P0, rx_addr_p0_def, 5);
	uint8_t rx_addr_p1_def[5] = {0xC2, 0xC2, 0xC2, 0xC2, 0xC2};
	nrf24_WriteRegMulti(RX_ADDR_P1, rx_addr_p1_def, 5);
	nrf24_WriteReg(RX_ADDR_P2, 0xC3);
	nrf24_WriteReg(RX_ADDR_P3, 0xC4);
	nrf24_WriteReg(RX_ADDR_P4, 0xC5);
	nrf24_WriteReg(RX_ADDR_P5, 0xC6);
	uint8_t tx_addr_def[5] = {0xE7, 0xE7, 0xE7, 0xE7, 0xE7};
	nrf24_WriteRegMulti(TX_ADDR, tx_addr_def, 5);
	nrf24_WriteReg(RX_PW_P0, 0);
	nrf24_WriteReg(RX_PW_P1, 0);
	nrf24_WriteReg(RX_PW_P2, 0);
	nrf24_WriteReg(RX_PW_P3, 0);
	nrf24_WriteReg(RX_PW_P4, 0);
	nrf24_WriteReg(RX_PW_P5, 0);
	nrf24_WriteReg(FIFO_STATUS, 0x11);
	nrf24_WriteReg(DYNPD, 0);
	nrf24_WriteReg(FEATURE, 0);
	}
}
