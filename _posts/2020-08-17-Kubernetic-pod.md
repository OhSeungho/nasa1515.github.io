---
layout: post
title: "[Kubernetes] - 쿠버네티스의 개념, PODS"
author: Lee Wonseok
categories: Kubernetes
date: 2020-08-17 09:36
comments: true
cover: "/assets/kubernets.jpg"
tags: Kubernetes
---



#  KUBERNETES - 쿠버네티스의 개념 정리, PODS    

**머리말**  
이전 포스트에서 드디어 GCP 인스턴스 기반의 k8s 클러스터 환경을 구축했다.  
이번 포스트에서는 이번에 간단하게 포스트해서 정리했지만  
실제 실습을 들어가기전 전체적인 개념에 대해서 다시 한번 정리하고  
쿠버네티스의 PODS에 대해서 포스트한다.


---

**참고 : 조대협님 블로그 :** https://bcho.tistory.com/1256
 
---

**목차**

- [개념 정리](#a1)
- [구성 (Install)](#a2)



---

## 개념정리   <a name="a1"></a>


* ### 오브젝트
쿠버네티스를 이해하기 위해서 가장 중요한 부분이 오브젝트이다. 가장 기본적인 구성단위가 되는 기본 오브젝트(Basic object)와, 이 기본 오브젝트(Basic object) 를 생성하고 관리하는 추가적인 기능을 가진 컨트롤러(Controller) 로 이루어진다. 그리고 이러한 오브젝트의 스펙(설정)이외에 추가정보인 메타 정보들로 구성이 된다고 보면 된다. 

오브젝트 스펙 (Object Spec)
오브젝트들은 모두 오브젝트의 특성 (설정정보)을 기술한 오브젝트 스펙 (Object Spec)으로 정의가 되고, 커맨드 라인을 통해서 오브젝트 생성시 인자로 전달하여 정의를 하거나 또는 yaml이나 json 파일로 스펙을 정의할 수 있다. 

기본 오브젝트 (Basic Object)
쿠버네티스에 의해서 배포 및 관리되는 가장 기본적인 오브젝트는 컨테이너화되어 배포되는 애플리케이션의 워크로드를 기술하는 오브젝트로 Pod,Service,Volume,Namespace 4가지가 있다. 



간단하게 설명 하자면 Pod는 컨테이너화된 애플리케이션, Volume은 디스크, Service는 로드밸런서 그리고 Namespace는 패키지명 정도로 생각하면 된다. 그러면 각각을 자세하게 살펴보도록 하자.



출처: https://bcho.tistory.com/1256 [조대협의 블로그]
파드의 컨테이너는 이미지로부터 파일시스템을 제공받는다.  
그러나 파드가 종료되면 파드 내의 ``데이터(파일)``은 더 이상 사용 할 수 없게 된다.  
