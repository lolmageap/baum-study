# 쿠버네티스
쿠버네티스는 컨테이너 오케스트레이션을 위한 솔루션입니다.
</br>
</br>
오케스트레이션(Orchestration)이란 복잡한 단계를 관리하고 요소들의 유기적인 관계를 미리 정의해 손쉽게 사용하도록 서비스를 제공하는 것을 의미합니다.  
즉, 다수의 컨테이너를 유기적으로 연결, 실행, 종료할 뿐만 아니라 상태를 추적하고 보존하는 등 컨테이너를 안정적으로 사용할 수 있게 만들어주는 것입니다.
</br>
</br>
</br>
## 1. 쿠버네티스 구성 방법
쿠버네티스를 구성하는 방법은 크게 3가지입니다.
</br>
</br>
* #### 퍼블릭 클라우드 업체에서 제공하는 관리형 쿠버네티스
  EKS(Amazon Elastic Kubernetes), AKS(Azure Kubernetes Services), GKE(Coogle Kubernetes Engine) 등이 있으며, 구성이 이미 다 갖춰져 있고 마스터 노드를 클라우드 업체에서 관리합니다.
* #### 플랫폼 업체에서 제공하는 설치형 쿠버네티스
  수세의 Rancher, 레드햇의 OpenShift와 같이 플랫폼에서 제공하며, 유료입니다.
* #### 솔루션으로 자동으로 구성해 주는 구성형 쿠버네티스
  주요 솔루션으로 kubeadm, kops(Kubernetes Operations), KRIB(Kubernetes Rebar Integrated Bootstrap), kubespray 가 있으며, 사용하는 시스템에 쿠버네티스 클러스터를 자동으로 구성해 줍니다.
</br>
</br>

## 2. 쿠버네티스 구성하기
학습을 위해 사용자 설정이 가장 많이 필요한 kubeadm으로 쿠버네티스를 구성해 보겠습니다.
</br>
</br>
<details>
    <summary>2-1. 베이그런트 스크립트 파일과 소스 코드 파일 내려받기</summary>

https://github.com/sysnet4admin/_Book_k8sInfra 에 접속하여 쿠버네티스 실습 환경을 만들어 줄 베이그런트 스크립트 파일과 실습에 사용할 소스 코드 파일들을 베이그런트가 설치된 디렉터리로 내려받습니다.
</br>
</br>
#### (Vagrantfile)  
베이그런트 프로비저닝을 위한 정보를 담고 있는 메인 파일입니다.  
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
N = 3 # max number of worker nodes
Ver = '1.18.4' # Kubernetes Version to install

#=============#
# Master Node #
#=============#

config.vm.define "m-k8s" do |cfg|
cfg.vm.box = "sysnet4admin/CentOS-k8s"
cfg.vm.provider "virtualbox" do |vb|
vb.name = "m-k8s(github_SysNet4Admin)"
vb.cpus = 2
vb.memory = 3072
vb.customize ["modifyvm", :id, "--groups", "/k8s-SgMST-18.6.0(github_SysNet4Admin)"]
end
cfg.vm.host_name = "m-k8s"
cfg.vm.network "private_network", ip: "192.168.1.10"
cfg.vm.network "forwarded_port", guest: 22, host: 60010, auto_correct: true, id: "ssh"
cfg.vm.synced_folder "../data", "/vagrant", disabled: true
cfg.vm.provision "shell", path: "config.sh", args: N
cfg.vm.provision "shell", path: "install_pkg.sh", args: [ Ver, "Main" ]
cfg.vm.provision "shell", path: "master_node.sh"
end

#==============#
# Worker Nodes #
#==============#

(1..N).each do |i|
config.vm.define "w#{i}-k8s" do |cfg|
cfg.vm.box = "sysnet4admin/CentOS-k8s"
cfg.vm.provider "virtualbox" do |vb|
vb.name = "w#{i}-k8s(github_SysNet4Admin)"
vb.cpus = 1
vb.memory = 2560
vb.customize ["modifyvm", :id, "--groups", "/k8s-SgMST-18.6.0(github_SysNet4Admin)"]
end
cfg.vm.host_name = "w#{i}-k8s"
cfg.vm.network "private_network", ip: "192.168.1.10#{i}"
cfg.vm.network "forwarded_port", guest: 22, host: "6010#{i}", auto_correct: true, id: "ssh"
cfg.vm.synced_folder "../data", "/vagrant", disabled: true
cfg.vm.provision "shell", path: "config.sh", args: N
cfg.vm.provision "shell", path: "install_pkg.sh", args: Ver
cfg.vm.provision "shell", path: "work_nodes.sh"
end
end

end
```
* 5번째 줄  
-쿠버네티스에서 작업을 수행할 워커 노드의 수를 변수로 받음  
-24번째 줄, 46번째 줄에서 config.sh로 넘김  
-사용자가 워커 노드의 개수를 직접 조절할 수 있음

* 6번째 줄  
-쿠버네티스 버전을 사용자가 선택할 수 있도록 변수로 저장

* 25번째 줄  
-쿠버네티스 버전 정보(Ver)와 Main이라는 문자를 install_pkg.sh로 넘김  
-Ver 변수는 각 노드에 해당 버전의 쿠버네티스 버전을 설치  
-Main 문자는 install_pkg.sh에서 조건문으로 처리해 마스터 노드에만 이 책의 전체 실행 코드를 내려받게 함

* 26번째 줄  
-쿠버네티스 마스터 노드를 위한 master_node.sh 코드 추가

* 48번째 줄  
-워커 노드를 위한 work_nodes.sh 코드 추가
</br>

#### (config.sh)  
kubeadm으로 쿠버네티스를 설치하기 위한 사전 조건을 설정하는 스크립트 파일입니다.  
```
#!/usr/bin/env bash

# vim configuration
echo 'alias vi=vim' >> /etc/profile

# swapoff -a to disable swapping
swapoff -a
# sed to comment the swap partition in /etc/fstab
sed -i.bak -r 's/(.+ swap .+)/#\1/' /etc/fstab

# CentOS repo change from mirror to vault
sed -i -e 's/mirrorlist=/#mirrorlist=/g' /etc/yum.repos.d/CentOS-*
sed -i -e 's/mirrorlist=/#mirrorlist=/g' /etc/yum.conf
sed -E -i -e 's/#baseurl=http:\/\/mirror.centos.org\/centos\/\$releasever\/([[:alnum:]_-]*)\/\$basearch\//baseurl=https:\/\/vault.centos.org\/7.9.2009\/\1\/\$basearch\//g' /etc/yum.repos.d/CentOS-*
sed -E -i -e 's/#baseurl=http:\/\/mirror.centos.org\/centos\/\$releasever\/([[:alnum:]_-]*)\/\$basearch\//baseurl=https:\/\/vault.centos.org\/7.9.2009\/\1\/\$basearch\//g' /etc/yum.conf

# kubernetes repo
gg_pkg="http://mirrors.aliyun.com/kubernetes/yum" # Due to shorten addr for key
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=${gg_pkg}/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=${gg_pkg}/doc/yum-key.gpg ${gg_pkg}/doc/rpm-package-key.gpg
EOF

# Set SELinux in permissive mode (effectively disabling it)
setenforce 0
sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config

# RHEL/CentOS 7 have reported traffic issues being routed incorrectly due to iptables bypassed
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
modprobe br_netfilter

# local small dns & vagrant cannot parse and delivery shell code.
echo "192.168.1.10 m-k8s" >> /etc/hosts
for (( i=1; i<=$1; i++ )); do echo "192.168.1.10$i w$i-k8s" >> /etc/hosts; done

# config DNS
cat <<EOF > /etc/resolv.conf
nameserver 1.1.1.1 #cloudflare DNS
nameserver 8.8.8.8 #Google DNS
EOF

# docker repo
yum install yum-utils -y
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
* 4번째 줄  
-vi를 호출하면 vim을 호출하도록 프로파일에 입력  
-코드에 하이라이트를 넣어 코드를 쉽게 구분할 수 있음

* 7번째 줄  
-쿠버네티스의 설치 요구 조건을 맞추기 위해 스왑 되지 않도록 설정

* 9번째 줄  
-시스템이 다시 시작되더라도 스왑 되지 않도록 설정

* 12번째 줄  
-쿠버네티스의 레포지토리를 설정하기 위한 경로를 변수로 처리

* 13~21번째 줄  
-쿠버네티스를 내려받을 레포지토리를 설정하는 구문

* 24~25번째 줄  
-selinux가 제한적으로 사용되지 않도록 permissive 모드로 변경

* 28~31번째 줄  
-브리지 네트워크를 통과하는 IPv4와 IPv6의 패킷을 iptables가 관리하게 설정  
-파드의 통신을 iptables로 제어  
-필요에 따라 IPVS(IP Virtual Server) 같은 방식으로도 구성할 수도 있음

* 32번째 줄  
-br_netfilter 커널 모듈을 사용해 브리지로 네트워크를 구성  
-IP 마스커레이드(Masquerade)를 사용해 내부 네트워크와 외부 네트워크를 분리  
-br_netfilter를 적용함으로써 28~31번째 줄에서 적용한 iptables가 활성화됨  
-IP 마스커레이드 : 커널에서 제공하는 NAT(Network Address Translation) 기능

* 35~36번째 줄  
-쿠버네티스 안에서 노드 간 통신을 이름으로 할 수 있도록 각 노드의 호스트 이름과 IP를 /etc/hosts에 설정  
-워커 노드는 Vagrantfile에서 넘겨받은 N 변수로 전달된 노드 수에 맞게 동적으로 생성

* 39~42번째 줄  
-외부와 통신할 수 있게 DNS 서버 지정
</br>

#### (install_pkg.sh)  
클러스터를 구성하기 위해서 가상 머신에 설치돼야 하는 의존성 패키지를 명시합니다.  
또한 실습에 필요한 소스 코드를 특정 가상 머신 내부에 내려받도록 설정되어 있습니다.
```
#!/usr/bin/env bash

# install packages
yum install epel-release -y
yum install vim-enhanced -y
yum install git -y

# install docker
yum install docker-ce-18.06.0.ce-3.el7 docker-ce-cli-18.06.0.ce-3.el7 \
containerd.io-1.2.6-3.3.el7 -y
systemctl enable --now docker

# install kubernetes cluster
yum install kubectl-$1 kubelet-$1 kubeadm-$1 -y
systemctl enable --now kubelet

# git clone _Book_k8sInfra.git
if [ $2 = 'Main' ]; then
git clone https://github.com/sysnet4admin/_Book_k8sInfra.git
mv /home/vagrant/_Book_k8sInfra $HOME
find $HOME/_Book_k8sInfra/ -regex ".*\.\(sh\)" -exec chmod 700 {} \;
fi

```
* 6번째 줄  
-깃허브에서 코드를 내려받을 수 있게 git을 설치

* 9번째 줄  
-쿠버네티스를 관리하는 컨테이너를 설치하기 위해 도커를 설치하고 구동

* 12~13번째 줄  
-쿠버네티스를 구성하기 위해 첫 번째 변수로 넘겨받은 1.18.4 버전의 kubectl, kubelet, kubeadm을 설치하고 kubeadm을 시작

* 16~20번째 줄  
-전체 실행 코드를 마스터 노드에만 내려받도록 Vagrantfile에서 두 번째 변수를 넘겨받음  
-git에서 코드를 내려받아 실습을 진행할 루트 홈 디렉터리(/root)로 옮김  
-배시 스크립트(.sh)를 find로 찾아서 바로 실행 가능한 상태가 되도록 chmod 700으로 설정
</br>

#### (master_node.sh)  
1개의 가상 머신을 쿠버네티스 마스터 노드로 구성하는 스크립트입니다.  
쿠버네티스 클러스터를 구성할 때 꼭 선택해야 하는 컨테이너 네트워크 인터페이스(CNI; Container Network Interface)도 함께 구성합니다.
```
#!/usr/bin/env bash

# init kubernetes
kubeadm init --token 123456.1234567890123456 --token-ttl 0 \
--pod-network-cidr=172.16.0.0/16 --apiserver-advertise-address=192.168.1.10

# config for master node only
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

# config for kubernetes's network
kubectl apply -f \
https://raw.githubusercontent.com/sysnet4admin/IaC/master/manifests/172.16_net_calico.yaml
```
* 4~5번째 줄  
-kubeadm을 통해 쿠버네티스의 워커 노드를 받아들일 준비를 함  
-토큰을 123456.1234567890123456으로 지정  
-ttl을 0으로 설정해서 기본값이 24시간 후에 토큰이 계속 유지되게 함  
-워커 노드가 정해진 토큰으로 들어오게 함  
-쿠버네티스가 자동으로 컨테이너에 부여하는 네트워크를 172.16.0.0/16으로 제공  
-워커 노드가 접속하는 API 서버의 IP를 192.168.1.10으로 지정해 워커 노드들이 자동으로 API 서버에 연결되게 함

* 8~10번째 줄  
-마스터 노드에서 현재 사용자가 쿠버네티스를 정상적으로 구동할 수 있게 설정 파일을 루트의 홈 디렉터리(/root)에 복사  
-쿠버네티스를 이용할 사용자에게 권한을 줌

* 13~14번째 줄  
-컨테이너 네트워크 인터페이스(CNI)인 캘리코(Calico)의 설정을 적용해 쿠버네티스의 네트워크를 구성
</br>

#### (work_nodes.sh)  
3대의 가상 머신에 쿠버네티스 워커 노드를 구성하는 스크립트입니다.  
마스터 노드에 구성된 클러스터에 조인이 필요한 정보가 모두 코드화되어 있어 스크립트를 실행하기만 하면 편하게 워커 노드로서 쿠버네티스 클러스터에 조인됩니다.
```
#!/usr/bin/env bash

# config for work_nodes only
kubeadm join --token 123456.1234567890123456 \
--discovery-token-unsafe-skip-ca-verification 192.168.1.10:6443
```
* 4~5번째 줄  
-kubeadm을 이용해 쿠버네티스 마스터 노드에 접속  
-연결에 필요한 토큰은 기존에 마스터 노드에서 생성한 123456.1234567890123456을 사용  
-간단하게 구성하기 위해 --discovery-token-unsafe-skip-ca-verification으로 인증을 무시  
-API 서버 주소로 기본 포트 번호인 6443번 포트에 접속하도록 설정

</details>

</br>

<details>
    <summary>2-2. 쿠버네티스 클러스터 자동으로 구성하기</summary>

cd C:/HashiCorp/_Book_k8sInfra-main/ch3/3.1.3 명령으로 쿠버네티스 설치 파일이 있는 디렉터리로 이동 후, vagrant up 명령을 실행하여 지금까지 설명한 파일들로 쿠버네티스 클러스터가 자동으로 구성되게 합니다.
</br>
</br>
이후 슈퍼 푸티를 통해 m-k8s 터미널에 접속하여 kubectl get nodes 명령으로 쿠버네티스 클러스터에 마스터 노드와 워커 노드들이 정상적으로 생성되고 연결되었는지 확인할 수 있습니다.

</details>

</br>
</br>

## 3. 파드 배포를 중심으로 쿠버네티스 구성 요소 살펴보기
kubectl get pods --all-namespaces 명령으로 설치된 쿠버네티스 구성 요소를 확인할 수 있습니다.
</br>
</br>

### 3-1. 관리자/개발자가 파드를 배포할 때
다음은 쿠버네티스의 구성 요소 간 통신 흐름을 나타낸 이미지입니다.  
즉, 파드를 배포하는 순서를 확인할 수 있습니다.

![img.png](../image/정현주-image1.png)

* #### ⓪ : kubectl
  -쿠버네티스 클러스터에 명령을 내리는 역할입니다.  
  -다른 구성 요소들과 다르게 바로 실행되는 명령 형태인 바이너리로 배포되기 때문에 마스터 노드에 있을 필요는 없습니다.  
  -API 서버와 주로 통신하므로 마스터 노드에 구성합니다.

* #### ① : API 서버
  -쿠버네티스 클러스터의 중심 역할을 하는 통로입니다.  
  -주로 상태 값을 저장하는 etcd와 통신합니다.  
  -다른 요소들도 API 서버를 중심에 두고 통신하므로 API 서버의 역할이 매우 중요합니다.  
  -모든 직원과 상황을 관리하고 목표를 설정하는 관리자 역할입니다.

* #### ② : etcd
  -구성 요소들의 상태 값이 모두 저장되는 곳입니다.  
  -다른 구성 요소들은 상태 값을 관리하지 않습니다.  
  -etcd 정보만 백업되어 있으면 긴급한 장애 상황에서도 클러스터를 복구할 수 있습니다.  
  -분산 저장인 가능한 key-value 저장소이므로, 복제해 여러 곳에 저장해두면 하나의 etcd에서 장애가 나더라도 시스템의 가용성을 확보할 수 있습니다.  
  -회사의 관리자가 모든 보고 내용을 기록하는 노트입니다.

* #### ③ : 컨트롤러 매니저
  -쿠버네티스 클러스터의 오브젝트 상태를 관리합니다.  
  -워커 노드에서 통신이 되지 않는 경우, 상태 체크와 복구가 이루어집니다.

* #### ④ : 스케줄러
  -노드의 상태와 자원, 레이블, 요구 조건 등을 고려해 파드를 어떤 워커 노드에 생성할 것인지 결정하고 할당합니다.  
  -파드를 조건에 맞는 워커 노드에 지정하고, 파드가 워커 노드에 할당되는 일정을 관리하는 역할을 담당합니다.

* #### ⑤ : kubelet
  -파드의 구성 내용(PodSpec)을 받아서 컨테이너 런타임으로 전달합니다.  
  -파드 안의 컨테이너들이 정상적으로 작동하는지 모니터링합니다.

* #### ⑥ : 컨테이너 런타임(CRI; Container Runtime Interface)
  -파드를 이루는 컨테이너의 실행을 담당합니다.  
  -파드 안에서 다양한 종류의 컨테이너가 문제없이 작동하게 만드는 표준 인터페이스입니다.

* #### ⑦ : 파드(pod)
  -한 개 이상의 컨테이너로 단일 목적의 일을 하기 위해서 모인 단위입니다.  
  -웹 서버 역할을 할 수도 있고 로그나 데이터를 분석할 수도 있습니다.  
  -언제라도 죽을 수 있는 존재이기 때문에, 쿠버네티스는 여러 대안을 디자인해둡니다.

* #### ⑪ : 네트워크 플러그인
  -쿠버네티스 클러스터의 통신을 위해서 네트워크 플러그인을 선택하고 구성해야 합니다.  
  -일반적으로 CNI로 구성합니다. (캘리코, 플래널, 실리움, 큐브 라우터, 로마나, 위브넷, Canal 등)  
  -CNI(Container Network Interface) : 컨테이너의 네트워크 안전성과 확장성을 보장하기 위해 개발됨

* #### ⑫ : CoreDNS
  -빠르고 유연한 DNS 서버입니다.  
  -쿠버네티스 클러스터에서 도메인 이름을 이용해 통신하는 데 사용합니다.  
  -실무에서 쿠버네티스 클러스터를 구성하여 사용할 때는 IP보다 CoreDNS를 사용하는 것이 일반적입니다.

</br>

### 3-2. 사용자가 배포된 파드에 접속할 때
파드가 배포된 이후 사용자 입장에서 배포된 파드에 접속하는 과정은 다음과 같습니다.
</br>
* #### kube-proxy
  -파드가 위치한 노드에 kube-proxy를 통해 파드가 통신할 수 있는 네트워크를 설정합니다.  
  -실제 통신은 br_netfilter와 iptables로 관리합니다.

* #### 파드
  -배포된 파드에 접속하고 필요한 내용을 전달받습니다.  
  -파드가 어느 워커 노드에 위치하는지 신경 쓰지 않아도 됩니다.

</br>
</br>

## 4. 파드의 생명주기로 쿠버네티스 구성 요소 살펴보기
아래 파드의 생명주기는 파드가 생성, 수정, 삭제되는 과정을 나타냅니다.

![img.png](../image/정현주-image2.png)

#### ① kubectl을 통해 API 서버에 파드 생성을 요청합니다.

#### ② (업데이트가 있을 때마다 매번) API 서버에 전달된 내용이 있으면 API 서버는 etcd에 전달된 내용을 모두 기록해 클러스터의 상태 값을 최신으로 유지합니다.  
  -각 요소가 상태를 업데이트할 매마다 모두 API 서버를 통해 etcd에 기록됩니다.

#### ③ API 서버에 파드 생성이 요청된 것을 컨트롤러 매니저가 인지하면 컨트롤러 매니저는 파드를 생성하고, 이 상태를 API 서버에 전달합니다.  
-아직 어떤 워커 노드에 파드를 적용할지는 결정되지 않은 상태로 파드만 생성됩니다.

#### ④ API 서버에 파드가 생성됐다는 정보를 스케줄러가 인지합니다.
  -스케줄러는 생성된 파드를 어떤 워커 노드에 적용할지 조건을 고려해 결정하고 해당 워커 노드에 파드를 띄우도록 요청합니다.

#### ⑤ API 서버에 전달된 정보대로 지정한 워커 노드에 파드가 속해 있는지 스케줄러가 kubelet으로 확인합니다.

#### ⑥ kubelet에서 컨테이너 런타임으로 파드 생성을 요청합니다.

#### ⑦ 파드가 생성됩니다.

#### ⑨ 파드가 사용 가능한 상태가 됩니다.
</br>
</br>

## 5. 쿠버네티스의 상태 유지 방법

![img.png](../image/정현주-image3.png)

쿠버네티스는 작업을 순서대로 진행하는 워크플로 구조가 아니라 선언적인 시스템 구조를 가지고 있습니다.  
즉, 각 요소가 추구하는 상태를 선언하면 현재 상태와 맞는지 점검하고 그것에 맞추려고 노력하는 구조로 되어있는 것입니다.
</br>
</br>
</br>

## 6. 쿠버네티스 구성 요소의 기능 검증하기
### 6-1. kubectl
kubectl은 꼭 마스터 노드에 위치할 필요는 없으며, 실제로 쿠버네티스 클러스터의 외부에서 쿠버네티스 클러스터에 명령을 내릴 수도 있습니다.
</br>
</br>
어느 곳에서든지 kubectl을 실행하기 위해서 아래와 같이 확인할 수 있습니다.
</br>

<details>
    <summary>자세히</summary>

#### ① 슈퍼 푸티 세션 창에서 w3-k8s를 클릭해 터미널에 접속합니다.

#### ② kubectl get nodes
  -kubectl이 쿠버네티스 클러스터의 정보를 알 수 없기 때문에 노드들에 대한 정보가 표시되지 않습니다.  
  -kubectl은 API 서버를 통해 쿠버네티스에 명령을 내리기 때문에 kubectl이 어디에 있더라도 API 서버의 접속 정보만 있다면 어디서든 쿠버네티스 클러스터에 명령을 내릴 수 있습니다.

#### ③ scp root@192.168.1.10:/etc/kubernetes/admin.conf .
  -쿠버네티스 클러스터의 정보(/etc/kubernetes/admin.conf)를 마스터 노드에서 scp 명령으로 w3-k8s의 현재 디렉터리에 받아옵니다.  
  -접속 기록이 없기 때문에 known_hosts로 저장하도록 yes를 입력합니다.  
  -마스터 노드의 접속 암호인 vagrant도 입력합니다.

#### ④ kubectl get nodes --kubeconfig admin.conf
  -kubectl get nodes 명령에 추가로 쿠버네티스 클러스터 정보를 입력받는 옵션(--kubeconfig)과 마스터 노드에서 받아온 admin.conf를 입력하고 실행하면, 노드 정보가 정상적으로 표시되는 것을 확인할 수 있습니다.

</details>
</br>

### 6-2. kubelet
쿠버네티스에서 파드의 생성과 상태 관리 및 복구 등을 담당하는 매우 중요한 구성 요소입니다.  
따라서 kubelet에 문제가 생기면 파드가 정상적으로 관리되지 않습니다.

<details>
    <summary>자세히</summary>

#### ① m-k8s(마스터 노드)에서 kubectl create -f ~/_Book_k8sInfra/ch3/3.1.6/nginx-pod.yaml 명령으로 nginx 웹 서버 파드를 배포합니다.  
  -f : filename을 의미

#### ② kubectl get pod 명령으로 배포된 파드가 정상적으로 배포된 상태(Running)인지 확인합니다.

#### ③ kubectl get pods -o wide 명령으로 파드가 배포된 워커 노드를 확인합니다.
  --o : output의 약어로 출력을 특정 형식으로 해주는 옵션  
  -wide : 제공되는 출력 형식 중에서 출력 정보를 더 많이 표시해 주는 옵션

#### ④ 배포된 노드(w2-k8s)에 접속해 systemctl stop kubelet으로 kubelet 명령어로 서비스를 멈춥니다.

#### ⑤ m-k8s에서 kubectl get pod 명령어로 상태를 확인하고, kubectl delete pod nginx-pod 명령으로 파드를 삭제합니다.

#### ⑥ 삭제를 기다리다 Ctrl + C로 명령을 중지합니다.

#### ⑦ 다시 kubectl get pod 명령으로 상태를 확인해 보면, nginx-pod를 삭제(Terminating) 하고 있지만 kubelet이 작동하지 않는 상태라 파드가 삭제되지 않습니다.

#### ⑧ 배포된 노드(w2-k8s)에서 systemctl start kubelet 명령으로 kubelet을 복구합니다.

#### ⑨ 잠시 후에 m-k8s에서 kubectl get pod 명령으로 nginx-pod가 삭제되었는지 확인합니다.
</br>

해당 과정을 통해 kubelet에 문제가 생기면 파드가 제대로 관리되지 않음을 확인해 볼 수 있습니다.

</details>

</br>

### 6-3. kube-proxy
파드의 통신을 담당합니다.
</br>
</br>
앞서 config.sh 파일에서 br_netfilter 커널 모듈을 적재하고 iptables를 거쳐 통신하도록 설정했습니다. 그런데 만약 해당 설정이 정상적으로 작동하지 않는다면 어떤 문제가 발생하는지 알아보도록 하겠습니다.

<details>
    <summary>자세히</summary>

#### ① kubectl create -f ~/_Book_k8sInfra/ch3/3.1.6/nginx-pod.yaml
  -마스터 노드인 m-k8s에서 파드를 배포합니다.

#### ② kubectl get pods -o wide 명령으로 파드의 IP와 워커 노드를 확인합니다.

#### ③. curl IP주소(전 단계에서 확인한 파드의 IP) 명령어로 nginx 웹 서버 메인 페이지 내용을 확인합니다.

#### ④ 배포된 노드(w3-k8s)에 접속해 modprobe -r br_netfilter 명령으로 파드가 위치한 워커 노드에서 br_netfilter 모듈을 제거하여 kube-proxy에 문제가 생기는 상황을 만듭니다.
  --r : remove

#### ⑤ systemctl restart network 명령으로 네트워크를 다시 시작해 변경된 내용을 적용합니다.

#### ⑥ m-k8s에서 다시 한번 curl IP주소 명령어로 파드의 nginx 웹 서버 페이지 정보를 받아옵니다.

#### ⑦ 기다려도 종료가 안되면 Ctrl + C로 요청을 종료합니다.

#### ⑧ kubectl get pods -o wide 명령어로 파드 상태를 확인합니다.
  -파드 노드 위치와 IP가 변경되지 않았는지, 작동 상태에 문제가 없는지 확인합니다.
  -현재 kube-proxy가 이용하는 br_netfilter에 문제가 있어서 파드의 nginx 웹 서버와의 통신만이 정상적으로 이루어지지 않는 상태(Connection timed out)입니다.

#### ⑨. 워커 노드에서 modprobe br_netfilter 명령어로 br_netfilter를 커널에 적재하고, reboot 명령어로 시스템을 다시 시작하여, 정상적으로 파드의 nginx 웹 서버 페이지 정보를 받아올 수 있는 상태로 만듭니다.

#### ⑩ 일정 시간이 지난 후 kubectl get pods -o wide 명령어로 파드의 상태를 확인하면 파드가 1회 다시 시작했다는 의미로 RESTARTS가 1로 증가하고 IP가 변경된 것을 확인할 수 있습니다.

#### ⑪ curl 바뀐 IP주소 명령어로 파드로부터 정보를 정상적으로 받아오는지 확인합니다.

#### ⑫ kubectl delete -f ~/_Book_k8sInfra/ch3/3.1.6/nginx-pod.yaml 명령어로 다음 내용을 진행하기 위해 배포한 파드를 삭제합니다.

</details>

</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
</br>
출처 : https://thebook.io/080241/
