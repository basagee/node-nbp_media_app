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
* 자체적으로 웹뷰를 사용할 경우 
	* $> sudo npm install -g electron

#### 2. '마을방송' 전용수신 앱 
* $> git clone  http://basagee@gitblit.basagee.tk/r/node/node\_rasp\_broadcast.git 
* $> cd node\_rasp\_broadcast
* 모듈 설치 
	* 개발환경까지 필요하다면 $> npm install --only=dev
	* 실행환경만 필요하다면  $> npm install --only=prod
* 실행 확인 
    * $> npm node-dev
    * 정상 설치되었으면 종료 

#### 3. pjsip-app(방송재생 앱) 설치 
* $> cd node\_rasp\_broadcast
* ./build_media_app.sh
* 실행 확인 
    * $> ./pjsua-armv7l-unknown-linux-gnueabihf
    * 현재 환경설정파일이 없으므로 에러가 나옴. 정상이므로 종료 


