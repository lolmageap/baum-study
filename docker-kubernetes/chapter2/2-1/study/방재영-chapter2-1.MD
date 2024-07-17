## 버추얼박스 (Virtual Box)
이노테크에서 개발한 가상화 소프트웨어로, 오라클에서 배포합니다. 버추얼박스를 통해 대부분의 운영체제를 게스트 운영체제로 사용할 수 있습니다.

## 베어그런트 (Vagrant)
가상화 환경(Virtual Box, VMware 등)에서 개발 및 테스트 환경을 구축하고 관리하기 위한 도구입니다.

## 푸티 (PuTTY)
터미널 접속 프로그램으로, 다양한 플러그인을 통해 여러 대의 가상 머신에 접근할 수 있습니다.   
또한, 접속 정보를 저장하고 바로 불러와 실행할 수 있는 기능이 있습니다.   
슈퍼푸티(SuperPuTTy)를 통해 한 번에 여러 대의 가상 머신에 접근해서 관리할 수 있습니다.

---

## Mac OS 도커 설치

설치 명령어
```zsh
1. brew install docker
2. brew install --cask docker    // docker-compose, docker-machine 함께 설치
    ㄴ brew에 cask가 없을 경우, brew install cask 먼저 실행
    ㄴ Homebrew 2.6.0 이후 버전부터는 brew install docker --cask 실행
```
- docker-compose: 여러 개의 도커 컨테이너를 하나의 애플리케이션으로 묶어서 쉽게 관리할 수 있는 도구   
- docker-machine: 로컬 또는 클라우드에 Docker 호스트를 쉽게 프로비저닝하고 관리할 수 있는 도구

<br/>

docker 설치 확인 명령어
```zsh
docker -v
```