---
title: "ObjectiveC에서의 안전한 객체 release"
layout: post
category : dev.client
tags : [ios, release, crash]
---
{% include JB/setup %}

ObjectiveC에서 특정 객체에 release를 시키면 retain count가 하나 감소가 됩니다.
그리고 이때 retain count가 0이면 이 객체는 dealloc이 됩니다. 그런데 해당 객체를 가리키고 있는 포인터가 남아있었고 이것에 접근하면 크래시가 나게 됩니다. 
이것을 예방하려고 아래와 같은 매크로를 만들어서 사용을 하고 있습니다. 원래 다른 프로젝트에서 사용하길래 가져다 써봤는데 좋더군요,,

```
#define SAFETY_RELEASE(__a) {[__a release]; __a = nil;}
```

그런데 습관적으로 무조건 위 매크로를 사용하다보면 간혹 낭패를 보는 경우가 있는데 이런 경우입니다.

```objectivec
UIView *newView = [[UIViewalloc] initWithFrame:CGRectZero];
[[selfview] addSubview:newView];
SAFETY_RELEASE(newView);

newView.backgroundColor = [UIColor redColor]; //여기서 newView는이미 nil인상태
```

그 외에도 필드로 선언된 객체에 대해 SAFETY_RELEASE를 해놓고 다른 메소드에서 접근했을 때 이상하게 원하는 대로 작동이 안되는 등 여러가지 상황이 있을 수 있겠지요,,

nil에는 어떤 메세지를 보내도(어떤 메소드를 콜해도) 괜찮다는 점이 좋을 때가 많지만 간혹 이런 사소한 실수로 30분 이상 까먹을 때가 있습니다. release를 시킬때는 꼭 해당 객체가 nil로 되야 하는 게 맞는지 생각하는 습관이 또한 필요한 것 같습니다.

Last Updated : 2011년 10월 1일 오후 6시 16분 0초 GMT+9