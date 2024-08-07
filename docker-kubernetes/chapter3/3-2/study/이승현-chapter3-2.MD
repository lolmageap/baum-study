## [ 파드 생성하는 방법 ]

```bash
(1) kubectl create nginx --image=nginx
(2) kubectl run nginx-pod --image=nginx
```

- 생성할 이미지의 이름 : --image=nginx
- 파드 이름 : nginx-pod 의 nginx

## [ (1), (2) 파드 생성 차이점 ]

### 디폴로이먼트

- 쿠버네티스에서 파드의 배포와 관리를 자동화하는 상위 리소스
- 원하는 상태(desired state)를 정의하고, 이를 기반으로 파드를 생성, 업데이트, 삭제 및 롤백하는 등의 작업을 수행합니다.

### 차이점

- `kubectl run`은 명령어의 간결함으로 인해 사용하기 쉽지만, 최신 버전의 쿠버네티스에서는 주로 디플로이먼트를 생성합니다.
- `kubectl create deployment`는 명확하게 디플로이먼트를 생성하는 데 사용됩니다.

---

## [ 오브젝트란 ]
![img.jpg](../image/이승현-image1.jpg)
- 파드와 디플로이먼트는 스펙과 상태 등의 값을 가지고 있습니다.
- 이러한 값을 가지고 있는 파드와 디플로이먼트를 개별 속성을 포함해 부르는 단위를 뜻합니다.
- 쿠버네티스는 여러 유형의 오브젝트를 제공합니다. (ex : 기본 오브젝트 등등등)

## [ 기본 오브젝트 ]

- 기본 오브젝트(4) : 파드, 네임스페이스, 볼륨, 서비

### 파드

- 쿠버네티스에서 실행되는 최소 단위
- 웹 서비스를 구동하는데 필요한 최소 단위
- 독립적인 공간과 사용가능한 IP 를 가집니다
- 파드 하나 당 1개 이상의 컨테이너를 갖고 있기 때문에 여러 기능을 묶어 하나의 목적으로 사용할 수 있습니다
- 그러나 대부분 1개의 파드에 1개의 컨테이너를 적용합니다

### 네임스페이스

- 쿠버네티스 클러스터에서 사용되는 리소스들을 구분해 관리하는 그룹입니다.

### 볼륨

- 파드가 생성될 때 파드에서 사용할 수 있는 디렉터리를 제공합니다
- 기본적으로 파드는 영속되는 개념이 아니라 제공되는 디렉터리로 임시로 사용합니다
- 파드가 사라지더라도 저장과 보존이 가능한 디렉터리를 볼륨 오브젝트를 통해 생성하고 사용할 수 있습니다

### 서비스

- 파드는 클러스터 내에서 유동적이기 때문에 접속 정보가 고정일 수 없습니다
- 파드 접속을 안정적으로 유지할 수 있도록 서비스를 통해 내/외부로 연결됩니다
- 서비스는 새로 파드가 생성될 때 부여되는 새로운 IP를 기존에 제공하던 기능과 연결해줍니다
- 쉽게말해 서비스는 쿠버네티스 외부에서 내부로 접속할 때, 파드의 생존여부 상관없이 이를 논리적으로 연결해줍니다

## [ 고급 오브젝트 ]

### 고급 오브젝트란?

- 기본 오브젝트(파드, 네임스페이스, 볼륨, 서비스) 만으로도 쿠버네티스를 사용할 수 있지만
- 애플리케이션의 복잡도가 증가할 때 이를 좀더 효율적으로 관리하기 위해 디플로이먼트와 같은 고급 오브젝트가 도입되었습니다
- 디플로이먼트는 파드와 리플리카셋 등의 기능을 조합하여, 애플리케이션의 배포, 업데이트, 스케일링 등을 자동으로 관리합니다

### 리플리카셋?

- 리플리카셋은 이전의 레플리케이션컨트롤러에서 발전한 형태입니다
- 레플리케이션컨트롤러는 파드의 복제본을 관리하는 역할을 했으며,
- 리플리카셋은 이를 더욱 향상시켜 더 유연하고 강력한 기능을 제공합니다
- 지정된 수의 파드가 항상 실행 중임을 보장합니다. 예를 들어, 레플리카셋에 3개의 파드를 선언하면, 항상 3개의 파드가 실행되도록 유지합니다.
- 파드가 실패하거나 삭제되면, 레플리카셋이 자동으로 새로운 파드를 생성하여 원하는 수를 유지합니다.

### 디플로이먼트 명령어

- 생성 명령어

```bash
# 생성 명령어
kubectl create deployment <deployment-name> --image=<image-name>

# 예시
kubectl create deployment nginx-deployment --image=nginx
```

- 삭제 명령어

```bash
# 삭제 명령어
kubectl delete deployment <deployment-name>

# 예시
kubectl delete deployment nginx-deployment
```

- 작업 후 파드 또는 디플로이먼트의 상태 확인 명령어

```bash
kubectl get pods
```

---

## [ 레플리카셋으로 파드 수 관리하기 ]
![img.jpg](../image/이승현-image2.jpg)
- 레플리카셋은 파드 수를 보장하는 기능만 제공합니다
- 따라서 롤링 업데이트 기능 등이 추가된 디플로이먼트를 사용해 파드 수를 관리하는 것을 권장합니다

1. 배포된 파드 상태 확인.

```bash
kubectl get pods
```

1. 파드를 scale 명령으로 3개로 증가시킴. (파드 수를 3개로 맞추는 옵션)

```bash
kubectl scale pod <pod-name> --replicas=<개수>
```

1. 디플로이먼트를 아래 명령어를 통해 파드의 수를 3개로 만듦.

```bash
kubectl scale deployment <deployment-name> --replicas=<개수>
```

1. scale 명령으로 추가 생성된 2개의 파드 확인.

```bash
kubectl get pods
```

1. 파드가 정상적으로 워커 노드에 적용되고 , IP가 부여되었는지 아래 명령어로 확인.

```bash
kubectl get pods -o wide
```

---

## [ 스펙을 지정해 오브젝트 생성하기 ]

- create 명령어에서는 replicas 옵션을 사용할 수 없고
- scale 은 이미 만들어진 디플로이먼트에서만 사용할 수 있습니다.
- 그렇다면 디플로이먼트를 생성하면서 한꺼번에 여러 개의 파드를 만들고자 할 때, 어떻게 해야할까,,! 요!

⇒ 오브젝트 스펙!

```yaml
apiVersion: v1 # API 버전
kind: Deployment # 오브젝트 종류
metadata:
  name: echo-hname
  labels:
    app: nginx
spec:
  replicas: 3 # 몇 개의 파드를 생성할지를 결정
```

- 사용 가능한 API 버전을 확인하는 방법

```yaml
kubectl api-version
```

---

## [ apply 를 이용한 오브젝트 생성 관리 ]

1. 오브젝트 스펙을 yaml 파일로 작성
2. 아래 명령어를 통해 클러스터에 적용하여 오브젝트 생성

```yaml
kubectl apply -f echo-hname.yaml
```

1. 파드 확인

```yaml
kubectl get pods
```
