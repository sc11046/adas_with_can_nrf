# Rf Networking

## Overview

Rf Networking이란?  

라디오 주파수를 사용해 네트워크를 구축하는 것

기존에는 블루투스 통신를 이용해 사용해봤으나 통신 속도 및 통신 거리가 너무 약해 RF 통신로 변경하였다.

무선 네트워킹을 이용해 안정적인 신호 전달을 위해서 사용하였다

사용 모듈 : nRF24L01

![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/a0c50c7d-8a97-40db-b810-52ee3818ed14)

## Flow Chart
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/e61b41f0-ca0e-4b92-82f7-91e78e73f6d7)
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/17cafb2a-9fba-4543-b76b-0874108ec1c0)

## NRF24_init

```c
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
```c
uint8_t TxAddress[] = {0xEE,0xDD,0xCC,0xBB,0xAA};
```

```c
uint8_t RxAddress[] = {0x00,0xDD,0xCC,0xBB,0xAA};
```

위 코드는 **adrress의 주소값**을 서로 맞춰주는 코드이다

## 레지스터 및 모드 설정
 아래 코드는 각 tx모드,rx모드의 설정이다
````c
void NRF24_TxMode (uint8_t *Address, uint8_t channel)
{
	// disable the chip before configuring the device
	CE_Disable();

	nrf24_WriteReg (RF_CH, channel);  // select the channel

	nrf24_WriteRegMulti(TX_ADDR, Address, 5);  // Write the TX address


	// power up the device
	uint8_t config = nrf24_ReadReg(CONFIG);
	config = config | (1<<1);   // write 1 in the PWR_UP bit
//	config = config & (0xF2);    // write 0 in the PRIM_RX, and 1 in the PWR_UP, and all other bits are masked
	nrf24_WriteReg (CONFIG, config);

	// Enable the chip after configuring the device
	CE_Enable();
}
````
>- tx모드 : 주어진 주소와 채널을 사용하여 모듈을 설정하고 송신 기능을 활성화시킨다
```c
void NRF24_RxMode (uint8_t *Address, uint8_t channel)
{
	// disable the chip before configuring the device
	CE_Disable();

	nrf24_reset (STATUS);

	nrf24_WriteReg (RF_CH, channel);  // select the channel

	// select data pipe 2
	uint8_t en_rxaddr = nrf24_ReadReg(EN_RXADDR);
	en_rxaddr = en_rxaddr | (1<<2);
	nrf24_WriteReg (EN_RXADDR, en_rxaddr);

	/* We must write the address for Data Pipe 1, if we want to use any pipe from 2 to 5
	 * The Address from DATA Pipe 2 to Data Pipe 5 differs only in the LSB
	 * Their 4 MSB Bytes will still be same as Data Pipe 1
	 *
	 * For Eg->
	 * Pipe 1 ADDR = 0xAABBCCDD11
	 * Pipe 2 ADDR = 0xAABBCCDD22
	 * Pipe 3 ADDR = 0xAABBCCDD33
	 *
	 */
	nrf24_WriteRegMulti(RX_ADDR_P1, Address, 5);  // Write the Pipe1 address
	nrf24_WriteReg(RX_ADDR_P2, 0xEE);  // Write the Pipe2 LSB address

	nrf24_WriteReg (RX_PW_P2, 32);   // 32 bit payload size for pipe 2


	// power up the device in Rx mode
	uint8_t config = nrf24_ReadReg(CONFIG);
	config = config | (1<<1) | (1<<0);
	nrf24_WriteReg (CONFIG, config);

	// Enable the chip after configuring the device
	CE_Enable();
}
```

>- rx모드 : 레지스터의 상태를 초기화 시킨 후 채널을 설정한다
>- 이후 rx주소를 설정하고 모듈의 데이터를 파이프 2번에 수신되도록 설정한다
>- 수신 기능을 활성화시켜 수신 받을 준비를 완료한다

## fifo 버퍼 데이터 초기화 및 송,수신함수
>- **최초 1회는 fifo가 비워져 있는 상태이므로 초기화 되지않고 넘어간다**
>- 아래 코드는 버퍼데이터 초기화가 포함된 코드이다
```c
uint8_t NRF24_Transmit (uint8_t *data)
{
	uint8_t cmdtosend = 0;

	// select the device
	CS_Select();

	// payload command
	cmdtosend = W_TX_PAYLOAD;
	HAL_SPI_Transmit(NRF24_SPI, &cmdtosend, 1, 100);

	// send the payload
	HAL_SPI_Transmit(NRF24_SPI, data,32, 1000);
//	HAL_SPI_Transmit(NRF24_SPI, data, sizeof(data), 1000);
	// Unselect the device
	CS_UnSelect();

	HAL_Delay(1);

	uint8_t fifostatus = nrf24_ReadReg(FIFO_STATUS);

	// check the fourth bit of FIFO_STATUS to know if the TX fifo is empty
	if ((fifostatus&(1<<4)) && (!(fifostatus&(1<<3))))
	{
		cmdtosend = FLUSH_TX;
		nrfsendCmd(cmdtosend);

		// reset FIFO_STATUS
		nrf24_reset (FIFO_STATUS);

		return 1;
	}

	return 0;
}
```
>- NRF24_Transmit : 초기값을 0으로 설정하고 HAL_SPI_Transmit함수를 호출해 nrf모듈에 32바이트의 데이터를 송신한다
>- fifostatus를 확인해 tx fifo가 비어져있는지 확인하고 cmdtosend = FLUSH_TX로 fifo를 비운다
>- 이후 리셋을 활용해 fifo를 초기화 시키고 return 1,0을 사용해 송신이 되면 1 안되면 0을 리턴한다
```c
void NRF24_Receive (uint8_t *data)
{
	uint8_t cmdtosend = 0;

	// select the device
	CS_Select();

	// payload command
	cmdtosend = R_RX_PAYLOAD;
	HAL_SPI_Transmit(NRF24_SPI, &cmdtosend, 1, 100);

	// Receive the payload
	HAL_SPI_Receive(NRF24_SPI, data, 32, 1000);

	// Unselect the device
	CS_UnSelect();

	HAL_Delay(1);

	cmdtosend = FLUSH_RX;
	nrfsendCmd(cmdtosend);
}
```
>- NRF24_Receive : 초기값을 0으로 설정하고 cs를 활성화 시킨다
>- 수신된 페이로드 데이터를 읽고 HAL_SPI_Transmit함수를 호출한다
>- 32바이트의 데이터를 "data" 배열로 받아온다 딜레이를 넣어주어 완료를 기다리게 세팅한다
>- 이후  cmdtosend = FLUSH_TX로 fifo를 비운다

## 송신 수신 과정
```
	      if (NRF24_Transmit(TxData) == 1)
	  	  {
	  		  HAL_GPIO_TogglePin(GPIOC, GPIO_PIN_13);
	  		  HAL_Delay(100);
	  	  }
	      HAL_Delay(10);
```
>- tx : "if (NRF24_Transmit(TxData) == 1)"를 while안에 배치시켜 지속적으로 송신하게끔 설정했다
>- 송신 성공시 보드에 내장된 LED에 불을 0.1초간격으로 깜빡거리게 설정했다
>- 리턴 값을 이용해 1이 되었을 때 송신하게끔 설정했다

```
          if (isDataAvailable(2) == 1)
          {
              NRF24_Receive(RxData);
          }
```
>- rx : "if (isDataAvailable(2) == 1)
>  {NRF24_Receive(RxData)}"를 while안에 배치시켜 지속적으로 수신하게끔 설정했다
>- 파이프데이터 2번안에 데이터가 수신되었을때  NRF24_Receive(RxData)가 동작되게 설정했다

## Networking test
수신부<br>
![nrf-rx](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/b76b45c1-343f-49d7-9c34-c52e42e75971)<br>
데이터 값에 핸들과 엑셀레이터 가변저항값을 전송시켜 무선으로 통신이 되도록 만들었다<br>

## demo

![NRF영상_AdobeExpress_AdobeExpress](https://github.com/crasdok/capstone/assets/118472691/0e8da015-865e-457a-8531-79db8783a3ea)
