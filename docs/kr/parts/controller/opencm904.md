---
layout: archive
lang: kr
ref: opencm904
read_time: true
share: true
author_profile: false
permalink: /docs/kr/parts/controller/opencm904/
sidebar:
  title: OpenCM 9.04
  nav: "opencm904"
---

# [개요](#개요)

![](/assets/images/parts/controller/opencm904/9.04_main.png)

> OpenCM 9.04

- OpenCM9.04 Types Package

|Item|OpenCM9.04 A Type|OpenCM9.04 B Type|OpenCM9.04 C Type|
|:---:|:---:|:---:|:---:|
|Power Switch|X|1|1|
|User Button|1|1|1|
|2Pin Battery(LBS-40)|X|2|2|
|JTAG/SWD|X|1|1|
|Micro B USB|X|1|1|
|5-Pin Port|X|4|4|
|Dynamixel TTL BUS|X|4|4|
|4 Pin 통신 포트|X|1|1|

- `OpenCM9.04`는 32bit ARM Cortex-M3가 장착되어 있으며, 회로도 및 소스코드가 공개된 로봇용 임베디드 보드입니다.
- Type으로는 A,B,C type이 있습니다. 위의 Type사진과 표를 보면 커넥터를 제공 여부에 따라 Type이 달라집니다.  
  (A Type의 경우 OpenCM9.04-악세서리 세트를 별도 구매하시면 B,C Type에서 제공하는 모든 커넥터 부품들과 USB Cable을 한번에 구입할 수 있습니다.) [악세서리 구매하기](http://www.robotis.com/shop/item.php?it_id=902-0084-030)

`Note` 제어기복구는 현재 OpenCM9.04 C 타입(다윈미니용제어기)만 지원되고있으며,복구방법은 아래 링크에서 확인가능합니다. [OpenCM9.04 C Type 펌웨어 복구하기]
{: .notice}

`주의` 다이나믹셀 펌웨어 업데이트 및 복구시 OpenCM9.04와 OpenCM 485 확장보드를 반드시 분리해주세요.
{: .notice--warning}

# [제품 사양](#제품-사양)

|          항목           |              사양               |
|:-----------------------:|:-------------------------------:|
|           CPU           |   STM32F103CB (ARM Cortex-M3)   |
|        동작 전압        |            5V ~ 16V             |
|         입출력          |            GPIO x 26            |
|         타이머          |            4 (16bit)            |
|   아날로그 입력(ADC)    |           10 (12bit)            |
|      플래시 메모리      |              128Kb              |
|          SRAM           |              20Kb               |
|        동작 클럭        |              72Mhz              |
|           USB           | 1 (2.0 Full Speed) Micro B Type |
|          USART          |                3                |
|           SPI           |                2                |
|        I2C(TWI)         |                2                |
|         디버깅          |           JTAG & SWD            |
| 다이나믹셀 3핀 TTL 버스 |                4                |
|          크기           |          27mm x 66.5mm          |

{% capture opencm904_caution_01 %}
`주의`
- USB전원으로는 다이나믹셀을 구동할 수 없습니다. 별도의 전원을 공급해주세요.  
  (OpenCM9.04 는 USB나 배터리 또는 + - 터미널 중에 한 군대 이상만 전원이 공급되면 동작합니다.)
- 별도의 전원공급시 연결되는 주변장치의 동작전압을 확인해주세요. 다이나믹셀이나 XL-Series의 경우 입력된 전압이 그대로 공급됩니다.
- 다이나믹셀과 XL-Series는 동작전압차이가 있어 함께 사용할 수 없습니다.
{% endcapture %}

<div class="notice--warning">{{ opencm904_caution_01 | markdownify }}</div>


# [컨트롤 테이블](#컨트롤-테이블)
컨트롤 테이블은 제어기 내부에 존재하는 데이터의 집합체입니다. 사용자는 컨트롤 테이블의 주소를 이용하여
해당 주소에 저장된 데이터를 읽어오거나 새로운 데이터를 입력할 수 있습니다.
 
- 영역(EEPROM, RAM)
컨트롤 테이블은 2개의 영역으로 구분됩니다. RAM 영역에 위치한 데이터는 전원이 인가될 때마다 다시 초기값으로 설정됩니다(Volatile).
반면 EEPROM 영역에 위치한 데이터는 값을 변경하면 전원이 꺼져도 그 값이 보존됩니다(Non-Volatile).
 
- 접근 속성(R, RW)
컨트롤 테이블의 데이터에는 2개의 접근 속성이 있습니다.
‘RW’는 읽기와 쓰기 접근이 모두 가능합니다. 반면 ‘R’은 읽기 전용(Read Only) 속성을 갖습니다.
읽기 전용 속성의 데이터는 사용자가 임의로 값을 바꿀 수 없기 때문에 주로 측정 또는 모니터링 용도로 사용되고,
읽기 쓰기 속성(‘RW’)은 제어 용도로 사용됩니다.
 
- 초기값
제어기에 전원이 인가될 때 컨트롤 테이블의 각 데이터는 초기값으로 설정됩니다.
사용자가 초기값을 변경하면 새로운 초기값이 적용됩니다. RAM 영역의 초기값은 전원이 인가되었을 때 설정되는 값입니다.
 
- 크기
데이터의 크기는 용도에 따라 1 ~ 4 byte로 정해져 있습니다. Instruction Packet을 통해 데이터를 변경할 때는 해당 데이터의 크기를 확인하시기 바랍니다.


## [EEPROM Area](#eeprom-area)

|주소|크기|데이터|설명|접근속성|초기값|
|:---:|:---:|:---:|:---:|:---:|:---:|
|0|2|Model Number|모델 변호|R|400|
|6|1|Firmware Version|펌웨어 버전 정보|R|-|
|7|1|ID|제어기 ID|RW|200|
|8|1|Baud Rate|제어기 통신속도|R|1|
|9|1|Return Delay Time|응답 지연 시간|RW|0|
|10|1|Status Return Level|응답 레벨|RW|2|
|12|1|DXL Baud Rate|다이나믹셀 통신속도|RW|3|

## [RAM Area](#ram-area)

|주소|크기|데이터|설명|접근속성|초기값|
|:---:|:---:|:---:|:---:|:---:|:---:|
|21|1|Mode Number|현재 모드 번호|RW|-|
|26|1|Button Status|사용자버튼 상태|R|0|
|66|2|Motion Play Page|모션 실행 페이지 번호|RW|0|
|68|1|Motion Play Status|모션 실행 상태|R|-|
|73|1|128ms Timer Value|128ms 타이머 값|RW|0|
|74|2|1ms Timer Value|1ms 타이머 값 |RW|0|
|77|1|Random Number|임의의 숫자|RW|-|
|79|1|Green LED|녹색 LED 값|RW|0|
|82|1|Motion LED|모션중 LED 사용 여부|RW|0|
|360|2|Port 1 IR Sensor Value|1번 포트 적외선 센서 값|R|-|
|366|2|Port 4 IR Sensor Value|4번 포트 적외선 센서 값|R|-|
|368|2|Port 1 DMS Sensor Value|1번 포트 DMS 센서 값|R|-|
|370|2|Port 2 DMS Sensor Value|2번 포트 DMS 센서 값|R|-|
|372|2|Port 3 DMS Sensor Value|3번 포트 DMS 센서 값|R|-|
|374|2|Port 4 DMS Sensor Value|4번 포트 DMS 센서 값|R|-|
|376|1|Port 1 Touch Sensor Value|1번 포트 터치 센서 값 |R|-|
|377|1|Port 2 Touch Sensor Value|2번 포트 터치 센서 값 |R|-|
|378|1|Port 3 Touch Sensor Value|3번 포트 터치 센서 값 |R|-|
|379|1|Port 4 Touch Sensor Value|4번 포트 터치 센서 값 |R|-|
|381|1|Port 2 LED Module Value|2번 포트 LED모듈 제어 값 |RW|0|
|382|1|Port 3 LED Module Value|3번 포트 LED모듈 제어 값 |RW|0|
|386|2|Port 2 User Device Value|2번 포트의 사용자 장치|RW|0|
|388|2|Port 3 User Device Value|3번 포트의 사용자 장치|RW|0|
|392|1|Port 1 Temperature Sensor Value|1번 포트 온도 센서 값|R|-|
|393|1|Port 2 Temperature Sensor Value|2번 포트 온도 센서 값|R|-|
|394|1|Port 3 Temperature Sensor Value|3번 포트 온도 센서 값|R|-|
|395|1|Port 4 Temperature Sensor Value|4번 포트 온도 센서 값|R|-|
|396|1|Port 1 Ultrasonic Sensor Value|1번 포트 초음파 센서 값|R|-|
|397|1|Port 2 Ultrasonic Sensor Value|2번 포트 초음파 센서 값|R|-|
|398|1|Port 3 Ultrasonic Sensor Value|3번 포트 초음파 센서 값|R|-|
|399|1|Port 4 Ultrasonic Sensor Value|4번 포트 초음파 센서 값|R|-|
|400|1|Port 1 Magnetic Sensor Value|1번 포트 자석 센서 값 |R|-|
|401|1|Port 2 Magnetic Sensor Value|2번 포트 자석 센서 값 |R|-|
|402|1|Port 3 Magnetic Sensor Value|3번 포트 자석 센서 값 |R|-|
|403|1|Port 4 Magnetic Sensor Value|4번 포트 자석 센서 값 |R|-|
|404|1|Port 1 Motion Sensor Value|1번 포트 동작 감지 센서 값|R|-|
|405|1|Port 2 Motion Sensor Value|2번 포트 동작 감지 센서 값|R|-|
|406|1|Port 3 Motion Sensor Value|3번 포트 동작 감지 센서 값|R|-|
|407|1|Port 4 Motion Sensor Value|4번 포트 동작 감지 센서 값|R|-|
|409|1|Port 2 Color Sensor Value|2번 포트 칼라 센서 값 |R|-|
|410|1|Port 3 Color Sensor Value|3번 포트 칼라 센서 값 |R|-|

`Note` R+ Manager 2.0을 통해서 제어기를 연결하면 컨트롤 테이블을 확인할 수 있습니다. ([컨트롤 테이블 확인하기])
{: .notice}

# [하드웨어](#하드웨어)

[OpenCM 9.04 영문 eManual]에서 더 많은 정보가 제공되고 있습니다. 오른쪽 탭의 `구글 버튼`을 클릭하시면 구글 번역기를 사용할 수 있습니다.
{: .notice--success}

[OpenCM 9.04 영문 eManual]: /docs/en/parts/controller/opencm904/


## [블럭 다이어그램](#블럭-다이어그램)

OpenCM9.04의 전체 블럭 다이어그램은 아래와 같습니다. OpenCM9.04는 32비트 Cortex-M3 코어를 가진 STM32F103CB 마이크로컨트롤러를 중심으로 회로가 구성되어 있습니다.  
전원 회로는 5V와 3.3V 레귤레이터가 다단(Cascade) 형태로 구성되어 있고 5V는 다이나믹셀 TTL 버스에 공급되고 3.3V는 마이크로컨트롤러 및 5핀 포트, 4핀 통신포트에 공급됩니다.  
OpenCM9.04의 USB 2.0 FS를 지원하고 Micro-B 커넥터를 이용해 프로그램을 다운로드 받거나 데이터 통신을 수행합니다.  
USART 1번 채널은 다이나믹셀 TTL Bus에 할당되어 있어서 핀11(TX1) 12(RX1)을 이용한 중복사용은 불가능합니다.

![](/assets/images/parts/controller/opencm904/opencm904_01.png)

> OpenCM9.04 블럭 다이어그램

## [각 부 명칭](#각-부-명칭)

![](/assets/images/parts/controller/opencm904/9.04_map.jpg)

### [Power Switch]
보드와 다이나믹셀 전원을 on/off하는 스위치 입니다. 단, USB 연결시 보드 전원은 on/off되지 않습니다.

### [직렬 배터리 소켓]
LBS-04 리튬 이온 배터리 2개를 직렬 연결하는 커넥터 입니다. 배터리를 2개 직렬 연결한 상태에서 충전할 경우 쇼트위험이 있으므로 반드시 보드와 연결을 분리해서 충전해주세요.

### [Dynamixel TTL 3 PIN]
3핀 케이블(Dynamixel TTL Bus)을 사용하는 다이나믹셀을 Daisy chain으로 연결하기 위한 포트 입니다.

### [통신포트]
BT-210, BT-110A, ZIG-110A, LN-101 등과 같은 4핀 케이블을 연결해서 외부 장치와 유/무선 통신을 수행합니다. OpenCM9.04의 4핀 통신 포트는 Serial2(USART2)를 사용합니다.

    ![](/assets/images/parts/controller/opencm904/opencm9.04_1-1.jpg)

    > 4핀 통신 포트 핀맵

    ![](/assets/images/parts/controller/opencm904/opencm9.04_2.png)  

    > OpenCM9.04와 스마트폰을 이용한 무선 통신 연결 예시

    ![](/assets/images/parts/controller/opencm904/opencm904_05.png)

    > OpenCM9.04와 RC100을 이용한 블루투스 무선 통신 연결 예시

    ![](/assets/images/parts/controller/opencm904/opencm904_06.png)   

    > OpenCM9.04와 RC100을 이용한 지그비 무선 통신 연결 예시

    ![](/assets/images/parts/controller/opencm904/opencm904_07.png)  

    > OpenCM9.04와 LN-101을 이용한 유선 통신 연결 예시

5. User Button : 사용자가 제어 할 수 있는 스위치 입니다. 23번핀 혹은 BOARD_BUTTON_PIN으로 사용할 수 있고 pinMode()에서 INPUT_PULLDOWN옵션으로 초기화해서 사용합니다.  
 그리고 OpenCM9.04에서 다운로드가 되지 않을 때 User button을 누른 상태에서 USB를 연결하면 Status LED가 계속 켜지면서 바로 다운로드 모드로 진입합니다.

    ![](/assets/images/parts/controller/opencm904/opencm904_08.png)

    > OpenCM9.04는 User button으로 긴급 복구 모드 진입을 할 수 있습니다.

6. Ext. ADC Ref Jumper : Analog 기준전압을 변경할 수 있습니다. 12. 입출력 IO헤더 부분을 참고해주십시요.  
7. Micro-B USB : OpenCM9.04를 다운로드 또는 PC와 같은 외부 장치와 USB 통신을 수행하며 동시에 5V 전원이 공급됩니다.  
  배터리 연결시 USB 5V는 자동으로 차단되고 배터리로부터 전원을 공급받습니다. 만약 과전류가 흐를 경우 내장된 Fuse가 USB 5V에서 흐르는 전류를 차단하므로 사용자 PC가 손상될 위험으로부터 보호됩니다

    ![](/assets/images/parts/controller/opencm904/opencm904_09.png)

8. Status LED : OpenCM9.04에서 프로그래밍 확인을 위한 테스트 LED입니다. 14번 핀 혹은 BOARD_LED_PIN이 HIGH이면 꺼지고 LOW가 되면 켜집니다. PWM으로 제어 가능합니다.

    ![](/assets/images/parts/controller/opencm904/opencm904_10.png)

    > Status LED 위치

9. Reset SW : CPU의 리셋을 수행합니다.
10. JTAG/SWD 4 PIN : ST-LINK와 같은 In-Circuit Debugger and Programmer와 연결할 수 있습니다.  
  JTAG/SWD 4핀 포트를 통해서 전문가를 위한 별도의 상용개발프로그램을 이용 가능합니다. 아래는 ST-LINK와의 연결도 예시입니다.

    ![](/assets/images/parts/controller/opencm904/opencm904_11.png)

    > ST-LINK 와 OpenCM9.04 연결 예시

11. 5 핀 포트 : ROBOTIS 5핀 장치(모듈)를 연결해 사용 할 수 있습니다.

    ![](/assets/images/parts/controller/opencm904/opencm904_12.png)

    > 5핀 포트 번호 및 사용 방법

    ![](/assets/images/parts/controller/opencm904/opencm904_13.png)

    > 5핀 케이블 연결 방향

|5핀 장치 종류|포트 1|포트 2|포트 3|포트 4|
|:---:|:---:|:---:|:---:|:---:|
|적외선센서|O|X|X|O|
|DMS센서|O|O|O|O|
|자이로센서|O|O|O|O|
|접촉센서|O|O|O|O|
|컬러센서|X|O|O|X|
|자석센서|O|O|O|o|
|온도센서|O|O|O|O|
|동작감지센서|O|O|O|O|
|LED모듈|X|O|O|X|

> 로보티즈 센서 및 LED 모듈 호환 리스트

12. 입출력 IO헤더 : OpenCM9.04의 CPU인 STM32F103CB의 GPIO 핀들이 외부로 인터페이스 되어 있어서 자유롭게 활용 가능합니다.  
핀0~25번까지 총26개의 GPIO 핀은 디지털 입력 및 출력이 가능하고 3.3V에서 동작합니다. 디지털 출력에서 HIGH일 경우 3.3V이고 LOW일때는 0V입니다.
디지털 출력에서 최대 토글 스피드는 18Mhz입니다.(단, 핀22, 23은 최대 2Mhz)디지털 입력의 경우 0V~3.3V까지만 입력 가능하지만 아래의 핀들은 5V입력이 가능합니다.
그 외에 핀에 3.3V 이상의 전압을 입력할 경우 OpenCM9.04가 손상될 수 있으니 주의하세요.  
**5V입력가능한 핀(5V Tolerant) : 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21**
각 핀에서 최대로 공급(Source) 또는 받아들일(Sink) 수 있는 전류는 최대 25mA입니다.(단 핀22,  23의 경우 3mA)  
OpenCM9.04의 모든 GPIO핀들은 내부적으로 pull-up 또는 pull-down이 지원되고 소프트웨어로 변경할 수 있습니다. 참고로 pull-up 또는 pull-down 저항은 40Kohm(Typical)입니다.
기타 궁금한 사항은 OpenCM9.04의 CPU인 STM32F103CB의 데이터시트를 참고하세요.

  ![](/assets/images/parts/controller/opencm904/opencm904_14.png)

  > OpenCM9.04 GPIO 핀맵 >

- Serial(USART) : 11(TX1), 12(RX1), 4(TX2), 5(RX2), 24(TX3), 25(RX3)
- PWM : 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14
- SPI : 0(SS1), 1(SCK1),6(MISO1) ,7(MOSI1), 18(SS2) ,19(SCK2) ,20(MISO2) ,21(MOSI2)
- LED : 14 (BOARD_LED_PIN is defined as 14)
- BUTTON : 23 (BOARD_BUTTON_PIN is defined as 23)

- RST : CPU Reset 신호와 연결된 핀입니다.
- 5V : 배터리 연결시 5V 전원을 사용할 수 있는 핀입니다. 단 USB 연결시 5V 전압이 출력되지 않습니다.
- 3.3V : 배터리 연결 혹은 USB 연결시 3.3V 전원을 사용할 수 있는 핀입니다.
- GND : -핀과 같이 연결되어 있고 보드 전체의 GND(-) 단자입니다.
- A0 : 디지털 입출력및 아날로그 입력이 가능합니다.
- A1 : 디지털 입출력및 아날로그 입력이 가능합니다. SPI 1번채널의 SCK클럭핀으로 사용 가능합니다.
- A2 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다.
- A3 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다.
- A4 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다. USART1번(Serial2) 채널의 TXD 핀으로 사용되고 4핀 통신포트의 TX2와 연결되어 있습니다.
- A5 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다. USART2번(Serial2) 채널의 RXD 핀으로 사용되고 4핀 통신포트의 RX2와 연결되어 있습니다.
- A6 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다. SPI 1번 채널의 MISO 핀으로 사용 가능합니다.
- A7 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다. SPI 1번 채널의 MOSI 핀으로 사용 가능합니다.
- A8 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다.
- A9 : 디지털 입출력및 아날로그 입력이 가능합니다. PWM 출력이 가능합니다.
- D10 : 디지털 입출력이 가능합니다. PWM 출력이 가능합니다.
- D11 : 디지털 입출력이 가능합니다. PWM 출력이 가능합니다. USART1번(Serial1)의 TX핀입니다. 다이나믹셀 TTL Bus와 중복사용 불가능합니다.
- D12 : 디지털 입출력이 가능합니다. PWM 출력이 가능합니다. USART1번(Serial1)의 RX핀입니다. 다이나믹셀 TTL Bus와 중복사용 불가능합니다.
- D13 : 디지털 입출력이 가능합니다. PWM 출력이 가능합니다.
- D14 : 디지털 입출력이 가능합니다. PWM 출력이 가능합니다. Status LED와 연결된 핀입니다. BOARD_LED_PIN으로 미리 define되어 있습니다.
- D15 : 디지털 입출력이 가능합니다.
- VCC(+) : 보드 전체 +전원 입력단입니다. 연속된 2개의 홀이 VCC(+)에 해당합니다. 배터리 소켓의 +단자와 연결되어 있습니다.
- GND(-):보드 전체 -전원 입력단입니다. 연속된 2개의 홀이 GND(-)에 해당합니다. 배터리 소켓의 –단자와 연결되어 있습니다.
- TTL : 다이나믹셀 TTL Bus의 Data 라인입니다. 이 핀만 가지고 3핀 TTL 데이지 체인 구성을 할 수 있습니다.
- D : 다이나믹셀 TTL Bus와 관련된 핀이고 TX, RX를 선택하는 핀입니다.
- X : 다이나믹셀 TTL Bus의 TX 핀입니다.
- L : 다이나믹셀 TTL Bus의 RX 핀입니다.
- D16 : 디지털 입출력이 가능합니다.
- D17 : 디지털 입출력이 가능합니다.
- D18 : 디지털 입출력이 가능합니다.
- D19 : 디지털 입출력이 가능합니다. SPI 2번 채널의 SCK 클럭핀으로 사용 가능합니다.
- D20 : 디지털 입출력이 가능합니다. SPI 2번 채널의 MISO핀으로 사용 가능합니다.
- D21 : 디지털 입출력이 가능합니다. SPI 2번 채널의 MOSI 클럭핀으로 사용 가능합니다.
- D22 : 디지털 입출력이 가능합니다. 최대 출력 전류가 3mA 이고 최대 토글 스피드는 2Mhz입니다.
- D23 : 디지털 입출력이 가능합니다. User Button과 연결된 핀입니다. 버튼을 누른채로 전원을 공급하면 보드가 긴급 복구(다운로드) 모드로 진입하게 되므로 유의합니다. 최대 출력 전류가 3mA 이고 최대 토글 스피드는 2Mhz입니다.
- D24 : 디지털 입출력이 가능합니다. I2C 2번 채널의 SCL 핀으로 사용 가능합니다. 또한 USART3(Serial3)의 TX핀으로 사용 가능합니다.
- D25 : 디지털 입출력이 가능합니다. I2C 2번 채널의 SDA 핀으로 사용 가능합니다. 또한 USART3(Serial3)의 RX핀으로 사용 가능합니다.
- 5V : 5V 공급 가능한 핀입니다. JP2 점퍼를 수정해서 아래의 AREF 핀과 연결해서 5V 기준전압으로 변경할 수 있습니다.
- AREF : CPU의 Analog Reference 핀과 연결되어 있습니다. JP2번 점퍼를 수정하면 AREF를 3.3V가 아닌 다른 전압으로 변경할 수 있습니다.(최대 5V전압만 가능합니다. 자세한 사항은 [STM32F103CB]의 데이터 시트를 참고하세요)

  ![](/assets/images/parts/controller/opencm904/opencm904_15.png)

  > JP2 점퍼 위치 및 사용방법

  ![](/assets/images/parts/controller/opencm904/opencm904_16.png)

  > JP2를 변경해서 아날로그 입력 기준 전압(AREF)를 5V로 변경한 예시

`주의` 아날로그 기준전압(AREF)을 5V로 변경시 5V 입력 가능한 핀을 확인하세요.
{: .notice--danger}

# 전원 연결 방법

OpenCM보드는 아래와 같이 3가지 전원 포트로 보드를 작동시킬 수 있습니다.
①LBS-40 배터리 소켓
②오른쪽 헤더의 +-핀을 통한 공급
③USB 연결을 통한 공급

  ![](/assets/images/parts/controller/opencm904/opencm904_17.png)

XL-320 다이나믹셀을 구동시키기 위해서는 LBS-04 배터리 2개를 배터리 소켓에 연결하는 것을 추천합니다.

 ![](/assets/images/parts/controller/opencm904/opencm9.04_12.png)

    <OpenCM9.04와 XL-320, LBS-40(Li-Ion Battery) 연결 예시>  

 ![](/assets/images/parts/controller/opencm904/dream_bat.JPG)

      < LBS-40 배터리 >

  XL-320을 제외한 3핀 TTL 다이나믹셀은 헤더의 +-핀 또는 배터리 소켓을 이용해 전원을 공급하시길 추천합니다.

   ![](/assets/images/parts/controller/opencm904/opencm9.04_15.JPG)

   < OpenCM9.04와 AX12 연결 예시 >

아래의 그림과 같이 배터리 커넥터의 양끝 +,-에 연결하고 전원을 공급할 수 있는 장치로 연결도 가능합니다.
(OpenCM9.04 뒷면을 보면 그림A와 같이 가운데 +,-가 연결 되어있으므로 아래와 같이 연결이 가능합니다.)

 ![](/assets/images/parts/controller/opencm904/opencm9.04_15_2.JPG)

     <그림 A>

 ![](/assets/images/parts/controller/opencm904/opencm9.04_15_1.JPG)

     <전원 연결 예시>

[전원 공급 케이블 예시]
{: .notice}

아래와 그림과 같이 배터리 소켓과 +-핀으로 동시 전원 공급은 피하세요.
그리고 배터리 소켓에 배터리를 혼용하지 마십시요. 배터리 소켓은 LBS-40 배터리만 사용해 주세요.

  ![](/assets/images/parts/controller/opencm904/opencm9.04_17.JPG)

배터리 커넥터 및 +-헤더핀 전원 두 개중 하나는 제거

  ![](/assets/images/parts/controller/opencm904/opencm9.04_17.JPG)

배터리 혼용 금지

  ![](/assets/images/parts/controller/opencm904/opencm9.04_14.PNG)

  USB 포트는 LBS-40 배터리 혹은  +-핀과 동시 연결해도 무방합니다.(차단회로 내장)
  +- 혹은 배터리로 공급되는 전압은 다이나믹셀의 권장 전압으로 사용하시길 추천합니다.
  그 이상의 전압은 다이나믹셀의 수명을 단축시키거나 고장의 원인이 될 수 있습니다.
  참고로 OpenCM9.04에 공급할 수 있는 최대 전압은 16V입니다. 그 이상의 전압은 OpenCM9.04의 부품들이 견디지 못할 수도 있습니다.
  USB 전원 공급만으로는 다이나믹셀의 정상 동작이 되지 않지만 그 외에 통신포트나 IO 헤더핀은 정상동작을 합니다.
  LBS-40 배터리를 직렬 연결한 채로 USB 충전은 쇼트가 생길 수 있으니 주의하세요.


# A타입 전원 스위치 추가하기

A타입에는 전원 스위치가 없이 JP1이 쇼트처리 되어서 판매됩니다. 전원 스위치를 추가하기 위해서는 JP1의 납땜 처리를 제거하시고 2.54mm pitch 전원 스위치를 추가하시면 됩니다.  
전원스위치는 OpenCM9.04 악세사리 키트에 포함되어 있고 pitch가 맞는 전원 스위치를 따로 구해서 사용할 수 있습니다.

![](/assets/images/parts/controller/opencm904/opencm9.04_23.PNG)

   < JP1과 전원 스위치 회로도 : JP1이 연결되어 있으면 스위치가 없이도 동작합니다. >

  ![](/assets/images/parts/controller/opencm904/opencm9.04_24.PNG)

  < OpenCM9.04의 A타입에서는 JP1의 연결을 끊고 스위치를 추가해야 제대로 동작합니다. >

# A타입 다이나믹셀 TTL 3핀 추가하기

OpenCM9.04의 다이나믹셀 TTL 3핀 홀은 기존 다이나믹셀 TTL 3핀과 XL-320 TTL 3핀(미니타입)이 모두 호환 가능합니다. 따라서 두 가지 종류의 커넥터 모두 납땜해서 사용할 수 있습니다.  
참고로 2종류의 TTL 3핀은 OpenCM 악세사리 키트에 모두 포함되어 있습니다.

  ![](/assets/images/parts/controller/opencm904/opencm9.04_25.PNG)

     < OpenCM9.04 A타입은 2가지 종류의 3핀 커넥터를 모두 납땜할 수 있습니다>

  
## 소프트웨어 개발환경

[Arduino IDE]

[OpenCM IDE]

||Arduino IDE|OpenCM IDE|
|:---:|:---:|:---:|
|Dynamixel SDK|O|X|
|DynamixelWorkbench|O|X|
|Arduino Libraries|O|O|
|OS X|O|O (10.12.2)|
|Linux|O|O (12.04)|
|Windows|O|O|

## 매뉴얼 다운로드
[download]OpenCM9.04_manual_ko.zip

## 회로도 및  서버 다운로드
[download]SCHEMATIC-OpenCM9.04.pdf
[download]OpenCM9.04_REV_1.-(131009)-TOP.pdf
[download]OpenCM9.04_REV_1.-(131009)-BOTTOM.pdf
[download]OpenCM9.04_REV_1.-(131009)-Gerber.pdf


# Dimension

![](/assets/images/parts/controller/opencm904/floor_plan.PNG)



[악세서리 구매하기]: ??
[OpenCM9.04 C Type 펌웨어 복구하기]: /docs/kr/edu/mini/#로보티즈-미니-제어기-펌웨어-업데이트
[Arduino IDE]: ?
[OpenCM IDE]: ?
[Dynamixel SDK]: ??
[DynamixelWorkbench]: ??
[STM32F103CB]: ??
[전원 공급 케이블 예시]: ??
[컨트롤 테이블 확인하기]: ??
