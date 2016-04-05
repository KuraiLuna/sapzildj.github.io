---
title: "PhotoKit"
layout: post
category : dev.client
tags : [ios8, photokit]
---
{% include JB/setup %}


iOS8 소개 페이지
----------------

<https://developer.apple.com/ios8/>

PhotoKit 소개 번역
------------------

```
PhotoKit은 사진/비디오(iCloud 사진 포함)들을 다루는 새로운 API입니다.

이 사진/비디오 항목들은 기본 앨범 앱에 의해 관리되므로 가져와서 편집할
필요 없이 여러분의 앱에서 직접 카메라롤의 사진을 편집할 수 있습니다.

썸네일/전체 사이즈의 항목들을 가져오고 캐싱하는 처리, 변경 내역을
받아오고, 다른 앱에서 생긴 변경 내역 관찰하고, 편집 중인 항목을 이어서
편집하기 등의 주요 기능들은 쓰레드 세이프한 구조로 포함되어 있습니다.
```

```
PhotoKit provides new APIs for working with photo and video assets,
including iCloud Photos assets, that are managed by the Photos app, so
your app can edit photos directly in the Camera roll without having to
import them first. Key features include a thread-safe architecture for
fetching and caching thumbnails and full-sized assets, requesting
changes to assets, observing changes made by other apps, and resumable
editing of asset content.
```

개요
----

Apple에서 만든 SDK 중 최악으로 생각되는 ALAssetsLibrary를 포기하고 결국 완전히 새로운 PhotoKit이 나왔다.
PhotoKit 관련 Trouble Shooting 및 느낀 점을 써본다.

Album PosterImage
------------------
ALAssetsGroup의 posterImage와 같은 기능이 없다. 그냥 fetch해와서 requestImage로 꺼내와야 한다.
http://stackoverflow.com/questions/28164778/how-to-fetch-album-poster-image-using-photo-kit 참고.

PHImageManager의 requestImageForAsset:targetSize:contentMode:options:resultHandler 메소드
-------------------------------------------------------------------------------------
# iOS8에서 targetSize에 따라 1px 녹색선이 생김.
예를 들어 {723, 964} 이미지는 options가 뭐든 간에 오른쪽에 녹색선이 하나 생김.

# resultHandler 호출 횟수
(void (^)(UIImage *__nullable result, NSDictionary *__nullable info))resultHandler

PHImageRequestOptionsDeliveryMode
---------------------------------
DeliveryMode를 로그로 찍으면 
- PHImageRequestOptionsDeliveryModeOpportunistic = ask+fst
- PHImageRequestOptionsDeliveryModeHighQualityFormat = ask
- PHImageRequestOptionsDeliveryModeFastFormat = fst
즉, Opportunistic는 HighQualityFormat와 FastFormat의 OR임.

PHImageRequestOptionsDeliveryModeOpportunistic
----------------------------------------------
항상 여러번 resultHandler가 불리는 것은 아니다. requestImage 호출이 몰렸을 때만 FastFormat Image를 준다.

Video의 Thumbnail
-----------------
경우에 따라 requestImage를 하면 resultHandler의 결과에서 image가 nil일 때가 있다.
retry를 하면 image가 내려온다;

iCloud
------
다운받지 않은 iCloud 이미지/영상은 resultHandler의 결과에서 image가 nil을 준다.
게다가 syncronous=YES로 requestImage 호출을 하면 다운받을 때까지 앱이 먹통이 된다.

느낀 점
-------
- ALAssetsLibrary에 비하면 엄청나게 안정적이다.
- 성능은 CoreData의 한계 때문인지 그렇게 좋은 것 같지는 않다.
- Apple Album앱이 정말 PhotoKit API로만 만들었을까?? 어떻게 저 성능이 나오지?
