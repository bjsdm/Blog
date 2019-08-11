Java 8 Lambda 表达式 简单使用
-------------

# 简介:
> 此文章简单介绍如何使用 Lambda表达式的简单写法.

# 目录:
[1.什么是 Lambda 表达式](#1)

[2.Lambda 表达式的部分简单示例](#2)


# <span id = "1">**1.什么是 Lambda 表达式**</span>

### 介绍:


> Lambda 表达式源于 Lambda 演算. Lambda 演算可以被称为最小的通用程序设计语言。它包括一条变换规则（变量替换）和一条函数定义方式，Lambda 演算之通用在于，任何一个可计算函数都能用这种形式来表达和求值。因而，它是等价于图灵机的。尽管如此，Lambda 演算强调的是变换规则的运用，而非实现它们的具体机器。可以认为这是一种更接近软件而非硬件的方式。 


### lambda表达式有三部分:

- 参数列表
- 箭头
- lambda主体

![lambda 表达式图解](https://upload-images.jianshu.io/upload_images/79431-dfdeeb7b08526c3a.png?imageMogr2/auto-orient/)


# <span id = "2">**2.Lambda 表达式的部分简单示例**</span>

### 二级标题:


> 这个lambda表达式具有一个string类型的参数，返回一个int，lambda没有return语句，因为已经隐含了return


```

(String s)->s.length()


```



> 这个lambda表达式有一个Apple类型的参数并返回一个boolean


```

(Apple a)->a.getWeigh()>150


```



> 这个lambda表达式具有两个int类型的参数，没有返回值，注意lambda表达式可以包含多行语句


```

(int x,int y)->{
    System.out.println("Result:");
    System.out.println(x+y);   
}


```



> 这个lambda表达式没有参数，返回一个int


```

()->42


```



> 无参无返回


```

public class Demo01 {

    public static void main(String[] args) {

        // 1.传统方式 需要new接口的实现类来完成对接口的调用
        ICar car1 = new IcarImpl();
        car1.drive();

        // 2.匿名内部类使用
        ICar car2 = new ICar() {
            @Override
            public void drive() {
                System.out.println("Drive BMW");
            }
        };
        car2.drive();

        // 3.无参无返回Lambda表达式
        ICar car3 = () -> {System.out.println("Drive Audi");};
        // ()代表的就是方法，只是匿名的。因为接口只有一个方法所以可以反推出方法名
        // 类似Java7中的泛型 List<String> list = new ArrayList<>(); Java可以推断出ArrayList的泛型。
        car3.drive();

        // 4.无参无返回且只有一行实现时可以去掉{}让Lambda更简洁
        ICar car4 = () -> System.out.println("Drive Ferrari");
        car4.drive();

        // 去查看编译后的class文件 大家可以发现 使用传统方式或匿名内部类都会生成额外的class文件，而Lambda不会
    }
}

interface ICar {
    
    void drive();
}

class IcarImpl implements ICar{
    
    @Override
    public void drive() {
        System.out.println("Drive Benz");
    }
}


```




> 有参有返回值


```

public class Demo02 {

    public static void main(String[] args) {

        // 1.有参无返回
        IEat eat1 = (String thing) -> System.out.println("eat " + thing);
        eat1.eat("apple");

        // 参数数据类型可以省略
        IEat eat2 = (thing) -> System.out.println("eat " + thing);
        eat2.eat("banana");

        // 2.多个参数
        ISpeak speak1 = (who, content) -> System.out.println(who + " talk " + content);
        speak1.talk("xinzong", "hello word");

        // 3.返回值
        IRun run1 = () -> {return 10;};
        run1.run();

        // 4.返回值简写
        IRun run2 = () -> 10;
        run2.run();
    }

}

interface IEat {

    void eat(String thing);
}

interface ISpeak {
    void talk(String who, String content);
}

interface IRun {

    int run();
}


```




> final类型参数


```

public class Demo03 {

    public static void main(String[] args) {

        // 全写
        IAddition addition1 = (final int a, final int b) -> a + b;
        addition1.add(1, 2);
        // 简写
        IAddition addition2 = (a, b) -> a+b;
        addition2.add(2, 3);
    }

}

interface IAddition {
    
    int add(final int a, final int b);
}


```

