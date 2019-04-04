---
layout: post
title: Jekyll 테마 변경하기
desc: Jekyll 테마 변경하기
categories: development
tags: git
comments: true
keywords: "Jekyll,Gitpage,jekyllthemes"
---

- Jekyll 특정 테마로 적용하는 글 입니다.
	- 테마별로 구조는 다양합니다.
	- JalPc 테마를 기준으로 커스텀하는 방법을 알아봅니다.

---


## 1. 적용할 테마 찾기

  
[jekyllthemes](http://jekyllthemes.org/) 에서 맘에 드는 테마를 찾자.  
  
본 포스팅에서 진행할 테마는 [JalPc](https://github.com/jarrekk/Jalpc) 라는 테마다.  


포트폴리오처럼 꾸밀 수 있어서 선택하게 됐고, 이 블로그에 사용된 테마이기도 하다. 

![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-13-54-14.png){: .wh70 .center}   

<br/>
## 2. 테마 다운로드 및 설치

[JalPc](https://github.com/jarrekk/Jalpc) 레포지터리 에서 테마를 다운로드 받자.  
  
ZIP 파일을 직접 다운로드 받아도 되고, git clone을 사용해서 받아도 된다.  
   
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-13-55-57.png){: .wh70 .center}   
  

압축을 해제(또는 git clone으로 복제)하면 다음과 같은 파일들이 확인된다.  

 
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-13-56-14.png){: .wh70 .center}   
 


위 파일들을 테마를 적용할 GitPage Repository 작업공간에 복사한다.

![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-13-57-01.png){: .wh70 .center}   


<br>
## 3. 테마 기본 설정 변경하기

 

이 테마의 경우, URL 기본 경로 설정을 변경해줘야 한다.

(테마 제작자의 Git 기준으로 경로가 되어 있기 때문이다.)

 

* `_config.yml` :<span style="mso-spacerun:yes">  ‘Website settings’, ‘author’, ‘comment’</span>, ‘analytics’ 항목을 수정한다.  
  
	* 꼭 바꿔줘야 하는 항목은 **Website settings** 다. (URL 관련)  
  
	* 자세한 내용은 주석을 참고하자.  
  
	- **baseurl** : /  
  
	- **url** : https://deuter431.github.io/  

 
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-13-58-03.png){: .wh70 .center}    
 

* `_data/landing.yml` : 메인 페이지의 섹션을 관리하는 파일이다.
  
* `_data/index/` : 메인 페이지의 각 섹션에 대한 파일이 있는 경로다.  
  
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-13-59-43.png){: .wh30 .center}  

   
* `_data/blog.yml` : 블로그 페이지의 카테고리를 관리한다.  
    
* `CNAME` : 개인 도메인을 사용하려면 내용을 편집하고, gh-pages branch를 생성한다.  
    
	* github.io 페이지를 쓸 경우 비워 놓으면 된다.  
  <br/>
* `README.md` : 테마에는 상관 없지만, GitHub 메인 내용이므로 적당히 변경하자.  
    
* 기타 다른 페이지 설정 관련  
  
 ![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-00-25.png){: .wh70 .center}   


<br/>

## 4. 페이지 확인

<br/>  

- Git에 변경내용을 적용하기 전에 Jekyll 서버로 개발 환경에서 먼저 확인한다.  
  
> jekyll serve

  
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-00-58.png){: .wh70 .center}   

  
<a href="http://127.0.0.1:4000">http://127.0.0.1:4000</a>로 페이지를 확인할 수 있다.

 
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-05-57.png){: .wh70 .center}   
 

<br>
## 5. Git Push 하기


Git 사용에 대한 자세한 내용은 [GitHub Pages로 무료 호스팅하기](/tips/2019/03/13/github-pages.html) 를 참고하자.
  
  
* Staging & Commit
  
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-06-26.png){: .wh50 .center}   
  

* Push
    
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-06-39.png){: .wh30 .center}   
 

<br>
## 6. post 작성 및 Jekyll-admin 설치

```
If you want to add a post at this theme, you need to create a yyyy-mm-dd-*.md file at _postsdirectory. yyyy-mm-dd-*.md file has a metadata like this:
---
layout: post
title:  "3 Steps (2 minutes) to Setup Your Personal Website with Jalpc"
date:   2017-01-31
desc: "3 Steps (2 minutes) to Setup Your Personal Website with Jalpc"
keywords: "Jalpc,Jekyll,gh-pages,website,blog,easy"
categories: [HTML]
tags: [Jalpc,Jekyll]
icon: icon-html
---
Each line means:
	• layout: post file use which template
	• title: post's title
	• date: the date when you create this post
	• desc: it will be written at meta tag at post html page, it's useful for SEO
	• keywords: it will be written at meta tag at post html page, it's useful for SEO
	• categories: the post belongs to what categories, it's a not none array
	• tags: the post's tags, it's a not none array
	• icon: the icon shown at /blog/ at each post, please reference http://fontawesome.io/icons/and http://fizzed.com/oss/font-mfizz, it should be fa-xxx or icon-xxx

출처: [https://github.com/jarrekk/Jalpc/wiki/How-to-add-posts](https://github.com/jarrekk/Jalpc/wiki/How-to-add-posts)

```


* Post 파일양식 맞춰주기가 번거롭다..  
   
	* `Jekyll-admin` 플러그인을 사용해서 작성해보자 !  
  
* Jekyll 프로젝트(?)의 최상위에 `Gemfile`을 생성하고 아래 내용을 추가한다.
   
> source 'https://rubygems.org'  
> gem 'jekyll-admin', group: :jekyll_plugins  
  
* jekyll-admin을 설치하자.  
  
* bundle을 사용하면 Gemfile을 참조하여 플러그인이 설치된다.  
   
> bundle install  
  
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-08-19.png){: .wh50 .center}   
  
* [admin](http://127.0.0.1:4000/admin)`( http://127.0.0.1:4000/admin )` 페이지를 확인해 보자.  
    
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-09-38.png){: .wh50 .center}   


 
  
* 관리자 페이지에서 에디터를 사용하여 포스팅을 GUI 환경에서 할 수 있다.  
  
  * jekyll-admin의 에디터에서는 Markdown 문법을 사용해야 된다.  
  

 
![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-10-01.png){: .wh50 .center}   
 

 

<br>
## 6.1 VS 코드에서 마크다운 개발환경 만들기

 

### **Extension 설치**

* Markdown All in One  
  
  * 마크다운 미리보기 및 자동완성을 사용할 수 있다.  
  

* Paste Image  
  
  * 마크다운에 클립보드 이미지를 삽입할 수 있다.(클립보드 이미지를 자동 저장해준다.)  
  

 
편하게 사용하기 위해 확장 프로그램의 설정을 추가하자.  


![](/assets/img/blog/2019-03-14-jekyll-theme/2019-03-14-14-10-54.png){: .wh50 .center}   


VS 코드에서 F1을 누르고 `Open Settings (JSON)` 를 검색하여 아래 내용을 추가하자.  


```json
"pasteImage.path": "${projectRoot}/assets/img/blog/${currentFileNameWithoutExt}",
"pasteImage.basePath": "${projectRoot}",
"pasteImage.forceUnixStyleSeparator": true,
"pasteImage.prefix": "/",
"markdown.extension.toc.githubCompatibility": true
```
 