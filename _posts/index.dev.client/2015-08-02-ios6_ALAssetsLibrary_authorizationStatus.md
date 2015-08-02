ALAssetsLibrary authorizationStatus
===================================

개요
----

iOS6부터 privacy 관련 체크가 강화되어 앨범 이미지/동영상에 접근하려고할
때 꼭 사용자의 확인을 받게 바뀌었다.

아래 메소드를 호출하면 현재 어느 상태인지 알 수가 있다.

```objectivec
typedef NS_ENUM(NSInteger, ALAuthorizationStatus) {
    ALAuthorizationStatusNotDetermined = 0, // 유저는 이 앱에 접근을 허가할 지 아직 결정을 하지 않음.
    ALAuthorizationStatusRestricted,        // 이 앱은 사진 데이터에 접근할 수 없는 상태. 
                                            // 유저는 자녀 보호와 같은 활성화된 제약으로 인해 앱의 접근 상태를 바꿀 수 없음.
    ALAuthorizationStatusDenied,            // 유저는 명시적으로 이 앱이 포토 데이터에 접근하는 것을 거부했음.
    ALAuthorizationStatusAuthorized         // 유저는 이 앱이 포토 데이터에 접근하는 것을 허가했음.
} __OSX_AVAILABLE_STARTING(__MAC_NA,__IPHONE_6_0);

// 이 앱이 포토 데이터에 접근할 수 있는 지 허가 상태를 리턴
+ (ALAuthorizationStatus)authorizationStatus __OSX_AVAILABLE_STARTING(__MAC_NA,__IPHONE_6_0);
```
