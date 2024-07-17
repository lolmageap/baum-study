### 1. 버추얼 박스 설치

버추얼박스는 리눅스, macOS, 솔라리스, 윈도우를 게스트 운영 체제로 가상화할 수 있는 x86 가상화 소프트웨어입니다.

- [버추얼박스 다운로드](https://www.virtualbox.org/)

### 2. 베이그런트 설치

베이그런트는 사용자의 요구에 맞게 시스템 자원을 할당, 배치, 배포해 두었다가 필요할 때 시스템을 사용할 수 있는 상태로 만들어주는 도구입니다. 이를 프로비저닝이라고 합니다.

- [베이그런트 다운로드](https://developer.hashicorp.com/vagrant/install?product_intent=vagrant)

### 베이그런트 명령어 설명

| 베이그런트 명령 | 설명 |
| --- | --- |
| vagrant init | 프로비저닝을 위한 기초 파일을 생성합니다. |
| vagrant up | Vagrantfile을 읽어 들어 프로비저닝을 진행합니다. |
| vagrant halt | 베이그런트에서 다루는 가상 머신을 종료합니다. |
| vagrant destroy | 베이그런트에서 관리하는 가상 머신을 삭제합니다. |
| vagrant ssh | 베이그런트에서 관리하는 가상 머신에 SSH로 접속합니다. |
| vagrant provision | 베이그런트에서 관리하는 가상 머신에 변경된 설정을 적용합니다. |

### 3. 슈퍼푸티 설치

슈퍼푸티는 윈도우에서 푸티(PuTTY)를 이용한 탭 기능을 제공하는 SSH 클라이언트 관리 도구입니다. 공식 GitHub 페이지에서 최신 버전을 다운로드할 수 있습니다.

- [슈퍼푸티 다운로드](https://github.com/jimradford/superputty/releases/tag/1.5.0.0)
