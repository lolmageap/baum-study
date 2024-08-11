## 쿠버네티스에서의 서비스

쿠버네티스에서 "서비스"는 클러스터 내부에 있는 애플리케이션이 외부와 통신할 수 있도록 하는 방법을 말합니다. 서비스는 클러스터 외부로의 연결을 관리하고, 로드 밸런싱, 보안, 트래픽 분배 등의 기능을 제공합니다. 이는 클러스터 내부의 파드와 클러스터 외부의 요청 간의 연결을 효과적으로 관리하기 위해 필수적입니다.

## NodePort 서비스: 가장 간단한 외부 연결 방법

**NodePort** 서비스는 쿠버네티스 클러스터 내부에 있는 애플리케이션을 외부에서 접근할 수 있도록 하는 가장 간단한 방법입니다. NodePort 서비스를 설정하면 클러스터 내의 모든 워커 노드의 특정 포트가 열리며, 해당 포트를 통해 들어오는 요청이 NodePort 서비스로 전달됩니다. 이후 서비스는 해당 요청을 적절한 파드로 전달하여 처리합니다.

### NodePort 서비스 동작 흐름

1. **사용자** → 2. **워커 노드** → 3. **NodePort 서비스** → 4. **Pod**
    
    ![img1.jpg](../image/최범진-1.jpg)

### NodePort 서비스 설정 및 접속 절차

1. **파드 생성 및 배포 확인**:
    - `kubectl create deployment <파드이름> —image=<이미지 이름>`
    - `kubectl get pod`
2. **NodePort 서비스 생성 및 확인**:
    - `kubectl create -f <오브젝트 스펙>`
    - `kubectl get services`
3. **워커 노드 IP 확인**:
    - `kubectl get nodes -o wide`
4. **접속 확인**: 워커 노드의 IP와 NodePort의 포트 번호를 통해 접속합니다.

### 부하 분산 테스트 (로드밸런서 기능)

1. **파드 수 증가**:
    - `kubectl scale deployment <파드이름> —replicas=<파드 개수>`
    - `kubectl get pods`

NodePort 서비스의 오브젝트 스펙에 명시된 파드와 디플로이먼트 이름이 동일할 경우, NodePort 서비스는 동일한 파드로 간주합니다.

### NodePort 서비스 생성 (expose 명령어 사용)

1. **디플로이먼트에 NodePort 서비스 노출**:
    - `kubectl expose deployment <파드이름> —type=NodePort —name=<서비스이름> —port=<포트번호>`
    - `kubectl get services`
2. **포트 번호로 접속 확인**: 생성된 포트 번호로 서비스에 접속하여 확인합니다.

## 인그레스: 사용 목적별로 연결

NodePort 서비스는 여러 개의 디플로이먼트를 사용할 때 비효율적일 수 있습니다. 이러한 상황에서 **인그레스(Ingress)** 를 사용하여 여러 디플로이먼트를 한 번에 관리하고, 고유한 주소 및 경로에 따라 다양한 응답을 제공할 수 있습니다. 인그레스는 L4/L7 로드밸런서와 보안 인증서 관리 기능도 포함합니다.

![img2.jpg](../image/최범진-2.jpg)
### 인그레스 설정 절차

1. **테스트용 디플로이먼트 배포**:
    - `kubectl create deployment <파드이름> —image=<이미지이름> X2`
    - `kubectl get pods`
2. **인그레스 컨트롤러 설치**:
    - `kubectl apply -f <오브젝트 스펙 경로>`
    - `kubectl get pods -n <네임스페이스>`
3. **인그레스 설정 및 적용**:
    - 경로와 작동을 정의한 설정을 적용합니다.
    - `kubectl apply -f <설정정보 경로>`
        
        ```yaml
        apiVersion: networking.k8s.io/v1beta1
        kind: Ingress
        metadata:
          name: ingress-nginx
          annotations:
            nginx.ingress.kubernetes.io/rewrite-target: /
        spec:
          rules:
          - http:
              paths:
              - path:
                backend:
                  serviceName: hname-svc-default // 외부에서 주소 값과 노트포드를 가지고 들어오는 해당 서비스와 연결된 파드로 넘김
                  servicePort: 80
              - path: /ip
                backend:
                  serviceName: ip-svc // ip를 추가한 주소 값은 해당 서비스와 연결된 파드로 접속하게 함
                  servicePort: 80
              - path: /your-directory
                backend:
                  serviceName: your-svc
                  servicePort: 80
        ```
        
4. **인그레스 컨트롤러 외부 노출**:
    - `kubectl apply -f <인그레스 오브젝트 스펙>`
    - `kubectl get services -n <인그레스 컨트롤러 이름>`
5. **NodePort 서비스와의 연동 및 확인**:
    - `kubectl expose deployment <파드이름> —name=<서비스이름> —port=<포트번호>`
    - `kubectl get services`

## 로드밸런서

NodePort 서비스를 통한 외부 접근은 비효율적일 수 있습니다. 이를 개선하기 위해 클라우드 환경에서는 **로드밸런서** 서비스를 사용합니다. 로드밸런서는 외부 트래픽을 클러스터의 파드로 효율적으로 분산시키며, 클라우드 서비스 업체가 제공하는 로드밸런서 기능을 통해 간단하게 구현할 수 있습니다.

1. **로드밸런서 서비스 노출**:
    - `kubectl expose deployment <파드이름> —type=LoadBalancer —name=<서비스이름>`
    - `kubectl get services <서비스이름>`

로드밸런서를 사용하면 클러스터의 파드들이 외부 트래픽을 효율적으로 처리할 수 있도록 지원합니다.
  
![img3.jpg](../image/최범진-3.jpg)
