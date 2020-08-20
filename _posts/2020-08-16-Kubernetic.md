---
layout: post
title: "[Kubernetes] - GCP 기반 k8s환경구성"
author: Lee Wonseok
categories: Kubernetes
date: 2020-08-16 09:36
comments: true
cover: "/assets/kubernets.jpg"
tags: Kubernetes
---



#  KUBERNETES - GCP 기반의 k8S 환경구성

**머리말**  
쿠버네티스 환경을 구성하는 방법은 여러가지가 존재한다.  
각 서버를 준비하는 방법은 여러 가지가 있겠지만  
가장 쉽게 생각해볼 수 있는 건 ``VirtualBox`` 와 ``Vagrant`` 를 이용한 ``로컬 VM``로 구성하는 것이다.    
하지만 이번 포스트에서는 CLOUD에 익숙해지고 싶은 마음 + GCP 무료 크레딧이 아까운 마음에  
GCP로 진행해보았다.

   
 
---

**목차**

- [사전준비](#a1)
- [구성 (Install)](#a2)
- [PV, PVC 생명주기](#a3)
- [PV, PVC 생성](#a4)
- [볼륨 플러그인](#a5)



---

## 사전준비   <a name="a1"></a>

* **쿠버네티스는 3개월 마다 새로운 버전이 릴리즈 되고**  
       **해당 버전은 9개월 동안 버그와 보안 이슈를 수정하는 패치가 이루어집니다.**  


* **이번 포스트에 구성할 노드는 ``Master 노드 하나``와 ``Worker 노드 세 개``로  
    총 네 개의 서버가 필요합니다.**

* **노드의 최소 요구 사양은 다음과 같습니다.**


    |항목|사양|
    |---|-------|
    |CPU|	1 CPU 이상
    |메모리|	2 GB 이상
    |OS|	CentOS 7, RHEL 7, Ubuntu 16.04+ etc.


* **또한 각 서버는 다음 조건을 만족해야 합니다.**

    * **각 노드가 서로 ``네트워크 연결``되어 있어야 합니다.**
    * **각 노드는 다음 ``정보가 겹치지 않아야`` 합니다.**

        ```
        hostname: hostname
        MAC address: ip link 또는 ifconfig -a
        product_uuid: sudo cat /sys/class/dmi/id/product_uuid
        ```

* **각 노드가 사용하는 ``포트``입니다. ``각 포트는 모두 열려`` 있어야 합니다.**

    |노드	|프로토콜|	방향|	포트 범위|	목적|	누가 사용?|
    |---|----|---|-----|---|-----|
    |Master|	TCP|	Inbound|	6443|	Kubernetes API server|	All
    |Master|	TCP|	Inbound|	2379-2380|	etcd server client API|	kube-apiserver, etcd
    |Master|	TCP|	Inbound|	10250|	Kubelet API	|Self, Control plane
    |Master|	TCP|	Inbound|	10251|	kube-scheduler|	Self
    |Master|	TCP|	Inbound|	10252|	kube-controller-manager|	Self
    |Worker|	TCP|	Inbound|	10250|	Kubelet API	|Self, Control plane
    |Worker|	TCP|	Inbound|	30000-32767|	NodePort Services|	All|


---

## 구성 (Install)  <a name="a2"></a>   

### **구성 전 안내**
* **쿠버네티스를 처음으로 만들고, 공개한 회사가 구글이나 보니 구글 클라우드인  
 ``GCP``에서도 ``Kubernetes Engine``란 이름으로 쿠버네티스를 사용할 수 있는 서비스를 제공하고 있습니다.**

    ![스크린샷, 2020-08-20 12-34-57](https://user-images.githubusercontent.com/69498804/90713985-91cef580-e2e1-11ea-98f2-09c7e0f72c9f.png)

* **실제 해당 메뉴에 들어가보면 이렇게 설치 없이 바로 클러스터를 만들 수 있습니다.**
![스크린샷, 2020-08-20 12-36-07](https://user-images.githubusercontent.com/69498804/90714040-ba56ef80-e2e1-11ea-91a7-51616910e81e.png)

* **하지만 이번 포스트에서는 쿠버스프레이를 이용한  
구성방법을 포스팅 할 것이기 때문에 해당 서비스는 넘기겠습니다.**

---

### Compute Engine에 ``Kubespray`` 를 설치하여 환경 설정

* **``GCP Console``에 접속합니다.**  

* **``INSTANCE`` 동일하게 4개 생성해보겠습니다. (구성 방법은 GCP포스트에 있습니다!!)**  

    ![스크린샷, 2020-08-20 14-20-27](https://user-images.githubusercontent.com/69498804/90719826-4e2fb800-e2f0-11ea-94d4-b8f9dc1d31f4.png)

---

* **아래 처럼 인스턴스 4개를 생성 완료했습니다.**

    ![스크린샷, 2020-08-20 14-24-55](https://user-images.githubusercontent.com/69498804/90720126-eded4600-e2f0-11ea-963e-c7d5203642b8.png)

---

* **``공개키 설정``을 위해서 SSH 버튼을 눌러서 ``nasa-master``에 접속해  
    ``ssh-keygen -t rsa``를 입력해 공개키를 생성합니다.**  

    **(그 외에 옵션은 Enter를 눌러서 기본값을 넣어줍니다.)**

    ```
    [h43254@nasa-master ~]$ ssh-keygen -t rsa
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/h43254/.ssh/id_rsa): 
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /home/h43254/.ssh/id_rsa.
    Your public key has been saved in /home/h43254/.ssh/id_rsa.pub.
    The key fingerprint is:
    SHA256:sr6RS6MW/ESkXfbVBi9LTdRSfPm3a2F7VrC9K8e93mc h43254@nasa-master
    The key's randomart image is:
    +---[RSA 2048]----+
    |            .+o+o|
    |      . o   .++.+|
    |     + o . .o.o.o|
    |    . o   .. o. o|
    |   . .. S   .  +o|
    |    o .+      .+o|
    |     +*       o B|
    |    .+.+     . BE|
    |   .. +.      =**|
    +----[SHA256]-----+
    ```

* **키가 잘 만들어 있는지 확인하기 위해서 아래 명령어로 확인합니다.** 

    ```
    [h43254@nasa-master ~]$ cd .ssh
    [h43254@nasa-master .ssh]$ ls -al
    total 8
    drwx------. 2 h43254 h43254   38 Aug 20 05:31 .
    drwx------. 3 h43254 h43254   74 Aug 20 05:28 ..
    -rw-------. 1 h43254 h43254 1679 Aug 20 05:30 id_rsa
    -rw-r--r--. 1 h43254 h43254  400 Aug 20 05:30 id_rsa.pub       ## 정상 생성.
    ```
---
    
* **아래 명령어를 통해서 공개키를 복사하고, 나온 명령어를 복사합니다.**
    
    ```
    [h43254@nasa-master .ssh]$ cat id_rsa.pub 
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMcBYtD/NDrxGOyjPJ9DryBOWzoWlVszqI+jqSAUeAsZ+hwjTtyU60I3vuBn9Ge6HcgKfKUccUyGPickMyTXk2qzeMsa9iN0MOgLZ3GM//aFE5z6yoEvjPJ9KxQg9qRrLhUUWqYtBhyegBt26E+YdSWF24ZNutp7CRLtVQpwT/opMkY9XTseaD1kaj1BZF8ls2V5WNCgC504JfPKuKBVKcbuOwBIBv6TyZhhGXRWfKTKpma3/L5Yhc4qNOZGDo913/kkwlMpqPb4JQAEasXELfFPMou9vPOaKEK7CDdcJ/EOkXct7d43vnMRa8360okA+BMP7vJ4c4ElWW+T0op5rt h43254@nasa-master
    ```
---

* **GCP에 ``공개키를 등록``하기 위해서 ``Compute Engine - 메타데이터- SSH`` 에  서  공개키를 추가합니다.**  

    ![스크린샷, 2020-08-20 14-43-15](https://user-images.githubusercontent.com/69498804/90721321-7e2c8a80-e2f3-11ea-914e-8174cfc57d39.png)

    **복사한 키를 넣으면 자동으로 왼쪽에 아이디(여기서는  h43254)이 나타납니다.  
    아이디를 확인 한 다음에 저장을 누릅니다.**

    **(만약에 나타나지 않았다면 공개키 코드의 띄어쓰기 때문이니그 확인해야 합니다)**

---

* **메타데이터 정상 확인을 위해 ``NODE1``에 접속 후 확인합니다.**

    ![스크린샷, 2020-08-20 14-47-55](https://user-images.githubusercontent.com/69498804/90721650-25112680-e2f4-11ea-8d93-c6202a38f327.png)

    **터미널에서 ``cat .ssh/authorized_keys``를 쳐보면  
    아래와 같이 등록된 키를 확인할 수 있습니다.**


    **``자 이제 마스터 <-> 노드의 통신이 원활해졌습니다.``**

---