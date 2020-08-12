---
layout: post
title: "test"
author: Lee Wonseok
categories: CCCR
comments: true
---

블로그를 만들어보겠다면서 jekyll 이란 것을 들어보게 되었다.

나는 jekyll 이란 것이 아직도 정확히 잘 알지는 못하지만 이를 이용해 블로그를 만드는 방법을 적어보고자 한다.

나와 다른 jekyll 테마를 사용한다면 조금 다를 수 있겠지만 기본적인 방법은 비슷할 것이다.

참고로 OS는 Windows10이며, jekyll 테마는 [centrarium](<http://jekyllthemes.org/themes/centrarium/>)이다.



## 1. 루비(ruby) 설치

[루비 다운로드](<https://rubyinstaller.org/downloads/>)사이트에서 루비를 다운받아 설치한다.

![루비 다운로드](/assets/img/ruby_download.JPG)

본인은 위의 사진에서 `Ruby+Devkit 2.5.5-1 (x64)` 를 다운받았다.

다운로드 후 설치는 크게 신경쓸 것 없이 진행하면 된다.



## 2. 지킬(jekyll) 설치

루비를 설치한 후 지킬을 설치한다.

윈도우 검색창에 ruby를 입력한 후 검색되는 Start Command Prompt with Ruby를 실행한다.

![루비 커맨드](/assets/img/ruby_command.jpg)

이 커멘드 콘솔창에서 `gem` 패키지를 이용하여 지킬을 설치한다.

```
gem install jekyll
gem install minima
gem install bundler
gem install jekyll-feed
gem install tzinfo-data
```

지킬 설치가 완료되면 로컬에서 블로그를 구성할 수 있게 된다.

참고로 위의 `gem` 패키지를 이용한 지킬 설치는 `git bash`로도 가능하다.



## 3. 블로그 생성

블로그를 관리할 폴더를 생성하고 해당 폴더로 이동한다.

나는 `testblog`라고 지었다.

이 후 지킬을 실행시킨다.

```
jekyll serve

# bundle exec jekyll serve
# 를 입력해도 된다.
```

인코딩 문제가 발생하면

```
chcp 65001
```

을 입력하여 실행한다.

![jekyll_serve](/assets/img/jekyll_serve.JPG)

이제 브라우저를 열어 URL에 `127.0.0.1:4000`을 입력하면 로컬 블로그가 생성된 것을 확인할 수 있다.

![jekyll_browser](/assets/img/jekyll_browser.JPG)



## 4. 지킬 테마 적용

이제 지킬도 설치하고 로컬 블로그도 생성해봤으니 지킬 테마를 이용하여 블로그를 꾸며보자.

테마의 장점은 완성된 틀을 사용하여 보기 좋은 블로그를 만들 수 있다는 점이다.

위에서도 언급했듯이 나는  [centrarium](<http://jekyllthemes.org/themes/centrarium/>) 테마를 적용했고 아주아주 약간의 커스터마이징을 했을 뿐이다.

다른 지킬 테마를 적용하고 싶다면 [지킬 테마 사이트](<http://jekyllthemes.org/>)를 찾아보자.

위의 사이트를 통해 테마를 선택하여 해당 테마의 홈페이지로 이동하면 깃허브로 이동할 것이다.

이 테마를 압축파일로 다운로드 받는다.

![centrarium_github](/assets/img/centrarium_github.jpg)

다운로드 받은 압축파일을 풀면 밑의 사진과 같이 폴더가 구성될 것이다.

![centrarium_dir](/assets/img/centrarium_dir.JPG)

이제 코드 에디터를 통해 `_config.yml`을 열어 코드를 수정한다.

```yml
# Site settings
title: <블로그 이름> # Hoik's blog
subtitle: "<블로그 subtitle>" # "단순히 농구를 좋아하는 사람의 나를 위한 블로그"
email: <사용 이메일>
name: <이름> # Hoik Jang
description: >
	<블로그 설명> # Hoik's developer blog
baseurl: ""
url: "<깃허브 블로그 주소>" # "http://hoik92.github.io"
```

이정도만 수정하더라도 기본적으로 자신의 블로그가 완성된다.

하지만 위처럼 `_config.yml` 파일을 수정한 후에 `bundle exec jekyll serve` 명령어를 통해 지킬을 실행시켜도 에러가 발생할 것이다. 이는 해당 테마에서 사용하는 jekyll plugin이 설치되지 않았거나 버전이 일치하지 않기 때문이다.

![jekyll_error](/assets/img/jekyll_error.JPG)

이를 간단히 해결하기 위해서 일단 `Gemfile.lock` 파일을 삭제한다.

이후 커맨드 콘솔창에 아래의 명령어를 입력한다.

```
bundle install
```

그러면 현재 자신이 설치한 지킬 및 플러그인들의 버전에 맞게, 그리고 설치되지 않은 플러그인은 자동으로 설치되어 `Gemfile.lock` 파일에 기록되고 생성된다.

이후 콘솔창에

```
jekyll serve
```

명령어로 지킬을 실행한 후 브라우저를 통해 테마가 적용된 모습을 확인할 수 있다.

![centrarium](/assets/img/centrarium.JPG)



## 5. 깃허브와 연동

자 이제 로컬 블로그를 만들었다.

이 글의 제목에서 볼 수 있듯이 목적은 로컬 블로그가 아니다. 따라서 이제 깃허브와 연동하여 온라인에서 이 블로그를 볼 수 있도록 만들자.

우선 자신의 깃허브에 새로운 repogitory를 생성한다. repogitory명은 `<자신의 github username>.github.io`이다.

이는 위에서 `_config.yml` 파일을 수정할 때 `url` 설정과 같은 주소이다.

![new_repo](/assets/img/new_repo.JPG)

이제 이 repogitory에 위에서 적용한 지킬 테마를 그대로 올리면 끝이다.

해당 테마의 로컬 폴더에서

```
git init
git add .
git commit -m "깃허브와 연동!!!"
git remote add origin <방금 생성한 깃허브 저장소의 URL>
git push -u origin master
```

이 github page는 길게는 1~2분 후에 적용이 되므로 조금 기다린 후에 `http://<자신의 github username>.github.io` URL로 접속하여 확인해보자.

