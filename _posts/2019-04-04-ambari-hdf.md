---
layout: post
title: 호튼웍스 HDP 환경에 HDF(Nifi) 설치
desc: 호튼웍스 HDP 환경에 HDF(Nifi) 설치
categories: data
tags: engineering
comments: true
keywords: "hdp,hdf,nifi"
---

- Ambari를 이용하여 호튼웍스의 HDP 환경에 Nifi를 설치합니다.
- Nifi는 클러스터 구성이 가능한 분산 애플리케이션으로 데이터 파이프라인을 구축에 사용됩니다.
  
---


## 실습 환경


- 본 실습은 아래와 같은 운영체제와 버전 환경에서 진행하였습니다.

> 운영체제 : `CentOS 7.5`  
> Ambari : `2.7.3.0`  
> HDP : `3.1.0.0-78`  
> HDF : `3.3.1`  


## 1. 설치 버전 확인


- `HDF(Hortonworks Data Flow)`는 Nifi를 기반으로 카프카, 스톰 등의 서비스 사용이 가능한 플랫폼 입니다.  

- Ambari를 사용해서 단독 플랫폼으로 사용하는 것도 가능하지만, 저는 HDP 환경을 구성하여 테스트 중이기 때문에 `HDP 환경에 설치하는 방법`을 알아보도록 하겠습니다.  
  - HDP 환경을 구성하는 방법은 [해당 포스트](https://sungchul-p.github.io/data/2019/03/25/ambari-hadoop/)를 참고해 주시기 바랍니다.

- 호튼웍스 가이드에 따르면 HDF 설치 전에 Ambari와 HDP 를 최신버전으로 업그레이드 할 것을 권하고 있습니다.  
  - 현재 최신 버전으로 구성된 상태이므로, 이 과정은 생략하도록 하겠습니다.  
  
- HDP에 설치된 서비스와의 호환성 때문이므로 릴리즈 노트를 참고하여 HDF 버전을 선택하셔서 진행하시면 됩니다.  


![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-19-48-59.png)  


- `HDF-3.3.1 버전`으로 설치를 진행하도록 하겠습니다.  



## 2. HDF Management Pack 설치


- Management Pack은 HDF 각 버전의 [릴리즈 노트](https://docs.hortonworks.com/HDPDocuments/HDF3/HDF-3.3.1/release-notes/content/hdf_repository_locations.html)에서 운영체제에 맞게 다운로드 받을 수 있습니다.  


![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-19-54-26.png)  


- 다운로드 받은 경로를 \-\-mpack 옵션으로 지정해주고 설치는 진행합니다.  

> ambari-server install-mpack \  
> \-\-mpack=/tmp/hdf-ambari-mpack-3.3.1.0-10.tar.gz \  
> \-\-verbose  


- 정상적으로 설치 되면 다음과 같은 로그를 확인할 수 있습니다.  


```
==========  중 략 ===========
INFO: Loading properties from /etc/ambari-server/conf/ambari.properties
INFO: Successfully switched addon services using config file /var/lib/ambari-server/resources/mpacks/hdf-ambari-mpack-3.3.1.0-10/hooks/HDF-3.3.json

INFO: Loading properties from /etc/ambari-server/conf/ambari.properties

Ambari Server 'install-mpack' completed successfully.
```


## 3. HDF Base URL 업데이트

- Management Pack이 설치 되면 Ambari 환경에서 서비스 설치 및 관리를 진행할 수 있습니다.  

- 설치를 진행하기 전에 HDF Base URL 을 체크해야 합니다.  

1) Ambari 웹 환경에서 우측 상단의 admin을 클릭하여 Manage Ambari로 이동합니다.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-03-13.png)  


2) 좌측 메뉴 중 Version를 클릭한 다음, HDP Version 링크를 클릭합니다.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-03-51.png)  


3) HDF Base URL을 HDF 버전에 맞게 업데이트 합니다. ([릴리즈 노트](https://docs.hortonworks.com/HDPDocuments/HDF3/HDF-3.3.1/release-notes/content/hdf_repository_locations.html)를 참고하세요.)  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-02-11.png)  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-02-20.png)  



## 4. Nifi 서비스 설치 및 설정


- Ambari 대시보드에서 Services 옆의 "..." 버튼을 클릭하여 Add Service 를 선택합니다.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-07-03.png)  


- Add Service Wizard에서 설치할 서비스를 선택하고 Next 를 클릭합니다.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-07-57.png)  


- 서비스를 설치 할 노드를 선택하면 되는데, Nifi를 클러스터로 구성하기 위해 모든 노드에 설치를 진행했습니다.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-08-54.png)  


- Assign Slaves and Clients 화면에서는 서버 외에 추가적으로 필요한 인증 및 매니저 서비스를 설치할 노드를 지정해 주면 됩니다.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-25-47.png)  


- Ambari는 각 서비스에 필수적으로 필요한 설정 값을 표시해 줍니다.  

- 추가한 서비스 중 빨간색 원으로 표시된 곳만 찾아서 설정해 주면 됩니다.  

  - 대부분 비밀번호 설정이므로, 요구사항에 맞게 설정해 주세요.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-26-06.png)  



## 5. 트러블 슈팅

- 일부 노드에서 서비스가 제대로 시작되지 않는 경우, 서비스 포트를 확인해야 합니다.  

- HDP에서 사용중인 포트는 중복되지 않으나, 다른 서비스를 올린 경우 체크 하시기 바랍니다.  

- Nifi가 기본으로 사용하는 포트는 `9088` 입니다.  

- 저의 경우, 기존 서비스와 포트가 충돌되어 설정을 변경하였습니다.  

- Ambari > Nifi > Configs > Advanced nifi-ambari-config  

	- Nifi protocol port : `9099` 로 변경

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-22-20.png)  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-22-28.png)  


- 정상적으로 실행된 Nifi 서비스 화면 입니다.  

![](/assets/img/blog/2019-04-04-ambari-hdf/2019-04-04-20-32-59.png)  



## 참고자료

- [호튼웍스 - Installing HDF Services on an Existing HDP Cluster](https://docs.hortonworks.com/HDPDocuments/HDF3/HDF-3.3.1/installing-hdf-on-hdp/content/hdf-upgrade-ambari-and-hdp.html)