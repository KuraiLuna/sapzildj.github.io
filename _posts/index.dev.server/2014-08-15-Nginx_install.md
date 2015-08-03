---
title: "CentOS5.10에 nginx 설치"
layout: post
category : dev.server
tags : [centos, install, nginx]
---
{% include JB/setup %}

서버 환경 확인
--------------

```bash
# cat /etc/*release*
CentOS release 5.10 (Final)
```

yum을 이용해서 설치
-------------------

-   /etc/yum.repos.d/nginx.repo 파일 생성

```bash
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```

-   yum install

```bash
# yum install nginx
```

서버 관리 command
-----------------

-   아래 Usage를 참고하여 start/stop/restart 등을 사용함.

```bash
# service nginx help
Usage: nginx {start|stop|restart|condrestart|try-restart|force-reload|upgrade|reload|status|help|configtest}
```

서버 환경설정
-------------

-   아래 디렉토리 안에 사이트명.conf 파일을 만들어서 추가

```bash
# cd /etc/nginx/conf.d/
```

-   nginx Configuration wiki

<http://wiki.nginx.org/Configuration>

<http://wiki.nginx.org/ServerBlockExample>

<http://wiki.nginx.org/FullExample>

<http://wiki.nginx.org/FullExample2>


참고
----

<http://wiki.nginx.org/Install>
