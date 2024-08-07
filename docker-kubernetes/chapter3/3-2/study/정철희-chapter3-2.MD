# 쿠버네티스 기본 사용법 배우기

## 파드를 생성하는 방법
쿠버네티스에서 파드를 생성할 때 `kubectl run` 명령어를 사용합니다.  
이렇게 생성된 파드는 생성후 실행되지만 관리하기가 어렵습니다.  
그래서 `kubectl create` 명령어를 사용해서 파드를 생성하고 관리하는 것이 좋습니다.  
이렇게 `kubectl create` 명령어로 생성된 파드는 디플로이먼트(deployment)라는 관리 그룹 내에서 생성됩니다.  
Q. 왜 관리에서 어려울까요?
A. 파드는 Ip가 랜덤으로 부여되는데 deployment가 없다면 파드를 찾기가 어렵습니다.

## 오브젝트란
오브젝트는 스펙과 상태 등의 값을 가지는 파드와 디플로이먼트를 부르는 단위입니다.  

### 기본 오브젝트
> 파드(pod)  
파드는 쿠버네티스에서 가장 작은 배포 단위입니다.  
파드는 하나 이상의 컨테이너로 구성되어 있지만 일반적으로 하나의 컨테이너로 구성하는 것을 권장합니다.  
파드는 언제든 죽을 수 있는 존재로 디자인 되었습니다.  

> 네임스페이스(namespace)  
쿠버네티스 클러스터에서 사용되는 리소스들을 명시적으로 볼 수 있게 구분해 놓은 그룹입니다.  

> 볼륨(volume)  
파드 생성 시 파드에서 사용할 수 있는 디렉토리 제공합니다.  
이 데렉토리는 파드가 사라지더라도 데이터 보존이 가능합니다.  

> 서비스(service)  
로드밸런서, 게이트웨이처럼 쿠버네티스 외부에서 내부로 접속할 때 내부의 상황에 신경쓰지 않아도 논리적으로 연결해줍니다.  

### 디플로이먼트
기본 오브젝트만으로도 충분히 쿠버네티스를 사용할 수 있지만 디플로이먼트를 사용하면 파드를 관리하기가 편리해집니다.   
왜냐면 디플로이먼트는 파드를 생성하고 관리하는 역할을 하기 때문입니다.  

#### 레플리카셋
디플로이먼트는 레플리카셋을 사용하여 파드를 관리합니다.  
레플리카셋은 scale-out, scale-in을 할 때 파드의 수를 보장해주는 역할을 합니다.

## 레플리카셋으로 파드 수 관리하기
레플리카셋은 파드 수를 보장하는 기능만 제공하므로 디플로이먼트를 사용해 파드 수를 관리하는 것이 좋습니다.  

## 스펙을 지정해 오브젝트 생성하기
우리는 지금까지 `kubectl create` 명령어를 사용해서 오브젝트를 생성했습니다.  
그런데 이렇게 생성된 오브젝트는 기본적인 설정만 가지고 있습니다.  
더 많은 설정을 하고 싶다면 yaml 파일을 작성해서 `kubectl apply` 명령어로 오브젝트를 생성해야 합니다.

## apply로 오브젝트 생성하고 관리하기
Q. `kubectl apply` 명령어를 사용하면 `kubectl create` 명령어로 생성한 오브젝트와 어떤 부분이 다를까요?
A. `kubectl create` 명령어를 통해 오브젝트를 생성하면 오브젝트의 상태를 바로 변경할 수 없습니다.  
왜냐하면 `kubectl create` 명령어는 일회적으로 간단한 오브젝트를 생성하는 명령어이기 때문입니다.  
하지만 `kubectl apply` 명령어를 사용하면 오브젝트의 상태를 빠르게 변경할 수 있습니다.  