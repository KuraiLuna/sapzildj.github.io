Xcode6 Simulator App 변경 사항
================================

iOS 지원범위 축소
--------------
-   iOS Simulator 6.x 지원안함.

Simulator 실행 기준 변경
---------------------
-   이전에는 iOS/iPad를 iOS 기준을 분리해서 Retina/non-Retina를 구분해서 Simulator를 실행하는 방식이었으나 이제는 출시된 모든 Device를 기준으로 분리해서 실행하도록 되었음.
-   Device 기준으로 Simulator를 실행하도록 되면서 Simulator 관련 파일들의 경로가 바뀜.
    -   /Users/유저명/Library/Developer/CoreSimulator/Devices에 Xcode에서 실행 가능한 모든 Device들의 Simulator 디렉토리가 모두 있고 디렉토리명은 0F3478BE-D716-4374-B9D1-F6B41D9601E8와 같은 uuid로 되어 있음.
    -   Device Simulator 디렉토리(예: 0F3478BE-D716-4374-B9D1-F6B41D9601E8)에는 device.plist 파일이 있어서 해당 Simulator에 대한 meta 정보를 확인할 수 있음.
    -   Simulator의 앱들은 Device Simulator 디렉토리 > data > Containers > Data > Application 에 저장되어 있음.
-   Resizable iPad/iPhone 추가
    -   Width/Height를 원하는 대로 설정해서 실행해 볼 수 있음.