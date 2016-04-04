---
title: "Masonry를 이용하여 코드 기반으로 iOS Autolayout 적용"
layout: post
category : dev.client
tags : [ios, autolayout, masonry]
---
{% include JB/setup %}

개요
----

iOS6부터 AutoLayout이 나왔지만, iOS6 하위 버전에서는 안돌아간다는 핑계로 적용을 안하고 있었는데 이제 왠만한 앱은 최소 iOS7이상만 지원하고 있는 상황으로 AutoLayout을 적용하지 않을 이유가 없어졌다.
그래서 코드로 AutoLayout을 적용하는 방법을 알아보니 코드량도 많고 가독성도 굉장히 떨어진다.
(https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html)
VFL(Visual Format Language)이란 게 있긴 하지만 VFL을 모르는 사람이 봤을 때 이것은 암호나 마찬가지인 것 같다.
또한, AutoLayout관련 기능도 iOS버전을 타는 것들이 좀 있다
예를 들면 NSLayoutConstraint에서 아래 메소드들도 iOS8이상이다.
```objectivec
 + (void)activateConstraints:(NSArray<NSLayoutConstraint *> *)constraints NS_AVAILABLE(10_10, 8_0);
 + (void)deactivateConstraints:(NSArray<NSLayoutConstraint *> *)constraints NS_AVAILABLE(10_10, 8_0);
```
그래서 iOS AutoLayout 기능을 Wrapping한 Framework이 있을 것 같아서 찾아보니
다행히도 비슷한 의도의 Framework이 몇 가지 있었다.
[이곳](http://www.letmecompile.com/advanced-auto-layout/)에서 알게 된
 - [Masonry](https://github.com/Masonry/Masonry)
 - [KeepLayout](https://github.com/iMartinKiss/KeepLayout)
 - [ReactiveCocoaLayout](https://github.com/ReactiveCocoa/ReactiveCocoaLayout)

그리고 [CTAssetsPickerController](https://github.com/chiunam/CTAssetsPickerController) 에서 사용하는 
- https://github.com/PureLayout/PureLayout

이 중에서 [Masonry](https://github.com/Masonry/Masonry)를 실제 적용해봤다.
	

알아둘 만한 내용
----------------

위 Masonry 공식 홈페이지에 정리가 워낙 잘되어 있어서 몇가지 사항만 언급한다.
(Masonry 0.6.4 버전 기준, iOS 6.0이상 지원)

-   AutoLayout을 UIView에 적용할 땐 translatesAutoresizingMaskIntoConstraints = NO을 매번 해줘야 하는데 이 부분은 Masonry의 MASConstraintMaker를 만들때 알아서 해준다.
-	right, bottom 방향기준으로 AutoLayout을 잡을 때는 마이너스 값으로 줘야한다.
-	UIView 객체에 setNeedsUpdateConstraints을 호출했는데도 updateConstraints 메소드가 호출안되는 경우에는 setNeedsLayout 메소드까지도 호출하면 해결된다.


Last Updated = 2016년 1월 15일 오전 10시 40분 20초 GMT+9
