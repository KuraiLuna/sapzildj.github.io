---
title: "UITextView, UITextField 객체의 delegate를 스스로에게 하면 무한루프"
layout: post
category : dev.client
tags : [ios5, uitextview, uitextfield]
---
{% include JB/setup %}

개요
----

iOS5에서 아래 처럼 UITextView, UITextField를 상속하여 delegate를
스스로에게 하면 텍스트 입력시 무한루프에 빠지므로 반드시 다른 객체에서
delegate를 구현해야 한다.

예제
----

```objectivec
@interface MyTextView : UITextView
@end

@implementation  MyTextView
- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self)
    {
        self.delegate = self;
    }
    return self;
}

#pragma mark - UITextViewDelegate

- (void)textViewDidChange:(UITextView *)aTextView
{
    // 무한 루프..
}

@end
```
