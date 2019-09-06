# Gattacker, BtleJuice 사용법

블루투스 기기들을 스캔 한 다음 정보를 받아와서 복사.

그 기기와 동일한 가짜 기기를 만드는 것

그 가짜 기기를 가지고 다른 기기와 연결해서 공격이나 정보를 빼온다.

다른 기기는 진짜 기기인 줄 알고 연결해줌



---

두 개의 툴을 사용하기 앞서 우분투는 2개 만들어야 하고 각각 gattacker 와 btleJuice 모두를 설치해야 한다.

1번 우분투는 서버가 되고 2번 우분투는 host가 되는 방식

1번 우분투에는  ~Radio Jl~ 블루투스 동글을 연결

2번 우분투에는 ~Radio CSR~  블루투스 동글을 연결하고 시작한다.

> 메뉴에서 VM -> Removable Devices 들어가면 연결할 수 있다.
>
> 두 개 동시에 연결하면 잘 안되는 경우가 있는데, 그때는 하나씩 연결해준다.

---



root로 로그인 하고 시작할 것.

```cmd
$ sudo su
```

안그래도 될 것 같은데 그러면 명령어 마다 sudo 를 붙여주며 된다.



## Gattacker

gattacker 폴더로 들어간다.

~~~cmd
$ cd /home/사용자/node_modules/gattacker
~~~



##### 1번 우분투 (server)

블루투스가 잡히는지 확인한다.

~~~cmd
$ hciconfig -a
~~~

이때 UP RUNNING 이 보이면 잘 잡힌 것이고 아닐 경우에는 아래와 같이 해주면 된다.

~~~cmd
$ hciconfig hci0(이름 확인할 것) up
~~~



##### 2번 우분투 (host)

*config.env* 파일에서 server 의 ip를 설정해 줘야 한다. 

아래와 같이 수정한다.

~~~visual basic
WS_SLAVE=server의 ip주소
~~~

> 서버의 ip 주소 확인 방법
>
> cmd 창에서 ifconfig 를 입력한 후 inet addr 을 확인한다.



##### 1번 우분투

이제 서버를 켜 줄 단계이다.

~~~cmd
$ node ws-slave.js
~~~



##### 2번 우분투

스캔을 해준다.

~~~cmd
$ node scan.js
~~~

스캔을 한 정보들을 *gattacker/devices* 에 저장한다.

파일 이름은 *mac주소_.adv.json* 으로 탐지된 기기별로 하나씩 생긴다.



하나의 기기를 더 자세하게 탐지하고 싶을 때는 아래 명령어를 입력해준다.

~~~cmd
$ node scan 맥주소
~~~

스캔한 후 정보는 *mac주소.srv.json* 파일로 동일한 위치에 저장된다.



이 정보를 가지고 advertising 해줘야 한다.

advertising은 블루투스 기기 검색? 같은 거에 나오게 하는 것.

입력 위치는 /home/사용자/node_modules/gattacker

~~~cmd
$ node advertise.js -a devices/mac주소_.adv.json -s devices/mac주소.srv.json
~~~

위의 명령어를 통해 할 수 있다.



안드로이드 nRF Connect를 이용해서 블루투스를 켜놓은 기기들을 확인할 수 있다.

가짜 기기 보는 법은 블루투스 동글 ip 로 나옴

[ gattacker 사용법 참고](https://blog.attify.com/hacking-bluetooth-low-energy/)



## BtleJuice

gattacker와 같은 일을 한다. 사용 법이 쉬우나 잘 안됨.



##### 1번 우분투

서버를 켜준다

~~~cmd
$ btlejuice-proxy
~~~



##### 2번 우분투

~~~cmd
$ btlejuice -u 1의ip주소 -w
~~~

위의 명령어를 입력하면 실행이 된다.

웹을 켜서 http://localhost:8080 으로 들어간다.

블루투스 모양 누르면 기기들을 스캔해주고, 나온 기기를 더블클릭 하면 알아서 advertising 해준다.



gattacker와 다른 점은 nRF Connect에 진짜 기기는 안나오게 막고 가짜 기기만 나오게 한다. 하지만 연결이 잘 끊긴다.

[btleJuice 사용법 참고](https://blog.attify.com/btlejuice-mitm-attack-smart-bulb/)



## 블루투스 기기를 스캔했을 때 나오는 정보들

##### Bluetooth Classic

1. 디바이스 이름 - BluetoothDevice.getName()
2. Mac 주소 - BluetoothDevice.getAddress()
3. 블루투스 연결 상태 - BluetoothDevice.getBondtate()
4. 기기 종류 (어떤 기기인지) - BlutetoothDevice.getClass().getMajorDeviceClass()
5. 지원하는 서비스 (ex. 음악) - BluetoothDevice.getClass().hasService(serviceValue)
6. 블루투스의 타입 (BLE인지 그런거) - BluetoothDevice.getType()
7. UUID (기기의 고유 번호) - BluetoothDevice.getUuids()
8. HashCode - BluetoothDevice.hashCode()



##### BLE

1. 디바이스 이름 - BluetoothLEDevice.getName()
2. MAC 주소 - BluetoothLEDevice.getAddress()
3. 블루투스 연결 상태 - BluetoothLEDevice.getBluetoothDeviceBondState()
4. 기기 종류 - BluetoothLEDevice.getBluetoothDeviceMajorClassName()
5. 지원하는 서비스 - BluetoothLEDevice.getBluetoothDeviceKnownSupportedServices()
6. First RSSI (어느정도 거리가 떨어져 있는지. 정확하지는 않음) - BluetoothLEDevice.getFirstRssi()
7. Last RSSI - BluetoothLEDevice.getRssi()
8. First TimeStamp - BluetoothLEDevice.getFirstTimestamp()
9. Last TimeStamp - BluetoothLEDevice.getTimestamp()
10. Running Average RSSI - BluetoothLEDevice.getRunningAverageRssi()