---
title: "iOS 정규표현식"
layout: post
category : dev.client
tags : [ios, nsregularexpression]
---
{% include JB/setup %}

개요
----

iOS 4.x부터 NSRegularExpression를 지원한다. 그 전에는
RegexKit.framework를 사용하는 수밖에 없었는데...

rangeOfString:를 이용한 간단한 방식
-----------------------------------

-   아래 메소드에서 NSStringCompareOptions에 NSRegularExpressionSearch를
    넣어주면 정규표현식이 match되는 NSRange를 return해준다.
    -   NSRegularExpressionSearch는 NSCaseInsensitiveSearch,
        NSAnchoredSearch 외에 다른 option mask과 같이 사용할 수 없다.

```objectivec
- (NSRange)rangeOfString:(NSString *)aString options:(NSStringCompareOptions)mask;
- (NSRange)rangeOfString:(NSString *)aString options:(NSStringCompareOptions)mask range:(NSRange)searchRange;
- (NSRange)rangeOfString:(NSString *)aString options:(NSStringCompareOptions)mask range:(NSRange)searchRange locale:(NSLocale *)locale NS_AVAILABLE(10_5, 2_0);
```

NSRegularExpression를 이용한 세밀한 처리
----------------------------------------
