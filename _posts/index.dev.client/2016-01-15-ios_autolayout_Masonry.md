---
title: "Masonry를 이용하여 코드 기반으로 iOS Autolayout 적용"
layout: post
category : dev.client
tags : [ios, autolayout, masonry, snapkit]
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
iOS AutoLayout 관련 Framework
--------------------------------

iOS AutoLayout 기능을 Wrapping한 Framework이 있을 것 같아서 찾아보니
다행히도 비슷한 의도의 Framework이 몇 가지 있었다.
[이곳](http://www.letmecompile.com/advanced-auto-layout/)에서 알게 된
 - [Masonry](https://github.com/Masonry/Masonry)
 - [KeepLayout](https://github.com/iMartinKiss/KeepLayout)
 - [ReactiveCocoaLayout](https://github.com/ReactiveCocoa/ReactiveCocoaLayout)

그리고 [CTAssetsPickerController](https://github.com/chiunam/CTAssetsPickerController) 에서 사용하는 
- https://github.com/PureLayout/PureLayout

이 중에서 [Masonry](https://github.com/Masonry/Masonry)를 실제 적용해봤다.

사용 방법
---------
공식 홈페이지에 설명이 잘되어 있어서 몇가지 사례만 써본다.
mas_makeConstraints, mas_updateConstraints, mas_remakeConstraints 등의 메소드 Block내에서 원하는 대로 다른 뷰와의 관계를 지정한다.

```objectivec

UIView* superview = self.superview;
UIView* anotherView; // 있다고 가정.
[self mas_updateConstraints:^(MASConstraintMaker *make) {
	make.center.equalTo(superview); // 부모뷰의 정가운데
	
	make.centerX.equalTo(superview); // 부모뷰의 가로 방향 가운데	
	make.centerY.equalTo(superview); // 부모뷰의 세로 방향 가운데		
	
	make.bottom.equalTo(0); // 부모뷰의 하단에 붙임.
	
	make.top.equalTo(0); // 부모뷰의 상단에 붙임.
	
	make.left.equalTo(anotherView).offset(10); // anotherView의 left + 10
}
```

느낀 점
-------
- AutoLayout을 정말 가독성 좋게 사용할 수 있게 해준다.
- 필수로 필요한 기능은 이미 다 있는 것 같다.
- *** Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: '*** +[NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]: Invalid pairing of layout attributes' 를 보지 않을 수 있도록 모듈 자체에서 Validation이 많이 필요할 듯 싶다.

알아둘 만한 내용
----------------

위 Masonry 공식 홈페이지에 정리가 워낙 잘되어 있어서 몇가지 사항만 언급한다.
(Masonry 0.6.4 버전 기준, iOS 6.0이상 지원)

- 공통의 부모 View가 없으면 Masonry의 NSAssert에 걸린다.
- AutoLayout을 UIView에 적용할 땐 translatesAutoresizingMaskIntoConstraints = NO을 매번 해줘야 하는데 이 부분은 Masonry의 MASConstraintMaker를 만들때 알아서 해준다.
- right, bottom 방향기준으로 AutoLayout을 잡을 때는 마이너스 값으로 줘야한다.
- MASConstraintMaker에 대해 equalTo를 값을 지정할 때 mas_equalTo를 사용하면 CGFloat, NSInteger와 같은 Primitive Type이거나 CGSize와 같은 구조체의 경우도 바로 넘길 수가 있다.
- mas_updateConstraints는 기존 Constraint가 있으면 constant만 바꾸지만,  mas_remakeConstraints 메소드는 기존 Constraint를 모두 제거하고 다시 Constraint를 Add한다.
- UIView 객체에 setNeedsUpdateConstraints을 호출했는데도 updateConstraints 메소드가 호출안되는 경우에는 setNeedsLayout 메소드를 호출하니 해결되었다.
- Swift 버전은 [SnapKit](https://github.com/SnapKit/SnapKit)이라는 별도의 모듈로관리되고 있다(쓰는 법은 거의 비슷함).

Last Updated = 2016년 4월 5일 오전 11시 18분 20초 GMT+9
