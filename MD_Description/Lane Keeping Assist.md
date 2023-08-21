# Lane Keeping Assist

## Overview

나중에할래 솰라솰라

뭐 카메라랑 보드~ 모듈 등등 이유 사용한 이유 솰라솰라

## 블럭도

<img width="70%" src="https://github.com/crasdok/capstone/assets/118472691/dbffaf9c-016d-4d24-91bf-44b46e3e6d16"/>

## 코드설명

**흑백화**

```c
gray = cv2.cvtColor(src, cv2.COLOR_BGR2GRAY)
```
**모서리 검출**

```c
can = cv2.Canny(gray, 50, 200, None, 3)
```
**관심구역 설정**

```c
 height, width = can.shape[:2]
 top_left = (width // 12, height)
 top_right = (width * 11 // 12, height)
 bottom_right = (width * 11 // 12, height * 2 // 3)
 bottom_left = (width // 12, height * 2 // 3)
 roi_vertices = [top_left, top_right, bottom_right, bottom_left]
```
**직선 검출**

```c
line_arr = cv2.HoughLinesP(masked_image, 1, np.pi / 180, 20, minLineLength=10, maxLineGap=10)
```
**원본에 합성**

```c
mimg = cv2.addWeighted(src, 1, ccan, 1, 0)
```

<br>

**방향제어**

**arctan2 함수를 사용하여 각도를 계산하여 각도에 따른 진행 방향을 결정**

```c
 line_arr2[i] = np.append(line_arr[i], np.array((np.arctan2(l[1] - l[3], l[0] - l[2]) * 180) / np.pi))
```

**여러 테스트 진행 후 각도에 따른 진행 방향 정확도 증가**

**arctan2 함수를 사용하여 각도를 계산하여 각도에 따른 진행 방향을 결정**

```c
 if l == 0 or r == 0:
            if (105 <= l and l<= 112) or (105 <= r and r<= 112) :
                print('right')

            elif (112 < l and l<= 200) or (112 < r and r<= 200) :
                print('Hard right')

            elif (90 < abs(l) and abs(l)< 105) or (90 < abs(r) and abs(r)< 105) :
                print('go')

            elif ( l < -113 ) or ( r < -113) :
                print('Hard left')

            else :
                print('left')

        elif l == 0 and r == 0  :
            print('go')

        elif (90 < abs(l) and abs(l)< 105) and (90 < abs(r) and abs(r)< 105) :
            print('go')
 
        elif (105 <= l and l<= 112) and (95 <= r and r<= 112) :
            print('right')

        elif (112 < l and l<= 200) and (98 <= r and r<= 200) :
            print('Hard right')

        elif ( l < -113 ) and ( r < -113) :
            print('Hard left')

        else :
            print('left')

```
**CAN-Data 송신**

**결정된 방향을 CAN-Data로 정의 후 송신**
id=0x44 ID는 0x44로 설정 후 데이터 0x4C(L)을 송신

```c
 message = can.Message(arbitration_id=0x44, is_extended_id=False,data=[0x4C])
           bus.send(message, timeout=0.2)
```

<br/>

## test

송신 되는 live 영상

