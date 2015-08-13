---
title: "Google Tag Manager 개요 및 적용"
layout: post
category : dev.server
tags : [google, tagmanger, analytics]
---
{% include JB/setup %}

개요
--------------

- GTM([Google Tag Manager](https://tagmanager.google.com))은 GA([Google Analytics](http://www.google.com/analytics))를 비롯한 여러 모니터링/마케팅 플랫폼들(GTM에서는 DataLayer라고 함)에 대한 Container역할을 하는 플랫폼

주요 기능
-------------------

- Container 기반의 Configuration을 관리가 가능하고, Container를 버전 관리할 수 있음.
	- Publish가 안된 작업 중인 Container Version을 Preview기능으로 테스트해볼 수 있음.
- Tag 설정에 따라 DataLayer에 전달할 데이터를 가공하여 전달 가능.
	- GA Property id(UA-xxxxxxxx-xx), GA parameter 등을 User-defined Variables로 정의하면, Tag 설정에서 해당 값을 사용할 수 있음.
- Tag에 설정한 Trigger에 따라 Tag가 Fire되는 조건을(DataLayer에 전달하는 시점)을 유연하게 정의할 수 있음.
	- Tag에서 사용한 Built-in Variables(Event, App Name, App Version Code, App Version, Name 등),  User-defined Variables들을 evaluation해서 Tag가 Fire하는 조건을 정의함.
	- GA 외에도 Google AdWords, Google doubleclick, Function Call 등의 여러가지 플랫폼과 연동 가능.

적용 방법
-----------------

- 아래를 참고하여 GTM WEB 세팅
	- <https://support.google.com/tagmanager/answer/2574370>
	- <https://support.google.com/analytics/answer/6164470?hl=en>
- GTM iOS v3 SDK 문서를 참고하여 App에 적용
	- <https://developers.google.com/tag-manager/ios/v3/>

체크 포인트
-------------

- GTM Refresh Interval
	- GTM의 새 버전을 확인하는 간격, 기본은 12시간.
	- 앱에서 수동으로 Refresh도 가능.
- GTM에서 GA Sample Rate 적용
	- GA에서는 일반 계정의 경우 천만 hits/월, 프리미엄 계정의 경우 10억 this/월 limit가 있음.
	- GA에서의 Data collection sampling의 경우 유저에 따라 지속적으로 적용되며, 앱의 경우 다운로드 기준으로 보면 됨.
	- Sample 대상으로 선택된 유저가 아닌 경우 아예 GA hits를 전송하지 않음(샘플링이 클라이언트 기준으로 적용).
	- 적용 방법
		- GTM WEB > Variables에서 User-defined Variable로 sampleRate Constant 추가하고 값은 0~100으로 입력.
		- GTM WEB > Tag 설정 > More Settings > Field Name(sampleRate), Value({{sampleRate}})
	- 관련 URL
		- <https://support.google.com/analytics/answer/2637192?hl=en>
		- <https://developers.google.com/analytics/devguides/collection/analyticsjs/field-reference#sampleRate>
		- <https://developers.google.com/analytics/devguides/collection/ios/v3/advanced>
		- <https://support.google.com/analytics/answer/1042498?hl=en>
		- <https://support.google.com/analytics/answer/1733979?hl=en>
- GTM Dispatch Interval
	- GTM에서 tracking한 데이터를 전송하는 간격, 기본은 120초이며 변경 가능함.
		- <https://developers.google.com/tag-manager/ios/v3/reference/interface_t_a_g_manager#a0fbe2615c7b2dd59290c4cca7d9e38f2>

참고 사항
-------

- Old Interface(<http://google.com/tagmanager>)는 2015/06/01까지만 사용 가능.
	- 관련 공지 URL = <https://support.google.com/tagmanager/answer/4605576>
	- 새로 시작하는 Account는 New Interface(<https://tagmanager.google.com>)에서 작업해두거나, Old Interface에서 New Interface로 Migration이 가능함.
	- New Interface에서 바뀐 점은 대부분 UI 관련 부분이며, 아래와 같이 명칭 변경이 있었음.
		- Rules => triggers
		- Macros => variables
