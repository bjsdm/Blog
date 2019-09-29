> App必须在Manifest文件声明所需的权限,普通权限系统会自动授予,危险权限需请示用户,[权限参考](https://developer.android.com/guide/topics/permissions/overview)

**添加权限:**
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.example.snazzyapp">
    <uses-permission android:name="android.permission.INTERNET"/>     
    <uses-permission android:name="android.permission.SEND_SMS"/>
    <application ...>
        ...
    </application>
</manifest>
```

**检查权限:**

```java
    if (ContextCompat.checkSelfPermission(thisActivity, Manifest.permission.WRITE_CALENDAR)
            != PackageManager.PERMISSION_GRANTED) {
        //未授予权限处理
        ......
    }
    
```
