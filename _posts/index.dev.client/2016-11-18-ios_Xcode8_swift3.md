---
title: "Xcode8, Swift3 적용"
layout: post
category : dev.client
tags : [ios, xcode8, swift3]
---
{% include JB/setup %}

개요
----

기존 Xcode7.3(swift 2.2)에서 Xcode8.1(swift3.0.1) 적용하면서 일어났던 일들을 정리해본다.

Cocoapods
---------
 - Pod 프로젝트들의 Swift버전 명시(Xcode 8.1은 swift version이 3.0이 아니라 3.0.1)

```ruby
post_install do |installer|
    installer.pods_project.targets.each do |target|
        target.build_configurations.each do |config|
            config.build_settings['SWIFT_VERSION'] = '3.0.1'
            config.build_settings['MACOSX_DEPLOYMENT_TARGET'] = '10.10'
        end
    end
end
```

Swift3 문법 적용
--------------
Swift3는 정말 코드가 간결하고 아름답게 바뀌었다.(하지만 그만큼 컨버팅할 때 개고생한다.....ㅠㅠ)

[공식 홈페이지 Swift3 마이그레이션 가이드](https://swift.org/migration-guide/)

 - func를 호출할 때 첫번째 파라미터명도 명시적으로 넣어줘야 함.
 - enum instance들이 소문자로 바뀜.
 - sharedxxxxx, standardxxxx 등의 변수나 func명은 shared, standard 등으로 줄여서 사용하게 됨.
 - DispatchQueue 사용 문법이 바뀜.

```swift
// Swift 2.2
dispatch_async(dispatch_get_main_queue(), { // Do something }})

// Swift 3
DispatchQueue.main.async(execute: { // Do something }})

// make new dispatch queue in Swift3
DispatchQueue(label: name, attributes: [.serial, .qosDefault])
```

 - 클래스 prefix NS 모두 제거, 파라미터명에 Type명이 들어간 부분 모두 제거.
 - closure의 파라미터명들이 모두 익명으로 바뀜. [참고](http://stackoverflow.com/questions/38669725/how-do-you-document-the-parameters-of-a-functions-closure-parameter-in-swift-3).
 - closure가 기본적으로 non-escaping기반으로 동작됨. 아마, non-escaping일 때는 Stack 메모리에서 갖고있고, escaping일 때는 Heap 메모리에서 갖고 있으려고 그런 것으로 보인다. [참고](http://stackoverflow.com/questions/39063499/updating-closures-to-swift-3-escaping/)
 - Optional Int Type Compare 하는 코드에 대해 Xcode Auto converting을 하면 아래와 같은 코드가 추가됨. Optional에 대한 unwrapping을 해주고 비교하도록 수정해야 함.

```swift
// FIXME: comparison operators with optionals were removed from the Swift Standard Libary.
// Consider refactoring the code to use the non-optional operators.
fileprivate func < <T : Comparable>(lhs: T?, rhs: T?) -> Bool {
  switch (lhs, rhs) {
  case let (l?, r?):
    return l < r
  case (nil, _?):
    return true
  default:
    return false
  }
}
// FIXME: comparison operators with optionals were removed from the Swift Standard Libary.
// Consider refactoring the code to use the non-optional operators.
fileprivate func > <T : Comparable>(lhs: T?, rhs: T?) -> Bool {
  switch (lhs, rhs) {
  case let (l?, r?):
    return l > r
  default:
    return rhs < lhs
  }
}
```

 - func에서 return한 값을 사용하지 않으면 warning 발생. _ = aFunc() 과 같이 처리해야 함.

APNS 관련 처리
------------
 - Xcode8 Capabilities에서 ON
  - Xcode8부터는 설정을 키지 않으면 Push가 동작하지 않는다. [참고1](http://stackoverflow.com/questions/39266891/xcode-8-push-notification-capabilities-and-entitlements-file-setting), [참고2](https://eladnava.com/send-push-notifications-to-ios-devices-using-xcode-8-and-swift-3/)
  - 신기한 것은 entitlements 파일에 aps-environment값으로 production이 아니라 development만 입력해놔도 된다;
 - APNS token data.description의 return 값 변경. [참고](http://stackoverflow.com/questions/39495391/swift-3-device-tokens-are-now-being-parsed-as-32bytes)
  - swift2.2에서는 return값이 hexString이라서 편하게 사용하고 있었는데, 값자기 32BYTES 로 return을 하고 있다 ㅜㅜ. hexString을 직접 구현해서 처리하면 됨.
- iOS10 부터 UserNotification.framework를 사용할 수 있고, Rich message push가 가능하다. [참고](https://blog.pusher.com/how-to-send-ios-10-notifications-using-the-push-notifications-api/)
	- 하지만 필요없는 경우 iOS10 별도 처리는 안해도 됨.

Dependency Module 업그레이드
--------------------------------
대부분 swift3 문법에 맞춰서 버전을 업데이트하면 되었지만, 이슈가 발생했던 것들만 보면..

 -  [Alamofire](https://github.com/Alamofire/Alamofire)
    - 이 프로젝트는 swift3 전환하면서 iOS8 지원을 포기했다. 쓰고 싶으면 swift 2.3기반으로 쓰란다. [참고](https://github.com/Alamofire/Alamofire/issues/1367)
    - 하지만 한 프로젝트에 여러 가지의 swift버전을 사용할 수 없는 것... 하아, swift계열 모듈들은 너무 급진적이다..
  - [SwiftHTTP](https://github.com/daltoniam/SwiftHTTP)
      - Almofire의 대안으로 적용했는데 인터페이스들이 아직은 깔끔하지 않다.
      - 특히, Error 처리는 정말 맘에 안들었고, responseHandler도 Background Thread에서 호출되서 별도 처리해야 했고...
      - swift가 문법이 하도 바뀌니 차라리 Objective-C기반 모듈이 안전해보일 정도다. 그냥 Afnetworking을 쓰는게 좋았으려나...
 -  [SnapKit](https://github.com/SnapKit/SnapKit)
    - 자잘하게 문법이 바뀐 부분들 처리. [참고](https://github.com/SnapKit/SnapKit/blob/master/Documentation/SnapKit%203.0%20Migration%20Guide.md)
    - center 지정하는 방법이 바뀜. [참고](https://github.com/SnapKit/SnapKit/issues/360)

```swift
// Swift 2.2
make.center.equalTo(0)

// Swift3
make.center.equalToSuperview()
```

- [Geometry](https://github.com/sapzildj/Geometry)
    - 개발하신 분이 바쁘신지 Swift3 Migration을 안해주셔서 다른 분이 작업하신 것을 fork해서 CoreGraphics 관련 변수명 충돌 부분만 수정해서 사용 중이다.

iOS10에서 AVVideoCompositionCoreAnimationTool을 사용했을 때 AVPlayer Bug
-------------------------------------------------------------------
이거 완전 심각한 버그인데 iOS10.1.1까지도 아직 안고쳐졌다. workaround도 아직 못찾았다.
iOS에서 Video 편집은 보통 AVFoundation을 이용해서 하는데, 갑자기 iOS10부터 AVMutableVideoComposition의 AVVideoCompositionCoreAnimationTool을 사용해서 ExportSession을 돌리고, 이 파일을 PhotoKit을 이용해서 Camera Roll에 저장시킨다.
그리고 나서, iOS 기본 앨범 앱을 왔다갔다 하면 갑자기 내 앱 쪽의 AVPlayerLayer가 검게 보인다.
검게 보일 때는 기본 앨범 앱에서도 해당 Video는 검게 보인다. 그러다가 갑자기 어느 순간 다시 보이기 시작한다.
[참고1](http://stackoverflow.com/questions/39760147/ios-10-0-10-1-avplayerlayer-doesnt-show-video-after-using-avvideocomposition),
[참고2](http://stackoverflow.com/questions/39560386/avplayer-playback-fails-while-avassetexportsession-is-active-as-of-ios-10/39786820#39786820)

GLKit
-----
iOS10에서는 투명 이미지를 텍스처로 로드시킬 때 GLKTextureLoader의 GLKTextureLoaderApplyPremultiplication 옵션이 기존(iOS8~9)과 반대로 작동한다.
GL Shdaer에서는 Premultiplied기반으로 처리를 하면 된다.
아래는 버전별 옵션을 분기 처리한 코드.

```objectivec

#define IS_OS_10_OR_LATER    ([[[UIDevice currentDevice] systemVersion] floatValue] >= 10.0)

+ (nullable GLKTextureInfo *)textureWithImage:(UIImage * __nonnull)image
                                        error:(NSError * __nullable * __nullable)outError
{
    NSDictionary* options = @{GLKTextureLoaderApplyPremultiplication: @YES};
    if (IS_OS_10_OR_LATER)
    {
        options = @{GLKTextureLoaderApplyPremultiplication: @NO}; // TODO: May be iOS10 bug?
    }

    GLKTextureInfo* textureInfo = [GLKTextureLoader textureWithCGImage:image.CGImage
                                                               options:options
                                                                 error:outError];

    return textureInfo;
}
```


느낀 점
-------
iOS10은 개발자를 넘 고통스럽게 했다...베타 버전부터...
제발 animationTool은 좀 고쳐주면 좋겠다. 안고쳐주면 CustomCompositor로 다 구현해야하는 데...흑흑
