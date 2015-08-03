---
title: "ALAssetsLibrary disableSharedPhotoStreamsSupport"
layout: post
category : dev.client
tags : [ios6, alassetslibrary]
---
{% include JB/setup %}

개요
----

ALAssetsLibrary로 부터 받은 포토 데이터 리스트들은 변화가 생겼을 때
ALAssetsLibraryChangedNotification이 발생하고 그것을 observing하고
있다가 리스트를 갱신해줘야 함.

그런데 photo stream을 사용하고 있는 경우 stream이 업데이트가 되어
받아오고 있을 때도 계속 위 notification이 발생하여 갱신을 처리하다 보면
앱이 제대로 사용하기 힘든 지경에 이르게 됨.

```objectivec
[ALAssetsLibrary disableSharedPhotoStreamsSupport]; // 공유된 포토 스트림을 통한 ALAssetsLibraryChangedNotification을 발생을 막는다. 
```

그런데 iOS6에서 위 메소드를 테스트해봤지만 완벽히 notification이 전혀
발생안하는 것은 아니었다.

notification을 덜 받기 위한 수단으로 생각하는 게 좋을 것 같다.

iOS6에서는 그 전 버전들에서와는 다르게
ALAssetsLibraryChangedNotification이 발생해도 기존 갖고 있던
ALAsset객체들이 invalid 상태로 변하지 않는다. 그렇기 때문에
ALAssetsLibraryChangedNotification을 아예 처리 안하는 것도 한 방법이다.
