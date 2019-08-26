## AndroidManifest

> 每个应用都必须包含一个AndroidManifest.xml文件.此文件向系统提供应用运行的必须信息

额外功能:

- 提供软件名
- 描述应用的各个组件
- 确定托管应用组件的进程
- 声明权限
- 列出`Instrumentation`类
- 声明系统最低API
- 声明应用所用的库

- manifest
	* xmlns:android 
		+ 命名空间,必须指定为`http://schemas.android.com/apk/res/android`
	* package 
		+ 包名,以此解析清单中相对类名,如`< activity android:name=".MainActivity">` 等于`com.example.myapp.MainActivity`
		+ 作为应用程序唯一标识
	* android:versionCode
		+ 内部版本号
	* android:versionName
		+ 展示给用户看的版本名
	* android:installLocation
		+ 安装路径 ,`internalOnly`,`auto` ,`preferExternal`
- application
	* android:usesCleartextTraffic , 是否使用HTTP协议,API28以上默认`false`
	* android:theme ,定义所有Activity的默认样式
	* resizeableActivity , 是否支持多窗口显示
	* android:persistent , 是否一直运行,默认`false`
	* android:permission , 自定义权限名称
	* android:largeHeap , 申请分配更大的内存空间
	* android:hardwareAccelerated , 是否启用硬件加速 , 加快多图加载 , 更占内存
	* android:icon , app图标
- screen
	* android:screenSize,`"small" | "normal" | "large" | "xlarge"`
	* android:screenDensity,`["ldpi" | "mdpi" | "hdpi" | "xhdpi" | "280" | "360" | "420" | "480" | "560" ]`
- Activity
	* android:launchMode , 指定启动模式
	* 其他大部分属性与application同
- 




```xml
<!--根元素-->
<manifest>
  <!--应用配置-->
  <application>
    <!--声明Activity,否则不能运行-->
    <activity>
    <!--将Activity表示为独立Activity,可以单独启动--> 
    <activity-alias>
    <!--声明Service,否则不能运行-->
    <service> 
    <!--声明Broadcast Receiver,否则不能运行-->
    <receiver>
      <!--包含于以上,指定意图类型--> 
      <intent-filter>
        <!--指定行为-->
        <action>
        <action android:name="android.intent.action.MAIN" />
        <!--指定类别-->
        <category>
        <category android:name="android.intent.category.LAUNCHER" />
        <!--向filter添加数据规范?-->
        <data>
    <provider> 
      <!--包含于以上,声明一个键值对,存储在Bundle中--> 
      <meta-data>
      <meta-data  android:name="zoo"  android:value="@string/kangaroo"  /> 
    <!--指定应用要链接的库-->
    <uses-library>
  </application>
  <!--屏幕适配-->
  <compatible-screens>
    <screen>
  <!--声明一个工具类或测试类,监视应用程序--> 
  <instrumentation> 
  <!--自定义自身的权限-->
  <permission> 
  <!--自定义自身的权限分组,以供识别,本身不声明权限-->
  <permission-group>
  <!--自定义自身的权限命名空间,本身不声明权限--> 
  <permission-tree>
  <!--声明支持纹理格式,应该是用在游戏方面的--> 
  <supports-gl-texture>
  <!--屏幕兼容模式,旧时代产物,少用--> 
  <supports-screens>
  <!--声明需要的硬件或软件功能,以免应用安装在无法运行的设备上--> 
  <uses-configuration>
  <!--声明使用的硬件或软件功能,可让您指定您的应用是需要声明的功能并且没有该功能便无法正常工作，还是使用该功能只是一种优先选择，没有它仍然可以正常工作-->  
  <uses-feature>
  <!--指明应用需要特定权限,5.0以上,在应用安装时由用户授予-->
  <uses-permission> 
  <!--指明应用需要特定权限，但仅当应用在API23或更高版本的设备上运行时才需要-->
  <uses-permission-sdk-23>
  <!--配置应用API级别的兼容性,不建议声明该属性--> 
  <uses-sdk>
</manifest>
```
