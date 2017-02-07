
## Prerequisite

0. Raspberry pi 3 설치 
	* 

1. 마을 방송을 위해서 필요한 package설치 
	- 아래 명령들을 순서대로 수행하여 설치
	- $> sudo apt-get install libv4l-dev libx264-dev libssl-dev asterisk libopus-dev
	- $> sudo apt-get install alsa-base alsa-utils alsa-tools
	- $> sudo apt-get install libasound2 libasound2-plugins libasound2-dev

	- $> sudo apt-get install build-essential libfreeimage-dev libopenal-dev libpango1.0-dev libsndfile1-dev libudev-dev libjpeg-dev libtiff5-dev libwebp-dev automake

2. Sound Setting
	- alsa를 위해서 관련 모듈들을 로드한다. 이를 위해서 /etc/moduels파일에 아래 내용을 넣는다.
	- $> sudo vi /etc/modules
		# added modules for audio
		snd_bcm2835
		# snd_soc_bcm2708
		# snd_soc_bcm2708_i2s
		# bcm2708_dmaengine
		snd-soc-pcm1794a
		snd_soc_rpi_dac
		snd-dummy 
 
11. 모듈 로드 및 세팅 완료를 위해서 시스템을 재부팅한다. 
	- $> sudo reboot



9 pjsip 설치
	- 압축파일을 해제 
		- $> tar xvfj pjproject-2.6.tar.bz2
	- 컴파일 
		- $> cd pjproject-2.6/
		- $> ./configure –disable-video –with-opus –disable-libwebrtc
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
	- "sip: brc01@nbplus.co.kr" 와 같이 방송 URL을 입력하고 엔터를 입력하면 연결이됨
	   brc01부분만 변경하면 됨...

실제로 소리가 나오는지 체크하면 됨.