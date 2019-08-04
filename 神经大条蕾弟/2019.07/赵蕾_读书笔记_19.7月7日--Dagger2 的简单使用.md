Dagger2 的简单使用
-------------

# 简介
> 本篇文章主要内容是介绍 Dagger2 的简单使用, 没有很详细的解析.

# 目录:
[1.什么是 Dagger2](#1)

[2.为什么要使用 Dagger2](#2)

[3.使用场景](#3)


# <span id = "1">**1.什么是 Dagger2**</span>

### 介绍:

&ensp;&ensp;Dagger 2 是一个基于 JSR-330 (Java 依赖注入) 标准的依赖注入框架, 在编译期间自动生成代码, 负责依赖对象的创建.

> Dagger2是一个依赖注入框架。


# <span id = "2">**2.为什么要使用 Dagger2**</span>

### 作用:

- 便于复杂依赖关系的解耦.
- 便于对象的管理.

# <span id = "3">**3.使用场景**</span>


### 添加依赖库:

> 在 Project 的 build.gradle 文件中添加引用


```

buildscript {
...
	dependencies {
	...
		classpath'com.neenbedankt.gradle.plugins:android-apt:1.8'
	}

}


```


> 在 app 的 build.gradle 文件中添加引用

```

apply plugin: `com.nenbedankt.android-apt`
...

dependencies {

...
	apt 'com.google.dagger:degger-compiler:2.7'
	compile 'com.google.dagger:dagger:2.7'
}

...


```


### 简单使用(这只是其中一种使用, 还有很多复杂情况...)

> 普通类的注入

`在类的构造方法添加 @Inject 注解`
```

public class Watch {
    @Inject
    public Watch(){

    }
    public void work(){
        Log.d("Dagger2","手表工作");
    }
}


```

> 定义一个接口来完成依赖注入, 接口名建议为: 目标类名 + Component. 在编译后 Dagger2 就会为我们生成名为 Dagger + 目标类名 + Component 的辅助类.

`为接口添加 @Component`
```

@Component
public interface MainActivityComponent {

	void inject(MainActivity acticity);

}


```


> Activity 调用

```

public calss MainActivity extends AppCompatActivity {

	//标记需要注入的属性
	@Inject
	Watch watch;

	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		
		//调用编译生成的 DaggerMainActivityComponent 的 inject 方法来完成注入.
		DaggerMainActivityComponent.create().inject(this);;
		watch.work();

	}

}


```