## docker 설치 방법

### ① docker 공식 사이트를 통한 설치

https://www.docker.com/products/docker-desktop/

### ② Homebrew 이용한 설치

```bash
$ brew install docker
$ docker --version
```

- 위 명령어를 실행합니다.

---

## docker daemon

- Docker Daemon  : Docker 컨테이너를 관리하고 실행하는 역할을 담당하는 백그라운드 서비스입니다.
- 역할 : 컨테이너 관리, 볼륨 관리 등을 수행합니다.
- `docker run`, `docker ps`, `docker stop` 등의 명령어를 통해 Docker Daemon에 명령을 전달받아 처리하는 역할을 합니다.

---

## docker volume

- **Docker 볼륨**: Docker 볼륨은 컨테이너와 호스트 간의 데이터를 지속적으로 저장하고 공유하기 위한 특별한 디렉토리입니다. 볼륨을 사용하면 컨테이너가 삭제되더라도 데이터는 유지됩니다.

```bash
$ docker volume create my_volume
$ docker volume ls
```

- 볼륨 관리 : 볼륨을 생성하고 확인하는 명령어입니다.
