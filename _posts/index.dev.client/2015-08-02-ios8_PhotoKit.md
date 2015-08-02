PhotoKit
========

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

설명
----

apple에서 만든 SDK 중 최악으로 생각되는 ALAssetsLibrary를 개선하다가
결국 apple에서 포기하고 새로 API를 만들었다!

아직 Beta SDK를 못깔아봐서 테스트를 못해봤지만 정말 기대된다!
