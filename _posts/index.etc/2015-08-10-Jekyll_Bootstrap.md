---
title: "GitHub Page에 Jekyll Bootstrap 적용하기"
layout: post
category : etc
tags : [application, github.io]
---
{% include JB/setup %}

개요
---
심플하고 편한 Markdown 문서 타입에 매료된 후에 마크다운 형태로 블로그 포스팅을 할 수 있는 방법들을 계속 찾고 있었다.
하지만 이제는 GitHub Page에 Jekyll Bootstrap을 적용해서 쓰는 것으로 마음을 정했고, 그 적용 과정을 정리해보려고 한다.

GitHub Page
-----------
이미 많은 분들이 GitHub Page 기능을 사용하고 계시고 사용 방법에 대해서도 상세하게 설명하고 계시므로 참고했던 링크만 추가한다.
[GitHub의 페이지 기능 이용하기](https://dogfeet.github.io/articles/2012/github-pages.html)
[지킬로 깃허브에 무료 블로그 만들기](https://nolboo.github.io/blog/2013/10/15/free-blog-with-github-jekyll/)


Jekyll Bootstrap
----------------
GitHub에서 [Jekyll](http://jekyllrb.com/)을 이용해서 Markdown 문서를 HTML static 페이지로 만들어서 제공하는 기능이 있다는 것은 이해하겠는데...
HTML 디자인은 어쩔고....하고 고민하고 있던 중에 [Jekyll Bootstrap](http://jekyllbootstrap.com/)을 발견했다. 와우!
Bootstrap 디자인을 그대로 쓸 수 있고, 다른 테마를 선택해서 사용할 수도 있다.
문서마다 Disqus나 Facebook 코멘트를 남길 수 있도록 할 수 있고, tags/categories/sitemap/atom 등의 페이지가 자동으로 생성된다.


Jekyll Bootstrap 설치 방법
------------------------
그냥 <https://github.com/plusjade/jekyll-bootstrap/>에서 
Download ZIP으로 다운 받은 압축 파일을 풀어서 
본인의 GitHub Pages Repository에 Commit&Push하면 끝.



Post 작성 방법
------------
Post 작성은 Jekyll에서 하던 방식과 완전히 동일하다.
_posts 디렉토리에 Markdown 문서 파일을 넣어놓으면 되고, 이때 파일명은 년-월-일-Title.md가 된다.
Markdown문서는 아래와 같은 식으로 시작해야 한다.

```
---
title: "파일명과 다르게 title을 지정하고 싶으면 이 항목을 추가한다."
layout: post
category : lessons
tagline: "Supporting tagline"
tags : [intro, beginner, jekyll, tutorial]
---
{% raw  %}{% include JB/setup %}{% endraw  %}
```

* layout은 post나 page를 지정할 수 있다.
* tagline은 사용하기 싫으면 해당 항목 라인을 아예 삭제하면 된다.


Jekyll로 생성된 HTML 확인
----------------------
결과로 생성되는 HTML페이지를 GitHub에 올리기 전에 미리 확인하기 위해서는 
Jekyll을 설치하고 실행한 후에 <http://localhost:4000> 으로 들어가서 확인한다.


GitHub Flavored Markdown
------------------------
[GFM](<https://help.github.com/articles/github-flavored-markdown/>)을 사용하기 위해서는 _config.xml에 아래 내용을 추가한다.

```
kramdown:
  input: GFM
```

Google Analytics(UA)
--------------------
Jekyll Bootstrap의 master 브랜치에는 아직 기본적으로 Universal Analytics를 사용할 수 있도록 안되어 있다(<https://github.com/plusjade/jekyll-bootstrap/pull/135>).
_includes/JB/analytics 파일을 아래와 같은 식으로 수정한다.

```
{% raw  %}
{% when "googleUA" %}
  {% include JB/analytics-providers/googleUA %}
{% endraw  %}  
```

그리고 _config.xml을 아래와 같이 수정한다.

```
   analytics :
    provider : googleUA
    googleUA : 
         tracking_id : 'GA ID'

```


Last Updated = 2015년 8월 10일 오전 4시 29분 46초 GMT+9