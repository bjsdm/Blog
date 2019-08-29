HashMap 与 HashTable 介绍
--------------

# 简介:
> 此文章简单介绍如何使用 HashMap 与 HashTable.

# 目录:
[1.什么是 HashMap](#1)

[2.HashMap 基本操作](#2)

[3.什么是 HashTable](#1)

[4.HashTable 基本操作](#2)

[5.其他](#2)


# <span id = "1">1.什么是 HashMap</span>

### 介绍:

基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了不同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。HashMap是线程不安全的，是异步的。


> Map 是一种以键值对储存数据的容器。而 HashMap 则是借助了键值 key 的 hashcode 值来组织储存，使得可以非常快速和高效地根据键值 key 进行数据的存取


# <span id = "2">2.HashMap基本操作</span>

**创建对象：**

```
//创建一个HashMap对象
		HashMap hm = new  HashMap();
```

**放入数据：**

```
//将emp放入到hm里面去（前面是键，后面是值）
		hm.put("s001", emp1);
```

**查找数据：**

```
//如果你要查找key是  s002
		(hm.containsKey("s002")
```

**取出数据：**

```
//如何取出
			Emp emp = (Emp)hm.get("s002");
```

**遍历HashMap：利用Iterator(迭代器)**

```
//遍历HashMap中所有的key和value
		//Iterator迭代器
		Iterator it = hm.keySet().iterator();
		//hasNext返回一个boolean
		while (it.hasNext()) 
		{
			//取出key然后转成字符串
			String key = it.next().toString();
			//通过key取出value
			Emp emp = (Emp)hm.get(key);
			System.out.println("名字:"+emp.getName());
			System.out.println("薪水:"+emp.getSal());
		
		}
```

**HashMap基本实现代码：**

```
/*
 * 功能:  HashMap 的基本操作
 */
package demo7;

import java.awt.event.MouseWheelEvent;
import java.util.HashMap;
import java.util.Iterator;

public class Demo7_5 {

	public static void main(String[] args) {
		//创建一个HashMap对象
		HashMap hm = new  HashMap();
		
		Emp emp1 = new Emp("s001", "赵蕾", 3.4f);
		Emp emp2 = new Emp("s002", "慧倩", 3.4f);
		
		//将emp放入到hm里面去
		hm.put("s001", emp1);
		hm.put("s002",emp2);
		
		//如果你要查找编号是  s002
		if (hm.containsKey("s002")) {
			System.out.println("有该员工");
			
			//如何取出
			Emp emp = (Emp)hm.get("s002");
			System.out.println("名字:"+emp.getName());
			
		}else {
			System.out.println("没有该员工");
		}
		
		//遍历HashMap中所有的key和value
		//Iterator迭代器
		Iterator it = hm.keySet().iterator();
		//hasNext返回一个boolean
		while (it.hasNext()) 
		{
			//取出key然后转成字符串
			String key = it.next().toString();
			//通过key取出value
			Emp emp = (Emp)hm.get(key);
			System.out.println("名字:"+emp.getName());
			System.out.println("薪水:"+emp.getSal());
		
		}
		
}

}

```


# <span id = "3">3.HashTable概念</span>

基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了不同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，特别是它不保证该顺序恒久不变。另外，HashMap是非线程安全的，也就是说在多线程的环境下，可能会存在问题，而Hashtable是线程安全的。是线程同步的。

> HashTable与HashMap非常相似。HashTable继承Dictionary类，实现Map接口。其中Dictionary类是任何可将键映射到相应值的类（如 Hashtable）的抽象父类。每个键和每个值都是一个对象。在任何一个 Dictionary 对象中，每个键至多与一个值相关联。Map是"key-value键值对"接口。

# <span id = "4">4.HashTable基本操作</span>

**创建：**

```
Hashtable ht = new Hashtable();
```
其他基本上与HashMap一模一样。
细节请看官方文档。
**HashTable基本实现代码：**
只是改了一下创建的对象。

```
/*
 * 功能: HashTable 基本用法,在用法上HashMap基本一样
 *
 */
package demo7;

import java.util.Hashtable;
import java.util.Iterator;

public class Demo7_6 {

	public static void main(String[] args) {
		
		Hashtable ht = new Hashtable();
		
		Emp emp1 = new Emp("s001", "赵蕾", 3.4f);
		Emp emp2 = new Emp("s002", "慧倩", 3.4f);
		
		//将emp放入到hm里面去
		ht.put("s001", emp1);
		ht.put("s002",emp2);
		
		//如果你要查找编号是  s002
		if (ht.containsKey("s002")) {
			System.out.println("有该员工");
			
			//如何取出
			Emp emp = (Emp)ht.get("s002");
			System.out.println("名字:"+emp.getName());
			
		}else {
			System.out.println("没有该员工");
		}
		
		//遍历HashMap中所有的key和value
		//Iterator迭代器
		Iterator it = ht.keySet().iterator();
		//hasNext返回一个boolean
		while (it.hasNext()) 
		{
			//取出key
			String key = it.next().toString();
			//通过key取出value
			Emp emp = (Emp)ht.get(key);
			System.out.println("名字:"+emp.getName());
			System.out.println("薪水:"+emp.getSal());
		
		}

	}

}
```


# <span id = "5">5.其他</span>


**HashMap与HashTable的区别：**
-------------------------
![区别](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcwNjEwMTY1NDExMzY5)


----------

**顺便说说ArrayList与Vector区别**
--------------------------

![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcwNjEwMTY1ODI4Mzk3)


**我们如何选择Java集合类使用：**
------------------

![我们如何选择Java集合类使用](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcwNjEwMTcxMDEyMjUw)

