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

### 1. 라즈베리 파이 RGB LED 및 이중 스위치 제어 시스템

- 개요
    - **목적**: 하드웨어 이벤트 제어 및 상태 관리 로직 이해
    - **주요 기능**:
    - **전원 스위치 (Switch 2)**: 전체 시스템의 활성화/비활성화 토글
    - **색상 스위치 (Switch 1)**: 전원이 켜진 상태에서 LED 색상을 순차적으로 변경 (빨강 → 초록 → 파랑 → 하양)

- 하드웨어 구성 및 회로 설계

    ![alt text](image.png)

    - 주요 부품
        | 부품 | 수량 | 비고 |
        | :--- | :--- | :--- |
        | 라즈베리 파이 | 1 | |
        | RGB LED | 1 | 공통 양극 (Common Anode) 타입 |
        | 택트 스위치 | 2 | 전원용, 색상변경용 |
        | 저항 (220$\Omega$) | 3 | LED 보호용 |
        | 저항 (10k$\Omega$) | 2 | 스위치 풀업(Pull-up) 회로용 |
        | 브레드보드 및 점퍼선 | 12 | |

    - 핀 맵 (Pin Mapping)
        | 기능 | 핀 번호 (BCM) | 물리 핀 번호 |
        | :--- | :--- | :--- |
        | **Switch 1 (Color)** | GPIO 2 | 3번 핀 |
        | **Switch 2 (Power)** | GPIO 4 | 7번 핀 |
        | **LED (Red)** | GPIO 17 | 11번 핀 |
        | **LED (Green)** | GPIO 27 | 13번 핀 |
        | **LED (Blue)** | GPIO 22 | 15번 핀 |

- 핵심 원리 및 개념

    - 공통 양극(Common Anode) RGB LED
        - 본 실습에 사용된 LED는 긴 다리가 `3.3V` 전원에 연결되는 방식입니다.
        - **작동 원리**: GPIO 핀에서 `LOW(0)` 신호를 주어 전위차를 만들 때 전류가 흐르며 불이 켜집니다.
        - **코드 적용**: `gpiozero` 라이브러리의 `active_high=False` 설정을 통해 로직을 직관적으로 반전시켜 처리합니다.

    - 풀업(Pull-up) 저항 회로
        - 스위치가 눌리지 않았을 때 입력 핀이 떠 있는 상태(Floating)를 방지하기 위해 10k$\Omega$ 저항을 전원에 연결했습니다.
        - **평상시**: `HIGH(1)` 신호 유지
        - **클릭 시**: 전류가 GND로 흐르며 `LOW(0)` 신호 발생

    - 디바운싱 (Debouncing)
        - 스위치 내부 금속판의 미세한 떨림으로 인해 한 번의 클릭이 여러 번 입력된 것으로 인식되는 '채터링' 현상을 방지하기 위해 코드상에서 `bounce_time=0.1` 옵션을 적용하여 하드웨어적 한계를 소프트웨어적으로 보완했습니다.

- 소프트웨어 구현

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

- 실행 및 결과
    1. 코드를 실행하면 대기 상태가 유지됩니다.
    2. **Switch 2**를 누르면 "전원이 켜집니다" 메시지와 함께 시스템이 활성화됩니다.
    3. 활성 상태에서 **Switch 1**을 누를 때마다 LED 색상이 변경됩니다.
    4. 다시 **Switch 2**를 누르면 모든 LED가 소등되며 시스템이 비활성화됩니다.

---

### 2. PCF8591을 이용한 ADC/DAC 제어 (I2C 통신)

- 라즈베리 파이의 디지털 환경에서 가변저항이나 광센서 같은 아날로그 신호를 처리하기 위해 **PCF8591 AD/DA 컨버터**를 활용하는 실습입니다.

#### I2C 통신 설정 및 환경 구축

- 라즈베리 파이는 기본적으로 I2C 통신이 비활성화되어 있으므로, 이를 활성화하고 연결 상태를 확인하는 과정이 선행되었습니다.

- **I2C 활성화**: `sudo raspi-config` -> Interface Options -> I2C -> Enable
- **필수 도구 설치**: `sudo apt-get install -y i2c-tools`

#### 하드웨어 연결 상태 확인

- I2C 핀(SDA, SCL)에 모듈을 연결한 후, `i2cdetect` 명령어를 통해 PCF8591의 고유 주소인 `0x48`이 정상적으로 인식됨을 확인했습니다.

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

- `i2cdetect`로 확인한 주소(`0x48`)를 바탕으로, 파이썬의 `smbus` 라이브러리를 사용하여 조도 센서(CDS)의 아날로그 값을 읽어오는 실습을 진행했습니다.

    - 센서 데이터 읽기 코드 (`cds_test.py`)

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

    - 코드 동작 원리
        - **`smbus.SMBus(1)`**: 라즈베리 파이의 I2C 1번 핀(SDA, SCL)을 통해 통신 라인을 개통합니다.
        - **채널 선택 (`0x41`)**: PCF8591 모듈의 4개 입력 채널 중 조도 센서가 연결된 `AIN1` 채널을 활성화하기 위한 제어 신호입니다.
        - **더미 리드 (Dummy Read)**: A/D 컨버터 하드웨어 특성상 데이터의 갱신 시차를 맞추기 위해, 한 번의 헛읽기를 수행한 뒤 실시간 데이터를 확보합니다.

    - 실행 결과
        - 프로그램을 실행하면 주변 밝기에 따라 `0` (어두움)에서 `255` (밝음) 사이의 정수 값이 0.5초 간격으로 실시간 출력되는 것을 확인했습니다. 센서를 손으로 가리면 값이 떨어지고, 빛을 비추면 값이 올라갑니다.

## 3일차

### 1. 스마트 가로등 시스템 (CDS & PCF8591)

- 조도 센서(CDS)를 통해 주변 밝기를 측정하고, 어두워지면 자동으로 LED 가로등을 점등하는 시스템입니다. 라즈베리파이의 디지털 핀으로는 아날로그 값(밝기)을 직접 읽을 수 없으므로 **PCF8591 AD/DA 컨버터**를 사용했습니다.

#### 하드웨어 연결 (Wiring)
| 컴포넌트 | 라즈베리파이 핀 | 비고 |
| :--- | :--- | :--- |
| **PCF8591 VCC** | 3.3V / 5V | 전원 공급 |
| **PCF8591 GND** | GND | 접지 |
| **PCF8591 SDA/SCL** | GPIO 2, 3 (I2C) | 데이터 통신 |
| **CDS 센서** | PCF8591 AIN0 | 아날로그 입력 |
| **RGB LED (R, G, B)** | GPIO 17, 27, 22 | 가로등 출력 (PWM 제어) |

#### 핵심 코드
```python
import smbus
from gpiozero import RGBLED
from time import sleep

# I2C 주소 및 LED 설정
DEVICE_ADDRESS = 0x48
streetlight_led = RGBLED(red=17, green=27, blue=22)
bus = smbus.SMBus(1)

def read_brightness():
    bus.write_byte(DEVICE_ADDRESS, 0x40) # AIN0 선택
    bus.read_byte(DEVICE_ADDRESS)        # 더미 리드
    return bus.read_byte(DEVICE_ADDRESS) # 실제 밝기 값 반환

try:
    while True:
        brightness_value = read_brightness()
        # 밝기가 일정 수준 이하로 떨어지면 노란색 불빛 점등
        if brightness_value > 150: 
            streetlight_led.color = (1, 1, 0) # Yellow
        else:
            streetlight_led.color = (0, 0, 0) # OFF
        sleep(0.5)
except KeyboardInterrupt:
    streetlight_led.off()
```

---

### 2. 부저를 이용한 전자 피아노 및 자동 연주

- 부저(Buzzer)의 주파수를 PWM(Pulse Width Modulation) 방식으로 제어하여 음계를 생성하고, 키보드 입력(`readchar`)을 통해 실시간으로 연주하는 시스템입니다.

#### 핵심 코드 (전자 피아노)
```python
import readchar
from gpiozero import PWMOutputDevice
import threading
from time import sleep

piano_buzzer = PWMOutputDevice(21)
note_frequencies = {
    '1': 261.63, '2': 293.66, '3': 329.63, '4': 349.23,
    '5': 392.00, '6': 440.00, '7': 493.88, '8': 523.25
}

def play_tone(frequency):
    piano_buzzer.frequency = frequency
    piano_buzzer.value = 0.5
    sleep(0.15)
    piano_buzzer.value = 0

print("숫자 키 1~8로 연주하세요 (종료: q)")
try:
    while True:
        input_key = readchar.readkey()
        if input_key in note_frequencies:
            # 렉 방지를 위한 멀티쓰레딩 적용
            threading.Thread(target=play_tone, args=(note_frequencies[input_key],)).start()
        elif input_key == 'q':
            break
finally:
    piano_buzzer.close()
```

---

### 3. HC-SR04 초음파 센서 기반 후진 경보 시스템

- 초음파의 반사 시간을 측정하여 거리를 계산하고, 장애물과의 거리에 따라 경고음의 간격이 비례해서 짧아지는 기본 자동차 후진 보조 시스템을 구현했습니다.

![alt text](image-1.png)

#### 하드웨어 연결 (Wiring)
| HC-SR04 핀 | 라즈베리파이 핀 | 비고 |
| :--- | :--- | :--- |
| **VCC** | 5V | 필수 (3.3V 연결 시 측정 거리 저하) |
| **Trig** | GPIO 23 | 초음파 발사 신호 |
| **Echo** | GPIO 24 | 반사 신호 수신 (전압 분배 권장) |
| **GND** | GND | 접지 |

#### 핵심 코드 (기본 거리 비례 경보)
```python
from gpiozero import DistanceSensor, PWMOutputDevice
from time import sleep

# 센서 세팅 (Trig=23, Echo=24) 및 고음역대 경보 부저 세팅
ultrasonic_sensor = DistanceSensor(echo=24, trigger=23)
warning_buzzer = PWMOutputDevice(21)
warning_buzzer.frequency = 1000 

try:
    while True:
        current_distance_cm = ultrasonic_sensor.distance * 100

        if current_distance_cm > 50:
            warning_buzzer.value = 0
            sleep(0.1)
        
        elif 5 <= current_distance_cm <= 50:
            # 거리에 비례하여 경고음 대기 시간 계산 (가까울수록 짧아짐)
            beep_delay_time = current_distance_cm / 100
            
            warning_buzzer.value = 0.5 
            sleep(0.05)
            warning_buzzer.value = 0   
            sleep(beep_delay_time)
            
        elif current_distance_cm < 5:
            # 5cm 미만 충돌 위험 시 연속음 발생
            warning_buzzer.value = 0.5
            sleep(0.1)

except KeyboardInterrupt:
    warning_buzzer.value = 0
```

---

### 4. I2C LCD(1602) 정보 출력 시스템

- I2C 통신을 이용하여 16x2 문자 LCD에 텍스트를 출력합니다. 하드웨어 제약상 한글 출력은 불가능하여 로마자(English)를 사용하였으며, 반복문을 활용한 구구단 애니메이션을 구현했습니다.

#### 핵심 코드 (LCD 구구단)
```python
from RPLCD.i2c import CharLCD
from time import sleep

lcd = CharLCD(i2c_expander='PCF8574', address=0x27, port=1, cols=16, rows=2)

try:
    for dan in range(2, 10):
        lcd.clear()
        lcd.cursor_pos = (0, 0)
        lcd.write_string(f"=== {dan} Dan ===")
        
        for num in range(1, 10):
            result_message = f"{dan} x {num} = {dan * num}"
            lcd.cursor_pos = (1, 0)
            # 잔상 방지를 위해 16칸 왼쪽 정렬 출력
            lcd.write_string(result_message.ljust(16))
            sleep(0.8)
finally:
    lcd.clear()
    lcd.close(clear=True)
```

### 5. 통합 사물 감지 시스템 (Object Sensor System)

- 초음파 센서, LCD, 부저를 하나로 통합한 시스템입니다. 실시간으로 사물과의 거리를 측정하여 LCD에 미터(m) 단위로 표시하고, 거리가 가까워질수록 부저의 경고 템포가 빨라지는 지능형 감지기입니다.

#### 하드웨어 연결 (Wiring)
| 컴포넌트 | 라즈베리파이 핀 | 비고 |
| :--- | :--- | :--- |
| **I2C LCD** | GPIO 2, 3 (SDA/SCL) | 거리 정보 출력 |
| **HC-SR04 Trig** | GPIO 23 | 초음파 발사 |
| **HC-SR04 Echo** | GPIO 24 | 초음파 수신 |
| **Buzzer** | GPIO 21 | 거리 비례 경고음 |

#### 핵심 코드 (통합 시스템)
```python
from gpiozero import DistanceSensor, PWMOutputDevice
from RPLCD.i2c import CharLCD
from time import sleep

# 하드웨어 장치 초기화
ultrasonic_sensor = DistanceSensor(echo=24, trigger=23)
warning_buzzer = PWMOutputDevice(21)
information_lcd = CharLCD(i2c_expander='PCF8574', address=0x27, port=1, cols=16, rows=2)

warning_buzzer.frequency = 1000

try:
    while True:
        distance_meters = ultrasonic_sensor.distance
        distance_cm = distance_meters * 100
        
        # LCD 출력 (제목: Object Sensor)
        information_lcd.cursor_pos = (0, 0)
        information_lcd.write_string("Object Sensor".ljust(16))
        
        information_lcd.cursor_pos = (1, 0)
        information_lcd.write_string(f"Dist: {distance_meters:.2f} m".ljust(16))

        # 거리별 부저 제어
        if distance_cm > 50:
            warning_buzzer.value = 0
            sleep(0.1)
        elif 5 <= distance_cm <= 50:
            beep_delay = distance_cm / 300
            warning_buzzer.value = 0.5 
            sleep(0.05)
            warning_buzzer.value = 0   
            sleep(beep_delay)
        elif distance_cm < 5:
            warning_buzzer.value = 0.5
            sleep(0.1)

except KeyboardInterrupt:
    pass
finally:
    warning_buzzer.value = 0
    information_lcd.clear()
    information_lcd.close(clear=True)
```
## 4일차

### 1. 스텝모터와 스위치를 이용한 방향 전환 구현
- 버튼(스위치)의 물리적 입력 상태를 감지하여 스텝모터의 회전 방향을 제어하는 기초 실습입니다.

    ![alt text](image-2.png)

    - **사용 부품:** 라즈베리파이, 스텝모터(28BYJ-48), 모터 드라이버(ULN2003), 택트 스위치
    - **주요 기능:**
        - GPIO를 통한 스위치 PULL-UP/PULL-DOWN 입력 감지
        - 스위치 입력에 따른 스텝모터 정회전(시계방향) 및 역회전(반시계방향) 구동
        - 모터 핀 시퀀스(Sequence) 제어의 이해
#### 핵심 코드

```python
from gpiozero import DigitalOutputDevice, Button
import time

# 모터 GPIO 설정
IN1 = DigitalOutputDevice(17)
IN2 = DigitalOutputDevice(27)
IN3 = DigitalOutputDevice(22)
IN4 = DigitalOutputDevice(23)

# 스위치 설정 (GPIO 26번 사용 예시)
# pull_up=True: 내부 풀업 저항 사용 (버튼 안 누르면 내부적으로 HIGH 유지)
# bounce_time=0.05: 50ms 동안의 기계적 떨림(바운싱) 무시
button = Button(26, pull_up=True, bounce_time=0.05)

# 방향 상태를 저장할 전역 변수 (True: 정방향, False: 역방향)
is_forward = True

# 스위치가 눌렸을 때 실행될 함수 (콜백 함수)
def toggle_direction():
    global is_forward
    is_forward = not is_forward # 상태를 반전 (True <-> False)
    
    if is_forward:
        print("방향 전환: 정방향 🔄")
    else:
        print("방향 전환: 역방향 🔃")

# 스위치가 눌리면(when_pressed) toggle_direction 함수를 실행하도록 연결
button.when_pressed = toggle_direction


# Half-step 시퀀스 (더 부드러움)
SEQ = [
    [1,0,0,0],
    [1,1,0,0],
    [0,1,0,0],
    [0,1,1,0],
    [0,0,1,0],
    [0,0,1,1],
    [0,0,0,1],
    [1,0,0,1],
]

pins = [IN1, IN2, IN3, IN4]

def step(delay=0.002):
    for seq in SEQ:
        for pin, val in zip(pins, seq):
            if val:
                pin.on()
            else:
                pin.off()
        time.sleep(delay)

def step_reverse(delay=0.002):
    for seq in reversed(SEQ):
        for pin, val in zip(pins, seq):
            if val:
                pin.on()
            else:
                pin.off()
        time.sleep(delay)

# 메인 실행부
try:
    print("모터 구동 시작 (스위치를 누르면 방향이 바뀝니다)")
    
    # 지정된 횟수만큼 도는 것이 아니라, 무한히 돌면서 상태를 체크합니다.
    while True: 
        if is_forward:
            step()         # is_forward가 True면 정방향 스텝 진행
        else:
            step_reverse() # is_forward가 False면 역방향 스텝 진행

except KeyboardInterrupt:
    print("\n프로그램을 종료합니다.")

finally:
    # 프로그램이 종료될 때 모든 핀의 전력을 차단 (모터 과열 방지)
    for pin in pins:
        pin.off()
```

### 2. DHT11 온습도 센서 및 LCD 디스플레이 출력
- 환경 데이터를 수집하는 센서와 정보를 시각화하는 디스플레이 장치를 연동하는 실습입니다.

    - **사용 부품:** 라즈베리파이, DHT11 온습도 센서, I2C Char LCD
    - **주요 기능:**
        - DHT11 센서를 통한 실시간 온도(°C) 및 습도(%) 데이터 파싱
        - I2C 통신 프로토콜을 이용한 16x2 또는 20x4 LCD 제어
        - 수집된 데이터를 포맷팅하여 LCD 화면에 실시간으로 갱신 및 출력
#### 핵심코드

```python
import time
import board
import adafruit_dht
from RPLCD.i2c import CharLCD

# ==========================================
# 1. 하드웨어 설정
# ==========================================
# DHT11 설정 (DATA 핀: GPIO4 = 물리핀 7번)
dht = adafruit_dht.DHT11(board.D4)

# LCD 설정 (I2C 주소는 보통 0x27 또는 0x3F 입니다)
# 주소가 다르면 화면이 안 켜질 수 있으니, 안 켜지면 0x3F로 바꿔보세요.
lcd = CharLCD('PCF8574', 0x27)
lcd.clear()

# ==========================================
# 2. 메인 실행부
# ==========================================
try:
    print("DHT11 온습도 측정 및 LCD 출력 시작...")
    
    # 시작할 때 LCD에 환영 문구 띄우기 (옵션)
    lcd.cursor_pos = (0, 0) # 첫 번째 줄, 첫 번째 칸
    lcd.write_string("DHT11 Sensor")
    lcd.cursor_pos = (1, 0) # 두 번째 줄, 첫 번째 칸
    lcd.write_string("Starting up...")
    time.sleep(2)
    lcd.clear()

    while True:
        try:
            # 센서 값 읽기
            temp = dht.temperature
            humi = dht.humidity

            if temp is not None and humi is not None:
                # 콘솔에 출력
                print(f"Temp: {temp:.1f}C / Humi: {humi:.1f}%")
                
                # LCD에 출력하기 위해 화면 포맷팅
                # (16칸을 넘지 않게 글자 수를 조절해 줍니다)
                lcd.cursor_pos = (0, 0)
                lcd.write_string(f"Temp: {temp:.1f} C   ") # 뒤에 공백을 넣어 이전 글자 덮어쓰기
                lcd.cursor_pos = (1, 0)
                lcd.write_string(f"Humi: {humi:.1f} %   ")

            else:
                print("Failed to measure")

        except RuntimeError as e:
            # DHT11은 타이밍에 민감해서 가끔 읽기 실패가 정상적으로 발생합니다.
            # 이때 LCD 화면은 굳이 지우지 않고, 콘솔에만 에러를 찍고 넘어갑니다.
            print("Failed, restart:", e)

        # DHT11은 최소 2초의 간격을 두는 것이 좋습니다.
        time.sleep(2)

except KeyboardInterrupt:
    print("프로그램 종료")

finally:
    # 프로그램 종료 시 센서와 LCD를 안전하게 정리합니다.
    dht.exit()
    lcd.clear()
    lcd.write_string("System Off")
```

### 3. 종합 실습: 스마트 디지털 도어락 시스템
- 앞선 실습들을 응용하여, 상용 도어락과 동일한 UX(사용자 경험)를 제공하는 복합 보안 시스템을 구현했습니다.

    - **사용 부품:** 4x4 매트릭스 키패드, 수동 부저(PWM), RGB LED(Common Anode), 스텝모터, I2C Char LCD
    - **주요 기능:**
    - **비밀번호 로직:** 6자리 고정 비밀번호 체계, `*` 버튼으로 입력 초기화, `#` 버튼으로 확인 및 승인. 파일(`password.txt`) 기반의 비밀번호 저장 및 변경 기능(`A` 버튼).
    - **피드백 시스템 (시각/청각):**
        - 키패드 입력 시 짧은 비프음 출력.
        - 인증 실패 시 LCD 경고 문구, 수동 부저 경고음, RGB LED 빨간색 1초간 점등.
        - 인증 성공 시 초록색 LED 점등 및 모터 회전.
    - **자동 잠금 및 멀티태스킹 (Threading):** 
        - 비밀번호가 맞으면 문(스텝모터)이 열리고 5초 대기 후 자동으로 다시 잠김.
        - 파이썬 `threading` 모듈을 활용하여 모터가 회전하고 대기하는 약 11초의 전 구간 동안 멈춤 없이 테마곡(BGM) 반복 재생.
#### 핵심코드
```python
import os
import time
import threading # 동시 작업을 위해 추가
from gpiozero import DigitalOutputDevice, Button, RGBLED, OutputDevice, PWMOutputDevice
from RPLCD.i2c import CharLCD
from time import sleep

# ==========================================
# 1. 하드웨어 핀 설정
# ==========================================
COL_PINS = [21, 20, 16, 12]
ROW_PINS = [5, 6, 13, 19]
KEYS = [
    ['1', '2', '3', 'A'],
    ['4', '5', '6', 'B'],
    ['7', '8', '9', 'C'],
    ['*', '0', '#', 'D']
]
rows = [DigitalOutputDevice(pin, active_high=True, initial_value=False) for pin in ROW_PINS]
cols = [Button(pin, pull_up=False) for pin in COL_PINS]

led = RGBLED(red=22, green=17, blue=27, active_high=False)
buzzer = PWMOutputDevice(23)

motor_pins = [OutputDevice(18), OutputDevice(24), OutputDevice(25), OutputDevice(8)]
step_sequence = [
    [1,0,0,1], [1,0,0,0], [1,1,0,0], [0,1,0,0],
    [0,1,1,0], [0,0,1,0], [0,0,1,1], [0,0,0,1]
]

lcd = CharLCD('PCF8574', 0x27)
PW_FILE = "password.txt"

# ==========================================
# 2. 기능 제어 함수
# ==========================================
def scan_keypad():
    for row_index, row in enumerate(rows):
        for r in rows: r.off()
        row.on()
        sleep(0.001)
        for col_index, col in enumerate(cols):
            if col.is_pressed:
                return KEYS[row_index][col_index]
    return None

def move_stepper(steps, delay=0.0015):
    direction = 1 if steps > 0 else -1
    for _ in range(abs(steps)):
        for step in range(8):
            seq_idx = step if direction == 1 else (7 - step)
            for pin_idx in range(4):
                motor_pins[pin_idx].value = step_sequence[seq_idx][pin_idx]
            sleep(delay)
    for pin in motor_pins: pin.off()

def play_key_beep():
    buzzer.frequency = 1200
    buzzer.value = 0.5
    sleep(0.05)
    buzzer.value = 0

# ==========================================
# 3. 멜로디 함수 (구간별 시간 맞춤)
# ==========================================

def melody_worker(type):
    """모터가 도는 동안 배경에서 실행될 멜로디 쓰레드"""
    if type == "open":
        # 약 3초 동안 올라가는 음계
        notes = [523, 587, 659, 698, 784, 880, 988, 1046]
        for note in notes:
            buzzer.frequency = note
            buzzer.value = 0.4
            sleep(0.38) # 합쳐서 약 3초
        buzzer.value = 0
    elif type == "close":
        # 약 3초 동안 내려가는 음계
        notes = [1046, 988, 880, 784, 698, 659, 587, 523]
        for note in notes:
            buzzer.frequency = note
            buzzer.value = 0.4
            sleep(0.38) # 합쳐서 약 3초
        buzzer.value = 0

def play_waiting_music(duration):
    """문이 열려 있는 5초 동안의 배경음악"""
    notes = [523, 0, 659, 0, 784, 0, 880, 0]
    start_time = time.time()
    while time.time() - start_time < duration:
        for note in notes:
            if time.time() - start_time >= duration: break
            if note == 0:
                buzzer.value = 0
                sleep(0.2)
            else:
                buzzer.frequency = note
                buzzer.value = 0.2 # 대기음은 조금 더 작게
                sleep(0.2)
    buzzer.value = 0

def play_fail_sound():
    notes = [784, 659]
    for note in notes:
        buzzer.frequency = note
        buzzer.value = 0.5
        sleep(0.2)
        buzzer.value = 0
        sleep(0.05)

# ==========================================
# 4. 도어락 메인 로직
# ==========================================

def get_password(prompt_text):
    lcd.clear()
    lcd.write_string(prompt_text)
    pwd = ""
    last_k = None
    while True:
        k = scan_keypad()
        if k is not None and k != last_k:
            last_k = k
            if k == '#' and len(pwd) == 6:
                play_key_beep()
                break
            if k == '*':
                play_key_beep()
                pwd = ""
                lcd.clear()
                lcd.write_string(prompt_text)
                continue
            if k in ['0','1','2','3','4','5','6','7','8','9'] and len(pwd) < 6:
                play_key_beep()
                pwd += k
                lcd.cursor_pos = (1, 0)
                lcd.write_string('*' * len(pwd))
        if k is None: last_k = None
        sleep(0.05)
    return pwd

def load_password():
    if os.path.exists(PW_FILE):
        with open(PW_FILE, "r") as f: return f.read().strip()
    return None

def save_password(pwd):
    with open(PW_FILE, "w") as f: f.write(pwd)

try:
    led.color = (0, 0, 0)
    master_password = load_password()
    
    if not master_password:
        master_password = get_password('Set 6-digit PW')
        save_password(master_password)
        lcd.clear()
        lcd.write_string('Saved!')
        sleep(2)

    input_password = ""
    lcd.clear()
    lcd.write_string('Enter PW + #')
    last_key = None

    while True:
        key = scan_keypad()
        if key is not None and key != last_key:
            last_key = key
            
            if key == 'A':
                play_key_beep()
                check = get_password('Old PW + #')
                if check == master_password:
                    master_password = get_password('New PW + #')
                    save_password(master_password)
                    lcd.clear()
                    lcd.write_string('Changed!')
                    sleep(2)
                else:
                    lcd.clear()
                    lcd.write_string('Wrong!')
                    led.color = (1, 0, 0)
                    play_fail_sound()
                    sleep(1)
                    led.color = (0, 0, 0)
                input_password = ""
                lcd.clear()
                lcd.write_string('Enter PW + #')
                continue

            if key == '*':
                play_key_beep()
                input_password = ""
                lcd.clear()
                lcd.write_string('Enter PW + #')
                continue

            if key == '#':
                if len(input_password) != 6: continue
                
                play_key_beep()
                lcd.clear()
                if input_password == master_password:
                    # --- [1단계: 열기] ---
                    lcd.write_string('Opening...')
                    led.color = (0, 1, 0)
                    # 노래 쓰레드 시작
                    t1 = threading.Thread(target=melody_worker, args=("open",))
                    t1.start()
                    # 노래가 나오는 동안 모터 회전
                    move_stepper(256)
                    t1.join() # 노래 끝날 때까지 대기
                    
                    # --- [2단계: 대기] ---
                    lcd.clear()
                    lcd.write_string('Door Opened')
                    play_waiting_music(5) # 5초간 대기 노래
                    
                    # --- [3단계: 닫기] ---
                    lcd.clear()
                    lcd.write_string('Closing...')
                    # 노래 쓰레드 시작
                    t2 = threading.Thread(target=melody_worker, args=("close",))
                    t2.start()
                    # 노래가 나오는 동안 모터 회전
                    move_stepper(-256)
                    t2.join() # 노래 끝날 때까지 대기
                    
                    led.color = (0, 0, 0)
                else:
                    lcd.clear()
                    lcd.write_string('Wrong PW!')
                    led.color = (1, 0, 0)
                    play_fail_sound()
                    sleep(1)
                    led.color = (0, 0, 0)

                input_password = ""
                lcd.clear()
                lcd.write_string('Enter PW + #')
                continue
                
            if key in ['B', 'C', 'D']: continue

            if len(input_password) < 6:
                play_key_beep()
                input_password += key
                lcd.cursor_pos = (1, 0)
                lcd.write_string('*' * len(input_password)) 

        if key is None: last_key = None
        sleep(0.05)

except KeyboardInterrupt:
    print("\n종료")
finally:
    lcd.clear()
    for r in rows: r.off()
    for p in motor_pins: p.off()
    led.off()
    buzzer.off()
```
---

### 트러블 슈팅 및 학습 주안점
- **RGB LED 핀 맵핑:** 공통 양극(Common Anode) 타입 LED의 전압 특성과 핀 반전 문제를 진단하고, 하드웨어 배선 및 소프트웨어 핀 번호를 교정하여 정확한 색상(Red, Green)을 출력함.
- **비동기 처리(Threading):** `time.sleep()` 블로킹으로 인해 모터 구동과 부저 멜로디가 동시에 실행되지 않는 문제를 `threading.Event()`와 병렬 쓰레드를 통해 해결하여 부드러운 작동 흐름을 완성함.
- **주파수 제어:** 수동 부저의 주파수(Frequency)와 재생 시간(Duration)을 조절하여 기계적인 삐- 소리가 아닌, 실제 음악(테트리스, 엘리제를 위하여 등)을 연주하는 기능을 구현함.