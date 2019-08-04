依赖注入框架 ButterKnife 简介
-------------

# 简介:
> 此篇文章主要介绍 Butterknife 这个依赖注入框架的简单介绍与使用.

# 目录:
[1.为何要使用依赖注入框架?](#1)

[2.ButterKnife 是什么?](#2)

[3.ButterKnife 的简单使用](#3)


# <span id = "1">**1.为何要使用依赖注入框架?**</span>

### 我们常用的依赖注入方式:


1. 构造方法注入


```

public class Car {

	private Engine mEngine;
	public Car(Engine mEngine){
		this.mEngine = mEngine;
	}

}


```



2. Setter 方法注入

```

public class Car {

	private Engine mEngine;
	public void set(Engine mEngine){
		this.mEngine = mEngine;
	}

}


```



3. 接口注入

```

public interface ICar{

	public void setEngine(Engine engine);

}


public class Car implements ICar{

	private Engine mEngine;
	@Override
	public void setEngine(Engine engine){
		this.mEngine = engine;
	}

}


```


> 通过以上3种注入方法, 明显将 Car 和 Engnine 解耦合了. Car 不关心 Engine 解耦合了. Car 不关心 Engine 的实现, 即使 Engine 的类型变换了, Car 也无须做任何修改.



### 为何需要使用 ButterKnife 依赖注入框架?:

> 上面3种做法对于简单的依赖来说是可行的, 但对于复杂的依赖就会造成为了传递就要编写相当多的复杂代码, 这些代码也是我们时常要避免编写的样板代码. 为了避免这种情况, 我们就要使用依赖注入框架, 如 ButterKnife.

# <span id = "2">**2.ButterKnife 是什么?**</span>


&ensp;&ensp; ButterKnife 从严格意义将不算是依赖注入框架, 它只是专注于 Android 系统的 View 注入框架, 并不支持其他方面的注入. 它可以减少大量的 findViewById 以及 setOnClickListener 代码, 简化代码并提升开发效率.



# <span id = "3">**3.ButterKnife 的简单使用**</span>



### 1. 添加依赖库

首先在 Project 的 build.gradle 文件中添加如下代码:

```

buildscript{
...

	dependencies {

	...

		classpath 'com.neenbednkt.gradle.plugins:android-apt:1.8'
	
	}

}


```

Module:app 的 build.gradle 文件中添加如下代码:

```

apply plugin: 'com.neenbedankt.android-apt'
...
dependencies {
...
	compile 'com.jakewharton:butterknife:8.4.0'
	apt 'com.jakewharton:butterknife-compiler:8.4.0'
}
...


```

### 2. 绑定控件

用注解 @BindView 绑定控件 id, 代码如下所示:

```

	@BindView(R.id.tv_text)
    TextView tv_text;
    @BindView(R.id.lv_list)
    ListView lv_list;
    @BindViews({R.id.bt_button1, R.id.bt_button2})
    List<Button> buttonList;
    @BindString(R.string.app_name)
    String appName;
    @BindArray(R.array.swordsman)
    String[] swordsman;
    @BindDimen(R.dimen.activity_horizontal_margin)
	float margin;


```


### 3. 绑定监听

```

    @OnTextChanged(value = R.id.et_edittext,callback = OnTextChanged.Callback.BEFORE_TEXT_CHANGED)
    void beforeTextChanged(CharSequence s, int start, int count, int after) {
        Log.i("wangshu", "befor"+s);
    }
    @OnTextChanged(value = R.id.et_edittext,callback = OnTextChanged.Callback.TEXT_CHANGED)
    void onTextChanged(CharSequence s, int start, int before, int count) {
        Log.i("wangshu", "change"+s);
    }
    @OnTextChanged(value = R.id.et_edittext, callback = OnTextChanged.Callback.AFTER_TEXT_CHANGED)
    void afterTextChanged(Editable s) {
        Log.i("wangshu", "after"+s.toString());
    }
    @OnClick(R.id.bt_button1)
    public void showToast() {
        Toast.makeText(this, "onClick", Toast.LENGTH_SHORT).show();
    }

    @OnTouch(R.id.bt_button2)
    public boolean onTouch(View view, MotionEvent event){
        Toast.makeText(this, "onTouch", Toast.LENGTH_SHORT).show();
        return true;
    }

    @OnItemClick(R.id.lv_list)
    void onItemClick(int position) {
        Toast.makeText(this, "onItemClick" + position, Toast.LENGTH_SHORT).show();
	}


```

