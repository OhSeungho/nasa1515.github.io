---
layout: post
title: "[Kubernetes] 쿠버네티스 잡(job) 개념"
author: Lee Wonseok
categories: Kubernetes
date: 2020-08-08 09:36
comments: true
tags: [Kubernetes]
---




**목차**
- [JOB?](#JOB?)
- [[DOX]-TEST](#[DOX]-TEST)

#  JOB?

* JOB은 하나 이상의 파드를 지정하고 지정된 수의 파드를 성공적으로 실행하도록 하는   설정 입니다.  
    노드의 H/W 장애나 재부팅 등으로 인해 파드가 정상 실행이 되지 않았을 경우  
    job은 새로운 파드를 시작하도록 할 수 있습니다.  

    즉, 백업이나 특정 배치 파일들처럼 한번 실행하고 종료되는 성격의 작업에 사용될 수 있습니다.  



 ## [DOX] TEST