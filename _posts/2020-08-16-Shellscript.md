---
layout: post
title: "[Shell Script] - 리눅스 명령어 sed"
author: Lee Wonseok
categories: Shell Script
date: 2020-08-15 09:36
comments: true
cover: "/assets/create-linux-unix-bash-shell-script.png"
tags: Shell Script
---



#  Shell Script - SED 명령어

**머리말**  
이미 2년정도 실무를 경험하면서 스크립트는 지겹도록 작성해서 이미 익숙한 명려어 이지만  
이 명령어도 쓰는 옵션만 쓰고 쓰지 않았던 옵션에 대해서는 상세하게 알지 못했다.  
이번 포스트를 쓰면서 머리속에 지식을 박아넣자.  


   
 
---

**목차**

- [Sed (streamlined editor) ?](#a1)
- [PV, PVC](#a2)



---
## 1. Sed(streamlined editor) ?  <a name="a1"></a>  

**``sed``는 대화형 기능이 없는 편집기이다.**  

* 명령행에서 직접 편집 명령어와 파일을 지정하여 작업한 후 결과를 화면으로 확인한다.  
* sed 편집기는 원본을 손상하지 않는다.  
* 리다이렉션을 이용하여 편집 결과를 파일로 저장하여 확인할 수 있다.  
* sed 명령어는 ``streamlined editor``를 의미합니다.  
streamlined = '능률적인'을 의미하듯 정말 편한 명령어입니다.


**``sed`` 명령어의 형식은 다음과 같다**  

  ```
    $ sed [옵션] 스크립트 입력파일1 [입력파일2 ... ]
   ```

