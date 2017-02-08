#<center>라즈베리 파이에 마을방송 설치 가이드</center>
---------
<br>

## Prerequisite

#### 0. Raspberry pi 3 설치 
    
* rasbian 최신 버전 설치 
* keyboard 설정 
	* USA 또는 Korean
* VNC, ssh 활성화 
	* ssh 설정 시 정상동작 하지 않을 수 있음. 아래와 같이 키 재생성 후 서비스 재시작
        * $> sudo rm /etc/ssh/ssh_host_* && dpkg-reconfigure openssh-server
        * $> sudo systemctl restart sshd
    * 비밀번호 변경 - ssh 사용시 기본 비밀번호는 안됨.
    * 한글설정 
        * LANG : ko_KR.UTF-8 
        * $> sudo apt-get install ibus ibus-hangul ttf-unfonts-core
* reboot system 

#### 1. 마을 방송을 위해서 필요한 package설치 
* $> sudo apt-get install libv4l-dev libx264-dev libssl-dev asterisk libopus-dev
* $> sudo apt-get install alsa-base alsa-utils alsa-tools
* $> sudo apt-get install libasound2 libasound2-plugins libasound2-dev

* $> sudo apt-get install build-essential libfreeimage-dev libopenal-dev libpango1.0-dev libsndfile1-dev libudev-dev libjpeg-dev libtiff5-dev libwebp-dev automake

#### 2. Sound Setting
* alsa를 위해서 관련 모듈들을 로드한다. 이를 위해서 /etc/moduels 파일에 아래 내용을 넣는다.
* $> sudo vi /etc/modules


```
		# added modules for audio
		snd_bcm2835
		# snd_soc_bcm2708
		# snd_soc_bcm2708_i2s
		# bcm2708_dmaengine
		snd-soc-pcm1794a
		snd_soc_rpi_dac
		snd-dummy 
```

* Reboot system

<br>

## Media application compile

#### 1. node, npm 및 electron 설치 
* $> mkdir Development
* $> cd Development
* $> curl -sL https://deb.nodesource.com/setup_6.x -o ~/nodesource_setup.sh
* $> sudo bash ~/nodesource_setup.sh
* $> sudo apt-get install nodejs build-essential
* $> sudo npm install -g electron

#### 2. electron에서 구동되는 '마을방송' 전용수신 앱 
* $> git clone  http://basagee@gitblit.basagee.tk/r/node/node\_rasp\_broadcast.git 
* $> cd node\_rasp\_broadcast
* $> npm install
* 실행 확인 
    * $> npm test
    * 정상 설치되었으면 종료 

#### 3. pjsip-app(방송재생 앱) 설치 
* $> cd node\_rasp\_broadcast
* ./build_media_app.sh
* 실행 확인 
    * $> ./pjsua-armv7l-unknown-linux-gnueabihf
    * 실행후에 sip regi 등의 로그와 메뉴화면이 정상 출력되는지 확인한다. 
    * 정상 설치되었으면 종료 

9 pjsip 설치
	- 압축파일을 해제 
		- $> tar xvfj pjproject-2.6.tar.bz2
	- 컴파일 
		- $> cd pjproject-2.6/
		- $> ./configure -disable-video -with-opus -disable-libwebrtc
		- $> make dep 
		- $> make
	- 설정파일 생성
		- vi $HOME/pjsip.cfg
		- 설정 파일에 아래 내용들을 넣는다.
			--clock-rate=48000 
			--snd-clock-rate=48000 
			--no-vad 
			--realm=*
			--registrar=sip:nbplus.co.kr:45060
			--reg-timeout=3600 
			--stun-srv=town.nbplus.kr
			--username=1003 
			--password=1234 
			--use-turn 
			--turn-srv=town.nbplus.kr:3478
			--turn-user=m2soft 
			--turn-passwd=onechance 
			--log-file=./pjsip2.log 
			--log-level=5 
			--proxy=sip:town.nbplus.kr:45060
			--id=sip:1003@nbplus.co.kr:45060
			--use-ice
			--no-tcp
			--local-port=0
			--dis-codec=speex/16000/1
			--dis-codec=speex/8000/1 
			--dis-codec=iLBC/8000/1 
			--dis-codec=GSM/8000/1 
			--dis-codec=PCMU/8000/1 
			--dis-codec=PCMA/8000/1 
			--dis-codec=G722/16000/1 
			--dis-codec=speex/32000/1 
			--dis-codec=L16/44100/2 
			--dis-codec=L16/8000/1 
			--dis-codec=L16/8000/2 
			--dis-codec=L16/16000/1 
			--dis-codec=L16/16000/2 
			--dis-codec=L16/44100/1 
			--playback-dev 0

	-실행스크립트 작성
		- pjsip.sh 파일을 생성한다. (실행 파일은 임의의로 지정)
		- 파일내에 아래와 같이 넣는다.
			/home/pi/pjproject-2.6/pjsip-apps/bin/pjsua-armv7l-unknown-linux-gnueabihf --config-file="/home/pi/pjsip.cfg"


12. 실행및 연결 테스트
	- 아래와 같이 실행한다. 9번째 단계에서 만든 실행 스크립트를 활용
		$> ./pjsip.sh 
 
	- 실행후 regi하도 대기하게됨. 엔터를 치면 사용가능한 명령들이 나옴.
	- conference URL을 가지고 접속을 한다.
	- pjsip 에서 엔터를 치면 사용가능한 명령셋들이 나옴.
	- 전화를 걸기위해서는 m을 입력 (방송 URL을 입력하라고 나옴)
	- "sip:5ba8f9e1f443d91d525ac76aad914f1cea11c36e@nbplus.co.kr" 와 같이 방송 URL을 입력하고 엔터를 입력하면 연결이됨
	   brc01부분만 변경하면 됨...

실제로 소리가 나오는지 체크하면 됨.
