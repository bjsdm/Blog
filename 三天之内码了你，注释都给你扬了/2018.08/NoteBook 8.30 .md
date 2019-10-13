> 坚持始终外部化应用资源,这有利单独维护,如下图所示的资源文件分组

![myhgC4.png](https://s2.ax1x.com/2019/08/24/myhgC4.png)

## Layout
> 定义用户界面

## Animation
> 定义交互动画

### Property Animation
> 属性动画

详见[抛物线的讲解](https://hencoder.com/tag/hui-zhi/)

### View Animation

#### Tween animation

#### Frame animation

## Menu
> 定义用户界面(选项菜单,上下文菜单,子菜单),通过`MenuInflater`实现

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@[+][package:]id/resource_name"
          android:title="string"
          android:titleCondensed="string"
          android:icon="@[package:]drawable/drawable_resource_name"
          android:onClick="method name"
          android:showAsAction=["ifRoom" | "never" | "withText" | "always" | "collapseActionView"]
          android:actionLayout="@[package:]layout/layout_resource_name"
          android:actionViewClass="class name"
          android:actionProviderClass="class name"
          android:alphabeticShortcut="string"
          android:alphabeticModifiers=["META" | "CTRL" | "ALT" | "SHIFT" | "SYM" | "FUNCTION"]
          android:numericShortcut="string"
          android:numericModifiers=["META" | "CTRL" | "ALT" | "SHIFT" | "SYM" | "FUNCTION"]
          android:checkable=["true" | "false"]
          android:visible=["true" | "false"]
          android:enabled=["true" | "false"]
          android:menuCategory=["container" | "system" | "secondary" | "alternative"]
          android:orderInCategory="integer" />
    <group android:id="@[+][package:]id/resource name"
           android:checkableBehavior=["none" | "all" | "single"]
           android:visible=["true" | "false"]
           android:enabled=["true" | "false"]
           android:menuCategory=["container" | "system" | "secondary" | "alternative"]
           android:orderInCategory="integer" >
        <item />
    </group>
    <item >
        <menu>
          <item />
        </menu>
    </item>
</menu>
```

## Style
> 一种定义样式资源的XML文件,以实现样式的统一或者样式的复用

定义:
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <style name="CustomText" parent="@style/Text">
        <item name="android:textSize">20sp</item>
        <item name="android:textColor">#008</item>
    </style>
</resources>
```

## String
> 定义字符串资源的XML文件

### 定义

```xml
<!-- res/values/strings.xml -->
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- 单个字符串 -->
    <string name="hello">Hello!</string>
    <!-- 字符串数组 -->
    <string-array name="planets_array">
        <item>Mercury</item>
        <item>Venus</item>
        <item>Earth</item>
        <item>Mars</item>
    </string-array>
    <!-- 用于多元化的不同字符串 -->
    <plurals name="numberOfSongsAvailable">
        <item quantity="one">Znaleziono %d piosenkę.</item>
        <item quantity="few">Znaleziono %d piosenki.</item>
        <item quantity="other">Znaleziono %d piosenek.</item>
    </plurals>
</resources>
```

### 引用
```java
//引用单个字符串
String string = getString(R.string.hello);
//字符串数组
Resources res = getResources();
String[] planets = res.getStringArray(R.array.planets_array);
//多元化字符串
int count = getNumberOfsongsAvailable();
Resources res = getResources();
String songsFound = res.getQuantityString(R.plurals.numberOfSongsAvailable, count, count);
```

### 格式以及样式

#### 撇号和引号
> 要使用转移符实现

```xml
<!-- This's work -->
<string name="good_example">This\'s work</string>
<!-- This is a "good string" -->
<string name="good_example">This is a \"good string\".</string>
```

#### 字符串格式
1. 设置格式参数
```xml
<string name="welcome_messages">Hello, %1$s! You have %2$d new messages.</string>
```
2. 设置格式
```java
Resources res = getResources();
String text = String.format(res.getString(R.string.welcome_messages), username, mailCount);
```

#### 使用HTML元素设置样式

```xml
<resources>
    <string name="welcome">Welcome to <b>Android</b>!</string>
</resources>
```

#### 使用Spannable 设置样式
> 部分文字颜色,字体粗细,上下标等等
