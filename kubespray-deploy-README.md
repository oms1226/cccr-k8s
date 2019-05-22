## Kubespray를 이용한 Production Ready Kubernetes 클러스터 배포
[Kubespray GitHub Repository](https://github.com/kubernetes-sigs/kubespray)

작성날짜: 2018년 11월 30일
업데이트: 2019년 5월 11일

(참고) kubespray는 k8s를 프로덕션 온프레미스에 설치할 수 있는 배포방법(kubeadm 지원)

### 0. On-Premise 노드 구성
OS: Ubuntu 18.04  

| Master     |                  |
|------------|------------------|
| k8s-master | 192.168.56.11/24 |
|------------|------------------|
| Node       |                  |
|------------|------------------|
| k8s-node1  | 192.168.56.21/24 |
| k8s-node2  | 192.168.56.22/24 |
| k8s-node3  | 192.168.56.23/24 |


### 1. Requirements
- Ansible 2.5(이상), python-netaddr
- Jinja 2.9(이상)
- 인터넷 연결(도커 이미지 가져오기)
- IPv4 포워딩
- SSH 키 복사
- 배포 중 문제가 발생하지 않도록 방화벽 비활성
- 적절한 권한 상승(non-root 사용자인 경우 passwordless sudo 설정)

#### 1-1. Master Node
- SSH 키 복사
> ssh-keygen
> ssh-copy-id \<ACCOUNT\>@\<NODE\>
localhost 포함

- Ansible 설치 (최신)
> sudo apt-get update
> sudo apt-get install software-properties-common
> sudo apt-add-repository --yes --update ppa:ansible/ansible
> sudo apt-get install ansible python3 python3-pip git


- sudo 설정
> visudo
>> 	vagrant ALL=(ALL) NOPASSWD: ALL

### 2. Kubespray 배포

- kubespray Git repository 클론
> git clone https://github.com/kubernetes-sigs/kubespray.git

- requirements.txt 파일에서 의존성 확인 및 설치
> cd kubespray
> sudo pip3 install -r requirements.txt

- 인벤토리 준비
> cp -rfp inventory/sample inventory/mycluster

- 인벤토리 빌드업
> declare -a IPS=(192.168.56.11 192.168.56.21 192.168.56.22 192.168.56.23)
> CONFIG_FILE=inventory/mycluster/hosts.yml python3 contrib/inventory_builder/inventory.py ${IPS[@]}

- 인벤토리 수정 
inventory/mycluster/hosts.yml 파일

>[all]
>k8s-master	 ansible_host=192.168.56.11 ip=192.168.56.11
>k8s-node1 	 ansible_host=192.168.56.21 ip=192.168.56.21
>k8s-node2 	 ansible_host=192.168.56.22 ip=192.168.56.22
>k8s-node3 	 ansible_host=192.168.56.23 ip=192.168.56.23
>
>[kube-master]
>k8s-master1
>
>[etcd]
>k8s-master
>
>[kube-node]
>k8s-node1
>k8s-node2
>k8s-node3
>
>[k8s-cluster:children]
>kube-master
>kube-node
>
>[calico-rr]
>
>[vault]
>k8s-master1
>k8s-master2
>k8s-master3


- 파라미터 확인 및 변경 (필요 시 수정)
> cat inventory/mycluster/group_vars/all/all.yml
> cat inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml

- 플레이북 실행
> ansible-playbook -i inventory/mycluster/hosts.yml cluster.yml --become