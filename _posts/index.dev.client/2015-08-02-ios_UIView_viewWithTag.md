UIView의 viewWithTag:
====================

viewWithTag: 메소드는 property로 View를 추가하긴 귀찮을 정도로 간단한
View들을 처리할 때 굉장히 편한 메소드입니다. 하지만 몇 가지 문제가
있습니다.

-   느리다.
    -   이 메소드는 현재 UIView 객체의 tag가 동일한 subview를 찾기
        때문에 필드로 선언해서 사용할 때에 비해 느릴 수 밖에 없습니다.

-   모든 subview들을 검색한다.
    -   이 메소드는 현재 UIView 객체의 모든 subview들 뿐만 아니라
        못찾았을 땐 그 하위에 있는 subview들도 검색을 합니다. 이
        method를 1 depth인 subview에 대해 썼을 때는 상관이 없을 수도
        있지만 실수로 2 depth 이상의 subview에 이 tag를 가진 View가
        있다면 이유를 찾기는 힘들면서 처리가 느려질 수 있습니다.

-   tag가 중복된 값을 가질 수가 있고 리턴되는 객체를 캐스팅해서
    써야한다.
    -   UIView클래스의 tag는 단순히 NSInteger 필드일 뿐이므로 얼마든지
        여러 개의 view가 동일한 tag를 가질 수 있습니다. 이 자체가
        언제든지 버그가 생길 수 있는 여지가 있는 부분이구요. 리턴되는
        객체가 UIView이므로 UIButton이나 UIImageView등으로 캐스팅을 해서
        써야되서 코드가 지저분해지는 편입니다.
