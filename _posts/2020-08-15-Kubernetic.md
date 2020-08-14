---
layout: post
title: "[Kubernetes] - 볼륨"
author: Lee Wonseok
categories: Kubernetes
date: 2020-08-15 09:36
comments: true
cover: "/assets/kubernets.jpg"
tags: Kubernetes
---



#  KUBERNETES - 볼륨

**머리말**  
 클라우드, 인프라쪽 엔지니어나 개발자를 지향하고 있다면 거의 필수적으로 알아야 할 기술인 것이 쿠버네틱스라고 하는데 아직은 너무 생소하다.  
 포스트를 작성하면서 조금씩 익숙해질 것이라고 생각한다.  
 이번 포스트에서는 쿠버네틱스의 볼륨에 대해서 알아보자.

   
 
---

**목차**

- [쿠버네티스의 볼륨](#a1)
- [PV, PVC](#a2)
- [PV, PVC 생명주기](#a3)
- [DB 구축](#a4)
- [DNS 구성](#a5)
- [오토스케일링 구축](#a6)
- [로드밸런서 구축](#a7)
- [서비스 동작 확인](#a8)


---

## 1. 쿠버네티스의 볼륨   <a name="a1"></a>

파드의 컨테이너는 이미지로부터 파일시스템을 제공받는다.  
그러나 파드가 종료되면 파드 내의 ``데이터(파일)``은 더 이상 사용 할 수 없게 된다.  

컨트롤러에 의해 새로운 파드가 생성이 되면 이미지로 부터 새로운 파일 시스템을 제공받는다.  
즉 컨테이너는 기본적으로 데이터를 유지하지 않으며, 이런 형태를 ``상태가 없다(Stateless)`` 라고 한다.
 
파드는 새로 생성된 데이터를 보존하기 위해 ``외부 저장소 볼륨``을 생성하고  
이런 볼륨을 컨테이너에 ``마운트``해서 사용한다.  
볼륨은 여러 파드에서 ``동시``에 접근이 가능하다.

* 기본적인 **볼륨**의 ``라이프사이클``은 **파드**의 ``라이프사이클``과 같다.  
    파드가 생성되고 삭제됨에 따라 볼륨도 같이 생성되고 삭제된다.  
    그러나 파드가 재 시작되면 볼륨의 데이터는 삭제되지 않고 유지되며,  
    재시작 된 파드에게 해당 볼륨의 데이터를 다시 제공해준다.  

    그러나 새로 도입된 ``PersistentVolume``, ``PersistentVloumeClaim``을 사용하며  
    볼륨만의 라이프 사이클은 분리 할 수 있게 되어  
    파드의 로직과 별도로 ``볼륨(스토리지)``을 사용 할 수 있게 되었다.


---

## 2. PV, PVC   <a name="a2"></a>


* **퍼시스턴트볼륨 (PersistentVolume, PV)**  
    
    쿠버네티스에서 볼륨을 사용하는 구조는 2개로 분리되어 있습니다.  
    PV는 볼륨 자체를 의미합니다.  
    클러스터내에서 리소스로 다뤄집니다.  
    포드하고는 별개로 관리되고 별도의 생명주기를 가지고 있습니다.  


* **퍼시스턴트볼륨클레임 (PersistentVolumeClaim)**

    PVC는 사용자가 PV에 하는 요청입니다.  
    사용하고 싶은 용량은 얼마인지 읽기/쓰기는 어떤 모드로 설정하고 싶은지 등을 정해서 요청합니다.  

* **내용**  

    쿠버네티스는 볼륨을 포드에 직접할당하는 방식이 아니라 이렇게 중간에 PVC를 둠으로써   
    포드와 포드가 사용할 스토리지를 분리했습니다.  
    이런 구조는 각자의 상황에 맞게 다양한 스토리지를 사용할 수 있게 해줍니다.  
    클라우드 서비스를 사용하는 경우에는 본인이 사용하는 클라우드 서비스에서 제공해주는 볼륨 서비스를 사용할 수도 있고,  
    사설로 직접 구축해서 사용중인 스토리지가 있다면 그걸 사용할 수도 있습니다.  
    이렇게 다양한 스토리지를 PV로 사용할 수 있지만 포드에 직접 연결하는게 아니라  
    PVC를 통해서 사용하기 때문에 포드는 자신이 어떤 스토리지를 사용하고 있는지 신경쓰지 않아도 됩니다.

----

## 3. PV, PVC 생명주기 <a name="a3"></a>  

* **PV와 PVC는 다음 그림에서 보이는 것 같은 생명주기를 가집니다.**


    ![스크린샷, 2020-08-14 12-11-40](https://user-images.githubusercontent.com/69498804/90209833-5470f080-de27-11ea-813a-45442d6dd942.png)



    **``생명주기`` : 프로비저닝(Provisioning)**  
    PV를 사용하기 위해선 먼저 PV가 만들어져 있어야 합니다.  
    이 PV를 만드는 단계를 ``프로비저닝``이라고 합니다.  
    PV 프로비저닝 방법에는 2가지가 있습니다.
    
    * ``정적(static)`` : PV를 미리 만들어두고 사용한다.  

        정적으로 PV를 준비한다는건 클러스터 관리자가 미리 적정용량의 PV를 만들어 두고 사용자들의 요청이 있으면 미리 만들어둔 PV를 할당해 주는 방식입니다.  
        사용할 수 있는 스토리지 용량에 제한이 있을 때 유용하게 사용할 수 있는 방법입니다.  
        사용자들에게 미리 만들어둔 PV의 용량이 100기가라면 150기가를 사용하려는 요청들은 실패하게 됩니다.  
        1테라짜리 스토리지를 사용한다고 하더라도 미리 만들어둔 PV의 용량이 150기가이상 되는 것이 없다면 요청이 실패하게 됩니다.  

    * ``동적(dynamic)`` : 요청이 있을때마다 PV를 만드는 방법.
    
        동적으로 PV를 준비하는건 미리 PV를 준비해두는 것이 아니고  
        사용자가 PVC를 통해서 요청을 했을때 PV를 생성해서 제공해 주는 방식입니다.  
        쿠버네티스 클러스터를 위해 1테라짜리 스토리지를 준비해 뒀다고 하면  
        사용자가 필요할 때 원하는 용량만큼을 생성해서 사용할 수 있습니다.  
        정적 PV생성과 달리 한번에 200기가 짜리도 필요하면 만들어 쓸 수 있습니다.  
        동적 프로비저닝을 위해서 PVC는 ``스토리지클래스(StorageClasses)``를 사용합니다.  
        스토리지클래스를 이용해서 원하는 스토리지에 PV를 생성합니다.


---


생명주기 : 바인딩(Binding)
바인딩은 프로비저닝을 통해 만들어진 PV를 PVC와 바인딩하는 단계입니다. PVC가 원하는 스토리지의 용량과 접근방법을 명시해서 요청하면 거기에 맞는 PV가 할당됩니다. 이 때 PVC에 맞는 PV가 없다면 요청은 실패합니다. 하지만 한 번 실패했다고 요청이 끝나는건 아니고 계속해서 대기하고 있게 됩니다. 그러다가 기존에 사용하던 PV가 반납되거나 새로운 PV가 생성되서 PVC에 맞는 PV가 생기면 PVC에 바인딩이 됩니다. PV와 PVC의 매핑은 1대1 관계입니다. 하나의 PVC가 여러개의 PV에 바인딩되는건 불가능 합니다.

생명주기 : 사용중(Using)
PVC는 포드에 설정되고 포드는 PVC를 볼륨으로 인식해서 사용하게 됩니다. 할당된 PVC는 포드가 유지되는 동안 계속해서 사용됩니다.   포드가 사용중인 PVC는 시스템에서 임의로 삭제할 수 없습니다. 이 기능을 사용중인 스토리지 객체 보호 (Storage Object in Use Protection) 라고 합니다. 사용중인 데이터 스토리지를 임의로 삭제하게 되면 치명적인 결과를 초래할 수 있기 때문에 이런 보호 기능이 있습니다. 포드가 사용중인 PVC를 삭제하려고 하면 상태가 Terminating으로 되지만 해당 PVC를 사용중인 포드가 남아 있는 도중에는 삭제되지 않고 남아 있게 됩니다. kubectl describe으로 pvc의 상태를 확인해 보면 다음처럼 pvc-protection이 적용되어 있는걸 확인할 수 있습니다.
Finalizers:    [kubernetes.io/pvc-protection]


생명주기 : 리클레이밍(Reclaiming)
사용이 끝난 PVC는 삭제가 되고 PVC가 사용중이던 PV를 초기화(reclaim)하는 과정을 거치게 됩니다. 이걸 리클레이밍이라고 합니다. 초기화 정책은 Retain, Delete, Recycle의 3가지가 있습니다.

Retain
Retain은 단어가 가지는 의미 그대로 PV를 그대로 보존해 둡니다. PVC가 삭제되면 사용중이던 PV는 해제상태만 되고 아직 다른 PVC에 의해 재사용 가능한 상태는 아니게 됩니다. 단순히 사용해제만 됐기 때문에 PV안의 데이터는 그대로 유지가 된채로 남아 있는 상태입니다. 이 PV를 재사용하려면 관리자가 다음 순서대로 직접 초기화를 해주어야 합니다.
PV 삭제. PV가 만약 외부 스토리지와 연계되어 있었다면 PV는 삭제되더라도 외부 스토리지의 볼륨은 그대로 남아 있는 상태가 됩니다.
스토리지에 남아 있는 데이터를 직접 정리.
남아 있는 스토리지의 볼륨을 삭제하거나 재사용하려면 그 볼륨을 이용하는 PV를 다시 만들어 줍니다.

Delete
PV를 삭제하고 연계되어 있는 외부 스토리지 쪽의 볼륨도 삭제합니다. 프로비저닝할 때 동적볼륨할당으로 생성된 PV들은 기본 리클레임 정책이 Delete입니다. 필요하면 처음에 Delete로 설정된 PV의 리클레임 정책을 수정해서 사용해야 합니다.

Recycle
recycle은 PV의 데이터들을 삭제하고 PV를 다시 새로운 PVC에서 사용가능하게 만들어 둡니다. 쿠버네티스에서 지원이 어렵다고해서 지금은 deprecated된 정책입니다. 데이터 초기화용으로 특별한 포드를 만들어두고 초기화할때 사용하는 기능도 있긴 하지만 PV의 데이터들을 초기화하는데 여러가지 상황들을 쿠버네티스에서 모두 지원하기는 어렵다고 판단해서 더 이상 지원하지 않게 되었습니다. 현재는 동적 볼륨 할당을 기본 사용하라고 추천하고 있습니다.

퍼시스턴트볼륨(PersistentVolume) 템플릿
퍼시스턴트 볼륨 템플릿은 다음과 같은 구조입니다.
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-hostpath
spec:
  capacity:
    storage: 2Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  storageClassName: manual
  persistentVolumeReclaimPolicy: Delete
  hostPath:
    path: /tmp/k8s-pv

apiVersion, kind, metadata부분은 다른 것들과 비슷한 구조입니다. spec부분을 주로 살펴 보도록 하겠습니다. 먼저 spec의 capacity부분을 보면 storage용량으로 1기가를 설정한걸 알 수 있습니다. 현재는 용량 관련한 설정만 가능하지만 앞으로는 IOPS나 throughput등도 설정할 수 있도록 추가될 예정입니다. volumeMode는 쿠버네티스 1.9버전에 알파 기능으로 추가된 옵션입니다. 기본값은 filesystem으로 볼륨을 파일시스템형식으로 붙여서 사용하게 합니다. 추가로 설정가능한 옵션은 raw입니다. 볼륨을 로우블록디바이스형식으로 붙여서 사용할 수 있게 해줍니다. 로우블록디바이스를 지원하는 플러그인들은 AWSElasticBlockStore, AzureDisk, FC (Fibre Channel), GCEPersistentDisk, iSCSI, Local volume, RBD (Ceph Block Device) 등이 있습니다.
accessModes는 볼륨의 읽기/쓰기에 관한 옵션을 지정합니다. 볼륨은 한번에 하나의 accessModes만 설정할 수 있고, 다음 3가지중 하나를 지정할 수 있습니다.
ReadWriteOnce : 하나의 노드가 볼륨을 읽기/쓰기 가능하게 마운트할 수 있음.
ReadOnlyMany : 여러개의 노드가 읽기 전용으로 마운트할 수 있음.
ReadWriteMany : 여러개의 노드가 읽기/쓰기 가능할게 마운트할 수 있음.

---------


NFS SERVER  설치

    # sudo apt install -y nfs-kernel-server

디렉토리 생성 (사용하기위한)
    # sudo apt install -y nfs-kernel-server

etc/exports 에 아래 내용 추가



        /nfs-volume     *(rw,sync,subtree_check)


index 파일을 하나 만들어줍니다.

    vagrant@kube-master1:~/kubernetes$ echo "HELLO NFS VOLUME" | sudo tee /nfs-volume/index.html
    HELLO NFS VOLUME

nfs 디렉토리를 설정해줍니다.

    vagrant@kube-master1:~/kubernetes$ sudo chown -R nobody:nobody /nfs-volume/
    chown: invalid group: ‘nobody:nobody’
    vagrant@kube-master1:~/kubernetes$ sudo chown -R nobody:nogroup /nfs-volume/
    vagrant@kube-master1:~/kubernetes$ sudo chmod 777 /nfs-volume/
    vagrant@kube-master1:~/kubernetes$ ls -lart /nfs-volume/
    total 12
    drwxr-xr-x 25 root   root    4096 Aug 14 03:19 ..
    -rw-r--r--  1 nobody nogroup   17 Aug 14 03:22 index.html
    drwxrwxrwx  2 nobody nogroup 4096 Aug 14 03:22 .


데몬 재 시작 후 정상 구동 확인.

    vagrant@kube-master1:~/kubernetes$ sudo systemctl restart nfs-kernel-server.service 
    vagrant@kube-master1:~/kubernetes$ 
    vagrant@kube-master1:~/kubernetes$ systemctl status nfs-kernel-server.service 
    ● nfs-server.service - NFS server and services
    Loaded: loaded (/lib/systemd/system/nfs-server.service; enabled; vendor preset: enabled)
    Active: active (exited) since Fri 2020-08-14 03:24:03 UTC; 10s ago
    Process: 11112 ExecStopPost=/usr/sbin/exportfs -f (code=exited, status=0/SUCCESS)
    Process: 11111 ExecStopPost=/usr/sbin/exportfs -au (code=exited, status=0/SUCCESS)
    Process: 11110 ExecStop=/usr/sbin/rpc.nfsd 0 (code=exited, status=0/SUCCESS)
    Process: 11142 ExecStart=/usr/sbin/rpc.nfsd $RPCNFSDARGS (code=exited, status=0/SUCCESS)
    Process: 11141 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=0/SUCCESS)
    Main PID: 11142 (code=exited, status=0/SUCCESS)

확인

    vagrant@kube-master1:~/kubernetes$ sudo exportfs
    /nfs-volume   	<world>


nfs PORT 오픈

    vagrant@kube-master1:~/kubernetes$ sudo iptables -A INPUT -p tcp --dport 2039 -j ACCEPT
    vagrant@kube-master1:~/kubernetes$ sudo iptables -A INPUT -p udp --dport 2039 -j ACCEPT



