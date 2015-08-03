---
title: "iOS Album image 첫번째의 thumbnail이 회색으로 나오는 현상"
layout: post
category : dev.client
tags : [ios6, alassetslibrary]
---
{% include JB/setup %}

재현 상황 설명
--------------

어느 시점에 재현이 되는 지는 알 수 없지만, 아이팟과 같은 사양이 좋지
않은 device에서 재현이 잘된다. ALAsset 객체의 thumbnail도 회색 정사각형
이미지이고, 기본 앨범 앱에서도 회색 정사각형 이미지로 보이게 된다.
하지만 실제 해당 이미지를 가져다가 사용하면 정상적으로 사용이 가능하다.
