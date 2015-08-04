---
title: "legacy 프로젝트에 ARC 적용"
layout: post
category : dev.client
tags : [ios, arc]
---
{% include JB/setup %}

개요
----

ARC 적용을 미루고 미루다 이제 겨우 프로젝트에 ARC를 적용하면서 겪은
상황을 기록함.

환경
----

-   Xcode version = 5.1.1
-   전환 프로젝트의 deployment target version = 5.0

진행 순서
---------

-   Xcode 최상위 메뉴 \> Xcode \> Preference.. \> General \> Continue
    build after errors 체크되어 있는지 확인.
-   Xcode 최상위 메뉴 \> Edit \> Refactor \> Convert to Objective-C
    ARC... 선택 후 전환을 할 target을 선택
-   preview 화면에서 문제되는 부분이 없는지 확인 후 save
-   소스 중 ARC를 사용하지 않아야 하는 경우(예: 외부 소스코드라던가..)는
    프로젝트 선택 \> Target 선택 \> Build Phases \> Compile Sources
    섹션에서 해당 소스의 Compiler Flags에 -fno-objc-arc를 넣음.

그 외 수정이 필요한 케이스
--------------------------

-   CF계열에서 NS계열로 캐스팅해서 사용하던 부분은 아래와 같이
    \_\_bridge\_transfer 키워드를 넣어준다.

```objectivec
            CFUUIDRef uuidRef = CFUUIDCreate(NULL);
            CFStringRef uuidStringRef = CFUUIDCreateString(NULL, uuidRef);
            CFRelease(uuidRef);
            SET_PREF_OBJECT(kDeviceUuid, (__bridge_transfer NSString *)uuidStringRef);
```

-   assign으로 선언된 UIView property가 weak로 바뀌면서 생긴 compile warning(warning: assigning retained object to weak property;
	object will be released after assignment [-Warc-unsafe-retained-assign]) 대응
	
-   non-ARC에서는 아래와 같았던 코드를

```objectivec
@interface MyView : UIView
@property (assign) UIView* myView;

- (void)setupMyView;

@end

@implementation MyView
- (void)setupMyView
{
  self.myView = [[[UIView alloc] iniWithFrame:CGRectZero] autorelease];
  [self addSubview:self.myView];
}
@end
```


-   ARC에서는 아래와 같이 처리함.

```objectivec
@interface MyView : UIView
@property (weak) UIView* myView;

- (void)setupMyView;

@end

@implementation MyView
- (void)setupMyView
{
  UIView* myView = [[UIView alloc] iniWithFrame:CGRectZero];
  [self addSubview:myView];
  self.myView = myView;
}
@end
```

참고
----

<https://developer.apple.com/library/ios/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html>
