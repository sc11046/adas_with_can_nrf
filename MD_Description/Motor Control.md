# Motor Control

## Overview

본 프로젝트는 컨트롤러에서 RF통신으로 수신받은 액셀레이터,핸들값으로 모터를 제어한다<br>그리고, Can통신으로 차량인식 부분에서 전송받은 데이터를 이용하여 앞바퀴의 방향을 제어한다.<br>모터 컨트롤을 위해 사용된 재원은 다음과 같다.

| 재원                   | 이유                            |
| ---------------------- | ------------------------------- |
| 6v 모터 (2개)          | 뒷바퀴 속도제어                 |
| 12v dc모터 (1개)       | 앞바퀴 방향 제어                |
| W314 DC 모터 드라이버  | 뒷바퀴 속도제어                 |
| MD10C DC 모터 드라이버 | 앞바퀴 방향 제어                |
| 12v 9ah 배터리 (2개)   | 모터드라이버에 연결해 파워 공급 |

[사진 참조](https://github.com/sc11046/adas_with_can_nrf/blob/main/MD_Description/EnvironmentSetting.md#motor) 

## Flow Chart

![KakaoTalk_20230821_223311406](https://github.com/sc11046/adas_with_can_nrf/assets/121782720/0034f505-db18-4bf8-bcbc-024101b9b17a)

## IOC

ioc사진 찍기

## Initialize

```c
  HAL_TIM_PWM_Start(&htim1, TIM_CHANNEL_1);//motor back1
  HAL_TIM_PWM_Start(&htim1, TIM_CHANNEL_2);//motor back2
  HAL_TIM_PWM_Start(&htim1, TIM_CHANNEL_3);//motor front
  HAL_GPIO_WritePin(GPIOF, GPIO_PIN_14,1);//motor enable1
  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_8, 1);//motor enable2
```

각 모터 부분의 PWM을 실행시키고 enable핀을 활성화하기 위한  GPIO핀을 활성화했다



## 기능 설명

### go_back

```
go_back();
```

메인문의 실행파일이다 자세한 내용은 다음과 같다

```c
void go_back(void){
		if(RxData[2]==0)
			  {
			  HAL_GPIO_WritePin(GPIOE, GPIO_PIN_14, 0);
			  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_3,0);
			  htim1.Instance->CCR1=RxData[0];
			  htim1.Instance->CCR2=RxData[0];
			  if(RxData[0]>=100)
				  {
					  htim1.Instance->CCR1=99;
					  htim1.Instance->CCR2=99;
				  }
			  light_sensor();
			 // ridar();
			  }
		if(RxData[2]==1)
				{
			    HAL_GPIO_WritePin(GPIOE, GPIO_PIN_14, 1);
				HAL_GPIO_WritePin(GPIOA, GPIO_PIN_3,1);
				htim1.Instance->CCR1=RxData[0];
			    htim1.Instance->CCR2=RxData[0];
				if(RxData[0]>=100)
					{
						htim1.Instance->CCR1=99;
						htim1.Instance->CCR2=99;
				    }
				light_sensor();
			//	ridar();
			  }
}
```

RxData[2]가 전 후진 모드 설정이다

각 모드에서 

			  HAL_GPIO_WritePin(GPIOE, GPIO_PIN_14, 0);//뒷모터1 후진
			  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_3,0);//뒷모터2 후진
			  
			  HAL_GPIO_WritePin(GPIOE, GPIO_PIN_14, 1);//뒷모터1 전진
			  HAL_GPIO_WritePin(GPIOA, GPIO_PIN_3,1);//뒷모터2 전진

뒷모터의 방향들을 선택해주고 송신부에서 CCR값에 맞게 보내도록 수정했고 <br>`RxData[0]`의 값을 CCR값으로 바로 넣어주었다. <br>`if(RxData[0]>=100)`이부분에서 혹시 ccr값이 100이상이 넘게 되면 CCR의 최대값으로 설정했다.<br>`light_sensor();`코드를 넣어주어 지속적으로 조도센서를 이용해 전조등을 설정해두었다.

#### test

전후진 테스트 영상 nrf 받는거랑 모터 전후진

### nrf_motor

```c
void nrf_motor (void){
	if(RxData[3]==0)
	{
			  if (RxData[1]  <50)
				  {
					  while (RxData[1]  < 50)
					  {
						  light_sensor();
						       buzzer();
						  if (isDataAvailable(2) == 1)
						  {
							  NRF24_Receive(RxData);
						  }
						  htim1.Instance->CCR3 = 80;
						  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 0);
						  HAL_Delay(100);
						  go_back();
						  if(50<=RxData[1]&&RxData[1]<=65)
						  {
							  htim1.Instance->CCR3 = 100;
							  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 1);
							  HAL_Delay(300);
							  htim1.Instance->CCR3 = 0;
						  }
					  }
				  }
			  if (RxData[1] > 65)
					  {
						  while (RxData[1] > 65)
						  {
							  light_sensor();
							       buzzer();
							  if (isDataAvailable(2) == 1)
							  {
								  NRF24_Receive(RxData);
							  }
							  htim1.Instance->CCR3 = 80;
							  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 1);
							  HAL_Delay(100);
							  go_back();
							  if(50<=RxData[1]&&RxData[1]<=65)
							  {
								  htim1.Instance->CCR3 = 90;
								  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 0);
								  HAL_Delay(250);
								  htim1.Instance->CCR3 = 0;
							  }
						  }
					  }
	  }
}
```

`if(RxData[3]==0)` 으로 수동제어 모드를 설정했다.<br>이후 `RxData[1]`값을 이용해 앞바퀴의 조향을 설정했다 <br>50보다 작거나 65보다 클때 동안만 `while (RxData[1] > 65 or <50)`while문을 배치하고<br>CCR값과 GPIO핀으로 앞바퀴를 회전을 시켰다.<br>`if(50<=RxData[1]&&RxData[1]<=65)`을 이용해  다시 중앙으로 값이 돌아오게 된다면 <br>회전 방향을 반대로 돌려 바퀴를 중앙으로 위치하게 설정했다.<br>지속적으로 nrf값을 받기위해 `if (isDataAvailable(2) == 1)`문을 추가시켰다 <br>

#### test

nrf 모터 통신 연결이랑 , 앞바퀴 이용한 전후진



### rpi_motor

```c
void rpi_motor (void){
	  if(RxData[3]==1)
	  {
		  while (RxData_From_Node4[0]=='L')
		  {
			  light_sensor();
			       buzzer();
			  if (isDataAvailable(2) == 1)
			  {
				  NRF24_Receive(RxData);
			  }
			  htim1.Instance->CCR3 = 80;
			  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 0);
			  HAL_Delay(100);
			  go_back();
			  if(RxData[3]==0)
			  {
				  break;
			  }
			  if(RxData_From_Node4[0]=='G')
			  {
				  htim1.Instance->CCR3 = 100;
				  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 1);
				  HAL_Delay(200);
				  htim1.Instance->CCR3 = 0;
				break;
			  }
		  }
			  while (RxData_From_Node4[0]=='R')
			  {
					   light_sensor();
				       buzzer();
				  if (isDataAvailable(2) == 1)
				  {
					  NRF24_Receive(RxData);
				  }
				  htim1.Instance->CCR3 = 80;
				  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 1);
				  HAL_Delay(100);
				  go_back();
				  if(RxData[3]==0)
				  {
					  break;
				  }
				  if(RxData_From_Node4[0]=='G')
				  {
					  htim1.Instance->CCR3 = 90;
					  HAL_GPIO_WritePin(GPIOG, GPIO_PIN_14, 0);
					  HAL_Delay(200);
					  htim1.Instance->CCR3 = 0;
					break;
				  }
			  }
	  }
}
```

`if(RxData[3]==1)`으로 차선인식 모드로 설정했다.<br>`while (RxData_From_Node4[0]=='L' or 'R')`동안 반복했다.<br>그리고 `light_sensor();, buzzer();`로 센서를 동작시키고<br>`if (isDataAvailable(2) == 1)`RF통신을 지속적으로 수신하게끔 했다.<br>CCR값과 GPIO핀으로 앞바퀴를 회전을 시켰다.<br>`if(RxData_From_Node4[0]=='G')`'G' 데이터가 오면 다시 바퀴를 중앙으로 되돌린다.<br>

#### test

l,r지속적으로 오는거 올리고 라이브 

인식하는거에 맞춰 방향 돌아가는거 

### Lidar
```c
void Lidar (void){


	  for(int j=0;j<3;j++)
	  {b[j]=RxData_From_Node1[j]-'0';}

	  int Distance4 = 100* b[0]  +10*b[1] +b[2];//lider
  	  if(Distance4<=20)
  	  {
  		  htim1.Instance->CCR1=0;
  		  htim1.Instance->CCR2=0;
  	  }
}
```
라이다 센서에서 받은 `RxData_From_Node1[j]`0,1,2byte를<br>
`int Distance4 = 100* b[0]  +10*b[1] +b[2];`으로 10진수로 변환시켜 distance값을 만들어주고<br>
20cm보다 아래일때 모터의 움직이지않게 설정했다.

#### test

