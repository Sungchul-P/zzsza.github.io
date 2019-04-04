---
layout: post
title: GitPage에 Jekyll 기본 테마 적용하기
desc: GitPage에 Jekyll 기본 테마 적용하기
categories: development
tags: git
comments: true
keywords: "Jekyll,gitpage,git"
---

- GitHub Page는 Jekyll을 이용하여 꾸밀 수 있습니다.
	- Jekyll 기본 테마를 내 페이지에 적용해 보는 글입니다.

---

## 1. 준비물 

윈도우 환경에서 Jekyll을 설치하는 방법은 공식 페이지의 가이드를 참고하자. 
[\<Jekyll Install for Windows\>]([https://link](https://jekyllrb-ko.github.io/docs/windows/))  
 
<br>

## 1.1 루비 설치

[Ruby Download](https://www.ruby-lang.org/en/downloads/)  
 
![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-19-22.png){: width="50%" height="50%"}  

![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-20-10.png){: width="50%" height="50%"}  

![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-20-33.png){: width="50%" height="50%"}  

<br>

* Gem 을 사용하려면 MSYS2 가 필요하다고 한다.
  * `ridk install` 을 통해 설치를 진행한다.
  * 선택 창이 뜨는 데,
  * `3 - MSYS2 and MINGW development toolchain` 을 선택하자!

![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-30-04.png){: width="50%" height="50%"}  


<br>

## 1.2 Jekyll 과 Bundler 설치

<br>

> gem install jekyll bundler  


![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-32-37.png){: width="50%" height="50%"}  

<br>
 
## 1.3 설치 확인

> jekyll -v  



![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-33-08.png){: width="50%" height="50%"}  

<br> 

## 1.4 기본 블로그 테마 설치

> jekyll new jekyll-website  


![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-33-40.png){: width="50%" height="50%"}  
 
 
<br>

## 1.5 GitPage 적용 및 확인


기본 테마로 만들어진 디렉터리의 구성은 다음과 같다.

![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-41-24.png){: width="50%" height="50%"}  

<br>
GitPage에 적용하기 전에 Jekyll 서버로 개발 환경에서 확인할 수 있다.
테마 파일이 있는 곳에서 Jekyll 명령을 실행한다.

> Jekyll serve  



http://127.0.0.1:4000 으로 페이지에 접근할 수 있다.

![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-50-37.png){: width="50%" height="50%"}  

<br>
기본 테마 메인페이지는 아래와 같다.


![](/assets/img/blog/2019-03-14-gitpage-jekyll/2019-03-14-03-51-03.png){: width="50%" height="50%"}  

GitPage를 생성할 때 만들어 놓은 Repository 작업 환경으로 jekyll-website 디렉터리의 내용을 이동시킨 다음, 
Git 으로 Commit & Push 한다.

> git add --all  
> git commit -m "commit"  
> git push -u origin master  


이제 GitPage의 `https://<username>.github.io` 으로 접속해도 위와 같은 테마 화면을 확인할 수 있다.