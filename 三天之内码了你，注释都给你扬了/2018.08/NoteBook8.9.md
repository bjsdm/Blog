## 设计模式
![](https://images0.cnblogs.com/blog/448646/201310/02162323-a8ea2a2f09c84eabbc151e9965dc7c69.png)
### 单例模式
> 确保一个类只有一个实例,而且自行实例化并向整个系统提供这个实例
![eTRGKs.jpg](https://s2.ax1x.com/2019/08/08/eTRGKs.jpg)
#### 使用场景
确保一个类有且只有一个对象的场景,避免产生多个对象消耗过多的资源,即一类一对象
#### Double Check Lock (DCL) 实现单例
> 在需要时才初始化单例.又能保证线程安全,且单例对象初始化调用getInstance不进行同步锁
```java
public class SingletonDCL {  
  private static SingletonDCL singletonDCL = null;  
  public SingletonDCL() {}   
  public static SingletonDCL getInstance() {  
   if (singletonDCL == null) {  
       synchronized (SingletonDCL.class) {  
          if (singletonDCL == null) {  
           singletonDCL = new SingletonDCL();  
            } 
         } 
      }  return singletonDCL;  
    }  
}
//Main
System.out.println(SingletonDCL.getInstance().toString());  
SingletonDCL singletonDCL = SingletonDCL.getInstance();  
System.out.println("singletonDCL2 = " + singletonDCL.toString());//输出内存地址一致
```
####静态内部类单例模式
>第一次调用getInstance才会初始化对象,不仅能确保线程安全,也能保证单例对象的唯一性,同时延迟了单例的实例化,推荐使用
```java
public class SingletonStatic {  
  public SingletonStatic() {  
  }
  public static SingletonStatic getInstance() {  //需要时才初始化
  return SingletonHolder.singletonStatic;  
  }  
  private static class SingletonHolder{  
    private static final SingletonStatic singletonStatic = new SingletonStatic();  
  }
 }
```
### Builder模式
>  将一个复杂对象的初始化和它的表示分离开来
![eTRebt.jpg](https://s2.ax1x.com/2019/08/08/eTRebt.jpg)
```java
//抽象类
public abstract class Computer {  
  protected String mBoard;  
  protected String mDisplay;  
  protected String mOs;  
  
  public Computer() { }  
  public void setmBoard(String mBoard) {  
  this.mBoard = mBoard;  
  }  
  public void setmDisplay(String mDisplay) {  
  this.mDisplay = mDisplay;  
   }  
  @Override  
  public String toString() {  
  return "Computer{" +  
  "mBoard='" + mBoard + '\'' +  
  ", mDisplay='" + mDisplay + '\'' +  
  ", mOs='" + mOs + '\'' +  
  '}';  
  }  
  public abstract void setOs();  
}
//复杂对象(假设)
public class MacBook extends Computer {  
  @Override  
  public void setOs() {  
  mOs = "Mac Os Herial 10.9";  
 }}
//抽象初始化类
public abstract class Builder {  
  public abstract void setBoard(String board);  
  
  public abstract void setDisplay(String display);  
  
  public abstract void setOs();  
  
  public abstract Computer create();  
}
//复杂对象初始化类
public class MacBookBuilder extends Builder {  
  private Computer computer = new MacBook();  
  @Override  
  public void setBoard(String board) {  
  computer.setmBoard(board);  
  }  
  @Override  
  public void setDisplay(String display) {  
  computer.setmDisplay(display);  
  }  
  @Override  
  public void setOs() {  
  computer.setOs();  
  }  
  @Override  
  public Computer create() {  
  return computer;  
  }
}
//构造者(执行者)
public class Director {  
  Builder builder = null;  
  
  public Director(Builder builder) {  
  this.builder = builder;  
 }  
  public void construct(String board, String display) {  
  builder.setBoard(board);  
  builder.setDisplay(display);  
  builder.setOs();  
 }}
//Main
Builder builder = new MacBookBuilder();  
Director director = new Director(builder);  
director.construct("Intel", "LG Screen");  
System.out.println(builder.create().toString());
```
#### Builder灵活应用(链式调用)
> 链式调用GGWP
```java
//链式调用类
public class User {  
  private String name;  
  private String sex;  
  private String level;  
  
  public User() { }  
  //内部Builder类
  public static  final class UserBuilder{  
  User user = new User();  
  public UserBuilder() {  
   }  
  public UserBuilder setName(String name) {  
  user.setName(name);  
  return this;  
   }  
  public UserBuilder setSex(String sex) {  
  user.setSex(sex);  
  return this;  
   }  
  public UserBuilder setLevel(String level) {  
  user.setLevel("VIP");  
  return this;  
   }  
  public User create() {  
  return user;  
   } 
  }  
  public void setName(String name) {  
  this.name = name;  
  }  
  public void setSex(String sex) {  
  this.sex = sex;  
  }  
  public void setLevel(String level) {  
  this.level = level;  
 }  
  @Override  
  public String toString() {  
  return "User{" +  "name='" + name + '\'' +  ", sex='" + sex + '\'' +  ", level='" + level + '\'' + '}';  
 }
}
//Main
//使用方式1
User.UserBuilder builder = new User.UserBuilder();  
System.out.println(builder
.setName("Onion233")
.setLevel("VIP")
.setSex("Man").create().toString()); 
//使用方式2 
System.out.println(new User
.UserBuilder()
.setName("Onion233")
.setLevel("VIP")
.setSex("Man").create().toString());
```
### 原型模式
> 用原型实例指定创建对象的种类,并通过赋值这些原型创建新的对象
![eT2IH0.jpg](https://s2.ax1x.com/2019/08/08/eT2IH0.jpg)
#### 使用场景
- 类初始化需要消耗非常多的资源,而用过原型复制可以避免这些消耗
- 当new一个对象需要非常繁琐的数据准备或访问权限时,可以使用原型直接Clone一个
- 一个对象需要提供给其他对象访问,且每个访问者都可能修改其值时,可以考虑使用原型模式Clone多个,以供其他对象使用,即保护性拷贝
> 需要注意的,使用原型模式调用Clone函数构造实例不一定比new操作速度快,只用当new构造对象较为耗时或者成本较高时,才能获得效率的提升
#### 浅拷贝
> Clone对象时不会执行构造函数,且需要注意的是浅拷贝对于指针型的变量,不会重新构造,如下代码的`mImages`,改动时,两个对象都会变化
```java
public class WordDocument implements Cloneable{  //实现Cloneable接口
  private String mText;  
  private ArrayList<String> mImages = new ArrayList<String>();  
  public WordDocument() {  
  System.out.println("---------------------WordDocument Create----------------------");  
  }  
  @Override  //拷贝处理
  public WordDocument clone(){  
  try{  
  WordDocument doc = (WordDocument) super.clone();  
  doc.mText = this.mText;  
  doc.mImages = this.mImages;  
  return doc;  
  }
  catch (Exception e){  }  
  return null;  
  }  
  public String getmText() {  
  return mText;  
  }  
  public void setmText(String mText) {  
  this.mText = mText;  
  }  
  public ArrayList<String> getmImages() {  
  return mImages;  
  }  
  public void setmImages(String mImages) {  
  this.mImages.add(mImages);  
  }  
  public void showDocument(){  
  System.out.println("-------------Content Start------------");  
  System.out.println("Text:"+mText);  
  System.out.println("Image :");  
  for (String image:mImages   ) {  
   System.out.println("image = " + image);  
   }  
  System.out.println("------------Content End---------------");  
  }
 }
```
![eIfOCF.png](https://s2.ax1x.com/2019/08/07/eIfOCF.png)
#### 深拷贝 
> 解决浅拷贝的问题
```java
@Override  
public WordDocument clone(){  
  try{  
  WordDocument doc = (WordDocument) super.clone();  
  doc.mText = this.mText; 
  //对mImages对象也调用clone()函数,进行深拷贝
  doc.mImages = (ArrayList<String>)this.mImages.clone();  
  return doc;  
  }
  catch (Exception e){ }  
  return null;  
}
```
> 某些程度上,原型模式避免了重复创建文件,直接使用复制完成
