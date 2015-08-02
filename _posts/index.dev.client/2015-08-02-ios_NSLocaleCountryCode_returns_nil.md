NSLocaleCountryCode nil 리턴
============================

현상
---
iOS에서 사용자 Device의 설정된 국가를 알기 위해서 아래와 같이 처리하곤 한다.
```objectivec
NSString* country = [[NSLocale currentLocale] objectForKey:NSLocaleCountryCode];
```
그런데 사용자 RegionFormat이나 알 수 없는 apple의 bug에 따라 이 country가 nil일 때가 있다.
RegionFormat까지 바꿔가며 테스트하는 경우가 흔치 않기 때문에 보통 테스트할 때는 발견하기가 쉽지 않다.

재현 스텝
-------
예전 iOS버전에서도 마찬가지 일 것 같은데 테스트한 환경은 7.1.2이다.
Settings App > General > International > Region Format을 아래 리스트 중 하나로 변경.

[Esperanto](http://ko.wikipedia.org/wiki/%EC%97%90%EC%8A%A4%ED%8E%98%EB%9E%80%ED%86%A0)
[Inuktitut](http://en.wikipedia.org/wiki/Inuktitut)
[Kashmiri](http://en.wikipedia.org/wiki/Kashmir)
[Sorani Kurdish](http://en.wikipedia.org/wiki/Sorani_Kurdish)
[Nuer](http://en.wikipedia.org/wiki/Nuer_language)

그리고 나서 NSLocaleCountryCode를 요청하면 nil이 return됨.

대응
---
return 값이 nil인지 체크. 특히 NSMutableDictionary에 setObject:forKey 메소드를 호출하거나 NSMutableArray에 addObject: 메소드를 호출할 때는 조심할 것.

관련 URL
-------
[NSLocale apple 문서](https://developer.apple.com/library/mac/documentation/cocoa/reference/foundation/classes/NSLocale_Class/Reference/Reference.html)

<http://stackoverflow.com/questions/15202454/nslocalecountrycode-returns-nil>

<https://github.com/KeepSafe/Switchboard/issues/6>

<https://github.com/mattt/FormatterKit/commit/504547516784ef2befda7aedd5fa82354666f787>
