---
title: "UIBezierPath를 이용해서 도형 만들기, 마스크 적용하기"
layout: post
category : dev.client
tags : [ios, uibezierpath]
---
{% include JB/setup %}

개요
----
Core Graphics의 path 관련 메소드들의 wrapper인 UIBezierPath를 이용해 여러가지 도형을 만들어 본다.

다각형 만들기
----------

```objectivec
- (void)makePolygon
{
	UIBezierPath *path = [UIBezierPath bezierPath];
	
	// 시작점으로 이동
	[path moveToPoint:CGPointMake(0, 0)];
	
	// 다각형을 만들 때 필요한 만큼 선을 추가함.
	[path addLineToPoint:CGPointMake(p1, p2)];
	
	// 선을 모두 추가한 후엔 closePath를 이용해 첫번째와 마지막 점을 연결하는 선을 추가해서 마무리함.
	[path closePath];
}
```

- 주의할 점으로는
	- moveToPoint: 메소드는 다각형의 시작점을 지정할 때 한번만 호출해야 함.
	- closePath를 안 해주고 수동으로 addLineToPoint로 첫번째 점을 다시 지정할 수도 있지만, 이렇게 하면 CAShapeLayer로 stroke를 할 때 두 선 사이의 교차점이 lineWidth 절반 만큼 벌어지게 되므로 될 수 있으면 closePath를 사용하자.
	
원 만들기
-------

```objectivec
// maskPath라는 정사각형 bezier path를 파라미터로 받아서 그 안에 그릴 수 있는 원 모양의 path를 만들어 내는 예제
+ (UIBezierPath *)circlePathWithMaskPath:(UIBezierPath *)maskPath
{
    CGRect pathRect = CGPathGetPathBoundingBox(maskPath.CGPath);
    
    UIBezierPath *aPath = [UIBezierPath bezierPathWithArcCenter:CGPointMake(CGRectGetMidX(pathRect), CGRectGetMidY(pathRect))
                                                         radius:CGRectGetWidth(pathRect) / 2.f
                                                     startAngle:0
                                                       endAngle:M_PI * 2
                                                      clockwise:YES];
    return aPath;
}
```

UIBezierPath 이동, 크기 조정
-------------------------
- translation
	- CGAffineTransformMakeTranslation으로 CGAffineTransform을 만든 후에 applyTransform: 메소드로 path에 적용시킴.
- scale
	- CGAffineTransformMakeScale으로 CGAffineTransform을 만든 후에 applyTransform: 메소드로 path에 적용시킴.
	- 시작점을 중심으로 선들의 길이가 늘어남.
- 참고로 translation과 scale을 CGAffineTransformConcat으로 하나의 CGAffineTransform로 만들어서 한번에 applyTransform:했더니 원하는 결과가 안 나와서 applyTransform:을 두번 실행해서 처리했다.

UIBezierPath 복사하기
-------------------

```objectivec
+ (UIBezierPath *)copyPath:(UIBezierPath *)originPath
{
	UIBezierPath* resultPath = [UIBezierPath bezierPath];
	[resultPath appendPath:originPath];
	
	return resultPath;
}
```
위와 같이 appendPath:를 이용하면 path를 복사할 수 있을 뿐 아니라, 하나의 UIBezierPath 객체 내에 여러가지 도형을 추가할 수 있다.

UIBezierPath로 mask영역 만들기
---------------------------

```objectivec
// 이미지에 마스크 영역을 적용해서 context에 그리는 예제
+ (UIImage *)maskedImage
	UIImage* resultImage = nil;
	UIImage* image = [UIImage imageNamed:@"sample"];
	UIGraphicsBeginImageContextWithOptions(image.size, NO, 1.0f);
	{
		UIBezierPath* maskPath = [self makeMaskPath];
		[maskPath addClip]; // maskPath를 mask영역으로 적용함.
		[image drawAtPoint:CGPointZero];
	}
	resultImage = UIGraphicsGetImageFromCurrentImageContext();
	UIGraphicsEndImageContext();
	
	return resultImage;
}
```
만약에 하나의 context 내에서 addClip을 여러번 사용하여 여러가지의 마스크를 적용하고 싶은 경우에는 
addClip을 하기 전에 CGContextSaveGState();을 호출했다가 addClip을 사용 후에 다시 CGContextRestoreGState();으로 addClip 전 상태로 돌아가서, 다시 원하는 mask를 만든 후 addClip하는 식으로 처리가 가능하다.


```objectivec
// UIView에 마스크 영역 적용하기.
+ (void)applyMaskLayerToView
{
    CAShapeLayer* maskLayer = [CAShapeLayer layer];
    maskLayer.frame = self.view.bounds;
	
	UIBezierPath* maskPath = [self makeMaskPath];
	maskLayer.path = [maskPath CGPath];
	
	self.view.layer.mask = maskLayer;
}
```

opacity가 먹힌 마스크 영역 적용하기
-----------------------------

위 방식을 사용하면 마스크로 덮히는 부분에 layer의 opacity를 사용할 수가 없다.
배경이 비치는 마스크로 만들기 위해서는 fillRule을 kCAFillRuleEvenOdd로 사용해서 만들 수가 있다.

```objectivec
// UIView에 마스크 영역 적용하기.
+ (void)applyMaskLayerToView
{
    CAShapeLayer* maskLayer = [CAShapeLayer layer];
    maskLayer.frame = self.view.bounds;
	
	UIBezierPath* maskPath = [self makeMaskPath];
	maskLayer.path = [maskPath CGPath];
	
	self.view.layer.mask = maskLayer;
}
```



Last Updated = 2015년 8월 25일 오전 2시 40분 1초 GMT+9