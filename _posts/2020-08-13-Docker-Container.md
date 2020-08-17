---
layout: post
title: "[DOCKER] - 이미지 관리"
author: Lee Wonseok
categories: DOCKER
date: 2020-08-13 10:55
comments: true
tags: [DOCKER]
cover: https://res.cloudinary.com/yangeok/image/upload/v1593160497/logo/posts/iot-protocol.jpg
---



# [DOCKER]  이미지 관리

**머리말**  
 이전 포스트에서는 도커의 설치방법에 대해서 간단하게 포스팅 했었다.  
 이번 포스트에서는 실제 도커의 컨테이너의 생성 관리 방법 및 명령어들을 포스트 했다.


---


**목차**
- [도커 이미지](#a1)
- [도커 이미지 명령어](#a2)

---

## 도커 이미지 <a name="a1"></a>  

* 도커는 기본적으로 ``도커 허브``라고 하는 중앙 이미지 저장소에서 이미지를 내려받습니다.  
도커 허브는 도커가 공식적으로 제공하고 있는 이미지 저장소로 쉽게 올리고 내려받을 수 있습니다.


    ![](https://miro.medium.com/max/1104/1*ttU6oMoZztKk2kjJid6PuQ.png)


* **Docker Hub**  
    도커 허브는 도커에서 제공하는 기본 이미지 저장소로 ubuntu, centos, debian등의 베이스 이미지와  
    ruby, golang, java, python 등의 공식 이미지가 저장되어 있습니다.  
    일반 사용자들이 만든 이미지도 50만 개가 넘게 저장되어 있고 다운로드 횟수는 80억 회를 넘습니다.

    회원가입만하면 대용량의 이미지를 무료로 저장할 수 있고  
    다운로드 트래픽 또한 무료입니다.  
    단, 기본적으로 모든 이미지는 공개되어 누구나 접근 가능하므로 비공개로 사용하려면 유료 서비스를 이용해야 합니다.

* **회원가입**  
    아래 링크에서 Dokcer hub 회원 가입 후 포스트를 읽는 것을 추천드립니다!!.  
   https://hub.docker.com/  

![](https://subicura.com/assets/article_images/2017-02-10-docker-guide-for-beginners-create-image-and-deploy/docker-hub.png)

---

## 도커 이미지 명령어 <a name="a2"></a>  

* **``docker images``** 명령어를 사용해 현재 HOST가 가지고 있는 이미지를 확인 할 수 있다. 

    ```
    nasa1515@nasa:~$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED                 SIZE
    hello-world         latest              bf756fb1ae65        7 months ago            13.3kB
    ```
    이전 포스트에서 ``hello-world`` 컨테이너를 실행하여 남아 있는 것을 확인.

---

* **``docker search``** 명령어를 사용해 docker hub에 존재하는 이미지를 검색 할 수 있습니다.  
    우선 centos os를 한번 검색해보겠습니다.  

    ```
    nasa1515@nasa:~$ docker search centos
    NAME                               DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
    centos                             The official build of CentOS.                   6134                [OK]                
    ansible/centos7-ansible            Ansible on Centos7                              132                                     [OK]
    consol/centos-xfce-vnc             Centos container with "headless" VNC session…   119                                     [OK]
    jdeathe/centos-ssh                 OpenSSH / Supervisor / EPEL/IUS/SCL Repos - …   115                                     [OK]
    centos/systemd                     systemd enabled base container.                 87                                      [OK]
    centos/mysql-57-centos7            MySQL 5.7 SQL database server                   80                                      
    imagine10255/centos6-lnmp-php56    centos6-lnmp-php56                              58                                      [OK]
    ...... (중략)    
    ```



    * **이미지 필드에 대한 설명은 다음과 같습니다.**
        *  ``NAME`` : 이미지 저장소의 이름
        *  ``DESCRIPTION`` : 이미지에 대한 설명
        *  ``STATS`` : 이미지에 대한 평가점수
        *  ``OFFICIAL`` : 공식 이미지 여부
        *  ``AUTOMATED`` : 자동화 빌드 여부

---

* **``docker pull``** 명령어를 사용해 docker hub에 존재하는 이미지를 다운로드 할 수 있습니다.  
    명령어의 사용법은 아래와 같습니다.

    ```
    $ docker pull --help


    Usage: docker pull [OPTION] NAME[:TAG|@DIGEST]
    ```

    *  NAME: search 명령의 결과의 name과 동일합니다.  
    *  추가로 ``TAG``, ``@DIGEST``를 사용하는데 이는 저장소의 실제 이미지를 구분하는 것입니다.  
    *  ``TAG`` : 보통 버전을 나타내거나 특성을 나타냅니다.
    *  ``@DIGEST`` : 해시처럼 이미지의 무결성을 검증하는데 사용합니다.  
    *  두개의 옵션을 모두 생략한다면 자동으로 TAG에 ``latest``가 부여되어 다운로드 됩니다.


* **우선 centos os를 TAG없이 한번 받아오겠습니다.**  
    
    ```
    nasa1515@nasa:~$ docker pull centos
    Using default tag: latest
    latest: Pulling from library/centos
    3c72a8ed6814: Pull complete 
    Digest: sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
    Status: Downloaded newer image for centos:latest
    docker.io/library/centos:latest

    nasa1515@nasa:~$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    centos              latest              0d120b6ccaa8        6 days ago          215MB
    hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
    ```

* **이번에는 TAG를 ``mysql:5.7``로 부여 후 mysql을 받아와 보겠습니다.**  

    ```
    nasa1515@nasa:~$ docker pull mysql:5.7
    5.7: Pulling from library/mysql
    bf5952930446: Pull complete 
    8254623a9871: Pull complete 
    938e3e06dac4: Pull complete 
    ea28ebf28884: Pull complete 
    f3cef38785c2: Pull complete 
    894f9792565a: Pull complete 
    1d8a57523420: Pull complete 
    5f09bf1d31c1: Pull complete 
    1b6ff254abe7: Pull complete 
    74310a0bf42d: Pull complete 
    d398726627fd: Pull complete 
    Digest: sha256:da58f943b94721d46e87d5de208dc07302a8b13e638cd1d24285d222376d6d84
    Status: Downloaded newer image for mysql:5.7
    docker.io/library/mysql:5.7
    student@cccr:~$ 
    nasa1515@nasa:~$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    centos              latest              0d120b6ccaa8        6 days ago          215MB
    mysql               5.7                 718a6da099d8        12 days ago         448MB
    hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
    ```
----

* **``docker rmi``** 명령어를 사용해 Host 저장소에 존재하는 이미지를 삭제 할 수 있습니다.  
    명령어의 사용법은 아래와 같습니다.

    ```
    $ docker rmi --help

    Usage:  docker rmi [OPTIONS] IMAGE  [IMAGE...]
    ...
    ```

* **방금 다운로드 받았던 mysql 이미지를 삭제 해보겠습니다.**  

    ```
    nasa1515@nasa:~$ docker rmi mysql:5.7
    Untagged: mysql:5.7
    Untagged: mysql@sha256:da58f943b94721d46e87d5de208dc07302a8b13e638cd1d24285d222376d6d84
    Deleted: sha256:718a6da099d82183c064a964523c0deca80619cb033aadd15854771fe592a480
    Deleted: sha256:058d93ef2bfb943ba6a19d8b679c702be96e34337901da9e1a07ad62b772bf3d
    Deleted: sha256:7bca77783fcf15499a0386127dd7d5c679328a21b6566c8be861ba424ac13e49
    Deleted: sha256:183d05512fa88dfa8c17abb9b6f09a79922d9e9ee001a33ef34d1bc094bf8f9f
    Deleted: sha256:165805124136fdee738ed19021a522bb53de75c2ca9b6ca87076f51c27385fd7
    Deleted: sha256:904abdc2d0bea0edbb1a8171d1a1353fa6de22150a9c5d81358799a5b6c38c8d
    Deleted: sha256:d26f7649f78cf789267fbbca8aeb234932e230109c728632c6b9fbc60ca5591b
    Deleted: sha256:7fcf7796e23ea5b42eb3bbd5bec160ba5f5f47ecb239053762f9cf766c143942
    Deleted: sha256:826130797a5760bcd2bb19a6c6d92b5f4860bbffbfa954f5d3fc627904a76e9d
    Deleted: sha256:53e0181c63e41fb85bce681ec8aadfa323cd00f70509107f7001a1d0614e5adf
    Deleted: sha256:d6854b83e83d7eb48fb0ef778c58a8b839adb932dd036a085d94a7c2db98f890
    Deleted: sha256:d0f104dc0a1f9c744b65b23b3fd4d4d3236b4656e67f776fe13f8ad8423b955c
    nasa1515@nasa:~$ 
    nasa1515@nasa:~$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    centos              latest              0d120b6ccaa8        6 days ago          215MB
    hello-world         latest              bf756fb1ae65        7 months ago        13.3kB
    ```
    MYSQL 이미지와 관련된 ``레이어``는 전부 삭제하며  
    해당 레이어를 다른 컨테이너 또는 이미지가 ``사용하고 있다면`` 삭제하지 않는다.


* **test를 위해 rmitest라는 이름의 hello-world 이미지를 사용한 컨테이너를 하나 생성합니다.**
    
    ```
    nasa1515@nasa:~$ docker run -itd --name rmitest hello-world
    701ad267fbc8aca292b033d424e6a295f06f410ae80807d8b124e15efa021685
    --------------------------------------------------------------------------------------------
    nasa1515@nasa:~$ docker ps -a
    CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                          PORTS               NAMES
    701ad267fbc8        hello-world         "/hello"            About a minute ago   Exited (0) About a minute ago                       rmitest
    --------------------------------------------------------------------------------------------
    nasa1515@nasa:~$ docker rmi hello-world
    Error response from daemon: conflict: unable to remove repository reference "hello-world" (must force) - container 701ad267fbc8 is using its referenced image bf756fb1ae65
    ```
    다음과 같이 사용중인 이미지의 경우 삭제가 되지 않는다.  
    이 경우에는 ``-f 옵션``을 사용하면 삭제 할 수 있지만 실행중인 컨테이너에게 영향을 미치기에 권장하지는 않는다.

----

* **``docker inspect``** 명령어를 사용해 docker 오브젝트의 정보를 자세히 확인 할 수 있습니다.  
    명령어의 사용법은 아래와 같습니다.

    ```
    $ docker inspect --help

    Usage: docker inspect [OPTIONS] NAME|ID [NAME|ID...]
    ...
    ```

* **inspect 명령어로 centos 이미지의 정보를 확인해보자.**

    ```
    nasa1515@nasa:~$ docker inspect centos:latest
    ...
            "Config" : {
                ...
                "CMD" : [
                    "/bin/bash"
                ],
                ...
                "Volumes": null,
                "WorkinDir": "",
                "Entrypoint" : null,
            ...
            "RootFS" : {
                "Type" : "layers",
                "Layers" : [
                
            "sha256:0d120b6ccaa8c5e149176798b3501d4dd1885f961922497cd0abef155c869566"
    ...
    ```
    출력된 부분 중 이후 포스트에서 필요한 부분만 나열하였다.  

    - ``Config`` : 섹션의 cmd는 이미지를 컨테이너로 생성할 때 실행하는 애플리케이션이다.
    - ``Volumes``: 도커 볼륨과 관련된 내용이다. 
    - ``WorkingDir`` : 컨테이너에 접근했을 때의 디렉토리
    - ``Entrypoint`` : cmd와 마찬가지고 실행 할 애플리케이션이다 cmd와 Entry가 함께 있으면  
    Entry는 ``명령``, cmd는 ``인자``처럼 동작한다.
    - ``RootFS`` : 레이어를 나타낸다. 