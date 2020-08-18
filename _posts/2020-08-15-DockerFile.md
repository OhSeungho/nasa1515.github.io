---
layout: post
title: "[DOCKER] - Dockerfile"
author: Lee Wonseok
categories: DOCKER
date: 2020-08-15 10:36
comments: true
tags: [DOCKER]
cover: https://res.cloudinary.com/yangeok/image/upload/v1593160497/logo/posts/iot-protocol.jpg
---


# [DOCKER]  - Dockerfile

**머리말**  
 이번 포스트에서는 Docker에서 조금도 간편화된 방법으로 이미지를 제작할 수 있는  
 Dockerfile에 대해서 포스팅한다.


---

**목차**
- [DOCKERFILE](#a1)
- [MACVLAN 설정](#a2)



---

## 1. DOCKERFILE <a name="a1"></a>  

**``Dockerfile``은 컨테이너를 만들고 해야하는 일련의 작업들을 미리 선언함으로써 매번 해당 작업을 하지않고도,  
컨테이너 생성시 자동으로 등록된 작업이 실행된 후 컨테이너를 생성할 수 있는 파일이다.**

**Dockerfile은 어플리케이션 개발 외에도 도커 허브에 배포할때,이미지가 아닌, Dockerfile을 이용하여 배포할 수도 있습니다.**

