### 컨테이너 이미지

- 애플리케이션과 그 실행 환경을 포함한 독립적 패키지로, 컨테이너를 생성하는 데 사용
- 레지스트리(저장소)에 있고, 별도의 레지스트리를 지정하지 않을 경우 도커 허브에서 이미지를 찾음
- `docker search <검색어>`로 원하는 이미지를 검색
- `docker pull <이미지>`로 찾은 이미지를 pull
- 태그를 생략하면 기본적으로 latest(최신)의 이미지를 pull 받게 되며, stable 또는 원하는 버전의 이미지를 pull 받을 수 있음
- 다이제스트(digest)는 이미지의 고유 식별자로, 이미지가 동일한지 검증하는 데 사용
- **이미지는 같은 내용일 경우, 동일한 레이어를 공유하므로 여러 이미지를 내려받더라도 디스크 용량을 효율적으로 사용할 수 있음**

<br/>

### 컨테이너 실행

- `docker run`로 컨테이너를 실행하게 되면 컨테이너를 식별할 수 있는 고유한 ID가 생성됨
- `-d` 옵션을 통해 백그라운드 실행 여부를 결정할 수 있음
- `--restart <옵션>`을 통해 컨테이너 재시작에 대한 설정을 할 수 있음
- `-p <요청받을 호스트 포트>:<연결할 컨테이너 포트>` 옵션을 통해 컨테이너를 실행시키면, 컨테이너 외부에서도 내부로 접속할 수 있음
- `docker ps -f <키>=<값>`을 통해 컨테이너를 필터링하여 검색할 수 있음
- **컨테이너는 변경 불가능한 인프라를 지향하므로, 컨테이너에 적용된 설정을 변경하려면 새로운 컨테이너를 생성해야 함. 이러한 특성으로 배포된 인프라는 배포된 상태를 유지한다는 장점이 있음**

<br/>

### 컨테이너 내부 파일 변경

- docker cp
    - 호스트에 위치한 파일을 구동 중인 컨테이너 내부에 복사하는 방법
    - 컨테이너에 임시로 필요한 파일이 있는 경우 단편적으로 전송하기 위해 사용
- Dockerfile ADD
    - 해당 구문으로 컨테이너 내부로 복사할 파일을 지정하면 이미지를 빌드할 때 지정한 파일이 이미지 내부로 복사됨
    - 사용자가 원하는 파일을 선택해 사용할 수 없다는 단점 존재
- 바인드 마운트
    - 호스트의 파일 시스템과 컨테이너 내부를 연결하여 어느 한 쪽에서 작업한 내용이 양쪽에 동시에 반영되는 방법
    - DB의 데이터 디렉토리와 같은 컨테이너가 바뀌어도 없어지면 안되는 자료를 보존하기 위해 주로 사용됨
- 볼륨
    - 호스트의 특정 디렉토리가 아닌, 도커가 관리하는 볼륨을 컨테이너와 연결함
    - 다른 호스트에서도 도커가 관리하는 볼륨을 함께 사용할 수 있음

<br/>

### 사용하지 않는 컨테이너 정리

- `docker stop <컨테이너 이름 | ID >`로 컨테이너를 정지할 수 있음
- `docker ps -a`로 모든(정지 상태 포함) 컨테이너를 확인하고, `docker rm`로 정지된 컨테이너 삭제 가능
- **컨테이너 삭제 이전에 중지하는 단계가 필요하며, 강제로 삭제할 경우 의도치 않은 삭제 발생 가능**
- 컨테이너를 삭제한 후에는 `docker rmi`로 내려받은 이미지 삭제 가능
- **이미지는 해당 이미지를 기반으로 생성된 모든 컨테이너가 삭제 상태일 경우에만 삭제 가능**
