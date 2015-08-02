NSArray의 firstObject
=====================

개요
----

NSArray.h에 보면

```objectivec
- (id)lastObject;
 
```

이렇게 마지막 item을 주는 함수는 있는데 첫번째 item을 주는 함수가
없었다.

그런데 Xcode5로 와서 확인해보니

```objectivec
- (id)firstObject NS_AVAILABLE(10_6, 4_0);
```

첫번째 item을 return하는 이 함수가 생겼다. 참고로 NSArray가 empty 상태일
때는 nil을 return한다.

의문점
------

신기한건 iOS 4.0부터 지원한다는 표시이다. 이유는 알 수 없지만 public
header에서 빠진채로 private api로만 iOS 4.0부터 제공하고 있었던 것 같다.

이 간단한 method 하나를 추가해주면서 sdk를 쓰는 입장에서 상당히
편해졌다.

참고
----

-   참고 =
    <http://stackoverflow.com/questions/13064610/why-nsarray-does-not-have-a-firstobject-method>
