---
title: "Masonry를 이용하여 코드 기반으로 iOS Autolayout 적용"
layout: post
category : dev.client
tags : [ios, autolayout, masonry]
---
{% include JB/setup %}

개요
----
iOS6부터 AutoLayout이 적용되어서 이젠 적용을 미룰 수 없게 되었다.
그런데 생짜 코드로 AutoLayout를 적용하기엔 코드량도 많고 가독성도 굉장히 떨어진다.
(https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)
VFL(Visual Format Language)이 있긴 하지만 VFL을 모르는 사람이 봤을 때 이것은 암호나 마찬가지인 것 같다.
또한, AutoLayout관련 기능도 iOS버전을 타는 것들이 좀 있다
예를 들면 NSLayoutConstraint에서 아래 메소드들도 iOS8이상이다.
```objectivec
+ (void)activateConstraints:(NSArray<NSLayoutConstraint *> *)constraints NS_AVAILABLE(10_10, 8_0);
+ (void)deactivateConstraints:(NSArray<NSLayoutConstraint *> *)constraints NS_AVAILABLE(10_10, 8_0);
```
그래서 iOS SDK의 AutoLayout 기능을 Wrapping한 Framework이 필요하다고 생각했다.
다행히도 비슷한 의도의 Framework이 몇 가지 있었다.(http://www.letmecompile.com/advanced-auto-layout/).
이중에서 Masonry(https://github.com/SnapKit/Masonry)를 적용하게 되었다.

알아둘만한 사항들
------------
위 Masonry 공식 홈페이지에 정리가 워낙 잘되어 있어서 몇가지 사항만 언급한다.
참고로 글쓰는 현재 Masonry 버전은 0.6.4이고, 최소 iOS 6.0이상 지원한다.

-   AutoLayout을 UIView에 적용할 땐 translatesAutoresizingMaskIntoConstraints = NO을 매번 해줘야 하는데 이 부분은 Masonry 내부에서 알아서 처리해준다.
-	left, bottom 방향으로 padding을 주고 싶은 경우는 마이너스 값으로 줘야한다.
-	UIView 객체에 setNeedsUpdateConstraints을 호출했는데도 updateConstraints 메소드가 호출안되는 경우에는 setNeedsLayout 메소드까지도 호출하면 해결된다.


Last Updated = 2016년 1월 15일 오전 10시 40분 20초 GMT+9