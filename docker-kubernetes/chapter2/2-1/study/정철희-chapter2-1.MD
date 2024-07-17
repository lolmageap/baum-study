## oracle virtual machine
oracle virtual machine은 가상화 소프트웨어이고 하드웨어를 가상화하여 여러 운영체제를 동시에 사용할 수 있게 해줍니다.  
일반적으로 윈도우 운영체제에서 리눅스 운영체제를 사용하고 싶을 때 사용합니다.  

## vagrant
vagrant는 가상화 소프트웨어이고 가상머신을 쉽게 생성하고 관리할 수 있게 해줍니다.  
일반적으로 virtual box나 vmware와 같은 가상화 소프트웨어를 사용하여 가상머신을 생성하고 관리할 때 사용합니다.  

## super putty
기존에 putty는 한 번에 하나의 putty만 실행할 수 있었지만 super putty는 putty를 대체하는 프로그램으로 여러 개의 putty를 한 번에 실행할 수 있게 해줍니다.    
일반적으로 여러 서버에 동시에 접속할 때 사용합니다.  

## mac 에서 docker 설치

### homebrew
mac은 패키지 관리자로 homebrew를 사용합니다.  
homebrew는 패키지를 설치하고 관리할 수 있게 해주는데 이를 통해 docker를 설치할 수 있습니다.  

### 설치 명령어
```shell
$ brew install docker
```
이렇게 하면 docker가 설치됩니다.  
사실 맥을 사용하는 가장 큰 이유중 하나가 homebrew라고 생각합니다.ㅎㅎ;;(진짜 편함)  

### docker 설치 확인
```shell
$ docker --version
```
이렇게 하면 docker 버전이 나옵니다.

### 경로
그리고 이렇게 깔린 도커는 '/usr/local/bin/docker' 경로에 설치됩니다.  
도커에서는 볼륨과 같은 데이터를 저장하는 경로는 '/var/lib/docker' 입니다.
