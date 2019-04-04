---
layout: post
title: Ambari를 이용한 호튼웍스 HDP-3.1 설치
desc: Ambari를 이용한 호튼웍스 HDP-3.1 설치
categories: data
tags: engineering
comments: true
keywords: "ambari,hdp,hadoop,cluster"
---

- Ambari를 이용하여 호튼웍스의 HDP-3.1 환경을 구축합니다.
	- 데이터 분산 환경을 쉽게 설치하고,
	- 다양한 서비스를 경험해 보는 것에 집중합니다.
  
---


## 실습 환경

- 본 실습은 아래와 같은 스펙의 서버 **3대**와 Ambari 2.7 버전을 사용하여 진행하였습니다.

> 운영체제 : `CentOS 7.5`  
> CPU : `Ryzen 8core`  
> 메모리(RAM) : `32 GB`  
> =======================  
> Ambari : `2.7.3.0`  
> HDP : `3.1.0.0-78`  

## 1. 설치 전 설정


### 1.1 호스트 파일 수정

- HDP(Hortonworks Data Platform)는 클러스터 환경을 구축할 때 IP 기준이 아닌 FQDN(Fully Qualified Domain Name) 방식으로 통신 합니다.


- 따라서 각 서버에는 각 노드에 대한 도메인 이름이 **`반드시`** 설정되어 있어야 합니다.


> sudo vi etc/hosts  


- 마스터 노드 1대와 워커 노드 2대로 구성할 계획이므로, 그에 맞춰 도메인 이름을 지정했습니다.


```
192.168.103.104 master.encore.com class14
192.168.103.105 worker1.encore.com class15
192.168.103.106 worker2.encore.com class16
```


### 1.2 SSH 설정

- 클러스터 환경을 위해 각 노드는 비밀번호 없이 SSH 접속이 가능해야 합니다.

- 이를 위해 공개키를 발급하여 서로의 노드에 복사합니다.

- 단순 실습 목적으로 설치하신다면, root 계정을 사용하시는 것이 편리합니다.
  - Ambari 공식 가이드에서도 root 계정으로 설치를 진행합니다.


- 여기서는 계정을 새로 생성하여 진행 해보겠습니다.


- 'ambari' 계정을 생성하고, 비밀번호를 부여합니다.


> adduser ambari  
> passwd ambari  


- root 계정이 아닌 다른 계정을 사용할 경우, `sudo 명령을 비밀번호 입력 없이` 사용할 수 있어야 합니다.
  - root 권한의 디렉터리에 설치가 진행되기 때문입니다.


- sudoers 파일에 아래의 내용을 추가합니다.


> sudo vi /etc/sudoers  
> ambari	ALL=NOPASSWD:	ALL


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-20-56-55.png)  

- 키젠을 이용하여 공개키와 비밀키를 생성합니다.

> ssh-keygen  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-04-36.png)  


- 생성된 공개키를 `계정@호스트명`으로 각 노드에 전달합니다.
  - 3개의 노드에서 모두 실행해야 합니다!


> ssh-copy-id ambari@class14  
> ssh-copy-id ambari@class15  
> ssh-copy-id ambari@class16  


- 키가 제대로 전달 됐는지 확인합니다.

> cat ~/.ssh/authorized_keys


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-09-00.png)  


- 이제 ssh 명령으로 비밀번호 없이 접속이 가능합니다.

> ssh class15


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-13-25.png)  


```
 ※ 참고 ※
 
 위의 설정을 모두 진행 했는데도 비밀번호를 요구하는 경우 비밀키를 직접 등록합니다.

 1) ssh-agent /bin/sh 명령으로 쉘 모드로 진입 합니다.

 2) ssh-add 명령을 수행하면 비밀키가 등록됩니다.
```  


### 1.3 데이터베이스 설치 및 설정

- 서비스를 어느 노드에 설치할 것인지 결정하지 않았기 때문에 `모든 노드에 설정을 미리 진행`합니다.  


- **MySQL 및 JDBC 드라이버 설치**
  - 데이터베이스가 필요한 서비스를 위해 MySQL 설치를 진행합니다.
  - 사용중인 데이터베이스가 있으면 연결하는 것도 가능합니다.  


> sudo yum install mysql  
> sudo yum install mysql-connector-java*  



- **MySQL 서버 설치**
  - 필수 조건은 아니지만, 서버가 필요한 서비스를 위해 미리 설치를 진행합니다.  



> sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-23-56.png)  


> sudo yum install mysql-community-server  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-24-05.png)  


- **MySQL 서비스 시작 및 root 비밀번호 설정**
  - root 비밀번호를 무작위로 생성한 다음, 초기화를 진행하여 원하는 비밀번호로 변경합니다.  


> grep 'A temporary password is generated for root@localhost' /var/log/mysqld.log |tail -1  
> sudo /usr/bin/mysql_secure_installation  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-26-15.png)  


- **서비스에 사용될 DB 및 계정 생성**
  - 설치할 서비스에 대해서만 생성을 진행하면 됩니다.
  - 저는 우선, DB와 연관된 서비스 중 Hive만 사용할 것이므로 Hive에 대한 내용만 진행하겠습니다.
  - 다른 설정이 궁금하신 분은 [공식문서 - mysql 설정](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-installation/content/database-config-mysql.html)을 참고하시기 바랍니다.  



- 원하는 비밀번호 설정 : ~~ IDENTIFIED BY '`비밀번호`';
- Hive를 설치할 노드의 도메인 이름 지정 : TO 'hive'@'`도메인 이름`'


```
CREATE USER 'hive'@'localhost' IDENTIFIED BY 'Ambari1@';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'localhost';

CREATE USER 'hive'@'%' IDENTIFIED BY 'Ambari1@';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'%';

CREATE USER 'hive'@'class15.encore.com' IDENTIFIED BY 'Ambari1@';
GRANT ALL PRIVILEGES ON *.* TO 'hive'@'class15.encore.com';

FLUSH PRIVILEGES;

create database hive;
```


## 2. Ambari 설치 시작

### 2.1 레포지터리 다운로드 및 설치

> sudo wget -nv http://public-repo-1.hortonworks.com/ambari/centos7/2.x/updates/2.7.3.0/ambari.repo -O /etc/yum.repos.d/ambari.repo  
>   
> sudo yum repolist  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-41-04.png)  


### 2.2 Ambari-server 설치

- Ambari-server 는 `마스터 노드로 사용할 곳에서만 설치를 진행`하면 됩니다.

- 패키지를 설치 합니다.


> sudo yum install ambari-server  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-46-59.png)  

- GPG-KEY 다운로드에 대한 승인을 진행해야 합니다.  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-47-25.png)  


- MySQL JDBC 드라이버의 경로를 지정해 줍니다.


> sudo ambari-server setup --jdbc-db=mysql --jdbc-driver=/usr/share/java/mysql-connector-java.jar  


- 준비가 완료 됐으므로, 설치를 진행합니다.


> sudo ambari-server setup  


- Ambari는 서버 설치에 필요한 설정 및 JDK 등을 체크하여 자동으로 설치를 진행해 줍니다.
  - 진행 로그 중 일부는 다음과 같습니다.


```
Using python  /usr/bin/python
Setup ambari-server
Checking SELinux...
SELinux status is 'disabled'
Customize user account for ambari-server daemon [y/n] (n)?
Adjusting ambari-server permissions and ownership...
Checking firewall status...
WARNING: iptables is running. Confirm the necessary Ambari ports are accessible. Refer to the Ambari documentation for more details on ports.
OK to continue [y/n] (y)?
Checking JDK...
[1] Oracle JDK 1.8 + Java Cryptography Extension (JCE) Policy Files 8
[2] Custom JDK
==============================================================================
Enter choice (1):
To download the Oracle JDK and the Java Cryptography Extension (JCE) Policy Files you must accept the license terms found at http://www.oracle.com/technetwork/java/javase/terms/license/index.html and not accepting will cancel the Ambari Server setup and you must install the JDK and JCE files manually.
Do you accept the Oracle Binary Code License Agreement [y/n] (y)?
Downloading JDK from http://public-repo-1.hortonworks.com/ARTIFACTS/jdk-8u112-linux-x64.tar.gz to /var/lib/ambari-server/resources/jdk-8u112-linux-x64.tar.gz
jdk-8u112-linux-x64.tar.gz... 100% (174.7 MB of 174.7 MB)
Successfully downloaded JDK distribution to /var/lib/ambari-server/resources/jdk-8u112-linux-x64.tar.gz
Installing JDK to /usr/jdk64/
Successfully installed JDK to /usr/jdk64/

===============================  생    략  ==================================
```

### 2.3 Ambari-server 실행

> sudo ambari-server start  

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-55-07.png)  


> sudo ambari-server status  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-55-22.png)  


- 설치를 진행할 때 많은 포트를 사용하므로, 방화벽을 내려야 원활하게 진행됩니다.
  - 방화벽은 모든 노드에서 내려야 합니다.


> sudo systemctl disable firewalld  
> sudo service firewalld stop  


- 정상적으로 ambari-server가 설치됐다면, 8080 포트로 브라우저에서 접속할 수 있습니다.
  - 마스터 노드가 192.168.103.104 이므로 192.168.103.104:8080 으로 접속하면 됩니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-25-21-57-25.png)  


- 이제 Ambari의 설치 마법사에 따라 진행하면 됩니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-21-26.png)  


1) Select Version

- 설치할 HDP 버전과 레포지터리를 지정합니다.
  - HDP버전은 HDP-3.1.0.0을 선택하고,
  - 레포지터리는 CentOS 7 환경이므로 redhat7 을 제외하고 모두 제거했습니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-24-29.png)  


2) Install Options

- Target Hosts
  - /etc/hosts 파일에 정의한 FQDN(Fully Qualified Domain Name)을 복사해서 붙여넣습니다.

- SSH Private Key
  - 마스터 노드에 있는 ~/.ssh/id_rsa 의 내용을 모두 복사해서 붙여 넣습니다.

- SSH User Account
  - root 대신 ambari 라는 계정을 만들었으므로, 해당 계정 이름을 입력합니다.


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-27-48.png)  


3) Confirm Hosts

- 이 과정에서 방화벽 해제가 되어 있지 않은 노드가 있으면 다음과 같은 오류가 발생합니다.  

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-30-43.png)  

- 아직 방화벽이 활성화 되어있는 노드에 설정을 다시 한번 진행합니다.

  > sudo systemctl disable firewalld  
  > sudo service firewalld stop  

- 노드간 통신이 원활하면 다음과 같이 완료됩니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-32-00.png)  


4) Choose Services

- 설치할 서비스들을 선택하여 설치를 진행합니다.
  - 서비스 추가는 나중에도 쉽게 할 수 있으므로 바로 사용할 서비스들만 선택하실 것을 추천합니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-36-24.png)  


5) Assign Masters

- 각 서비스를 설치할 노드를 지정할 수 있습니다.  
- 저는 Ambari가 지정해 준 값 그대로 진행 했습니다.
  - Kafka의 경우만 클러스터로 사용하기 위해 모든 노드에 Broker를 설치했습니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-39-50.png)  


6) Assign Slaves and Clients  

- 그 외 추가적으로 필요한 서비스 서버 및 클라이언트를 지정하여 설치할 수 있습니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-41-23.png)  


7) Customize Services

- 각 서비스에 대한 계정, 데이터베이스, 디렉터리, 설정 등을 변경할 수 있습니다.
- Ambari에서 요구하는 항목만 변경해 주고 진행하면 됩니다.
  - 데이터베이스를 사용하는 서비스는 테스트가 성공해야 정상적으로 진행됩니다.
  - 설정 변경이 필요한 서비스는 탭에 빨간색 원으로 표시됩니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-45-14.png)  


8) Review

- 마지막으로, 설치를 진행할 내역을 확인합니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-45-32.png)  


9) Install, Start and Test

- 정상적으로 설치가 완료됐으나, 다음과 같이 Warning이 뜨는 경우는 단순히 서비스가 시작이 안 된 것이므로 넘어가면 됩니다.  

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-46-09.png)  


10) Summary 

- 설치 결과를 확인합니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-49-37.png)  


- 설치가 완료되면 다음과 같은 화면을 볼 수 있습니다.
  - 서비스가 시작되지 않았으므로, Services 탭의 "..." 버튼을 클릭하여 `Start All`을 클릭합니다.

![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-50-48.png)  


- 모든 서비스가 정상적으로 시작되면, 대시보드를 통해 모니터링 할 수 있습니다.  


![](/assets/img/blog/2019-03-25-ambari-hadoop/2019-03-26-09-51-23.png)

---

## 참고자료

- [호튼웍스 - Apache Ambari Installation](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-installation/content/ch_Getting_Ready.html)