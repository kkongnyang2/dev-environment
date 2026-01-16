## 입력기 설정

작성자: kkongnyang2 작성일: 2025-06-04

---
### 입력기 시스템 어떻게 되어있나요

[1] 하드웨어 | 키보드 자체
 ↓
[2] 커널 계층 | 키 입력 처리 (/dev/input/event로 전달) | libinput
 ↓
[3] 레이아웃 계층 | keycode → keysym 변환 | XKB
 ↓
[4] 입력기 프레임워크 | 문자 조합 | IBus, Fcitx
 ↓
[5] 입력기 엔진 | 각 언어별 로직 | ibus-hangul
 ↓
[6] 디스플레이 서버 | 최종 입출력 전달 백엔드(무대) | Wayland, X11
 ↓
[7] 데스크톱 환경 | 화면 구성 등 사용자 UI(겉모습) | Gnome

* gnome은 마스터 컨트롤러 같은 존재. 각 애들한테 명령을 내림.
* ibus는 그저 입력기 프로그램이기 때문에 gnome의 요청없이 그 상위계층에 입출력을 전달할 수가 없다.

```
$ echo $XDG_SESSION_TYPE         #디스플레이 서버 확인
$ echo $XMODIFIERS               #프레임워크 확인
$ ibus list-engine               #엔진 종류 확인
$ ps aux | grep -E 'ibus|fcitx|uim' | grep -v grep   #입력기 데몬 확인
$ xev                            #키 매핑 확인
$ sudo libinput debug-events     #입력 흐름 확인
```
* `ps aux`: 모든 사용자와 프로세스 출력
* `grep`: 찾기
* `-E`: 또는
* `-v`: 제외

---
### ibus-hangul 설치

```
$ sudo apt install ibus-hangul
재부팅
설정 들어가 hangul 키보드 추가 후 기존 키보드 삭제
```
* 더 필요시 토글키 설정, ibus-setup 들어가 reference에 hangul 추가

ibus-hangul 설치 여부 확인
```
$ dpkg -l | grep ibus-hangul
$ ibus list-engine
```
* `dpkg -l`: 현재 설치된 모든 패키지 목록
* `grep`: 검색

---
### 키 매핑 변경

RALT를 HNGL로 설정해야 함
```
$ cd /usr/share/X11/xkb/keycodes
$ sudo cp evdev evdev.bak   # 백업 파일 남겨놓기. 되돌리려면 sudo cp evdev.bak evdev
$ sudo nano evdev           # 수정
```

원본:
```
<RALT> = 108;
<HNGL> = 130;
```

수정:
```
<HNGL> = 108;
```

* 108번 하드웨어 키를 `HNGL`로 지정함으로써, 우측 ALT를 한영키로 대체
* 원본은 전부 before로 표기해두니 필요하면 ctrl+f로 찾아라

---
### z(keycode 52) 저수준 차단하기

기존 키맵 복사
```
$ sudo cp /usr/share/X11/xkb/symbols/us ~/.XkbSymbols_us
$ gedit ~/.XkbSymbols_us
```

원본
```
key <AB03> { [ z, Z ] };;
```

수정
```
key <AB03> { [ NoSymbol, NoSymbol ] };
```

반영
```
$ setxkbmap -I$HOME -layout us
```

* `setxkbmap`: X 맵 설정
* `-I$HOME` : 커스텀 심볼 파일을 홈 디렉토리에서 우선 찾게함
* `-layout us` : 레이아웃이 us 파일을 기반


### 윈도우에서 마우스

```
vxe R1 pro max 마우스는 2.4K 수신기와 4K 수신기가 있음.
형태는 다섯가지나 가능
2.4K 수신기
선+젠더+2.4K 수신기(거리 늘려주기용)
선+4K 수신기
유선
블루투스
난 2.4K 수신기를 사용하고 있고 변경이 필요하면 https://hub.atk.pro/ 가서 원하는 수신기를 꼽고 원래껀 빼고 페어링해주면 됨.

로지텍 Pro X superlight 마우스는 2.4K 수신기가 있음
형태는 네가지가 가능
2.4K 수신기
선+젠더+2.4K 수신기(거리 늘려주기용)
유선
블루투스
변경이 필요하면 logitech G hub 소프트웨어에서 설정.
```
