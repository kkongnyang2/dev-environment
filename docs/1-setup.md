## 우분투 설치

작성자: kkongnyang2 작성일: 2025-06-04

---
### 윈도우 설치

```
usb FAT32로 포맷해서 준비
윈도우 설치마법사 다운 후 실행해서 usb 굽기
노트북 제조사 홈페이지 노트북 모델명 검색
인텔이면 IRST 드라이버 다운 후 압축 풀어 usb에 같이 넣어주기
노트북에 꽂고 전원키기
f2 연타해서 바이오스에서 부팅 순서 usb 1순위로

바로 윈도우 설치 페이지 뜸
마우스 연결
파티션 설정 창에서 ssd 인식 안될거임
드라이버 로드 - 아까 usb에 넣어놓은 IRST VMD Controller
이제 ssd가 보이면 할당되지 않은 공간과 windriver 파티션이 뜰거임
윈도우 우분투 듀얼부팅을 위해 190000MB 파티션 만들기
그럼 시스템/예약/주/할당x/windriver 이렇게 파티션이 될거임 
주에 윈도우 설치 시작
키보드는 default (101 type1) - 이게 뭐냐? RALT는 한글키로, RCRTL은 한자키로 쓰는 키보드 배열.
네 키보드가 RALT 따로, 한글키 따로 되어 있지 않다면 이걸 선택.
네트워크 없이 설치
완료
```
* 만약 바이오스에서 vmd controller를 껐다면 ssd도 바로 인식이 되고 irst 드라이버 필요x

### 드라이브 설치

```
랜선 꼽고 설정에 윈도우 업데이트 들어가서 전부 업데이트하면 알아서 맞는 드라이브 설치해줌
아니면 노트북 제조사별 전용앱을 설치해서 거기서 맡겨도 됨
```


### 우분투 설치

```
ubuntu-22.04-desktop-amd64.iso 다운받기
rufus 다운로드
rufus로 해당 iso 이미지를 usb에 굽기(영구 공간 0, GPT, UEFI 추천)
포맷 형식은 FAT32(대중적), NTFS(윈도우 전용), exFAT(안정성 취약), ext4(리눅스 전용) 중에 보통 FAT32
* mbr/gpt. mbr은 파티션 적은 구버전(bios호환), gpt는 신버전(ufei호환).
* 영구저장공간. usb로 디스크 설치 없이 live 부팅할때 변경사항을 usb에 저장하는 할당 공간.
* FAT32. 32bit주소체계 파일 주소록. 대부분의 EFI System Partition이 사용.

F2으로 부팅 페이지 들어가기
usb 선택
try or install 우분투 클릭
* try : live 부팅. 디스크 설치 없이 usb에서 램에 올려서 사용.

설정 쭉 하고 간소화 버전으로 선택하고 드라이버는 두개(업데이트 다운, 써드파티 소프트웨어) 다 체크.
드라이버 업데이트는 아까 윈도우랑 비슷하고 써드파티는 third-party 외부 소프트웨어라 보면됨 필요한거 있으면 알아서 깔아줌 주로 nvidia

파티션은 수동 파티셔닝 선택
남겨둔 unallocated 영역을 ext4로 포맷하고 설치
우분투 설치가 완료되면 usb 빼고 엔터
```
* 만약 secure boot를 켰다면 서드파티 드라이버 설치 중 UEFI Secure boot를 위해 MOK 비밀번호 설정 필요함 41080000. 재부팅 후 서드파티 드라이버 활성화 화면에서 Enroll MOK, continue, yes 선택 후 아까 비밀번호 입력. 그럼 설치가 되고 reboot를 택해주면 됨


### 점검

파티션 확인
```
$ lsblk -f
```
* `-f` : 파일 시스템 정보

패키지 최신상태 확인
```
$ sudo apt update && sudo apt upgrade -y
```
* &&는 명령어 연속. -y는 자동 yes

드라이버 확인
```
$ sudo ubuntu-drivers devices
```
* 설치가능한 목록. 아무것도 안뜨면 굿.


### 듀얼부팅 시간 맞추기

```
우분투에서 timedatectl set-local-rtc 1 --adjust-system-clock
되돌리려면 timedatectl set-local-rtc 0 --adjust-system-clock
```


### 기본 명령어

```
ctrl+alt+t : 터미널

~ : home. /home의 줄임말
cd : 상위 디렉토리로
cd ~/... : 디렉토리 이동
ls -al : 디렉토리 목록 보기
pwd : 현재 디렉토리 경로 출력
mkdir : 디렉토리 생성
rm : 파일/디렉토리 삭제
which : 위치

cp .. .. : 복사
mv .. .. : 이동/이름변경
touch myfile.txt
echo "Hello, world!" > hello.txt
nano myfile.txt
cat > myfile.txt
Hello, this is my first file.
cat >> example.txt
cat example.txt

top : 실시간 자원 사용
ps : 실행중인 프로세스 보기

sudo apt update : APT 패키지 목록을 갱신
sudo apt upgrade : 설치된 패키지 업그레이드
sudo apt install : 패키지 설치
sudo apt remove : 패키지 삭제
sudo apt murge
sudo : 관리자 권한

명령어1 ; 명령어2 : 두개 실행
명령어1 && 명령어2 : 앞 명령어가 성공했을때만 실행
명령어1 || 명령어2 : 앞 명령어가 실패했을때만 실행

nano 에 들어가서는 선택 Ctrl+6, 복사 Alt+6
```

### 명령어 지속성 구분

영구 설정
```
/etc 에 적어두면 영구
/etc/default/grub 영구
systemctl enable 영구
virsh --config 영구
virsh edit
usermod -aG libvirt,kvm $USER 영구
```
* 다만 영구는 보통 재부팅해야 함

부팅동안(런타임)
```
/sys, /proc 에 echo 하면 이번 부팅 동안만
systemctl start 이번 부팅 동안만
systemctl stop 이번 부팅 동안만
modprobe vfio-pci
```

터미널/세션동안
```
/usr/bin에서 실행만 하면 터미널·세션 한정
newgrp libvirt 현재 셸에만
virsh --live 지금 실행 중인 vm에만
```

보통 파일은 영구, 명령은 휘발성이다


### 파티션 조정
```
윈도우 가서
window+x 눌러 디스크 관리 들어가 윈도우 파티션 우클 누르고 볼륨 축소
가능한 만큼 최대한 축소해 unallocated 파티션 만들기
ubuntu 22.04 desktop.iso 파일 다운
usb 꽂고 rufus 실행해 해당 파일로 gpt(uefi)로 만들기

부팅 중 f10으로 들어가 usb로 부팅
라이브 세션에서 터미널 들어가 sudo gparted로 파티션 재배치.
우분투 파티션 누르고 resize/move를 눌러 빈 공간 없이 채워주기
apply
라이브 세션 종료

다시 우분투로 돌아와서 용량 늘어난거 확인
```

### 용량 정리

```
$ apt-mark showmanual     # apt를 통해 깐 패키지만 보여줌
$ sudo apt remove qemu-system-arm
$ snap list               # snap을 통해 깐 앱 보여줌
$ sudo snap remove rpi-imager
```
