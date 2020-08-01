# RASPBERRY PI TUTORIAL ADVANCED
This example is about how to install google assistant and magic mirror on raspbian.

## Dependency Versions
1. OS : 2019-04-08-raspbian-stretch-full.img
2. MagicMirror2 : 2.7.1
3. MMM-Hotword : 2.0.1
4. MMM-AssistantMk2 : 2.1.4

## Install
### STEP1. Raspbian installation
1. Download the latest image from the Raspbian website. (2019-04-08-raspbian)
2. Download Rufus to write images to SD card.
3. Run Rufus and select the downloaded image to burn the SD card.

### STEP2. Insert SD card + LCD + Power connection
1. Insert SD card and keyboardㆍmouse dongle into raspberry pi.
2. Connect LCD to raspberry pi on HDMI.

### STEP3. First Boot - PART1
1. After boot is done, connect to the Internet with WIFI
2. Install Remote Desktop Server
```
sudo apt-get install xrdp
```
3. Check the IP address with ipconfig

### STEP3. First Boot - PART2
1. Remote access to raspberry pi. (ID: pi, password raspberry)
2. Update packages
```sh
sudo apt-get update
```

### STEP4. Installing a Magic Mirror
1. Install the Magic Mirror using a script on the Internet
```sh
sudo apt-get install npm
sudo npm install -g npm@latest
bash -c "$(curl -sL https://raw.githubusercontent.com/MichMich/MagicMirror/master/installers/raspberry.sh)"

- 변경되었다.
bash -c "$(curl -sL https://raw.githubusercontent.com/sdetweil/MagicMirror_scripts/master/raspberry.sh)"
```

### STEP5. Installing Magic Mirror Modules
1. Install dependencies
 ```sh
sudo apt-get install libmagic-dev libatlas-base-dev sox libsox-fmt-all mpg321 libasound2-dev
```

2. Install the MMM-Hotword
 ```sh
cd ~/MagicMirror/modules/
git clone https://github.com/eouia/MMM-Hotword.git
cd MMM-Hotword
chmod +x ./installer/install.sh
./installer/install.sh

- follow as below
https://github.com/bugsounet/MMM-Snowboy

cd ~/MagicMirror/modules/
git clone https://github.com/bugsounet/MMM-Snowboy
cd MMM-Snowboy
npm install

```

3. Install MMM-AssistantMk2
 ```sh
cd ~/MagicMirror/modules/
git clone https://github.com/eouia/MMM-AssistantMk2
cd MMM-AssistantMk2
npm install
cd scripts
chmod +x *.sh
cd ..
npm install --save-dev electron-rebuild
./node_modules/.bin/electron-rebuild
 ```

### STEP6. Configure Google Assistant Module
1. Open the Google Action Console and create a new project
```
https://console.actions.google.com
```
2. Open the Google Cloud Platform Console and select the generated project
```
https://console.cloud.google.com
```
3. Search for the Google Assistant API and click Enable.
4. Click CONFIGURE ... of Credentials and put the name and e-mail.
5. Generate Other credentials with the OAuth Client ID in Create Credentials
6. Download generated OAuth client ID in json format
7. Move the downloaded OAuth client ID to modules/MMM-AssistantMk2/credentials.json
```sh
mv ~/Download/cre.... credentials.json
```
8. Run auth_and_test.js to verify the generated client ID
```sh
node auth_and_test.js
```
9. Accept the client verification process and copy and enter your Google account key
10. Move the generated token.json
```sh
mv token.json ./profiles/default.json
```

### STEP7. Edit Google assistant module config
1. Open the Magic Mirror configuration file with TextEditor and modify it with the contents of github
```sh
{
	module: "MMM-Hotword",
	position: "top_right",
	config: {
		chimeOnFinish: null,
		mic: {
			recordProgram: "arecord",
			device: "plughw:1"
		},
		models: [
			{
				hotwords    : "smart_mirror",
				file        : "smart_mirror.umdl",
				sensitivity : "0.5",
			},
		],
		commands: {
			"smart_mirror": {
				notificationExec: {
					notification: "ASSISTANT_ACTIVATE",
					payload: (detected, afterRecord) => {
						return {profile:"default"}
					}
				},
				restart:false,
				afterRecordLimit:0
			}
		}
	}
},
{
	module: "MMM-AssistantMk2",
	position: "top_right",
	config: {
		deviceLocation: {
			coordinates: {
				latitude: 37.5650168, // -90.0 - +90.0
				longitude: 126.8491231, // -180.0 - +180.0
			},
		},
		record: {
			recordProgram : "arecord",  
			device        : "plughw:1",
		},
		notifications: {
			ASSISTANT_ACTIVATED: "HOTWORD_PAUSE",
			ASSISTANT_DEACTIVATED: "HOTWORD_RESUME",
		},
		useWelcomeMessage: "brief today",
		profiles: {
			"default" : {
				lang: "en-US"
			}
		},
	}
},
```
## Install USB Mic. and Speaker
https://github.com/makepluscode/rpi-tips/tree/master/001-bringup-audio-and-mic

```sh
vi ~/.asoundrc
```

```sh
pcm.!default{
  type asym
  playback.pcm{
    type hw
    card 0
  }
  capture.pcm{
    type plug
    slave.pcm "hw:1, 0"
  }
}

ctl.!default{
  type hw
  card 0
}
```

And, change audio output from hdmi to analog.

##  Test
Go to the location where the Magic Mirror is and start the application
```
cd ~/MagicMirror
npm start
```


- snowboy
https://blog.naver.com/PostView.nhn?blogId=naininfo&logNo=221565643529&parentCategoryNo=&categoryNo=11&viewDate=&isShowPopularPosts=true&from=search

https://m.blog.naver.com/PostView.nhn?blogId=cosmosjs&logNo=221328328264&proxyReferer=https%3A%2F%2Fwww.google.com%2F

https://m.blog.naver.com/PostView.nhn?blogId=chandong83&logNo=221147785877&proxyReferer=https%3A%2F%2Fwww.google.com%2F

