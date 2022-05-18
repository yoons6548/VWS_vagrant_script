# Bash Shell Script 실습용
---

## Virtual Web Service사의 가상 서버 구축 vagrant 스크립트
---

## 서버 정보
  - cent1 (웹)
  ```script
    OS : CentOS8 
    CPU : 2 Core
    MEM : 1G
    Disk : 20G
    Middleware : nginx
    DMZ : 172.18.1.91
    Local : 10.18.1.91
   ```
  - cent2 (데이터베이스)
  ```script
    OS : CentOS8 
    CPU : 2 Core
    MEM : 1G
    Disk : 20G
    Middleware : mariadb
    DMZ : 172.18.1.92
    Local : 10.18.1.92
   ```
  - cent3 (스토리지)
  ```script
    OS : CentOS8 
    CPU : 2 Core
    MEM : 1G
    Disk : 20G
    Middleware : nfs
    DMZ : 172.18.1.93
    Local : 10.18.1.93
   ```

---
## 파일 및 디렉토리 설명
 - Vagrantfile : 가상서버 생성용 vagrant 스크립트 파일
 - WORK : 필요한 패키지 및 데이터
 - SHELL : 필요한 쉘 스크립트
 - CONF : 필요한 설정 파일

---
## 실행전 필요한 환경 준비
 - virtualbox 설치
   - URL : https://www.virtualbox.org/
 - vagrant 설치
   - URL : https://www.vagrantup.com/
 - git 설치
   - URL : https://git-scm.com/downloads

 - linux / macos 의 경우 주의점
   - 터미널에서 원하는 작업 디렉토리에 cd 커맨드로 이동 후 다음 작업을 진행

 - windows의 경우 주의점
   - CMOS 설정을 할 경우 재시작 필요
   - CMOS에서 CPU 가상화 설정을 모두 enabled로 설정
     - Virtualization Techenology
     - VT-d 
   - 각 프로그램의 설치는 기본 옵션으로 설치
   - 서버 구축할 때 백신 감시를 off로 변경
     - vagrant에서 centos8 이미지를 가져올 때 에러발생 사례가 있음
   - 탐색기에서 사용자 폴더 아래에 원하는 작업 폴더를 마우스 우클릭 
     --> Git Bash Here 클릭 후 다음 작업을 진행

---
## vagrant 스크립트 가져오기
```script
git clone https://github.com/bashbomb/VWS_vagrant_script.git 
```

---
## 서버 구축
```script
cd VWS_vagrant_script
vagrant up
```

---
## 실행/정지/삭제/상태확인 방법
```script
cd VWS_vagrant_script

## 괄호 안의 cent[1-3]은 각각 가상서버를 의미하며 생략 가능
## 생략하면 모든 서버를 실행/정지/삭제 

# 실행
vagrant up (cent[1-3])

# 정지
vagrant halt (cent[1-3])

# 삭제
vagrant destroy (cent[1-3])

# 상태확인
vagrant status (cent[1-3])
```

---
## 포트 및 프로세스 확인
```script
vagrant ssh cent[1-3] 
sudo netstat -nltpu
sudo ps -ef | grep (nginx|mysql|nfs)
```

---
## 웹 페이지 접속
 - URL : http://172.18.1.91/www/index.html
 - hosts를 수정한 경우 지정한 도메인으로 접속 가능
   - 웹서버 가상호스트 설정 : vws.tmpcompany.com

---
## 주의점
  - 이 스크립트에서 구현이 되지 않은 부분
    - Local IP사이의 통신 설정
    - Gateway설정을 추가하지 않기 위해 각 서버에 DMZ대역의 IP를 할당
  - 실제 서비스와의 다른 점
    - 데이터베이스와 스토리지는 실제 환경에서는 DMZ대역의 통신을 허용하지 않음

---
## Error case #1
  - 최신 버전의 OS image 에서 정상적으로 동작이 되지 않으면 다음을 추가. 
    - 최신 버전의 OS 대신 직전 버전으로 설정하여 동작하도록 함. 
```script
diff --git a/Vagrantfile b/Vagrantfile
index 255b3cb..ff4a69d 100644
--- a/Vagrantfile
+++ b/Vagrantfile
@@ -2,6 +2,7 @@ Vagrant.configure("2") do |config|

   # All server set
   config.vm.box = "rockylinux/8"
+  config.vm.box_version = "4.0.0"
   config.vm.box_check_update = true

   # Create cent1
```
---
## Error case #2
  - ssh 가 접속되지 않음에 대한 해결
    - vagrant ssh cent1 이후 cent1 에서 ssh cent2 로 cent2 로 접속되지 않는 현상
    - ssh 설정이 일부 전달되지 않아 발생 가능한 현상으로 직접 들어가 설정 진행. 

```script
# cent1, 2, 3서버에 root로 접속해서 작업
git clone https://github.com/yoons6548/VWS_vagrant_script.git
cp -rfp VWS_vagrant_script/CONF/ssh/* /root/.ssh/
chown root:root /root/.ssh -R
chmod 600 /root/.ssh/id_rsa
chmod 644 /root/.ssh/authorized_keys
```

---
