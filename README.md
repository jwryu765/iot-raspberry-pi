# iot-raspberry-pi
IoT개발자 과정 라즈베리파이 학습

## 1일차

### 목차
1. [OS 설치 및 부팅](#1-os-설치-및-부팅)
2. [CMD를 통한 원격 접속 및 시스템 업데이트](#2-cmd를-통한-원격-접속-및-시스템-업데이트)
3. [VNC Viewer를 이용한 GUI 접속](#3-vnc-viewer를-이용한-gui-접속)

#### 1. OS 설치 및 부팅

1. **Raspberry Pi Imager 준비**
    - 공식 홈페이지에서 [Raspberry Pi Imager](https://www.raspberrypi.com/software/)를 다운로드 및 설치합니다.
2. **SD 카드에 OS 굽기**
    - MicroSD 카드를 USB 리더기에 꽂아 PC에 연결합니다.
    - Raspberry Pi Imager를 실행하고, 설치할 OS(Raspberry Pi OS)와 저장소(SD 카드)를 선택합니다.
    - 고급 설정(톱니바퀴 아이콘)에서 **SSH 활성화**, **사용자 이름/비밀번호 설정**, **Wi-Fi 설정**을 미리 구성한 후 `쓰기(Write)`를 진행합니다.
3. **라즈베리파이 작동**
    - OS 설치가 완료된 SD 카드를 라즈베리파이에 삽입합니다.
    - 전원 케이블을 연결하여 라즈베리파이를 부팅합니다.

#### 2. CMD를 통한 원격 접속 및 시스템 업데이트

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

#### 3. VNC Viewer를 이용한 GUI 접속

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

#### 4. 한글 패치

- 터미널에서 아래 명령어를 순차적으로 입력하여 한글 환경을 구축합니다.
```c
$ sudo apt install fonts-nanum fonts-nanum-extra     // 폰트 설치
$ sudo apt install fonts-unfonts-core                // 폰트 등록
$ sudo apt install ibus                              // 입력기 설치
$ sudo apt install ibus-hangul                       // ibus 패키지 설치
$ ibus-setup
```
input Method > Add > kroean 검색 > Korean - Hangul 선택 > Preferences > Add > Key 등록

#### 5. 개발 환경 설정(C,C++,Python)

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
#### 6. GPIO 실습

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