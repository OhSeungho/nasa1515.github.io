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
- [볼륨 플러그인](#a4)
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


* **생명주기 : 바인딩(Binding)**  

    바인딩은 프로비저닝을 통해 만들어진 PV를 PVC와 바인딩하는 단계입니다.  
    PVC가 원하는 스토리지의 용량과 접근방법을 명시해서 요청하면 거기에 맞는 PV가 할당됩니다.  
    이 때 PVC에 맞는 PV가 없다면 요청은 실패합니다.  
    하지만 한 번 실패했다고 요청이 끝나는건 아니고 계속해서 대기하고 있게 됩니다.  
    그러다가 기존에 사용하던 PV가 반납되거나 새로운 PV가 생성되서  
    PVC에 맞는 PV가 생기면 PVC에 바인딩이 됩니다.  
    PV와 PVC의 매핑은 1대1 관계입니다.  
    하나의 PVC가 여러개의 PV에 바인딩되는건 불가능 합니다.  

* **생명주기 : 사용중(Using)**  

    PVC는 포드에 설정되고 포드는 PVC를 볼륨으로 인식해서 사용하게 됩니다.  
    할당된 PVC는 포드가 유지되는 동안 계속해서 사용됩니다.  
    포드가 사용중인 PVC는 시스템에서 임의로 삭제할 수 없습니다.  
    이 기능을 ``스토리지 객체 보호 (Storage Object in Use Protection)`` 라고 합니다.  
    사용중인 데이터 스토리지를 임의로 삭제하게 되면 치명적인 결과를 초래할 수 있기 때문에 이런 보호 기능이 있습니다.  
    포드가 사용중인 PVC를 삭제하려고 하면 상태가 Terminating으로 되지만  
    해당 PVC를 사용중인 포드가 남아 있는 도중에는 삭제되지 않고 남아 있게 됩니다.  
    ``kubectl describe``으로 pvc의 상태를 확인해 보면  
    다음처럼 pvc-protection이 적용되어 있는걸 확인할 수 있습니다.  

        Finalizers:    [kubernetes.io/pvc-protection]


* **생명주기 : 리클레이밍(Reclaiming)** 

    사용이 끝난 PVC는 삭제 되고,  
    PVC가 사용중이던 PV를 ``초기화(reclaim)``하는 과정을 거치게 됩니다.  
    이것을 ``리클레이밍``이라고 합니다.  
    초기화 정책은 ``Retain``, ``Delete``, ``Recycle``의 3가지가 있습니다.

    * **Retain**  
Retain은 단어가 가지는 의미 그대로 PV를 그대로 보존해 둡니다. PVC가 삭제되면 사용중이던 PV는 해제상태만 되고 아직 다른 PVC에 의해 재사용 가능한 상태는 아니게 됩니다. 단순히 사용해제만 됐기 때문에 PV안의 데이터는 그대로 유지가 된채로 남아 있는 상태입니다. 이 PV를 재사용하려면 관리자가 다음 순서대로 직접 초기화를 해주어야 합니다.
PV 삭제. PV가 만약 외부 스토리지와 연계되어 있었다면 PV는 삭제되더라도 외부 스토리지의 볼륨은 그대로 남아 있는 상태가 됩니다.
스토리지에 남아 있는 데이터를 직접 정리.
남아 있는 스토리지의 볼륨을 삭제하거나 재사용하려면 그 볼륨을 이용하는 PV를 다시 만들어 줍니다.

    * **Delete**  
PV를 삭제하고 연계되어 있는 외부 스토리지 쪽의 볼륨도 삭제합니다. 프로비저닝할 때 동적볼륨할당으로 생성된 PV들은 기본 리클레임 정책이 Delete입니다. 필요하면 처음에 Delete로 설정된 PV의 리클레임 정책을 수정해서 사용해야 합니다.

    * **Recycle**  
recycle은 PV의 데이터들을 삭제하고 PV를 다시 새로운 PVC에서 사용가능하게 만들어 둡니다. 쿠버네티스에서 지원이 어렵다고해서 지금은 deprecated된 정책입니다. 데이터 초기화용으로 특별한 포드를 만들어두고 초기화할때 사용하는 기능도 있긴 하지만 PV의 데이터들을 초기화하는데 여러가지 상황들을 쿠버네티스에서 모두 지원하기는 어렵다고 판단해서 더 이상 지원하지 않게 되었습니다. 현재는 동적 볼륨 할당을 기본 사용하라고 추천하고 있습니다.

----

## 4. 볼륨 플러그인 <a name="a4"></a>  

* 쿠버네티스 에서 사용할 수 있는 볼륨 플러그인은 무수히 많다. 

    **[몇가지 예시]**  

    ![스크린샷, 2020-08-14 14-04-52](https://user-images.githubusercontent.com/69498804/90215628-25627b00-de37-11ea-8d8e-98d0b2dfcfd2.png)


* 대표적인 3가지는 아래 가지가 있다.

    * **empty** : 임시로 데이터를 저장하는 빈 볼륨  
    emptyDir은 개별적인 Pod에 적용할 수 있는 Volume으로서  
    Pod가 생성 될 시 비어있는 볼륨으로 생성된다.  
    만약 Pod 내에 여러 개의 컨테이너가 정의되어 생성될 경우  
    그 컨테이너들은 하나의 emptyDir을 공유하게 된다.  
    또한 Pod가 삭제될 경우 emptyDir 또한 삭제되기 때문에  
    Pod 내부 컨테이너 간에 공유해야 하는 휘발성 데이터를 저장하기 위해서 사용 될 수 있다.  
    아래의 예시는 두 개의 컨테이너가 하나의 Pod에서 emptyDir을 공유하는 것을 보여준다

        ```
        1 apiVersion: v1
        2 kind: Pod
        3 metadata:
        4 name: test-pod
        5 spec:
        6 containers:
        7    - image: ubuntu:14.04
        8    name: ubuntu-container
        9    command: ["tail","-f", "/dev/null"]
        10    volumeMounts:
        11        - mountPath: /data
        12        name: my-empty-volume
        13
        14    - image: nginx
        15    name: nginx-containe
        16    volumeMounts:
        17        - mountPath: /data
        18        name: my-empty-volume
        19
        20 volumes:
        21    - name: my-empty-volume
        22    emptyDir: {}
        ```

        ```
        [root@nasa1515]# kubectl create -f emp.yaml
        pod/emp-pod created
        --------------------------------------------------------------------------
        [root@nasa1515]# kubectl get po
        NAME                                   READY     STATUS    RESTARTS   AGE
        emp-pod                                 2/2       Running   0          39s
        ```

    * **한 컨테이너에서 /data에 파일을 생성할 경우  
    다른 컨테이너에서도 해당 파일에서 접근할 수 있다.**

        ```
        [root@nasa1515]# docker ps | grep ubuntu
        78a266359307        ubuntu@nasa1515:885bb6705b0... 
        --------------------------------------------------------------------------
        [root@nasa1515]# docker exec -it 78 bash
        root@emp-pod:/# ls
        bin  boot  data  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
        root@emp-pod:/# cd data/
        root@emp-pod:/data# echo test >> Test
        root@emp-pod:/data# exit
        exit
        
        --------------------------------------------------------------------------
        [root@nasa1515# docker ps | grep nginx
        7c72d8409845        nginx@nasa1515:d85914d547a6...
        [root@nasa1515]# docker exec -it 7c bash
        root@test-pod:/# ls data/
        Test
        ```

* **hostPath** :
* NFS 서버





---


**동적**
    
* 해당 깃에서 파일을 받아온다.
  
    ```
    git clone --single-branch --branch release-1.2 https://github.com/rook/rook.git
    Cloning into 'rook'...
    remote: Enumerating objects: 60, done.
    remote: Counting objects: 100% (60/60), done.
    remote: Compressing objects: 100% (26/26), done.
    remote: Total 40355 (delta 40), reused 51 (delta 34), pack-reused 40295
    Receiving objects: 100% (40355/40355), 13.76 MiB | 6.33 MiB/s, done.
    Resolving deltas: 100% (27455/27455), done.
    ```

* 해당 경로에서 common 을 실행시켜 기본 구축을 시켜준다
    ```
    /rook/cluster/examples/kubernetes/ceph$ kubectl create -f common.yaml
    ```

* 구축 뒤 해당 yaml 파일을 실행시킨다.
    
    ```
    kubectl create -f operator.yaml 
    configmap/rook-ceph-operator-config created
    deployment.apps/rook-ceph-operator created
    ```