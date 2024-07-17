# 테스트 환경 구성하기
</br>

## 1. 버추얼박스 설치하기
버추얼박스는 현존하는 대부분의 운영 체제를 게스트 운영 체제로 사용할 수 있습니다.
</br>
</br>
만약 버추얼박스를 기존에 설치한 적 있고, 새롭게 설치를 원한다면 기존 설정을 완전히 삭제해야 합니다.  
프로그램을 삭제함과 동시에 디렉터리의 내용도 삭제하도록 합니다.
</br>
</br>
공식 홈페이지(https://www.virtualbox.org/wiki/Downloads)에 접속하여 운영 체제에 맞는 버전을 내려받습니다.
</br>
</br>
실행된 버추얼박스는 어떤 가상 머신도 없이 깨끗한 상태이므로, 베이그런트로 가상 머신을 생성해 줍니다.
</br>
</br>
</br>
## 2. 베이그런트 설치하기
베이그런트는 사용자의 요구에 맞게 시스템 자원을 할당, 배치, 배포해 두었다가 필요할 때 시스템을 사용할 수 있는 상태로 만들어 줍니다.  
이를 프로비저닝이라 하며, 필요할 때 환경을 매우 쉽고 간단하게 구현할 수 있습니다.
</br>
</br>
공식 홈페이지(https://developer.hashicorp.com/vagrant/install?product_intent=vagrant)에 접속하여 운영 체제에 맞는 버전을 내려받습니다.
</br>
</br>
### 2-1. 베이그런트 명령어
* #### vagrant init
  프로비저닝을 위한 기초 파일을 생성합니다.
* #### vagrant up
  Vagrantfile을 읽어 들여 프로비저닝을 진행합니다.
* #### vagrant halt
  베이그런트에서 다루는 가상 머신을 종료합니다.
* #### vagrant destroy
  베이그런트에서 관리하는 가상 머신을 삭제합니다.
* #### vagrant ssh
  베이그런트에서 관리하는 가상 머신에 ssh로 접속합니다.
* #### vagrant provision
  베이그런트에서 관리하는 가상 머신에 변경된 설정을 적용합니다.
* #### vagrant port
  베이그런트에서 사용하는 포트를 확인합니다.
</br>
</br>

## 3. 베이그런트로 테스트 환경 구축하기
Vagrantfile을 수정해 원하는 구성이 자동으로 CentOS에 입력되도록 해보겠습니다.
</br>

### 3-1. 가상 머신에 필요한 설정 자동으로 구성하기
베이그런트 코드는 루비 언어로 작성합니다.  
아래는 베이그런트로 구성되는 테스트 환경 구성도입니다.  

![img](../image/정현주-image1.png)

</br>

#### 3-1-1. vagrant init 명령으로 생성된 Vagrantfile을 다음과 같은 내용으로 수정한 후 저장합니다.
(Vagrantfile)
```
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
    config.vm.define "m-k8s" do |cfg|
        cfg.vm.box = "sysnet4admin/CentOS-k8s"
        cfg.vm.provider "virtualbox" do |vb|
            vb.name = "m-k8s(github_SysNet4Admin)"
            vb.cpus = 2
            vb.memory = 2048
            vb.customize ["modifyvm", :id, "--groups", "/k8s-SM(github_SysNet4Admin)"]
        end
        cfg.vm.host_name = "m-k8s"
        cfg.vm.network "private_network", ip: "192.168.1.10"
        cfg.vm.network "forwarded_port", guest: 22, host: 60010, auto_correct: true, id: "ssh"
        cfg.vm.synced_folder "../data", "/vagrant", disabled: true
    end
end
```
* 1~2번째 줄  
-에디터에 현재 파일이 루비임을 인식하게 하는 호환 코드  
-ft는 파일 종류(file type)의 약자이며, 해당 내용은 실행에 아무런 영향을 미치지 않음

* 3번째 줄  
-"2"는 베이그런트에서 루비로 코드를 읽어 들여 실행할 때 작동하는 API 버전  
-do |config| 는 베이그런트 설정의 시작을 알림

* 4번째 줄  
-버추얼박스에서 보이는 가상 머신을 "m-k8s"로 정의  
-do |cfg| 를 추가해 원하는 설정으로 변경  
-do |이름| 으로 시작한 작업은 end로 종료

* 5번째 줄  
-기본값 config.vm.box를 do |cfg| 에 적용한 내용을 받아 cfg.vm.box로 변경

* 6번째 줄  
-베이그런트의 프로바이더가 버추얼박스라는 것을 정의  
-프로바이더는 베이그런트를 통해 제공되는 코드가 실제로 가상 머신으로 배포되게 하는 소트프웨어  
-버추얼박스에서 필요한 설정을 정의하는데, 그 시작을 do |vb| 로 선언

* 7~11번째 줄  
-버추얼박스에 생성한 가상 머신의 이름, CPU 수, 메모리 크기, 소속된 그룹을 명시  
-마지막으로 end를 적어 버추얼박스 설정이 끝났음을 알림

* 12번째 줄  
-가상 머신 자체에 대한 설정으로, do |cfg| 에 속한 작업  
-호스트의 이름(m-k8s)을 설정

* 13번째 줄  
-호스트 전용 네트워크를 private_network로 설정  
-eth1 인터페이스를 호스트 전용(Host-only)으로 구성  
-IP는 192.168.1.10으로 지정

* 14번째 줄  
-ssh 통신은 호스트 60010번을 게스트 22번으로 전달되도록 구성  
-포트 중복을 대비해 auto-correct: true로 설정해서 포트가 중복되면 포트가 자동으로 변경 되도록 함

* 15번째 줄  
-호스트(PC 또는 노트북)와 게스트(가상 머신) 사이에 디렉터리 동기화가 이뤄지지 않게 설정 (disabled: true)

* 16~17번째 줄  
-설정 작업(do |config|, do |cfg|)이 종료됐음을 end 구문으로 명시  
-do와 end의 들여쓰기 (indentation) 위치가 정확하게 일치해야 함
</br>

#### 3-1-2. vagrant up 명령 실행
</br>

#### 3-1-3. vagrant ssh 명령을 실행해 생성된 가상 머신에 접속합니다.
</br>

#### 3-1-4. ip addr show eht1 명령을 입력해 IP(192.168.1.10)가 제대로 설정되었는지 확인합니다.
</br>

#### 3-1-5. exit 명령을 실행해 CentOS 접속을 종료합니다.
</br>
</br>

### 3-2. 가상 머신에 추가 패키지 설치하기
위에서 필요한 내용이 설정된 Vagrantfile을 통해 CentOS에 호스트네임, IP 등을 자동으로 설정해 보았습니다.  
아래에서는 CentOS에 필요한 패키지를 설치하는 방법을 알아보겠습니다.
</br>
</br>

#### 3-2-1. Vagrantfile에 셸 프로비전을 추가합니다.
(vagrantfile)
```
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
    config.vm.define "m-k8s" do |cfg|
        cfg.vm.box = "sysnet4admin/CentOS-k8s"
        cfg.vm.provider "virtualbox" do |vb|
            vb.name = "m-k8s(github_SysNet4Admin)"
            vb.cpus = 2
            vb.memory = 2048
            vb.customize ["modifyvm", :id, "--groups", "/k8s-SM(github_SysNet4Admin)"]
        end
        cfg.vm.host_name = "m-k8s"
        cfg.vm.network "private_network", ip: "192.168.1.10"
        cfg.vm.network "forwarded_port", guest: 22, host: 60010, auto_correct: true, id: "ssh"
        cfg.vm.synced_folder "../data", "/vagrant", disabled: true
    cfg.vm.provision "shell", path: "install_pkg.sh" #add provisioning script
    end
end
```
* 16번째 줄
-vm.provision "shell" 구문으로 경로에 있는 install_pkg.sh를 게스트(CentOS) 내부에서 호출해 실행되도록 함
</br>
Vagrantfile이 위치한 디렉터리에서 추가 패키지를 설치하기 위한 스크립트를 다음과 같이 작성하고 install_pkg.sh라는 이름으로 저장합니다.
</br>
</br>

#### 3-2-2. Vagrantfile에서 호출한 install_pkg.sh로 입력해 둔 배시 셸 파일을 실행해 EPEL 저장소와 코드 하이라이트를 위한 Vim의 추가 기능을 설치합니다.
(install_pkg.sh)
```
#!/usr/bin/env bash
# install packages
yum install epel-release -y
yum install vim-enhanced -y
```
</br>

#### 3-2-3. vagrant provision 명령으로 추가한 프로비전 구문을 실행합니다.
</br>

#### 3-2-4. vagrant ssh 명령을 실행해 CentOS에 접속합니다.
</br>

#### 3-2-5. yum repolist 명령으로 추가한 EPEL 저장소가 구성되었는지 확인합니다.
</br>

#### 3-2-6. vi .bashrc를 실행해 문법 하이라이트가 적용되었는지 확인합니다.
</br>

#### 3-2-7. exit 명령으로 가상머신을 빠져나옵니다.
</br>
</br>

### 3-3. 가상 머신 추가로 구성하기
아래는 CentOS 3대를 추가로 구성한 테스트 환경입니다.

![img](../image/정현주-image1.png)

</br>

#### 3-3-1. Vagrantfile에 CentOS 3대와 네트워크 구성을 테스트할 파일을 추가합니다.
(vagrantfile)
```
# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
    config.vm.define "m-k8s" do |cfg|
        cfg.vm.box = "sysnet4admin/CentOS-k8s"
        cfg.vm.provider "virtualbox" do |vb|
            vb.name = "m-k8s(github_SysNet4Admin)"
            vb.cpus = 2
            vb.memory = 2048
            vb.customize ["modifyvm", :id, "--groups", "/k8s-SM(github_SysNet4Admin)"]
        end
        cfg.vm.host_name = "m-k8s"
        cfg.vm.network "private_network", ip: "192.168.1.10"
        cfg.vm.network "forwarded_port", guest: 22, host: 60010, auto_correct: true, id: "ssh"
        cfg.vm.synced_folder "../data", "/vagrant", disabled: true
        cfg.vm.provision "shell", path: "install_pkg.sh"
        cfg.vm.provision "file", source: "ping_2_nds.sh", destination: "ping_2_nds.sh"
        cfg.vm.provision "shell", path: "config.sh"
    end

#=============#
# Added Nodes #
#=============#
    (1..3).each do |i| # 1부터 3까지 3개의 인자를 반복해 i로 입력
        config.vm.define "w#{i}-k8s" do |cfg| # {i} 값이 1, 2, 3으로 차례대로 치환됨
            cfg.vm.box = "sysnet4admin/CentOS-k8s"
            cfg.vm.provider "virtualbox" do |vb|
                vb.name = "w#{i}-k8s(github_SysNet4Admin)" # {i} 값이 1, 2, 3으로 차례대로 치환됨
                vb.cpus = 1
                vb.memory = 1024 # 메모리를 1GB 사용하도록 변경
                vb.customize ["modifyvm", :id, "--groups", "/k8s-SM(github_SysNet4Admin)"]
            end
            cfg.vm.host_name = "w#{i}-k8s" # {i} 값이 1, 2, 3으로 차례대로 치환됨
            cfg.vm.network "private_network", ip: "192.168.1.10#{i}" # {i} 값이 1, 2, 3으로 차례대로 치환됨
            cfg.vm.network "forwarded_port", guest: 22, host: "6010#{i}", auto_correct: true, id: "ssh" # {i} 값이 1, 2, 3으로 차례대로 치환됨
            cfg.vm.synced_folder "../data", "/vagrant", disabled: true
            cfg.vm.provision "shell", path: "install_pkg.sh"
        end
    end
end
```
* 18번째 줄
-파일을 게스트 운영 체제에 전달하기 위해 "shell"이 아닌 "file" 구문으로 변경
-호스트에 있는 ping_2_nds.sh 파일을 게스트의 홈 디렉터리(/home/vagrant)로 전달

* 19번째 줄
-config.sh를 게스트에서 실행

* 26~37번째 줄
-추가한 3대의 CentOS에 대한 구성
-기존 CentOS와 동일하나, 3대를 효율적으로 구성하기 위해 반복문을 사용
-반복은 (1..3).each로 이루어지며, 해당 값은 |i| 를 통해 #{i}로 치환돼 사용됨
</br>

#### 3-3-2. 추가 패키지를 설치하는 스크립트를 작성합니다.
(install_pkg.sh)
```
#!/usr/bin/env bash
# install packages
yum install epel-release -y
yum install vim-enhanced -y
```
</br>

#### 3-3-3. ping 테스트 파일을 작성합니다.
(ping_2_nds.sh)
```
# ping 3 times per nodes
ping 192.168.1.101 -c 3
ping 192.168.1.101 -c 3
ping 192.168.1.101 -c 3
```
</br>

#### 3-3-4. 설정을 변경하는 스크립트를 작성합니다.
(config.sh)
```
#!/usr/bin/env bash
# modify permission
chmod 744 ./ping_2_nds.sh
```
</br>

#### 3-3-5. vagrant up을 실행해 총 4대의 CentOS를 설치하고 구성합니다.
</br>

#### 3-3-6. vagrant ssh 명령으로 설치된 CentOS에 접속합니다.
</br>

#### 3-3-7. vagrant ssh m-k8s(가장 먼저 설치된 가상 머신 이름)를 입력합니다.
</br>

#### 3-3-8. ./ping_2_nds.sh 파일을 실행해 3대의 CentOS(192.168.101~103)와 통신하는 데 문제가 없는지 확인합니다.
</br>

#### 3-3-9. exit 명령으로 가상 머신 접속을 종료합니다.
</br>
</br>

## 4. 터미널 프로그램으로 가상 머신 접속하기
### 4-1. 푸티
터미널 접속 프로그램인 푸티를 사용하여 여러 대의 가상 머신에 접근할 수 있습니다.  
단, 한 번에 한 대씩만 접근할 수 있습니다.
</br>
</br>
우선 mkdir C:/putty 명령을 입력하여 푸티 실행 파일을 저장할 디렉터리를 생성합니다.
</br>
</br>
이후 공식 홈페이지(https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)에 접속하여 운영 체제에 맞는 실행 파일을 위의 디렉터리에 내려받습니다.
</br>
</br>
### 4-2. 슈퍼푸티
슈퍼푸티를 사용하면 한 번에 여러 대의 가상 머신에 접근해서 관리할 수 있습니다.
</br>
</br>
공식 홈페이지(https://github.com/jimradford/superputty/releases)에 접속하여 최신 버전 파일을 내려받습니다.
</br>
</br>
실행 후 슈퍼푸티는 푸티를 통해 실행되므로 푸티의 위치를 지정해야 합니다.  
putty.exe Location(Required)에서 푸티의 위치를 지정해 주어야 합니다.
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
출처 : https://velog.io/@esjw_/테스트-환경-구성
