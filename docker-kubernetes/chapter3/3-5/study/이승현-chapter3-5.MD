# 온프레미스에서 로드밸러서를 제공하는 MetalLB

### MetalLB 정의

- 베어메탈(운영체제가 설치되지 않은 하드웨어) 로 구성된 쿠버네티스에서도 로드밸러서를 사용할 수 있게 고안된 프로젝트입니다.

### MetalLB 필요성

- 온프레미스에서 로드밸런서를 사용하려면 내부에 로드밸런서 서비스를 받아주는 구성이 필요한데 이때 사용하는 것이 MetalLB 입니다.
- MetalLB 는 특별한 네트워크 설정이나 구성이 있는 것이 아니라 기존의 L2 네트워크와 L3 네트워크로 로드밸런서를 구현합니다.

```bash
#1. 파드 생성 및 노드당 1개씩 파드가 배포되도록 함
#   - 테스트를 위해 디플로이먼트는 2개
kubectl create deployment <디플로이먼트1> --image=sysnet4admin/echo-hname
kubectl scale deployment <디플로이먼트1> --replicas=3

kubectl create deployment <디플로이먼트2> --image=sysnet4admin/echo-hname
kubectl scale deployment <디플로이먼트2> --replicas=3

#2. 2종류의 파드가 3개씩 총 6개가 배포되었는지 확인함
kubectl get pods

#3. 이전에 정의했던 오브젝트 스펙으로 MetalLB 을 구성함
kubectl apply -f ~/_Book_k8sInfra/ch3/3.3.4/metalLB.yaml

#4. MetalLB vkemrk 5개인지 확인하고, IP 상태도 확인
#   - 5개 : controller 1, speaker 4
kubectl get pods -n metallb-system -o wide

#5. 오브젝트 ConfigMap 을 사용해서 설정
kubectl apply -f ~/_Book_k8sInfra/ch3/3.3.4/metallb-12config.yaml

#6. ConfigMap 생성 확인 명령어
kubectl get configmap -n metallb-system

#7. 옵션 넣어서 ConfigMap 생성 확인
kubectl get configmap -n metallb-system -o yaml

#8. 로드밸런서 서비스 노출
kubectl expose deployment <<디플로이먼트1> --type=LoadBalancer --name=1b-hnmae-svc --port=80
```

### configMap

- 애플리케이션에서 사용되는 설정 데이터를 관리하는 쿠버네티스 객체입니다.
- 애플리케이션이 실행될 때 필요한 설정 정보(예: 데이터베이스 주소, 외부 API 키, 설정 파일의 내용 등)를 configMap에 저장해두고, 이 정보를 파드(Pod)에서 사용할 수 있도록 제공하는 역할을 합니다.

### 데몬셋

- 디플로이먼트의 replicas가 노드 수만큼 정해져 있는 형태라고 할 수 있는데 노드 하나당 파드 한 개만을 생성합니다.
- 노드를 관리하는 파드라면 데몬셋으로 만드는게 가장 효율적입니다.
    - 데몬셋을 사용하면 클러스터 내 모든 노드에 자동으로 파드가 배포됩니다. 이를 통해 각 노드에서 동일한 작업이 수행되도록 보장합니다.
    - 모든 노드에서 동일한 파드를 실행하게 되므로, 클러스터 내 모든 노드에서 동일한 작업(예: 로그 수집, 모니터링)이 일관되게 수행됩니다.

---

# 부하에 따라 자동으로 파드 수를 조절하는 HPA

- 쿠버네티스는 부하량에 따라 디플로이먼트의 파드 수를 유동적으로 관리하는 기능을 제공합니다.
- 이를 HPA 라고 합니다.
- 메트릭 서버 : HPA가 자원을 요청할 때 메트릭 서버를 통해 계측값을 전달받습니다. → HPA 구성을 위해서는 메트릭 서버의 설정이 필요합니다.
- autoscale : 특정 조건이 만족되는 경우에 자동으로 scale 명령이 수행되도록 해주는 명령어입니다.

```bash
#1. 디플로이먼트 생성
kubectl create deployment <디플로이먼트1> --image=sysnet4admin/echo-hname

#2. 위에서 설정했던 Metallb 서비스로 바로 설정 (위의 8번)
kubectl expose deployment <디플로이먼트1> --type=LoadBalancer --name=1b-hnmae-svc --port=80

#3. 로드밸런서 서비스와 부여된 IP 확인
kubectl get services

#4. HPA 작동 전 파드 자원 파악
kubectl top pods

#5. autoscale 로 설정
#   - min : 최소 파드 수/ max : 최대 파드 수/ cpu-percent : cpu 사용량이 50% 넘게 되면 autoscale 진행
kubectl autoscale deployment <디플로이먼트1> --min=1 --max=30 --cpu-percent=50
```
