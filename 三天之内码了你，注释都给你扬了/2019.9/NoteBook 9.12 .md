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

**请求权限:**

- ActivityCompat.shouldShowRequestPermissionRationale( Activity,Manifest.permission.? )
  * 用于用户之前拒绝过权限的情况下
  * 如果用户之前拒绝了该请求，该方法将返回 `true`；
  * 如果用户之前拒绝了某项权限并且选中了权限请求对话框中的**不再询问**选项，或者如果设备政策禁止该权限，该方法将返回 `false`
- ActivityCompat.requestPermissions( Activity,Manifest.permission.? )
  * 用于请求权限

**一个完整的请求流程:**

```java
    // 判断权限是否授予
    if (ContextCompat.checkSelfPermission(thisActivity,
            Manifest.permission.READ_CONTACTS)
            != PackageManager.PERMISSION_GRANTED) {

        // 权限未授予
        // 判断用户之前是否拒绝过权限,是否需要解释
        if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
                Manifest.permission.READ_CONTACTS)) {
            // 拒绝过,但没有被禁止请求,这里向用户解释请求权限的原因,然后再请求
           
        } else {
            // 情况1:没拒绝过,请求权限
            // 情况2:拒绝过,且禁止重复请求
            ActivityCompat.requestPermissions(thisActivity,
                    new String[]{Manifest.permission.READ_CONTACTS},
                    MY_PERMISSIONS_REQUEST_READ_CONTACTS);
            // MY_PERMISSIONS_REQUEST_READ_CONTACTS 是一个自定义的常量(信号码),以作识别处理
        }
    } else {
        // 权限已被授予
    }
    //requestPermissions方法回调处理
    @Override
    public void onRequestPermissionsResult(int requestCode,
            String[] permissions, int[] grantResults) {
        switch (requestCode) {
            case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
                // 如果授权请求被拒绝,grantResults数组为空
                if (grantResults.length > 0
                    && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    // 权限被授予成功

                } else {
                    // 授权被拒绝
                }
                return;
            }

            // 其他case处理,App可能有多个权限请求
        }
    }     
```

**按APi级别声明权限**
要仅在支持运行时权限的设备（即运行 Android 6.0（API 级别 23）或更高版本的设备）上声明某项权限，请添加 uses- permission-sdk-23 标记，而不是 uses-permission 标记。

使用这些标记中的任意一个时，您均可设置 maxSdkVersion 属性，以指定在运行更高版本的设备上不需要特定权限。
