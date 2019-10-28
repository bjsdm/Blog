###  内存分配过程

```java
public class Memory {

	public static void main(String[] args) { // Line 1
		int i=1; // Line 2
		Object obj = new Object(); // Line 3
		Memory mem = new Memory(); // Line 4
		mem.foo(obj); // Line 5
	} // Line 9
	
	private void foo(Object param) { // Line 6
		String str = param.toString(); //// Line 7
		System.out.println(str);
	} // Line 8

}
```

![堆与栈](Java%20Tutorial_md_files/Java-Heap-Stack-Memory_20191007095201.png?v=1&type=image&token=V1:k3GM0oYBJtMHLHPOv4oU435EtLI_lMBlFv1Nf9Ec8BI)


1. 每当程序运行时,就会加载所有引用到的类到堆空间
2. 到 line 1 的时候 , java 就会为 mian() 方法线程创建栈空间
3. 到 line 2 的时候 , java 创建原始局部变量并存储在 mian() 所在的栈空间中
4. 到 line 3,4 的时候 , 对象是在堆空间中创建的, 堆空间包含对它的引用
5. 到 line 5 的时候 , 栈顶的一块空间被创建用于 foo() 方法,
6. 到 line 6 的时候 , 因为Java是通过值传递的 , 一个对象的引用在 foo() 所在的栈空间被创建
7. 到 line 7 的时候 ,创建了一个String类型, java 将进入堆空间的字符串池并于 foo() 所在的栈空间为它创建一个引用
8. 到 line 8 的时候 , foo() 方法结束 , 所在的栈空间也被释放
9. 到 line 9 的时候 , main() 方法跟 foo() 方法一样 , 同样程序也在这行结束并释放所有内存

> Java 总是通过值传递 
