# Can,CanFD Networking

## Overview

Can,CanFD Networking이란? <br>고속 데이터 전송과 유연성을 제공하기 위해 <br>개발된 자동차 및 산업 분야에서 사용되는 통신 프로토콜이다

실제 차량에 필수적인 통신인 Can,CanFD통신을 활용하여 <br>전동차량에 ECU간 통신을 통해 네트워킹 환경을 구축하고자 하였다.<br>

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
![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/1b9c5a67-1c2b-41f7-a7f8-b4ad552efe6c)

## Initialize

 <img src="https://github.com/sc11046/adas_with_can_nrf/assets/121782720/9fe35461-5be0-49fa-aa2e-3745aa3d7e78" width="700" height="350"/>

![image](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/236bd01b-e379-4ee9-8bcf-a11536222b97)

모든 ECU들을 통신하기 위해서는 각 ECU들의 클럭 속도를 맞추는게 중요하다<br>계산방법은 아래와같다.<br>

APB1 clock = 4MHz<br>Prescaler = 1<br>4MHz / 1 = 4MHz8MHz → 1초에 4000000 bit가 뛴다.<br>제일 작은 단위. 한 clock의 시간을 계산하려면 역으로 뒤집으면 된다.<br>1타임 퀀텀의 시간 단위 = 1 / 4MHz =0.25us<br>SYNC_SEG → 1 타임 퀀텀 사용 (고정)<br>BIT SEGMENT 1 (BS1) → 5 타임 퀀텀 배정<br>BIT SEGMENT 2 (BS2) → 2 타임 퀀텀 배정<br>SYNC_SEG + BS1 + BS2 = 총 8개 타임 퀀텀. 1 타임 퀀텀 당 0.25us<br>total quantum → 0.25us * 8 = 2us (1 clock (bit) 뛰는데 들어가는 시간)<br>해당 값의 (2us) 속도를 계산하려면 뒤집어야 한다.<br>따라서 **1 bit 당 2us 소요** (500kbps → 초당 500k bit)<br>SAMPLE POINT*= (SYNC_SEG + BS1) / 전체 타임 퀀텀 = (1 + 5) / 8 =75% <br>BaudRate (통신속도) = **500kbps**<br>

```c
  static void MX_FDCAN1_Init(void){
  hfdcan1.Instance = FDCAN1;
  hfdcan1.Init.FrameFormat = FDCAN_FRAME_FD_NO_BRS;
  hfdcan1.Init.Mode = FDCAN_MODE_NORMAL;
  hfdcan1.Init.AutoRetransmission = ENABLE;
  hfdcan1.Init.TransmitPause = DISABLE;
  hfdcan1.Init.ProtocolException = DISABLE;
  hfdcan1.Init.NominalPrescaler = 1;
  hfdcan1.Init.NominalSyncJumpWidth = 1;
  hfdcan1.Init.NominalTimeSeg1 = 5;
  hfdcan1.Init.NominalTimeSeg2 = 2;
  if (HAL_FDCAN_Init(&hfdcan1) != HAL_OK)
  {
    Error_Handler();
  }
  }
```

위의 코드는 stm보드의 공통된 통신속도를 위한 설정이다

```python
sudo ip link set can0 type can bitrate 4000000
sudo ip link set up can0
```

위의 코드는 라즈베리파이의 통신속도를 맞추기 위한 설정이다

## 데이터 및 헤더ID, 필터 설정 

센서들의 코드는 TxFifo를 사용하고 <br>FIFO 큐의 동작 모드를 사용한다<br>

### 초음파,조도센서 ECU

```c
  FDCAN_TxHeaderTypeDef TxHeader;
  uint8_t TxData_Node3_To_Node2[16];  

  hfdcan1.Init.TxFifoQueueElmtsNbr = 1;
  hfdcan1.Init.TxFifoQueueMode = FDCAN_TX_FIFO_OPERATION;
  hfdcan1.Init.TxElmtSize = FDCAN_DATA_BYTES_16;
  
  TxHeader.Identifier = 0x33;
  TxHeader.DataLength = FDCAN_DLC_BYTES_16;
```

데이터의 길이는 16bytes, 헤더ID는 0x33으로 사용한다<br>

### 라이다 센서 ECU

```
  FDCAN_TxHeaderTypeDef TxHeader;
  uint8_t TxData_Node1_To_Node3[16];
  hfdcan1.Init.TxFifoQueueElmtsNbr = 1;
  hfdcan1.Init.TxFifoQueueMode = FDCAN_TX_FIFO_OPERATION;
  hfdcan1.Init.TxElmtSize = FDCAN_DATA_BYTES_16;
  
  TxHeader.Identifier = 0x11;
  TxHeader.DataLength = FDCAN_DLC_BYTES_16;
```

데이터의 길이는 16bytes,헤더ID는 0x11으로 사용한다<br>

### 차선 인식 ECU

```python
 bus = can.Bus(interface='socketcan',
              channel='can0',
              receive_own_messages=True)
    
 message = can.Message(arbitration_id=0x44, is_extended_id=False,data=[0x52])
```

위에 코드는 헤더ID는 0x44로 사용하고 데이터는 0x52로  'R'를 전송한다

### 수신부

```c
#include "main.h"


FDCAN_FilterTypeDef sFilterConfig;
FDCAN_FilterTypeDef sFilterConfig1;
FDCAN_FilterTypeDef sFilterConfig2;
FDCAN_RxHeaderTypeDef RxHeader;

uint8_t RxData_From_Node3[16];
uint8_t RxData_From_Node1[16];
uint8_t RxData_From_Node4[8];
```



```
  hfdcan1.Init.StdFiltersNbr = 3;
  hfdcan1.Init.RxFifo0ElmtsNbr = 1;
  hfdcan1.Init.RxFifo0ElmtSize = FDCAN_DATA_BYTES_16;
  hfdcan1.Init.RxFifo1ElmtsNbr = 1;
  hfdcan1.Init.RxFifo1ElmtSize = FDCAN_DATA_BYTES_16;
  hfdcan1.Init.RxBuffersNbr = 1;
  hfdcan1.Init.RxBufferSize = FDCAN_DATA_BYTES_16;
  
  sFilterConfig.IdType = FDCAN_STANDARD_ID;
  sFilterConfig.FilterIndex = 1;
  sFilterConfig.RxBufferIndex = 1;
  sFilterConfig.FilterType = FDCAN_FILTER_DUAL; // Ignore because FDCAN_FILTER_TO_RXBUFFE
  sFilterConfig.FilterConfig = FDCAN_FILTER_TO_RXFIFO0;
  sFilterConfig.FilterID1 = 0x33; // ID Node2
  sFilterConfig.FilterID2 = 0x7ff; // Ignore because FDCAN_FILTER_TO_RXBUFFER
      if(HAL_FDCAN_ConfigFilter(&hfdcan1, &sFilterConfig) != HAL_OK)
            {
              Error_Handler();
            }
        
  sFilterConfig1.IdType = FDCAN_STANDARD_ID;
  sFilterConfig1.FilterIndex = 2;
  sFilterConfig1.RxBufferIndex = 2;
  sFilterConfig1.FilterType = FDCAN_FILTER_DUAL; // Ignore because FDCAN_FILTER_TO_RXBUFFE
  sFilterConfig1.FilterConfig = FDCAN_FILTER_TO_RXFIFO1;
  sFilterConfig1.FilterID1 = 0x11; // ID Node2
  sFilterConfig1.FilterID2 = 0x7ff; // Ignore because FDCAN_FILTER_TO_RXBUFFER
     if(HAL_FDCAN_ConfigFilter(&hfdcan1, &sFilterConfig1) != HAL_OK)
       	 {
           Error_Handler();
       	 }
  sFilterConfig2.IdType = FDCAN_STANDARD_ID;
  sFilterConfig2.FilterIndex = 0;
  sFilterConfig2.RxBufferIndex = 0;
  sFilterConfig2.FilterType = FDCAN_FILTER_DUAL; // Ignore because FDCAN_FILTER_TO_RXBUFFE
  sFilterConfig2.FilterConfig = FDCAN_FILTER_TO_RXBUFFER;
  sFilterConfig2.FilterID1 = 0x44; // ID Node2
  sFilterConfig2.FilterID2 = 0x7ff; // Ignore because FDCAN_FILTER_TO_RXBUFFER
          if(HAL_FDCAN_ConfigFilter(&hfdcan1, &sFilterConfig2) != HAL_OK)
               {
                  Error_Handler();
               }
```

위 코드는 송신부 데이터 설정 코드로 <br>송신부에선 3개의 ECU에서 데이터를 받으므로 fifi0,fifo1,buffer를 사용하도록 구성했다.<br>총 세개의 필터를 만들었고 각각 0x11,0x33,0x44로 필터링되도록 사용했다.<br>

## 데이터 송신

### 초음파,조도센서 ECU

```
	  	if (HAL_FDCAN_AddMessageToTxFifoQ(&hfdcan1, &TxHeader, TxData_Node3_To_Node2)!= HAL_OK)
            {
                 Error_Handler();
            }
```

위 코드에서 "txHeader"구조체를 통해 메시지 헤더 정보를 설정하고, "TxData_Node3_To_Node2" 배열을 통해 전송할 데이터를 준비합니다. <br>"HAL_FDCAN_AddMessageToTxFifoQ "함수를 호출하여 메시지를 송신 FIFO 큐에 추가하여 전송합니다.

### 라이다 센서 ECU

```
  	  	if (HAL_FDCAN_AddMessageToTxFifoQ(&hfdcan1, &TxHeader, TxData_Node1_To_Node3)!= HAL_OK)
  	  		{
  	  		     Error_Handler();
  	  		}
```

위 코드에서 "txHeader"구조체를 통해 메시지 헤더 정보를 설정하고, "TxData_Node1_To_Node2" 배열을 통해 전송할 데이터를 준비합니다. <br>"HAL_FDCAN_AddMessageToTxFifoQ "함수를 호출하여 메시지를 송신 FIFO 큐에 추가하여 전송합니다.

### 차선인식 ECU

```
         message = can.Message(arbitration_id=0x44, is_extended_id=False,data=[0x52])
         bus.send(message, timeout=0.2
```

위 코드는 11비트 Arbitration ID가 0x44인 메시지를 생성하고, 데이터 부분에 0x52 값을 포함하여 해당 메시지를 송신한다

## 데이터 수신

### ActivateNotification

            if (HAL_FDCAN_ActivateNotification(&hfdcan1, FDCAN_IT_RX_BUFFER_NEW_MESSAGE, 0) != HAL_OK)
              {
                /* Notification Error */
                Error_Handler();
              }
            if (HAL_FDCAN_ActivateNotification(&hfdcan1, FDCAN_IT_RX_FIFO0_NEW_MESSAGE, 0) != HAL_OK)
              {
                Error_Handler();
              }
            if (HAL_FDCAN_ActivateNotification(&hfdcan1, FDCAN_IT_RX_FIFO1_NEW_MESSAGE, 0) != HAL_OK)
              {
                Error_Handler();
              }

이 함수는 FIFO1에 새로운 메시지가 도착했을 때 인터럽트를 활성화한다.<br> FIFO0, FIFO1,Buffer에 새 메시지가 도착했을 때, 설정된 인터럽트 핸들러가 호출된다.<br>각 호출에서 `HAL_OK`가 반환되지 않을 경우, 에러 핸들러(`Error_Handler()`)가 호출된다.<br>수신 버퍼 및 FIFO에서 새 메시지가 도착했을 때 적절한 동작을 수행하기 위해 인터럽트를 활성화하고, <br>활성화 과정에서 오류가 발생하면 에러 처리를 수행하는 부분이다.<br>

### CallBack

```
void HAL_FDCAN_RxFifo0Callback(FDCAN_HandleTypeDef *hfdcan, uint32_t RxFifo0ITs)
{
   if(FDCAN1 == hfdcan->Instance)
   {
	  if((RxFifo0ITs & FDCAN_IT_RX_FIFO0_NEW_MESSAGE) != RESET)
	  {

		if (HAL_FDCAN_GetRxMessage(hfdcan, FDCAN_RX_FIFO0, &RxHeader, RxData_From_Node3) != HAL_OK)
		{
		Error_Handler();
		}

	  }
   }

 }//choumpa jodo
void HAL_FDCAN_RxFifo1Callback(FDCAN_HandleTypeDef *hfdcan, uint32_t RxFifo1ITs)
{
   if(FDCAN1 == hfdcan->Instance)
   {
	  if((RxFifo1ITs & FDCAN_IT_RX_FIFO1_NEW_MESSAGE) != RESET)
	  {
		if (HAL_FDCAN_GetRxMessage(hfdcan, FDCAN_RX_FIFO1, &RxHeader, RxData_From_Node1) != HAL_OK)
		{
		Error_Handler();
		}

	  }
   }
 }//rider
void HAL_FDCAN_RxBufferNewMessageCallback(FDCAN_HandleTypeDef *hfdcan)
{

    if (FDCAN1 == hfdcan->Instance)
    {
        if (HAL_FDCAN_GetRxMessage(hfdcan, FDCAN_RX_BUFFER0, &RxHeader, RxData_From_Node4) != HAL_OK)
        {
            Error_Handler();
        }
    }

}//raspi
```

각 핸들러는 수신된 메시지를 읽어 `RxHeader` 변수에 메시지 헤더 정보를 저장하고, 데이터를 `RxData_From_NodeX` 배열에 저장한다. <br>만약 메시지 읽기에 실패하면 `Error_Handler()` 함수를 호출하여 오류 처리를 수행한다.<br>이렇게 정의된 핸들러들은 위에서 설명한대로 `HAL_FDCAN_ActivateNotification` 함수를 통해 활성화되며,<br>새로운 메시지 도착 시에 인터럽트가 발생하여 호출된다.<br>우선순위는 fifio>buffer 순이다.

## 통신 테스트

1:3 통신<br>
![1_3통신-rx](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/8a6d5a59-95c7-49bd-b423-e3fe1993d4a9)


