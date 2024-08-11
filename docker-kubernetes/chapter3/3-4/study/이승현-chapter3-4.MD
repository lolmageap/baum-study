# 쿠버네티스 연결을 담당하는 서비스

- 쿠버네티스에서 사용하는 서비스는 개념이 조금 다릅니다.
- 쿠버네티스에서 서비스란, 외부에서 쿠버네티스 클러스터에 접속하는 방법을 말합니다.
- 외부 사용자가 파드를 이용하는 방법을 설명하고자 합니다.

# 가장 간단하게 연결하는 노드포트

- 노드포트 서비스는 외부에서 쿠버네티스 클러스터의 내부에 접속하는 가장 쉬운 방법입니다.
- 아래는 노드포트 서비스 과정입니다.

<aside>
📎 1️⃣ 모든 워커노드의 특정포트를 연다

2️⃣ 특정포트로 오는 모든 요청을 노드포트 서비스로 전달한다.

3️⃣ 노드포트 서비스는 해당 업무를 처리할 수 있는 파드로 요청을 전달한다.

</aside>

- 아래는 노드포트 서비스 실제 실행 과정입니다.

```bash
# 1. 디플로이먼트로 파드 생성하기
#    - 이떄 이미지는 sysnet4admin 계정에 있는 echo-hname 을 사용함
kubectl create deployment np-pods --image=sysnet4admin/echo-hname

# 2. 배포된 파드 확인
kubectl get pods

# 3. kubectl create 로 노드포트 서비스 생성
kubectl create -f ~/_Book_k8sInfra/ch3/3.3.1/nodeport.yaml

# 3-1. nodeport.yaml 일부
아래 따로 정리

# 4. 노드포트 서비스로 생성한 np-svc 서비스 확인
kubectl gt services

# 5. 쿠버네티스 클러스터의 워커 노드 IP 확인
#    - 배포된 파드에 모든 노드의 노드포트를 통해 외부에서도 접속할 수 있음을 확인
kubectl get nodes -o wide
		
```

```yaml
# 3-1. nodeport.yaml
spec: 
	selector:
		app: np-pods
	ports:
		-name : http
		protocol: TCP
		port: 80
		targetPort: 80
		nodePort: 30000
	type: NodePort
```

- 기존 디플로이먼트에 파드가 1개가 생성되어있다가 3개로 증가했을 때 로드밸러서는 가능합니다.
- 여러 방법이 있지만, 오브젝트 스펙에 적힌 (nodeport.yaml 의 app: np-pods) 디플로이먼트의 이름을 확인하여 동일할 경우 같은 파드라고 간주하기 떄문입니다.
- 아래는 expose 로 노드포트 서비스를 생성하는 방법입니다.

```bash
# 1. expose 명령어를 사용해 서비스로 내보낼 디플로이먼트를 no-pods 로 지정
#    - 'np-svc-v2' : 해당 서비스 이름
#    - 'NodePort'  : 타입 (서비스  타입은 대소문 구분에 유의해야함)
kubectl expose deployment np-pods --type=NodePort --name=np-svc-v2--port=80

# 2. 생성된 서비스 확인
#    - 이때, 노드포트 번호는 expose 를 사용할 경우 30000 ~ 32767 에서 임의로 지정됨
kubectl get services

# 3. 삭제
kubectl delete deployment np-pods
kubectl delete services np-svc
kubectl delete services np-svc-v2
```

# 사용 목적별로 연결하는 인그레스

- 노드포트 서비스는 포트를 중복 사용할 수 없어서 1개의 노드포트에 1개의 디플로이먼트만 적용할 수 있습니다.
- 그렇다면 여러 개의 디플로이먼트가 있을 때 노드포트를 그 수만큼 설정해야할텐데, 이런 문제를 인그레스를 통해서 해결할 수 있습니다.
- 인그레스는 고유한 주소를 제공해 사용 목적에 따라 다른 응답을 제공할 수 있고, 트래픽에 대한 L4/L7 로드밸러서와 보완 인증서를 처리하는 기능을 제공합니다.
- 인그레스는 인그레스 컨트롤러와 함께 사용하는데, 다양한 컨트롤러 중에서 NGINX 인그레스  컨트롤러를 구성해보겠습니다.

<aside>
📎 1️⃣ 사용자는 노드마다 설정된 노드포트를 통해 노드포트 서비스로 접속합니다.

2️⃣ 이때, 노드포트 서비스를 NGINX 인그레스 컨트롤러로 구성합니다. 

3️⃣ NGINX 인그레스 컨트롤러는 사용자의 접속 경로에 따라 적합한 클러스터 IP 서비스로 경로를 제공합니다.

4️⃣ 클러스터 IP 서비스는 사용자를 해당 파드로 연결해줍니다. 

⚠️ 인그레스 컨트롤러는 파드와 직접 통신할 수 없어서 노드포트 또는 로드밸러서 서비스와 연동해서 사용해야합니다.

</aside>

- 아래는 실습 코드입니다.

```bash
# 1. 테스트용 디플로이먼트 2개를 배포합니다
kubectl create deployment in-hname-pod --image=sysnet4admin/echo-hnme
kubectl create deployment in-ip-pod --image=sysnet4admin/echo-ip

# 2. 배포된 파드 상태 확인!
kubectl get pods

# 3. NGNIX 인그레스 컨트롤러를 설치합니다. 
kubectl apply -f ~/Book_k8sInra/ch3/3.3.2/ingress-nginx.yaml

# 4. NGNIX 인그레스 컨트롤러 파드 배포 확인!
kubectl get pods -n ingress-nginx

# 5. 인그레스를 사용자 요구사항에 맞게 설정하기 위해 경로와 작동을 정의해줍니다.
kubectl apply -f ~/Book_k8sInra/ch3/3.3.2/ingress-config.yaml

# 5-1. ingress-config.yaml 일부
아래 따로 정리

# 6. 5번의 파일이 제대로 등록되었는지 확인!
kubectl get ingress

# 7. 인그레스에 요청한 내용이 확실하게 적용됐는지 확인!
kubectl get ingress -o yaml

# 8. 설정완료. 이제 외부에서 NGINX 인그레스 컨트롤러에 접속할 수 있게 외부에 노출합니다.
kubectl apply -f ~/_Book_k8sInfra/ch3/3.3.2/ingress.yaml

# 9. 노드포트 서비스로 생성된 NGINX 인그레스 컨트롤러 확인!
kubectl get services -n ingress-nginx

# 10. expose 명령으로 디플로이먼트도 서비스로 노출합니다.
kubectl expose deployment in-hname-pod --name=hname-svc-default--port=80, 443
kubectl expose deployment in-ip-pod --name=ip-svc-default--port=80, 443

# 11. 10번 내용 확인!
kubectl get services
```

```yaml
# ingress-config.yaml
apiVersion: v1
kind: Service
metadata:
	name: nginx-ingress-controller
	namespace: ingress-nginx
spec:
	ports:
	- name: http. # http 에 대한 프로토콜 및 포트 지정
		protocol: TCP
		port: 80
		targetPort: 80
		nodePort: 30100
	- name: https # https 에 대한 프로토콜 및 포트 지정
		protocol: TCP
		port: 443
		targetPort: 443
		nodePort: 30101
selector:
	app. kubernetes. io/name: ingress-nginx
type: NodePort
```

# 클라우드에서 쉽게 구성 가능한 로드밸런서

- 쿠버네티스에서는 로드밸런서라는 서비스 타입을 제공해 간단한 구조로 파드를 외부에 노출하고 부하를 분산합니다.
- 그러나 로드밸러서를 사용하려면 서비스업체의 도움을 받아 쿠버네티스 클러스터 외부에 구현해야하기 때문에 사용하지 않고 있었습니다.
- 클라우드에서 제공하는 쿠버네티스를 사용하고 있을 경우 아래처럼 선언할 수 있습니다.

```bash
kubectl expose deployment ex-lb --type=LoadBalancer --name=ex-svc
kubectl get services ex-svc
```
