---
layout: post
title: "[CentOS 7] 기존 LVM 파티션에 하드디스크 추가하기"
desc: "[CentOS 7] 기존 LVM 파티션에 하드디스크 추가하기"
categories: development
tags: linux
comments: true
keywords: "linux,CentOS7,LVM,partition"
---

- 기존 LVM 파티션에 새로운 하드 디스크를 추가합니다.
- 파티션 생성 - 물리/논리 볼륨 생성 및 변경 - 파일시스템 용량 변경
  
---


## 실습 환경

- 본 실습은 아래와 같은 운영체제와 하드디스크(SSD)로 진행하였습니다.

> 운영체제 : `CentOS 7.5`  
> /dev/xvda(사용 중인 디스크) : `68.7 GB`  
> /dev/xvdb(추가 SSD) : `137.4 GB`  

## 1. LVM

- LVM(Logical Volume Manager, 논리 볼륨 관리자)을 사용하면 여러 디스크를 단일 볼륨으로 사용할 수 있고, 디스크를 추가하여 파티션 용량을 늘릴 수 있습니다.  

- CentOS 7은 설치 시 기본으로 LVM을 이용하여 스토리지를 구축합니다.  


- `LVM의 원리`  

>  1) **LVM**은 `물리 범위(PE)`라는 작은 영역으로 이루어진 `물리 볼륨(PV)`으로 각 하드 디스크를 관리합니다.  
>  2) 하드 디스크가 여러 개 있을 때는 각 물리 범위를 하나로 모아서 `볼륨 그룹(VG)`으로 관리합니다.  
>  3) 사용자는 볼륨 그룹에서 필요한 만큼 꺼내 `논리 볼륨(LV)`으로 구성하여 사용합니다.  


## 2. LVM 상태 확인

- 물리 볼륨 상태 확인 : pvdisplay  

- 볼륨 그룹 상태 확인 : vgdisplay  

- 논리 볼륨 상태 확인 : lvdisplay  


## 3. 하드 디스크 추가하기

- 물리적인 하드 디스크를 추가한 다음 현재의 디스크 현황을 확인합니다.  

> fdisk -l  

![](/assets/img/blog/2020-04-04-centos7-disk_add/2019-04-04-15-18-13.png)  

- 추가할 하드 디스크(137.4 GB) 가 확인 됐습니다.  

### 3.1 파티션 생성하기

- 추가 하드 디스크(/dev/xvdb)에 gdisk 로 다음과 같이 파티션을 생성합니다.  

> gdisk /dev/xvdb  

```
GPT fdisk (gdisk) version 0.8.6

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries.

Command (? for help): n  # 파티션을 새로 생성
Partition number (1-128, default 1): 1  # 파티션 1을 생성

# 파티션의 시작 섹터와 종료 섹터를 설정
First sector (34-268435422, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-268435422, default = 268435422) or {+-}size{KMGTP}:
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): 8e00  # LVM 유형을 선택
Changed type of partition to 'Linux LVM'

Command (? for help): w  # 파티션 정보를 디스크에 기록하고 종료

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): y
OK; writing new GUID partition table (GPT) to /dev/xvdb.
The operation has completed successfully.
```


### 3.2 물리 볼륨 생성하기

- 생성한 파티션에 LVM 물리 볼륨을 생성합니다.  

> pvcreate /dev/xvdb1

- 생성된 물리 볼륨을 pvdisplay 명령으로 확인할 수 있습니다.  

> pvdisplay  

![](/assets/img/blog/2019-04-04-centos7-disk_add/2019-04-04-15-30-18.png)  


### 3.3 볼륨 그룹 추가하기

- 추가한 물리 볼륨(/dev/xvdb1)을 실행중인 `centos` 볼륨 그룹에 추가합니다.  

> vgextend centos /dev/xvdb1  

![](/assets/img/blog/2019-04-04-centos7-disk_add/2019-04-04-15-34-17.png)  

- 물리 볼륨이 `centos` 볼륨 그룹에 추가되었습니다.  


### 3.4 논리 볼륨 용량 변경하기

- -L 옵션으로 변경할 용량을 설정합니다. (K, M, G 단위사용)  
- 변경 전에 현재의 논리 볼륨을 확인해 보겠습니다.  


> lvdisplay  


![](/assets/img/blog/2019-04-04-centos7-disk_add/2019-04-04-15-39-09.png)  

- / (루트)에 마운트된 논리 볼륨 /dev/centos/root 에는 59.8 GB가 할당되어 있습니다.  
- 루트의 용량이 부족하므로 이 볼륨에 새로운 디스크를 추가하도록 하겠습니다.  


> lvextend -L +127G /dev/centos/root  


- 다시 논리 볼륨을 확인하면 용량이 추가된 것을 볼 수 있습니다.  


### 3.5 파일시스템 용량 변경하기

- 추가한 논리 볼륨을 실제 파일시스템에 적용해야 됩니다.  
- 파일시스템 용량을 변경하기에 앞서 df 명령어로 마운트 정보를 확인합니다.  

> df -h  

![](/assets/img/blog/2019-04-04-centos7-disk_add/2019-04-04-15-43-54.png)  


- /dev/mapper/centos-root 파일시스템의 용량을 변경하겠습니다.  

> xfs_growfs /dev/mapper/centos-root  


- 다시 df 명령어를 사용하여 용량을 확인합니다.  

![](/assets/img/blog/2019-04-04-centos7-disk_add/2019-04-04-15-47-13.png)  

- /(루트)의 용량이 187 GB로 변경 되었습니다.  


## 참고자료

- [CentOS 7 예비학교 - 후쿠다 카즈히로](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791160502886&orderClick=LEA&Kc=)