
## Github Blog
- [https://Sungchul-P.github.io](https://Sungchul-P.github.io)
- 이 블로그는 [박민](https://github.com/isme2n/isme2n.github.io)님과 [변성윤](https://github.com/zzsza/zzsza.github.io)님의 블로그 테마를 기반으로 제작되었습니다

### Structure
- 변성윤님께서 정리해 주신 블로그 구조입니다

```
├── README.md
├── _config.yml : 기본 설정이 저장된 파일
├── _data : 유저 데이터가 저장된 폴더, author.yml만 수정하면 됨
├── _draft : 초안 작성 폴더, 커밋해도 반영되지 않음
├── _featured_categories : 카테고리(메뉴판의 큰 제목)
├── _featured_tags : 카테고리의 태그(메뉴판의 소제목)
├── _includes : 기본 홈페이지 포맷
├── _ipynbs : ipynb 저장 폴더
├── _js : 자바스크립트 소스 저장 폴더
├── _layouts : 타입별 레이아웃 폴더
├── _plugins : 플러그인 저장 폴더. 그러나 Github에서 빌드시 플러그인 사용 불가능
├── _posts : 글 저장 폴더
├── _sass
├── _site : 빌드시 생기는 폴더, 신경쓸 필요 없음
├── about.md : about에서 나타날 내용
├── assets : css, js, img 등 저장하는 폴더
├── favicon.ico : favicon 아이콘
├── feed.xml
├── index.html
├── robots.xml
├── search.html
├── sitemap.xml
├── tile-wide.png
└── tile.png
```

- ```_config.yml```, ```_data```, ```_featured_categories```, ```_featured_tags```, ```about.md``` 내용 수정
- ```favicon.ico```, ```tile-wide.png```, ```tile.png``` 원하는 이미지로 설정


### 글 작성
- ```_featured_categories```, ```_featured_tags``` 설정한 후, ```_posts```에 글을 작성합니다
- 글 제목 형태는 ```2018-01-03-title1.md``` 이런 방식처럼 작성! 날짜를 빼고 쓰면 반영되지 않습니다
