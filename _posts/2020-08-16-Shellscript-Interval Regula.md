---
layout: post
title: "[Shell Script] - 정규표현식"
author: Lee Wonseok
categories: [Shell Script]
date: 2020-08-16 12:36
comments: true
cover: "/assets/create-linux-unix-bash-shell-script.png"
tags: Shell Script
---



#  Shell Script - 정규표현식

**머리말**  
이전 포스트에서는 sed,awk등 텍스트를 편집하는 명령어들에 대해서 알아보았다.  
이번에는 쉘스크립트를 작성할때 가장 기초가 되는 정규표현식에 대해서 포스트했다.  

---

**목차**

- [awk ?](#a1)
- [정규표현식 표현방법](#a2)
- [정규표현식 사용 예제](#a3)


---
## 1. 정규표현식  <a name="a1"></a>  

* **정규 표현식은 ``데이터 검색``, ``복잡한 패턴 매칭``을 도와주는 특별한 문자입니다**  

* **정규표현식(regular expression)은 줄여서 ``'regexp'`` 또는 ``'regex'`` 라고도 합니다.**  

* **정규 표현식은 리눅스 뿐만 아니라 ``유닉스, SQL, R, Python`` 등 에서도 사용할 수 있습니다.**  


    ![스크린샷, 2020-08-19 14-23-56](https://user-images.githubusercontent.com/69498804/90595346-a2229a00-e227-11ea-995d-990cf210fd93.png)

---

## 2. 정규표현식 표현방법  <a name="a2"></a>  

* **정규표현식은 표준인 ``POSIX의 정규표현식``과 POSIX 정규표현식에서 확장된 ``Perl방식의 PCRE``가 대표적이다**  

* **이외에도 수많은 정규표현식이 존재하지만 약간의 차이점이 있을뿐 대부분 비슷합니다.**  

* **정규표현식에서 사용하는 기호를 ``Meta문자``라고 합니다.**


* **Meta문자란 표현식 내부에서 특정한 의미를 갖는 문자를 말하며,  
    공통적인 기본 Meta문자의 종류로는 다음과 같습니다.**

  ![스크린샷, 2020-08-19 14-27-48](https://user-images.githubusercontent.com/69498804/90595597-2bd26780-e228-11ea-829f-01e7003b4d5e.png)

---

* **Meta 문자중에 독특한 성질을 지니고 있는 ``문자클래스'[ ]'``라는 문자가 있습니다.  
문자클래스는 그 내부에 해당하는 문자열의 범위 중 한 문자만 선택한다는 의미이며,  
문자클래스 내부에서는 Meta문자를 사용할 수 없거나 의미가 다르게 사용됩니다.**  

    ![스크린샷, 2020-08-19 14-29-16](https://user-images.githubusercontent.com/69498804/90595694-5e7c6000-e228-11ea-8a14-ff254a3eb351.png)

---

* **``POSIX에서만 사용``하는 문자클래스가 있는데 단축키처럼 편리하게 사용할 수 있습니다.  
대표적인 POSIX 문자클래스는 다음과 같으며 ``대괄호'[ ]'`` 가 붙어있는 모양 자체가 표현식이므로  
실제로 문자클래스로 사용할 때에는 대괄호를 씌워서 사용해야만 정상적인 결과를 얻을 수 있습니다.**


    ![스크린샷, 2020-08-19 14-30-58](https://user-images.githubusercontent.com/69498804/90595815-9f747480-e228-11ea-81bd-157acfc5010d.png)

---

* **``Flag``의 종류**  
    **자주 사용하는 Flag는 밑의 3종류가 있으며 Flag를 사용을 하지 않을 수도 있습니다.  
    만약 Flag를 설정 하지 않을 경우에는 문자열 내에서 검색대상이 많더라도 한번만 찾고 끝나게 됩니다.**

    ![스크린샷, 2020-08-19 14-32-47](https://user-images.githubusercontent.com/69498804/90595939-dd719880-e228-11ea-984d-1da56b69cb66.png)

---

## 3. 정규표현식 사용 예제  <a name="a3"></a>    

* **우선 실습을 위해서 아래와 같은 텍스트 파일을 만들었습니다.**




    ```
    $ cat nasa1515.txt 
    drum
    photography
    data science
    greenplum
    python
    R
    book
    movie
    dancing
    singing
    milk
    english
    gangnam style
    new face
    soccer
    pingpong
    sleeping
    martial art
    jogging
    blogging
    apple
    grape
    banana
    tomato
    bibimbab
    kimchi
    @email
    123_abc_d4e5
    xyz123_abc_d4e5
    123_abc_d4e5.xyz
    xyz123_abc_d4e5.xyz
    ```
---

### 먼저 기본 정규 표현식을 실습해보겠습니다. 

**정규표현식은 ``큰 따옴표(" ")``안에 매칭할 문자와 함께 사용합니다.**   







* **1. 문자열의 처음 시작 부분 매칭: ``^``**  
    **``-n`` 은 행번호를 출력하라는 뜻입니다.**  

    ```
    student@cccr:/home/won/script$ grep -n "^m" nasa1515   # m으로 시작하는 텍스트
    8:movie
    11:milk
    18:martial art 
    ```


* **2. 문자열의 끝 부분 매칭: ``$``**  

    ```
    student@cccr:/home/won/script$ grep -n "m$" nasa1515 
    1:drum
    4:greenplum
    ```
 
* **3. 점의 개수만큼 아무 문자나 대체: ``...``**  

    ```
    # m 문자 뒤에 아무 문자 3개 이상이 존재하는 텍스트 검색

    student@cccr:/home/won/script$ grep -n "m..." nasa1515 
    8:movie
    11:milk
    13:gangnam style
    18:martial art
    24:tomato
    25:bibimbab
    26:kimchi
    27:@email
    ```


    ```
    # m 문자 뒤에 아무 문자 5개 이상이 존재하는 텍스트 검색
    student@cccr:/home/won/script$ grep -n "m....." nasa1515 
    13:gangnam style
    18:martial art
    ```


    ```
    # m 문자 앞에 2개 이상 + 뒤에 3개 이상이 존재하는 텍스트 검색
    student@cccr:/home/won/script$ grep -n "..m..." nasa1515 
    13:gangnam style
    24:tomato
    25:bibimbab
    26:kimchi
    27:@email
    ```

```
student@cccr:/home/won/script$ grep -n "....m" nasa1515 
4:greenplum
13:gangnam style
25:bibimbab
```
 ㅁㄴㅇㅁ

