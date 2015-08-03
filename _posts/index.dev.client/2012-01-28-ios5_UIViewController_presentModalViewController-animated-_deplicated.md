---
title: "UIViewController presentModalViewController:animated: deplicated됨"
layout: post
category : dev.client
tags : [ios5, presentmodalviewcontroller]
---
{% include JB/setup %}

개요
----

iOS5부터는 animation 관리 방식이 바뀌었는지 UIViewController의
presentModalViewController:animated:나 dismissModalViewController:를
연속적으로 호출하거나 crash가 남. 게다가 이 API들은 deplicated됨.

UIViewController.h를 보면
-------------------------

```objectivec
/*
  The next two methods are replacements for presentModalViewController:animated and
  dismissModalViewControllerAnimated: The completion handler, if provided, will be invoked after the presented
  controllers viewDidAppear: callback is invoked.
*/
- (void)presentViewController:(UIViewController *)viewControllerToPresent animated: (BOOL)flag completion:(void (^)(void))completion NS_AVAILABLE_IOS(5_0);
// The completion handler, if provided, will be invoked after the dismissed controller's viewDidDisappear: callback is invoked.
- (void)dismissViewControllerAnimated: (BOOL)flag completion: (void (^)(void))completion NS_AVAILABLE_IOS(5_0);

// Display another view controller as a modal child. Uses a vertical sheet transition if animated.This method has been replaced by presentViewController:animated:completion:
- (void)presentModalViewController:(UIViewController *)modalViewController animated:(BOOL)animated NS_DEPRECATED_IOS(2_0, 6_0);

// Dismiss the current modal child. Uses a vertical sheet transition if animated. This method has been replaced by dismissViewControllerAnimated:completion:
- (void)dismissModalViewControllerAnimated:(BOOL)animated NS_DEPRECATED_IOS(2_0, 6_0);
```

기존 API대신 Modal이라는 keyword를 빼고 completion 파라미터를 더 추가한
아래 메소드 2개를 사용하라고 함.

```objectivec
- (void)presentViewController:(UIViewController *)viewControllerToPresent animated: (BOOL)flag completion:(void (^)(void))completion NS_AVAILABLE_IOS(5_0);
- (void)dismissViewControllerAnimated: (BOOL)flag completion: (void (^)(void))completion NS_AVAILABLE_IOS(5_0);
```

정리하면..
----------

iOS5부터는 새로운 API를 사용하는 것이 좋고, ViewController를
present/dismiss하는 작업들은 completion이 호출되기 전에 연속적으로 하면
crash가 발생하므로 조심해서 사용할 것.
