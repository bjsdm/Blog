##  TextView
### SpannableString
> SpannableString可以直接作为TextView的显示文本，不同的是SpannableString可以通过使用其方法setSpan方法实现字符串各种形式风格的显示,重要的是可以指定设置的区间，也就是为字符串指定下标区间内的子字符串设置格式

通过`setSpan(Object what,int start, int end, int flags)`方法实现字符串的样式显示
#### what属性
`Spanned.SPAN_INCLUSIVE_EXCLUSIVE` 从起始下标到终了下标，包括起始下标  
`Spanned.SPAN_INCLUSIVE_INCLUSIVE` 从起始下标到终了下标，同时包括起始下标和终了下标  
`Spanned.SPAN_EXCLUSIVE_EXCLUSIVE` 从起始下标到终了下标，但都不包括起始下标和终了下标  
`Spanned.SPAN_EXCLUSIVE_INCLUSIVE` 从起始下标到终了下标，包括终了下标
#### Span常用格式
- ForegroundColorSpan(字体颜色)
- BackgroundColorSpan(背景颜色)
- RelativeSizeSpan(文字相对大小)
- StrikethroughSpan(删除线)
- Underline Span(下划线)
- SuperscriptSpan(下标)
- SubscriptSpan(上标)
- StyleSpan(字体样式)
```java
 SpannableString spannableString = new SpannableString("为文字设置粗体、斜体风格"); 
 StyleSpan styleSpan_B = new StyleSpan(Typeface.BOLD);
 StyleSpan styleSpan_I = new StyleSpan(Typeface.ITALIC); spannableString.setSpan(styleSpan_B,5,7,Spanned.SPAN_INCLUSIVE_EXCLUSIVE);
```
- ImageSpan(文本图片)
![](https://upload-images.jianshu.io/upload_images/430632-e45de5724bc6b02d.jpg)
```java
SpannableString spannableString = new SpannableString("在文本中添加表情（表情）"); 
Drawable drawable = getResources().getDrawable(R.mipmap.a9c); drawable.setBounds(0, 0, 42, 42); 
ImageSpan imageSpan = new ImageSpan(drawable); spannableString.setSpan(imageSpan, 6, 8,Spanned.SPAN_INCLUSIVE_EXCLUSIVE); textView.setText(spannableString);
```
- ClickableSpan
```java
SpannableString spannableString = new SpannableString("为文字设置点击事件"); MyClickableSpan clickableSpan = new MyClickableSpan("http://www.jianshu.com/users/dbae9ac95c78"); spannableString.setSpan(clickableSpan, 5, spannableString.length(), Spanned.SPAN_INCLUSIVE_EXCLUSIVE); 
//支持对TextView的内容滑动，但对Textview内容不支持长按文字可以复制，搜索等
textView.setMovementMethod(LinkMovementMethod.getInstance()); 
//设置点击时背景颜色
textView.setHighlightColor(Color.parseColor("#36969696")); textView.setText(spannableString); /***************************************************************/  class
//重写ClickableSpan方法
MyClickableSpan extends ClickableSpan { 
private String content; 
public MyClickableSpan(String content) 
{ this.content = content; } 
@Override 
public void updateDrawState(TextPaint ds) 
{ //是否显示下滑线 
ds.setUnderlineText(false); 
} 
@Override //点击事件
public void onClick(View widget) 
{ 
Intent intent = new Intent(MainActivity.this, OtherActivity.class);
Bundle bundle = new Bundle(); 
bundle.putString("content", content);
intent.putExtra("bundle", bundle); 
startActivity(intent); 
} }
```
- URLSpan(超链接)

- SpannableStringBuilder
  * 用append() 实现字符串拼接
### 自定义TextView样式
```xml
<! activity_main.xml-->
<com.xxxx.xx.CustomTextView
  style="?mediumTextStyle"
/>
```
