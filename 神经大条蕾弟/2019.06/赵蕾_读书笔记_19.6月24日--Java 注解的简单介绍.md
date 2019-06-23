Java 注解的简单介绍
-------------

# 简介:
> 本篇文章主要介绍 Java 中的`注解`, 以及`注解`的简单使用.

# 目录:
[1.注解的介绍](#1)

[2.注解的分类](#2)

[3.定义注解](#3)


# <span id = "1">**1.注解的介绍**</span>

### 什么是注解:

&ensp;&ensp;从 Java 5 开始, Java 增加了注解. 通过使用注解, 开发人员可以在不改变原有逻辑的情况下, 在源文件中嵌入一些补充的信息. 代码分析工具, 开发工具和部署工具可以通过这些补充信息进行验证, 处理或者进行部署.

> `注解`是代码里的特殊标记, 这些标记可以在编译, 类加载, 运行时被读取, 并执行相应的处理.



# <span id = "2">**2.注解的分类**</span>

### 注解的分类:

##### 标准注解:

- @Override
> 对覆盖超类中的方法进行标记, 如果被标记的方法并没有实际覆盖超类中的方法, 则编译器会发出错误警告.

- @Deprecated
> 对不鼓励使用或者已经过时的方法添加注解, 当编程人员使用这些方法时, 将会在编译时显示提示信息.

- @SuppressWarnings
> 选择性地取消特定代码段中的警告.

- @SafeVarargs
> JDK 7 新增, 用来声明使用了可变长度参数的方法. 其在与泛型类一起使用时不会出现类型安全问题.



##### 元注解


- @Targe
> 注解所修饰的对象范围
> 参数 ElementType:
> - ElementType.TYPE: 能修饰类, 接口或枚举类型.
> - ElementType.FIELD: 能修饰成员变量.
> - ElementType.METHOD: 能修饰方法.
> - ElementType.PARAMETER: 能修饰参数.
> - ElementType.CONSTRUCTOR: 能修饰构造方法.
> - ElementType.LOCAL_VARIABLE: 能修饰局部变量.
> - ElementType.ANNOTATION_TYPE: 能修饰注解.
> - ElementType.PACKAGE: 能修饰包.
> - ElementType.TYPE_PARAMETER: 类型参数声明.
> - ElementType.TYPE_USE: 使用类型.


- @Inherited
> 表示注解可以被继承


- @Documented
> 表示这个注解应该被 JavaDoc 工具记录


- @Retention
> 用来声明注解的保留策略
> 参数 RetentionPolicy:
> - RetentionPolicy.SOURCE: 源码级注解. 注解信息只会保留在 .java 源码中, 源码在编译后注解信息被丢弃, 不会保留在 .class 中.
> - RetentionPolicy.CLASS: 编译时注解. 注解信息会保留在 .java 源码以及 .class 中. 当运行 java 程序时, JVM 会丢弃该注解信息, 不会保留在 JVM 中.
> - RetentionPolicy.RUNTIME: 运行时注解. 当运行 Java 程序时, JVM 也会保留该注解信息, 可以通过反射获取该注解信息.

- Repeatable
> JDK 8 新增, 允许一个注解在同一声明类型(类, 属性或方法)上多次使用.




# <span id = "3">**3.定义注解**</span>

### 基本定义:


```

public @interface Annotation {

....

}


```


```

@Annotation
public class AnnotationTest {

....

}


```


### 定义成员变量:


```

public @interface Annotation {

	String name();
	int age();

}


```


```


public class AnnotationTest {

	@Annotation (name = "蕾弟", age = 18)
	public void test(){
		....
	}

}


```


> 在定义注解成员变量时, 使用 Default 关键字为其指定默认值.




```

public @interface Annotation {

	String name() default "蕾弟";
	int age() default 18;

}


```


```


public class AnnotationTest {

	@Annotation
	public void test(){
		....
	}

}


```


### 定义运行时注解



```


@Retention(RetentionPolicy.RUNTIME)
public @interface Annotation {

	String name() default "蕾弟";
	int age() default 18;

}


```



### 定义编译时注解


```


@Retention(RetentionPolicy.CLASS)
public @interface Annotation {

	String name() default "蕾弟";
	int age() default 18;

}


```