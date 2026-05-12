# iot-ros2-2026
IoT 개발자과정 ROS2 기반 Raspberry Pi 학습 리포지토리

## 1일차

### ROS2

- ROS(Robot Operating System)는 운영체제가 아니라 로봇 개발을 위한 오픈소스 미들웨어 플랫폼
- 로봇 제어, 센서 데이터 처리, 노드 간 통신 등의 기능을 쉽게 구현할 수 있도록 다양한 라이브러리와 도구 제공
- Ubuntu 환경에서 주로 사용되며, 자율주행·로봇 제어·IoT·임베디드 시스템 개발 등에 활용

### Ubuntu 원격 접속 특징

- Raspberry Pi OS(라즈비안)는 초기 설정 단계에서 Wi-Fi 및 SSH 설정을 미리 구성하면 모니터 없이 원격 접속 가능
- Ubuntu Server/Desktop 환경에서는 초기 네트워크 및 계정 설정을 위해 최초 1회 모니터 연결이 필요한 경우가 있음
- 초기 설정 완료 후에는 SSH 또는 VNC를 이용한 원격 접속 가능

### Raspberry Pi Ubuntu 설치

- Raspberry Pi Imager를 이용하여 Ubuntu 이미지 설치
- Raspberry Pi Ubuntu 초기 설정 진행
- 언어(Language)는 영어(English)로 설정

### 계정 정보

- User : `rpi`
- Hostname : `rospi`
- Password : `ubuntu`

---

### Ubuntu 패키지 업데이트

```bash
sudo apt update
sudo apt upgrade
```

업데이트 완료 후 재부팅 진행

---

### SSH 설치 및 PuTTY 원격 접속

SSH 설치

```bash
sudo apt install openssh-server
```

SSH 상태 확인

```bash
systemctl status ssh
```

IP 주소 확인

```bash
ip a
```

확인한 IP 주소를 이용하여 PuTTY로 접속

> SSH는 네트워크를 통해 원격으로 시스템에 접속하기 위한 서비스이며,
> PuTTY와 같은 프로그램을 이용하여 접속 가능

PuTTY 접속 후 다시 패키지 최신화 진행

```bash
sudo apt update
sudo apt upgrade
```

---

### Ubuntu 버전 확인

```bash
lsb_release -a
```

---

### ROS2 Jazzy 설치

ROS2 공식 문서 참고

- https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html

설치 과정

- Locale 설정 진행
- ROS2 Desktop Install 설치
- ROS Base 대신 ROS2 Desktop Install 선택
- Environment Setup까지 완료

---

### ROS2 Node & Package 개념

- ROS2는 여러 개의 Node가 서로 통신하며 동작하는 구조
- Node는 하나의 기능을 수행하는 실행 단위
- Package는 여러 Node와 코드, 설정 파일 등을 관리하는 단위

예시:

- ROS : 회사
- Package : 부서
- Node : 직원

즉, Node에 기능을 구현하고 Package 단위로 관리하며 서로 통신하여 시스템을 구성

---

### RealVNC 설치

PuTTY 접속 후 Downloads 폴더에서 설치 진행

```bash
cd Downloads

wget https://downloads.realvnc.com/download/file/vnc.files/VNC-Server-7.16.0-Linux-ARM64.deb

chmod u+x VNC-Server-7.16.0-Linux-ARM64.deb

sudo apt install ./VNC-Server-7.16.0-Linux-ARM64.deb
```

`chmod u+x` 적용 후 `ls` 명령으로 확인 시 실행 권한이 추가되어 파일 색상이 변경됨

---

### Wayland 비활성화

```bash
sudo nano /etc/gdm3/custom.conf
```

아래 행의 주석 제거

```bash
#WaylandEnable=false
```

↓

```bash
WaylandEnable=false
```

---

### Dummy Display 설정

필요 패키지 설치

```bash
sudo apt install xserver-xorg-video-dummy xinit
```

X11 폴더 확인

```bash
ls /etc
```

`X11` 폴더가 존재하면 설정 파일 생성

```bash
sudo nano /etc/X11/xorg.conf
```

설정 내용 작성

```conf
Section "Device"
        Identifier "Configured Video Device"
        Driver "dummy"
EndSection

Section "Monitor"
        Identifier "Configured Monitor"
        HorizSync 28-80
        VertRefresh 48-75
        Modeline "1920x1080" 172.80 1920 2040 2248 2576 1080 1081 1084 1118
EndSection

Section "Screen"
        Identifier "Default Screen"
        Device "Configured Video Device"
        Monitor "Configured Monitor"
        DefaultDepth 24
        SubSection "Display"
                Depth 24
                Modes "1920x1080"
        EndSubSection
EndSection
```

---

### VNC 서비스 실행

```bash
sudo systemctl enable vncserver-x11-serviced.service

sudo systemctl start vncserver-x11-serviced.service

sudo systemctl status vncserver-x11-serviced.service
```

`running` 상태이면 정상 동작

---

### HDMI 설정

```bash
sudo nano /boot/firmware/config.txt
```

`[all]` 부분에 아래 내용 추가

```bash
hdmi_force_hotplug=1
hdmi_group=2
hdmi_mode=82
```

설정 후 재부팅

```bash
sudo reboot
```

---

### VNC 접속

- VNC Viewer 실행
- PuTTY에서 확인한 IP 주소 입력 후 접속

검은 화면이 나타나는 경우

```bash
sudo systemctl restart gdm3
```

---

### Python 가상환경 생성

```bash
mkdir -p venvs

cd venvs

sudo apt install python3.12-venv

python3 -m venv --system-site-packages .venv
```

생성 확인

```bash
ls -a
```

가상환경 활성화

```bash
source .venv/bin/activate
```

---

### JupyterLab 설치

```bash
python -m pip install --upgrade pip

python -m pip install ipykernel

pip3 install jupyterlab
```

설치 완료 후 VMware 재시작

---

### JupyterLab 실행

```bash
source venvs/.venv/bin/activate

cd venvs

jupyter lab
```

실행 시 웹 브라우저에서 JupyterLab 접속 가능

---

### JupyterLab 외부 접속 설정

설정 파일 생성

```bash
jupyter lab --generate-config
```

설정 파일 수정

```bash
nano ~/.jupyter/jupyter_lab_config.py
```

아래 내용 추가

```python
c.ServerApp.ip = '0.0.0.0'
c.ServerApp.port = 7890
c.ServerApp.open_browser = False
```

---

### Windows에서 JupyterLab 접속

브라우저에서 접속

```text
http://192.168.0.8:7890/
```

토큰 확인

```bash
jupyter server list
```

- Token : 표시된 토큰 번호 입력
- Password : `ubuntu`