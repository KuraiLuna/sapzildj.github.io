UITextView의 contentOffset 오류
==============================

개요
----

iOS7에서 UITextView Control이 기존 version과 다르게 글을 입력할 때
적절한 위치로 scroll되던 부분이 갑자기 작동되지 않아서 이에 대한 대응이
필요. iOS 7.0.4까지 아직 해결이 되지 않았고, iOS7.1에서는 다시
확인해봐야 함.

해결 방법
---------

UITextView의 delegate method인 textDidChange에서 contentOffset을
caret위치를 이용해서 계산한다. stackoverflow에서 찾은 실제 대응 방법은
아래와 같다.

-   <http://stackoverflow.com/questions/19259886/uitextview-cursor-not-positioning-properly-when-editing-in-ios-7-why>

```objectivec
- (void)textViewDidChange:(UITextView *)textView {
    CGRect line = [textView caretRectForPosition:
        textView.selectedTextRange.start];
    CGFloat overflow = line.origin.y + line.size.height
        - ( textView.contentOffset.y + textView.bounds.size.height
        - textView.contentInset.bottom - textView.contentInset.top );
    if ( overflow > 0 ) {
    // We are at the bottom of the visible text and introduced a line feed, scroll down (iOS 7 does not do it)
    // Scroll caret to visible area
        CGPoint offset = textView.contentOffset;
        offset.y += overflow + 7; // leave 7 pixels margin
    // Cannot animate with setContentOffset:animated: or caret will not appear
        [UIView animateWithDuration:.2 animations:^{
            [textView setContentOffset:offset];
        }];
    }
}
```

-   <http://stackoverflow.com/questions/18431684/uitextview-cursor-below-frame-when-changing-frame>

글을 입력할 때, 디바이스 화면 rotaion이 일어날 때, 키보드가
becomeFirstResponder될 때 등에 아래 method를 이용할 때 제일 마음에
들어서 이것을 사용함. \_oldRect는 물론 변수 선언을 해줘야 함.

```objectivec
- (void)_scrollCaretToVisible
{
    //This is where the cursor is at.
    CGRect caretRect = [self.textView caretRectForPosition:self.textView.selectedTextRange.end];

    if(CGRectEqualToRect(caretRect, _oldRect))
        return;

    _oldRect = caretRect;

    //This is the visible rect of the textview.
    CGRect visibleRect = self.textView.bounds;
    visibleRect.size.height -= (self.textView.contentInset.top + self.textView.contentInset.bottom);
    visibleRect.origin.y = self.textView.contentOffset.y;

    //We will scroll only if the caret falls outside of the visible rect.
    if(!CGRectContainsRect(visibleRect, caretRect))
    {
        CGPoint newOffset = self.textView.contentOffset;

        newOffset.y = MAX((caretRect.origin.y + caretRect.size.height) - visibleRect.size.height + 5, 0);

        [self.textView setContentOffset:newOffset animated:YES];
    }
}
```
