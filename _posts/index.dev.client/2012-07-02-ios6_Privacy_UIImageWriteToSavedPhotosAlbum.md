---
title: "iOS6 설정 > 프라이버시 > 사진 > Off"
layout: post
category : dev.client
tags : [ios6, privacy]
---
{% include JB/setup %}

개요
----

iOS6 설정 > 프라이버시 > 사진 > Off 상태에서 

아래 코드처럼 사진을 앨범에 저장하면 에러가 발생한다.

```objectivec
UIImageWriteToSavedPhotosAlbum(someImage, self, @selector(image:didFinishSavingWithError:contextInfo:), nil);
```

그러므로 아래와 같은 식으로 error 객체를 Privacy Off 상태인지 체크해서 별도 처리를 한다.

```objectivec
if ([ALAssetsLibraryErrorDomain isEqualToString:[aError domain]] && [aError code] == -3310)
{
	// do something
}
```

Last Updated : 2015년 8월 8일 오전 1시 24분 45초 GMT+9