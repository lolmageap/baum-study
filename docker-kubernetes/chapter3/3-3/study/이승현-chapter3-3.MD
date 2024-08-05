## 파드의 컨테이너 자동 복구 방법

### 셀프 힐링

- 파드의 자동 복구 기술
- 제대로 작동하지 않는 컨테이너를 다시 시작하거나 교체해 파드가 정상적으로 작동하게 함

```bash
# 1. 접속하려는 파드의 IP 주소를 확인
kubectl get pods -o wide 

# 2. 파드 컨테이너 셸에 접속
#    - exe : 실행  
#    - i   : stdin (표준입력)
#    - t   : tty
kubectl exec -it nginx-pod -- /bin/bash

# 3. 컨테이너에서 구동하는 nginx의 PID 확인
#    - nginx의 PID는 언제나 1
cat /run/nginx.pid

# 4. ls -l 명령으로 프로세스가 생성된 시간 확인
ls -l /run/nginx.pid

# 5. 스크립트 실행
#    - 슈퍼푸티에서 m-k8s의 터미널을 1개 더 띄우고 
#      이 터미널 환경에서 nginx-pod의 IP에서 돌아가는 웹 페이지를 
#      1초마다 한 번씩 요청함
#    - --silent : curl 에서 요청한 값만 받도록 함
i=1 ; while true; do sleep 1; echo $((i++)) 'curl --slient 172.16.103.132 | grep title'; done

# 6. 셸에서 nginx 프로세서인 PID 1번을 kill 로 종료
kill 1

# 7. 5번에서 추가한 스크립트의 작동 여부와 자동 복구도 확인합니다. 
슈퍼푸티에서 확인
```

## 노드 자원 보호하기

- 앞서 쿠버네티스 파드는 여러 상황에서도 안정적인 작동이 가능하다는 것을 확인했습니다.
- 노드의 목적 : 쿠버네티스 스케줄러에서 파드를 할당받고 처리하는 역할을 합니다.
- (쿠버네티스는 모든 노드에 균등하게 파드를 할당하는데, 노드에 문제가 생기더라도 파드의 문제를 최소화해야합니다. )
- 그래서 영향도가 적은 파드를 해당 노드에 할당해 일정 기간 사용하면서 모니터링을 해주어야 합니다.

### cordon 기능 사용해보기

```bash
# 1. echo-hname.yaml 적용한 파드 생성
kubectl apply -f ~/_Book_k8sInfra/ch3/3.2.8/echo-hname.yaml

# 2. 파드 개수 증가시키기
kubectl scale deployment echo-hname --replicas=9

# 3. 생성된 파드 정보 확인하기
#    - 작동여부, IP할당 여부, 노드 배분 여부 확인
kubectl get pods \
-o=custom-columns=NAME:.metadata.name, IP:.status.podIP, STATUS:.status.phase, NODE:.spec.nodeName

# 4. 파드 개수 줄이기
kubectl scale deployment echo-hname --replicas=3

# 5. 다시 3번을 실행하여 각 노드에 파드가 1개씩 남았는지 확인

# 6. 한 노드에서 문제가 발생한 것을 확인할 경우 현재 상태를 보존해야함
#    - cordon 명령 실행
kubectl cordon <해당 노드 이름>

# 7. cordon 명령 적용 확인
#    - 그러면 STATUS 에 'Ready, SchedulingDisabled' 확인 가능
#    - 해당 노드에는 더이상 파드를 할당하지 않겠다는 뜻
kubectl get nodes

# 8. 다시 파드 수를 늘리고 파드 상태를 확인하면 해당 노드에는 파드가 할당되지 않는 것을 확인할 수 있음

# 9. 파드 할당되지 않게했던 설정을 해지하기
kubectl uncordon <해당 노드 이름>
```

---

## 노드 유지보수하기

- 쿠버네티스를 사용하다 보면 정기 또는 비정기적인 유지보수를 위해 노드를 꺼야하는 상황이 존재합니다.
- 이럴 때 drain 기능을 사용할 수 있습니다.

### Drain

- 지정된 노드의 파드를 전부 다른 곳으로 이동시켜 해당 노드를 유지보수할 수 있습니다.

```bash
# 1. kubectl drain 명령을 실행해 유지보수할 노드를 파드가 없는 상태로 만들기
#    - 그러나 실행 후 -> 데못셋이 없어서 지울 수 없다는 문구 발생
#    - drain 은 실제로 파드를 옮기는 것이 아니라 노드에서 파드를 삭제하고 다른 곳에 다시 생성
#    - 그러나 데몬셋은 각 노드에 1개만 존재하는 파드라서 drain 으로 삭제 불가능
kubectl drain <해당 노드 이름>

# 2. ignore-daemonsets 옵션 사용하여 drain 실행하기
#    - 데몬셋 무시 후 진행 -> 경고 발생하지만 모든 파드 이동 성공
kubectl drain <해당 노드 이름> --ignore-daemonsets

# 3. <해당 노드 이름> 이 사라졌는지 확인 & 옮긴 노드에 파드 확인
kubectl get pods \
-o=custom-columns=NAME:.metadata.name, IP:.status.podIP, STATUS:.status.phase, NODE:.spec.nodeName
```

---

## 파드 업데이트하고 복구하기

- 파드를 운영하다 보면 컨테이너에 버전 업데이트를 해야하는 상황이 발생하기도 합니다.

### —record 옵션

- 배포한 정보의 히스토리를 기록하는 옵션입니다.
- yaml 파일 작성 시 아래처럼 image 로 버전을 정해줍니다.

```yaml
spec:
	replicas: 3
	...
	spec:
		containers:
			- name: nginx
				image: nginx: 1.15.12
```

- 작성 후 명령어로 확인해보는 방법은 아래와 같습니다.

```bash
# 1. 레코드 옵션으로 기록된 히스토리 확인하기
kubectl rollout history deployment rollout-nginx

# 2. 배포한 파드 정보 확인하기
kubectl get pods \
-o=custom-columns=NAME:.metadata.name, IP:.status.podIP, STATUS:.status.phase, NODE:.spec.nodeName

# 3. 배포된 파드에 속해있는 nginx 컨테이너 버전 확인하기
#    - curl -I : 헤더 정보만 보여주는 옵션
curl -I --silent 172.16.103.143 | grep Server

# 4. 컨테이너 버전 업데이트하기
kubectl set image deployment rollout-nginx nginx=nginx:1.16.0 --record

# 5. 2,3번 명령어로 파드 상태 확인하기

# 6. 버전이 모두 업데이트되면 deployment 상태 확인하기
kubectl rollout status deployment rollout-nginx
```

### 파드에 문제가 생겼을 경우

```bash
# 파드 배포 중 문제가 발생했을 때 롤백
kubectl rollout undo deployment rollout-nginx
```
