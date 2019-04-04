---
layout: post
title: GitHub Pages로 무료 호스팅하기
desc: GitHub Pages로 무료 호스팅
categories: development
tags: git
comments: true
keywords: "gitpage,git,vscode"
---

- GitHub에서 무료로 제공하는 도메인을 이용하는 방법입니다.
	- 정적 웹 페이지를 구성할 수 있습니다.

---

## 1. Repository 생성

새로운 Repository를 `"<username>.github.io"` 형식으로 만들어 준다.
 

![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-39.png){: width="50%" height="50%"}   

---
## 2. Git 연동
  
![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-41-15.png){: width="50%" height="50%"}  
  

git clone으로 Repository 작업 공간을 만들자.
  

> git clone https://github.com/deuter431/deuter431.github.io


![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-41-08.png){: width="50%" height="50%"}  

 
Repository 이름으로 폴더가 생성된다. (내용이 있는 레포지터리 였다면 당연히 내용도 포함된다.)


> cd deuter431.github.io  
> dir

지금은 당연히 비어 있는 상태다.

![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-41-32.png){: width="50%" height="50%"}  


테스트 파일을 하나 만들고 Git으로 Push 해보자.

 
> echo "Hello World" > index.html

![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-41-43.png){: width="50%" height="50%"}  

<br/>
Git을 사용하는 방법은 여러 가지가 있으므로 편한 방법을 사용하면 된다.

아래 방법 중 하나를 사용해서 PUSH 해보자.
 

### 1) Git Bash 사용하기

> git add --all  
> git commit -m "Initial commit"  
> git push -u origin master  


![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-41-53.png){: width="50%" height="50%"}  

<br/>
### 2) VS Code의 Git 기능 사용하기


VS Code는 작업폴더에 .git 정보가 있으면 자동으로 인식한다.


index.html 파일이 변경 됐음을 알려준다.(새로 생성 했으므로)


![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-42-06.png){: width="50%" height="50%"}  
 

Commit 메시지를 입력하고, Ctrl + Enter를 누른다.

![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-42-14.png){: width="50%" height="50%"}  
 

스테이징 단계를 거쳐서 커밋할 수 있지만, 

따로 하지 않았으므로 자동으로 스테이징 후 커밋을 진행한다.

 
![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-42-24.png){: width="50%" height="50%"}  
 

아래 화면의 아이콘을 클릭하여, 푸시(PUSH)를 진행할 수 있다.

 
![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-42-35.png){: width="50%" height="50%"}  
 

또는 아래의 기타 작업 아이콘을 눌러서 원하는 작업을 선택할 수 있다.

![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-42-44.png){: width="50%" height="50%"}  

![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-42-54.png){: width="30%" height="30%"}  

 
---


## 3. 페이지 확인
<br/>
페이지에 적용이 됐는지 확인해 보자.

테스트로 만들었던 index.html이 잘 보인다.

GitPage는 Push 한 뒤에 약간의 시간차가 있으므로, 약간의 여유를 두고 확인한다.

(페이지가 안뜨는 경우, index.html 까지 직접 입력하여 접근해 보도록 한다.)

 
![](/assets/img/blog/2019-03-13-github-pages/2019-03-13-21-43-03.png){: width="50%" height="50%"}  
 
