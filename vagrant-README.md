Vagrant 설치 밒 사용법
=====================

### 1. Vagrant 다운로드 및 설치
- 설치 파일 및 패키지  
https://www.vagrantup.com/downloads.html  

- macOS
> brew install vagrant

- Windows
> choco install vagrant

### 2. Vagrant

#### 플러그인 설치  
> vagrant plugin install vagrant-hostmanager  
> vagrant plugin install vagrant-disksize

#### Box 이미지 다운로드
> vagrant box add ubuntu/bionic64

#### Vagrant 파일
> cd  
> mkdir k8s  
[Vagrantfile](https://raw.githubusercontent.com/c1t1d0s7/cccr-k8s/master/kubespray-Vagrantfile)  
> 파일 받아서 Vagrantfile로 저장  

### 3. Vagrant 사용법

상태확인  
> vagrant status [VM]    

시작  
> vagrant up [VM]  

일시중지  
> vagrant suspend [VM]  

재개  
> vagrant resume [VM]  

중지  
> vagrant halt [VM]  

삭제  
> vagrant destroy [VM]  

SSH 연결
> vagrant ssh [VM]  

스냅샷 확인  
> vagrant snapshot list [VM]  

스냅샷 생성
> vagrant snapshot save [VM]  <SNAP-NAME>  

스냅샷 복구
> vagrant snapshot restore [VM]  <SNAP-NAME>  

공유폴더 동기화
- 공유폴더 동기화는 Vagrantfile이 있는 현재 디렉토리와 VM의 /vagrant 파일이 기본 동기화 됨
- 공유폴더 동기화가 되는 시점은 vagrant up 명령을 실행할 때만 동기화 됨
> vagrant rsync [VM]  
