---
title: "Python web framework django 설치"
layout: post
category : dev.server
tags : [python, django]
---
{% include JB/setup %}

설치 환경
-------
이 문서에서는 맥(OS X Mavericks 10.9.4)에서 설치했지만, Linux 환경에서의 설치도 비슷했던 것으로 기억함.

pip 설치
-------
<https://bootstrap.pypa.io/get-pip.py>를 다운로드해서 root 권한으로 실행.
이미 설치되어 있다면 Skip.
```bash
sudo python get-pip.py
```
```bash
Downloading/unpacking pip
	Downloading pip-1.5.6-py2.py3-none-any.whl (1.0MB): 1.0MB downloaded
Installing collected packages: pip
Successfully installed pip
Cleaning up...
```
	
django 설치
----------
```bash
sudo pip install Django
```
```bash
Downloading/unpacking Django
	Downloading Django-1.6.6-py2.py3-none-any.whl (6.7MB): 6.7MB downloaded
Installing collected packages: Django
Successfully installed Django
Cleaning up...
```

설치 확인
-------
```python
Python 2.7.5 (default, Mar  9 2014, 22:15:05) 
[GCC 4.2.1 Compatible Apple LLVM 5.0 (clang-500.0.68)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import django
>>> print(django.get_version())
1.6.6
```

.gitignore
----------
프로젝트 처음 세팅할 때 늘 하는 일인 .gitignore를 아래 것으로 사용함.
http://stackoverflow.com/a/14058267/1028711


관련 URL
---------
https://pip.pypa.io/en/latest/installing.html
https://www.djangoproject.com/
https://docs.djangoproject.com/en/1.6/intro/install/