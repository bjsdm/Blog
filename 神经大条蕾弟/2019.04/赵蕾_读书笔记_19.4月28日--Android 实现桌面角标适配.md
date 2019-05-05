Android 实现桌面角标适配
-------------

# 简介:
> 本篇文章是讲解如何使用第三方库 [ShortcutBadger](https://github.com/leolin310148/ShortcutBadger "ShortcutBadger") 去实现适配 Android 桌面角标.

# 目录:
[1.什么是 Badge](#1)

[2.利用 ShortcutBadger 实现](#2)

[3.注意事项](#3)


# <span id = "1">**1.什么是 Badge**</span>

### Badge 介绍:


![角标](https://camo.githubusercontent.com/498add81447c018d8022aea23e52e95559c8173b/68747470733a2f2f7261772e6769746875622e636f6d2f6c656f6c696e3331303134382f53686f72746375744261646765722f6d61737465722f73637265656e73686f74732f73735f6c61756e636874696d652e706e67)


&ensp;&ensp; 直到 Android 8.0 开始, Android 原生系统才内置角标, 8.0 之前一直都是各厂商定制系统实现的角标.所以各厂商的适配问题也是让人头疼. 因为每个厂商都有一套 API 去实现角标. 

> Badge 就是 Android 中的桌面角标.(如图所示)


# <span id = "2">**2.利用 ShortcutBadger 实现**</span>

### 使用:

1. 在 porject 下的 build 添加

```

repositories {
        mavenCentral()
    }


```

2. 在 app 下的 build 添加

```

dependencies {
        implementation "me.leolin:ShortcutBadger:1.1.22@aar"
    }

```

3. 设置角标数量

```

int badgeCount = 1;
    ShortcutBadger.applyCount(context, badgeCount);

```

4. 移除角标

```

//第一种方法移除
ShortcutBadger.removeCount(context); //for 1.1.4+

//第二种方法移除
ShortcutBadger.applyCount(context, 0); //for 1.1.4+


```




# <span id = "3">**3.注意事项**</span>

### 在小米手机的注意事项

> 在小米手机上需要自己实现 `NotificationChannel` 才能成功设置小米手机的角标


`小米手机上的实现`
```

package me.leolin.shortcutbadger.example;

import android.annotation.TargetApi;
import android.app.IntentService;
import android.app.Notification;
import android.app.NotificationChannel;
import android.app.NotificationManager;
import android.content.Context;
import android.content.Intent;
import android.os.Build;

import me.leolin.shortcutbadger.ShortcutBadger;

public class BadgeIntentService extends IntentService {

    private static final String NOTIFICATION_CHANNEL = "me.leolin.shortcutbadger.example";

    private int notificationId = 0;

    public BadgeIntentService() {
        super("BadgeIntentService");
    }

    private NotificationManager mNotificationManager;

    @Override
    public void onCreate() {
        super.onCreate();
        mNotificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
    }
    
    @Override
    public void onStart(Intent intent, int startId) {
        super.onStart(intent, startId);
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        if (intent != null) {
            int badgeCount = intent.getIntExtra("badgeCount", 0);

            mNotificationManager.cancel(notificationId);
            notificationId++;

            Notification.Builder builder = new Notification.Builder(getApplicationContext())
                .setContentTitle("")
                .setContentText("")
                .setSmallIcon(R.drawable.ic_launcher);

            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                setupNotificationChannel();

                builder.setChannelId(NOTIFICATION_CHANNEL);
            }

            Notification notification = builder.build();
			//小米手机上需这样调用 ShortcutBadger 来设置角标
            ShortcutBadger.applyNotification(getApplicationContext(), notification, badgeCount);
            mNotificationManager.notify(notificationId, notification);
        }
    }

    @TargetApi(Build.VERSION_CODES.O)
    private void setupNotificationChannel() {
        NotificationChannel channel = new NotificationChannel(NOTIFICATION_CHANNEL, "ShortcutBadger Sample",
                NotificationManager.IMPORTANCE_DEFAULT);

        mNotificationManager.createNotificationChannel(channel);
    }
}


```

