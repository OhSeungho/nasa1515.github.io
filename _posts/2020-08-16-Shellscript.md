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
- [Sed 동작](#a2)
- [Sed 사용법](#a3)


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

---
## 2. Sed 동작  <a name="a2"></a>  

* **``패턴 스페이스(Pattern space)``와 ``홀드 스페이스(hold space)``**  

    sed 명령어는 동작시 내부적으로 두개의 워크스페이스를 사용하는데,  
    (마치 우리 복붙할 때 임시 저장소 클립보드처럼~!)  
    이 두 버퍼를 ``패턴 스페이스(=패턴 버퍼)``와 ``홀드 스페이스(=홀드 버퍼)``라고 합니다.

    ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbnZHd6%2FbtqEsDE9VUa%2FpmR81oKgh75TFjJuMNsGQ1%2Fimg.png)

* **``패턴 버퍼``: sed가 파일을 라인단위로 읽을 때 그 읽힌 라인이 저장되는 임시 공간**  
    우리가 sed명령어로 출력하라는 명령을 주면 여기 있는 버퍼 내용을 출력하는거고,  
    뭔가 조작을 하면 여기 저장되어 있는 내용을 조작하는 겁니다.  
    **``원본을 건드는게 아닙니다.``** 
    즉 이 버퍼는 현재 내가 담고 있는 정보를 갖고 있겠죠.  
    텍스트 1라인에서 2라인으로 넘어가 글을 읽게 되면  
    여기 패턴 버퍼에는 2라인 현재 내용이 저장되겠죠. 

 

* **``홀드 버퍼`` : 홀드 스페이스는 패턴 버퍼처럼 짧은 순간 임시 버퍼가 아니라  
좀 더 오랜 기간 가지고 있는 저장소입니다.**  
2라인 작업중이더라도 1라인을 기억하고 있을 수 있는 거예요.

    즉 어떤 내용을 홀드 스페이스에 저장하면,  
    sed가 다음 행을 읽더라도 나중에 내가 원할 때 불러와서 재사용할 수 있는 버퍼가 홀드 버퍼가 됩니다. 
---

## 3. Sed 사용법  <a name="a3"></a>  

* **사용법 설명을 위해서 특정 정보가 들어가 있는 파일을 준비 했습니다**

    ```
    student@cccr:~/다운로드$ cat employees 
    EMPLOYEE_ID	FIRST_NAME	LAST_NAME	EMAIL	PHONE_NUMBER	HIRE_DATE	JOB_ID	SALARY	COMMISSION_PCT	MANAGER_ID	DEPARTMENT_ID
    100	Steven	King	SKING	515.123.4567	03/06/17	AD_PRES	24000			90
    101	Neena	Kochhar	NKOCHHAR	515.123.4568	05/09/21	AD_VP	17000		100	90
    102	Lex	De Haan	LDEHAAN	515.123.4569	01/01/13	AD_VP	17000		100	90
    103	Alexander	Hunold	AHUNOLD	590.423.4567	06/01/03	IT_PROG	9000		102	60
    104	Bruce	Ernst	BERNST	590.423.4568	07/05/21	IT_PROG	6000		103	60
    105	David	Austin	DAUSTIN	590.423.4569	05/06/25	IT_PROG	4800		103	60
    106	Valli	Pataballa	VPATABAL	590.423.4560	06/02/05	IT_PROG	4800		103	60
    107	Diana	Lorentz	DLORENTZ	590.423.5567	07/02/07	IT_PROG	4200		103	60
    108	Nancy	Greenberg	NGREENBE	515.124.4569	02/08/17	FI_MGR	12008		101	100
    109	Daniel	Faviet	DFAVIET	515.124.4169	02/08/16	FI_ACCOUNT	9000		108	100
    ```
---

* **sed 명령어 자주쓰는 대표적 사용법**  

    편집기 기능을 모두 수행할 수 있다보니 sed의 ``subcommand``  
    즉 sed와 같이 쓰이는 명령어 조합이 굉장히 많다.

    자세한 옵션들은 하단에 쭉 다루도록하고 대표적으로 자주 쓰이는 명령어 조합부터 일단 살펴보겠다.  

---
* **``특정 범위만큼 파일내용 출력하기``** 

*  **1. ``sed -n '1p' employees;``**  
    **employees파일에서 첫 번째 행만 출력해서 화면에 보여준다.**

    ```
    student@cccr:/home/won/script$ sed -n '1p' employees 
    EMPLOYEE_ID	FIRST_NAME	LAST_NAME	EMAIL	PHONE_NUMBER	HIRE_DATE	JOB_ID	SALARY	COMMISSION_PCT	MANAGER_ID	DEPARTMENT_ID
    ```

* **2. ``sed -n '1,3p' employees;``**  
    **employees파일에서 1~3라인 범위의 내용을 출력해서 보여준다.**

    ```
    student@cccr:/home/won/script$ sed -n '1,3p' employees 
    EMPLOYEE_ID	FIRST_NAME	LAST_NAME	EMAIL	PHONE_NUMBER	HIRE_DATE	JOB_ID	SALARY	COMMISSION_PCT	MANAGER_ID	DEPARTMENT_ID
    100	Steven	King	SKING	515.123.4567	03/06/17	AD_PRES	24000			90
    101	Neena	Kochhar	NKOCHHAR	515.123.4568	05/09/21	AD_VP	17000		100	90
    ```


* **3. ``sed -n '8,$p' employees;``**   
    **employees파일에서 8라인부터 파일끝까지 출력해서 보여준다. ``$는 '끝'을 의미``합니다.** 

    ```
    student@cccr:/home/won/script$ sed -n '8,$p' employees 
    106	Valli	Pataballa	VPATABAL	590.423.4560	06/02/05	IT_PROG	4800		103	60
    107	Diana	Lorentz	DLORENTZ	590.423.5567	07/02/07	IT_PROG	4200		103	60
    108	Nancy	Greenberg	NGREENBE	515.124.4569	02/08/17	FI_MGR	12008		101	100
    109	Daniel	Faviet	DFAVIET	515.124.4169	02/08/16	FI_ACCOUNT	9000		108	100
    ```
    **3번 명령같은 경우 첫번째 헤더가 나오지 않아 어떤 데이터인지 구분하기가 힘드네요**


* **4. ``sed -n -e '1p' -e '8,$p' employees``**  
    **여러개의 편집 명령을 실행할 때 ``-e 옵션``을 씁니다.  
    다음에 오는 것도 편집 명령어라는걸 알려줍니다.**

    **첫 번째 행과 8~끝 행 두 부분을 출력해줍니다.**
    
    ```
    student@cccr:/home/won/script$ sed -n -e '1p' -e '8,$p' employees 
    EMPLOYEE_ID	FIRST_NAME	LAST_NAME	EMAIL	PHONE_NUMBER	HIRE_DATE	JOB_ID	SALARY	COMMISSION_PCT	MANAGER_ID	DEPARTMENT_ID
    106	Valli	Pataballa	VPATABAL	590.423.4560	06/02/05	IT_PROG	4800		103	60
    107	Diana	Lorentz	DLORENTZ	590.423.5567	07/02/07	IT_PROG	4200		103	60
    108	Nancy	Greenberg	NGREENBE	515.124.4569	02/08/17	FI_MGR	12008		101	100
    109	Daniel	Faviet	DFAVIET	515.124.4169	02/08/16	FI_ACCOUNT	9000		108	100
    ```

    **위 실습한 명령에서의 ``p는 print의 약자로 출력``을 의미합니다.**  
    **컴마 ``(`)``는 주소 범위를 지정해요.**

    * **sed는 원본을 건드리지 않습니다.  
    그런고로 sed로 작업한 부분만 억제해서 출력시키고 싶다면 ``-n옵션``을 써줘야해요.**  
    **그래서 보통 ``-n옵션``은 ``p``와 항상 짝짝꿍으로 사용됩니다.**
---

 * **``특정 단어로 시작하는 행들만 추출하기``**  
    **보통 로그파일의 경우 데이터들의 구별하기 위해 ``unique``한 값이 맨앞에 붙는 경우가 많습니다.**

    **예를들어 ``log20200816`` 이라는 파일이 있는데 이 날짜에 처리된 거래들이 기록된다고 합시다.**  
    **당연히 거래를 구분하는 유니크값으로 상품코드, 거래코드등이 붙을 겁니다ㅊ.**


* **1. ``sed -n '/^107/p' employees``**  
    **employees파일에서 107로 ``시작``하는 행만 출력해서 화면에 보여준다.**  
    **여기서 '^'는 메타문자로 '시작'을 의미합니다.**

    ```
    student@cccr:/home/won/script$ sed -n '/^107/p' employees 
    107	Diana	Lorentz	DLORENTZ	590.423.5567	07/02/07	IT_PROG	4200		103	60
    ```

* **2. ``sed -n '/103/p' employees``**  
    **employees파일에서 103을 ``포함``하고 있는 행들을 출력해서 보여준다.**

    ```
    student@cccr:/home/won/script$ sed -n '/103/p' employees 
    103	Alexander	Hunold	AHUNOLD	590.423.4567	06/01/03	IT_PROG	9000		102	60
    104	Bruce	Ernst	BERNST	590.423.4568	07/05/21	IT_PROG	6000		103	60
    105	David	Austin	DAUSTIN	590.423.4569	05/06/25	IT_PROG	4800		103	60
    106	Valli	Pataballa	VPATABAL	590.423.4560	06/02/05	IT_PROG	4800		103	60
    107	Diana	Lorentz	DLORENTZ	590.423.5567	07/02/07	IT_PROG	4200		103	60
    ```

---

* **``파일에서 공백으로 이루어지거나 빈줄 제거하기``**  

* **1. ``sed '/^$/d' employees``**  
    **employees파일에서 빈 라인들을 지운 후 내용을 출력해준다.**  

    ```
    student@cccr:/home/won/script$ sed '/^&/d' employees 
    EMPLOYEE_ID	FIRST_NAME	LAST_NAME	EMAIL	PHONE_NUMBER	HIRE_DATE	JOB_ID	SALARY	COMMISSION_PCT	MANAGER_ID	DEPARTMENT_ID
    100	Steven	King	SKING	515.123.4567	03/06/17	AD_PRES	24000			90
    101	Neena	Kochhar	NKOCHHAR	515.123.4568	05/09/21	AD_VP	17000		100	90
    102	Lex	De Haan	LDEHAAN	515.123.4569	01/01/13	AD_VP	17000		100	90
    103	Alexander	Hunold	AHUNOLD	590.423.4567	06/01/03	IT_PROG	9000		102	60
    104	Bruce	Ernst	BERNST	590.423.4568	07/05/21	IT_PROG	6000		103	60
    105	David	Austin	DAUSTIN	590.423.4569	05/06/25	IT_PROG	4800		103	60
    106	Valli	Pataballa	VPATABAL	590.423.4560	06/02/05	IT_PROG	4800		103	60
    107	Diana	Lorentz	DLORENTZ	590.423.5567	07/02/07	IT_PROG	4200		103	60
    108	Nancy	Greenberg	NGREENBE	515.124.4569	02/08/17	FI_MGR	12008		101	100
    109	Daniel	Faviet	DFAVIET	515.124.4169	02/08/16	FI_ACCOUNT	9000		108	100
    ```
    **추가적으로 빈 라인들을 삭제한 후 결과를 파일로 저장 하고 싶다면 아래 형식을 사용한다.**

    
    ```
    sed '/^$/d' employees > new_employees   
    # " > "(다이렉션)은 덮어쓰기 기능을 한다.
    # " >> " (리다이렉션)의 경우 기존 파일이 존재하면 덧붙이게 된다.
    ```

* **3. ``sed '/^ *$/d' employees > new_employees``**  
    **빈 라인들이나 공백으로 채워진 행들을 삭제한 후 파일로 저장한다.**  

    **보이는 ``*``는 메타문자로 앞의 문자를 0개 이상 찾습니다.  
    행의 시작이 0개 이상의 공백으로 이뤄지다 끝을 맺으니  
    공백이거나 빈 줄을 찾아낸다는 의미가 됩니다.**

    위의 예제에서의 ``'d'`` 서브명령어는 ``delete``의 약자로 ``삭제``를 의미합니다.  
    ``'/'``사이의 단어르 포함한 모든 줄을 삭제시키는 의미입니다.  
    ``^`` 는 행의 처음을 의미하고 ``$``는 행의 끝을 의미하니까  
    행의 처음과 끝이 같이 만나있는 것인 빈 줄을 의미합니다.  


---

* **``단어 치환``**  



* **1. ``sed 's/IT_PROG/DEVELOPER/g' employees``**  
    **``IT_PROG``라고 되어있는 단어를 ``DEVELOPER``로 변경**  

    ```
    student@cccr:/home/won/script$ sed 's/IT_PROG/DEVELOPER/g' employees 
    EMPLOYEE_ID	FIRST_NAME	LAST_NAME	EMAIL	PHONE_NUMBER	HIRE_DATE	JOB_ID	SALARY	COMMISSION_PCT	MANAGER_ID	DEPARTMENT_ID
    100	Steven	King	SKING	515.123.4567	03/06/17	AD_PRES	24000			90
    101	Neena	Kochhar	NKOCHHAR	515.123.4568	05/09/21	AD_VP	17000		100	90
    102	Lex	De Haan	LDEHAAN	515.123.4569	01/01/13	AD_VP	17000		100	90
    103	Alexander	Hunold	AHUNOLD	590.423.4567	06/01/03	DEVELOPER	9000		102	60
    104	Bruce	Ernst	BERNST	590.423.4568	07/05/21	DEVELOPER	6000		103	60
    105	David	Austin	DAUSTIN	590.423.4569	05/06/25	DEVELOPER	4800		103	60
    106	Valli	Pataballa	VPATABAL	590.423.4560	06/02/05	DEVELOPER	4800103	60
    107	Diana	Lorentz	DLORENTZ	590.423.5567	07/02/07	DEVELOPER	4200		103	60
    108	Nancy	Greenberg	NGREENBE	515.124.4569	02/08/17	FI_MGR	12008		101	100
    109	Daniel	Faviet	DFAVIET	515.124.4169	02/08/16	FI_ACCOUNT	9000		108	100
    ```

* **2. ``sed 's/it_prog/DEVELOPER/gi' employees``**  
    **기능은 동일하지만 ``i``옵션을 사용하면 대소문자를 구분하지 않습니다.**


    **예제에서 ``s``와 같이 쓰이는 ``g`` 플래그는 치환이 행에서 ``전체``를 대상으로 이루어짐을 의미합니다.**  

---

## 4. SED subcommand 명령어 종류와 의미  <a name="a4"></a>  


|**subcommand**|**의미**|
|:---|---|
|``a``| 현재 행에 하나 이상의 새로운 행을 추가한다.|
|``c``| 현재 행의 내용을 새로운 내용으로 교체한다.|
|``d``| 행을 삭제한다. |
|``i``| 현재 행의 위에 텍스트를 삽입한다. |
|``h``|	패턴 스페이스의 내용을 홀드 스페이스에 복사한다. |
|``H``|	패턴 스페이스의 내용을 홀드 스페이스에 추가한다. |
|``g``|	홀드 스페이스의 내용을 패턴 스페이스에 복사한다. (패턴 스페이스가 비어있지않은 경우에는 덮어쓰기~)|
|``G``|	홀드 스페이스의 내용을 패턴 스페이스에 복사한다. (패턴 스페이스가 비어있지 않은 경우에는 그 뒤에 추가) |
|``l``|	출력되지 않는 특수문자를 명확하게 출력한다.|
|``p``|	행을 출력한다. |
|``n``|	다음 입력 행을 첫 번째 명령어가 아닌 다음 명령어에서 처리하게 한다. |
|``q``|	sed를 종료한다.|
|``r``|	파일로부터 행을 읽어온다.|
|``!``|	선택된 행을 제외한 나머지 전체 행에 명령어를 적용한다.|
|``s``|	문자열을 치환한다.|



* **sed s와 같이 쓰는 치환플래그**   

|**s와 쓰이는 플래그**|	**의미**|
|:---|---|
|``g``	|치환이 행 전체에 대해 이뤄진다.|
|``p``	|행을 출력한다.|
|``w``	|파일에 쓴다. |
|``x``	|홀드 버퍼와 패턴 스페이스의 내용을 서로 맞바꾼다. |
|``y``	|한 문자를 다른 문자로 변환한다. (y에 정규표현식 메타문자를 사용할 수 없다)|
---