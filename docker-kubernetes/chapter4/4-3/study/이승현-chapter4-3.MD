# 4가지 방법으로 컨테이너 이미지 빌드

## 기본 방법으로 빌드하기

```bash
#1. 이미지 빌드
docker build -t basic-img

#2. 이미지 확인
docker images basic-img

#3. 태그 옵션 추가하여 이미지 생성
docker build -t basic-img:1.0 -t basic-img:2.0

#4. Docker 내용의 일부 변경 후 재빌드 (태그 중복 방지 3.0)
sed -i 's/Application/Development/' Dockerfile
docker build -t basic-img:3.0

#5. 컨테이너 실행
docker run -d -p 60431:80 --name basic-run --restart always basic-img

#6. curl 이용하여 외부 요청에 컨테이너의 응답 여부 확인
curl 127.0.0.1:60431
```

 

## 컨테이너 용량 줄이기

- 불필요한 공간을 점유하는 건 비용 낭비, 성능에 좋지 않은 영향을 줄 수 있습니다.
- 다음은 컨테이너의 이미지 용량을 줄여 빌드하는 방법입니다.
- 기초 이미지가 openjdk에서 GCR에서 제공하는 distoless로 변경됩니다.

```bash
#1. build-in-host.sh 를 실행해 경량화 이미지 빌드
./build-in-host.sh

#2. 새로 생성된 이미지를 조회하여 size 확인
docker imges | head -n 3
```

## 컨테이너 내부에서 컨테이너 빌드하기

- Dockerfile 하나만 빌드하면 컨테이너가 바로 생성되기 때문에 편리합니다.
- 하지만 컨테이너 이미지는 커질수록 비효율적으로 작동합니다.
- 따라서 컨테이너 내부에서 컨테이너를 빌드하는 것은 좋지 않은 방법입니다.

## 최적화해 컨테이너 빌드하기

- 멀티 스테이지 빌드 방법  : 최종 이미지의 용량을 줄이고, 호스트에 어떠한 빌드 도구도 설치하지 않아도 됩니다.
- 멀티 스테이지는 docker-ce 17.06 버전부터 지원됩니다.
- (추가) 댕글링 : 컨테이너 이미지 중에 <none>으로 표시되는 이미지처럼 이름이 없는 이미지를 말합니다.

```bash
#1. 댕글링 제거
docker rmi $(docker images -f dangling=true -q)
```

## 쿠버네티스에서 도커 이미지 구동하기

```bash
#1. 파드 및 디플로이먼트 생성
kubectl run my-pod --image=my-docker-image:tag
kubectl create deployment my-deployment --image=my-docker-image:tag

#2. 외부 노출
kubectl expose deployment my-deployment --type=LoadBalancer --port=80

#3. 생성 확인
kubectl get deployments
```

## 레지스트리 구성하기

- 호스트에서 생성한 이미지를 쿠버네티스에서 이용하려면 모든 노드에 공통으로 접근하는 레지스트리(저장소)가 필요합니다.
- 이때 제약 없는 저장소가 필요하다면 레지스트리를 직접 구축할 수 있습니다.

### Quay (키)

- Red Hat에서 제공하는 컨테이너 레지스트리입니다.
- 주로 기업 환경에서 사용되며, 보안과 이미지 관리를 강조합니다.

### Harbor (하버)

- 클라우드 네이티브 컴퓨팅 재단의 지원을 받는 Project Harbor 에서 오픈소스로 제공하는 레지스트리입니다.

### Nexus Repository (넥서스 레포지터리)

- Sonatype 에서 만든 레지스트리로, 오픈소스로 제공됩니다.
- 도커 이미지 외에도 리눅스 설치 패키지, 자바 라이브러리, 파이썬 라이브러리 등 다양한 형식의 파일을 저장할 수 있어서 여러 형식의 패키지를 하나의 저장소에 관리하려는 사용자에게 적합합니다.

### Docker Registry (도커 레지스트리)

- 도커에서 제공하는 레지스트리입니다.
- 무료로 사용할 수 있고 도커 이미지만 저장할 수 있습니다.
- 기능이 매우 간단해서 개인용이나 테스트용으로 적합합니다.
