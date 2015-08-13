---
title: "ShareKit 사용했는데 iOS 5.0.1이하에서 배포된 앱에서만 크래시 나는 현상"
layout: post
category : dev.client
tags : [ios5, sharekit]
---
{% include JB/setup %}

크래시 발생 상황
------------

이번에 ShareKit을 앱에 추가했는데, iOS 5.0.1 이하 에서 무조건 앱이 뜨다가 크래시 나는 사태를 겪었다.
원인은 이 이슈([issue #394](https://github.com/ShareKit/ShareKit/pull/394)) 때문이다. 

```objectivec
// ShareKit에서 NSURLIsExcludedFromBackupKey 심볼이 있는지 확인을 하기 위해 아래와 같이 하지만 

if (&NSURLIsExcludedFromBackupKey == nil) 
{
// do something
}
```

CoreFoundation.framework이 required로 링크되어 있는 경우 앱을 배포하면 실행하자 마자 크래시가 난다.
그래서 ShareKit 설치 매뉴얼을 다시 살펴보니 **optional로 써야하는 framework은 Twitter.framework, CoreFoundation.framework, UIKit.framework(iOS 4.2보다 하위 iOS를 지원하는 앱일 때만) 이렇게 총 3개**였다..
외부 모듈 사용할 때 늘 링크 관련 부분을 잘 확인할 필요가 있는 것 같다.

ShareKit 설치 매뉴얼의 Framework 부분
---------------------------------
**Add Frameworks**
Expand the 'Frameworks' group in your project's file list. Make sure you have the following frameworks:
- SystemConfiguration.framework
- Security.framework
- MessageUI.framework
- CFNetwork.framework (for Flickr)
- CoreLocation.framework (for Foursquare)
- **Twitter.framework (it is new in iOS 5, so if you deploy to older versions of iOS, mark it optional)**
- **CoreFoundation.framework (mark it optional, it is a workaround for issue #394)**
- **if you plan to use print sharer, and have deployment target < iOS 4.2 you have to mark UIKit.framework optional too, unfortunately.**


Last Updated : 2012년 10월 24일 오후 2시 7분 0초 GMT+9