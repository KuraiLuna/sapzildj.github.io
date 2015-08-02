Resource디렉토리와 Data 디렉토리 경로 분리
===================================

issue 발생
---------

iOS8에서 NSCachesDirectory에 있는 이미지 파일을 상대 경로로 대략 아래와 같이 로드했을 때 nil이 return 됨.
```objectivec
[UIImage imageNamed:../Library/Caches/a@2x.png];
```

원인 분석
-------

iOS8에서는 Resource디렉토리(App.app)와 Data 디렉토리(NSCachesDirectory, NSTemporaryDirectory 등)를 별도 디렉토리로 관리함.
그런데 [UIImage imageNamed:]는 Resource디렉토리 기준의 상대 경로로 Data 디렉토리를 찾게 되므로 문제가 발생함.

-   iOS7 디렉토리 구조
    -   App UUID
        -   Documents
        -   Library
            -   Caches
            -   Preferences
        -   tmp
        -   App.app
-   iOS8 디렉토리 구조
    -   Resource디렉토리 UUID
        -   App.app
    -   Data디렉토리 UUID
        -   Documents
        -   Library
            -   Caches
            -   Preferences
        -   tmp

대응
---

iOS8에서는 아래와 같이 Data 디렉토리를 절대 경로로 처리함.

```objectivec
NSString* imagePath = [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) firstObject] stringByAppendingPathComponent:@"a@2x.png"];
[UIImage imageNamed:imagePath];
```

관련 URL
-------

<http://stackoverflow.com/questions/25989767/writing-reading-to-paths-with-interleaved-double-dots-fails-on-ios-8>