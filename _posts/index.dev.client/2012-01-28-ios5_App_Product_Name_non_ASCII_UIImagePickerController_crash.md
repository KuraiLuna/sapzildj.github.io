---
title: "app의 Product Name에 non ASCII 문자가 들어가있으면 UIImagePickerController crash"
layout: post
category : dev.client
tags : [ios5, uiimagepickercontroller]
---
{% include JB/setup %}

개요
----

UIImagePickerController를 이용해서 카메라 촬영을 시도하려고 하면 이유 없이 crash가 발생함.

엄청 삽질하면서 비교하다 보니 앱 개발 초창기에 Info.plist에서 Product
Name에 한글을 적어둔게 원인이었음.



참고 URL
-------

<http://stackoverflow.com/questions/7810251/uiimagepickercontroller-crash-on-ios-5/8002812#8002812>
