> 语言用于沟通交流，其要做的是交流双方都明白相互所表达的意思，如果这个做不到的话，那其存在的意义也会大大折扣，同样于编程上的团队工作，编写代码的时候，也要让自己，让别人明白，编写此代码的目的是什么，同时自己的表达也要简洁明了！

## 编程规约
### 命名风格
> 代码中的命名不能以下划线或美元符号开始.也不能以下划线或美元符号结束

正例: `_name ,name_,$name.name$ `
> 代码中的命名避免使用拼音与混合的方式

正例: `login / button`
>类名使用大驼峰命名(**UpperCamelCase**)

正例: `UserDao / ServiceImp`
> 方法名,参数名,变量(除常量)都统一使用小驼峰命名(**lowerCamelCase**)

正例: `userId / userName`
> 常量命名大写,单词间下划线隔开

正例: `MAX_USERNUMBERS`
>  抽象类命名使用**Abstract**或**Base**开头 ; 异常类命名使用**Exception**结尾 ; 测试类以**Test**结尾

正例: `BaseActivity`
> 类型与中括号紧挨相连来表示数组

正例: `int[] arrayDemo;`
> POJO类中布尔类型变量都不要加**is**前缀,否则部分框架解析会引起序列化错误

> 包名统一使用小写,点分隔符之间有且仅有一个自然语义的英语单词

正例: `com.alibaba.ai.util`
> 避免在子父类的成员变量,或者不同代码块间采用完全相同的命名

反例:
```java
public class Father{
  public int age;
}
public Son extends Father{
  public int age;
}
```
> 杜绝完全不规范的缩写

> 在变量与常量的命名时,表示类型的名词放在词尾,以提升辨识度

正例: `nameArray / workQueue`

> 如果在模块,接口,类,方法使用了设计模式,在命名时需体现出具体模式

正例 : `public class OrderFactory / public class LoginProxy`

> 接口类的方法和属性不要加任何修饰符,保持代码的简洁性 , 并加上有效的注释 ,尽量不要在接口里定义变量,如果要定义变量, 那肯定 与接口方法相关,并且是整个应用的基础**常量**

正例: `void commit(); `

>  对于Service 和 DAO 类 , 基于SOA 的理念 , 暴露出来的服务一定是接口 , 内部实现类 Impl 的 后缀 与接口区别

正例: `CacheServiceImpl 实现 CacheServie`

> 枚举类名带上 Eunm 后缀  , 枚举成员名称需要全大写 , 单词间用下划线隔开

### 常量定义
> 不允许出现任何魔法值(即未经预先定义的常量) 直接出现在代码中

反例:
```java
String key = "AppId:" +appId 
//修改
final String APPID = "AppId:"; 
String key = APPID + appId ;
```
> 在 **Long** 或 **long** 赋值时,数值后使用大写的L

> 不要使用一个常量类维护所有常量,按常量功能进行归类,分开维护

> 如果变量值仅在一个固定范围内变化使用 **enum** 类型 来定义

### 代码格式
> 如果是大括号为空,则简洁地写成`{}`即可,大括号中间无需换行和空格

非空代码规则:
- 左大括号前不换行
- 左大括号后换行
- 右大括号前换行
- 右大括号后还有else等代码则不换行
- 表示终止地右大括号后必须换行

> 小括号和字符之间不出现空格 ; 大括号前需要空格

```java
if (flag == 0) {
   i = 10;
}
```
> 任何二目,三目运算符的左右两边都需要一个空格

>对齐可以采用4个空格缩进

> 单行注释加一空格

> 单行字符数字数限制不超过120个,超过则需要换行

- 第二行相对第一行缩进4个空格
- 运算符与下文一起换行
- 方法调用的点符号与下文一起换行
- 方法调用中的多个参数需要换行时,在逗号后进行

```java
sb.append("saasd")
    .append("asdasd")
    .append("sadasd");
void add (value1,
          value2,
          value3);
```
>  方法参赛在定义和传入时,多个参数逗号边必须加空格

`method(value1, value2, value3);`

> 不同逻辑,不同语义,不同业务的代码之间插入一个空行分隔开来以提升可读性

任何情形,没有必要插入多个空行来进行隔开

> 单个方法的总行数不超过80行

代码逻辑分清红花与绿叶,个性和共性,个性逻辑单独出来成为额外方法,使主干代码更加清晰 ; 共性逻辑抽取成为共性方法,便于复用与维护
### OOP规约
> 不能使用过时的类或方法

接口提供方既然已明确过时接口,那么就有义务提供新的接口;作为调用方来说,有义务去考证过时方法的新实现是什么
> Object的equal方法容易抛出空指针异常,应使用常量或确定已有值的对象来调用

正例: `"test".equal(object)`
反例: `object.equal("test")`

> 所有整形包装类对象之间值得比较,全部使用`equals` 方法

对于 Integer var = ? 在-128 至 127 范围内的赋值，Integer 对象是在 IntegerCache.cache 产 生，会复用已有对象，这个区间内的 Integer 值可以直接使用==进行判断，但是这个区间之外的所有数 据，都会在堆上产生，并不会复用已有对象，这是一个大坑，推荐使用 equals 方法进行判断
> 浮点数之间的等值判断,基本数据类型不能用 == 比较,包装数据类型不能使用 equals 来判断

```java
//反例
float a = 1.0f - 0.9f; 
float b = 0.9f - 0.8f; 
if (a == b) { 
// 预期进入此代码快，执行其它业务逻辑 
// 但事实上 a==b 的结果为 false 
} 
Float x = Float.valueOf(a); 
Float y = Float.valueOf(b); 
if (x.equals(y)) { 
// 预期进入此代码快，执行其它业务逻辑 
// 但事实上 equals 的结果为 false
 }
//正例： (1) 指定一个误差范围，两个浮点数的差值在此范围之内，则认为是相等的
float a = 1.0f - 0.9f; 
float b = 0.9f - 0.8f; 
float diff = 1e-6f; 
if (Math.abs(a - b) < diff) { 
System.out.println("true"); 
}
//(2)使用 BigDecimal 来定义值，再进行浮点数的运算操作
 BigDecimal a = new BigDecimal("1.0");
 BigDecimal b = new BigDecimal("0.9"); 
 BigDecimal c = new BigDecimal("0.8"); 
 BigDecimal x = a.subtract(b); 
 BigDecimal y = b.subtract(c); 
 if (x.equals(y)) { 
 System.out.println("true"); 
 }
```
> 慎用Object 的clone 方法来拷贝对象

对象 clone 方法默认是浅拷贝，若想实现深拷贝需覆写 clone 方法实现域对象的深度遍历式拷贝
### 集合处理
>关于 hashCode 和 equals 的处理，遵循如下规则
>> 1. 只要覆写 equals，就必须覆写 hashCode
>> 2. 因为 Set 存储的是不重复的对象，依据 hashCode 和 equals 进行判断，所以 Set 存储的对象必须覆 写这两个方法
>> 3. 如果自定义对象作为 Map 的键，那么必须覆写 hashCode 和 equals。

 说明：String 已覆写 hashCode 和 equals 方法，所以我们可以愉快地使用 String 对象作为 key 来使用。
 >  使用 Map 的方法 keySet( )/values( )/entrySet( )返回集合对象时,不可以对其进行添 加元素操作，否则会抛出 UnsupportedOperationException 异常

> 利用 Set 元素唯一的特性，可以快速对一个集合进行去重操作，避免使用 List 的 contains 方法进行遍历、对比、去重操作
### 并发处理
> 获取单例对象需要保证线程安全,其中的方法也要保证线程安全

资源驱动类,工具类,单例工厂都需要注意
>**SimpleDateFormat**是线程不安全的类,一般不要定义为static变量,如果定义为static,必须加锁,或者使用DateUtils工具类

### 控制语句
>在一个 switch 块内，每个 case 要么通过 continue/break/return 等来终止，要么 注释说明程序将继续执行到哪一个 case 为止；在一个 switch 块内，都必须包含一个 default 语句并且放在最后，即使它什么代码也没有。 

说明：注意 break 是退出 switch 语句块，而 return 是退出方法体

>当switch括号内的变量类型为String并且此变量为外部参数时,必须先进行null判断

> 在if/else/for/while/do 语句中必须使用大括号

> 在高并发场景中,避免使用"等于"判断作为中断或退出条件

如果并发控制没有处理好,容易产生等值判断被击穿的情况,使用小于或大于的区间判断来替代
> 除常用方法外,不要在条件判断中执行其他复杂的语句,将复杂逻辑判断的结果赋值给一个有意义的布尔变量名,以提高可读性

`final boolean existed = (file.open.....)`
> 避免采用取反逻辑运算符

取反逻辑不利于快速理解,并且取反逻辑写法必然存在对应的正向逻辑写法
> 循环体中的语句要考量性能

定义对象,变量,获取连接,进行不必要的try-catch等操作尽量移至循环体外
### 其他

> 及时清理不再使用的代码段或配置信息

对于垃圾代码或过时配置 ,坚决清理干净,避免程序过度臃肿,代码冗余

对于暂时被注释掉,后续可能恢复使用的代码片段,在注释上方,说明理由

> 任何数据结构的构造或初始化,都应指定大小,避免数据结构无限增长吃光内存


** -参考阿里JAVA代码规范华山版 **
