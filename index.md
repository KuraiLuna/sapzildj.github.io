---
layout: page
title: Sapzildj's Blog Home
---
{% include JB/setup %}

## 소개
여기는 Sapzildj의 삽질 히스토리 저장소입니다.
프로그래밍 관련된 내용이 대부분입니다.

## 포스트 카테고리 안내

### dev.client
클라이언트 프로그래밍 기술 관련 내용들입니다.

### dev.server
OS나 서버 프로그램 설치 및 서버 프로그래밍 기술 관련 내용들입니다.

### dev.vcs
버전 관리 시스템(Version Control System) 관련 내용들입니다.

### etc
그 외에
    
## Recent Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## To-Do

This theme is still unfinished. If you'd like to be added as a contributor, [please fork](http://github.com/plusjade/jekyll-bootstrap)!
We need to clean up the themes, make theme usage guides with theme-specific markup examples.


