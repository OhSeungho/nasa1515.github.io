---
layout: post
title: "[DOCKER] - Container 생성과 관리"
author: Lee Wonseok
categories: DOCKER
date: 2020-08-13 10:55
comments: true
tags: [DOCKER]
cover: https://res.cloudinary.com/yangeok/image/upload/v1593160497/logo/posts/iot-protocol.jpg
---



# [DOCKER]  Container 생성과 관리

**머리말**  
 이전 포스트에서는 도커의 설치방법에 대해서 간단하게 포스팅 했었다.  
 이번 포스트에서는 실제 도커의 컨테이너의 생성 관리 방법 및 명령어들을 포스트 했다.


---


**목차**
- [도커 이미지 다운로드](#a1)

---

## 도커 이미지 다운로드 <a name="a1"></a>  

* **docker images** 명령어를 사용해 현재 HOST가 가지고 있는 이미지를 확인 할 수 있다. 

    ```
    student@cccr:~$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED                 SIZE
    hello-world         latest              bf756fb1ae65        7 months ago            13.3kB
    ```