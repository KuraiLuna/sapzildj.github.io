---
title: "Webwork의 action 탐색 방식"
layout: post
category : dev.server
tags : [webwork,action]
---
{% include JB/setup %}

개요
----

Webwork에서는 /barspace/foo.action과 같이 네임스페이스가 있는 url이 호출되었을 때, 만약 매핑되는 action을 찾지 못한다면

디폴트 네임스페이스, 즉 /foo.action과 같은 url에 매핑되는 action을 찾는다. 이것은 문제가 생길 소지가 있다.

Webwork 공식홈페이지의 관련 내용
---------

<http://opensymphony.com/webwork/wikidocs/Namespace%20Configuration.html>

```
If a request is made to /barspace/foo.action, the action foo will be searched for in a namespace of /barspace. 
If the action is not found, the action will then be searched for in the default namespace. 
Unless specified, the default namespace will be "". In our example above, their is no action foo in the namespace /barspace, 
therefore the default will be searched and /foo.action will be executed.
```

문제점
-----

- 외부 서버와 URL CALL방식으로 통신하는 경우에 잘못된 URL(개발자가 의도하지 않은 URL)이 계속 호출될 가능성이 있음.
- 사용자가 잘못된 URL을 통해 계속 접근할 가능성이 있음.
- 네임스페이스만 다르고 action name은 동일한 경우, 쌩뚱 맞은 페이지를 보게 될 가능성이 있음.
	- /membership/main.action - 회원제 관련 메인 페이지
	- /main.action - 홈페이지의 메인 페이지
	- 위와 같은 URL을 정의해뒀을 때 xwork.xml의 네임스페이스 지정 실수 하나로 큰 결과를 초래할 가능성이 있음(경험담)
	
	
결론
---
- 어짜피 하위버전 호환성 때문에 문제점이 있더라도 위 기능을 아예 뺄 수는 없을 거라 생각.
- 프레임워크를 사용하는 클라이언트 코드 개발자들의 위 내용에 대한 정확한 인식이 필요.
- 네임스페이스만 다르고 action name이 동일한 경우는 지양해야 함.