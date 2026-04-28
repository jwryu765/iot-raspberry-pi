# iot-raspberry-pi
IoT개발자 과정 라즈베리파이 학습

## 1일차

### 1. OS 설치 및 부팅

1. **Raspberry Pi Imager 준비**
    - 공식 홈페이지에서 [Raspberry Pi Imager](https://www.raspberrypi.com/software/)를 다운로드 및 설치합니다.
2. **SD 카드에 OS 굽기**
    - MicroSD 카드를 USB 리더기에 꽂아 PC에 연결합니다.
    - Raspberry Pi Imager를 실행하고, 설치할 OS(Raspberry Pi OS)와 저장소(SD 카드)를 선택합니다.
    - 고급 설정(톱니바퀴 아이콘)에서 **SSH 활성화**, **사용자 이름/비밀번호 설정**, **Wi-Fi 설정**을 미리 구성한 후 `쓰기(Write)`를 진행합니다.
3. **라즈베리파이 작동**
    - OS 설치가 완료된 SD 카드를 라즈베리파이에 삽입합니다.
    - 전원 케이블을 연결하여 라즈베리파이를 부팅합니다.

### 2. CMD를 통한 원격 접속 및 시스템 업데이트

PC의 명령 프롬프트(CMD)를 사용하여 라즈베리파이에 SSH로 원격 접속한 뒤, 시스템 패키지를 최신 상태로 업데이트합니다.

- SSH 접속
```bash
# 기본 접속 명령어 (IP 주소는 공유기 설정 등에서 확인 가능)
ssh [설정한 사용자 이름]@[라즈베리파이의 IP 주소]

# 예시
ssh pi@192.168.0.1x
```
최초 접속 시 나타나는 보안 경고 메시지에는 **yes**를 입력하고, 설정해둔 비밀번호를 입력하여 접속합니다.

![alt text](<스크린샷 2026-04-27 103205.png>)

- 시스템 업데이트 및 업그레이드
접속이 완료된 후, 아래의 명령어를 순차적으로 입력하여 패키지 목록을 갱신하고 업그레이드를 진행합니다.
```bash
sudo apt update
sudo apt upgrade -y
```

### 3. VNC Viewer를 이용한 GUI 접속

CLI(명령어 창) 환경뿐만 아니라, 데스크탑 환경(GUI)을 사용하기 위해 VNC Viewer를 설정합니다.

1. **VNC Server 활성화 (라즈베리파이)**
    - 최신 Raspberry Pi OS는 기본적으로 VNC가 설치되어 있으나 활성화가 필요할 수 있습니다.
    - SSH로 접속된 CMD 창에서 `sudo raspi-config`를 입력합니다.
    - `Interface Options` -> `VNC`로 이동하여 **Enable(활성화)** 처리합니다.
2. **VNC Viewer 다운로드 (PC)**
    - PC에 [RealVNC Viewer](https://www.realvnc.com/en/connect/download/viewer/)를 다운로드하고 설치합니다.
3. **라즈베리파이 접속**
    - VNC Viewer를 실행합니다.
    - 주소창에 `[라즈베리파이의 IP 주소]`를 입력하고 엔터를 누릅니다.
    - 라즈베리파이의 사용자 이름과 비밀번호를 입력하면 라즈베리파이의 바탕화면 화면이 나타납니다.

### 4. 한글 패치

- 터미널에서 아래 명령어를 순차적으로 입력하여 한글 환경을 구축합니다.
```c
$ sudo apt install fonts-nanum fonts-nanum-extra     // 폰트 설치
$ sudo apt install fonts-unfonts-core                // 폰트 등록
$ sudo apt install ibus                              // 입력기 설치
$ sudo apt install ibus-hangul                       // ibus 패키지 설치
$ ibus-setup
```
input Method > Add > kroean 검색 > Korean - Hangul 선택 > Preferences > Add > Key 등록

### 5. 개발 환경 설정(C,C++,Python)

- CMD 창을 열고 아래 명령어를 순차적으로 입력합니다.
    - C/C++
    ```c
    $ sudo apt install libgpiod-dev gpiod -y  
    # include <gpiod.h>
    gcc compile 시 -lgpiod 옵션 추가
    ```
    - Python
    ```c
    //$ sudo apt install python3-libgpiod -y
    $ sudo apt install python3.13-venv
    $ mkdir -p ~/venvs
    $ cd ~/venvs
    $ python3 -m venv --system-site-packages .venv
    $ soucre .venv/bin/activate
    $ deactivate
    ```
### 6. GPIO 실습

- 라즈베리파이의 GPIO 핀을 활용하여 3개의 LED를 순차적으로 점멸시키는 실습을 진행했습니다.
```python
from gpiozero import LED
from time import sleep

# GPIO 핀 번호 설정 (BCM 방식)
led = LED(14)
led1 = LED(15)
led2 = LED(18)

while True:
    # 모든 LED 켜기
    led.on()
    led1.on()
    led2.on()

    # LED 1 점멸 (0.5초간 껐다 켜기)
    led.off()
    sleep(0.5)
    led.on()

    # LED 2 점멸 (0.5초간 껐다 켜기)
    led1.off()
    sleep(0.5)
    led1.on()

    # LED 3 점멸 (0.5초간 껐다 켜기)
    led2.off()
    sleep(0.5)
    led2.on()
```

## 2일차

### **라즈베리 파이 RGB LED 및 이중 스위치 제어 시스템**

#### 1. 개요
- **목적**: 하드웨어 이벤트 제어 및 상태 관리 로직 이해
- **주요 기능**:
  - **전원 스위치 (Switch 2)**: 전체 시스템의 활성화/비활성화 토글
  - **색상 스위치 (Switch 1)**: 전원이 켜진 상태에서 LED 색상을 순차적으로 변경 (빨강 → 초록 → 파랑 → 하양)

#### 2. 하드웨어 구성 및 회로 설계

![alt text](image.png)

##### 2.1 주요 부품
| 부품 | 수량 | 비고 |
| :--- | :--- | :--- |
| 라즈베리 파이 | 1 | |
| RGB LED | 1 | 공통 양극 (Common Anode) 타입 |
| 택트 스위치 | 2 | 전원용, 색상변경용 |
| 저항 (220$\Omega$) | 3 | LED 보호용 |
| 저항 (10k$\Omega$) | 2 | 스위치 풀업(Pull-up) 회로용 |
| 브레드보드 및 점퍼선 | 12 | |

##### 2.2 핀 맵 (Pin Mapping)
| 기능 | 핀 번호 (BCM) | 물리 핀 번호 |
| :--- | :--- | :--- |
| **Switch 1 (Color)** | GPIO 2 | 3번 핀 |
| **Switch 2 (Power)** | GPIO 4 | 7번 핀 |
| **LED (Red)** | GPIO 17 | 11번 핀 |
| **LED (Green)** | GPIO 27 | 13번 핀 |
| **LED (Blue)** | GPIO 22 | 15번 핀 |

#### 3. 핵심 원리 및 개념

##### 3.1 공통 양극(Common Anode) RGB LED
본 실습에 사용된 LED는 긴 다리가 `3.3V` 전원에 연결되는 방식입니다.
- **작동 원리**: GPIO 핀에서 `LOW(0)` 신호를 주어 전위차를 만들 때 전류가 흐르며 불이 켜집니다.
- **코드 적용**: `gpiozero` 라이브러리의 `active_high=False` 설정을 통해 로직을 직관적으로 반전시켜 처리합니다.

##### 3.2 풀업(Pull-up) 저항 회로
스위치가 눌리지 않았을 때 입력 핀이 떠 있는 상태(Floating)를 방지하기 위해 10k$\Omega$ 저항을 전원에 연결했습니다.
- **평상시**: `HIGH(1)` 신호 유지
- **클릭 시**: 전류가 GND로 흐르며 `LOW(0)` 신호 발생

##### 3.3 디바운싱 (Debouncing)
스위치 내부 금속판의 미세한 떨림으로 인해 한 번의 클릭이 여러 번 입력된 것으로 인식되는 '채터링' 현상을 방지하기 위해 코드상에서 `bounce_time=0.1` 옵션을 적용하여 하드웨어적 한계를 소프트웨어적으로 보완했습니다.

#### 4. 소프트웨어 구현

`gpiozero` 라이브러리의 이벤트 기반 프로그래밍 방식을 사용하여 CPU 부하를 최소화하고 가독성을 높였습니다.

```python
from gpiozero import Button, RGBLED
from signal import pause

# 1. 하드웨어 인스턴스 생성
sw_color = Button(2, bounce_time=0.1) 
sw_power = Button(4, bounce_time=0.1) 
led = RGBLED(red=17, green=27, blue=22, active_high=False)

# 2. 전역 상태 변수
is_power_on = False
color_state = 0
led.color = (0, 0, 0) # 초기 상태: OFF

# 3. 전원 제어 함수
def toggle_power():
    global is_power_on, color_state 
    is_power_on = not is_power_on 
    
    if is_power_on:
        print("전원이 켜집니다")
    else:
        print("전원이 꺼집니다")
        led.color = (0, 0, 0) # 시스템 종료 시 LED 즉시 소등
        color_state = 0       # 상태 초기화

# 4. 색상 제어 함수
def change_color():
    global color_state
    if is_power_on: # 메인 전원이 켜진 상태에서만 동작
        if color_state == 0: led.color = (1, 0, 0)   # Red
        elif color_state == 1: led.color = (0, 1, 0) # Green
        elif color_state == 2: led.color = (0, 0, 1) # Blue
        elif color_state == 3: led.color = (1, 1, 1) # White
        
        color_state = (color_state + 1) % 4

# 5. 이벤트 핸들러 연결
sw_power.when_pressed = toggle_power 
sw_color.when_pressed = change_color 

# 6. 프로그램 실행 유지
try:
    pause() 
except KeyboardInterrupt:
    pass
```

#### 5. 실행 및 결과
1. 코드를 실행하면 대기 상태가 유지됩니다.
2. **Switch 2**를 누르면 "전원이 켜집니다" 메시지와 함께 시스템이 활성화됩니다.
3. 활성 상태에서 **Switch 1**을 누를 때마다 LED 색상이 변경됩니다.
4. 다시 **Switch 2**를 누르면 모든 LED가 소등되며 시스템이 비활성화됩니다.

---

### **PCF8591을 이용한 ADC/DAC 제어 (I2C 통신)**

본 프로젝트는 라즈베리 파이의 디지털 환경에서 가변저항이나 광센서 같은 아날로그 신호를 처리하기 위해 **PCF8591 AD/DA 컨버터**를 활용하는 실습입니다.

#### 1. I2C 통신 설정 및 환경 구축
라즈베리 파이는 기본적으로 I2C 통신이 비활성화되어 있으므로, 이를 활성화하고 연결 상태를 확인하는 과정이 선행되었습니다.

- **I2C 활성화**: `sudo raspi-config` -> Interface Options -> I2C -> Enable
- **필수 도구 설치**: `sudo apt-get install -y i2c-tools`

#### 2. 하드웨어 연결 상태 확인
I2C 핀(SDA, SCL)에 모듈을 연결한 후, `i2cdetect` 명령어를 통해 PCF8591의 고유 주소인 `0x48`이 정상적으로 인식됨을 확인했습니다.

```bash
# I2C 장치 연결 확인 명령어
i2cdetect -y 1
```

**결과 화면:**
```text
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- 48 -- -- -- -- -- -- -- 
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: -- -- -- -- -- -- -- --                         
```
- **주소 확인**: 40번대 행의 8번 열에서 `48`이 검출되었습니다. 이는 PCF8591 모듈이 시스템에 성공적으로 마운트되었음을 의미합니다.

#### 3. 주요 기능 및 원리
- **ADC (Analog to Digital Converter)**: 가변저항(Potentiometer)이나 조도센서로부터 들어오는 아날로그 전압 신호를 0~255 사이의 디지털 값으로 변환하여 라즈베리 파이로 전달합니다.
- **DAC (Digital to Analog Converter)**: 라즈베리 파이에서 계산된 디지털 값을 다시 아날로그 전압으로 변환하여 LED의 밝기를 부드럽게 조절하는 등의 제어가 가능합니다.
- **I2C 인터페이스**: 단 2개의 신호선(SDA, SCL)을 사용하여 데이터 주소(`0x48`)를 통해 통신하므로 배선이 간결합니다.

#### 4. 파이썬을 이용한 아날로그 센서 데이터 읽기 (CDS 조도 센서)

`i2cdetect`로 확인한 주소(`0x48`)를 바탕으로, 파이썬의 `smbus` 라이브러리를 사용하여 조도 센서(CDS)의 아날로그 값을 읽어오는 실습을 진행했습니다.

##### 4.1 센서 데이터 읽기 코드 (`cds_test.py`)

```python
import smbus
import time

# I2C 버스 1번 사용
bus = smbus.SMBus(1)

# PCF8591의 I2C 주소
ADDR = 0x48

def read_cds():
    # 0x41 제어 바이트 전송: AIN1 채널(조도 센서) 선택
    bus.write_byte(ADDR, 0x41)   
    
    # 더미 리드(Dummy Read): 
    # PCF8591의 특성상 첫 번째 읽기에서는 이전 변환 값을 반환하므로 한 번 읽고 버림
    bus.read_byte(ADDR)          
    
    # 두 번째로 읽은 최신 아날로그 변환 값(0~255) 반환
    return bus.read_byte(ADDR)

try:
    while True:
        # 0.5초마다 조도 센서 값을 읽어와서 터미널에 출력
        val = read_cds()
        print("CDS:", val)
        time.sleep(0.5)

except KeyboardInterrupt:
    print("\n프로그램을 종료합니다.")
```

##### 4.2 코드 동작 원리
- **`smbus.SMBus(1)`**: 라즈베리 파이의 I2C 1번 핀(SDA, SCL)을 통해 통신 라인을 개통합니다.
- **채널 선택 (`0x41`)**: PCF8591 모듈의 4개 입력 채널 중 조도 센서가 연결된 `AIN1` 채널을 활성화하기 위한 제어 신호입니다.
- **더미 리드 (Dummy Read)**: A/D 컨버터 하드웨어 특성상 데이터의 갱신 시차를 맞추기 위해, 한 번의 헛읽기를 수행한 뒤 실시간 데이터를 확보합니다.

##### 4.3 실행 결과
- 프로그램을 실행하면 주변 밝기에 따라 `0` (어두움)에서 `255` (밝음) 사이의 정수 값이 0.5초 간격으로 실시간 출력되는 것을 확인했습니다. 센서를 손으로 가리면 값이 떨어지고, 빛을 비추면 값이 올라갑니다.
