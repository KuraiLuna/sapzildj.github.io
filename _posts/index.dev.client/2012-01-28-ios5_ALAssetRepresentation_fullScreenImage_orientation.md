---
title: "ALAssetRepresentation fullScreenImage의 orientation"
layout: post
category : dev.client
tags : [ios5, alassetslibrary]
---
{% include JB/setup %}

개요
----

iOS Album image를 화면에 보여줄 때 사용하는 ALAssetRepresentation의
fullScreenImage는 그동안 rotation이 적용되지 않는 CGImageRef였는데,
iOS5부터는 orientation, scale이 모두 적용이 된 CGImageRef를 넘겨줌.

해결 방법
---------

-   아래와 같이 iOS5이상 일때와 아닐 때를 분기 처리함.

```objectivec
    ALAssetRepresentation *rep = [alasset defaultRepresentation];
    UIImage* fullScreenImage = nil;
    if (IsIOS5EqualOrOver)
    {
        fullScreenImage = [UIImage imageWithCGImage:[rep fullScreenImage]];
    }
    else
    {
        fullScreenImage = [UIImage imageWithCGImage:[rep fullScreenImage]
                                               scale:rep.scale
                                         orientation:(UIImageOrientation) rep.orientation];
    }
```
