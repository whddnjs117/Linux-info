# 명령어 총 정리

## 개요
Linux를 이용 함으로 써 늘 사용하던 명령어의 형식이 기억이 나지 않거나 자주 사용 하는 명령어를 빠르게 찾기위하여 정리하는 페이지

## 명령어 종류
* netstat



### netstat
* 네트워크 연결상태,라우팅 테이블 인터페이스 상태등을 보여주는 명령어

<b>[사용법]</b>
```shell
netstat #옵션
```

<b>[옵션]</b>
|옵션|설명|명령어|
|:------:   |:---|:---:|
|-a         |모든 네트워크 상태 출력|--all|
|-c         |현재 실행 명령을 매 초마다 실행|--continuous|
|-e         |확장된 정보 출력|--extend|
|-g         |멀티캐스트에 대한 그룹별 정보 출력|--groups|
|-i         |인터페이스별 통계값 출력|--interface|
|-l         |대기중인 네트워크|--listening|
|-n         |도메인 주소를 숫자로 출력|--numeric|
|-o         |연결 대기 시간 출력|--timers|
|-p         |PID(프로세스 ID)와 사용중인 프로그램명 출력|--program|
|-r         |라우팅 테이블 출력|--route|
|-s         |프로토콜 요약 정보 출력|--statistics|
|-t         |TCP 프로토콜만 출력|--tcp|
|-u         |UDP 프로토콜만 출력|--udp|
|-V         |버전 출력|--version|
|-w         |RAW 프로토콜만 출력|--raw|
|-A         |프로토콜별로 출력|--protocol|
|-M         |마스커레이딩 정보 출력|--masquerade|

<b>[사용예]</b>
```shell
netstat -a                  #모든 네트워크 연결 확인하기
netstat -at                 #TCP만 확인하기
netstat -au                 #UDP민 확인하기
netstat -nap | grep LISTEN  #LISTEN 상태인 포트만 출력하기
```
---