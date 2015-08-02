ALAsset lifetime
================

개요
----

iOS5부터 iOS Album의 image나 video를 wrapping해서 주는 ALAsset 객체에
lifetime이 생김.

해결 방법
---------

ALAssetsLibrary 객체로 부터 얻은 ALAsset 객체를 ALAssetsLibrary 객체가
dealloc된 후에 사용을 하려고 하면 lifetime이 어쩌구 하면서 crash가 남.

해결 방법은 아래와 같습니다.

1.  ALAsset 사용이 끝날 때까지 ALAssetsLibrary 객체를 dealloc되지 않도록
    보관
2.  ALAssetsLibrary 클래스를 singleton으로 사용하여 lifetime을 신경
    안쓰고 사용\
    (http://www.daveoncode.com/2011/10/15/solve-xcode-error-invalid-attempt-to-access-alassetprivate-past-the-lifetime-of-its-owning-alassetslibrary/)

그런데 2번의 경우 편하긴 하지만 memory를 엄청나게 사용하는
ALAssetsLibrary객체를 계속 유지하고 있으므로 현재는 거의 사용하지 않음.

참고
----

<http://developer.apple.com/library/ios/#DOCUMENTATION/AssetsLibrary/Reference/ALAssetsLibrary_Class/Reference/Reference.html>

위 애플 개발자 문서를 보면

`The lifetimes of objects you get back from a library instance are tied to the lifetime of the library instance.`
