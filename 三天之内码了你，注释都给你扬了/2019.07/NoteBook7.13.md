## 属性代码复用
### string.xml
```xml
<style name="indicate_button_edit">  
 <item name="android:layout_width">175dp</item>  
 <item name="android:layout_height">match_parent</item>  
 <item name="android:layout_marginLeft">15dp</item>  
 <item name="android:textSize">14sp</item>  
 <item name="android:background">@color/editLine_color</item>  
 <item name="android:gravity">bottom</item>  
 <item name="android:paddingBottom">10dp</item>  
</style>
```
### layout.xml
```xml
<EditText  
  android:id="@+id/et_password"  
  style="@style/indicate_button_edit"  
  android:hint="请输入密码"  
  android:inputType="textPassword" />
```
## xml背景文件定义
>selected和focused的区别：focused是指获取焦点，早期某些设备存在方向键盘可以用来移动焦点，而pressed仅仅是指按下状态。然后selected容易和focused混淆。一个界面只允许有一个控件处在focus状态，却可以有多个处于Selected状态，可以调用View.setSelected设置选中。
![](https://img-blog.csdn.net/20161103173620571?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

- android:drawable
这个属性是必须的，为当前控件指定资源。
- android:state_pressed
true指当用户点击或者触摸该控件的状态。默认为false；一般用于设置按钮颜色/图片的设置。
- android:state_focused
- ture指当前控件获得焦点时的状态。默认为false；一般用于EdiText。
- android:state_hovered
布尔值。true表示光标移动到当前控件上的状态。默认为false；光标是否悬停，通常与state_focused 相同，它是4.0的新特性，一般用于EdiText。
- android:state_selected
布尔值。true表示被选择的状态，例如在一个下拉列表中用方向键下选择其中一个选项。 
这个和focus的区别，selected是focus不充分的情况。比如一个listview获得焦点（focus），而用方向键选择了其中的一个item（selected）
- android:state_checkable
ture表示可以被勾选的状态。这个仅在当控件具有被勾选和不被勾选的状态间转换时才起作用。
- android:state_checked
true表示当前控件处于被勾选（check的状态）
- android:state_enabled
布尔值。true表示当前控件出于可用的状态。比如可以被点击
- android:state_activated
布尔值。true表示当前控件被激活的状态。 
- android:state_window_focused 
布尔值。true表示当前控件出于最前端时，应用窗口获得焦点的状态。

```xml
<selector  
  xmlns:android="http://schemas.android.com/apk/res/android">  
 <item android:state_pressed="false">  
 <shape>  
 <corners android:radius="5dp" />  
 <solid android:color="#FFFFFF" />  
 </shape>  
 </item>  
 <item android:state_pressed="true">  
 <shape>  
 <corners android:radius="5dp" />  
 <solid android:color="#ED1731" />  
 </shape>  
 </item>  
 <item a>  
 <shape>  
 <corners android:radius="5dp" />  
 <solid android:color="#ED1731" />  
 </shape>  
 </item>  
</selector>
```
## 控价可见性
- GONE
- INVISIBLE
- VISBLE
```java
 view.setVisibility(View.GONE);
